# tiny-chess — a 4 KB chess engine in Python

A UCI chess engine that fits in **4,096 bytes** (`main.py`), built for the
make.fun bounty *Build the strongest chess engine in 4 KB of Python*
(`tiny-chess-01`). It plays the kit's practice gauntlet at **1,485 mean Elo**
(practice trials, 6 games each vs the house anchors).

## How it works

`main.py` is a self-contained loader: it holds the whole engine as an
LZMA-compressed payload in a latin-1 string literal and `exec`s it.
The engine itself is a classic alpha-beta design, tuned for strength per
byte and per node:

- **Board**: 0x88, integer move encoding, incremental Zobrist hash.
- **Eval**: material + piece-square tables (sunfish-style tables, stored as
  least-squares additive file/rank profiles to save bytes), bishop-pair
  bonus, king safety via middlegame/endgame PST interpolation by phase.
- **Search**: iterative deepening by **fixed node budget** (default 150,000
  nodes/move, override with `CHESS_NODES`), so play is fully deterministic
  — no clock, no unseeded randomness. Negamax with alpha-beta, PVS,
  transposition table, killer moves, null-move pruning, LMR, quiescence
  search with delta pruning and bad-capture filtering.
- **Protocol**: talks to the make.fun environment over HTTP (`MAKEFUN_ENV_URL`)
  with the `/game`, `/move`, `/resign` endpoints.

## Files

- `main.py` — the engine (4,035 bytes). The only file that matters for grading.
- `LICENSE` — MIT.
- `TESTING.md` — test evidence: perft, benchmarks, practice-trial Elo.
- `dev/` — development sources: the readable engine (`engine.py`, the exact
  source `main.py` is packed from), the packer (`pack.py`), the AST minifier
  (`minify.py`), the local trial harness (`run_trial.sh`), perft tests
  (`test.py`), and earlier engine variants (`engine_v*.py`). Not part of the
  submission.

## Running

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:PORT python3 main.py
```

Optional: `CHESS_NODES` (default 150000) to change the fixed node budget.
Python 3.13, standard library only.

## Building `main.py`

```sh
cd dev && python3 pack.py engine.py ../main.py
```

The packer strips comments, minifies identifiers with an AST renamer,
re-indents to one space, joins simple statements, and embeds the
LZMA-compressed source. See `dev/pack.py` for the exact pipeline.
