# matchmaking-queue — Problems & Solutions (Role/Level Constrained Matchmaking)

> Pairs of: Problem (with example) → Solution / Theorem / Good practice.

---

Role & Tier Constraints (Templates)
- Problem (example): Each battle needs a mix like 3 heavies, 5 mediums, 7 lights, bounds per arty; tiers must be within ±1. During peaks you have 1,800 tier‑8 tanks (heavy‑skewed), but mediums are scarce, causing stalls.
- Solution / Theorem / Good practice: Use a template catalog (e.g., 3‑5‑7 patterns) and fill by bucket (tier, role). Greedy fill works if you separate by tier buckets and apply soft caps for scarce roles. Model as a constrained matching or min‑cost flow; “tick‑based” builder assembles batches every Δt (e.g., 250ms) to reduce fragmentation.

Long Waits (Off‑Peak, Skewed Supply)
- Problem (example): Off‑peak queue hits 30+ minutes when roles are unbalanced (too many heavies, few lights). Strict templates stall.
- Solution / Theorem / Good practice: Dynamic rule relaxation by wait time (aging). After T1, widen allowed tier spread; after T2, relax role caps (e.g., allow 1 extra heavy, fewer lights); after T3, enable cross‑region or bots. Use an aging priority: priority = base(role_weight) + α · wait_seconds.

Fairness & Starvation (Queue Discipline)
- Problem (example): Popular roles (heavies) dominate; rare roles (lights) trickle; some players keep getting skipped.
- Solution / Theorem / Good practice: Weighted fair queuing (WFQ) across role buckets; round‑robin among buckets when building a match. Add starvation protection via aging; no player’s priority stays below newer arrivals indefinitely (monotonic aging).

Round‑Robin vs Weighted Round‑Robin
- Problem (example): After meeting minimums, a greedy filler keeps pulling from the “richest” role bucket (e.g., mediums), starving others or exceeding soft mix goals.
- Solution / Theorem / Good practice: Use round‑robin to cycle roles fairly when filling remaining slots; remove a role from rotation when it’s empty or hits its cap. When supply is intentionally skewed, use weighted round‑robin: roles with higher weights get proportionally more turns. Variants like deficit round‑robin account for “cost” per pick (e.g., larger parties). This keeps selection predictable and prevents hogging.

Skill Balance (Anti‑Stomp)
- Problem (example): Two 15‑player teams end up lopsided on skill; stomps increase churn.
- Solution / Theorem / Good practice: Maintain bounds on team MMR mean and variance (σ). Use min‑cost bipartite assignment during the final team split: cost = |MMR_teamA − MMR_teamB| + λ · role_imbalance. Keep skill constraints “soft” and relax with wait time to avoid stalls.

Parties/Platoons (Group Constraints)
- Problem (example): Three‑player platoons must stick together; their combined roles may violate templates.
- Solution / Theorem / Good practice: Treat party as an atomic unit with a composite role vector; pre‑pack parties into buckets; allow template variants that reserve slots for a party; apply slightly higher aging to groups to avoid indefinite deferral.

Tick Scheduling vs Immediate Matching
- Problem (example): Matching one‑by‑one increases fragmentation; early matches consume scarce roles and block later ones.
- Solution / Theorem / Good practice: Tick‑based batching: every Δt collect a “frame” of candidates and assemble multiple matches at once using templates + min‑cost flow. Improves global optimality with bounded latency increase (Δt).

Dynamic Template Selection
- Problem (example): Static templates mismatch live supply; e.g., too many heavies waiting.
- Solution / Theorem / Good practice: Maintain a small family of templates (e.g., balanced, heavy‑skewed, light‑leaning). Pick templates proportionally to current supply while respecting caps (limit arty) and fairness. Backtest to ensure no role is permanently disadvantaged.

Cross‑Tier & Cross‑Region Merge Windows
- Problem (example): Thin queues by tier/region create long tails.
- Solution / Theorem / Good practice: Periodic merge windows (every N seconds) allow combining adjacent tiers (±1) and nearby regions (low latency) when local bucket supply is insufficient and wait thresholds are exceeded. Cap latency delta to protect experience.

Bots/Backfill (Last‑Resort)
- Problem (example): A single role scarcity blocks forming matches for many waiting players.
- Solution / Theorem / Good practice: After a high wait threshold, allow limited, clearly inferior bots to fill specific roles (e.g., 1 light). Use sparingly with clear rules and visibility for analytics to measure impact on retention.

Anti‑Sniping & Manipulation Resistance
- Problem (example): Players coordinate to force favorable compositions or dodge counters.
- Solution / Theorem / Good practice: Randomize template choice within acceptable bounds; jitter tick times; hide queue state; apply anti‑coordination rules (e.g., split clumped parties). Monitor anomalies.

Observability & SLOs
- Problem (example): Can’t see where time is lost (queueing, assembling, skill balancing), which roles starve, or how often rules relax.
- Solution / Theorem / Good practice: Track p50/p95/p99 wait, time‑in‑stage (enqueue→assigned→started), fill rates by role/tier, relaxation levels used, match fairness scores, abandonment rate. Define error budgets for p95 wait by tier/role.

Capacity Planning (Little’s Law)
- Problem (example): Unclear if target concurrency can be met; operator guesses lead to over/under‑provisioning.
- Solution / Theorem / Good practice: Apply Little’s Law L = λ·W. For arrival rate λ and target wait W, ensure the system can start matches at ≥ λ and that templates consume the constrained roles in proportion to supply.

---

Code/Prototype Ideas (optional, one‑file MVPs)
- Simulation harness (Python): generate arrivals by role/tier/MMR and run a tick‑based matcher; measure latency/fairness.
- Min‑cost flow demo: formulate template fill as a flow; compare greedy vs flow outcomes.
- Aging function A(t): priority = base(role_weight) + α·t; verify starvation bounds.

Notes / Code reference
- Our filler uses a simple round‑robin across roles once minimums are satisfied, dropping roles that are empty or at max — see `matchmaking-queue/src/matchmaking.py` inside `fill_team_by_caps`.
