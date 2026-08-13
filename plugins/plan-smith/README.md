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

## Measured results — why this skill earns its tokens

Every claim below is a lab measurement, not an estimate (data: z-lab `plan-smith-lab/`, series `tco/`, `transfer/`).

**Total cost to a *working* artifact** (repair loop included; weak implementer, fixed repair-prompt template, n=4 chains per arm):

| Stage | baseline plan | plan-smith v1.4 |
|---|---|---|
| Plan (opus) | 229k | 689k — **3.0× dearer** (reads four skill docs) |
| Implementations ×4 (haiku) | 6,968k | 4,069k — **42% cheaper** |
| Repairs to DONE | **8,380k** (4 rounds) | **620k** (1 round) — **13.5× cheaper** |
| **Total, 4 working artifacts** | **15.58M** | **5.38M — 65.5% cheaper** |

In round numbers: baseline 100 + repairs 116 = 216; plan-smith 66, done. The mechanism, not just the number: the repair prompts were identical templates, so the gap comes from the **defect class each plan produces** — plan-smith's one failure was a single symbol its copyable blocks had missed (one-line fix; that chain ended in the test family's first stage CLEAR), while the baseline's failures were cross-component contract mismatches (one interface rebuild, one three-round whack-a-mole).

**Side effects with receipts:** phantom dependency versions 5 → 0 once URLs became copyable strings; 4/4 replicas reproduced the identical CDN line and file layout; the only stage CLEARs in the whole test family came from plan-smith cells.

**Limits, stated in the same breath:** small repair sample (2 chains vs 1), one task, one weak implementer — the gap shrinks with strong implementers, which succeed without the skill. Half the baseline repair bill rides on a single chain.

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
