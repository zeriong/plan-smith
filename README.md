<p align="center">
  <strong>plan-smith</strong>
</p>

<p align="center">
  <strong>Forge plans like a smithy — the main agent distills intent, a clean-context writer forges the plan, and it is delivered verbatim.</strong>
</p>

<p align="center">
  <a href="https://github.com/zeriong/plan-smith/releases"><img src="https://img.shields.io/badge/version-1.1.3-blue" alt="Version"></a>
  <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/License-MIT-green.svg" alt="License: MIT"></a>
  <a href="https://docs.claude.com/en/docs/claude-code/plugins"><img src="https://img.shields.io/badge/Claude%20Code-Plugin-orange" alt="Claude Code Plugin"></a>
</p>

<p align="center">
  <a href="#installation">Install</a> &bull;
  <a href="#what-it-does">What it does</a> &bull;
  <a href="#the-pipeline">The pipeline</a> &bull;
  <a href="#frames--styles">Frames & styles</a> &bull;
  <a href="#faq">FAQ</a>
</p>

<p align="center">
  <a href="README.md">English</a> &bull;
  <a href="README.ko.md">한국어</a>
</p>

---

Long sessions produce bad plans for a structural reason: the agent that knows your intent best is also the one whose context is the most polluted — dozens of tool outputs, repeated file dumps, dead ends. Writing a plan there means writing through noise. Writing it in a fresh subagent means losing the intent.

**plan-smith refuses the trade-off by splitting the job.** The main agent spends its one unique asset — session context — entirely on *intent distillation*, producing a structured context packet. A clean-context `plan-writer` agent then forges the plan from the packet, guided by a battle-tested reasoning-frame library and one of two validated writing styles. The finished plan is relayed **verbatim** — never summarized.

## What it does

- **Intent distillation, not main-agent writing** — the skill extracts goals, hard constraints, **rejected alternatives**, settled decisions, and relevant files from the whole conversation into a packet. Extraction is far more robust to context noise than composition is.
- **One confirmation gate** — before any writing happens, the packet is shown to you: *"here is the intent and constraints I distilled — correct?"* Every inferred field is marked `⚠guess` and confirmed. A pipeline that can correct intent always beats a clean draft of the wrong intent.
- **Clean-context forging** — `plan-writer` runs with no session noise: packet + frame spec + style directive only. Read-only toward your codebase; writes only its designated output file(s) — the plan, plus audit.md in relay pass 2 — and returns paths, not text.
- **A reasoning-frame library that earns its keep** — 26 frames (backward-chaining, premortem, back-of-envelope, constraint-first, incentive accounting, …) distilled from a 26-plan empirical experiment, stress-tested by a 100-plan validation corpus, and **corrected by a controlled A/B in which a framed plan lost to an unframed baseline** on a spec-complete build — the fix is `spec-coverage` plus the Gate 0 routing rule below. Each frame ships with its **required components** (the parts whose absence turns it into decoration) and routing predicates for auto-selection.
- **Two validated writing styles + relay** — `opus`-style (coverage-first disciplined draft, honest confession log) and `fable`-style (structure-auditing revision, judgment encoded as rules). **Relay mode** chains them: draft → confession → adversarial revision with a T1–T6 contamination audit — the strongest pipeline observed in the source experiment.
- **Gate 0: it knows when a frame would hurt** *(new in 1.1)* — before any frame is chosen the pipeline asks *"followed literally, is the danger that we chose wrong, or that we left things out?"* A complete spec whose risk is omission is a **build-out** and routes to `spec-coverage` (a requirement × surface completeness matrix), because narrowing frames license the omission of whatever they did not select. This rule exists because a framed plan lost to an unframed one — see the FAQ.
- **Lossless delivery + retrospective data** — the plan file is presented in full, and every outcome (adopted / edited / rejected) is appended to the packet, accumulating data to tune frame routing over time.

## Installation

### Via Claude Code plugin marketplace

1. In Claude Code, run `/plugin`.
2. Marketplaces → Add Marketplace.
3. Enter the URL: `https://github.com/zeriong/plan-smith.git` (or a local path to this repo).
4. Install `plan-smith`.

### Or via CLI

```bash
claude plugin marketplace add https://github.com/zeriong/plan-smith.git   # or a local path
claude plugin install plan-smith@plan-smith-marketplace
```

### Or wire it directly in `~/.claude/settings.json`

```json
{
  "extraKnownMarketplaces": {
    "plan-smith-marketplace": {
      "source": { "source": "git", "url": "https://github.com/zeriong/plan-smith.git" }
    }
  },
  "enabledPlugins": { "plan-smith@plan-smith-marketplace": true }
}
```

