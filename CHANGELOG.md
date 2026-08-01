# Changelog

All notable changes to this project are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

**Why every change is tied to a version bump:** plugins are served from a
version-keyed cache (`~/.claude/plugins/cache/<marketplace>/<plugin>/<version>/`).
Editing a file in the source tree does **not** reach an installed client — the
version has to move. Release 1.1.1 exists for that reason alone.

---

## [1.2.0] — 2026-08-01

The second release driven by a failure of this plugin's own output — and this time the plan
**passed every existing gate.** A controlled round-2 A/B scored a `spec-coverage` build-out plan
**7/7** on a rubric fixed before the run: Gate 0 fired, routing was correct, all seven required
surfaces were specified, no requirement was demoted, the stack was named with what it bought.
The artifact built from it had a dead primary interaction.

Reading the code showed why, and it was not a missing part. The launch path crossed five layers,
every layer existed, and each one carried a guard. Nothing in the plan ever wrote the chain down,
so nobody checked that the guards can all hold at the same moment. **1.1 taught the pipeline to
stop dropping surfaces; 1.2 teaches it to specify that the surfaces are connected.**

Two more findings from the same round shaped the rest of this release: one plan reached 3,026 words
of which **67% were table rows**, leaving ~334 words of executable instruction; and one
implementation resurrected an alternative its plan had rejected by name, declaring a dependency
version that had never been published.

### Added

