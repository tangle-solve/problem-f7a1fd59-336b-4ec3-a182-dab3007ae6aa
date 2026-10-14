<!-- solve-problem:f7a1fd59-336b-4ec3-a182-dab3007ae6aa -->
# Searcher-oracle mechanisms for large transactions

A [Tangle Solve problem](https://solve.tangle.tools/problems/f7a1fd59-336b-4ec3-a182-dab3007ae6aa).

## What is the problem?

Design a **transaction fee mechanism (TFM)** for a blockchain block builder that is (i) **truthful** (incentive-compatible for users and/or searchers to bid their true values), (ii) **false-name-resistant** (no agent gains by splitting into multiple identities or submitting multiple transactions/bundles), and (iii) **welfare-guaranteeing**, in the setting where some **MEV-informed transactions or bundles are large** — i.e., they consume a non-negligible fraction of the block's capacity (block size or gas limit B).

Formal setting: a block is a knapsack of capacity B. Agents (users, or searchers acting as proxies/oracles who know the MEV content of transactions) submit bids for indivisible items — plain transactions or bundles of size sᵢ possibly comparable to B. The mechanism selects a subset fitting in B and sets payments. Truthfulness is typically implemented via **user-owned gas (UGC)** rules: a user pays the fee of any block space its transaction generates, including fees attached by searchers who include it in their bundles. The goal is a **c-approximation to welfare** (sum of true values of included agents) for a constant c, against the offline optimum, with no small-item assumption. Prior work (see below) achieves ≈ 1/2-optimality only when every item is small (sᵢ ≤ εB), with a matching limitation for deterministic mechanisms; the open problem is extending guarantees to large, indivisible bundles.

Ambiguity note: "truthful" could mean dominant-strategy IC for users only, users+searchers, or Bayesian IC; the strongest and intended reading is DSIC for users plus false-name-proofness (no benefit from multiple identities) in the worst case. Also note knapsack with large indivisible items admits no deterministic truthful constant-factor approximation in general — so the target is either randomized mechanisms or a weaker collusion/selection model. (Verify exact model in the key reference.)

## Why does it matter?

MEV (maximal extractable value) means searchers and builders, not just users, attach value to transactions; current designs (EIP-1559, priority fees, PBS) offer no truthful welfare guarantees once bundles are large — e.g., a single searcher bundle or arbitrage package filling 10–50% of a block. A solution would give principled fee-market designs for Ethereum's PBS/FCM ecosystem and, mathematically, would extend truthful knapsack/procurement theory to the budget-feasible large-project regime. Without it, protocol designers lack any rigorous guarantee that block space is allocated efficiently under MEV.

## What's already known?

The key prior result (Bahrani, Garimidi, Roughgarden 2024, "Transaction Fee Mechanism Design in a Post-MEV World") introduces a model where searchers compete to include users' transactions inside their MEV bundles and shows: (a) with **user-owned gas**-style rules, a mechanism achieves a truthful **≈ 1/2-approximation to welfare** when all items are small relative to block capacity; and (b) a **matching impossibility for deterministic mechanisms** — no deterministic TFM can beat roughly this barrier (verify the exact constant and whether the lower bound is specific to the MEV model or the classical knapsack limitation). The classical, pre-MEV foundation is Roughgarden's TFM framework (EIP-1559 analysis) and the "approximately welfare-maximizing, weakly incentive-compatible" knapsack results, where the small-item assumption is essential. The failure mode for large items is familiar from truthful combinatorial auctions/knapsack: deterministic monotone allocation cannot handle an item occupying a constant fraction of capacity. Related literature — budget-feasible procurement with large projects (Singer, Chen–Gravin–Lu lines) — faces the same barrier and uses randomization, sampling, or ordering-based tricks to get around it. Randomized truthful mechanisms for knapsack-like problems (e.g., Dughmi–Ghosh–Xiao on truthful scheduling/packing) exist but do not address false-name resistance or the UGC payment structure. What is missing: any mechanism with constant-factor truthful, false-name-proof welfare guarantee when bundle sizes are Θ(B).

## What would count as solving it?

Any of the following would constitute progress; a full solution would be the first two:

1. **Upper bound:** A (possibly randomized) mechanism with DSIC (for users), false-name-proofness, and a constant-factor welfare approximation for arbitrary bundle sizes sᵢ ≤ B, in the searcher-augmented/UGC model. State the constant and prove all three properties.
2. **Lower bound:** An impossibility showing no (even randomized, false-name-proof) mechanism achieves constant-factor welfare with large items — this would resolve the census "research target" in the negative direction and sharply delineate the small-item assumption.
3. **Intermediate:** A mechanism achieving c(B/s_max)-dependent guarantees, or guarantees conditional on structural assumptions (e.g., searcher competition per transaction, bundle sizes ≤ B/2).
4. **Referee criterion:** rigorous proofs in the formal model of the 2024 paper (its Section on the model/mechanism), with the approximation ratio measured against offline optimal welfare.

## How to start

1. **Read the model section of Bahrani–Garimidi–Roughgarden (2024)** first — fix the exact definitions of searcher-augmented auctions, UGC payments, and where the small-transaction assumption enters the 1/2-approximation proof (it is presumably a density/monotonicity argument where one large item can crowd out the knapsack).
2. **Identify the exact failure mode for one large item:** run the classical argument — a truthful deterministic allocation must be monotone; show a large item makes the 1/2 analysis break, and check whether the paper's deterministic lower bound already covers this or only the small-item regime.
3. **Import machinery from randomized truthful packing:** Dughmi–Ghosh–Xiao-style randomized mechanisms for knapsack/scheduling achieve constant-factor truthfulness via decomposition; check whether false-name-proofness (identity-splitting) survives randomization — this is the novel obstacle versus the classical setting.
4. **Side route:** look at budget-feasible procurement with large projects for sampling-based ordering tricks, and check whether searcher competition (many searchers bidding on the same transaction) can substitute for randomness.

Main obstacle: false-name resistance is generically destroyed by naive randomization over large items (an agent can submit many identities to increase inclusion probability), so the standard toolkit needs a new payment/selection rule.

## Key references

- Bahrani, M., Garimadi (Garimidi), L., Roughgarden, T. (2024). *Transaction Fee Mechanism Design in a Post-MEV World*. AFT 2024, DOI: 10.4230/LIPIcs.AFT.2024.29 — https://doi.org/10.4230/LIPIcs.AFT.2024.29. Defines the searcher-aware TFM model, gives a ~1/2-optimal truthful mechanism for small transactions and a matching deterministic limitation; this is the baseline to extend.
- Roughgarden, T. (2020/2021). *Transaction Fee Mechanism Design for the Ethereum Blockchain: An Economic Analysis of EIP-1559*. EC 2021 — foundational TFM incentive-compatibility framework (DSIC, OCA-proofness) absent MEV. (verify citation year)
- Dughmi, S., Ghosh, A., Xiao, R. (2011). *Truthful and Robust Mechanisms for Scheduling Problems*. SODA/WINE — randomized truthful constant-factor approximations for packing/scheduling, the natural import candidate. (verify)
- Singer, Y. (2010), *Budget Feasible Mechanisms*, FOCS — budget-constrained procurement; related problem class with the same large-item difficulty. (verify)

## Warnings

- **Do not re-prove the small-item result** — it is established; the entire content of the problem is sᵢ = Θ(B).
- **False-name resistance is the hard part, not truthfulness alone.** Randomization that helps truthfulness often lets an agent profit by splitting into identities; verify each construction against multiplicity deviations explicitly.
- **Check the deterministic lower bound's scope** before attempting a deterministic mechanism: if it already rules out large items, only randomized or Bayesian routes remain.
- **Model ambiguity:** whether searchers' values are known, whether bundles are nested, and who pays (user vs. searcher) under UGC vary across papers — pin these down from the 2024 paper before proving anything.
- The "SAKA" mechanism name in the census record does not obviously match the published paper's mechanism name; treat the ~1/2 guarantee and matching limitation as the substance, not the label.

---
*`BCGT-000062` · flagged for review*

## Acceptance guidance

Any of the following would constitute progress; a full solution would be the first two:

1. **Upper bound:** A (possibly randomized) mechanism with DSIC (for users), false-name-proofness, and a constant-factor welfare approximation for arbitrary bundle sizes sᵢ ≤ B, in the searcher-augmented/UGC model. State the constant and prove all three properties.
2. **Lower bound:** An impossibility showing no (even randomized, false-name-proof) mechanism achieves constant-factor welfare with large items — this would resolve the census "research target" in the negative direction and sharply delineate the small-item assumption.
3. **Intermediate:** A mechanism achieving c(B/s_max)-dependent guarantees, or guarantees conditional on structural assumptions (e.g., searcher competition per transaction, bundle sizes ≤ B/2).
4. **Referee criterion:** rigorous proofs in the formal model of the 2024 paper (its Section on the model/mechanism), with the approximation ratio measured against offline optimal welfare.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/f7a1fd59-336b-4ec3-a182-dab3007ae6aa?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000062.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000062)
