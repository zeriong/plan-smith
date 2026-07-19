# plan-smith (plugin)

Two-stage planning pipeline for Claude Code. Full documentation: [repository root README](../../README.md) ([한국어](../../README.ko.md)).

## Components

| Component | Path | Role |
|---|---|---|
| Skill `plan-smith` | `skills/plan-smith/SKILL.md` | Pipeline orchestration for the **main agent**: Stage 1 intent distillation → packet + user confirmation gate, Stage 2 delegation, Stage 3 verbatim relay + retrospective record. |
| Frame library | `skills/plan-smith/references/frames.md` | 25 reasoning frames in 6 families — each with starting point, **required components**, failure mode, watch-outs — plus 4-predicate routing and the common plan template. |
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
