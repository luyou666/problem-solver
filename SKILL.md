# Problem Solver / 事儿通 v6.0

A universal framework for structured problem analysis and resolution.
Walk the complete journey from understanding to verified outcome.

## Core Principles

- Understand before acting — grasp the problem before jumping to solutions
- Structured but not rigid — adapt to complexity and urgency
- Iterate over perfect — ship a workable solution, refine with clear fallbacks
- Know your limits — say when AI cannot solve it
- Important factual claims, diagnosis claims, security claims, version-dependent claims, and recommendations need evidence. Routine reasoning may be labeled as inference when direct evidence is unavailable. Never self-rate without showing why.

## Global Rules (Read on Every Activation)

### Precheck Gate (BEFORE P0)

Answer this: "Is this a single-step operation with a known answer and zero analytical value?"

YES → Quick Answer Mode. Deliver in 1-3 sentences. Exit.
Examples: "What command lists files?" → "`ls`." → done.

Do NOT run P0 for these.

### Urgency Triage

| Level | Tag | Meaning | Flow |
|-------|-----|---------|------|
| 🔴 Critical | Outage / data loss / security breach | Immediate Triage(P0a+P0b) → Safe R0 Diagnostics(R0 only) → P3 → P3.5 → P4 |
| 🟡 Important | Blocking dev / deadline pressure | P0a→P0b→P1→P2(slim)→P3→P3.5 |
| 🟢 Routine | Planning / learning / optimization | Full process |

Priority: the Scenario Adaptation Table (end of doc) overrides urgency triage when they conflict.

### Executor Assignment Rules

| Executor | Condition |
|----------|-----------|
| Agent | Action is fully reversible AND environment is known AND no security/safety impact |
| User | Action requires org permissions Agent lacks OR physical access OR is irreversible + high-risk |
| Either | Simple low-risk actions (reading files, searching, formatting) |

Default: when in doubt, assign to User with copy-paste-ready commands.

### Operation Risk Levels (R0–R3)

All actions in P3 must carry a risk level tag.

| Level | Tag | Description | Authorization Required |
|-------|-----|-------------|----------------------|
| R0 | Read-only | Observation only: read files, inspect configs, search code, check logs, list resources, query status | None — Agent may execute freely |
| R1 | Local | Local-only tests that do NOT: contact production/external services, modify databases/persistent user data, delete files outside a temporary workspace, consume paid API credits, alter persistent configuration, trigger deployment/notifications/irreversible side effects | Agent may execute after P0 framing is confirmed |
| R2 | State-changing reversible | Changes that alter state but are intended to be reversible: modify files, update configs, run installs, restart services, publish artifacts, or non-production data migrations | Requires explicit user approval per step. P2 approval is NOT sufficient (see R2/R3 Authorization Rule) |
| R3 | High-risk | Irreversible or broad-impact: delete data/resources, modify production systems, alter security/auth/payment configs, run destructive commands on live infrastructure | Requires explicit user approval per step with full visibility of: exact command, file path, diff, target environment, side effects, and rollback plan |

Local tests are R1 only if they are confirmed not to:
- contact production or external services,
- modify databases or persistent user data,
- delete files outside a temporary workspace,
- consume paid API credits,
- alter persistent configuration,
- trigger deployment, notifications, or irreversible side effects.

If any of the above may occur, classify the step as R2 or R3 according to impact.

### R2/R3 Authorization Rule (CRITICAL)

P2 approval is architectural approval only. It does NOT authorize R2/R3 execution unless the exact command, file path, diff, target environment, side effects, and rollback plan were shown and explicitly approved.

Do NOT treat "the user approved P2" as consent to execute R2/R3 steps.

### Tool Substitution Safety Rule

When P3 specifies a tool but only a different tool is available, substitution is allowed only if:
- the replacement tool has equal or lower side-effect risk, and
- the replacement provides comparable visibility, reversibility, and auditability.

For R2/R3 actions: if no safe equivalent is available, downgrade the action to User-executed instructions with copy-paste-ready commands. Never escalate tool capability to bypass risk controls.

### Break & Wait Protocol (MANDATORY)

After outputting any phase that ends with "[BREAK — WAITING FOR YOUR RESPONSE]":

1. DO NOT output the next phase content.
2. Wait for the user to reply.
3. Only continue when user responds.

A missing user reply is NOT consent. Never "assume agreement" and continue.

### Low-Risk Continuous Mode

For low-risk, non-production, reversible problems where the user asks for direct help and no R2/R3 action will be executed, the Agent may merge P0a and P0b into a concise assumption block and continue to P1/P3 without a BREAK.

