# Content File Structure (Tsumego + Joseki)

This miner produces (or will produce) Go content that the main game can ingest as simple files on disk. The recommended structure below keeps loading fast and makes it easy to ship content updates.

## Root layout (recommended)

In the main game repo, place mined content under a single root:

```text
content/go/
  tsumego/
    puzzles/
      puzzle_<uuid>.json
      puzzle_<uuid>.sgf
    index.json
  joseki/
    trees/
      joseki_<id>.json
      joseki_<id>.sgf
    index.json
```

Notes:
- The miner already emits `puzzle_<uuid>.json` and `puzzle_<uuid>.sgf` into its local `output/` folder; this mirrors that naming for easy syncing.
- The `index.json` files are lightweight manifests the game can load first, then lazy-load individual JSON/SGF files as needed.

---

## Tsumego puzzles

### Required files
- `content/go/tsumego/puzzles/puzzle_<uuid>.json` (primary)
- `content/go/tsumego/puzzles/puzzle_<uuid>.sgf` (optional but recommended for replay/debug)

### Coordinate conventions
- JSON `setup_moves` uses KataGo-style coordinates like `"C3"` on a 9x9 board, plus `"pass"`.
- SGF uses standard `aa`-style coordinates.

### Ownership map
If present, `metadata.ownership` is a flat array of length `board_size * board_size`, row-major from KataGo. Values typically range `[-1, 1]` (negative = White ownership, positive = Black).

---

## Joseki trees

The main game consumes joseki as a **branching tree** of positions/moves rather than a single “correct move”.

### Required files
- `content/go/joseki/trees/joseki_<id>.json` (primary)
- `content/go/joseki/trees/joseki_<id>.sgf` (optional reference line; may include variations)

### Tree model (high level)
- A joseki file defines:
  - Board size (usually 19, but can support 9 for testing)
  - Root node (`root_node_id`)
  - A set of nodes keyed by `node_id`
  - Each node contains: move (or `null` for root), comment, tags, and child edges

The schema in `game_dropin/schemas/joseki_tree_v1.schema.json` is the authoritative definition for integration work.

