# ʻUlu Maika — Makahiki Games

A pixel-art browser game based on the traditional Hawaiian stone-rolling game played during the Makahiki harvest festival — a celebration of peace, community, and skills.

Roll the biconvex maika stone through the pahu hoku (stakes), or send it flying down the kahua maika course. Hear the crowd chant *"Ā ʻo ia!"* (Go for it!) and *"ʻOnipa'a!"* (Stay determined!) as your stone rolls. Learn the history of a game that King Kamehameha I played — one where thousands gathered and wagered everything on a single throw.

**[Play it now →](https://yourusername.github.io/ulu-maika/)** *(replace with your URL)*

## Screenshots

*Coming soon*

## About the Game

ʻUlu Maika was a traditional Hawaiian stone-rolling game played during the Makahiki harvest festival to test strength and accuracy. The stone was a biconvex disc — thick at the center, narrow at the edges — originally made from ʻulu (breadfruit), later from polished stone, coral, and lava. Courses (kahua maika) were engineered to stretch up to a mile, with smooth, hard-packed trenches sometimes featuring slight turns to challenge each player.

This game brings two of the four historical playing methods to life:

- **Precision Mode** — Roll the maika (stone) through the pahu hoku (stakes) without touching them, from thirty to forty yards away
- **Distance Mode** — Roll the maika (stone) as far as possible down the kahua maika course

All scoring uses ʻŌlelo Hawaiʻi (Hawaiian language). Cultural "Did You Know?" facts appear between games, teaching players about the history behind what they're playing.

## Features

- Two game modes based on historically documented playing methods
- Hawaiian-language scoring with English subtitles
- Animated pixel-art Hawaiian landscape (mountains, ocean, coconut palms, plumeria)
- Crowd spectators that chant and wave during rolls
- Perspective system — overpowered rolls recede toward the horizon instead of flying offscreen
- Biconvex stone with rotation physics and dust trail
- Cultural fact screens between games
- High score tracking
- Traditional niho (tooth) triangle border pattern
- Flick-to-roll touch and mouse controls
- Fully responsive — works on any screen size

## How to Play

1. **Tap** the title screen, then choose Precision or Distance
2. **Swipe** in the direction you want the stone to roll (swipe up to roll forward)
3. The length of your swipe controls power — a longer swipe throws harder
4. You get **5 rolls** per game
5. After your rolls, you'll see a cultural fact and your final score with a rating from Keiki (beginner) to Aliʻi (chief)

### Scoring — Precision Mode

| Result | Hawaiian | Points |
|--------|----------|--------|
| Perfect center | Poʻokela! | +100 |
| Good pass | Maikaʻi loa! | +50 |
| Barely through | Holo kokoke! | +25 |
| Hit stake, still went in | Pōmaikaʻi! | +25 |
| Hit stake, bounced out | Auē! | 0 |
| Too far | He keu! | 0 |
| Too short | Hoʻohakalia! | 0 |

### Scoring — Distance Mode

| Result | Hawaiian | Points |
|--------|----------|--------|
| 500+ feet | Pāʻani Maikaʻi! / Poʻokela! | distance ÷ 5 |
| 250–500 feet | Maikaʻi loa! | distance ÷ 5 |
| Under 250 feet | Hoʻohakalia! / Hana Hou! | distance ÷ 5 |

## Quick Start

No build step. No dependencies. It's a single HTML file.

### Option 1: Open locally
```bash
git clone https://github.com/yourusername/ulu-maika.git
open ulu-maika/ulu-maika.html
```

### Option 2: Host it
Upload `ulu-maika.html` to any web server, GitHub Pages, Netlify, or Vercel. Done.

### Option 3: GitHub Pages
1. Fork this repo
2. Go to Settings → Pages → Source: main branch
3. Your game is live at `https://yourusername.github.io/ulu-maika/`

## Tech Stack

- **Single HTML file** — everything in one file, zero dependencies
- **HTML5 Canvas** — all graphics drawn programmatically (no image assets)
- **Vanilla JavaScript** — no frameworks, no libraries
- **Pixel art** — all visuals rendered with `fillRect` pixel plotting
- **~980 lines** of code total

The game runs at 320×480 internal resolution and scales responsively to any screen. Touch and mouse input both work identically.

## Project Structure

```
ulu-maika/
├── ulu-maika.html      # The complete game (single file)
├── ulu-maika-SAD.md     # Software Architecture Document
├── README.md            # This file
└── LICENSE              # Apache 2.0
```

## Software Architecture Document

The `ulu-maika-SAD.md` file contains a comprehensive specification of every game mechanic, screen design, physics value, color hex code, Hawaiian word, and technical decision. It is detailed enough to regenerate the entire game from scratch in a single AI-assisted session.

## Hawaiian Language Reference

### Scoring Words

| Hawaiian | Meaning | Context |
|----------|---------|---------|
| ʻUlu Maika | Breadfruit disc rolling | The game itself |
| Poʻokela! | Perfect! | Perfect roll |
| Pāʻani Maikaʻi! | Great Play! | Great play |
| Maikaʻi loa! | Very Good! | Good roll |
| Pōmaikaʻi! | Lucky! | Lucky bounce through stakes |
| Holo kokoke! | Barely made it! | Close call |
| Auē! | Oh no! | Missed |
| Hahau | To strike, hit | Hit the stake |
| He keu | Too much | Overshot |
| Hoʻohakalia | Lagging behind | Too short |
| Hana Hou! | Do it again! | Encouragement |
| Pau | Finished | Game over |
| Pāʻani Hou! | Play again! | Restart prompt |

### Crowd Chants

| Hawaiian | Meaning |
|----------|---------|
| Ā ʻo ia! | Go for it! |
| ʻOnipa'a! | Stay determined! |

### Game Terms

| Hawaiian | Meaning |
|----------|---------|
| Kahua Maika | The course/playing field |
| Pahu hoku | The stakes/goal posts |
| Maika | The stone disc |
| Makahiki | Harvest festival season |

## Roadmap

- [ ] Language toggle (English ↔ Hawaiian)
- [ ] Sound effects (Web Audio API)
- [ ] Additional Makahiki mini-games (Collision mode, Racing mode, and more)
- [ ] Tile-based launcher for the full game collection
- [ ] Persistent high scores (localStorage)
- [ ] Multiplayer leaderboard
- [ ] Mobile app (iOS/Android via Capacitor)

## Cultural Note

This game was built with respect for Hawaiian culture and the significance of the Makahiki season. The historical details — the kahua maika courses, the crowd energy, the wagering customs — come from documented historical sources. The goal is cultural preservation in a format people will engage with.

If you have knowledge of Makahiki traditions and see something that could be more accurate, please open an issue. Mahalo.

## Contributing

Contributions welcome! Whether it's bug fixes, new mini-games, better pixel art, sound design, or cultural accuracy improvements — open a PR or issue.

## License

```
Copyright 2026 kahalewai

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

## Acknowledgments

- Built iteratively with [Claude](https://claude.ai) by Anthropic
- Cultural research sourced from historical documentation of Hawaiian games
- Inspired by childhood memories of playing ʻUlu Maika at the Polynesian Cultural Center

---

*Created by kahalewai*
