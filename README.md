# Makahiki Games - ʻUlu Maika

![ʻŌlelo%20Hawaiʻi](https://img.shields.io/badge/ʻŌlelo%20Hawaiʻi-Yes!-blue)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-orange.svg)](LICENSE)
![Version](https://img.shields.io/badge/version-1.0.0-green.svg)

<br>

## Intro

ʻUlu Maika is a traditional Hawaiian game played during the Makahiki harvest festival, a celebration of peace, community, and skills. Although played for fun today, the game was traditionally played to test strength and accuracy. The stone is a biconvex disc, thick at the center, narrow at the edges, originally made from ʻulu (breadfruit), later from polished stone, coral, and lava. Courses (kahua maika) were engineered to stretch up to a mile, with smooth, hard-packed trenches sometimes featuring slight turns to challenge each player. Learn the history of a game that King Kamehameha I played, one where thousands gathered and wagered everything on a single throw.

<br>

This is a pixel-art browser game that brings two of the four historical playing methods to life:

- Precision Mode - Roll the maika (stone) through the pahu hoku (stakes) without touching them, from thirty to forty yards away
- Distance Mode - Roll the maika (stone) as far as possible down the kahua maika course

<br>

**[Play it now →](https://yourusername.github.io/ulu-maika/)** *(replace with your URL)*

<br>

## Why this was created

I was discussing the Makahiki Games with my Keiki, and one of them said it should be a mobile game. So I decided to try a one-shot using AI (Anthropic Claude), and the game was born. The current version was created using 8 prompts (one to create, 7 to edit), about 30 minutes of effort. It was so easy, my Keiki can do it. And it was then, that I realized the true value of these efforts; Not only to spread Hawaiian Language and Culture, but also for our Keiki to learn how to use AI to build things.

- Hawaiian Language - Keiki will learn Hawaiian Language (ʻŌlelo Hawaiʻi) as they play
- Hawaiian Culture - Keiki will learn about the game, and how it is played
- Technology - Keiki can experiment with Building the Game using AI (One Shot)

Along with the game (which is a single HTML file that can be put anywhere), there is a SAD file in Markdown (.md). Build your own version of the game with your Keiki. Click on the SAD Document above, and copy everything to your Clipboard. Paste the SAD into an AI Interface (I used Claude, probably works with ChatGPT too), and follow the instructions at the end of the file. You and your Keiki can watch as AI builds you your own version of the game. If you want to change aspects of the game, update the SAD before pasting.

<br>

## Screenshots

<img width="380" height="567" alt="ulu-maika1" src="https://github.com/user-attachments/assets/185e5089-d23f-4487-a19b-f461843d1d6b" />

<br>
<br>

## Features

- Two game modes based on historically documented playing methods
- Hawaiian-language scoring with English subtitles
- Animated pixel-art Hawaiian landscape (mountains, ocean, coconut palms, plumeria)
- Crowd spectators that chant and wave during rolls
- Perspective system - overpowered rolls recede toward the horizon instead of flying offscreen
- Biconvex stone with rotation physics and dust trail
- Cultural fact screens between games
- High score tracking
- Traditional niho (tooth) triangle border pattern
- Flick-to-roll touch and mouse controls
- Fully responsive, works on any screen size

<br>

## How to Play

1. **Tap** the title screen, then choose Precision or Distance
2. **Swipe** in the direction you want the stone to roll (swipe up to roll forward)
3. The length of your swipe controls power — a longer swipe throws harder
4. You get **5 rolls** per game
5. After your rolls, you'll see a cultural fact and your final score with a rating from Keiki (beginner) to Aliʻi (chief)

<br>

### Scoring - Precision Mode

| Result | ʻŌlelo Hawaiʻi | Points |
|--------|----------|--------|
| Perfect center | Poʻokela! | +100 |
| Good pass | Maikaʻi loa! | +50 |
| Barely through | Holo kokoke! | +25 |
| Hit stake, still went in | Pōmaikaʻi! | +25 |
| Hit stake, bounced out | Auē! | 0 |
| Too far | He keu! | 0 |
| Too short | Hoʻohakalia! | 0 |

<br>

### Scoring - Distance Mode

| Result | ʻŌlelo Hawaiʻi | Points |
|--------|----------|--------|
| 500+ feet | Pāʻani Maikaʻi! / Poʻokela! | distance ÷ 5 |
| 250–500 feet | Maikaʻi loa! | distance ÷ 5 |
| Under 250 feet | Hoʻohakalia! / Hana Hou! | distance ÷ 5 |

<br>

## Quick Start

No build step. No dependencies. It's a single HTML file.

<br>

### Option 1: Open locally
```bash
git clone https://github.com/yourusername/ulu-maika.git
open ulu-maika/ulu-maika.html
```

<br>

### Option 2: Host it
Upload `ulu-maika.html` to any web server, GitHub Pages, Netlify, or Vercel. Done.

<br>

### Option 3: GitHub Pages
1. Fork this repo
2. Go to Settings → Pages → Source: main branch
3. Your game is live at `https://yourusername.github.io/ulu-maika/`

<br>

## Tech Stack

- **Single HTML file** — everything in one file, zero dependencies
- **HTML5 Canvas** — all graphics drawn programmatically (no image assets)
- **Vanilla JavaScript** — no frameworks, no libraries
- **Pixel art** — all visuals rendered with `fillRect` pixel plotting
- **~980 lines** of code total

The game runs at 320×480 internal resolution and scales responsively to any screen. Touch and mouse input both work identically.

<br>

## Project Structure

```
ulu-maika/
├── ulu-maika.html      # The complete game (single file)
├── ulu-maika-SAD.md     # Software Architecture Document
├── README.md            # This file
└── LICENSE              # Apache 2.0
```

<br>

## Software Architecture Document

The `ulu-maika-SAD.md` file contains a comprehensive specification of every game mechanic, screen design, physics value, color hex code, Hawaiian word, and technical decision. It is detailed enough to regenerate the entire game from scratch in a single AI-assisted session (one shot).

<br>

## Hawaiian Language Reference

### Scoring Words

| ʻŌlelo Hawaiʻi | Meaning | Context |
|----------|---------|---------|
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

<br>

### Crowd Chants

| ʻŌlelo Hawaiʻi | Meaning |
|----------|---------|
| Ā ʻo ia! | Go for it! |
| ʻOnipa'a! | Stay determined! |

<br>

### Game Terms

| ʻŌlelo Hawaiʻi | Meaning |
|----------|---------|
| Kahua Maika | The course/playing field |
| Pahu hoku | The stakes/goal posts |
| Maika | The stone disc |
| Makahiki | Harvest festival season |

<br>

## Contributing

Contributions welcome! Whether it's bug fixes, new mini-games, better pixel art, sound design, or cultural accuracy improvements, open a PR or issue. This game was built with respect for Hawaiian culture and the significance of the Makahiki season. The historical details, the kahua maika courses, the crowd energy, the wagering customs, come from documented historical sources. The goal is cultural preservation in a format people will engage with. If you have knowledge of Makahiki traditions and see something that could be more accurate, please open an issue. Mahalo. 

<br>

These efforts are not done! The goal is to create a mini-game for each game of the Makahiki Season, and eventually release the full Makahiki Games as a free mobile app on iOS and Android.

<br>

Roadmap

- [ ] Language toggle (English ↔ Hawaiian)
- [ ] Sound effects (Web Audio API)
- [ ] Additional Makahiki mini-games (Collision mode, Racing mode, and more)
- [ ] Tile-based launcher for the full game collection
- [ ] Persistent high scores (localStorage)
- [ ] Multiplayer leaderboard
- [ ] Mobile app (iOS/Android via Capacitor)


<br>

## License

Apache 2.0

<br>

## Acknowledgments

- Built iteratively with [Claude](https://claude.ai) by Anthropic
- Cultural research sourced from historical documentation of Hawaiian games
- Inspired by childhood memories of playing ʻUlu Maika at Kamehameha Explorations
