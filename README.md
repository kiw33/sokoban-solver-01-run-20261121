# Sokoban Solver — push-optimal, room by room

Entry for make.fun competition `sokoban-solver-01-run-20261121`
("Build the best Sokoban solver, 21 November 2026").

## How it works

Every generated level is one 1-wide hallway with 1–4 small rooms off it. A
box pushed out of a room's door into the hallway can never come back (the
player cannot reach the hallway's far side of the door), so each room is an
independent problem and the level optimum P\* is the sum of the rooms'
push-optima.

1. **Decompose** — cut the hallway row's dead squares (squares from which a
   box reaches no goal) out of the floor; each remaining connected part
   holding boxes is a room.
2. **Solve each room push-optimally** — breadth-first search over pushes
   (the first solved state met is push-optimal). A state is the boxes'
   squares plus the player's reachable region, named by its lowest square.
   Two deadlocks are pruned, neither of which can prune a solvable state:
   dead squares (reverse pull-reachability from the goals) and frozen 2×2
   blocks of walls and boxes with a box off its goal. While solving a room,
   the other rooms' boxes stand as walls (on their goals once done).
3. **Stitch** — the rooms are solved in turn, the player walking on from
   where the last room left it; walk moves are shortest paths (BFS) and do
   not change the push count.

Fallbacks, in order: whole-level push BFS, then a plain move-based A\* so a
level the push search gives up on may still be solved at a lower score
instead of scoring 0.

The search is pure work-budgeted (a cap on states, never the clock) and
uses no randomness, so two runs on the same set write byte-identical
answers. Node 22, erasable TypeScript only, no dependencies, no network.

## Practice results (kit's practice set, local grading)

| instance | pushes | P\* | points |
|----------|--------|-----|--------|
| i001 | 13 | 13 | 1,000 |
| i002 | 16 | 16 | 1,000 |
| i003 | 29 | 29 | 1,000 |
| i004 | 47 | 47 | 1,000 |
| i005 | 60 | 60 | 1,000 |

**Practice score: 1,000,000 / 1,000,000** (passed 5/5, all instances at P\*).
House baseline plain-astar 1.0.0 on the same practice set: **200,000**
(only i001 solved; i002–i005 unsolved). Runtime ~1.5 s for the whole set
(budget 60 s). Determinism verified: two runs byte-identical
(`grade-local` L1 regrade reproduced byte for byte).

## Files

- `main.ts` — the solver (entrypoint, run as `node /bot/main.ts`)
- `LICENSE` — MIT (kit starter license)
- `.makefun/owner.json` — claim file naming the payout wallet
