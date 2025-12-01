Here is the **Database Schema** document. This is the definitive source of truth for the project's data structure. It is designed to be applied via Supabase migrations (SQL) to ensure reproducible environments (local and cloud).

It relies heavily on **Row Level Security (RLS)**, which is Supabase's primary mechanism for securing data in a client-heavy architecture.

-----

# 05\_Database\_Schema.md

# Database Schema & Security Policies

**Project:** Tsumego Tower: A Yokai Dating Sim
**Version:** 1.0.0
**Context:** This document defines the PostgreSQL schema managed by Supabase. It serves as the reference for AI Agents writing backend migrations, seed scripts, and frontend data fetching logic.

-----

## 1\. Design Principles & Conventions

  * **Supabase Auth Integration:** The core user identity is managed by Supabase Auth's internal `auth.users` table. Our custom data links to this via the `profiles.id` foreign key.
  * **Row Level Security (RLS) is Mandatory:** Every table created in the `public` schema must have RLS enabled immediately to prevent accidental data leaks.
  * **Client-Side Trust Model:** For the MVP, we lean on client-side validation for puzzle solving, verified by database constraints. (e.g., The client checks the move against the WASM engine, then inserts a record into `player_puzzle_history`. RLS ensures they can only insert for themselves). Future iterations may move validation to Edge Functions.
  * **JSONB Usage:** We use structured columns for relational data but utilize `JSONB` for flexible metadata (e.g., puzzle tags, specific SGF properties) to avoid over-normalizing the schema.
  * **Naming:** Snake\_case for all table and column names.

-----

## 2\. Custom Types & Enums

Define these first to ensure consistency across tables.

```sql
-- Difficulty ranks matching standard Go ranking systems
CREATE TYPE rank_difficulty AS ENUM (
  '25k', '20k', '15k', '10k', '5k', '1k',
  '1d', '3d', '5d', '9d'
);

-- Puzzle attempt status
CREATE TYPE puzzle_status AS ENUM ('solved', 'failed');

-- Companion archetype defines their personality/dialogue style
CREATE TYPE companion_archetype AS ENUM ('tsundere', 'genki', 'yamato_nadeshiko', 'goddess', 'trickster');
```

-----

## 3\. Core Tables (User & Economy)

### 3.1 Table: `profiles`

Extends the default Supabase Auth user with game-specific data. This table is created via a trigger whenever a new user signs up.

```sql
CREATE TABLE profiles (
  id UUID REFERENCES auth.users(id) ON DELETE CASCADE PRIMARY KEY,
  username TEXT UNIQUE,
  avatar_url TEXT,
  
  -- Economy
  spirit_stones INT NOT NULL DEFAULT 0 CHECK (spirit_stones >= 0),
  karma INT NOT NULL DEFAULT 0 CHECK (karma >= 0),
  
  -- Tower Progression
  current_floor INT NOT NULL DEFAULT 1 CHECK (current_floor > 0),
  highest_floor INT NOT NULL DEFAULT 1 CHECK (highest_floor > 0),
  
  -- Streak mechanics
  current_streak INT NOT NULL DEFAULT 0 CHECK (current_streak >= 0),
  last_active_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable RLS
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Policies
-- Users can see their own profile (Read)
CREATE POLICY "Users can view own profile" ON profiles
  FOR SELECT USING (auth.uid() = id);

-- Users can update their own profile (Update - e.g. changing avatar/username)
-- Note: Currency updates should ideally happen via RPC functions in the future, but direct update is okay for MVP prototype.
CREATE POLICY "Users can update own profile" ON profiles
  FOR UPDATE USING (auth.uid() = id);
```

-----

## 4\. Gameplay Content Tables (Puzzles & Tower)

### 4.1 Table: `puzzles`

The core content generated offline by the Python/KataGo pipeline. This data is functionally static read-only for clients.

