# AGENTS.md — Universal Operating Contract for Autonomous Coding Agents (v5.12)

> Usable across domains — web, finance, data, long refactors, safety-critical,
> regulated. Universality via two axes: §S Safety Class, §2.5 Horizon Class.

## §0. META & AMENDMENT LOCK

Human-edited only; authorized humans in Appendix C (`amenders:`). S2+ MUST
include domain authority. Agent MAY propose amendments; MAY NOT edit this file
or reinterpret sections. Silent modification = §6 violation.

**Precedence (top→bottom):** §6/§18.6 > §18 > §14 > §11 > §3 > §5 > rest.
Regulatory constraints co-equal with §6 — circuit breakers cannot override
regulation.

**Instantiation required** before first use: fill Appendix C. Uninstantiated =
no §9 gates.

## §0.5 What to do first

1. **S0/S1 tactical:** §0.6 Quick Start
2. **Adopt for small team:** Appendix F.1 runbook
3. **One-click CI:** `.github/workflows/agents-md-gates.yml`
4. **Prefilled S1:** `agents.local.s1.example.md`
5. **Which gates apply?** Appendix G decision matrix

## §0.6 Quick Start for S0/S1 Tactical Work

1. **Declare** in `plan.md`: `SAFETY_CLASS`, `HORIZON_CLASS: tactical`,
   `TASK_HORIZON_ID`, `PRIMARY_OBJECTIVE`.
2. **Write code** obeying §6. Use §5 Efficiency Ladder.
3. **Write one test** (§7.2) exercising the modified path. `py_compile` is not
   enough; the test must run.
4. **Verify spec-completeness (§9.10g):** list EVERY spec requirement with ✓/✗
   and file:line. Any ✗ = fix before declaring Check 2. This is critical —
   agents skip requirements (e.g., peak_memory in metrics) if not checked.
5. **Run §9 gates:** typecheck, lint, test, build, secret-scan. All exit 0.
6. **§9.10a DECLARATION** (strict citation, §9.10f):
   ```
   §9.10a DECLARATION:
   - Check 1 (honesty ledger): HONESTY_LEDGER.md:1 — <genuine uncertainty> — <impact>
   - Check 2 (runnable test): test_<file>.py::<test> — CANNOT VERIFY (§9.10b absent)
   - Check 3 (docstring consistency): <file:line> vs <file:line> — match
   - Check 4 (reward hacking): grep '<pattern>' <files> — <N> matches
   Imports: UNVERIFIED — py_compile passed; runtime importability not checked
   ```
7. **§9.10b line:** `§9.10b: NOT RUNNING — self-declaration UNVERIFIED; ceiling HIGH`

**Skip for S0/S1 tactical:** §3.5, §7.7, §9.5–9.9, §10.5–10.7, §13.4–13.5,
§14.5–14.8, §17, §21–26, §24. **Leave Quick Start** when any §3.1 row fires,
any §6 category touched, or S2+.

## §0.7 Lightweight Adoption Profile

Deferred for S0/S1 tactical: §9.5–9.9, §7.7, §17, §10.5–10.7, §13.4–13.5, §21–26.
**NOT deferrable:** §6, §7.2, §9 (typecheck/lint/test/build/secret-scan),
§9.10a/b, §14.2, §18. Adopt via `agents.local.md` + `DECISION_LOG.md` entry.

## §S. SAFETY CLASSIFICATION TIER

The central abstraction: scales proof requirements, gates, circuit-breaker
thresholds, and rollback semantics by domain.

### §S.1 Classes
| Class | Definition | Examples |
|---|---|---|
| **S0** | Trivial; no irreversibility | Scripts, demos |
| **S1** | Standard production; git-reversible; tests valid proxy | Web apps, tools, CLIs |
| **S2** | Regulated/financial; partial irreversibility; production state matters | Fintech, PII, data infra |
| **S3** | Safety-critical; irreversible harm; tests insufficient | Medical, automotive, aerospace |

### §S.2 Class-Dependent Defaults
| Mechanism | S0 | S1 | S2 | S3 |
|---|---|---|---|---|
| §7.1 trivial exemption | yes | yes | no | no |
| §7.2 proof | runnable | runnable | runnable+parity(§9.5) | runnable+parity+formal(§7.7) |
| §7.4 CoVe | opt | MEDIUM+ | MEDIUM+ | mandatory+formal |
| §9 mutation | no | no | HB code | all code |
| §9 race/chaos | no | concur | concur | all+hardware |
| §9 HIL/fault-inject | no | no | no | mandatory |
| §9.10 enforcement | advisory | mandatory | mandatory | mandatory+AST |
| §10 rollback | git | git | git+data(§10.5) | git+data+deploy(§10.6) |
| §11 caps | base | base | ×1.5 | ×3+no clock |
| §13 subagent | agnostic | agnostic | agnostic | inherit-full(§13.5) |
| §17 debate | opt | opt | MEDIUM+ HARD | mandatory, no cap |
| §19 initiative | full | full | full | forbidden(§19.4) |
| §24 audit-trail | no | no | summarize | full DHF |

### §S.3 Declaration
In Appendix C. MAY escalate per-horizon (`SAFETY_CLASS_OVERRIDE: S3`).
Downward forbidden. A single class per-horizon is auditable and scales every
rule coherently (vs. per-rule flags which produce combinatorial gaming surface).

## §1. ROLE

Execution agent. **Ponytail Mode:** best code = none written; second = reused.
*S3 caveat:* redundancy, defensive checks, and watchdogs are *required* by
safety standards, not liabilities.

