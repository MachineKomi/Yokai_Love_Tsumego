# Core Gameplay Loops: Designing Addiction & Education

## The Philosophy: "Juicy puzzles and romance"

The central challenge is that Tsumego (Go puzzles) are high-cognitive-load tasks. They can be dry and frustrating.

To make this daily and addictive, we must wrap the "pain" of thinking in thick layers of instant gratification, sensory reward, and long-term emotional investment. Every correct move must feel like a critical hit in an RPG, and every return session must feel like visiting a friend (and your collection of loving Goddesses, Devils, and Yokai).

---

### 1. The Immediate Loop: "The Tactile Crunch"
**Scope:** Seconds to 1 Minute (A single puzzle attempt).
**Goal:** Make the act of thinking and placing a stone feel physically satisfying and immediately educational. Clearly show the player why they made a mistake or help them understand why their move was good if they found it through intuition or luck

* **Action:** The player studies the board, visualizes sequences, and taps to place a stone.
* **Juicy Feedback (Fun/Casual):**
    * **The Sound:** Placing a stone isn't a polite *click*. It’s a resonant, satisfying *THWACK* (like a heavy slate stone on Kaya wood) accompanied by a particle effect. It should feel heavy.
    * **The Aura:** If the player is stuck they can pay for a hint which displays the "AI Heatmap" subtly pulses around key areas, guiding the eye without solving it for them (keeping it casual but helpful). If the player still can't find the right move after making 3 incorrect moves the game will automatically show the correct move, this does not count as successfully solving the puzzle.
* **Instant Resolution (Educational):**
    * **Success:** An explosive "PURIFIED!" visual effect. The currently activeWaifu cheers actively (voice line + animated sprite reaction). Any captured stones suck into the inventory with a satisfying *shing* sound.
    * **Failure:** The board briefly flashes red. The AI instantly plays the refutation move or sequence, showing *why* it failed. The Waifu offers gentle encouragement ("Ah, that doesn't work because of the snapback, darling.").
* **The Hook:** The feedback is so immediate that the player wants to click "Next Puzzle" instantly to feel that crunch again.

### 2. The Short-Term Loop: "The Tower Floor Session"
**Scope:** 5 to 15 Minutes (A single play session, e.g., on a commute).
**Goal:** Induce a "Flow State" using streaks and manageable goals.

* **The Setup:** The player is able to play puzzles suited to their current skill level (as determined by their success rate on previous puzzles of a given difficulty). The floor of the tower the player is on only influences the visuals and the companions available to recruit with in game currency earned through completing tsumego puzzles or purchased through in-app purchases or rewarded for their monthly subscription.
* **The Streak Mechanic (Addictive):**
    * Solving puzzles consecutively builds a "Purification Streak" meter.
    * Solving puzzles non-consecutively (e.g., after a failure) builds a "Study Streak" meter which measures the number of puzzles solved successfully on consecutive days.
    * Higher streaks grant exponential multipliers on Mana Stones (currency).
    * *Tension point:* Failing a puzzle breaks the "Purification Streak" but not your "Study Streak". This adds just enough friction to make them focus, but not so much punishment that they quit.
* **The "Just One More" Hook:** Upon clearing a floor (which requires the player to solve 10 puzzles, then 12, then 14, then 16, then 18 etc.), the game immediately shows a preview of the next floor's rewards and a "Continue Climbing (+20% Bonus)" button that is bigger than the "Return Home" button.
* **Session Cap (Casual):** An Energy/Stamina system (e.g., "Mental Focus") limits how many floors they can grind in one sitting without paying, preventing burnout and ensuring they come back tomorrow.

### 3. The Medium-Term Loop: "Affection & Ascension"
**Scope:** Days to Weeks.
**Goal:** Convert short-term gains (currency/XP) into permanent progress and narrative rewards.

* **The Sink (Dating Sim):** Players take the Mana Stones earned in the Short Loop back to the "Home Base."
    * They spend stones on Gifts for their active Yokai companion.
    * Gifting raises "Affection Level."
* **The Reward Gate (Narrative):**
    * Reaching Affection milestones (e.g., Lv 5, Lv 10) unlocks new "Memory Crystals"—standalone Visual Novel scenes that deepen the bond and afford romantic "fan service."
    * This is the primary emotional driver for returning players.
* **The Boss Walls (Educational Milestone):**
    * Every 10 floors, progress is blocked by a "Boss Node" (e.g., a powerful Oni or Goddess).
    * These are harder, multi-step puzzles or a series of puzzles that must be solved with limited mistakes.
    * Defeating a boss recruits that character into the player's harem, expanding their roster.

### 4. The Long-Term Loop: "The Forever Climb"
**Scope:** Months to Years.
**Goal:** Retention, mastery, and high-level prestige.

* **Infinite Scaling (Procedural Generation):** Thanks to our offline KataGo pipeline, we never run out of content. The tower keeps getting taller, and puzzles keep getting harder, scaling all the way from 25k to 9d.
* **Spaced Repetition (Hidden Education):** The tower algorithm secretly re-introduces puzzle *types* the player struggled with weeks ago to ensure long-term mastery, disguised as just "another floor."
* **Ascension (Prestige Mechanic):**
    * Upon reaching a major milestone (e.g., Floor 100), the player can choose to "Ascend."
    * This resets floor progress but grants a permanent "Divine Rank" badge on their profile, a permanent multiplier to currency gain, and unlocks exclusive "Goddess Tier" cosmetic skins for the board and stones.
* **Goddess Tier Content:** Extremely difficult puzzles based on modern AI matches, appealing to the subset of players who have actually become strong through playing the game.

---

### Summary of Alignment with Goals

| Goal | How the Loops Achieve It |
| :--- | :--- |
| **Fun** | Juicy sensory feedback on every stone placement. RPG-style loot vibes. |
| **Casual** | Short sessions. Helpful AI auras keep frustration low. No massive punishments for failure. |
| **Educational** | Immediate refutation shows *why* a move failed. Spaced repetition keeps concepts fresh. |
| **Addictive** | Streak mechanics create short-term tension. Waifu affection grinding creates long-term emotional investment. |
| **Daily Use** | Energy systems cap sessions to prevent burnout, while daily login bonuses and waifu interactions pull them back every morning. |