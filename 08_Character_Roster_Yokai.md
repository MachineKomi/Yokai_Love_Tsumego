Here is the **Yokai Character Roster**. This document is the "Actor's Bible" for the AI agents. It provides the visual cues for art generation, the voice/tone guidelines for dialogue generation, and the data needed for the gifting economy.

It focuses on the initial "Starting Trio" to keep the scope manageable for the vertical slice, ensuring a diverse range of personalities and visuals that fit the "Nostalgic 90s OVA" aesthetic.

***

# 08_Character_Roster_Yokai.md

# Character Roster: The Yokai Spirits
**Project:** Yokai Love Tsumego: A Yokai Dating Sim
**Version:** 1.0.0
**Context:** This document defines the personalities, visual designs, and dialogue voices for the recruitable Yokai companions. It is used by AI Art Agents for sprite generation and AI Writing Agents for dialogue and visual novel scene creation.

---

## 1. Roster Overview & Design Philosophy

The Yokai represent **Emotion and Chaos**. They are powerful spirits trapped in the tower, their natural instincts clouded by the Miasma, making them erratic and unfocused.

**The Player's Role:** You are their anchor. Through the logic of Go, you help them focus their chaotic energy.
**The "90s OVA" Aesthetic Rule:** All designs must feel hand-drawn and painterly. Avoid modern, glossy, "vtuber-style" designs. Think *Inuyasha*, *Ranma ½*, or *Tenchi Muyo* aesthetics.

### Progression & Duplicates
*   **Essence:** Pulling a duplicate of a Yokai grants "Essence" specific to that character.
*   **Rank Up:** Essence is used to increase the Yokai's **Rank** (Stars), boosting their Base Stats and unlocking new Limit Break tiers.
*   **Level Up:** Mana/Aji is used to increase **Level**, boosting stats linearly.

---

## Character 00: Tama (The Neko Guide)

**Archetype:** The Sarcastic Pet / Tutorial Guide / Mascot.
**Role:** Starter Companion.
**Go Playstyle Metaphor:** "Saboteur." Points out obvious mistakes with snark.

### A. Lore & Backstory
A two-tailed Neko (cat) yokai who claims to be the former guardian of the tower, now reduced to a small, floating cat form due to the Demon Lord's curse. She guides the player not out of kindness, but because she needs a "meat shield" to climb back up. She is cheeky, sarcastic, and constantly demands snacks.

### B. Visual Design Spec (For Niji v6)
*   **Vibe:** Cute but grumpy, expressive, mascot-like.
*   **Key Features:** White fur with black spots, two tails, wearing a large red prayer bead necklace. Floating.
*   **Expression:** Smug, yawning, or judging the player's moves.

### C. Dialogue Voice (Examples)
*   **Greeting:** "Oi, human. Feed me before you play. I can't think on an empty stomach."
*   **Puzzle Success:** "Not bad. For a hairless ape."
*   **Puzzle Failure:** "Wow. Even a dog would have seen that ladder. Embarrassing."

---

## Character 01: Ren (The Kitsune Guide)

**Archetype:** The Playful Trickster / Genki Girl / "Ara Ara" Oneesan.
**Go Playstyle Metaphor:** "Trick Plays." Loves setting traps, snapbacks, and confusing ko fights. Gets bored with slow, solid play.

### A. Lore & Backstory
A nine-tailed fox spirit who has inhabited the lower levels of the tower for centuries. She finds the Miasma boring rather than scary. She attaches herself to the Player out of sheer curiosity, amused by a human trying to climb the Spire using mere stones. She acts as the initial tutorial guide, hiding her genuine loneliness behind a mask of playful teasing.

### B. Visual Design Spec (For Niji v6)
* **Vibe:** Warm, fluffy, inviting, mischievous.
* **Key Features:** Large, expressive fluffy fox ears; nine large golden-orange tails that fan out behind her like a soft cloud.
* **Clothing:** A loosely worn traditional floral kimono (autumn colors: orange, cream, gold) slipped slightly off one shoulder. A soft, knitted shawl.
* **Expression:** Usually a sly, knowing smile with half-closed eyes, or a surprised blush when the player does something smart.

**Art Generation Prompt Template:**
> `character portrait of Ren the kitsune yokai girl, fluffy fox ears and nine large tails, wearing an loosened autumn floral kimono and a knitted shawl, mischievous smiling expression, blushing, holding a go stone near her lips, 90s anime OVA style, painterly cel shading, soft watercolor textures, warm diffused lighting, gentle atmosphere, traditional japanese room background --ar 2:3 --niji 6`

### C. Dialogue Voice (Examples)

* **Greeting (Lobby):** "Ara ara~ Back already, Master? Did you miss my fluffy tails, or did you come for another lesson in losing?"
* **Puzzle Success (Strong Move):** "Oh? A Tesuji? My, my... perhaps you aren't just a cute face after all. My heart skipped a beat~"
* **Puzzle Failure (Blunder):** "Ufufu! You walked right into that self-atari! You need to watch more than just me, darling."
* **Romance Scene (High Affection):** "Humans live such short lives. Why do you spend yours staring at a wooden board with me? ...Not that I'm complaining. Stay a little longer."