- **`## Load-bearing path`** — a required section for build-outs. Pick the one path whose failure
  makes the artifact pointless (a game's launch, a form's submit-to-persisted, a pipeline's first
  record landing, an approval flow's first request) and write it as a chain of **≤5 hops**. Every hop
  carries three columns: its name, the condition that must hold for it to pass, and **where that
  condition first becomes true**. Followed by a **cold-start table** covering every condition named
  in those hops — initial value, who changes it, when. **A blank cell is a defect in the plan.**
  Two hard rules: a hop whose "first becomes true at" cannot be filled means the plan is unfinished,
  and a hop may only name symbols the plan commits to creating elsewhere.
- **Verb sentences** — every requirement marked `build` owes one sentence **outside any table**:
  *when ⟨actor⟩ does ⟨action⟩, ⟨observable result⟩ happens; its absence shows up as ⟨symptom⟩*.
  Rows are the ledger; sentences are the instruction. Rows without sentences are a stub inventory.
- **`## Implementer contract`** — the plan's last and shortest section, for what must survive the gap
  to someone who never saw the conversation: rejections carry **revival triggers** (an unexplained
  prohibition gets overturned), dependencies are pinned to **versions that resolve**, and any guarantee
  the stack was bought for is claimed with **the command and its exit status in "done"**, not as a
  property in prose. Three round-2 plans bought compile-time checking and shipped code that did not
  compile, because nothing in "done" referred to the build.
- **Stage 2c — wiring audit** (`SKILL.md`) — for build-outs, a **second, fresh** `plan-writer` instance
  audits the finished plan against five document-level questions and writes `wiring-audit.md`; the main
  agent then has the writer make the minimal additions. An author cannot audit their own omissions,
  which is why the auditor is a different instance. Roughly **+20–35% tokens** on the writing stage,
  spent on the one defect class that reading cannot see. Skipped for decision documents.
- **`Load-bearing path candidate`** in the packet template, and input-contract item 7 for `plan-writer`
  — the main agent has the session context and is better placed to know what the user came for; the
  writer owns the final call but must name the path it chose instead.
- Three quality-gate items and two `spec-coverage` required components wired to the above.

### Deliberately not added

An instruction to "review whether the code will actually work". It is unfalsifiable (an agent can
assert it), it arrives after the cost is sunk, and in two validation corpora self-review language was
counted as credit for defects that were never fixed. A plan cannot verify anything — so every rule in
this release is checkable **by reading the plan against itself**, and none of them asks anyone to build,
run, open or look at the artifact.

## [1.1.3] — 2026-08-01

Documentation only. No change to skill behaviour, frames, or agent definitions.

### Added

- `CHANGELOG.md` — this file. Release history reconstructed from
  `git log -p -- plugins/plan-smith/.claude-plugin/plugin.json`, which is the
  only authority on where each version boundary actually fell.
- A repo rule (local, untracked) requiring that `CHANGELOG.md` and both READMEs
  be updated as part of any version bump, rather than after it.

### Notes

- `frames.md` is unchanged at 364 lines / **26 frames** (verified by counting
  `### ` headings: 28 total, minus `Gate 0` and `The four predicates`, which are
  routing sections rather than frames).

## [1.1.2] — 2026-07-29

### Added

- **Run stamp in the context packet** (`packet-template.md`) — the packet's first
  section now records the plugin version read from `plugin.json`, a `frames.md`
  fingerprint, the **resolved model ids** of both the main agent and the writer,
  and whether the run was interactive or scripted.
- `SKILL.md` step **2b** makes filling the stamp the first thing that happens in
  the packet.

### Why

Aliases are not versions. `opus` resolved to `claude-opus-4-8` on 2026-07-25 and
to `claude-opus-5` on 2026-07-29, and the pipeline's own A/B lost the model
version of half its artifacts — two rounds were nearly mislabelled and had to be
recovered from raw transcripts. A plan that cannot name what produced it is not
comparable to the next one.

## [1.1.1] — 2026-07-28

### Changed

- Version bump only, so installed clients pull the clauses added after 1.1.0.

### Why

The changes in the preceding commit (document budget, Gate 0 tie-break) were
written into the source tree but the installed plugin kept serving 1.1.0 from
cache while reporting itself up to date. The bump is the delivery mechanism, not
a code change.

## [1.1.0] — 2026-07-28

The first release driven by a **failure of this plugin's own output**. A
controlled A/B on a spec-complete task (browser game: 10 stages, physics loop, a
named UI requirement) had a `backward`-framed plan **lose to an unframed
baseline** — 38% longer, 41% less implementable instruction (777 → 460 words),
~37% of the body narrating its own methodology, and sound / particles /
persistence / stack absent from the shipped code.

### Added

- **`Gate 0` routing** (`frames.md`, `SKILL.md` step 1b) — before any frame is
  chosen, ask *if this plan is followed literally, is the danger that we chose
  wrong, or that we left things out?* A complete spec whose risk is omission is a
  **build-out** and routes to `spec-coverage`; other frames may be borrowed for a
  single genuinely open sub-decision, and the borrow is recorded.
  **Tie-break: when genuinely unsure, choose build-out** — the two misroutings do
  not cost the same. A build-out sent to a narrowing frame silently omits
  requirements; a decision sent to `spec-coverage` merely leaves one cell shallow.
- **`spec-coverage` frame** (26th frame) — requirement × surface completeness
  matrix, with required components: a no-silent-drop ledger
  (`build` / `defer (+trigger)` / `n-a (+reason)`, blank = defect), a **quality
  floor per surface**, a **content axis** distinct from the mechanics axis, a
  dependency-ordered thin end-to-end slice whose polish step is **named**, and a
  **named delivery stack with the reason it was bought**.
- **Document budget** rule — methodology argument belongs in the packet; in the
  plan the frame and style get *the header line only*. One test governs every
  paragraph: would a reader who must build this be worse off if it were deleted?
  A matching quality-gate item asks whether the body is **majority implementable
  instruction**.
- Deliverable type (from Gate 0) added to the user confirmation gate.

### Changed

- **Off-anchor clause rewritten** — a requirement absent from the anchors may not
  *bend the architecture*, but it **stays in scope and still ships**. Two hard
  bans: never re-classify a stated requirement as decorative/cosmetic to keep it
  off the anchor list (the anchor list derives decisions, it is not a scope
  filter), and never leave an uncovered requirement in silence — it gets an
  explicit build step or an explicit deferral with a trigger.
- New watch-out: **canon ends the derivation, not the specification.** Noting "this
  choice is canonical, not derived" is honest, but the plan must still state the
  choice and what it buys ("TypeScript, so the stage schema and the state machine
  are checkable"). Observed failure: a plan logged the stack as canon and then
  contained **zero** stack sentences, so implementer defaults filled the vacuum.
- New failure mode recorded: **the anchor list silently becomes the scope list** —
  an item killed as an *alternative* is never re-instated as a *feature*. One plan
  killed "score-threshold clear" as a judgment rule, after which score existed in
  the document only as a dead alternative and the score/star system was never
  specified.

## [1.0.0] — 2026-07-19

Initial release.

### Added

- **Two-stage pipeline** — the main agent distills the whole conversation into a
  self-contained context packet (goals, hard constraints, rejected alternatives);
  a clean-context `plan-writer` agent drafts the plan; the main agent relays the
  plan file verbatim.
- **`plan-writer` agent** restricted to `Read / Glob / Grep / Write` — read-only
  toward the codebase, writes exactly one output file.
- **Frame library** (`frames.md`) — reasoning frames in 6 families, each with a
  starting point, **required components** (the parts whose absence turns the frame
  into decoration), failure mode, and corpus-derived watch-outs, plus four routing
  predicates.
- **Style library** (`styles.md`) — `opus-style`, `fable-style`, and `relay`,
  distilled from a controlled 26-plan experiment run under two model fingerprints
  and stress-tested against a 100-plan validation corpus. Styles are prompt
  discipline, not model selection.
- **Packet template** (`packet-template.md`), skill entry point (`SKILL.md`),
  bilingual READMEs (`README.md` / `README.ko.md`), MIT licence, and a local
  directory marketplace (`.claude-plugin/marketplace.json`).

---

## Open items (not scheduled)

- **`analogical` frame is absent.** It existed in the original planning playground
  and was dropped when `frames.md` was distilled; `spec-coverage` took the 26th
  slot. A 76-topic corpus generated against 1.1.2 had to fall back to the
  playground's own description for that one topic. Restoring it would change plugin
  behaviour and therefore make any corpus generated afterwards a different spec, so
  it is deliberately left undone pending a decision.
- **`relay` is a single observation.** It produced the strongest artifact in the
  experiment it came from, and the pipeline still treats it as promising rather
  than proven. Retrospectives accumulate the evidence.

[1.2.0]: https://github.com/zeriong/plan-smith/releases/tag/v1.2.0
[1.1.3]: https://github.com/zeriong/plan-smith/releases/tag/v1.1.3
[1.1.2]: https://github.com/zeriong/plan-smith/releases/tag/v1.1.2
[1.1.1]: https://github.com/zeriong/plan-smith/releases/tag/v1.1.1
[1.1.0]: https://github.com/zeriong/plan-smith/releases/tag/v1.1.0
[1.0.0]: https://github.com/zeriong/plan-smith/releases/tag/v1.0.0