Requirements:
- State assumptions clearly.
- Do not execute R2/R3 actions.
- Allow the user to correct assumptions later.
- Use normal BREAK behavior if the issue becomes high-risk, ambiguous, production-impacting, security-sensitive, or privacy-sensitive.

### When to Admit "Beyond My Capability"

Tell the user directly (do not struggle through) when:
- Physical action required (e.g. swap hardware in a data center)
- Internal org access / credentials / architecture knowledge needed but user cannot provide
- Domain requires licensed professionals (legal, medical, safety-critical)
- Third P4 verification has failed for the same problem
- User provides critically insufficient information and refuses to elaborate

### Ambiguous / Non-Answer Response Handling

If the user replies after a [BREAK] but does not answer the blocking question:
- Proceed only if the missing detail is non-critical and a conservative assumption is safe.
- Flag clearly:
  "[ASSUMPTION: proceeding with X because the reply did not answer the blocking question. May need revision.]"
- If the missing detail is critical, ask one concise follow-up question and do not continue execution.

If the user does not reply at all, do not continue.
A missing reply is not consent.

### Parallel Research Trigger

Use a parallel agent, subtask, or research delegation mechanism only if safely available in the host environment. If unavailable, decompose the research sequentially and label it as a sequential fallback.

Trigger conditions (any one suffices):
- Comparing 2+ independent technology ecosystems (e.g. React vs Vue vs Svelte approaches)
- Researching 3+ unrelated dimensions simultaneously (performance, security, cost)
- The problem spans multiple domains and each needs dedicated investigation
Otherwise research sequentially.

### Offline / Search-Failure Fallback

If web_search or web_extract is unavailable:
1. State clearly: "[LIMITATION: search unavailable. Proceeding with best-effort reasoning.]"
2. Flag all unverified conclusions with "[UNVERIFIED — not backed by live search]"
3. If the problem critically depends on external data → exit with "Cannot proceed without search"

### Output Size Control

Default to concise output. For routine problems, keep P0/P1/P2 compact. Expand only when:
- risk is high,
- the user asks for detail,
- execution requires exact commands,
- verification or rollback needs precision.

---

## Core Process

```
Precheck → P0a [BREAK] → P0b [BREAK] → P1 Research → P2 Architecture [BREAK]
                 ↑                              ↑                  ↑
                 │ (wrong framing)              │ (root cause      │ (user
                 │                              │  misdiagnosed)   │  vetoes)
                 │                              │                  │
         P3 Blueprint → P3.5 Execute → P4 Verify (max 2 retries + initial)
                 ↑                          │
                 └── (implementation bug)   │ pass
                                            ↓
                                           P5 Assess
```

### Critical Incident Exception

For 🔴 Critical incidents:
- Combine P0a and P0b into a short "Immediate Triage" block.
- Do not wait for confirmation before providing safe, read-only diagnostic steps.
- Require explicit confirmation before any destructive, irreversible, security-sensitive, or production-changing action.
- Prioritize containment, evidence preservation, and reversible diagnostics.
- If live web research may help and does not delay containment, use it in parallel or immediately after initial safe diagnostics.

Critical incident flow:
Immediate Triage(P0a+P0b) → Safe R0 Diagnostics(R0 only) → P3 → P3.5 → P4

### P0a — Problem Framing

Goal: confirm shared understanding of the problem before diving into details.

Action checklist:
1. Urgency triage: 🔴 / 🟡 / 🟢
2. Restate the problem in your own words
3. Problem type: technical failure / design decision / knowledge gap / process optimization / non-technical / other
4. Extract 3-5 key points, distinguishing "symptoms" from "root cause clues"

Output format:
```
## P0a — Problem Framing
- Urgency: [🔴 / 🟡 / 🟢]
- Problem Statement: [one sentence]
- Problem Type: [classification]
- Key Points:
  1. [symptom / root cause clue]
  2. [...]

[BREAK — WAITING FOR YOUR RESPONSE. Is this understanding correct? Correct or refine before I proceed to constraints and success criteria.]
```

### P0b — Constraints & Success Definition

Goal: define the boundaries and what "done" looks like.

Action checklist:
1. Revisit P0a framing — does any new information from the user change the problem understanding? If yes, return to P0a to re-frame before continuing.
2. Collect constraints: time, resources, tech stack, compatibility, user skill level, available tools. Do not ask for constraints already provided by the user — extract them from the prompt and only ask about missing critical constraints.
3. Identify stakeholders — who else is affected by or must approve this?
4. Worth-solving check (qualitative): does solving this unlock significant subsequent work? Is this recurring or one-off? Is the impact meaningful?
5. Define success criteria: "what counts as solved?"
6. Record initial assumptions in the Assumption Log

