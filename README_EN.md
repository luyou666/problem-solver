# Problem Solver / 事儿通 v6.0

It exists for one thing: helping you solve problems when you don't know where to start.

A universal structured problem analysis and resolution framework. From understanding the problem to verifying the result — a complete closed loop.

---

## Beginner's Guide 🐣

Don't be intimidated. It looks complex but only takes three steps:

### Step 1: Hand it to your AI assistant

Copy the full contents of [SKILL.md](./SKILL.md) and paste it into a conversation with ChatGPT / Claude / DeepSeek / Hermes, then say:

> "Please use the Problem Solver / 事儿通 framework to handle my problem."

That's it. The AI will follow the process automatically.

### Step 2: Describe your problem in plain language

No need to learn any format. Just talk like you're venting to a friend:

- "My website just went down, help!" → AI auto-identifies it as 🔴 Critical Incident, starts with diagnostic commands
- "I want to learn Python but don't know where to start" → AI identifies as 🟢 Learning Plan, walks you through the full process
- "Our team can't decide between MySQL and PostgreSQL" → AI will help you compare and choose

You can even say: "I'm not sure this is even a problem, but something feels off…"

### Step 3: Follow the AI's lead

The AI will pause at critical checkpoints (you'll see `[BREAK — WAITING FOR YOUR RESPONSE]`). When that happens:

- It asks "Did I understand this correctly?" → Confirm or correct it
- It gives you diagnostic commands → Copy, paste into your terminal, run them, and share the output
- It presents options for your approval → Say "go ahead" or explain what you disagree with
- It asks if it should make changes directly → You can say "Let me see first, don't jump ahead"

**The core mindset: You're the boss, the AI is your technical advisor. It proposes — you decide.**

### Common Scenarios Quick Reference

| Your Situation | How to Use It |
|---------------|---------------|
| Code is throwing errors | Paste the full error message and say "Help me analyze this" |
| Can't decide between two technologies | Say "Compare A vs B for me. My project context is…" |
| Want to learn something but no direction | Say "I have XX background, I want to learn YY, build me a learning path" |
| System/service is down | Describe the symptom + when it started + any anomalies |
| Disagreeing with a colleague | Say "My view is A, they say B, help me analyze objectively" |

### Three Principles to Remember

1. **The more complete your information, the better the AI's judgment** — paste full logs, screenshots, entire error messages. Don't worry about being verbose.
2. **The AI won't touch anything without your approval** — it only reads, never writes until you explicitly say "make the change for me."
3. **Plain language is all you need** — don't learn framework terminology. That's the AI's job. Just describe the problem.

---

## Core Philosophy

- Understand before acting — grasp the essence of the problem, don't rush to a solution
- Structured but not rigid — adapt flexibly based on complexity and urgency
- Iteration over perfection — deliver a usable solution first, then refine continuously
- Know what you don't know — admit when something is beyond your capability
- Claims need evidence — diagnostics, security advice, and version-related statements must have sources

## Full Process

```
Precheck → P0a Problem Framing → P0b Constraints & Goals → P1 Information Gathering → P2 Solution Architecture
                                                                    ↓
                                                          P3 Execution Blueprint → P3.5 Execute → P4 Verify → P5 Evaluate
```

## Risk Tier System (R0–R3)

| Tier | Label | Description |
|------|-------|-------------|
| R0 | Read-only | Read files, check logs, search code — Agent may execute freely |
| R1 | Local | Local testing, no production/live services/databases — execute after framework confirmed |
| R2 | State Change Reversible | Edit files, update config, install packages, restart services — requires explicit user approval per step |
| R3 | High Risk | Delete data, modify production systems, security/payment config changes — requires full visibility + rollback plan |

## Scenario Adaptation

| Scenario | Process |
|----------|---------|
| 🔴 Critical Incident | Immediate Triage → Safe R0 diagnostics → P3 → P3.5 → P4 |
| 🟡 Technology Selection | P0a → P0b → P1 → P2 → P5 |
| 🟡 Bug Debugging | P0a → P0b → P1 (fast) → P3 → P3.5 → P4 |
| 🟢 System Design / Learning Plan | Full process |
| Low-risk local debugging | P0-lite → local evidence → hypothesis → R0/R1 checks → fix suggestion → P4 |

## Critical Safety Rules

- BREAK and wait for user confirmation after P0a / P0b / P2
- P2 approval ≠ R2/R3 execution authorization
- No user response ≠ consent
- Read-only diagnostics may be provided without waiting for confirmation (critical incidents)

## License

MIT License

---

Designed for both AI assistants and human collaborators. Suitable for everyday troubleshooting, technology selection, architecture design, learning planning, and more.
