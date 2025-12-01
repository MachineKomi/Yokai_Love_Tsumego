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

-----

## 3\. Algorithm Steps

The pipeline script (`generate_puzzles.py`) should perform the following steps for each SGF file in the input directory.

### Step 1: Game Analysis (Blunder Detection)

1.  Load the SGF file.
2.  Run a full KataGo analysis on the entire game.
3.  Iterate through every move.
4.  **Identify Candidates:** Flag a position as a potential puzzle candidate if:
      * The actual move played in the game resulted in a significant winrate drop (e.g., `> 20%`) OR a significant score loss (e.g., `> 5 points`). This indicates a **Blunder**.
      * The position before the blunder was relatively balanced or winning for the player whose turn it is.

### Step 2: Position Isolation & Cropping

Tsumego are rarely full-board problems. We must isolate the relevant local battle.

1.  **Identify Active Groups:** For the candidate move, identify the stone groups directly involved in the blunder and KataGo's suggested best move.
2.  **Determine Bounding Box:** Calculate the minimum bounding box that encompasses all active groups + a padding of 2-3 intersections on all sides.
3.  **Crop & Verify:** Create a new, smaller board state containing only stones within the bounding box.
      * *Crucial Check:* Does the cropped position still make sense? Are the groups' life/death status identical to the full board status? If cropping cuts off vital connecting stones that change the status, discard this candidate.

### Step 3: Solution Verification (The "Uniqueness" Test)

A good tsumego must have **exactly one** optimal move.

1.  **Analyze Cropped Position:** Run KataGo analysis on the cropped position for the color to play.
2.  **Get Top Moves:** Retrieve the top 3 suggested moves.
3.  **Check Winrates:**
      * **Move A (Best Move):** Must have a high winrate/score (e.g., 99% winrate, +10 points).
      * **Move B (Second Best):** Must have a significantly lower winrate/score (e.g., \< 10% winrate, -20 points).
4.  **Filter:** If Move A and Move B are too close in value (e.g., both lead to a ko, or both live but with a 1-point difference), **discard the puzzle**. It is ambiguous and bad for teaching.
5.  **Generate Solution SGF:** Play out the best sequence (Move A followed by KataGo's best responses) for 5-10 ply to create the `solution_sgf`.

### Step 4: Difficulty Scoring & Tagging

1.  **Calculate Difficulty Score:** Assign a numerical score based on:
      * Number of moves required to solve (depth).
      * Complexity of the shape (number of stones).
      * Presence of advanced techniques (tesuji like under-the-stones, snapbacks, liberty shortages). *Note: KataGo analysis data can hint at these.*
2.  **Map to Rank:** Map the numerical score to our `rank_difficulty` enum (e.g., Score 0-10 = '25k', Score 90-100 = '9d').
3.  **Apply Tags:** Based on the board region and techniques detected, add tags like `corner`, `side`, `ko`, `semeai`.

-----

## 4\. Quality Assurance Constraints

The script must aggressively enforce quality. It is better to discard a good puzzle than to include a bad one.

  * **No Ladders:** Discard puzzles where the solution is just reading a long, simple ladder. They are boring and hard to crop.
  * **No Ko (Initial Pass):** For the MVP, prioritize unconditional life/death. Discard puzzles where the best result is a Ko fight, unless it is explicitly tagged as a "Ko Puzzle" for higher levels.
  * **Move Limit:** Discard puzzles that require reading more than \~25 ply deep (too complex for a mobile game).

-----

## 5\. Final Output Generation

Once a batch of puzzles is generated and verified:

1.  **Sort:** Sort puzzles by their calculated difficulty score from easiest to hardest.
2.  **Assign Floors:** Assign `floor_number` based on the sorted order. (e.g., Puzzles 0-99 go to Floors 1-10).
3.  **Write JSONL:** Append the final puzzle objects to the output `puzzles.jsonl` file.