Output format:
```
## P0b — Constraints & Success
- Constraints: [time / environment / tech stack / user skill level / tools]
- Stakeholders: [who else affected or must approve]
- Worth Solving? [YES — unlocks X and is recurring / BORDERLINE — one-off low impact / NO — cost exceeds benefit]
- Success Criteria: [definition]
- Assumption Log:
  1. [ASSUMPTION: ...]
  2. [...]

[BREAK — WAITING FOR YOUR RESPONSE. Please confirm these constraints and assumptions are correct. If any assumption is wrong, tell me now — it will affect the entire solution.]
```

### P1 — Information Gathering

Goal: don't reinvent the wheel. Understand existing solutions.

#### Source Priority

Use provided/local evidence first when the problem includes logs, stack traces, uploaded files, screenshots, repository code, or user-supplied data.

Use live web research when:
- external versions, dependencies, APIs, regulations, security advisories, product specs, or current best practices may affect the answer;
- local evidence is insufficient;
- the user explicitly asks for external research;
- the decision depends on current public information.

When using only local/provided evidence, state:
[SOURCE SCOPE: using provided/local evidence only because public sources would not add value.]

#### Web Research Rules (when applicable)

You MUST web_extract at least 2 distinct sources before writing the P1 conclusion.
Cite sources inline: "[source: URL]".
If after 3 rounds of search+extract you still have fewer than 2 usable sources,
proceed with what you have and flag: "[LIMITED SOURCES: only N usable after 3 rounds.
Conclusions below are best-effort and may be incomplete.]"

Choose mode by problem type:

Mode A — Technical Research (technical failures / architecture / tech selection):
1. If web research is applicable, web_search for solutions, docs, community discussion.
2. If web research is applicable, web_extract at least 2-3 key pages.
3. Use session_search only if available, authorized, and relevant.
4. Find at least 2-3 viable approaches when the problem calls for comparison.
5. Compare: pros/cons, applicable scenarios, risks, evidence for each.
6. Check for known traps: anti-patterns, deprecated methods, version incompatibilities.

Mode B — Non-Technical Discovery (product decisions / career planning / interpersonal):
1. If web research is applicable, web_search for industry reports, case studies, methodologies.
2. If web research is applicable, web_extract at least 2 substantive sources.
3. Load `references/structured-interview.md` only when needed and available.
4. Use interview depth according to stakes: lightweight 1–3 questions, medium 3–5 questions, high-stakes/ambiguous/multi-stakeholder full 7 questions.
5. Identify key decision variables and trade-off dimensions.

Output format:
```
## P1 Information Gathering
- Mode: [A / B]
- Sources: [list URLs actually read via web_extract]
- Approach A: [description] — Pros / Cons / Risks [source: URL]
- Approach B: [description] — Pros / Cons / Risks [source: URL]
- Recommended Direction: [based on P0 constraints + success criteria]
```

### P2 — Solution Architecture

Goal: transform research into an executable architecture.

Action checklist:
1. Based on P1 recommendation, design the overall solution
2. Decompose into independent modules / phases
3. Identify dependencies: what must come first, what can run in parallel
4. Estimate risk and effort per module
5. Develop rollback / degradation strategy (for high-risk operations)
6. For knowledge blind spots (internal architecture, team capabilities): consult user — don't guess
7. Verify alignment with P0 constraints and success criteria
8. Extend Assumption Log: what new assumptions does this architecture make?
9. Re-confirm existing assumptions from P0 are still valid

Output format:
```
## P2 Solution Architecture
- Approach Summary: [one paragraph]
- Modules:
  A: [description] — Depends on: none — Risk: Low / Medium / High
  B: [description] — Depends on: A — Risk: Low / Medium / High
- Execution Order: [A → B or A+B parallel → C]
- Rollback Strategy: [fallback plan]
- Assumption Log (all, including P0):
  1. [ASSUMPTION: ... — still valid? Y/N]
  2. [...]

[BREAK — WAITING FOR YOUR RESPONSE. Approve this architecture to proceed to step design. If you reject this direction, I will return to P1 and recommend an alternative path.]
```

#### User Veto Handler

If the user vetoes the P2 architecture:
1. Ask: "What specifically is wrong — direction, risk level, complexity, or a missing constraint?"
2. Return to P1 with user's objection as new constraint
3. Re-recommend based on updated constraints
4. Re-enter P2 with the new direction only

### P3 — Step Blueprint

Goal: turn each module into concrete, executable steps.

