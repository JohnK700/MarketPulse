<!-- Back to top link -->
<a id="readme-top"></a>

<!-- PROJECT SHIELDS -->
[![MIPS Assembly][MIPS-shield]][MIPS-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<br />
<div align="center">

<h3 align="center">Number Matching Game</h3>

  <p align="center">
    A memory card matching game built entirely in MIPS Assembly, featuring real-time bitmap graphics, keyboard input, audio, and a live timer — running in the MARS simulator.
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#about-the-project">About The Project</a></li>
    <li><a href="#built-with">Built With</a></li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#implementation">Implementation Details</a></li>
    <li><a href="#project-structure">Project Structure</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
  </ol>
</details>

---

## About The Project

Number Matching Game is a fully functional memory matching game written from scratch in MIPS Assembly. Players flip cards by pressing keyboard keys and must match pairs of numbers — displayed in one of three formats: single digit, two digit, or multiplication expression (e.g. `3x4`). The game tracks elapsed time, displays a live score, plays audio on events, and renders a victory screen when all pairs are matched.

**Key concepts demonstrated:**
- Bitmapped display rendering using direct memory writes to a frame buffer
- Real-time keyboard input via MMIO (Memory-Mapped I/O) polling
- Random number generation and Fisher-Yates shuffle implemented in assembly
- Register-based state management and stack discipline for nested subroutine calls
- Audio playback using MARS system calls
- Custom character rendering (digits 0–9, A–F, colon, X) pixel-by-pixel

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## Built With

[![MIPS Assembly][MIPS-shield]][MIPS-url]
[![MARS][MARS-shield]][MARS-url]

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## Getting Started

### Prerequisites

- [MARS MIPS Simulator](https://dpetersanderson.github.io/) (v4.5 recommended)
- Java Runtime Environment (required to run MARS)

### Installation

1. Clone the repo
   ```sh
   git clone https://github.com/JohnK700/Number-Matching-Game.git
   ```
2. Open MARS and load `Main.ASM`
3. Enable the **Bitmap Display** tool in MARS:
   - Go to `Tools → Bitmap Display`
   - Set Unit Width: `4`, Unit Height: `4`, Display Width: `512`, Display Height: `256`
   - Connect to MIPS memory
4. Enable the **Keyboard and Display MMIO Simulator** tool:
   - Go to `Tools → Keyboard and Display MMIO Simulator`
   - Connect to MIPS memory
5. Assemble and run (`F3` then `F5`)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## Usage

- **Keys `0–9`** — flip cards 1 through 10
- **Keys `a–f`** — flip cards 11 through 16
- Flip two cards with the same value to match them
- If two flipped cards don't match, they automatically flip back over
- Match all 8 pairs to win
- A live **timer** (MM:SS) and **score** (cards remaining) are displayed on the bitmap at all times

### Card Number Formats

Cards display their values in one of three randomly assigned formats:

| Format | Example |
|---|---|
| Single digit | `7` |
| Two digit | `12` |
| Multiplication expression | `3x4` |

Two cards match if their values are numerically equal (e.g. `12` matches `3x4`).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## Implementation Details

| Component | Details |
|---|---|
| **Random number generation** | `RandomNumGen.ASM` generates 8 unique values, duplicates each for pairing, then applies a Fisher-Yates shuffle using `SysRandIntRange` to randomize card positions |
| **Number formatting** | Each value is randomly assigned one of three display formats (single digit, two digit, or `NxM`); values are stored as 3-byte ASCII sequences with `.` as a terminator |
| **Bitmap rendering** | `Bitmap.ASM` writes directly to a frame buffer in memory; cards, backgrounds, characters, and the victory screen are all drawn via pixel-level `sw` instructions |
| **Character drawing** | `draw` dispatches on the ASCII value in `$t7` to hand-coded pixel routines for digits 0–9, letters A–F, colon, and X |
| **Keyboard input** | Main game loop polls the MMIO register at `0xFFFF0000` each iteration; ASCII values `48–57` (0–9) and `97–102` (a–f) are mapped to card indices |
| **Match logic** | On each card flip, the value is decoded from its ASCII format and compared against the last flipped card's stored value; mismatches trigger a re-flip after a short delay |
| **Timer & score** | Time is tracked as a loop counter incremented every second via `SysSleep`; both timer and score are redrawn each loop iteration at fixed bitmap coordinates |
| **Audio** | `soundPlayer.ASM` plays `sound1.wav` and `sound2.wav` on game events using MARS audio system calls |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## Project Structure

```
Number-Matching-Game/
├── Main.ASM            # Game loop, input handling, match logic, score/timer updates
├── Bitmap.ASM          # Frame buffer initialization, card rendering, character drawing
├── RandomNumGen.ASM    # Number generation, shuffling, and ASCII format encoding
├── soundPlayer.ASM     # Audio playback for game events
└── SysCalls.asm        # System call constants (.eqv definitions)
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## Roadmap

- [ ] Difficulty modes (more cards, faster timer)
- [ ] High score tracking across sessions
- [ ] Mouse input support in place of keyboard
- [ ] Animated card flip transitions

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

<!-- MARKDOWN LINKS & BADGES -->
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/john-kalu-b60587379/
[MIPS-shield]: https://img.shields.io/badge/MIPS_Assembly-darkred?style=for-the-badge&logo=assemblyscript&logoColor=white
[MIPS-url]: https://en.wikipedia.org/wiki/MIPS_architecture
[MARS-shield]: https://img.shields.io/badge/MARS_Simulator-orange?style=for-the-badge&logo=java&logoColor=white
[MARS-url]: https://dpetersanderson.github.io/
