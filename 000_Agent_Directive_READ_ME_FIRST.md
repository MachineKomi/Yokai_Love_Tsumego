# 000_Agent_Directive_READ_ME_FIRST.md

# THE PRIME DIRECTIVE: Documentation is Truth
**Project:** Tsumego Tower
**Context:** This document is the absolute highest authority for all AI Agents working on this project. It defines *how* you work, *where* you look for answers, and the *rules* of engagement.

---

## 1. The Golden Rule of Documentation Maintenance

**Documentation is not a historical record. It is a living specification.**

As an AI Agent, you will inevitably encounter situations during implementation where the initial design defined in these documents is impractical, incomplete, or superseded by user feedback during manual testing.

**YOUR MANTRA:** If you change the code logic, you MUST immediately update the corresponding documentation file to reflect that change.

* **NEVER** leave the code and the documentation in conflict.
* If the `Tech_Spec` says to use Library A, but you find Library B works better and implement it, you must rewrite the section in `Tech_Spec` to specify Library B and the rationale for the change.
* If the user manually tests a feature and requests a change to the gameplay loop, update the `Game_Design_Document` first, then write the code.

A project where documentation lags behind code is a project destined to fail. Keep it synchronized.

---

## 2. The Documentation Map (Where to look)

Use this index to locate the specific "Source of Truth" for any given task.

### Core Vision & Design
*   **[00_Game_Design_Document.md](file:///c:/GameDev/YokaiLoveTsumego/00_Game_Design_Document.md):** The "Constitution". Core mechanics, economy, and progression.
*   **[01_Tone_and_Appeal.md](file:///c:/GameDev/YokaiLoveTsumego/01_Tone_and_Appeal.md):** The "Vibe Check". Emotional pillars, "Intellectual Heroism", and "Steam Safe" boundaries.
*   **[02_Art_Style_Bible.md](file:///c:/GameDev/YokaiLoveTsumego/02_Art_Style_Bible.md):** Visual language, AI art prompts, and UI aesthetics ("Nostalgic 90s OVA").
*   **[07_Story_and_World_Bible.md](file:///c:/GameDev/YokaiLoveTsumego/07_Story_and_World_Bible.md):** Narrative lore, Demon Lord, and world logic.
*   **[13_core_gameplay_loop.md](file:///c:/GameDev/YokaiLoveTsumego/13_core_gameplay_loop.md):** Detailed breakdown of the loops (Immediate, Short, Medium, Long) and Streak mechanics.

### Technical Specifications
*   **[03_Architecture_Overview.md](file:///c:/GameDev/YokaiLoveTsumego/03_Architecture_Overview.md):** High-level stack (Tauri, React, PixiJS, Supabase) and "Central Square" UI strategy.
*   **[04_Tech_Spec_and_Conventions.md](file:///c:/GameDev/YokaiLoveTsumego/04_Tech_Spec_and_Conventions.md):** Coding standards, folder structure, and library versions.
*   **[05_Database_Schema.md](file:///c:/GameDev/YokaiLoveTsumego/05_Database_Schema.md):** PostgreSQL schema, RLS policies, and data types.
*   **[06_DevOps_and_Pipeline.md](file:///c:/GameDev/YokaiLoveTsumego/06_DevOps_and_Pipeline.md):** Local development workflow, migrations, and content pipeline.

### Feature Specifications
*   **[10_Tsumego_Algorithm_Spec.md](file:///c:/GameDev/YokaiLoveTsumego/10_Tsumego_Algorithm_Spec.md):** Requirements for the offline puzzle generation script (Python).
*   **[11_Teaching_Mode_Spec.md](file:///c:/GameDev/YokaiLoveTsumego/11_Teaching_Mode_Spec.md):** Requirements for the client-side AI analysis (God's Eye, Emotional Aura).
*   **[12_Tsumego_Generation_Engine.md](file:///c:/GameDev/YokaiLoveTsumego/12_Tsumego_Generation_Engine.md):** Architecture of the "Content Factory" pipeline.

### Content Data
*   **[08_Character_Roster_Yokai.md](file:///c:/GameDev/YokaiLoveTsumego/08_Character_Roster_Yokai.md):** Yokai companion definitions (Tama, Ren, Kagara, Miyuki).
*   **[09_Character_Roster_Goddesses.md](file:///c:/GameDev/YokaiLoveTsumego/09_Character_Roster_Goddesses.md):** Goddess companion definitions (Zero, Kata).

---

## 3. The Mission: Phase 1 Vertical Slice

Our immediate goal is NOT the full game. It is the "Skeleton" phase defined in section 8 of `00_Game_Design_Document.md`.

We need to prove that our hybrid tech stack works. The success criteria for this session are:
1.  A local development environment is running (Tauri/React frontend + Supabase Local backend).
2.  The database schema defined in `05_Database_Schema.md` is applied to the local database.
3.  We can load the React frontend in a Tauri window.
4.  The frontend successfully connects to the local Supabase database, fetches a single static SGF puzzle string, and renders it on a PixiJS board.

---

## 4. Constraints & Rules

* **Strict Adherence:** Follow the technology choices and folder structure defined in `03_Architecture_Overview.md` and `04_Tech_Spec_and_Conventions.md`.
* **Local Only:** Do not attempt to connect to any cloud services. Use Supabase CLI and Docker locally.
* **Version Control:** Initialize a git repository immediately.
* **Asset Safety:** Before creating any other files, create a `.gitignore` file that excludes `/public/assets` and `/assets-src` to ensure we don't accidentally commit future proprietary art assets to a public repo.

---

## 5. Execution Plan

Please proceed with the following steps. Stop after each step to confirm successful execution before moving to the next.

**Step 1: Initialization & Safety**
Initialize the git repo and create the correct `.gitignore` file to protect our future assets.

**Step 2: Scaffolding**
Set up the project skeleton using Vite (React + TypeScript) and initialize Tauri, following the structure laid out in the Tech Spec document. Install necessary dependencies (Zustand, PixiJS, Supabase client, React Router).

**Step 3: Backend Spin-up**
Initialize Supabase locally using the CLI. Create the first migration file containing the schema defined in `05_Database_Schema.md` and apply it to the local instance.

**Step 4: The "Hello World" of Go**
Create the basic React component structure. Implement a minimal PixiJS board renderer that can take an SGF string as a prop and render Black and White stones.

**Step 5: The Vertical Slice Connection**
Manually insert one row of dummy puzzle data into your local `puzzles` table via Supabase Studio. Modify the React frontend to fetch this puzzle via the Supabase JS client and render it on the PixiJS board.