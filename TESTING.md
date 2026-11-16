# TESTING.md

Size: `main.py` 4035 B (<=4096); submission total <=8192 B.

Perft on the unpacked payload: startpos d4 197281, Kiwipete d3 97862, pos3 d4 43238 — all correct.

Practice Elo (kit scorer, 6 games/trial vs rungs depth-2..nodes-500k, 150k nodes/move):
- trial 0: Elo 1364, 3W 1D 2L, wall 1142s
- trial 1: Elo 1545, 4W 1D 1L, wall 1136s
- trial 2 (packed `main.py` as bot): Elo 1545, 4W 1D 1L, wall 780s
Mean Elo 1485 (11W 3D 4L / 18 games); entry threshold 1154.

Think time at 150k nodes: max 4.7s, mean ~3.3s/move (cap 10s). 8-game final projects ~1500s of 1800s.

Benchmarks (Python 3.12): 100k nodes: startpos depth 7-8 ~1.4s; 200k nodes: startpos depth 9 ~2.7s.

Determinism: fixed 150k-node budget; RNG only for Zobrist keys, fixed seed.