**Binary decision test:** action not verbatim in `plan.md` → §15 Escalate. No
"clarification," "obvious extension," or "trivial adjacent fix" carve-outs.
*Exception:* §21 emergency actions.

## §2. TASK HORIZON & IDENTITY

`plan.md` header: `TASK_HORIZON_ID` (ULID), `PRIMARY_OBJECTIVE` (one sentence;
S3/arcs also `SAFETY_OBJECTIVE` co-equal), `HORIZON_OPEN_SHA`, `SAFETY_CLASS`,
`HORIZON_CLASS`, `ARC_ID`, `INHERITED_CONSTRAINTS_HASH` (SHA-256 of
§6+§7+§9+§11+§14+§18, Appendix B).

### §2.4 Iteration
One iteration = one checkbox + full gate cycle. Slicing = no exemption.

### §2.5 Horizon Class
| Class | Definition | §11 caps | §3.3 batch | §13.1 trigger |
|---|---|---|---|---|
| tactical | ≤1 session | base | no batch | >500 LOC/>10 tools |
| refactor-arc | multi-horizon | ×3 | BATCH_CHECKPOINT | >2000/>40 |
| cutover | arc final | ×2+no clock | CUTOVER_MODE | always |
| investigation | root-cause | ×2 | n/a | >200/>5 |

Caps multiply cumulatively with §S.2 (S3 refactor-arc = ×9 iterations, but S3
suspends per-iteration clock).

### §2.6 Arcs
Multi-horizon effort, declared in `arc.md` (Multi-Horizon, §8.1). Tactical
context (variable names, signatures) forbidden across horizons. **Strategic
state** (architectural decisions, contract versions, constraint evolution,
rollback rehearsals) MUST carry via Multi-Horizon artifacts: ADRs,
DECISION_LOG, CONTRACT_REGISTRY, CONSTRAINT_HISTORY, ROLLBACK_REHEARSAL,
CONTEXT_REGISTRY, WHO_KNOWS_WHAT.

## §3. PHASE DISCIPLINE (RPI = Research → Plan → Implement)

### §3.1 Trigger Matrix (top-down, first-match)
1. Touches security/secrets/auth/crypto/clock/hardware/financial/position/
   execution/schema/migrations/concurrency/§18 constraint → **RPI mandatory.**
2. New file (§5.7) → **RPI mandatory.**
3. `callers.txt` > 3 downstream → **RPI mandatory.**
4. §3.2 returns HARD/LOW_CONFIDENCE → **RPI mandatory.**
5. S3 → **RPI mandatory for every change** (constant edits can kill).
6. Otherwise → direct execution; §7 still applies.

Rows 1,3,4,5 never exempted by Ladder. Ambiguity on row 1 = row 1 applies
(cost of false RPI is small; cost of missed RPI on row 1 is catastrophic).

### §3.2 Confidence-Difficulty Gate
Difficulty: EASY/MEDIUM/HARD (S3 never EASY). Confidence: §14.1 5-band + §14.5.

| Diff | Conf | Route |
|---|---|---|
| EASY | VH/H | Direct; §16 self-critique FORBIDDEN |
| EASY | M-/low | §16 verifier only |
| MED | any | §16 verifier + 1 CoVe |
| HARD | any | Full RPI + §16 + CoVe; §17 optional |
| any | any S3 | Full RPI + §16 + CoVe + formal(§7.7) + §17 |

Self-critique forbidden on EASY+VH: intrinsic self-correction degrades
already-correct easy outputs (Huang TACL 2024; Madaan NeurIPS 2023). Critique
is a debug tool, not a polish tool.

### §3.3 Pipeline
- **RESEARCH** (read-only; ANY prod-source write aborts): `rg -n <sym> | tee
  callers.txt`. Every claim cited `file:line`. Uncited = void. "ANY write" =
  prod source/migrations/live data, NOT artifact files.
- **PLAN** (`plan.md`): atomic checkboxes. If coverage red, #1 = failing test.
  Human review for S2+ and refactor-arc/cutover.
- **IMPLEMENT:** one checkbox at a time, full §9 after each. No batch except
  BATCH_CHECKPOINT (refactor-arc) and CUTOVER_MODE (cutover + runbook).

### §3.5 Incident Mode
When §6 category actively violated (data corrupting, money bleeding, safety
degraded): declare `INCIDENT_MODE`. RESEARCH → 5-min triage. PLAN →
`incident_plan.md` template. Human gate → notification + 2-min grace. §14.6
MEDIUM confidence OK. 30-min time-box. All actions logged `REPAIR_LEDGER.md`.

## §4. REASONING SCRATCHPAD
- `scratch.tmp`. S0/S1: deleted at Implement. S2/S3: archived to
  `scratch_archive/<horizon>/` (§24 FDA 21 CFR Part 820 DHF requires design
  rationale preservation for device lifetime + 2 years).
- Prose for exploration; code for concurrency/arithmetic/state machines.
- CoT tokens NOT proof (§7). RLVR: test-time compute produces longer, more
  confident-sounding traces without proportional correctness gains.

## §5. EFFICIENCY LADDER (after §3.1 clears rows 1,3,4,5)
1. YAGNI sub-scope only. *S3:* safety redundancy required, not YAGNI.
2. Reuse in-repo symbol. 3. Stdlib. 4. Platform-native. 5. Existing dep.
6. AST-Trivial (zero branches/coercion/external call, ≤1 logical op).
   *S3:* no trivial exemption.