Action checklist:
1. Write operational steps for each module
2. Annotate per step: Risk Level (R0–R3), tool, command, input, expected output, verification
3. Set "checkpoints" for high-risk steps — what to confirm before proceeding
4. Mark step dependencies clearly
5. Mark executor using the Executor Assignment Rules
6. For "User" steps: complete copy-paste-ready commands
7. For "Agent" steps: exact tool and parameters

Output format (executor field is REQUIRED):
```
## P3 Execution Blueprint

### Step 1: [Name]
- Executor: [Agent / User / Either]
- Risk Level: [R0 / R1 / R2 / R3]
- Tool: [terminal / write_file / patch / browser / ...]
- Action: [specific command]
- Depends on: [Step N or none]
- Expected Output: [what success looks like]
- Verification: [how to confirm]

### Step 2: [Name]
...
```

### P3.5 — Execution

Goal: actually run the steps.

Rules:
- Agent steps: invoke tools directly only if they are R0/R1 and allowed by the applicable flow.
- User steps: provide complete commands, wait for user feedback before continuing.
- Either steps: Agent may execute directly only if classified as R0/R1.
- R2/R3 steps always require the authorization rules below, regardless of executor label.
- After each step, check against "Verification" before proceeding.
- If a step fails: record actual output vs expected output. Note which prior steps this step depends on — the root cause may be upstream.
- If user does not respond: flag "[AWAITING YOUR RESULT for step N]" and pause.

R0/R1 Agent steps may execute directly after either:
- P2 approval, or
- a scenario-specific flow that intentionally skips P2, provided P3 lists the exact steps and risk levels.

R2/R3 steps must still follow the R2/R3 authorization rules below.

### P4 — Verification & Iteration

Goal: ensure the solution works. When it doesn't, use external signals to route correctly.

Verification:
1. Check against P0 success criteria item by item
2. Run tests (happy path / edge cases / error scenarios)
3. Code changes: actually run. Config changes: confirm they took effect
4. Regression: does the fix break related functionality?

Exit rule: if this is the THIRD time P4 has failed for the same problem, exit.
"Same problem" definition: the problem as it originated from the user's initial
query. P4 attempt counter is monotonic across the entire conversation — it does
NOT reset if P0a reframes the problem. Count all P4 outputs in the conversation.

The third-failure exit applies when three verification attempts have failed for the same original problem without materially new evidence or a changed hypothesis. If the user provides decisive new evidence that changes the root-cause hypothesis, start a new P4 attempt counter for the new hypothesis while preserving the previous history.

Iteration Loop (when verification fails):

| Signal | Meaning | Route |
|--------|---------|-------|
| User says "that's not the real problem" or provides new symptom info | Root cause misdiagnosed | Back to P0a — reframe |
| Expected output matched but user says "didn't fix the real issue" | Wrong solution direction | Back to P2 — switch path |
| Expected output DID NOT match actual output | Implementation issue | Diagnose further first: was the failed step dependent on a prior step? If yes → check the prior step. If the prior step verified correctly → the bug is in THIS step → back to P3 |
| Solution works but user says acceptance bar was wrong | Criteria unreasonable | Adjust P4 conditions |
| Third P4 failure (count P4 outputs in conversation) | Stuck | Exit — recommend human expert |
| User reports problem resolved itself | False alarm | Exit — record as "Solution not required" |

Critical rule: when diagnosing, cite the specific signal:

"User reported: 'the 504s are still happening after the nginx config change.'
This means the root cause was not nginx config → route back to P0a to reframe."

Output format:
```
## P4 Verification
- Primary Acceptance: [✅ / ❌]
- Tests: Happy Path [✅/❌] | Edge [✅/❌] | Error [✅/❌]
- Regression: [✅/❌]
- P4 Attempt #: [N] (count all P4 outputs in this conversation for this problem)
- Diagnosis Signal: [cite user feedback or output mismatch]
- Decision: [pass→P5 / back to P0a / back to P2 / back to P3 / exit (false alarm) / exit (stuck)]
```

### P5 — Outcome Assessment & Capture

Goal: retrospect, evaluate with evidence, capture learnings.

Action checklist:
1. Evaluate against P0 success criteria item by item
2. Rate solution quality — each rating MUST include evidence:
   - Completeness X/5 [because: covered A,B,C; did NOT cover D,E]
   - Robustness X/5 [because: handles failure modes X,Y; NOT tested for Z under load]
   - Maintainability X/5 [because: modular / documented / concern: part W is coupled]
3. Review Assumption Log: which assumptions were verified? Which remain unchecked?
4. Identify residual risks and uncovered edge cases
5. Follow-up recommendations (P1 necessary / P2 nice-to-have)

