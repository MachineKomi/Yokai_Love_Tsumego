Here is the **Technical Specification and Conventions** document. This is the detailed "rulebook" that will keep our coding agents (and human developers) aligned. It defines the folder structure, coding style, and specific patterns for integrating React, PixiJS, and Supabase, ensuring the codebase remains clean and maintainable.

-----

# 04_Tech_Spec_and_Conventions.md

# Technical Specification & Coding Conventions

**Project:** Tsumego Tower
**Version:** 1.0.0
**Context:** This document defines the mandatory technical standards, file structures, and coding patterns for the project. AI Agents must strictly adhere to these conventions to ensure code compatibility and maintainability.

-----

## 1\. Development Environment & Prerequisites

We use a **Local-First** development workflow. Agents must assume code is running in this environment before being deployed.

  * **Node Version:** `v20.x (LTS)` or higher.
  * **Package Manager:** `pnpm` (Chosen for speed and efficient disk usage in monorepo-like setups).
  * **Rust:** stable toolchain (Required for Tauri build).
  * **Docker Desktop:** Required for running Supabase Local.
  * **VS Code Extensions (Recommended):** ESLint, Prettier, Tailwind CSS IntelliSense, Tauri, Supabase.

-----

## 2\. Core Core Libraries & Version Pinning

We pin major versions to prevent breaking changes during automated code generation.

| Category | Library | Version Constraint | Notes |
| :--- | :--- | :--- | :--- |
| **Framework** | React | `^18.2.x` | Use Functional Components and Hooks only. |
| **Build Tool** | Vite | `^5.x` | |
| **Desktop** | Tauri | `v2.0.0-beta` (or latest v2 rc) | Utilizing v2 for mobile target capability. |
| **2D Engine** | PixiJS | `^8.x` | The WebGPU-ready version. |
| **State** | Zustand | `^4.x` | Transient updates allowed for high-frequency board interactions. |
| **Styling** | Tailwind CSS | `^3.4.x` | Used for layout and utility classes. |
| **Styling** | `clsx` / `tailwind-merge` | Latest | For conditional class naming without conflicts. |
| **Backend** | `@supabase/supabase-js` | `^2.x` | |
| **Routing** | `react-router-dom` | `^6.x` | For navigating between Lobby, Tower Map, and Game views. |

-----

## 3\. Project File Structure (The Map)

AI Agents must strictly adhere to this structure when creating new files.

```
/
├── /.github/workflows    # CI/CD pipelines
├── /src-tauri            # Rust host code (Tauri configuration)
├── /public               # Static assets served directly (favicon, robots.txt)
│   └── /assets           # GAME ASSETS (sprites, bg, audio) - Excluded from git if paid
├── /supabase             # Supabase CLI config (managed by CLI tools)
│   ├── /migrations       # SQL migration files
│   └── /seed.sql         # Local dev data seed script
├── /src
│   ├── /assets           # Code-referenced static assets (e.g., small UI icons)
│   ├── /components       # Shared, reusable UI components (Buttons, Modals)
│   │   └── /ui           # Shadcn-like base primitives
│   ├── /features         # Feature-based modules
│   │   ├── /game         # The core gameplay loop (Board view, stone logic)
│   │   ├── /tower        # Tower map selection view
│   │   ├── /waifu        # Companion interaction screens
│   │   └── /auth         # Login/Signup screens
│   ├── /hooks            # Custom shared React hooks
│   ├── /lib              # Utilities, API clients, Helpers
│   │   ├── supabase.ts   # The instantiated Supabase client
│   │   └── utils.ts      # CN helper, formatters
│   ├── /pixi             # PixiJS specific rendering logic (non-React)
│   │   ├── BoardRenderer.ts
│   │   └── VFXManager.ts
│   ├── /stores           # Zustand global state stores
│   ├── App.tsx           # Main routing layout
│   └── main.tsx          # Entrypoint
├── .env.local            # Local environment variables (API keys)
├── pnpm-lock.yaml
└── tauri.conf.json       # Tauri main config
```

-----

## 4\. Coding Conventions & Style

### A. TypeScript Rules

  * **Strict Mode:** Enabled in `tsconfig.json`.
  * **Explicit Types:** Do not use `any`. Define interfaces for all component props and API response shapes.
  * **Type Location:** Co-locate types with their component if specific, or in a `types.ts` file within the feature folder if shared.

### B. Naming Conventions

  * **React Components:** `PascalCase` (e.g., `StoneButton.tsx`). Do not use index.ts exports for components.
  * **Functions/Variables:** `camelCase` (e.g., `calculateScore`, `isActive`).
  * **Zustand Stores:** `use[Feature]Store` (e.g., `useGameStore`).
  * **Constant Values:** `UPPER_SNAKE_CASE` for hardcoded configuration values (e.g., `MAX_STREAK_MULTIPLIER`).

