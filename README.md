# Yokai Love Tsumego

> **"Solve Puzzles. Romance Spirits."**

[![License: AGPL v3](https://img.shields.io/badge/License-AGPL_v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)
![Status](https://img.shields.io/badge/Status-Pre--Alpha_Prototype-yellow)

## 📖 About The Game

**Yokai Love Tsumego** is a hybrid casual puzzle game and visual novel with a "Nostalgic 90s Anime" aesthetic.

In a world where logic is magic, you play as a Climber ascending an infinite, chaotic spire. To advance, you must purify corrupted spirits by solving authentic **Tsumego** (Go life-and-death puzzles) of increasing difficulty. Your brilliant moves transform fierce Yokai—from playful Kitsune to fierce Oni—into devoted companions whose hearts you capture between battles in a fully-fleshed dating sim meta-game.

---

## ⚖️ License & Legal

This project employs a hybrid licensing model to allow open-source code collaboration while protecting the game's commercial artistic intellectual property.

### The Source Code (AGPL-3.0)
The source code of this project (everything within `/src`, `/src-tauri`, etc.) is licensed under the **GNU Affero General Public License v3.0 (AGPL-3.0)**.

This means you are free to study, modify, and redistribute the code, but if you use it to build a networked service or game for the public, you must open-source your entire project under the same license. See the [LICENSE](LICENSE) file for details.

### 🚫 The Assets (All Rights Reserved)
**IMPORTANT EXCLUSION:** The AGPL-3.0 license applied to this repository governs the source code **only**.

All artistic and narrative assets, including but not limited to:
* Character sprites, portraits, and concept art (the "Waifus/Yokai")
* Background artwork and environment textures
* UI elements, icons, and button graphics
* Music tracks and sound effects
* Narrative story text, character dialogue scripts, and world lore

Are **All Rights Reserved (ARR)** and are the proprietary intellectual property of the creators. They may **not** be used, redistributed, modified, or sold without explicit written permission, except as part of playing the official build of the game.

> **Note to Developers:** Placeholder assets included in this public repo for development purposes fall under this ARR clause unless stated otherwise.

---

## 🏗️ Tech Stack

We are building a high-performance hybrid application targeting Desktop (Steam) and Mobile (iOS/Android).

* **Frontend:** [React 18](https://react.dev/) + TypeScript + [Vite](https://vitejs.dev/)
* **Game Rendering:** [PixiJS v8](https://pixijs.com/) (WebGL2 for the board and VFX)
* **Desktop Host:** [Tauri v2](https://tauri.app/) (Rust)
* **State Management:** [Zustand](https://github.com/pmndrs/zustand)
* **Backend-as-a-Service:** [Supabase](https://supabase.com/) (PostgreSQL, Auth, Storage)
* **Go AI Engine:** [KataGo](https://github.com/lightvector/KataGo) (WASM for client-side analysis, Python for offline puzzle generation)

---

## ⚡ Getting Started (Local Development)

We use a **Local-First** development workflow. You do not need internet access to cloud APIs to develop this game; a full replica of the backend runs on your machine via Docker.

### Prerequisites
Ensure you have the following installed before starting:
* [Node.js (LTS v20+)](https://nodejs.org/) & `pnpm`
* [Rust Toolchain](https://www.rust-lang.org/tools/install) (for Tauri)
* [Docker Desktop](https://www.docker.com/products/docker-desktop/) (must be running for Supabase Local)
* [Supabase CLI](https://supabase.com/docs/guides/cli/getting-started) (`brew install supabase/tap/supabase` or equivalent)

### Installation & Startup

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/yourusername/yokai-love-tsumego.git](https://github.com/yourusername/yokai-love-tsumego.git)
    cd yokai-love-tsumego
    ```

2.  **Install dependencies:**
    ```bash
    pnpm install
    ```

3.  **Start the Local Backend:**
    Make sure Docker is running, then fire up Supabase. The first time you run this, it will take a while to download the necessary Docker images.
    ```bash
    npx supabase start
    ```
    *This command also automatically applies existing database migrations to your local Postgres instance.*

4.  **Start the Game Client:**
    This will start the React dev server and launch the Tauri desktop window.
    ```bash
    pnpm tauri dev
    ```

---

## 📂 Project Structure Overview

* `/src`: The React frontend application.
* `/src/pixi`: The isolated PixiJS rendering engine logic.
* `/src-tauri`: The Rust backend for the desktop wrapper.
* `/supabase`: Supabase local configuration, migrations (SQL), and seed data.
* `/public`: Static assets served directly.
