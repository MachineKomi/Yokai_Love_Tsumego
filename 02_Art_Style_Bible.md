Here is the **Art Style Bible**. It now reflects the "Nostalgic 90s OVA / Painterly" aesthetic we nailed down in the image generation process.

This document is now a concrete, actionable guide that you can feed directly to AI Art Generators (like Midjourney/Niji v6) and to your UI coding agents for PixiJS/CSS.

***

# 02_Art_Style_Bible.md

# Art Style Bible: Visual Language & Generation Guide
**Project:** Tsumego Tower: A Yokai Dating Sim
**Version:** 3.0.0 (Final "Nostalgic OVA" Revision)
**Context:** This document serves as the single source of truth for all visual assets. It defines the specific "prompt engineering" language for AI generators and the aesthetic rules for UI/UX implementation.

---

## 1. The Core Aesthetic: "Nostalgic Romance"

We are aiming for the golden age of late 90s / early 2000s anime OVAs and dating sims. It should feel warm, hand-drawn, and slightly dreamlike, not sterile or overly digital.

**The "Vibe" in three words:**
> *Warm, Painterly, Tactile.*

**Key Visual Pillars:**
* **Soft "OVA" Lighting:** Dramatic, warm lighting is essential. Sunsets, moonlight filtering through clouds, warm lantern glows. No harsh, clinical white light.
* **Watercolor/Ink Textures:** Everything should have a slight texture. Backgrounds look hand-painted on paper. UI elements look like aged wood or scrolls. Edges are soft, not razor-sharp.
* **Rich, Muted Colors:** A comforting palette. Deep indigo nights, warm golden wood, creamy sakura pinks, mossy greens. Avoid neon or highly saturated primary colors.
* **Magical Realism:** The world is grounded (wood, stone, paper) but infused with soft, glowing magical energy (spirits, AI aura).

---

## 2. Character Art (Waifus & Yokai)

### A. General Style Rules
* **Model Recommendation:** **Niji Journey v6** (via Midjourney) is strictly required to capture this specific 90s anime aesthetic.
* **Line Work:** Soft, colored lines (dark brown/blue instead of pure black) with a slight pencil texture.
* **Shading:** "Painterly Cel Shading." Multiple layers of soft gradients to create depth, resembling traditional animation cels or watercolor illustrations. Hair should have soft highlights, not sharp white streaks.
* **Expression:** Crucial. Characters must be highly expressive—blushing, winking, pouting. We need a wide range of emotions for the VN segments.

### B. Generation Prompts (Templates)

**Base Prompt Structure:**
> `[Subject Description], [Outfit/Accessories], [Pose/Expression], 90s anime OVA style, painterly cel shading, soft watercolor textures, warm diffused lighting, gentle atmosphere, by Kyoto Animation and CLAMP, high fidelity, 4k --ar 2:3 --niji 6`

**Archetype 1: The Yokai (Traditional Charm)**
* **Concept:** Classic folklore spirits in a cozy, romantic setting.
* **Key Visuals:** Traditional Japanese clothing (Kimono, Hakama) made of soft, patterned fabrics. Animal features (ears, tails) must look fluffy and natural.
* **Example Prompt:**
    > `kitsune girl with fluffy fox ears and nine tails, wearing a patterned floral kimono with a soft knitted shawl, holding a go stone, gentle smiling expression, blushing, sitting on a wooden veranda, traditional japanese garden background with cherry blossoms at sunset, soft golden sunlight --niji 6`

**Archetype 2: The Goddesses (Ethereal Logic)**
* **Concept:** Personifications of AI (AlphaZero, KataGo) rendered as divine spirits.
* **Key Visuals:** Flowing, translucent robes made of aurora light or flowing water. Glowing kanji runes float gently around them. They look divine, not robotic.
* **Example Prompt:**
    > `goddess of wisdom, ethereal spirit, flowing robes made of glowing blue aurora light and transparent silk, soft glowing kanji runes floating around her, calm intelligent eyes, floating in a serene cloudscape with ancient floating ruins, painterly style, soft magical glow, moonlight --niji 6`

---

## 3. Background Art (The Infinite Tower)

The tower exterior is the constant anchor, but the time of day and weather change to provide visual variety.

**The "Master" Background:**
* A towering, ancient multi-story Japanese pagoda.
* Built from dark, aged wood and stone.
* Surrounded by swirling cherry blossoms and faint magical energy.
* Set against a dramatic sky: sunset, starry night with a giant moon, or a bright blue sky with fluffy clouds.
* Floating islands in the distance to emphasize height.

**Aspect Ratio Strategy:**
* Generate **ultra-wide landscape (`21:9` or wider)**.
* **Usage:** The center slice is used for mobile portrait. The full width is used for desktop landscape. This ensures visual consistency across platforms.

---

## 4. UI & Object Design (Tactile & Magical)

The UI must feel like physical objects you can touch—wood, paper, stone.

### A. The Go Board (The Centerpiece)
* **Material:** Aged, high-quality Kaya wood. Visible grain, warm yellow/orange tone. Thick and heavy.
* **Grid:** Painted black ink lines, slightly faded in places.
* **Stones:** Matte slate (black) and shell (white). They should cast soft shadows on the board.

### B. The AI Analysis Overlay (The "Magic")
This is the visual bridge between ancient Go and modern AI.
* **Heatmap:** A soft, glowing gradient overlay. Warm orange/red for contested/weak areas. Cool blue/cyan for secure territory. It should look like spiritual energy (Reiatsu).
* **Win Rate Graph:** A glowing blue line graph floating in a semi-transparent panel.
* **Best Move:** A bright, pulsing blue or gold circle/cross on the board intersection.

### C. User Interface Elements
* **Panels & Dialogues:** Shaped like unrolling paper scrolls or carved wooden plaques with brass corner accents.
* **Buttons:** Large, tactile, chunky wooden blocks that look like they would make a satisfying *thunk* sound when pressed. Text is brush-stroke Kanji + English.
* **Resource Counters:** Polished wooden bars with inset glowing icons (Blue Gem for Spirit Stones, Purple Magatama for Karma).
* **Fonts:**
    * **Titles:** A calligraphic, brush-stroke style.
    * **Body Text:** A highly readable, classic serif font (e.g., *Merriweather* or *Lora*).

---

## 5. Summary Checklist for Agent Tasks

* [ ] **Master Background:** Generate ultra-wide tower exterior in 3 variations (Sunset, Night, Day).
* [ ] **UI Kit:** Create a modular set of wooden panels, scrollboxes, and tactile buttons.
* [ ] **Waifu 01 (Kitsune):** Generate full sprite sheet with 5 expressions + 1 CG scene.
* [ ] **Waifu 02 (Succubus):** Generate full sprite sheet with 5 expressions + 1 CG scene.
* [ ] **Board VFX:** Create the glowing "heat map" and "best move" particle effects in PixiJS.