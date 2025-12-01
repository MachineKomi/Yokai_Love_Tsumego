Here is the **Tsumego Generation Engine** specification. This document defines the "Content Factory" of the game. It is an offline, Python-based data pipeline responsible for procedurally generating the infinite supply of puzzles required to keep players climbing the tower.

It integrates the algorithmic steps previously discussed into a broader system view, focusing on data sources, processing stages, and quality control.

-----

# 12_Tsumego_Generation_Engine.md

# Tsumego Generation Engine: The Content Factory

**Project:** Tsumego Tower
**Version:** 1.0.0
**Context:** This document defines the offline Python infrastructure used to procedurally generate Go puzzles from historical and modern game records. It is meant for the backend/AI engineers building the data pipeline.

-----

## 1\. Executive Summary

The **Tsumego Generation Engine** is an offline, automated Python pipeline. It solves the "content treadmill" problem by turning millions of existing Go game records into unique, graded, life-and-death puzzles.

It does not run in the client or the cloud backend. It runs on a dedicated development machine (or high-compute VPS) to generate static assets (JSONL files) which are then seeded into the Supabase production database.

**Core Philosophy:** "Every blunder is a puzzle waiting to be discovered."

-----

## 2\. Data Sources (The Raw Material)

To ensure variety across 100+ floors, we require a massive, diverse library of SGF (Smart Game Format) files.

### A. Source Categories

1.  **Historical Pro Games (The Classics):**
      * *Source:* Public databases (e.g., U-Go, GoGoD archives if licensed).
      * *Characteristics:* Classic shapes, varied joseki, fundamental life & death. Good for low-to-mid tier floors.
2.  **Modern AI Self-Play (The Aliens):**
      * *Source:* KataGo public training data, TCEC archives, OGS high-level bot games.
      * *Characteristics:* Incomprehensibly deep reading, bizarre vital points, sacrificing huge groups for influence. Perfect for "Goddess Tier" high floors and boss nodes.
3.  **High-Dan Amateur Games (The Brawlers):**
      * *Source:* Publicly available archives from major Go servers (Fox, Tygem, OGS) - *Must ensure legal compliance for commercial use.*
      * *Characteristics:* Messy, aggressive fighting games rich with practical, common mistakes. Excellent for mid-tier "Yokai" floors.

### B. Ingestion Strategy

  * The pipeline must accept a directory path containing thousands of `.sgf` files.
  * **SGF Cleaner:** A pre-processing script must normalize SGFs (handle varying character encodings, strip unnecessary chat logs, fix broken property tags) before passing them to the analysis engine.

-----

## 3\. The Pipeline Architecture (Python + KataGo)

The engine operates in a five-stage conveyor belt process.

### Stage 1: The Blunder Hunter (Analysis)

  * **Input:** Cleaned SGF file.
  * **Process:** The script runs a full-game analysis using a local, high-strength KataGo engine.
  * **Logic:** It iterates through every move of the game, looking for sharp drops in winrate.
  * **Candidate Criteria:** A position becomes a puzzle candidate if:
      * The actual move played lost significantly (e.g., > 15% winrate drop OR > 5 points score loss).
      * The game state before the move was relatively close (not already a 99% blowout).

### Stage 2: The Localizer (Cropping)

Tsumego are usually local problems, not full-board ones.

  * **Input:** Full board state at the moment of the blunder.
  * **Process:**
    1.  Identify the "active stones" (the group that died/lived due to the blunder).
    2.  Calculate a bounding box around these stones plus a padding of ~3 intersections.
    3.  **Crucial Verification:** Run KataGo on the *cropped* position. Does the life/death status match the *full* board state? If cropping cuts off a vital ladder breaker or connecting stone outside the box, the puzzle is invalid and must be discarded.

### Stage 3: The Validator (Uniqueness Check)

A good puzzle must have exactly one clear correct move.

  * **Input:** Validated cropped position.
  * **Process:** KataGo analyzes the cropped position for the side whose turn it is.
  * **Rejection Criteria (Discard if):**
      * **Ambiguity:** The top two suggested moves have very similar winrates (e.g., Move A wins by 20 points, Move B wins by 19 points). This is a bad puzzle.
      * **Ko:** For the initial release, we prioritize unconditional life/death. Puzzles where the best result is a Ko fight are discarded (saved for a future update).
      * **Too Deep:** If KataGo requires 50k+ playouts to find the solution, it's too hard for a mobile game.

