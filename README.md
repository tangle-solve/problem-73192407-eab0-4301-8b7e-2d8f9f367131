<!-- solve-problem:73192407-eab0-4301-8b7e-2d8f9f367131 -->
# Single-exponential exact treewidth

A [Tangle Solve problem](https://solve.tangle.tools/problems/73192407-eab0-4301-8b7e-2d8f9f367131).

## What is the problem?

Given an undirected graph G on n vertices and an integer k, decide whether the treewidth tw(G) ≤ k, and if so output a tree decomposition of width exactly tw(G) (or at most k, see Warnings) — in time 2^{O(k)} · n, i.e., single-exponential dependence on k and *linear* dependence on n. Current exact algorithms achieve 2^{O(k)}·n^{O(1)} (single-exponential but superlinear input time, e.g., 2^{O(k)}·n^{2} via Bodlaender–Koster-style or Korhonen/Belbasi–Fürer improvements) and older FPT algorithms (Bodlaender 1996) run in 2^{O(k³)}·n — linear in n but not single-exponential in k. The goal is to close this gap: linear input time AND single-exponential k-dependence simultaneously. Key subtlety: the algorithm must actually verify optimality (width ≤ k but width > k−1) or at least produce an optimal decomposition; the 2^{O(k)}·n time budget does not allow even one pass of, say, n log n for some standard subroutines unless it is O(n).

## Why does it matter?

Treewidth is the master parameter for dynamic programming over graph structure: CSP, probabilistic inference (optimal variable elimination orderings), compile-time optimization of join trees, and model checking all reduce to computing an optimal decomposition first. Any 2^{O(k)}·n^{f(k)} downstream DP is bottlenecked by an exact-treewidth step costing 2^{O(k)}·n² or worse, so matching linear time would make the preprocessing genuinely negligible. It is also a benchmark question for whether "single-exponential + linear" is achievable for exact structural computation, the same way it was for FPT approximation (resolved by Bodlaender et al. and Korhonen 2021–2023).

## What's already known?

- **Bodlaender (1996):** decides tw(G) ≤ k and outputs a decomposition in f(k)·n time with f(k) = 2^{O(k³)} — linear in n, terrible in k. Uses an irrelevant-vertex/recursive-compression scheme.
- **Bodlaender–Drange et al. (2013+), Fomin et al.:** 2^{O(k)}-time algorithms with polynomial (n² or n³) dependence — via dynamic programming over potential maximal cliques (Fomin–Todinca–Villanger line) or via rank-based/Cut & Count techniques.
- **Approximation:** a randomized single-exponential (1+ε)-approximation in 2^{O(k)}·n time exists (Belbasi–Fürer 2022; Korhonen 2023 deterministic 2^{O(k/ε)}·n) — this gives a decomposition of width c·k, not an optimal one, and is the main reason the exact version is exposed as the missing piece.
- Known lower-bound barriers: single-exponential exact algorithms cannot rely on the standard rank-based DP over potential maximal cliques to be linear-time, since enumerating them is inherently superlinear; the Bodlaender-style linear-time approach is what needs a single-exponential version, and its combinatorics (safe separators, reduction rules) has resisted this.

## What would count as solving it?

1. **Upper bound (positive):** a (possibly randomized) algorithm with proven running time 2^{c·k}·n for constant c, deciding tw(G) ≤ k exactly and outputting a decomposition of width k when yes. Randomized with bounded error would be a major advance; deterministic is the gold standard.
2. **Lower bound (negative):** a proof, e.g., under SETH or related hypotheses, that 2^{O(k)}·n (or 2^{O(k)}·n^{1+ε}) is impossible — e.g., that any 2^{O(k)}·n^{1-o(1)} algorithm would refute SETH. Either direction resolves the census item.
3. Partial: exact algorithm in 2^{O(k)}·n log n or 2^{O(k)}·n·α(n) would be significant stepping stones.

## How to start

1. Read Bodlaender (1996) SIAM J. Comput. for the only known linear-in-n paradigm, and note where the 2^{O(k³)} blowup arises (large constants from graph-theoretic lemmas on bounded-degree ambidextrous reductions). Ask: which of those lemmas can be replaced by single-exponential (possibly randomized) equivalents?
2. Read Korhonen's deterministic 2^{O(k)}·n approximation (FOCS/JACM 2023, verify venue). It already finds a decomposition of width O(k) in the target time; the natural attack is to run the approximation, then certify/exactify within the O(k)-width structure — e.g., check whether a width-(ck) decomposition contains a refinement of width k−1 using a single-exponential-time local argument on the (linear-size) decomposition.
3. Consider rank-based DP over the approximate decomposition's bags: if the approximation's decomposition has O(n) bags of size O(k), can an exact lower-bound certificate (a bramble, dual to treewidth) of size k+1 be searched for in 2^{O(k)}·n time? A matching-bramble search is the dual formulation.
4. Main obstacle: all known exact DPs are superlinear because they touch global structure (potential maximal cliques); all known linear-time algorithms are single-pass and combinatorial, with the exactness coming from heavily superexponential case analysis. Bridging requires a new linear-time-exactness mechanism.

## Key references

- H. L. Bodlaender, "A Linear-Time Algorithm for Finding Tree-Decompositions of Small Treewidth," SIAM J. Comput. 25(6):1305–1317, 1996. The f(k)·n exact algorithm with f(k)=2^{O(k³)}; the starting point for any linear-in-n approach.
- F. V. Fomin, I. Todinca, Y. Villanger, "Exact algorithm for treewidth" (SIAM J. Discrete Math / earlier SOSA-lineage work, verify venue/year). O(1.88^n) and parameterized 2^{O(k)}·n^{O(1)} exact algorithms via potential maximal cliques.
- M. Korhonen, "A Single-Parameter Approach to Treewidth" / deterministic single-exponential approximation in 2^{O(k)}·n (FOCS 2023; verify exact title). Provides the near-miss: single-exponential linear-time approximation, the key structural tool to build on.
- T. Korhonen, "Open Problems" list (September 2026 version referenced in the census record). The source recording this problem as open; no link available — obtain from the author's page (verify).
- Bodlaender, Drange, Kregers, Kratsch, "A cubic kernel for feedback vertex set and treewidth" plus the follow-up 2^{O(k)}·n³ exact FPT work (2013+, verify authors) — representative of the current best single-exponential exact running times.

## Warnings

- "Treewidth at most k" as stated can be trivialized to approximation: the intended question is *exact* decision (accept iff tw(G) ≤ k, reject when tw(G) ≥ k+1) or output of an *optimal* decomposition. Approximation results (width ck) do not resolve it.
- Watch the n-dependence: n log n or n·α(n) is not strictly 2^{O(k)}·n; decide up front whether the target tolerates these (most formulations would count them as partial progress, not full resolution).
- The census record's dates ("September 2026") cannot be verified against the literature; treat the framing, not the citations' years, as authoritative, and verify all bibliographic details above marked (verify).

---
*`ALGO-000093` · unverified*

## Acceptance guidance

1. **Upper bound (positive):** a (possibly randomized) algorithm with proven running time 2^{c·k}·n for constant c, deciding tw(G) ≤ k exactly and outputting a decomposition of width k when yes. Randomized with bounded error would be a major advance; deterministic is the gold standard.
2. **Lower bound (negative):** a proof, e.g., under SETH or related hypotheses, that 2^{O(k)}·n (or 2^{O(k)}·n^{1+ε}) is impossible — e.g., that any 2^{O(k)}·n^{1-o(1)} algorithm would refute SETH. Either direction resolves the census item.
3. Partial: exact algorithm in 2^{O(k)}·n log n or 2^{O(k)}·n·α(n) would be significant stepping stones.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/73192407-eab0-4301-8b7e-2d8f9f367131?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000093.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000093)
