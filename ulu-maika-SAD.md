# Software Architecture Document: ʻUlu Maika — Makahiki Games

**Created by:** kahalewai
**Version:** 1.1
**Purpose:** This document contains every detail needed to regenerate the complete ʻUlu Maika game from scratch in a single session. Feed this entire document to Claude and ask it to build the game.

---

## 1. Project Overview

ʻUlu Maika is a pixel-art browser game based on the traditional Hawaiian stone-rolling game played during the Makahiki harvest festival. It is the first mini-game in a planned collection called **Makahiki Games**. The game is a single self-contained HTML file with no external dependencies — all graphics are drawn programmatically on an HTML5 Canvas using pixel-art techniques. It works on both mobile (touch) and desktop (mouse).

### Cultural Context

ʻUlu Maika was a traditional Hawaiian stone-rolling game played during the Makahiki harvest festival to test strength and accuracy. The stone itself was a biconvex disc — thick at the center, narrow at the edges, shaped like a wheel. Originally the implement was ʻulu (breadfruit); polished stone came later. King Kamehameha I played it. Crowds of two to three thousand gathered to watch and wager everything — property, tools, and goods/foods — on a single throw. Professional runners were identified through these contests. This cultural depth is woven into the game through "Did You Know?" fact screens, Hawaiian-language scoring, and crowd chants.

---

## 2. Technical Stack