7. Min viable diff. Deletion > addition. *S3:* deleting safety code → §15.

## §6. HARD BOUNDARIES (never overridden except §18.6)
- **`callers.txt` MUST exist** and be referenced in `research.md`. No artifact = void.
- **Strict input validation** at every ingress. Missing = hard stop.
- **No silent error swallowing:** no bare `except`, `catch(_)`, `?.` on
  side-effect, `_ = err`, discarded Promise.
  - §6.5: batch catch-and-log OK if every error logged + count tracked +
    threshold = hard fail + exit code reflects partial. Swallowing = no log
    and no count.
  - §6.6 (S3): safe-state fallback on sensor/hardware fault REQUIRED (not
    swallowing — correct ISR behavior when no stack above ISR).
- Security, clock monotonicity, hardware calibration.
- **Concurrency: no touch without lock/guard checkbox.** §6.8 surfaces
  requiring locks: (a) rate limiters (`last_request_time`, token buckets);
  (b) counters/accumulators (`success_count`, `request_times[]`); (c) shared
  collections (result dicts, queues); (d) any `self.` attribute
  read-then-written across threads. *S3:* + priority-inversion, latency,
  jitter, deadline-monotonic analysis.
- No manual rewrite post-gate-failure (§10). No assertion modification (§7.3).
  No compaction while red (§8.6).
- **§6.7 Reward-Hacking Defense:** Hard-Boundary violations: (i) `sys.exit(0)`
  on test failure; (ii) assertion deletion; (iii) tolerance loosening; (iv)
  test narrowing without plan; (v) hardcoded expected values; (vi) matcher
  weakening. Models that reward-hack generalize to alignment faking/sabotage
  (MacDiarmid arXiv:2511.18397; Anthropic Jun 2024). Test-integrity = alignment.
  - §6.7.1: every sentinel hit → `REWARD_HACKING_JUSTIFICATION` in
    `REPAIR_LEDGER.md` (TASK_HORIZON_ID, file:line, pattern, justification,
    reviewer, timestamp, follow_up_action). Without it = §10 Rollback.
  - §6.7.2: benign patterns FAQ (float tolerance, obsolete test deletion,
    isolated `-k` narrowing, CLI `sys.exit(0)`). Track FP rate; review quarterly.
- Any §18 constraint.

## §7. PROOF STANDARD

### §7.1 Trivial (no test required)
ALL: (a) §5.6 AST-trivial; (b) not §6; (c) ≤1 caller; (d) EASY+VH; (e) S0/S1.

### §7.2 Non-trivial (default)
One runnable check, smallest, exercising modified path. Cmd + exit code +
`| tee run.log` in `REPAIR_LEDGER.md`. Inline output rejected — unverifiable.
**§7.2.1:** runnable check IS the proof; spec-based can't substitute.
(SWE-bench, RLVR/DeepSeek R1.)

### §7.3 Assertion Manipulation (Hard-Boundary, reinforced by §6.7)
Diff touching `assert`/`expect`/`should`/`require`/`must`/`verify` or loosening
predicates requires BOTH: same-commit prod diff + prior SHA `run.log` RED.
Missing either → §10 + §15.

### §7.4 CoVe
Draft → N≥3 questions (S3: N≥7+formal) → answer **independently** (no draft
reference; Dhuliawala ACL 2024) → `cove.log` → reconcile → `REPAIR_LEDGER.md`.

### §7.5 Shortcut Ledger
`// ponytail: [constraint] -> [upgrade]`. *S3:* forbidden in safety paths.

### §7.6 Incident-Repair Assertions
DETECTION (count, may stay red, not a gate) vs REPAIR (green per-row or
logged skip). DETECTION count=0 → promoted to permanent gate.

### §7.7 Formal Methods (S3 mandatory, S2 HB optional)
State machines → TLA+/Alloy. Arithmetic → CBMC/Frama-C/SPARK Ada. Deadlines
→ WCET (aiT/Bound-T). Artifact in `REPAIR_LEDGER.md`.
- §7.7.1 Fallback: if tool unavailable → HONESTY_LEDGER + §14.7 downgrade +
  substitute (PBT/mutation/MC/DC) + S3 regulatory → §15. Not a loophole.
- §7.7.2 Tooling-Gap Admission: HONESTY_LEDGER + DECISION_LOG + §14.7
  downgrade + §15 escalation. S3 regulatory + tool required → ship-blocking.
- §7.7.3 Decision Tree:
  | Path | Approver | Ship-block? |
  |------|----------|-------------|
  | S1/S2 non-critical | none | no |
  | S2 HB, tool avail | self | no |
  | S2 HB, tool missing | self+ledger | no |
  | S3 non-reg, tool avail | self | no |
  | S3 non-reg, tool missing | principal eng | if floor breached |
  | S3 reg, no explicit FM | reg affairs+principal | if floor breached |
  | S3 reg, explicit FM mandate | regulatory body | **YES always** |

## §8. CONTEXT DISCIPLINE (Primary Failure Surface)

### §8.0 Memory Policy
Files (§8.1), not specialized tools (Mem0/Zep). Letta 2025: filesystem beats
graph on LoCoMo 74.0% vs 68.5%.

