# Independent Lean replay: OpenAI families 156 (Borsuk, dim 9) and 158 (plane not 5-colorable)

Date: 2026-11-26. Prepared with AI assistance (Claude Code, Anthropic).

## Setup
- Source: `github.com/openai/math`, upstream commit **`f57028540`** ("Merge pull request #5 from openai/codex/update-11-23"). This is the same commit as the existing Borsuk replay (teorth/optimizationproblems #394). It was checked out as a detached git worktree with no local edits.
- Toolchain: Lean **4.34.1** (`5045d0056413266e57c625dcd7c365b10e377c52`), macOS arm64, 18 cores / 128 GB.
- Mathlib: `d13f23b723`, matching `lake-manifest.json`, with a clean working tree.
- Dependencies: an import trace from both targets reaches **339 OAI modules and no package besides Mathlib**. Lake reports the repo's own `patches/` on other packages (belyi, iut, …) as "local changes", but neither proof imports those packages.
- Build cache: reused from an earlier build of the same toolchain and Mathlib revision (APFS clone). Lake rebuilt every module whose inputs differ at this commit.

## Results
| Target | Build | Time | Axioms |
|---|---|---|---|
| `lake build OAI.Geometry.PlaneColoring.Five` | ✅ 8993 jobs | 232 s, 6.9 GB peak RSS | — |
| `lake build OAI.Geometry.Borsuk.Main` | ✅ 9192 jobs | 499 s | — |
| `OAI.EuclideanFiveColor.no_proper_five_coloring` | | | `propext, Classical.choice, Quot.sound` |
| `OAI.BorsukNine.euclidean_nine_counterexample` | | | `propext, Classical.choice, Quot.sound` |

## Statements, as printed by Lean (`#check` / `#print`)
```
def OAI.EuclideanFiveColor.ProperColoring : (k : ℕ) → (ℂ → Fin k) → Prop :=
  fun k c => ∀ (x y : ℂ), ‖x - y‖ = 1 → c x ≠ c y
OAI.EuclideanFiveColor.no_proper_five_coloring : ¬∃ c, ProperColoring 5 c

OAI.BorsukNine.euclidean_nine_counterexample : ∃ X : Set (EuclideanSpace ℝ (Fin 9)),
  IsCompact X ∧ Metric.diam X = √2 ∧
  ¬∃ C : Fin 10 → Set _, (∀ i, C i ⊆ X) ∧ X ⊆ ⋃ i, C i ∧ ∀ i, Metric.diam (C i) < √2
```
The plane statement matches `lean/ComparatorChallenges/EuclideanFiveColor.lean` word for word: arbitrary colorings, no measurability. The Borsuk statement is the standard Borsuk failure in ℝ⁹: a compact set of diameter √2 that cannot be covered by 10 sets of smaller diameter.

## Not done
- **Comparator was not run.** It isn't installed on this Mac. The statement match was checked by reading the files, not mechanically.
- The prose papers were not refereed or read in full.
- Not a finite counterexample: the Borsuk result is for a compact infinite set.

## Logs
[`logs/build_plane_five.txt`](logs/build_plane_five.txt), [`logs/build_borsuk_main.txt`](logs/build_borsuk_main.txt), `logs/axioms_check.lean`, `logs/axioms.txt`.

Local absolute paths in the logs were replaced by `<worktree of openai/math@f57028540>`; nothing else was edited.
