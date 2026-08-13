# plan-smith (plugin)

Two-stage planning pipeline for Claude Code. Full documentation: [repository root README](../../README.md) ([한국어](../../README.ko.md)).

## Components

| Component | Path | Role |
|---|---|---|
| Skill `plan-smith` | `skills/plan-smith/SKILL.md` | Pipeline orchestration for the **main agent**: Stage 1 intent distillation → packet + user confirmation gate, Stage 2 delegation, **Stage 2c wiring audit** (build-outs, fresh writer instance), Stage 3 verbatim relay + retrospective record. |
| Frame library | `skills/plan-smith/references/frames.md` | 26 reasoning frames in 6 families — each with starting point, **required components**, failure mode, watch-outs — plus Gate 0 + 4-predicate routing, the common plan template, and the three specification rules every build-out owes: **load-bearing path** (a ≤5-hop chain whose guards each say where they first become true, plus a cold-start table), **verb sentences outside the ledger**, and the **implementer contract** (revival triggers, pinned versions, and the command whose exit status proves any guarantee the stack was bought for) — plus **Gate 0's implementer axis and the machinery budget** (1.3): a weak or unknown implementer gets the plan demanding the least machinery, with the highest-risk glue as **verbatim copyable blocks** (1.4, gate-validated pre-release). |
| Style directives | `skills/plan-smith/references/styles.md` | `opus`-style (coverage-first disciplined draft + confession log), `fable`-style (structure-auditing revision + rule-encoded judgment), and the `relay` two-pass protocol with T1–T6 contamination audit. |
| Packet template | `skills/plan-smith/references/packet-template.md` | The context packet contract — the only channel from session to writer. |
| Agent `plan-writer` | `agents/plan-writer.md` | Clean-context author. Self-contained input contract, read-only toward the codebase, writes only its designated output file(s) (plan, plus audit.md in relay pass 2), returns paths — never the plan text. |

## Entry point

```
/plan-smith:plan-smith [frame=<frame>] [style=opus|fable|relay|auto] <task description>
```

Natural-language requests ("write a plan for …") trigger the skill as well. Artifacts: `plans/<slug>/{packet.md, plan.md[, draft.md, audit.md]}`.

## Design invariants

1. The main agent **never writes the plan** — it distills intent; extraction survives context noise, composition doesn't.
2. **No writing without a confirmed packet** — every inferred field is `⚠guess`-marked and resolved with the user first. When a decision is ambiguous, ask immediately.
3. The writer's inputs are **self-contained** (packet + inline frame/style specs) — no session access, no path guessing.
4. **Verbatim relay** — the plan file is presented in full; summaries are a contract violation.
5. Styles are **prompt disciplines, not model choices** — distilled from a 26-plan controlled experiment and stress-tested in a 100-plan validation corpus; relay chaining and cross-model portability remain promising-but-unverified, with retrospectives accumulating the evidence.