### Stage 4: The Solver (Solution Generation)

  * **Process:** Once a unique best move is identified, the engine plays out the sequence against itself (KataGo vs KataGo) for 5-15 ply to generate the "Correct Variation" SGF tree.

### Stage 5: The Grader (Difficulty & Tagging)

  * **Input:** The final puzzle state and solution sequence.
  * **Process:** An algorithm assigns a difficulty score (0-100) based on:
      * **Depth:** How many moves is the solution sequence?
      * **Breadth:** How many distinct branches did KataGo consider?
      * **Score swing:** How subtle is the winning move?
  * **Mapping:** The score is mapped to our database enum (`25k`, `10k`, `1d`, etc.).
  * **Tagging:** AI heuristically tags the puzzle based on location (`corner`, `side`) and detected shapes (`snapback`, `liberty-shortage`).

### Stage 6: The Multiplier (Permutation Engine)

To maximize content efficiency, every valid puzzle is expanded into 8 variations.

1.  **Rotations:** 0°, 90°, 180°, 270°.
2.  **Flips:** Mirror X (or Color Swap if applicable).
3.  **Output:** 1 Raw Puzzle $\rightarrow$ 8 Unique Database Entries.
    *   *Note:* Each variation gets a unique ID but shares the `source_sgf_hash` for deduplication logic if needed.

-----

## 4\. Output & Database Integration

The final output of a generation run is a large `puzzles.jsonl` (JSON Lines) file ready for bulk ingestion.

### JSON Schema (Matching `05_Database_Schema.md`)

```json
{
  // Calculated by The Grader
  "difficulty": "3d",
  // The cropped problem state
  "initial_state_sgf": "(;GM[1]...SZ[19]AB[pd][qc]...C[Black to play.])",
  // The generated solution sequence
  "solution_sgf": "(;B[ra];W[sb];B[rc]...)",
  // Generated by The Grader
  "tags": ["life-and-death", "corner"],
  // Provenance data for debugging/credits
  "metadata": {
    "source_sgf_hash": "a1b2c3d4...",
    "original_move_number": 145,
    "katago_winrate_drop": 0.22
  }
  // Note: "floor_number" is assigned during database seeding, not generation.
}
```

### Seeding Workflow

1.  Run generation script -> produces `output_v1.jsonl`.
2.  Run a TypeScript/Node script that reads the JSONL, sorts puzzles by difficulty, assigns floor numbers dynamically, and generates a massive SQL `INSERT` statement.
3.  This SQL is saved as a Supabase migration file (e.g., `supabase/migrations/20251129_seed_puzzles.sql`) and pushed to production.

-----

## 5\. Quality Control (Human-in-the-Loop)

AI generation isn't perfect. We need a review step.

  * **The "Puzzle Browser" Tool:** A simple local-only web page (React+PixiJS) that loads the generated JSONL file.
  * **Workflow:** The developer quickly clicks through generated puzzles. They can press 'D' to delete bad puzzles (boring ladders, unclear shapes) or 'A' to approve them.
  * **Final Polish:** Only approved puzzles make it into the final database seed script.

-----

## 6. Evaluation Strategy: Pre-baked vs. Live Analysis

To ensure the "Pre-baked" approach is sufficient for a premium experience, we must validate it against Live Analysis during the prototype phase.

### A. The Hypothesis
We believe that for 95% of Tsumego (which are local problems), a pre-calculated tree of depth 3-5 covers all reasonable human moves. Live KataGo analysis on the client is a fallback, not the primary engine.

### B. The Test Plan
1.  **Generate a Test Batch:** Create 100 puzzles using the pipeline.
2.  **Human Testing:** Have a strong player (Dan level) attempt to "break" the puzzle by playing valid but non-optimal moves that might not be in the pre-baked tree.
3.  **Success Criteria:**
    *   If the pre-baked tree contains the refutation for the human's move, it is a **Pass**.
    *   If the game says "Wrong Move" but cannot show *why* (because the move wasn't in the tree), it is a **Fail**.
4.  **Decision Gate:**
    *   If Failure Rate < 5%: We proceed with Pre-baked only (and maybe a generic "Incorrect" message for unknown moves).
    *   If Failure Rate > 5%: We **MUST** implement the Client-Side KataGo (WASM) fallback to handle these edge cases in real-time.