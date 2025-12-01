Here is the **DevOps and Pipeline** document. This is the operational manual for the project. It defines the practical workflows for a solo developer to build, manage data, generate content, and ship the game without getting overwhelmed by infrastructure.

It leans heavily on the **Supabase CLI** and **Docker** to ensure that your local development environment is a perfect mirror of production, eliminating the need for "mock APIs" and preventing duplicate work.

-----

# 06_DevOps_and_Pipeline.md

# DevOps Guidelines & Content Pipeline

**Project:** Tsumego Tower
**Version:** 1.0.0
**Context:** This document outlines the practical workflows for local development, database management, content generation (puzzles/art), and deployment. It is designed for a solo developer workflow using a "Local-First" philosophy.

-----

## 1\. The "Local-First" Philosophy

To avoid rework and the "it works on my machine" syndrome, we do **not** use mock data or fake APIs for development.

We use **Supabase Local**. This runs a full PostgreSQL database, Auth server, and Storage API in Docker containers on your dev machine. The React/Tauri app connects to this local instance exactly as it would connect to the production cloud.

**The Golden Rule:** If you need to change the database schema, you do it locally first via migrations, test it, and then push the migrations to the repo. Never edit the production database dashboard directly.

-----

## 2\. Prerequisites & Initial Setup

### A. Required Tools

Ensure these are installed and in your system PATH before starting.

1.  **Node.js (LTS v20+)** & **pnpm** (Package management).
2.  **Rust Toolchain** (`rustup`, `cargo`) - Required for Tauri.
3.  **Docker Desktop** - Required for Supabase Local.
4.  **Supabase CLI** - (`brew install supabase/tap/supabase` or `npm i -g supabase`).
5.  **Python 3.10+ & pip** - Required for the offline puzzle generation scripts.

### B. Day 1 Initialization (Run Once)

1.  Clone the repository.
2.  Install dependencies: `pnpm install`.
3.  Initialize local environment variables: `cp .env.example .env.local`.
4.  Start the Docker engine.
5.  Initialize Supabase Local: `npx supabase start`.
      * *Note: The first run will take time to download Docker images.*

-----

## 3\. Daily Development Workflow

### A. Starting the Dev Environment

You need two terminal processes running simultaneously.

**Terminal 1: The Backend (Supabase Local)**
Keeps the local database and API gateway running.

```bash
npx supabase start
```

  * *Output:* Provides local URLs (e.g., API: `http://127.0.0.1:54321`, Studio Dashboard: `http://127.0.0.1:54323`).

**Terminal 2: The Frontend/Client (Tauri + Vite)**
Starts the React dev server and wraps it in the Tauri desktop window with hot-reloading.

```bash
pnpm tauri dev
```

  * *Note:* If you only want to test the web view without launching the desktop window, run `pnpm dev`.

### B. Stopping Development

When finished, stop the Tauri process (Ctrl+C) and then stop the Docker containers to free up resources.

```bash
npx supabase stop
```

-----

## 4\. Database Management Pipeline

We manage the database schema using **migrations**. This serves as version control for our SQL.

### A. Making Schema Changes

1.  Ensure Supabase Local is running (`npx supabase start`).
2.  Open the local dashboard (`http://127.0.0.1:54323`).
3.  Make changes in the Table Editor UI (create tables, add columns, edit RLS policies).
4.  **Generate the Migration:** In your terminal, run:
    ```bash
    npx supabase db diff --schema public -f description_of_change
    ```
      * *Example:* `npx supabase db diff -f add_waifu_affection_column`
      * *Result:* A new timestamped `.sql` file is created in `/supabase/migrations`.

### B. Applying & Resetting changes

To apply new migrations or reset your local database to a clean state (useful if data gets messy during testing):

```bash
npx supabase db reset
```

  * *Action:* This drops the local database, re-applies all migrations from scratch, and runs the `supabase/seed.sql` script to populate it with test data.

### C. Seeding Test Data

The `supabase/seed.sql` file contains SQL insert statements to create dummy users, a few test puzzles, and initial companion data. Update this file if you need specific test scenarios available immediately after a reset.

-----

## 5\. The Content Pipeline (Puzzles & Art)

This is the workflow for generating the game's assets outside of the main engine.

### A. Puzzle Generation (Python -\> SQL)

This is an "Offline" process run periodically to build up the content library.

1.  **Location:** `/tools/puzzle-generator`
2.  **Input:** Place raw SGF files (pro games) in `/tools/sgf_library`.
3.  **Process:** Run the Python script (e.g., `python generate_puzzles.py`).
      * *The script uses KataGo to analyze games, detect blunders, crop the board, validate unique solutions, and output a large `puzzles.json` file.*
4.  **Ingestion:** Run the SQL generation script to convert `puzzles.json` into a SQL insert statement, and append it to a migration file or the `seed.sql` file to get it into the database.

### B. Art Asset Management (Public vs. Private)

  * **Raw Assets (PRIVATE):** High-resolution PSDs, raw Midjourney outputs, and layered files should be kept in a private backup folder outside the main repo (or a private submodule) to avoid bloating Git history.
  * **Game-Ready Assets (PUBLIC):** Processed, optimized files go into `/public/assets/`.
      * **Format:** `.webp` for virtually everything (sprites, backgrounds).
      * **Naming Convention:**
          * Backgrounds: `bg_[biome]_[variation].webp` (e.g., `bg_cloud_shrine_day.webp`)
          * Sprites: `ch_[name]_[outfit]_[expression].webp` (e.g., `ch_kitsune_kimono_blush.webp`)
          * UI: `ui_[element]_[state].webp` (e.g., `ui_button_wood_pressed.webp`)

-----

## 6\. Build & Deployment

### A. Production Environment Variables

Never commit production secrets to Git.

1.  Create a new project on Supabase Cloud.
2.  In your CI/CD environment (e.g., GitHub Actions) or local build machine, set the production variables:
      * `VITE_SUPABASE_URL`: Your cloud project URL.
      * `VITE_SUPABASE_ANON_KEY`: Your cloud public API key.

### B. Deploying the Database to Cloud

Do not manually create tables in the cloud dashboard. Push your local migrations.

1.  Login via CLI: `npx supabase login`
2.  Link local project to cloud project ID: `npx supabase link --project-ref your-project-id`
3.  Push migrations: `npx supabase db push`

### C. Building the Client

**Web Build (PWA):**

```bash
pnpm build
```

*Output:* The `/dist` folder containing static HTML/JS/CSS. Deploy this to any static host (Vercel, Netlify, Supabase Hosting).

**Desktop Build (Steam/Itch.io):**

```bash
pnpm tauri build
```

  * *Prerequisite:* You must have code signing certificates set up for Windows/macOS for a release build.
  * *Output:* Native installers (`.msi`, `.dmg`, `.deb`) located in `/src-tauri/target/release/bundle`.