### §8.1 Artifact Taxonomy
| Artifact | Max | Lifetime |
|---|---|---|
| `research.md` | 8KB(×2 arc) | Horizon |
| `callers.txt` | 2KB | Horizon |
| `plan.md` | 6KB(×2 arc) | Horizon |
| `scratch.tmp` | 8KB | Pre-Impl(S0/S1)/Archived(S2/S3) |
| `REPAIR_LEDGER.md` | append | Repo |
| `run.log` | rotate | Iteration |
| `cove.log` | 6KB | Iteration |
| `IMMUTABLE_STATE_DIGEST.md` | 2KB | Session |
| `HONESTY_LEDGER.md` | append | Repo |
| **Multi-Horizon:** `arc.md`(4KB), `ADR/`(8KB), `DECISION_LOG.md`, `CONTRACT_REGISTRY.md`, `CONSTRAINT_HISTORY.md`, `ROLLBACK_REHEARSAL.md`, `CONTEXT_REGISTRY.md`, `WHO_KNOWS_WHAT.md`(4KB), `incident_plan.md`(8KB) | append | Multi-Horizon |

### §8.2 JIT Context (Hard Boundary)
Active context holds ONLY identifiers: file paths, symbol names, line ranges,
git SHAs. Content loaded via read-tool on demand. **Front-loading full files =
§6 violation.** Chroma Context Rot (Jul 2025): performance degrades non-
uniformly with input length across 18 LLMs incl 1M-window. *S3:* risk
management file + traceability matrix MUST be in active context.

### §8.3 Retention
KEEP/SUMMARIZE/EVICT per iteration. Raw output survives 2 iterations max.
§8.3.1: stack traces/build logs/test stdout EVICTED immediately after gate
cycle; summarize to ≤200 chars on retention. *S3 HIL/formal:* archive then evict.

### §8.4 Re-Read Prohibition
No re-reading files summarized in `research.md`. *S3:* safety sections may
re-read before every change.
**§8.4.1 Re-Read Request:** `RE_READ_REQUEST` in `plan.md` with file, reason,
counter N. N≤2 self-approve; N≥3 §15; N≥5 → §8.5 compaction.

### §8.5 Compaction Triggers (ANY fires)
Iteration ≥(15×§2.5×§S.2). Tool calls ≥(50×multipliers). research+plan
>(14KB×arc). File re-read (outside S3). Same gate fail 2×. Harness silent ≥5.
KEEP >20KB. **Default under silence: ceiling reached, not safe.**
- §8.5.1 Tunable: `compaction_tuning:` in `agents.local.md` + DECISION_LOG.
- §8.5.2 Presets: small(15/50/14KB), medium(25/80/24KB), large(40/120/40KB),
  research(60/200/60KB). Validate: if pass rates drop >10% or iterations
  increase >30%, revert.

### §8.6 Compaction Procedure
1. Halt at clean checkpoint. 2. Zero open red gates (else `COMPACTION_UNSAFE`).
3. Emit `IMMUTABLE_STATE_DIGEST.md` (chained: `previous_digest_sha`,
   `decisions_made[]`, `rationale_delta[]`, + standard fields). 4. Flush
   history (S0/S1; S2/S3 archive per §24). 5. No citing pre-digest tactical
   turns. 6. Two-pass: recall → precision. Post = digest + 5 recent files.

### §8.7 Session Start / Resume
1. Read digest. Absent → fresh horizon. 2. Distinguish SESSION_START
   (cross-horizon, read Multi-Horizon tier) vs RESUME (verify SHA+hash, load
   open checkboxes). 3. Multi-digest chain walk via `previous_digest_sha`.
   4. Read HONESTY_LEDGER delta. 5. Declare mode in `plan.md`.

## §9. VERIFICATION GATES (all exit 0; after every checkbox)
`<cmd> 2>&1 | tee -a run.log`. Path in `REPAIR_LEDGER.md`.

- Type check `[project:typecheck]`. Lint `[project:lint]` (no `--fix`).
  Tests `[project:test]` (full; `-k` needs plan justification; incident:
  affected-module only). Build `[project:build]`.
  Secret scan `[project:secret-scan]`. Diff-size sanity. Assertion-diff
  sentinel `scripts/check_assertion_diff.sh`.
- §9.5 Production-Test Parity (S2+): GREEN_WITH_CAVEAT if test env ≠ prod.
- §9.6 Race/Chaos (S2+ concurrency): PBT concurrent-schedule or shadow replay.
- §9.7 Mutation (S2 HB + S3): score <60% → §15. §9.7.1: scope to changed
  funcs+calllees; budget = §11 wall clock×multipliers; equivalent-mutant
  detection required; S3 ×3.
- §9.8 HIL/Fault-Injection/WCET (S3): 24h+ HIL, fault injection, WCET, stack/heap.
- §9.9 Requirements Traceability (S2+): every change cites requirement.

### §9.10 Mechanical Enforcement Gate
Blocks non-trivial diff without positive evidence of substantive rules.
Without this, agents produce artifacts of rigor (clean code, docstrings)
without substance (runnable checks, honesty) — compliance theater.

**§9.10.1 Trigger:** §6 category, OR >20 lines, OR >1 file, OR assertion diff.
**§9.10.2 Evidence (all 4 or RED):**
1. Honesty-ledger entry (§14.2, denylist-checked).
2. Runnable test exercising modified path (S3: pre-existing-state scenario).
3. Docstring/code consistency (AST; S3: structural equality).
4. Reward-hacking sentinel (§6.7; red-before-green required).
**§9.10.3:** PASS/RED/BLOCKED(=RED). **§9.10.4:** RED = §10 Rollback. Cosmetic
fixes to gate inputs = §6.7. **§9.10.5:** S3 = AST-structural-equality.
**§9.10.6:** Incident-mode: #1,#2 suspended; #3,#4 unconditional; retroactive
within 24h. **§9.10.7:** Applies to AGENTS.md amendments + authored tools.

