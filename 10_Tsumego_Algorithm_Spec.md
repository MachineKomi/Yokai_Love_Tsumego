Here is the **Tsumego Algorithm Specification**. This document is a technical requirement spec for the "Offline Puzzle Factory." It is written primarily for the AI agent (or human developer) who will write the Python scripts that generate the game's content.

It defines the input data, the processing steps using KataGo, and the exact JSON output format required by the Supabase database schema we defined earlier.

-----

# 10_Tsumego_Algorithm_Spec.md

# Tsumego Generation Algorithm Specification

**Project:** Tsumego Tower
**Version:** 1.0.0
**Context:** This document defines the requirements for the offline Python pipeline that generates high-quality Go puzzles (Tsumego) from professional game records using KataGo.

-----

## 1\. Pipeline Overview

**Objective:** To programmatically create a large, tagged library of life-and-death puzzles ranging from 25k to 9d difficulty without human intervention.

**Input:**

  * A large library of SGF files (e.g., Pro games, high-dan Fox/Tygem games).
  * A local installation of the KataGo engine (latest release) with a strong network file.

**Output:**

  * A JSONL (JSON Lines) file, where each line is a self-contained puzzle object ready for bulk insertion into the Supabase `puzzles` table.

-----

## 2\. The Puzzle Data Structure (JSON Schema)

The output JSON object for a single puzzle must adhere to this structure to match the database schema.

```json
{
  "floor_number": 105,           // Integer: Assigned post-generation based on difficulty score
  "difficulty": "3d",            // String (Enum): Mapped from KataGo score loss/complexity
  "initial_state_sgf": "(;GM[1]FF[4]CA[UTF-8]SZ[19]AB[pd][qc][qb][rb]AW[nc][oc][pb][qa]C[Black to play. How can Black live unconditionally?])",
  "solution_sgf": "(;B[ra];W[sb];B[rc]C[Correct. Black lives.])",
  "tags": ["life-and-death", "corner", "snapback"], // Array of strings
  "metadata": {
    "source_game_hash": "a1b2c3d4...", // Unique ID of the source SGF for provenance
    "move_number": 145,                // Move number where the puzzle state occurred
    "color_to_play": "black",
    "katago_score_loss": 0.1,          // The score loss of the *actual* move played in game (must be a blunder)
    "katago_winrate_drop": 0.45,       // The winrate drop caused by the blunder
    "board_region": "TR"               // TR=Top Right, BL=Bottom Left, etc.
  }
}
```

4.  **Output:** Generate 8 distinct JSON objects for each unique puzzle source, with unique IDs but linking back to the same `source_game_hash`.

-----

## 6. Final Output Generation

Once a batch of puzzles is generated, expanded, and verified:

1.  **Sort:** Sort puzzles by their calculated difficulty score from easiest to hardest.
2.  **Assign Floors:** Assign `floor_number` based on the sorted order. (e.g., Puzzles 0-99 go to Floors 1-10).
3.  **Write JSONL:** Append the final puzzle objects to the output `puzzles.jsonl` file.

## 7. Player Rank Calculation (Elo System)

While puzzles have static difficulty, the Player's rank is dynamic.

*   **Algorithm:** Weighted Elo rating.
*   **Placement Test:** New users play 5 puzzles (range of difficulties). Performance seeds their initial Rank.
*   **Updates:** Solving a puzzle increases Elo; failing decreases it. K-factor is higher for lower ranks (faster movement) and stabilizes at Dan levels.
*   **Fallback:** If a player exhausts puzzles at their exact Rank, serve puzzles +/- 2 Ranks away.