Capture tool selection:
- memory: atomic facts ("this project uses pytest", "user prefers concise output")
- skill_manage: complete reusable workflows (3+ steps + clear tool-call patterns)
- Solution is reusable → propose creating a skill
- Pitfall lessons → memory

#### Capture Safety

Only write to memory or skill storage when the host platform permits it and the content complies with the host memory/storage policy. If user consent is required, ask before storing. Never silently store user-specific preferences, project details, or workflows when the platform requires explicit consent.

Output format:
```
## P5 Outcome Assessment
- Success Criteria: [✅ / ⚠️ / ❌ per item]
- Solution Quality:
  - Completeness: X/5 [because: ...]
  - Robustness: X/5 [because: ...]
  - Maintainability: X/5 [because: ...]
- Assumption Log Review:
  - Verified: [list assumptions that held]
  - Unchecked: [list still unverified — these are risks]
- Residual Risks: [description + probability + impact]
- Follow-up:
  1. [P1] ...
  2. [P2] ...
- Capture: [memory: ... / skill: ... / none]
```

---

## Flexible Adaptation Guide

Priority: this table overrides urgency triage when they conflict.

| Scenario | Flow | Rationale |
|----------|------|-----------|
| 🔴 Critical incident | Immediate Triage(P0a+P0b) → Safe R0 Diagnostics(R0 only) → P3 → P3.5 → P4 | Stop the bleeding first; diagnostics must be read-only |
| 🟡 Tech selection | P0a→P0b→P1→P2→P5 | Comparison only — no execution |
| 🟢 System design | Full process | Thorough but keep P2 lean |
| 🟢 Learning plan | P0a→P0b→P1→P2→P3 | Resources and path — skip execution |
| 🟡 Bug debugging | P0a→P0b→P1(fast)→P3→P3.5→P4 | Quick research, focus on execution |
| Non-technical decision | P0a→P0b→P1(B)→P2→P5 | Interview over search |
| Low-risk local debugging | P0-lite → Local Evidence Review → Hypothesis → R0/R1 checks → Fix proposal → P4 | Faster path for reversible, non-production problems where no R2/R3 action is needed. Does NOT bypass R2/R3 authorization |

---

## Tool Collaboration (Prescriptive)

| Phase | Execute This |
|-------|-------------|
| Precheck | Pure reasoning. Zero tools. |
| P0a | read_file if user referenced a file/log. Otherwise conversation only. |
| P0b | None (reasoning from user answers). |
| P1(A) | If web research is applicable: web_search("problem keywords + solution") → web_extract 2–3 sources. If local/provided evidence is sufficient: use local/provided evidence first. Use session_search only if available and authorized. |
| P1(B) | If web research is applicable: web_search("industry report / case study") → web_extract 2–3 sources. Use structured interview depth according to decision stakes: lightweight (1–3 questions), medium (3–5 questions), high-stakes/ambiguous/multi-stakeholder (full 7 questions). Load `references/structured-interview.md` only when needed and available. |
| P2 | Reasoning. If parallel research trigger met → delegate_task(goal="research X", context="P1 findings...", toolsets=["terminal","web"]). |
| P3 | Reasoning only — design the blueprint. Every step must include Executor and Risk Level. |
| P3.5 | Execute only R0/R1 Agent/Either steps directly when allowed by the applicable flow. For R2/R3, show exact command/change and require explicit approval. |
| P4 | terminal for test suites; browser_console for frontend errors; read_file for config/file verification. |
| P5 | Reasoning. Then memory for facts OR skill_manage for workflows (only when permitted by host memory/storage policy and consent requirements). |

---

## Examples (Filled Templates)

Examples are non-executable illustrations. Never run example commands unless they are adapted to the user's actual environment, reclassified for risk, and approved according to R0–R3 rules.

### Example 1: Production 504 Timeouts (🔴 Critical)

User: "Our API service is suddenly returning massive 504 timeouts. Help me troubleshoot."

Precheck: not single-step → 🔴 Critical Incident Exception.

**Immediate Triage (P0a+P0b combined, 🔴 Critical)**
- Urgency: 🔴
- Problem Statement: API service returning 504 Gateway Timeout errors at scale
- Problem Type: technical failure
- Key Points:
  1. Symptom: 504 responses from clients
  2. Symptom: "suddenly" suggests a recent change or threshold breach
  3. Root cause clue: 504 = upstream didn't respond in time → upstream server or network
  4. Root cause clue: "massive" = not isolated — systemic issue