- **Single HTML file** — all code, styles, and game logic in one file
- **HTML5 Canvas API** — all rendering via 2D canvas context
- **Vanilla JavaScript** — no frameworks, no libraries, no external assets
- **Pixel art style** — all graphics drawn programmatically with `fillRect` pixel plotting
- **Monospace font** — all text uses the system monospace font
- **CSS** — minimal: reset, dark background (#1a1a2e), touch-action:none, user-select:none, image-rendering:pixelated

### Canvas Configuration

- Internal resolution: **320×480** (portrait mobile aspect ratio)
- Responsive scaling: canvas scales to fit window while maintaining aspect ratio
- Scale factor computed as `Math.min(windowWidth/320, windowHeight/480)`
- Canvas CSS dimensions set to scaled size, vertically centered with marginTop

---

## 3. Game States

The game uses a state machine with these states:

| State | Enum Value | Description |
|-------|-----------|-------------|
| TITLE | 0 | Title screen with game name, description, "TAP TO PLAY" |
| MODE | 1 | Mode selection — Precision or Distance |
| AIM | 2 | Player can swipe to aim and throw |
| ROLLING | 3 | Stone is in motion, physics active |
| STOPPING | 4 | Stone has stopped, waiting before next roll (prevents duplicate timeouts) |
| GAMEOVER | 5 | Final score display with rating |
| FACT | 6 | "Did You Know?" cultural fact screen (shown between STOPPING and GAMEOVER) |
| HIGHSCORE | 7 | High score overlay (accessible via button during gameplay) |

### State Flow

```
TITLE → MODE → AIM → ROLLING → STOPPING → AIM (repeat for 5 rolls)
                                    ↓ (after 5th roll)
                                   FACT → GAMEOVER → MODE
```

The HIGHSCORE state can be entered from AIM or STOPPING via the HI-SCORE button, and returns to whichever state it came from.

---

## 4. Game Modes

### 4.1 Precision Mode

Roll the biconvex stone between two wooden stakes (pahu hoku) without hitting them. Based on the historical method: "30-40 yards away, roll the maika (stone) through the pahu hoku (stakes)."

**Layout:**
- Two brown wooden stakes at y=130, separated by 50px gap, centered on screen
- Stone starts at center-x, y=400
- Stakes are 4px wide, 44px tall, with shadow and highlight details

**Scoring (per roll):**

| Result | Hawaiian | English Sub | Points | Color |
|--------|----------|------------|--------|-------|
| Perfect center (dist < 5px) | Poʻokela! | Perfect! | +100 | #FFD93D (gold) |
| Good pass (dist < half gap) | Maikaʻi loa! | Very Good! | +50 | #81C784 (green) |
| Barely through | Holo kokoke! | Barely made it! | +25 | #64B5F6 (blue) |
| Hit stake but went through | Pōmaikaʻi! | Lucky Shot! | +25 | #FFA726 (orange) |
| Hit stake and bounced out | Auē! | Oh no! Hit the stake! | 0 | #FF6B6B (red) |
| Overshot (past stakes) | He keu! OR Hana Hou! | Too far! OR Try again! | 0 | #FF6B6B (red) |
| Too short (before stakes) | Hoʻohakalia! OR Hana Hou! | Too short! OR Try again! | 0 | #FF6B6B (red) |

Miss messages (He keu/Hoʻohakalia/Hana Hou) are randomly selected (50/50) each time.

**Stake Collision Logic:**
- When stone hits a stake, `stone.hitStake` flag is set to true
- Stone bounces off (velocity reflected, halved)
- Scoring is NOT determined at collision — the stone keeps rolling
- Final scoring happens when stone passes through the stake zone (checks if stone is between stakes)
- If `hitStake` is true and stone made it through → "Pōmaikaʻi!" (+25)
- If `hitStake` is true and stone did NOT make it through → "Auē!" (0 points)

**Perspective System:**
When the stone rolls past the stakes (y < STAKE_Y), it does NOT fly off the screen. Instead, a perspective mapping makes it visually recede toward the horizon:
- Horizon line is at y=95 (where grass meets mountains)
- Visual y position uses exponential asymptote: `visualOffset = maxRange * (1 - exp(-pastDist/300))`
- Stone shrinks: `scaleFactor = max(0.15, 1 - visualOffset/maxRange * 0.85)`
- Stone x converges toward center: `visX = centerX + (stoneX - centerX) * scaleFactor`
- Dust trail particles also spawn at the perspective-mapped position and scale with the stone
- A shared `getStoneVisualPos()` function ensures stone and particles always match

### 4.2 Distance Mode

Roll the maika (stone) as far as possible down the kahua maika course. Based on the historical method: "Roll the stone as far as possible. Where it stops, mark it with a flag or stake."

**Layout:**
- Course is 100px wide, centered, with brown edge lines (guardrails)
- Foot markers every 60px along the course
- Camera follows the stone with smooth lerp: `distCamera += (target - distCamera) * 0.08`
- Stone starts at y=400 (distStartY)

**Unit Conversion:**
- Pixels to feet: `PX_TO_FEET = 0.8` (1 pixel of travel = 0.8 feet)
- A full-power throw reaches roughly 500-700 feet

**CRITICAL: The distance track is drawn inside `ctx.translate(0, distCamera)`.** All track positions use world coordinates directly — do NOT add distCamera a second time.

**Clipping:**
- Guardrails, foot markers, and roll markers all clip at:
  - Top: grass line (`92 - distCamera` in world coords)
  - Bottom: crowd area (`GH - 50 - distCamera` in world coords)
- Nothing renders over the sky, ocean, mountains, or crowd

**Scoring (per roll):**

| Result | Hawaiian | Points | Color |
|--------|----------|--------|-------|
| 500+ feet | Pāʻani Maikaʻi! OR Poʻokela! (random 50/50) | dist/5 | #FFD93D (gold) |
| 250-500 feet | Maikaʻi loa! | dist/5 | #81C784 (green) |
| 80-250 feet | Hoʻohakalia! OR Hana Hou! (random 50/50) | dist/5 | #FF6B6B (red) |
| Under 80 feet | Hoʻohakalia! OR Hana Hou! (random 50/50) | 0 | #FF6B6B (red) |

Previous rolls are marked on the course with red lines and numbered labels.

---

## 5. Physics

### Swipe Input (Flick Style)

The game uses **flick/swipe** controls — swipe in the direction you want the stone to go. Swipe up to roll the stone up the field.

- Direction: `dx = endPos.x - startPos.x`, `dy = endPos.y - startPos.y` (flick direction, NOT slingshot)
- Minimum swipe distance: 10px (below this, no throw)
- Maximum swipe distance: 80px (clamped)
- Power: `power = min(swipeDist / 80, 1)`
- **Quadratic power curve:** `speed = baseSpeed + power² × maxExtra`
  - Precision: baseSpeed=2.5, maxExtra=5.5
  - Distance: baseSpeed=3, maxExtra=9
- Velocity: `vx = nx × speed`, `vy = ny × speed` (where nx,ny are normalized direction)

### Friction

**Constant deceleration** (not multiplicative). This ensures harder throws go proportionally farther:
```
friction = 0.08
if speed > friction:
    vx -= (vx/speed) × friction
    vy -= (vy/speed) × friction
else:
    vx = 0, vy = 0
```

### Stone Properties

- Radius: 10px
- Rotation: `rot += vx × 0.15` each frame (visible via rotation indicator dots)
- Flags: `rolling`, `scored`, `hitStake`
- Stop threshold: speed < 0.3 (precision), speed < 0.2 (distance)

### Boundary Collisions

- Precision: walls at x=0 and x=320 (bounce with 0.5 damping)
- Distance: course walls at center±48 (bounce with 0.3 damping)

---

## 6. Rolls and Scoring

- Each game has **5 rolls** (MAX_ROLLS = 5)
- Rolls counter **increments immediately at launch** (not after the roll finishes)
- UI displays remaining: `(MAX_ROLLS - rolls) / MAX_ROLLS`
- After all 5 rolls → save high score → show random cultural fact → show game over
- The transition from ROLLING to STOPPING happens **exactly once** (prevents duplicate setTimeout calls that caused flickering bugs)

### High Scores

- Stored in memory as `{precision: [], distance: []}` — two separate leaderboards
- Top 5 scores kept per mode, sorted descending
- Saved when all 5 rolls are complete
- Session-only (reset when page closes)

### Game Over Ratings

**Precision mode:**

| Score | Rating |
|-------|--------|
| 400+ | ALIʻI (Chief)! |
| 250+ | KOA (Warrior)! |
| 100+ | MĀHIAI (Farmer) |
| Under 100 | KEIKI (Beginner) |

**Distance mode:**

| Best Distance | Rating |
|--------------|--------|
| 600+ feet | ALIʻI (Chief)! |
| 350+ feet | KOA (Warrior)! |
| 150+ feet | MĀHIAI (Farmer) |
| Under 150 | KEIKI (Beginner) |

---

## 7. Visual Design

### Hawaiian Landscape Background

Drawn every frame behind all gameplay elements. Layer order (back to front):

1. **Sky** — 5 horizontal bands of blue gradient (#5BA3D9 → #9FD4F2), each 18px tall
2. **Ocean** — full-width band starting at y=52, 4 layers of blue (#1565C0 → #42A5F5), with animated wave highlights (12 small white lines using sine wave for gentle horizontal sway)
3. **Mountains** — two ranges (left and right sides), each with 3 layers:
   - Dark back ridge (#2E7D32)
   - Lighter front ridge (#388E3C)
   - Highlight patch (#43A047)
   - NO dark valley crease lines (these were removed)
4. **Clouds** — 3 pixel clouds drifting right at different speeds (0.10-0.15 px/frame), wrapping around. Each cloud is 3 stacked rectangles with varying opacity
5. **Grass** — starts at y=92, alternating 14px stripe pattern (#45A049/#4CAF50/#51B655), plus 30 scattered 2×4px tufts (#66BB6A)
6. **Coconut trees** — 4 trees (2 left, 2 right) with:
   - Curved trunk (sine-based lean), tapers from 4px to 2px
   - 6 fronds with gentle sine sway animation
   - 3 coconuts at the crown
   - Positions: (12,135,h50,lean8), (48,148,h38,lean-5), (272,138,h48,lean-7), (302,150,h36,lean6)
7. **Flowers** — 25 plumeria-style flowers scattered on grass, 4 colors (#FF6B6B, #FFD93D, #FF8E53, #F48FB1), each with cream center (#FFF9C4) and green stem

### Biconvex Stone

Drawn with concentric pixel circles to show the thick-center, narrow-edge profile:
- Shadow: offset (+2,+2), black 25% opacity
- Outer edge: #555
- Body layers: #6E6E6E → #808080 → #8A8A8A → #969696
- Specular highlight: small #A8A8A8 circle offset upper-left
- Rotation indicator: two 2×2 dark dots (#5A5A5A) at opposite ends, rotating with `stone.rot`

### Crowd

Three groups of pixel-art people:
- **Bottom** — 20 people across the bottom, spaced ~16px apart with random jitter
- **Left side** — 6 people at x=2-22, spaced vertically every 35px starting at y=180
- **Right side** — 6 people at x=295-315, spaced vertically every 35px starting at y=175

Each person has:
- Body (8px wide rectangle, random height 8-14px, random brown clothing color)
- Head (4px radius circle, random skin tone)
- Hair (6×3px dark rectangle)
- Arms (wave when excited — sine-based animation with per-person offset)
- Gentle bobbing animation (sine wave, per-person speed and phase)

When excited (stone is rolling), bob amplitude increases from 0.8 to 2.5 and arms wave.

### Crowd Chants

Floating text that appears from the crowd while the stone rolls:
- Texts: "Ā ʻo ia!" (Go for it!), "ʻOnipa'a!" (Stay determined!)
- Color: #FFD93D (gold)
- Spawn from random positions among all three crowd groups
- Float upward (vy: -0.8) and fade out (life decreases by 0.015/frame)
- Cooldown: 15-30 frames between chants
- Only trigger when stone speed > 2

### Celebration Particles

30 colorful particles burst outward on perfect/excellent rolls:
- 5 colors: gold, red, green, blue, orange
- Random angle and speed (1-3), slight gravity (vy += 0.05)
- Fade over ~50 frames

### Dust Trail Particles

2 particles spawn per frame while stone rolls:
- Colors: #66BB6A (green grass) and #8D6E63 (brown dirt), random 50/50
- Spawn at stone position (perspective-mapped in precision mode)
- Size scales with perspective
- Drift upward slightly, fade over ~23 frames

---

## 8. UI Layout

### Top Bar (during gameplay)
- Full-width dark overlay bar, 28px tall, at top
- Left: "SCORE: {n}" in gold (#FFD93D), bold 12px
- Right: "ROLLS: {remaining}/{max}" in white, 12px
- Center: mode name ("PRECISION" or "DISTANCE") in orange (#FFA726), 9px

### Bottom Bar (during gameplay)
- Full-width dark overlay bar (#000 70% opacity), 20px tall, at very bottom
- Below the crowd (crowd sits above this bar)
- Left: "EXIT" button in light gray (#E0E0E0), 9px — returns to mode select
- Right: "HI-SCORE" button in gold (#FFD93D), 9px — opens high score overlay
- Center: "ʻUlu Maika" in white 30% opacity, 7px

### Aim Arrow (while dragging)
- Dashed yellow (#FFD93D) line from stone in swipe direction
- Triangle arrowhead at end
- Power bar below stone: 60×8px, dark background, filled portion green→yellow→red based on power
- Text label below power bar: "gentle" / "firm" / "FULL POWER"

---

## 9. Screen Designs

### Title Screen
- Hawaiian background visible behind dark overlay (50% opacity)
- Top decorative border: brown bar (#A0522D) 3px tall at y=65 with triangle teeth (#8B4513) pointing down below it (4px gap between bar and triangles)
- Title: "ʻULU MAIKA" in gold, bold 26px monospace
- Subtitle: "~ Makahiki Games ~" in orange, 12px
- Sub-subtitle: "Peace, Community, Skills" in light green (#C8E6C9), 10px
- Animated stone rolling across screen at y=180 continuously (loops every ~400px)
- Description box (dark semi-transparent, 130px tall): "A traditional Hawaiian stone-rolling game played during the Makahiki harvest festival to test strength and accuracy." (5 lines)
- 10px gap, then "Even Kamehameha I played." / "Swipe to roll. Aim true!" in green
- Pulsing "TAP TO PLAY" in gold, bold 16px
- Bottom decorative border: brown bar with triangle teeth pointing up above it
- Credit line: "created by kahalewai" in white 40% opacity, 9px, at y=465
- Tap anywhere → MODE screen

### Mode Select Screen
- Hawaiian background with dark overlay (55% opacity)
- "CHOOSE YOUR GAME" in gold, bold 18px
- Two selection boxes:
  - **PRECISION** — green border (#4CAF50), title in gold, description: "Roll between two stakes (pahu hoku) without touching them.", historical quote: "30-40 yards away, roll the maika (stone) through the pahu hoku (stakes)"
  - **DISTANCE** — blue border (#2196F3), title in gold, description: "Roll the maika (stone) as far as possible down the kahua maika course.", historical quote: "Where it stops, mark it.", mini preview (track lines + stone)
- Each box: tap target area from y=110-240 (precision) and y=260-390 (distance)
- "TAP A MODE TO BEGIN" at bottom in white

### "Did You Know?" Fact Screen
- Hawaiian background with dark overlay (75% opacity)
- Top border with triangle teeth pointing down
- "DID YOU KNOW?" label in orange, 10px
- Fact title in gold, bold 14px
- Fact text in light gray, 10px, multi-line (pre-split into array of strings)
- Bottom border with triangle teeth pointing up
- Pulsing "TAP TO CONTINUE" in green
- **CRITICAL:** The fact index is selected ONCE when transitioning to this state (stored in `selectedFactIndex`). It must NOT be randomly chosen each frame — this causes flickering.

### Cultural Facts Content

5 facts, randomly selected (one per game completion):

1. **The Kahua Maika** — "ʻUlu Maika courses (Kahua) stretched up to a mile long, smooth, hard-packed trenches, sometimes with slight turns, engineered to challenge each player."
2. **Kamehameha I** — "The great king loved to throw the Maika stone, along with hurling spears and riding waves. The games built strength worthy of a ruler."
3. **The Crowds** — "Two to three thousand people gathered to watch. They wagered everything, property, tools, and goods/foods, on a single throw."
4. **The Stone** — "Originally rolled from ʻulu (breadfruit). The polished biconvex disc came later, stone, coral, wood, even olivine and lava."
5. **Professional Athletes** — "From these disc-rolling contests, the fastest runners were identified. Players chased their rolling stones at full speed."

### Game Over Screen
- Dark overlay (65% opacity)
- "PAU!" in gold, bold 22px (Hawaiian for "finished")
- "(Finished!)" in orange, 12px
- Mode name in white, 14px
- Final score in gold, bold 32px
- Best roll distance (distance mode only) in light blue, 12px
- Rating title (ALIʻI/KOA/MĀHIAI/KEIKI) with color coding
- Pulsing "Pāʻani Hou! (Play Again)" in white, bold 13px

### High Score Overlay
- Dark overlay (75% opacity) over gameplay
- Top border bar with triangles pointing down
- "HIGH SCORES" in gold, bold 18px
- "PRECISION" section header in green, top 5 scores listed (or "No scores yet")
- "DISTANCE" section header in blue, top 5 scores listed (or "No scores yet")
- #1 score in gold bold, others in light gray
- Bottom border bar with triangles pointing up
- Pulsing "TAP TO CLOSE" — returns to previous gameplay state

---

## 10. Decorative Borders — Hawaiian Triangle Pattern (Niho)

Based on the traditional niho (tooth) pattern found in Hawaiian tattoo and kapa cloth.

**Implementation:**
- A horizontal brown bar (#A0522D), 3-4px tall
- One row of triangles along the bar, with a 4px gap between the bar and the triangles:
  - **Top borders**: triangles hang below the bar, points facing DOWN
  - **Bottom borders**: triangles sit above the bar, points facing UP
  - Both orientations: flat edge faces the bar, point faces away from the content
- Triangle specs: base 10px wide, height 6px, spaced 12px apart, color #8B4513
- Loop stops at `x < endX - 10` to prevent overflow past the bar

---

## 11. Input Handling

### Touch and Mouse
- Both use identical event handlers
- `touchstart`/`mousedown` → `onStart` (records swipe start position)
- `touchmove`/`mousemove` → `onMove` (updates swipe end position)
- `touchend`/`mouseup` → `onEnd` (calculates direction and launches stone)
- All touch events use `{ passive: false }` and `e.preventDefault()`
- Position conversion accounts for canvas scale factor

### State-Specific Input

| State | Input Action |
|-------|-------------|
| TITLE | Tap anywhere → MODE |
| MODE | Tap precision box (y:110-240) → start precision. Tap distance box (y:260-390) → start distance |
| AIM | Swipe → aim arrow appears, release → launch stone. Tap EXIT → MODE. Tap HI-SCORE → overlay |
| ROLLING | No input accepted |
| STOPPING | Tap EXIT → MODE. Tap HI-SCORE → overlay |
| FACT | Tap anywhere → GAMEOVER |
| GAMEOVER | Tap anywhere → MODE |
| HIGHSCORE | Tap anywhere → return to previous state |

---

## 12. Key Technical Decisions and Bug Fixes

These are lessons learned during development. If regenerating the code, these MUST be followed:

1. **STOPPING state is essential.** Without it, the `speed < threshold` check fires every frame while state is ROLLING, spawning multiple `setTimeout` calls. Each one picks a new random fact index → fact screen flickers wildly. Transition to STOPPING immediately on first detection.

2. **Rolls increment at launch time**, not in `finishRoll()`. Otherwise the counter appears stale during the throw.

3. **Distance track uses `ctx.translate(0, distCamera)`** — do NOT also add `distCamera` to positions inside `drawDistanceTrack()`. This double-offset was a bug that made foot markers show 2× the actual distance.

4. **Constant friction, not multiplicative.** `speed × 0.99` each frame means gentle and hard throws decelerate at similar rates. Constant deceleration (`speed -= friction`) ensures harder throws go proportionally farther.

5. **Perspective dust trail** — particles must spawn at the visual (perspective-mapped) position, not the raw physics position. Use the shared `getStoneVisualPos()` function for both stone rendering and particle spawning.

6. **Track elements must clip** at grass line (top) and crowd area (bottom). Without clipping, guardrails and markers draw over the sky, ocean, mountains, and crowd.

7. **Fact index selected once** when transitioning to FACT state, stored in `selectedFactIndex`. Never randomize during the draw loop.

8. **Flick controls, not slingshot.** Direction = `endPos - startPos` (not `startPos - endPos`).

---

## 13. Future Features (Planned)

These are NOT implemented yet but are planned for future sessions:

- **Language toggle** — switch all in-game text between English and Hawaiian (ʻŌlelo Hawaiʻi)
- **Sound effects** — Web Audio API for stone rolling, crowd chanting, score celebrations
- **Additional mini-games** — the full Makahiki collection (6-10 games):
  - Collision mode (stones rolled against each other)
  - Racing mode (simultaneous rolls from opposite ends)
  - Other traditional Makahiki games (to be researched)
- **Tile-based launcher** — main menu with tiles for each mini-game
- **Persistent high scores** — localStorage to save between sessions
- **Leaderboard** — shared scores between players
- **Mobile app wrapping** — Capacitor or PWA for App Store / Google Play distribution

---

## 14. Hawaiian Language Reference

### Scoring Words Used in Game

| Hawaiian | Meaning | Used When |
|----------|---------|-----------|
| Poʻokela! | Perfect! | Perfect precision roll, great distance roll |
| Pāʻani Maikaʻi! | Great Play! | Great distance roll (alternates with Poʻokela) |
| Maikaʻi loa! | Very Good! | Good precision pass, good distance roll (250-500 ft) |
| Pōmaikaʻi! | Lucky! | Hit stake but still went through |
| Holo kokoke! | Barely made it! | Just squeezed through the stakes |
| Auē! | Oh no! | Hit stake and bounced out |
| He keu! | Too much! | Rolled too far past the stakes |
| Hoʻohakalia! | Lagging behind | Too short / weak roll |
| Hana Hou! | Do it again! Encore! | Miss — encouragement to retry |
| Pau! | Finished, done | Game over screen |
| Pāʻani Hou! | Play again! | Game over "play again" prompt |

### Crowd Chants (during rolling)

| Hawaiian | Meaning |
|----------|---------|
| Ā ʻo ia! | Go for it! |
| ʻOnipa'a! | Stay determined! |

### Game Terms

| Hawaiian | Meaning |
|----------|---------|
| ʻUlu Maika | Breadfruit disc rolling (the game itself) |
| Kahua Maika | The course/playing field |
| Pahu hoku | The stakes/goal posts |
| Maika | The stone disc |
| Makahiki | Harvest festival season |

### Rating Titles

| Hawaiian | Meaning |
|----------|---------|
| ALIʻI | Chief (highest rank) |
| KOA | Warrior |
| MĀHIAI | Farmer |
| KEIKI | Beginner/child |

---

## 15. Color Palette Reference

### Landscape
- Sky: #5BA3D9 → #9FD4F2 (5 bands)
- Ocean: #1565C0, #1E88E5, #2196F3, #42A5F5 (deep to light)
- Wave highlights: #90CAF9, #BBDEFB
- Mountains dark: #2E7D32, lighter: #388E3C, highlight: #43A047
- Grass: #4CAF50, #45A049, #51B655, tufts: #66BB6A
- Tree trunk: #795548, highlight: #8D6E63
- Coconuts: #6D4C41, #5D4037, #795548

### UI
- Gold (scores, titles): #FFD93D
- Orange (subtitles): #FFA726
- Green (success): #81C784, #C8E6C9
- Blue (okay): #64B5F6
- Red (failure): #FF6B6B
- White text: #FFF, #E0E0E0
- Dark overlays: rgba(0,0,0, 0.5-0.75)

### Decorative Borders
- Bar: #A0522D
- Triangles: #8B4513

### Stakes
- Dark wood: #8B4513
- Light wood: #A0522D

### Stone
- Layers: #555 → #6E6E6E → #808080 → #8A8A8A → #969696 → #A8A8A8

### Flowers
- Petals: #FF6B6B, #FFD93D, #FF8E53, #F48FB1
- Center: #FFF9C4
- Stem: #2E7D32

### Crowd
- Clothing: #8B4513, #A0522D, #6D4C41, #795548, #D2691E
- Skin tones: #C68642, #8D5524, #E0AC69, #F1C27D
- Hair: #1a1a1a

---

## 16. Generation Instructions

To regenerate this game, paste this entire document into Claude and say:

> "Build the complete ʻUlu Maika game as described in this SAD. Generate a single self-contained HTML file with all game logic, pixel art rendering, physics, Hawaiian language scoring, and all screens. Follow every specification exactly — especially the technical decisions in Section 12."

The output should be a single HTML file that can be opened in any browser or hosted on any web server.