## Usage

```
/plan-smith:plan-smith migrate the session store from Redis to Postgres
/plan-smith:plan-smith frame=premortem style=relay launch plan for the paid newsletter
/plan-smith:plan-smith style=fable review and harden the deploy runbook
```

Or just ask naturally — *"write a plan for X"* triggers the skill. Arguments:

| Argument | Values | Default |
|---|---|---|
| `frame` | any frame in [frames.md](plugins/plan-smith/skills/plan-smith/references/frames.md) | auto-routed by 4 predicates, rationale recorded |
| `style` | `opus` \| `fable` \| `relay` \| `auto` | `auto` — routed by task signals, rationale recorded |

Artifacts land in `plans/<slug>/`: `packet.md`, `plan.md` (+ `draft.md`, `audit.md` in relay mode).

## The pipeline

```
Stage 1 — Intent distillation (main agent)
  conversation ──▶ context packet ──▶ user confirmation gate («⚠guess» fields resolved)

Stage 2 — Isolated writing (plan-writer agent, fresh context)
  packet + frame spec + style directive ──▶ plans/<slug>/plan.md
  relay: opus-style draft (+ confession) ──▶ fable-style revision (+ T1–T6 audit)

Stage 3 — Lossless relay (main agent)
  plan file presented verbatim ──▶ user verdict ──▶ retrospective line appended to packet
```

Why the split works: *wanting the right plan* requires session context (Stage 1's resource); *uncontaminated writing* requires isolation (Stage 2's resource); *undamaged delivery* requires a file contract (Stage 3's rule). One agent can't hold all three — a pipeline can.

## Frames & styles

- **[frames.md](plugins/plan-smith/skills/plan-smith/references/frames.md)** — 26 frames in 6 families (backward, negative/failure, quantitative/constraint, diagnostic, multi-perspective, form), each with starting point, required components, failure mode, and corpus-derived watch-outs. Routing runs **Gate 0 first** — *decision document or build-out?* (a complete spec whose risk is omission goes to `spec-coverage`, because narrowing frames license omission) — then four predicates: *where is the uncertainty / how rigid are resources / how much cognitive slack does the executor have / can a scale be agreed*.
- **[styles.md](plugins/plan-smith/skills/plan-smith/references/styles.md)** — the two writing disciplines and the relay protocol. A style is a prompt-level discipline, **not** a model choice — designed to run on any model (cross-model portability is still being validated through retrospectives).

## Changelog

Release history, and *why* each frame or rule was added, lives in
**[CHANGELOG.md](CHANGELOG.md)**. Worth knowing before you pin a version: the
plugin is served from a **version-keyed cache**, so editing the source tree does
not reach an installed client — every behavioural change ships as a version bump,
and 1.1.1 exists for that reason alone.

## FAQ

**Why doesn't the main agent just write the plan?** Because its context is polluted by the very session that gave it the intent. Extraction (signal-picking) survives noise; composition doesn't. plan-smith uses the polluted context for the only job it's still good at.

**Doesn't the subagent lose the conversation's nuance?** That's what the packet is for — goals, constraints, *rejected alternatives* (so the writer doesn't re-propose them), settled decisions, and per-file gists, all confirmed by you at the gate before writing begins.

**When is relay worth double cost?** Hard-to-reverse or high-blast-radius decisions, plans you'll execute for months. The audit trail (`audit.md`) records what the revision changed, what it kept, and why — final merge authority stays with you.

**Can a frame make a plan *worse*?** Yes — and it did, which is why 1.1 exists. In a controlled A/B (same model, same fully-specified task: a browser game with 10 stages, a physics loop and a named UI requirement), the `backward`-framed plan ran 38% longer than the unframed baseline yet carried **41% less implementable instruction**, spent ~37% of its body narrating its own methodology, cut sound/particles/mobile as "off-anchor", went silent on score, stars, persistence *and the stack*, and re-labelled one of the user's three stated requirements "cosmetic". The baseline shipped `audio.ts`, `effects.ts`, `storage.ts`, `types.ts`; the framed one shipped none of them. Three fixes followed: an anchor list may no longer act as a scope filter, "this choice is canonical" may end a derivation but never a specification, and **Gate 0** now diverts build-outs away from narrowing frames entirely.

**What are the styles based on?** A controlled experiment: the same 26 planning tasks were executed under two distinct model fingerprints, then comparatively audited. The audit distilled each fingerprint into a reproducible writing directive; chaining them (relay) produced the strongest artifacts in that experiment — single-run evidence, so the pipeline treats relay as promising rather than proven and accumulates retrospectives to test it.

## License

MIT
