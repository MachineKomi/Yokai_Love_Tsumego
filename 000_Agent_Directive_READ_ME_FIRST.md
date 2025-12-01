### The Final Core File: The "Agent Meta-Guide"

This file is the most crucial one for maintaining project coherence. It tells the agents how to use the other 12 files and enforces the rule that documentation must live and breathe with the code.

**File Name:** `000_Agent_Directive_READ_ME_FIRST.md`

***

# 000\_Agent\_Directive\_READ\_ME\_FIRST.md

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

Use this table to determine which file governs the task you are currently performing.

| If you are working on... | You MUST reference... |
| :--- | :--- |
| **Understanding the overall vision, loop, and monetization.** | `00_Game_Design_Document.md` |
| **Generating art prompts, writing CSS, or designing UI components.** | `01_Tone_and_Appeal.md` AND `02_Art_Style_Bible.md` |
| **Choosing libraries, setting up frameworks, or understanding the stack.** | `03_Architecture_Overview.md` |
| **Writing code, naming variables, structuring folders, or creating components.** | `04_Tech_Spec_and_Conventions.md` |
| **Writing SQL, creating migrations, or fetching data in the frontend.** | `05_Database_Schema.md` |
| **Running the local dev environment, seeding data, or building for deployment.** | `06_DevOps_and_Pipeline.md` |
| **Writing narrative text, character dialogue, or world-building flavor text.** | `07_Story_and_World_Bible.md` |
| **Creating assets or dialogue specifically for Yokai characters.** | `08_Character_Roster_Yokai.md` |
| **Creating assets or dialogue specifically for Goddess characters.** | `09_Character_Roster_Goddesses.md` |
| **Working on the Python script that generates puzzles offline.** | `10_Tsumego_Algorithm_Spec.md` AND `12_Tsumego_Generation_Engine.md` |
| **Implementing the real-time KataGo WASM analysis or PixiJS visualizations.** | `11_Teaching_Mode_Spec.md` |

---

## 3. Workflow Constraints (Non-Negotiable)

1.  **Local-First Development:** You must assume there is no internet connection to the production cloud. All development happens against Supabase Local via Docker.
2.  **Asset Protection:** You must never commit high-resolution art assets to the public git repository. Ensure `.gitignore` is correctly configured for `/assets-src` (raw) and `/public/assets` (game-ready).
3.  **Hybrid Tech Stack:** Respect the boundary between React (UI/Menus) and PixiJS (Board/VFX). Do not try to render 361 stones using HTML DOM elements.

***

### The Initial Prompt to the Antigravity Agent

Copy and paste the text below into your first session with the Gemini 3 Pro coding agent in your IDE.

---

**PROMPT START**

Hello Agent. We are starting a new project called "Tsumego Tower".

It is a hybrid mobile/desktop game that combines rigorous Go (Baduk/Weiqi) puzzles with a 90s anime-style dating sim aesthetic.

I have already done the heavy lifting of defining the entire project. Your immediate task is to absorb this context and then execute the setup for the initial "Vertical Slice" prototype.

### 1. The Context Inputs
In the root of this repository, you will find a series of numbered Markdown files ranging from `000_Agent_Directive_READ_ME_FIRST.md` to `12_Tsumego_Generation_Engine.md`.

**Action:** Read file `000_Agent_Directive_READ_ME_FIRST.md` first. It explains the rules of engagement and how to use the other documents. Then, read all remaining documents to build a comprehensive mental model of the product, architecture, art style, and technical constraints.

### 2. The Mission: Phase 1 Vertical Slice
Our immediate goal is NOT the full game. It is the "Skeleton" phase defined in section 8 of `00_Game_Design_Document.md`.

We need to prove that our hybrid tech stack works. The success criteria for this session are:
1.  A local development environment is running (Tauri/React frontend + Supabase Local backend).
2.  The database schema defined in `05_Database_Schema.md` is applied to the local database.
3.  We can load the React frontend in a Tauri window.
4.  The frontend successfully connects to the local Supabase database, fetches a single static SGF puzzle string, and renders it on a PixiJS board.

### 3. Constraints & Rules
* **Strict Adherence:** Follow the technology choices and folder structure defined in `03_Architecture_Overview.md` and `04_Tech_Spec_and_Conventions.md`.
* **Local Only:** Do not attempt to connect to any cloud services. Use Supabase CLI and Docker locally.
* **Version Control:** Initialize a git repository immediately.
* **Asset Safety:** Before creating any other files, create a `.gitignore` file that excludes `/public/assets` and `/assets-src` to ensure we don't accidentally commit future proprietary art assets to a public repo.

### 4. Execution Plan
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

Let's begin with Step 1.

**PROMPT END**