### C. AI Documentation & Commenting

AI agents often struggle with context. Comments must explain *intent*, not just mechanics.

  * **Bad:** `// Sets active to true`
  * **Good:** `// Activates the 'God's Eye' visualization layer when the user clicks the hint button, costing karma.`
  * **Top-Level Comments:** Every major component or hook should have a JSDoc block explaining its role in the larger architecture.

-----

## 5\. Integration Patterns

### A. React \<-\> PixiJS Bridge

We separate the UI logic from the high-performance rendering logic.

1.  **React is the Brain:** The React component tree handles state (whose turn it is, board config), UI overlays, and user input events.
2.  **PixiJS is the Renderer:** The `<PixiViewport />` React component initializes the Pixi Application in a `useEffect` and refs a canvas element.
3.  **Communication:** React uses `useEffect` hooks listening to state changes to call imperative methods on the Pixi renderer class.

<!-- end list -->

```typescript
// Example Pattern
// GameBoard.tsx (React)
const boardState = useGameStore((state) => state.board);
const pixiRef = useRef<BoardRenderer | null>(null);

// Initialize Pixi once
useEffect(() => {
  pixiRef.current = new BoardRenderer(canvasRef.current);
  return () => pixiRef.current.destroy();
}, []);

// React tells Pixi to update when state changes
useEffect(() => {
  if (pixiRef.current && boardState) {
    pixiRef.current.updateStones(boardState);
  }
}, [boardState]);
```
Here is the **Technical Specification and Conventions** document. This is the detailed "rulebook" that will keep our coding agents (and human developers) aligned. It defines the folder structure, coding style, and specific patterns for integrating React, PixiJS, and Supabase, ensuring the codebase remains clean and maintainable.

-----

# 04_Tech_Spec_and_Conventions.md

# Technical Specification & Coding Conventions

**Project:** Tsumego Tower
**Version:** 1.0.0
**Context:** This document defines the mandatory technical standards, file structures, and coding patterns for the project. AI Agents must strictly adhere to these conventions to ensure code compatibility and maintainability.

-----

## 1\. Development Environment & Prerequisites

We use a **Local-First** development workflow. Agents must assume code is running in this environment before being deployed.

  * **Node Version:** `v20.x (LTS)` or higher.
  * **Package Manager:** `pnpm` (Chosen for speed and efficient disk usage in monorepo-like setups).
  * **Rust:** stable toolchain (Required for Tauri build).
  * **Docker Desktop:** Required for running Supabase Local.
  * **VS Code Extensions (Recommended):** ESLint, Prettier, Tailwind CSS IntelliSense, Tauri, Supabase.

-----

## 2\. Core Core Libraries & Version Pinning

We pin major versions to prevent breaking changes during automated code generation.

| Category | Library | Version Constraint | Notes |
| :--- | :--- | :--- | :--- |
| **Framework** | React | `^18.2.x` | Use Functional Components and Hooks only. |
| **Build Tool** | Vite | `^5.x` | |
| **Desktop** | Tauri | `v2.0.0-beta` (or latest v2 rc) | Utilizing v2 for mobile target capability. |
| **2D Engine** | PixiJS | `^8.x` | The WebGPU-ready version. |
| **State** | Zustand | `^4.x` | Transient updates allowed for high-frequency board interactions. |
| **Styling** | Tailwind CSS | `^3.4.x` | Used for layout and utility classes. |
| **Styling** | `clsx` / `tailwind-merge` | Latest | For conditional class naming without conflicts. |
| **Backend** | `@supabase/supabase-js` | `^2.x` | |
| **Routing** | `react-router-dom` | `^6.x` | For navigating between Lobby, Tower Map, and Game views. |

-----

## 3\. Project File Structure (The Map)

AI Agents must strictly adhere to this structure when creating new files.

```
/
├── /.github/workflows    # CI/CD pipelines
├── /src-tauri            # Rust host code (Tauri configuration)
├── /public               # Static assets served directly (favicon, robots.txt)
│   └── /assets           # GAME ASSETS (sprites, bg, audio) - Excluded from git if paid
├── /supabase             # Supabase CLI config (managed by CLI tools)
│   ├── /migrations       # SQL migration files
│   └── /seed.sql         # Local dev data seed script
├── /src
│   ├── /assets           # Code-referenced static assets (e.g., small UI icons)
│   ├── /components       # Shared, reusable UI components (Buttons, Modals)
│   │   └── /ui           # Shadcn-like base primitives
│   ├── /features         # Feature-based modules
│   │   ├── /game         # The core gameplay loop (Board view, stone logic)
│   │   ├── /tower        # Tower map selection view
│   │   ├── /waifu        # Companion interaction screens
│   │   └── /auth         # Login/Signup screens
│   ├── /hooks            # Custom shared React hooks
│   ├── /lib              # Utilities, API clients, Helpers
│   │   ├── supabase.ts   # The instantiated Supabase client
│   │   └── utils.ts      # CN helper, formatters
│   ├── /pixi             # PixiJS specific rendering logic (non-React)
│   │   ├── BoardRenderer.ts
│   │   └── VFXManager.ts
│   ├── /stores           # Zustand global state stores
│   ├── App.tsx           # Main routing layout
│   └── main.tsx          # Entrypoint
├── .env.local            # Local environment variables (API keys)
├── pnpm-lock.yaml
└── tauri.conf.json       # Tauri main config
```

