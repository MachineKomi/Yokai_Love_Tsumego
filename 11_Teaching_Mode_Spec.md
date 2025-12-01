Here is the **Teaching Mode Specification**. This document defines the functional and visual requirements for the game's most technically complex feature: the real-time AI analysis engine running in the browser.

It bridges the gap between the raw mathematical output of KataGo (WASM) and the "magical, painterly" aesthetic defined in the Art Bible, providing clear instructions for both the React logic developers and the PixiJS graphics developers.

***

# 11_Teaching_Mode_Spec.md

# Teaching Mode Specification: The "God's Eye"
**Project:** Tsumego Tower
**Version:** 1.0.0
**Context:** This document defines the functional requirements, technical implementation, and visualization standards for the client-side AI analysis features. It informs the development of the React state machine, the KataGo Web Worker, and the PixiJS VFX layer.

---

## 1. Feature Overview & Goal

**Goal:** To transform the abstract, invisible concepts of Go (territory, influence, aji, life & death) into tangible, visually intuitive effects. This empowers players of all levels to understand *why* a move is good or bad without needing to read dense text explanations.

**User Story:** "As a 15k player, I just failed a puzzle. I don't know why my move was wrong. I enter Teaching Mode, and immediately see a jagged red aura appear where I played, indicating I created a weakness. I click the AI's suggested green move, and the aura turns a calm blue, showing secure territory. I now understand the shape intuitively."

---

## 2. Technical Architecture (The AI Engine)

To ensure the UI remains buttery smooth (60fps) on mobile devices, the heavy lifting of AI analysis must happen off the main thread.

1.  **The Engine:** KataGo compiled to WebAssembly (WASM).
2.  **Execution Environment:** A dedicated browser Web Worker.
3.  **Communication:** The main React app communicates with the worker via asynchronous message passing.

**Data Flow Loop:**
1.  **React:** User places a stone or enters review mode.
2.  **React -> Worker:** Sends current board state (SGF string or array) + settings (e.g., number of playouts).
3.  **Worker (KataGo):** Runs analysis (thinking time approx. 500ms - 2s depending on device).
4.  **Worker -> React:** Returns a JSON object containing:
    * `winrate`: Current estimated win probability for Black.
    * `scoreLead`: Estimated point difference.
    * `ownership`: A 361-length array (19x19) containing values from -1.0 (certain White territory) to +1.0 (certain Black territory).
    * `candidateMoves`: Top 3-5 suggested next moves with their associated winrate drops.
5.  **React -> PixiJS:** React updates the `teachingModeState` store. The PixiJS renderer component reacts to this state change and updates the visual effects on the canvas.

---

## 3. Functional Modes

The Teaching Mode exists in two distinct states based on the user's context.

### Mode A: The "Hint" (During Gameplay)
* **Trigger:** Player clicks the "AI Hint" button.
* **Availability:** Always available (limited only by the daily puzzle cap for free players).
* **Functionality:** Restricted.
    * Does **not** show the full ownership aura (too distracting/spoiler-heavy during a puzzle).
    * **Does** show the top 1 best move as a pulsing, glowing indicator on the board for 5 seconds.

### Mode B: Post-Game Review (After Win/Loss)
* **Trigger:** Player completes a puzzle (success or failure) and clicks "Analyze" on the results screen.
* **Functionality:** Unrestricted full access.
    * Full "Emotional Aura" visualization enabled.
    * Top 3 candidate moves shown continuously.
    * **Variation Exploration:** Player can click anywhere on the board to play out hypothetical moves. The AI instantly re-analyzes the new position, updating the aura and suggestions in real-time.

---

## 4. Visualization Specifications (The "Art" of Data)

This section defines how raw KataGo data translates into the "Nostalgic Romance/Magical" aesthetic defined in the Art Bible. These effects are rendered via PixiJS shaders and particle containers.

### A. The "Emotional Aura" (Territory/Ownership)
Data Source: KataGo `ownership` array grid.

| Ownership Value | Meaning | Visual Effect (PixiJS) | Art Bible Vibe |
| :--- | :--- | :--- | :--- |
| **> +0.6 (Black)** | Secure Black Territory | Deep Blue/Indigo glowing mist. Slow, calm particle movement. High opacity at center of region. | "Calm Spiritual Pressure" |
| **< -0.6 (White)** | Secure White Territory | Bright Cyan/White glowing mist. Slow, calm particle movement. | "Divine Light" |
| **-0.3 to +0.3** | Contested / Aji / Unclear | **Crucial:** Jagged, flickering electrical haze. Alternating Orange/Red colors. Faster, agitated particle movement. | "Chaos / Conflict Zone" |

* **Transitions:** The aura must interpolate smoothly when the board state changes. It should not snap instantly. A 300ms cross-fade between states makes it feel organic.

### B. Stone Status Visualization
Data Source: Derived from `ownership` and life/death status.

* **Dead Stones:** If stones are on the board but sit in a region with high opposing ownership, overlay a subtle, desaturated "cracked stone" texture and lower their opacity slightly. They should look like ghosts of stones.

### C. Candidate Moves (Suggestions)
Data Source: KataGo `candidateMoves` array.

Visualized as glowing circular indicators on empty intersections. Color-coded by quality relative to the best move.

* **Best Move (Top 1):** Bright Green pulse, golden outer ring glow.
* **Good Moves (Top 2-3, <5% winrate drop):** Yellow pulse.
* **Bad Moves (High winrate drop):** (Only shown if player hovers them in review mode) Red warning reticle.

### D. The Win Rate Graph
Data Source: Historical `winrate` data over the course of the puzzle attempt.

* **Location:** A semi-transparent wooden panel.
    *   **Desktop:** Floating to the side of the board.
    *   **Mobile:** Stacked below the board (Central Square compliant).
* **Style:** A simple line graph.
    * X-Axis: Move Number.
    * Y-Axis: Win Rate (0-100%).
    * Line Color: Glowing Blue.
    * The current move is marked with a glowing dot.
* **Interaction:** Clicking a point on the graph jumps the board state back to that move in the review mode.