```sql
CREATE TABLE puzzles (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  
  -- Organization
  floor_number INT NOT NULL CHECK (floor_number > 0),
  difficulty rank_difficulty NOT NULL,
  
  -- The Content
  initial_state_sgf TEXT NOT NULL, -- The problem setup
  solution_sgf TEXT NOT NULL, -- The correct sequences for validation
  
  -- Metadata for filtering/display
  tags TEXT[], -- e.g. ['life-and-death', 'tesuji', 'corner']
  metadata JSONB DEFAULT '{}'::jsonb, -- Source game info, author credits
  
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Indexes for fast tower loading
CREATE INDEX idx_puzzles_floor ON puzzles(floor_number);

-- Enable RLS
ALTER TABLE puzzles ENABLE ROW LEVEL SECURITY;

-- Policies
-- Everyone can read puzzles (Public Data)
CREATE POLICY "Puzzles are public read-only" ON puzzles
  FOR SELECT USING (true);
-- No insert/update policies for anon/authenticated roles. Only service_role can manage these.
```

### 4.2 Table: `player_puzzle_history`

Tracks individual user progress on specific puzzles. This ensures a user only gets rewards for solving a puzzle once.

```sql
CREATE TABLE player_puzzle_history (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) NOT NULL,
  puzzle_id UUID REFERENCES puzzles(id) NOT NULL,
  
  status puzzle_status NOT NULL,
  attempts INT DEFAULT 1,
  solved_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  
  -- Ensure a user has only one definitive record per puzzle
  UNIQUE(user_id, puzzle_id)
);

-- Enable RLS
ALTER TABLE player_puzzle_history ENABLE ROW LEVEL SECURITY;

-- Policies
-- Users can see their own history
CREATE POLICY "Users view own puzzle history" ON player_puzzle_history
  FOR SELECT USING (auth.uid() = user_id);

-- Users can insert their own results (The client app validates, then inserts)
CREATE POLICY "Users insert own puzzle results" ON player_puzzle_history
  FOR INSERT WITH CHECK (auth.uid() = user_id);
```

-----

## 5\. Companion & Metagame Tables (The Waifus)

### 5.1 Table: `companions` (Static Definitions)

Defines the base data for the characters available in the game.

```sql
CREATE TABLE companions (
  id TEXT PRIMARY KEY, -- Slug, e.g., 'kitsune', 'alphazero'
  display_name TEXT NOT NULL,
  description TEXT NOT NULL,
  archetype companion_archetype NOT NULL,
  
  -- Assets
  base_sprite_url TEXT NOT NULL,
  portrait_url TEXT NOT NULL,
  
  -- Unlocking logic
  unlock_floor_requirement INT, -- e.g., beat floor 10 boss to unlock
  
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable RLS
ALTER TABLE companions ENABLE ROW LEVEL SECURITY;

-- Policies
-- Public read-only definition data
CREATE POLICY "Companions are public read-only" ON companions
  FOR SELECT USING (true);
```

### 5.2 Table: `player_companions` (User State)

Tracks the relationship between a specific player and a companion.

```sql
CREATE TABLE player_companions (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES profiles(id) NOT NULL,
  companion_id TEXT REFERENCES companions(id) NOT NULL,
  
  -- Progression
  affection_level INT NOT NULL DEFAULT 0 CHECK (affection_level >= 0 AND affection_level <= 100),
  is_favorite BOOLEAN DEFAULT FALSE, -- Who appears on the home screen
  
  unlocked_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  
  -- Ensure one entry per companion per user
  UNIQUE(user_id, companion_id)
);

-- Enable RLS
ALTER TABLE player_companions ENABLE ROW LEVEL SECURITY;

-- Policies
-- Users view their own companions
CREATE POLICY "Users view own companion state" ON player_companions
  FOR SELECT USING (auth.uid() = user_id);

-- Users update their own companions (e.g., changing favorite status, increasing affection via gifts)
CREATE POLICY "Users update own companion state" ON player_companions
  FOR UPDATE USING (auth.uid() = user_id);
```

-----

## 6\. Triggers & Automation

### 6.1 Profile Creation Trigger

Automatically creates a `profile` entry when a new user signs up via Supabase Auth.

```sql
-- Function to handle new user insertion
CREATE OR REPLACE FUNCTION public.handle_new_user()
RETURNS trigger AS $$
BEGIN
  INSERT INTO public.profiles (id, username, avatar_url)
  VALUES (new.id, new.raw_user_meta_data->>'username', new.raw_user_meta_data->>'avatar_url');
  RETURN new;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

-- Trigger the function on auth.users insert
CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW EXECUTE PROCEDURE public.handle_new_user();
```