- Constraints: production environment, cannot take extended downtime, user has SSH access, Linux servers, nginx + Node.js stack
- Stakeholders: all API consumers, ops team, on-call engineer
- Worth Solving? YES — service is down, blocking all dependent systems, recurring risk until root cause fixed
- Success Criteria: 504 rate returns to <0.1%, root cause identified and mitigated
- Assumption Log:
  1. [ASSUMPTION: user has production access and can run diagnostic commands]

**Safe R0 Diagnostics (R0 only, no user wait before diagnostics)**

### Step 1: Check nginx error logs
- Executor: User
- Risk Level: R0
- Tool: terminal (SSH)
- Action: `tail -n 200 /var/log/nginx/error.log | grep "upstream timed out"`
- Expected Output: timed-out upstream addresses and frequency
- Verification: log output shows specific upstream IPs and timing pattern

### Step 2: Check upstream service health
- Executor: User
- Risk Level: R0
- Tool: terminal (SSH)
- Action: `curl -o /dev/null -s -w "%{http_code} %{time_total}" http://upstream:port/health`
- Depends on: Step 1 (use IPs found in logs)
- Expected Output: HTTP 200 with <100ms response
- Verification: if >1s or non-200 → upstream is the bottleneck

### Step 3: Check current connections and load
- Executor: User
- Risk Level: R0
- Tool: terminal (SSH)
- Action: `ss -s && uptime && free -h`
- Expected Output: connection counts, load average, memory usage
- Verification: identify if resource exhaustion is contributing

(... remaining safe diagnostic steps)

[BREAK — WAITING FOR YOUR RESPONSE. Please run these read-only diagnostics and share the output. Confirm before any destructive, irreversible, security-sensitive, or production-changing action.]

User runs diagnostics and shares output. P3 and P3.5 follow based on findings.

After P3.5 execution, P4:
- Primary Acceptance: ❌ — user reports "504s still happening after nginx config adjustment"
- Diagnosis Signal: "User: 'the 504s are still happening after the nginx config change' → root cause was not nginx config"
- Decision: back to P0a with new information

### Example 2: Message Queue Selection (🟡 Important)

User: "Our project needs a message queue. RabbitMQ or Kafka?"

Precheck: not single-step → P0a.

**P0a — Problem Framing**
- Urgency: 🟡
- Problem Statement: selecting a message queue technology for a project
- Problem Type: design decision
- Key Points:
  1. Context: order processing pipeline
  2. Constraint clue: user narrowed to RabbitMQ vs Kafka — already researched basics
  3. Decision variable: throughput (100K/day), reliability, ordering guarantees
  4. Growth consideration: may scale to millions/day later

[BREAK — WAITING FOR YOUR RESPONSE. Is this understanding correct?]

User confirms.

**P0b — Constraints & Success**
- Constraints: throughput ~100K/day, needs message ordering, small team, existing infrastructure is Linux-based, user skill level: intermediate backend dev
- Stakeholders: dev team, operations (future)
- Worth Solving? YES — architecture decision with 3+ year impact, costly to change later
- Success Criteria: selected queue meets current throughput with headroom, provides ordering guarantees, manageable operational complexity, migration path to higher throughput if needed
- Assumption Log:
  1. [ASSUMPTION: team has no prior experience with either queue — learning curve matters]
  2. [ASSUMPTION: ordering is required at message level, not just best-effort]

[BREAK — WAITING FOR YOUR RESPONSE. Confirm constraints and assumptions.]

User confirms.

**P1 Information Gathering**
- Mode: A
- Sources: [web_extract RabbitMQ official docs], [web_extract Kafka official docs], [web_extract comparison article]
- Approach A: RabbitMQ — Pros: mature, strong ordering (per-queue FIFO), lower ops complexity, good for moderate throughput. Cons: clustering adds complexity, not designed for million-msg/sec. Risk: may need migration if throughput grows 10x. [source: rabbitmq.com]
- Approach B: Kafka — Pros: massive throughput, replay capability, strong ecosystem. Cons: operational complexity is significant, ordering only within partition, requires ZooKeeper/KRaft management. Risk: over-engineered for current needs. [source: kafka.apache.org]
- Recommended Direction: RabbitMQ for current 100K/day throughput. Include migration path section in architecture for future scaling.

**P2 Solution Architecture**
- Approach Summary: Deploy RabbitMQ with mirrored queues for HA. Use publisher confirms for reliability. Document the migration trigger point and path to Kafka.
- Modules:
  A: Infrastructure — RabbitMQ installation + clustering config. Depends on: none. Risk: Low
  B: Client integration — message producer/consumer patterns with ordering. Depends on: A. Risk: Medium
  C: Monitoring + migration path doc — metrics, alerts, and Kafka migration trigger criteria. Depends on: B. Risk: Low