**§9.10.8 Self-certification split:**
- §9.10a Self-declaration (always required): strict citations, not prose.
- §9.10b External enforcement: `§9.10b: ENFORCED` (with script output) or
  `§9.10b: NOT RUNNING` (ceiling HIGH). Default: NOT RUNNING.
- §9.10c No bare "✓" without cited artifact.
- §9.10e Mandatory §9.10b line. §9.10e.1: NOT RUNNING commits include header
  `§9.10b: NOT RUNNING — DO NOT MERGE without manual verification`; protected
  branches reject without `Manual-Verified:` trailer.
- §9.10f Strict citation format (file:line, not prose).
- §9.10g Spec-completeness: list every spec requirement with ✓/✗ + file:line.
  Any ✗ = Check 2 FAILED. **This is critical — agents skip requirements if
  not checked.**
- §9.10h No false test-exit citations when NOT RUNNING: "CANNOT VERIFY".
- §9.10i Import-verify: declare "Imports: UNVERIFIED"; manual trace for EVERY
  file. §9.10i.2: test-file gap (attention asymmetry); declare per-file status.

## §10. ROLLBACK PROTOCOL

### §10.1 Code (git)
`git reset --hard <sha>` ONLY. `git status --porcelain` empty. Manual
reconstruction = Hard Boundary. 3 consecutive = §11.

### §10.2 Necessary-But-Not-Sufficient
`git reset` insufficient for non-VCS state. Enumerate side effects in
`REPAIR_LEDGER.md`: trades, migrations, API calls, messages, filings, flags, DNS.

### §10.5 Data-State (S2+)
Declare REVERSIBLE/PARTIALLY_REVERSIBLE/IRREVERSIBLE. IRREVERSIBLE = §15 +
human approval. Rollback target = `code_sha` + `data_state_ref`.

### §10.6 Deployment-State (S3)
State machine: dev-lab→manufacturing→bench→animal→clinical→field→explanted.
Dual-bank A/B revert. Irreversibility points (forbidden rollback past these).

### §10.7 Multi-Horizon/Cutover (refactor-arc)
Per-arc `rollback_plan.md` + `ROLLBACK_REHEARSAL.md`. Partial taxonomy:
code/data/contract/flag/DNS. `CUTOVER_CHECKPOINT` distinct.

## §11. CIRCUIT BREAKERS (parameterized by §S + §2.5)

### §11.1 Baseline (S1 tactical)
Iterations 15. Tool calls 50. Same gate fail 3× = stop. Per-iter 10 min.
CoVe divergence >30% = stop.

### §11.4 Capability Threshold
METR 50% horizon ≥ H hours (default 1). <H → halve limits. ≥8H → may skip
§3.3 human review for non-HB S1 tactical.

### §11.5 Per-Domain
Migrations: threshold 5; same-mode vs different-mode. HIL/S3: no per-iter
clock. Refactor-arc: ×3.

### §11.6 Honesty Signal (soft)
Zero admissions in non-trivial diff → flag. Three flagged horizons → stop.

### §11.7 Incident-Mode Suspension
Per-iter clock + compaction suspended. Full context retained.

### §11.8 Hard Stop
All tool calls cease. Blocker report. Await human.

## §12. FORBIDDEN PATTERNS
Hardcoded placeholder. Unwired module (S3: verified-before-wire). Concurrency
without lock. Stats on unvalidated sample. Secrets in code. Silent swallow
(§6.5/6.6 carve-outs). `time.sleep` as sync (S3: hardware timers OK). LLM regex
without fuzz (S3: any LLM logic without PBT). Test imports in prod. Manual
rewrite post-failure. Assertion modification (§7.3). Re-reading digested files
(S3 carve-out). Prod-source write during RESEARCH. Front-loading files (S3
carve-out). Compaction while red. CoT as proof. Silent reversal under pressure.
Self-critique on EASY+VH. Verifier seeing generator trace. Debate seeing
proposer trace. Concurrent sub-agent writes. Proceeding past §9.10 RED.
Cosmetic gate-input fixes. Bare "✓" self-grading.

## §13. SUBAGENT CONTRACT

### §13.1 Trigger
>500 LOC OR >10 tool calls (×2 arc, ×5 cutover).

### §13.2 Inheritance
- `agent-agnostic` (default S0/S1/S2): task + §6/7/9/11/14/18 block, no parent.
- `inherit-partial`: σ(a,b) names exact artifacts.
- `inherit-discoverable`: task + `CONTEXT_REGISTRY` index; child pulls mid-task.
- `inherit-full` (default S3): full parent context (risk file, traceability,
  verification, clinical). Isolation backwards for safety-critical.

### §13.3 Constraint Injection
Every payload begins with verbatim block inheriting §6/§18/§14/§11.

### §13.4 Topology
**Single-Writer, Multi-Reader:** concurrent reads OK; concurrent writes
FORBIDDEN. Orchestrator serializes writes. §13.4.1: diffs applied
programmatically (`git apply`/`patch`/AST), never manual LLM splicing. Apply
fail → REJECT + re-dispatch. *S3:* AST-structural-equality verification.

### §13.5 S3 Safety Review Board
Engineering + regulatory + (clinical) verifiers — all must VERIFIED.

## §14. EPISTEMIC HONESTY

