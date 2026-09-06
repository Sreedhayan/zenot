# ♟️ ZENOT — Z-Mini Chess Engine

<div align="center">

### A compact C++ chess engine built from the ground up around classical game-tree search.

**Created by Sreedhayan (CN)**

[![Website](https://img.shields.io/badge/Website-zenot--chess--engine.netlify.app-00AEEF?style=for-the-badge)](https://zenot-chess-engine.netlify.app/)
[![Language](https://img.shields.io/badge/C%2B%2B-17%2B-00599C?style=for-the-badge&logo=cplusplus&logoColor=white)](https://isocpp.org/)
[![License](https://img.shields.io/badge/License-BSD--3--Clause-8A2BE2?style=for-the-badge)](LICENSE)
[![Protocol](https://img.shields.io/badge/Protocol-UCI-111111?style=for-the-badge)](https://www.chessprogramming.org/UCI)
[![Status](https://img.shields.io/badge/Status-Experimental-8B5CF6?style=for-the-badge)](#status)

</div>

---

## Website

The project website is available at: https://zenot-chess-engine.netlify.app/

Visit the site for demos, documentation, or a hosted interface (when available).

---

## Overview

**ZENOT** is a compact chess-engine project whose current engine implementation is named **Z-Mini**.

The project explores how a chess-playing program can evaluate positions and select moves using classical game-tree search rather than relying on a neural network.

At its current stage, Z-Mini combines:

* Legal chess move generation through its chess library interface
* Material-based position evaluation
* A pawn piece-square table
* Minimax search
* Alpha-beta pruning
* Terminal-position handling
* UCI-style engine communication

The project is intentionally small, making the underlying engine logic easy to inspect and experiment with.

> **ZENOT is a real engine project, not a mock chess interface.**

---

# ⚙️ Current Engine Architecture

The current implementation follows a straightforward pipeline:

```text
             ┌──────────────────┐
             │   UCI Command     │
             │      Input        │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │   Board State     │
             │   chess.hpp API   │
             └────────┬─────────┘
                      │
             ┌────────▼─────────┐
             │ Legal Move        │
             │ Generation        │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │ Minimax Search    │
             │ + Alpha-Beta      │
             │    Pruning        │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │ Position          │
             │ Evaluation        │
             └────────┬─────────┘
                      │
                      ▼
             ┌──────────────────┐
             │    bestmove       │
             │      UCI          │
             └──────────────────┘
```

---

# 🧠 How Z-Mini Thinks

## 1. Material Evaluation

The engine assigns numerical values to chess pieces.

| Piece  | Value |
| ------ | ----: |
| Pawn   |   100 |
| Knight |   300 |
| Bishop |   320 |
| Rook   |   500 |
| Queen  |   900 |
| King   | 20000 |

The evaluation starts with material balance and adds a positional component for pawns.

Positive scores favor White, while negative scores favor Black.

---

## 2. Pawn Piece-Square Evaluation

Z-Mini includes a 64-square pawn piece-square table.

This gives pawns different values depending on their location on the board.

The current table rewards several central/advanced pawn positions and penalizes some less desirable placements.

Black pawns use the mirrored table so that the positional evaluation is applied from the appropriate perspective.

---

## 3. Minimax

The engine searches possible continuations using **minimax**.

Conceptually:

```text
                 Current Position
                       │
          ┌────────────┴────────────┐
          ▼                         ▼
       Move A                    Move B
          │                         │
      Opponent                 Opponent
          │                         │
       replies                  replies
          │                         │
        score                    score
          └────────────┬────────────┘
                       ▼
                 Best decision
```

The maximizing side attempts to increase the evaluation while the minimizing side attempts to decrease it.

---

## 4. Alpha-Beta Pruning

The minimax search is accelerated using **alpha-beta pruning**.

Branches that cannot improve the current result are abandoned instead of being completely searched.

This allows the engine to examine the same game tree more efficiently than an unpruned minimax implementation.

---

## 5. Fixed Search Depth

The current `go` implementation searches candidate moves using a fixed depth of **4**.

This is an implementation detail of the current Z-Mini engine and should not be interpreted as a rating or strength guarantee.

Engine strength depends on many factors beyond search depth.

---

# ♜ UCI Interface

Z-Mini communicates using a subset of the **Universal Chess Interface (UCI)** style command protocol.

The current implementation handles:

```text
uci
isready
position
go
quit
```

For example:

```text
uci
```

causes the engine to identify itself and return:

```text
id name Z-Mini
id author Gemini
uciok
```

> **Note:** the current source identifies the UCI author as `Gemini`. If the intended project attribution is `Sreedhayan (CN)`, this string should be updated in the source.

---

# 📁 Repository Structure

The repository is intentionally minimal.

```text
zenot/
│
├── LICENSE
│
└── zmini1.1
```

The engine source currently contains the evaluation, search, UCI loop, and program entry point in the same implementation unit.

The source includes:

```cpp
#include "chess.hpp"
```

so the corresponding chess library/header dependency must be available when compiling the engine.

---

# 🔬 Technical Details

### Evaluation

Current evaluation consists of:

```text
Material Score
      +
Pawn Positional Bonus
      =
Position Evaluation
```

### Search

Current search consists of:

```text
Legal Moves
     ↓
Make Move
     ↓
Recursive Minimax
     ↓
Alpha-Beta Pruning
     ↓
Unmake Move
     ↓
Compare Scores
     ↓
Best Move
```

### Terminal Positions

The engine checks the board's game-over state during search.

The current implementation distinguishes winning, losing and drawn terminal results through the chess library's game-result interface.

---

# 🚧 Current Limitations

ZENOT is intentionally not presented as a feature-complete tournament engine.

The current source does **not** provide evidence for features such as:

* Neural-network evaluation
* NNUE
* Machine learning
* Self-play training
* Opening books
* Transposition tables
* Iterative deepening
* Quiescence search
* Killer-move heuristics
* History heuristics
* Late-move reductions
* Null-move pruning
* Syzygy tablebases
* Multiplayer networking
* Web UI
* Mobile application
* Online matchmaking
* Rating system
* Cloud analysis

Those features should only be documented if they are actually implemented.

---

# 🧪 Project Status

**Current stage: Experimental / early engine implementation**

ZENOT currently focuses on the core idea of building a chess engine around:

> **Board state → evaluation → game-tree search → best move**

The small codebase makes it particularly suitable for experimentation with chess-programming techniques and future engine improvements.

---

# 🛠️ Future Development

Possible future engineering directions include improving the search and evaluation architecture.

Potential areas include:

* More sophisticated piece-square tables
* Improved evaluation terms
* Iterative deepening
* Move ordering
* Transposition tables
* Quiescence search
* Search extensions
* Time-controlled search
* More complete UCI support
* Automated engine testing
* Perft testing
* Search diagnostics
* A graphical interface

These are **future possibilities, not current capabilities**.

---

# 👨‍💻 Author

**Sreedhayan (CN)**

ZENOT is created and developed by Sreedhayan.

The project is an exploration of chess programming, search algorithms, evaluation functions, and engine architecture.

---

# 📜 License

ZENOT is distributed under the **BSD 3-Clause License**.

See [`LICENSE`](LICENSE) for the complete license text.

---

<div align="center">

### ZENOT

**Small codebase. Classical search. Real chess-engine logic.**

Built by **Sreedhayan (CN)** ♟️

Project website(please check it out): https://zenot-chess-engine.netlify.app/

</div>