-----

## 4\. Coding Conventions & Style

### A. TypeScript Rules

  * **Strict Mode:** Enabled in `tsconfig.json`.
  * **Explicit Types:** Do not use `any`. Define interfaces for all component props and API response shapes.
  * **Type Location:** Co-locate types with their component if specific, or in a `types.ts` file within the feature folder if shared.

### B. Naming Conventions

  * **React Components:** `PascalCase` (e.g., `StoneButton.tsx`). Do not use index.ts exports for components.
  * **Functions/Variables:** `camelCase` (e.g., `calculateScore`, `isActive`).
  * **Zustand Stores:** `use[Feature]Store` (e.g., `useGameStore`).
  * **Constant Values:** `UPPER_SNAKE_CASE` for hardcoded configuration values (e.g., `MAX_STREAK_MULTIPLIER`).

### C. AI Documentation & Commenting

AI agents often struggle with context. Comments must explain *intent*, not just mechanics.

  * **Bad:** `// Sets active to true`
  * **Good:** `// Activates the 'God's Eye' visualization layer when the user clicks the hint button, costing karma.`
  * **Top-Level Comments:** Every major component or hook should have a JSDoc block explaining its role in the larger architecture.

-----

## 5\. Integration Patterns

### A. React <-> PixiJS Bridge

We separate the UI logic from the high-performance rendering logic.

1.  **React is the Brain:** The React component tree handles state (whose turn it is, board config), UI overlays, and user input events.
2.  **PixiJS is the Renderer:** The `<PixiViewport />` React component initializes the Pixi Application in a `useEffect` and refs a canvas element.
3.  **Communication:** React uses `useEffect` hooks listening to state changes to call imperative methods on the Pixi renderer class.

<!-- end list -->

```typescript
// Example Pattern
// GameBoard.tsx (React)
const boardState = useGameStore((state) => state.board);
const pixiRef = useRef<BoardRenderer | null>(null);

// Initialize Pixi once
useEffect(() => {
  pixiRef.current = new BoardRenderer(canvasRef.current);
  return () => pixiRef.current.destroy();
}, []);

// React tells Pixi to update when state changes
useEffect(() => {
  if (pixiRef.current && boardState) {
    pixiRef.current.updateStones(boardState);
  }
}, [boardState]);
```

### B. Styling Implementation (The "Nostalgic" Look)

To achieve the tactile, painterly aesthetic defined in the Art Bible, we use a hybrid approach:

1.  **Tailwind CSS:** Used for layout structure (flex, grid, margins, padding, responsive breakpoints).
2.  **Custom CSS Classes / Tailwind Plugin:** Used for textures. We will not rely on flat colors.
      * *Example:* Instead of `bg-brown-500`, we will use custom classes that apply repeating texture images defined in the public assets, e.g., `bg-texture-wood` or `border-texture-gold`.

### C. UX & Layout Strategy (Central Square)

To ensure cross-platform consistency:
*   **Container:** The Board View must be wrapped in a responsive container that maintains a **1:1 aspect ratio**.
*   **Responsive Logic:**
    *   *Landscape (Desktop):* `flex-row`. Board in center, panels on sides.
    *   *Portrait (Mobile):* `flex-col`. Board in center, panels stacked.

### D. Input Configuration

*   **One Tap Move:** (Default for Speed) Tap intersection $\rightarrow$ Submit immediately.
*   **Tap + Confirm:** (Precision Mode) Tap intersection $\rightarrow$ Show ghost stone + Confirm Button $\rightarrow$ Submit.
*   **Zoom:** Mobile users must be able to pinch-zoom the PixiJS canvas for precision.

### E. Backend Integration (Supabase)

  * **Environment Variables:** Never hardcode API keys. Use `import.meta.env.VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY`.
  * **Data Access Layer:** Do not make `supabase.from('...').select()` calls directly inside UI components. Abstract them into async functions within `/src/lib/api/[feature].ts` (e.g., `/src/lib/api/puzzles.ts`).
  * **Row Level Security (RLS):** Agents must assume RLS is active. All queries should be written assuming the backend will automatically filter data based on the authenticated user's JWT.