- Execution Order: A → B → C
- Rollback Strategy: phase A can be undone by removing RabbitMQ container/package
- Assumption Log (all, including P0):
  1. [ASSUMPTION: team has no prior queue experience — still valid? Y — learning curve factored into Module B effort]
  2. [ASSUMPTION: ordering is required at message level — still valid? Y — confirmed by user]

[BREAK — WAITING FOR YOUR RESPONSE. Approve this architecture?]

User approves. 🟡 tech selection → skip P3/P3.5/P4 per scenario table, go to P5.

**P5 Outcome Assessment**
- Success Criteria: ✅ — RabbitMQ meets 100K/day with headroom, supports ordering, manageable ops, Kafka migration path documented
- Solution Quality:
  - Completeness: 4/5 [because: covered selection, deployment, monitoring, migration path; did NOT cover disaster recovery drill or multi-DC failover]
  - Robustness: 4/5 [because: mirrored queues handle node failure; NOT tested under simulated network partition]
  - Maintainability: 4/5 [because: clean module separation, documented migration trigger; concern: team needs RabbitMQ ops training]
- Assumption Log Review:
  - Verified: team no prior experience (confirmed), ordering required (confirmed)
  - Unchecked: projected throughput growth rate (unknown — migration trigger should be monitored)
- Residual Risks: if throughput exceeds 500K/day within 6 months, migration urgency may surprise the team. Probability: Low. Impact: Medium.
- Follow-up:
  1. [P1] Set up RabbitMQ monitoring with throughput alerts
  2. [P2] Conduct team RabbitMQ ops workshop
- Capture: skill — reusable tech selection framework (P0a→P0b→P1→P2→P5 flow)

### Example 3: Learning Rust (🟢 Routine)

User: "I'm a Python backend dev. Want to learn Rust. Where do I start?"

Precheck: not single-step → P0a.

**P0a — Problem Framing**
- Urgency: 🟢
- Problem Statement: planning a learning path for Rust from a Python background
- Problem Type: knowledge gap
- Key Points:
  1. Context: experienced Python backend developer — strong programming fundamentals
  2. Constraint clue: "where do I start" — needs structured roadmap, not random resources
  3. Strength: understands HTTP, databases, APIs — can skip general programming concepts
  4. Risk: Rust's ownership model is the biggest conceptual hurdle for Python devs

[BREAK — WAITING FOR YOUR RESPONSE. Is this understanding correct?]

User confirms.

**P0b — Constraints & Success**
- Constraints: part-time learning (evenings/weekends), ~5-8 hours/week available, prefers hands-on over theory, user skill level: senior Python backend
- Stakeholders: self only
- Worth Solving? YES — career growth, unlocks systems programming and high-performance work
- Success Criteria: can build a working CLI tool in Rust from scratch, understands ownership/borrowing/lifetimes, can read and understand common Rust codebases
- Assumption Log:
  1. [ASSUMPTION: user has access to a computer with Rust toolchain installable]
  2. [ASSUMPTION: user prefers project-based learning over textbook approach]

[BREAK — WAITING FOR YOUR RESPONSE. Confirm constraints and assumptions.]

User confirms.

**P1 Information Gathering**
- Mode: A
- Sources: [web_extract Rust official book], [web_extract Rust by Example], [web_extract community learning path guide]
- Approach A: Official Rust Book first — Pros: authoritative, comprehensive, free. Cons: theory-heavy in early chapters, may lose motivation before project phase. Risk: abandonment in chapter 4-6 (ownership deep dive). [source: doc.rust-lang.org/book]
- Approach B: Project-first (Rust by Example + exercism.io) — Pros: hands-on, immediate gratification, learn by doing. Cons: may develop bad habits without understanding ownership fundamentals. Risk: superficial understanding. [source: doc.rust-lang.org/rust-by-example]
- Recommended Direction: Hybrid — start with Rust Book chapters 1-6 (through ownership), then switch to project-based learning for the rest.

**P2 Solution Architecture**
- Approach Summary: Three-phase learning path. Phase 1 covers fundamentals through official book. Phase 2 applies knowledge via progressively harder projects. Phase 3 bridges to real-world usage.
- Modules:
  A: Phase 1 — Rust Book ch.1-10 (ownership, structs, enums, error handling, generics). Depends on: none. Risk: Low — standard curriculum
  B: Phase 2 — Build 3 projects (CLI calculator → file parser → HTTP client with reqwest). Depends on: A. Risk: Low
  C: Phase 3 — Contribute to a small open-source Rust project or rewrite a Python utility in Rust. Depends on: B. Risk: Medium — depends on finding suitable project