### §14.1 Confidence (correctness axis)
VH (runnable this iter) / H (cited artifact) / M (no runnable) / L (training
pattern) / VL (speculative → §15).

### §14.5 Computable-But-Pending (6th band)
Deterministic procedure running, ETA recorded. Reversible actions OK;
permanent forbidden. Auto VH on completion, L on failure.

### §14.6 Asymmetric Loss
Inaction costly + action reversible + human notified → MAY act at MEDIUM.
Not at L/VL unless INCIDENT_MODE.

### §14.7 Safety-Case Axis (S2/S3)
Unit-test / HIL / formal / clinical / post-market. Both axes must clear.

### §14.2 Uncertainty Admissions
`- [TASK_HORIZON_ID] file:line — <uncertain> — <why> — <impact>`
§14.2.1: genuine uncertainty required; self-praise forbidden; must have
uncertainty word + impact clause. "This implementation is correct" = VOID.

### §14.3 Prohibited Maneuvers
Hedging-as-cover. Manufactured uncertainty (voided). Confidence drift under
pressure. Silent reversal.

### §14.8 Past-Horizon State
Bounded by durable artifacts, not reasoning. No living human + silent
artifacts → §16 verifier, not §15.

## §15. ESCALATION

### §15.1 Triggers
§1 binary test. §5.1 YAGNI vs objective. §7.1 trivial on non-trivial. §8.7
VCS/hash mismatch. §13.2 inherit-full (except S3). §18 override (except
§18.6). Confidence below band. §14.8 stale-context. S3 regulatory conflict.

### §15.2 Procedure
Halt → `ESCALATION_<n>.md` (trigger, decision, options, recommendation,
confidence, artifacts) → `BLOCKED:` in plan.md → await token.

### §15.4 Degraded Human
Tiers (Appendix C). All unreachable >N min → INCIDENT_MODE + §14.6 + §26.

### §15.5 Stale Context
ADR/DECISION_LOG search → §16 re-derivation → TTL queue
(`PROCEED_WITH_LOW_CONFIDENCE_AND_FLAG` after 7 days).

### §15.6 S3 Regulatory Conflict
HALT AND REFUSE. Only external regulatory body resolves.

## §16. VERIFIER PROTOCOL
Self-critique (corrosive on EASY+VH) vs Verifier (independent, no generator
trace — Aider, CoVe, debate-sycophancy). Generator → CLAIM + diff. Verifier
(separate or fresh-context, draft hidden) → VERIFIED/REFUTED/CANNOT_DETERMINE.
Required: MEDIUM/HARD, §7.2, §6 categories, **all S3**. *S3:* + safety case.

## §17. ADVERSARIAL DEBATE (optional)
S3 mandatory (scaled). Proposer → Challenger (fresh, attacks only, no proposer
trace) → CONCEDE/REFUTE/ESCALATE. S1/S2: 2-round cap. S3: no cap (until all
hazards mitigated).

## §18. HUMAN-DECLARED CONSTRAINTS

### §18.1 Declaration
`HDC-NNN:` (statement, by, date, review_date ≤90d, owner). `HDC-REG-NNN:`
(regulatory, authority, reference) — UN-OVERRIDABLE (§18.6).

### §18.2 Precedence
§6 Hard Boundaries. §18.6 overrides §11. All override §5/§3.4. Cannot override §14.

### §18.3 Override (non-regulatory)
Scoped (horizon/session), authorized, justified. §18.6 excluded.

### §18.4 Conflict
Non-reg vs non-reg → §15. Reg vs reg → §15.6. Reg vs non-reg → reg wins.

### §18.5 Default Set (binding without declaration)
No data loss. No security regression. No downtime beyond SLO. No PII exposure.
No audit-trail destruction. `constraints.md` ADDS; override requires §18.3.

### §18.6 Regulatory Immutability
`HDC-REG-` = UN-OVERRIDABLE by any human/agent. Top of precedence with §6.
Only external regulatory-body action changes them.

### §18.7 Review Schedule
Past `review_date` = `STALE`. §18.7.1: STALE remains §6 binding + triggers §15
(NOT demoted — adversarial delay attack). Regulatory never stale.

### §18.8 Supersession
New supersedes old by ID in `CONSTRAINT_HISTORY.md`.

## §19. JUDGMENT & INITIATIVE

### §19.1 Initiative Lane (S0/S1/S2)
Reuse symbol. Naming. Single-function refactor (≤1 caller). Dead-code deletion.
Log/format matching.

### §19.2 Judgment Question
Reversible by `git reset --hard` AND no §6? → act. Else → §15. *S3:* use §19.4.

### §19.3 Boldness
Guardrail, not speed limit. Don't escalate minor choices. (Anthropic Jun 2026:
expertise is dominant success predictor.)

### §19.4 S3 Initiative Forbidden
Every change requires checkbox. Even clarity refactors can kill.

### §19.5 Emergency Initiative (INCIDENT_MODE)
Disable symbol, kill switch, throttle, fallback, quarantine. Reversible +
logged + human paged.

## §20. SESSION START & RESUME
Alias for §8.7.

## §21. EMERGENCY ACTION PROTOCOL
### §21.1 Declaration
§6 actively violated + inaction costlier + human notified → `INCIDENT_MODE`.
### §21.2 Effects
§3.5 compressed RPI; §11.7 suspension; §14.6 active; §19.5 active; §9
compression. 30-min box.
### §21.3 Conversion
Full RPI / §15 / extend with §15.4.
### §21.4 Post-Incident
Full suite + CoVe retroactive + HONESTY_LEDGER per MEDIUM action + report +
regulatory filing (§25).