### D. Affection Data (Economy)
* **Likes (High XP Gifts):** Fried Tofu (Aburaage), Hair Ornaments, Shiny things.
* **Dislikes (Low XP Gifts):** Dogs, Bitter Medicine, overly serious books.

---

## Character 02: Kagara (The Oni Vanguard)

**Archetype:** The Hot-headed Tsundere / Tomboy.
**Go Playstyle Metaphor:** "Overplay Aggression." Constantly invades, tries to kill every group, ignores her own weaknesses. Hates defending.

### A. Lore & Backstory
A red Oni (ogre) warrior spirit who believes strength is everything. The Miasma has amplified her aggression into blind rage, causing her to constantly pick fights she can't win against higher-floor guardians. She initially sees Go as a stupid, cowardly game for weaklings, but respects the Player after they defeat her "boss form" using pure logic.

### B. Visual Design Spec (For Niji v6)
* **Vibe:** Intense, muscular but feminine, intimidating but cute when flustered.
* **Key Features:** Two small red horns on her forehead. Wild, spiky red hair tied back roughly. Slightly pointed teeth.
* **Clothing:** A modified, more revealing version of a traditional warrior's outfit. Tiger-skin patterned sash, armored gauntlets over bandaged arms, a tattered hakama skirt.
* **Expression:** Usually scowling, yelling, or intensely focused. Extremely embarrassed and blushy when complimented.

**Art Generation Prompt Template:**
> `character portrait of Kagara the oni yokai girl, small red horns, wild spiky red hair, wearing armored gauntlets and a tiger-skin sash over a traditional warrior outfit, scowling angry expression, flexing arm, 90s anime OVA style, painterly cel shading, soft watercolor textures, dramatic lighting, intense atmosphere, dojo background --ar 2:3 --niji 6`

### C. Dialogue Voice (Examples)

* **Greeting (Lobby):** "What are you staring at, weakling? If you have time to loiter, you have time to train! Place a stone already!"
* **Puzzle Success (Strong Move):** "Hmph! That was... adequate. You didn't die immediately. Don't get cocky, it was just luck!" (Blushing look away).
* **Puzzle Failure (Blunder):** "GAH! Why did you play there?! You left your entire corner open! Are you trying to make me mad!?"
* **Romance Scene (High Affection):** "Ugh, stop looking at me like that. It's distracting. ...But if anyone else looks at you like that, I'll crush them. You're *my* rival, got it?"

### D. Affection Data (Economy)
* **Likes (High XP Gifts):** Sake, Meat buns, Weapons/Armor polish.
* **Dislikes (Low XP Gifts):** Flowers, Perfume, Delicate things.

---

## Character 03: Miyuki (The Yuki-onna Recluse)

**Archetype:** The Kuudere / Dandere / Shy Wallflower.
**Go Playstyle Metaphor:** "Over-defensive." Terrified of being cut or invaded. Makes slow, heavy shapes to ensure 100% safety, missing opportunities to attack.

### A. Lore & Backstory
A Snow Woman spirit who froze herself in a block of ice on a mid-tier floor to escape the Miasma. She is deeply afraid of connection because her touch freezes everything. She plays Go defensively because she fears making a mistake and hurting others (or getting hurt). The player's "warm" logic begins to melt her icy exterior.

### B. Visual Design Spec (For Niji v6)
* **Vibe:** Ethereal, fragile, cold, melancholic.
* **Key Features:** Very pale skin, long flowing white/pale blue hair. Her breath should always have a small visible vapor cloud.
* **Clothing:** A beautiful white kimono that looks like it's woven from frost and ice crystals. Subtle snowflake patterns.
* **Expression:** Sad, hesitant, eyes averting gaze. A very small, faint smile when happy.

**Art Generation Prompt Template:**
> `character portrait of Miyuki the yuki-onna yokai girl, pale skin, long flowing white hair, wearing a frosted white kimono with snowflake patterns, sad hesitant expression, looking away, visible cold breath vapor, 90s anime OVA style, painterly cel shading, soft watercolor textures, cold blue lighting, ethereal atmosphere, snowy landscape background --ar 2:3 --niji 6`

### C. Dialogue Voice (Examples)

* **Greeting (Lobby):** "...Hello. Please don't stand too close. I don't want you to catch a cold."
* **Puzzle Success (Strong Move):** "...That shape is very solid. It makes me feel... safe. Thank you."
* **Puzzle Failure (Blunder):** "Ah... your stones are cut off. They are all alone now. Like me."
* **Romance Scene (High Affection):** "Your logic... it is warm. When we play together, the ice around my heart feels a little thinner. Is it okay... if I stay close to you?"

### D. Affection Data (Economy)
* **Likes (High XP Gifts):** Shaved ice (Kakigori), Beautiful combs, warm tea sets (ironic like).
* **Dislikes (Low XP Gifts):** Spicy food, loud noises, fire-related items.