- Execution Order: A → B → C
- Rollback Strategy: N/A (learning plan, nothing to roll back)
- Assumption Log (all, including P0):
  1. [ASSUMPTION: user can install Rust toolchain — still valid? Y]
  2. [ASSUMPTION: user prefers project-based learning — still valid? Y — hybrid approach balances book + projects]

[BREAK — WAITING FOR YOUR RESPONSE. Approve this learning architecture?]

User approves. 🟢 learning plan → proceed to P3, skip P3.5/P4 per scenario table.

**P3 Execution Blueprint**

### Step 1: Install Rust toolchain
- Executor: User
- Risk Level: R1
- Tool: terminal
- Action: `curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
- Depends on: none
- Expected Output: rustup, rustc, cargo installed and on PATH
- Verification: `rustc --version` and `cargo --version` return version numbers

### Step 2: Read Rust Book chapters 1-6
- Executor: User
- Risk Level: R0
- Tool: browser
- Action: https://doc.rust-lang.org/book/ — work through chapters 1-6 including all code examples (type them out, don't copy-paste)
- Depends on: Step 1
- Expected Output: understanding of variables, functions, ownership, borrowing, structs, enums
- Verification: can explain ownership rules in own words; can write a small program using structs and enums without looking up syntax

### Step 3: Build CLI calculator project
- Executor: User
- Risk Level: R1
- Tool: terminal + editor
- Action: Build a CLI calculator that takes expressions like "2 + 3 * 4" from args, parses them, and outputs results. Use clap for arg parsing.
- Depends on: Step 2
- Expected Output: working binary that handles basic arithmetic with operator precedence
- Verification: `cargo run -- "2 + 3 * 4"` outputs 14

### Step 4: Build file parser project
- Executor: User
- Risk Level: R1
- Tool: terminal + editor
- Action: Build a tool that reads a CSV/JSON log file, parses it, filters entries by date range, and outputs summary statistics. Use serde for deserialization.
- Depends on: Step 3
- Expected Output: binary that processes a sample file and prints correct stats
- Verification: run against a known test file and verify output matches expected counts

### Step 5: Build HTTP client project
- Executor: User
- Risk Level: R1 (upgrade to R2 if calling real external APIs or consuming paid quotas)
- Tool: terminal + editor
- Action: Build a CLI tool that calls a REST API (e.g. GitHub API), fetches data, and displays formatted results. Use reqwest + tokio for async HTTP.
- Depends on: Step 4
- Expected Output: binary that fetches and displays GitHub user info or repo list
- Verification: `cargo run -- <username>` outputs correct public data matching GitHub web UI

### Step 6: Find and contribute to an open-source project
- Executor: User
- Risk Level: R1 (require user confirmation before submitting a PR or publishing content)
- Tool: browser + terminal + editor
- Action: Search GitHub for Rust projects tagged "good-first-issue". Pick one, read CONTRIBUTING.md, submit a small PR.
- Depends on: Step 5
- Expected Output: a merged (or submitted) PR to a real Rust project
- Verification: PR link exists and received review feedback

→ Learning plan complete. Skip P3.5 and P4 per scenario table. P5 assessment at user's discretion after completing the plan.

---

## Reference Files

- `references/structured-interview.md` — Mode B interview template (7 questions). Load via skill_view(file_path='references/structured-interview.md') when entering P1(B).

---

## Important Notes

1. Precheck saves time — don't run P0 on `ls` questions
2. P0a is the most critical phase — get framing right or everything downstream is wrong
3. BREAKPOINTS are mandatory at P0a, P0b, and P2 unless Low-Risk Continuous Mode or Critical Incident Exception applies.
4. Don't repeat work the user has already done — continue from where they stopped
5. P1 requires actual evidence: use local/provided evidence first when appropriate; use web_extract ≥2 sources when web research is applicable.
6. P3 steps must be directly executable — copy-paste commands, not "approaches"
7. P4 diagnosis MUST cite an external signal — never self-diagnose silently
8. Exit on 3rd P4 failure unless decisive new evidence changes the root-cause hypothesis.
9. P5 ratings require evidence: "X/5 because A, B, C; NOT D"
10. Capture only when permitted by host memory/storage policy and consent requirements.
11. If user vetoes P2, return to P1 with the objection as a new constraint
12. If search is unavailable, flag all unverified conclusions and exit if search-critical
13. P4 dependency chain: if step N fails and depends on step N-1, verify step N-1 first