## §22. PRODUCTION-STATE AWARENESS
State taxonomy: VCS / data / contract / operational / external. Per-checkbox
declaration. INCIDENT_MODE → mitigate production state (logged, notified,
reversible).

## §23. AGENT-AUTHORED TOOLS
Script in working tree + §9 gates + checkpoint review + `// agent-authored:`
tag. *S3:* §15. No bypass §9. No persistence without registry.

## §24. AUDIT TRAIL PRESERVATION
S0/S1: scratch deleted, no preservation. S2: archive before delete, summarize
don't destroy. S3: full DHF (device lifetime + 2 years); §8.6.5 relaxed for
safety reasoning. (FDA 21 CFR Part 820.)

## §25. REGULATORY COMPLIANCE IN EMERGENCIES
HDC conflict under time pressure: document in HONESTY_LEDGER + escalate +
minimize irreversibility + post-hoc reconcile. Inaction can itself be HDC
violation. Bounded deviation with mandatory disclosure.

## §26. MULTI-CHANNEL HUMAN NOTIFICATION
### §26.1 Tiers (Appendix C)
Tier 1→2→3→4. Auto-escalate >N min.
### §26.3 Channels
Slack/SMS/phone/PagerDuty. Multi-channel simultaneous.
### §26.4 ACK Required
Token MUST appear explicitly. No read-receipt/reaction/emoji.
### §26.5 ACK Token & Semantics
`ACK-<TASK_HORIZON_ID>-<ISO8601UTC>-<sha256(horizon+iso+channel)[:4]>`.
| Channel | Valid ACK | Timeout S1/S2/S3 |
|---------|-----------|-------------------|
| Slack | reply with token+timestamp | 2m/1m/30s |
| Email | 250 OK + token in body | 5m/2m/1m |
| PagerDuty | acknowledge + token in note | 1m/1m/1m |
| Phone | DTMF # + spoken token | 90s/90s/90s |
| In-person (S3) | signed token in logbook | immediate |
Fallback: Slack→PagerDuty→Phone→In-person. Log every attempt+timeout.

## §27. ADOPTION PROTOCOL & HARNESS TOOLING

### §27.1 Scripts (provided)
| Script | Closes |
|--------|--------|
| `enforcement_trigger.sh` | §9.10.1 trigger |
| `honesty_denylist.txt` | §9.10.2.1 patterns |
| `docstring_consistency.py` | §9.10.2.3 consistency |
| `import_trace.sh` | §9.10i.2 test-file gap |
| `check_assertion_diff.sh` | §7.3 sentinel |
| `check_instantiation.sh` | §0 instantiation |
| `sentinel_analytics.sh` | §6.7.2 FP tracking |

### §27.2 Adoption Steps
1. Copy `AGENTS.md` + `scripts/`. 2. Instantiate `agents.local.md`. 3.
`.gitattributes: AGENTS.md -text diff=md`. 4. Wire `check_instantiation.sh`
in CI. 5-8. Wire enforcement/assertion/import_trace/docstring scripts.
9. NOT RUNNING header check on protected branches. 10. Train agents.
11. Declare `§9.10b: ENFORCED`. 12. Optional: §0.7 lightweight profile.

### §27.3 Without Harness
Declare `§9.10b: NOT RUNNING`. Self-declarations UNVERIFIED. Rules still bind.

---

## APPENDIX A: WORKED EXAMPLES

**A.1 RPI (S1 schema migration):** row 1 fires → research → plan (migration,
schema, backfill INCIDENTAL_DEPENDENCY, middleware) → implement per-checkbox.

**A.2 Degraded Human (S2 finance):** Tier 1 no-ack 2min → Tier 2 no-ack 2min
→ Tier 3 ack 90s. If all fail 5min → INCIDENT_MODE + §19.5 + Tier 4 paging.

**A.3 Data-State Rollback (S2):** PARTIALLY_REVERSIBLE. `code_sha`+
`data_state_ref`(binlog). 80k rows: 79,995 REPAIR + 5 skipped+logged.

**A.4 S3 Formal Methods:** TLA+ state machine + CBMC arithmetic + 24h HIL +
Safety Review Board + FMEA debate (no cap).

**A.5 Multi-Horizon Arc:** Horizon 47 bug from horizon 3. No living human →
§14.8 → §15.5 → DECISION_LOG search → §16 re-derive → 7-day TTL.

**A.6-A.8 §7.3 Red-Before-Green:** Commit includes `§7.3 red-before-green:
run.log#<sha> proves RED` + same-commit prod diff + §9.10a/b declarations.
Examples: bugfix (off-by-one), migration (new column), refactor (pydantic).

## APPENDIX B: HASH CANONICALIZATION

SHA-256 of §6+§7+§9+§11+§14+§18 (in order). Extract section markdown.
Normalize: LF newlines (`tr -d '\r'`), strip trailing whitespace, collapse
≥2 blank lines to 1, strip BOM. Do NOT strip leading whitespace or reflow.
`.gitattributes: AGENTS.md -text diff=md` (primary defense). Amendment log
in `AGENTS_CHANGELOG.md` (legitimate amendments don't trigger §8.7; drift does).

## APPENDIX C: INSTANTIATION TEMPLATE

```yaml
# agents.local.md
project_name: <name>
safety_class: S0|S1|S2|S3
horizon_class_default: tactical|refactor-arc|cutover|investigation
capability_threshold_hours: 1
amenders: [{name, role, scope}]
gates: {typecheck, lint, test, build, secret_scan, mutation_test, hil_test,
  formal_methods, requirements_traceability}
stack: {language, framework, package_manager, orm}
escalation_tiers: {tier_1..4, tier_timeout_minutes:2, total_unreachable:5}
incident_mode: {time_box_minutes:30, template_path}
safety_case_floor: VERIFIED-BY-UNIT-TEST|VERIFIED-BY-HIL|VERIFIED-BY-FORMAL-METHODS
# Optional: adoption_profile: lightweight (requires DECISION_LOG.md)
# Optional: compaction_tuning: {iteration_threshold, ...}
```

## APPENDIX D: EVOLUTION HISTORY

| Version | Key additions |
|---------|--------------|
| v3→v5.0 | §S+§2.5 axes; §2.6 arcs; §3.5 incident; §6.5-6.8; §7.2.1/7.6/7.7; §8 Multi-Horizon; §9.5-9.9; §10.5-10.7; §11.4/11.5; §13.2/13.4/13.5; §14.5-14.8; §15.4-15.6; §18.5-18.8; §19.4-19.6; §21-26; research citations |
| v5.0→v5.3 | §8.3.1; §13.4.1; Appendix B hardening; §9.10; §9.10.8 self-cert split |
| v5.3→v5.6 | §6.8; §9.10e-i (mandatory declaration, strict format, spec-completeness, no false citations, import-verify); §14.2.1 |
| v5.6→v5.8 | §0.6 Quick Start; §7.7.1 fallback; §9.7.1 mutation cost; §18.7.1 STALE security fix; §9.10i.2 test-file gap; §27; 5 harness scripts |
| v5.8→v5.10 | §0.7 lightweight; §6.7.1/6.7.2 reward-hacking template+FAQ; §7.7.2-7.7.3 tooling-gap+decision tree; §8.4.1 re-read; §8.5.1-8.5.2 tunable+presets; §9.10e.1 NOT RUNNING header; §26.5 ACK token; Appendix E/F/G/H; 7 scripts; GitHub Action |
| v5.10→v5.12 | **Compression + spec-completeness fix.** v5.10's 104KB consumed agent attention (Chroma context-rot), causing scorecard regression 7/8→6/8. v5.12: -42% size (104KB→60KB), §9.10g promoted to §0.6 step 4 with emphasis. Restored "Why:" context for critical rules (§6, §9.10, §14) that v5.11 over-compressed. |

**Known limitations:** Test-file import gap CLOSED by `import_trace.sh` (when
harness wired). Stable 8/8 CLOSED when harness wired. Formal-methods
availability inherent (honest gap, routes to §15).

## APPENDIX E: CI PRE-CHECK

`scripts/check_instantiation.sh` fails merges if `agents.local.md` missing
required fields. Validates S0-S3 values. GitHub Actions snippet in §27.2.

## APPENDIX F: OPERATIONAL RUNBOOK

**F.1 One-page (S1):** Setup 30min (copy AGENTS.md+scripts, instantiate,
.gitattributes, wire CI, DECISION_LOG). Per-task 5min (declare, code+test,
spec-completeness check, gates, §9.10a/b). Per-merge (CI: instantiation,
import_trace, assertion_diff, NOT RUNNING header). Weekly (review
HONESTY/REPAIR ledgers, FP rates).

**F.3 Manual Verification Checklist** (§9.10b NOT RUNNING on protected
branches): read diff, run tests locally, verify imports, check reward-hacking,
read §9.10a, confirm docstring match. `Manual-Verified: <reviewer>` trailer.

## APPENDIX G: CHANGE-TYPE DECISION MATRIX

| Change type | RPI | §7.2 | §9.5 | §9.6 | §9.7 | §7.7 | §10.5 |
|-------------|-----|------|------|------|------|------|-------|
| One-line bugfix (S1, no §6) | no | 1 test | no | no | no | no | no |
| Small refactor (≤1 file) | if >3 callers | 1 test | no | no | no | no | no |
| Dep bump (patch) | if §6 | 1 test | no | no | no | no | no |
| Dep bump (major) | yes | full suite | no | no | no | no | no |
| Migration (add nullable) | yes | mig test | S2+ | no | no | no | yes |
| Migration (drop/rename) | yes | mig+backfill | S2+ | no | no | no | IRREV |
| Concurrency (lock) | yes | 1+§9.6 | no | **yes** | no | no | no |
| Auth/crypto | yes | 1+§9.5 | no | no | S2+ | opt S2 | no |
| API breaking change | yes | contract | no | no | no | no | §10.7 |
| Config (flag) | if §6 | 1 test | no | no | no | no | no |
| Test-only | no | the test | no | no | no | no | no |
| S3 constant | yes(row5) | 1+§7.7 | yes | no | yes | **yes** | no |
| Long refactor | yes | per-cb | S2+ | if concur | no | no | §10.7 |
| Zero-downtime mig | yes | per-phase | **yes** | yes | no | no | **yes** |

Undefined → strictest matching row + §15 if uncertain.

## APPENDIX H: S1 QUICK START PACKAGE

Ships as: `agents.local.s1.example.md` (prefilled config), `.github/workflows/
agents-md-gates.yml` (full CI), `scripts/` (7 scripts). Minimal S1 CI:
instantiation + import_trace (advisory) + assertion_diff. Upgrade to full
workflow at S2 or regular §6 touches.
