# Scriptorium

**Your personal, local-first research operating system.**
A single authoritative memory, a human + AI workbench, event-driven capture with
on-demand sync, and pluggable output — your library, conversations, and
slides governed safely on your own machine, tied together by one local memory
layer and exchanged through open file contracts. No telemetry, no lock-in; cloud
is opt-in, never required.

```
 ① discover     ② literature        ③ projects               ④ report
┌──────────┐  ┌──────────────┐   ┌────────────────────────┐  ┌──────────────┐
│ Zotero    │─▶│ Steward       │─▶ │ project portfolio       │─▶│ Lectern       │
│           │  │ govern·tag·KB │   │ (Steward portfolio)       │ │ editable .pptx│
└──────────┘  └──────────────┘   └────────────────────────┘  └──────────────┘
                     │                        │                      ▲
                     ▼     one Obsidian vault │                      │ handoff pkg
            Literature/  Projects/  Conversations/                   │
                     └───────────────┬────────────────┘              │
                                     ▼                               │
                  ⑤ Provenance — local memory hub ───────────────────┘
                  indexes conversations + literature + projects
                  → MCP: get_portfolio() / get_current_context() / search_brain()
                  any AI session opens already knowing what you read & what you're doing
```

Four capabilities, one hub: **information analysis · literature management ·
project tracking · reporting**, with a local AI **memory hub** as
the relay between them — captures stream in automatically through an
**event-driven sync layer**, then you fold them into memory on demand with a
single command, with literature reviewed on demand and a mobile front door for
remote capture.

| Tool | Role | Status |
|---|---|---|
| [**Steward**](https://github.com/scriptorium-suite/steward) | Zotero library **governance** (backup → audit → propose → review → apply → rollback; AI tags / TLDR / reading status; KB + Obsidian export) **and project portfolio** (`portfolio` → dashboard). Now also drives **on-demand literature automation** (`steward review --since-days N` → `export` → ingest → `Reviews/` what's-new digest; optional weekly opt-in). Dry-run-first, journaled, never clobbers. | live (M3) |
| [**Provenance**](https://github.com/foxsplendid/Provenance) | Local AI **memory hub**: captures all your AI conversations (chat + coding agents) → redact/pseudonymize → organize → profile, **and ingests your reference library + project portfolio**. Now the consumer of the **event/sync layer** (notes, session summaries, openclaw drops). Read-only MCP server feeds it to any session. Pure stdlib, no network. | live (Phase A) |
| [**Lectern**](https://github.com/foxsplendid/Academic-Slides-Agent) | Academic slides: paper PDF **or a Scriptorium handoff package (single/multi-paper)** → evidence pool → human-approved outline → native editable `.pptx`. | live (Phase E) |
| [**scriptorium-spec**](https://github.com/scriptorium-suite/scriptorium-spec) | The data contracts that hold it together: `library-kb`, `proposal`, `handoff` (1.1 multi-paper), `project`, plus `note` (1.0) and `session-summary` (1.0) for the event/sync layer, and the vault-layout · sync-layer · literature-automation · Obsidian-export · config-root conventions. | v2.0.0 |

## How they connect

Tools never call each other's internals — they exchange **files** specified in
`scriptorium-spec`, in one shared Obsidian vault:

- **Steward** governs Zotero and writes `Literature/` (notes) + refreshes the
  `Projects/` dashboard (总纲), and on demand
  (`steward review --since-days N`) refreshes the KB and emits a `Reviews/`
  what's-new digest — with an optional weekly opt-in for users who want it
  automated.
- **Provenance** reads that vault + a `library-kb` export and folds it into a
  local memory layer; its MCP `get_portfolio()` / `get_current_context(project)`
  let any Claude session open already knowing your projects and
  which papers you've read.
- **Lectern** consumes a `handoff` package staged by Steward `pick` to build a
  deck from one or several papers.

You work each project in its own session; the dashboard + memory hub keep the
cross-project picture so nothing siloes.

## Stays in sync — the event / sync layer

The connective tissue that keeps the OS current: **capture is event-driven and
always-on**, while **processing is an on-demand pull** — you fold recent
captures into memory by running one command, `sync-run -Mode once`, whenever you
choose (no daemon, no scheduled tasks, no watchers, no periodic popups). Three
event-driven front doors auto-enqueue into **one serializing worker**, which on
that pull writes to two destinations:

```
 desktop  Obsidian save   ─┐
 agent    CC SessionEnd    ─┼─▶  enqueue ─▶ single serializing worker ─┬─▶ ① Provenance memory (SSoT)
 mobile   openclaw inbox   ─┘    (queue)    (lockfile · append-only     └─▶ ② project-note progress-log
                                             · journaled · idempotent)       (append-only marker region)
```

- **Three front doors, one memory.** Desktop (Obsidian), AI sessions (Claude
  Code `SessionEnd` hook), and mobile (openclaw) all auto-enqueue into the same
  authoritative store; the worker drains the queue on demand, short-lived —
  no daemon, no scheduled tasks, no watchers, no sub-second polling.
- **Safe by construction.** Triggers only *enqueue*; the single worker holds a
  lockfile and only ever appends inside tool-owned regions, never overwriting
  your hand-authored content. Every write is journaled and reversible; every
  entry is idempotent.
- **One manual touch.** Raw timelines apply automatically; high-value claims
  (`status` / `stage` / `next_actions` / `conclusion` / `blocked_by`) land in a
  hybrid approval queue (Obsidian + CLI) for a one-click confirm.
- **openclaw — least-trust mobile remote.** A mobile front door that drops
  captures and triggers actions into an isolated inbox only; it reads project
  state read-only and never writes to your authoritative files
  directly.

This runs on the `note/1.0` and `session-summary/1.0` contracts (produced by the
host sync layer, consumed by Provenance) frozen in `scriptorium-spec` v2.0.0.

## Principles

1. **Local-first.** Your unpublished work never needs to leave your machine.
2. **Files as contracts.** Everything flows through versioned, documented
   exchange files anyone can produce or consume — including you with a text
   editor.
3. **Capture automatically, fold in on demand.** Sessions, notes, and library
   are captured on their own — event-driven and append-only — and you fold them
   into the memory hub with one pull (`sync-run -Mode once`) whenever you
   choose. Capture never relies on "I'll do it manually" (and forget);
   assembling the picture is a single deliberate command, not a daemon.
4. **Safe writes.** Backup-gated, dry-run by default, journaled, reversible.
   Proposals are offline artifacts you review — not chat bubbles you approve in
   a hurry.
5. **Privacy by default.** Personal profiling stays session-local; the memory
   hub never phones home, and a pseudonymized layer is what downstream reads.
6. **Zero lock-in.** Plain Python (stdlib-only cores), plain Markdown, plain
   JSON. If Scriptorium disappears tomorrow, your data is already yours.

> *A scriptorium was the room in a medieval monastery where manuscripts were
> copied, kept, and passed on. Same idea, better tooling.*

## Trust & Safety

You're trusting Scriptorium with two sensitive things — your **unpublished
research** and your **cross-AI conversation history** — both kept on your own
machine. Here is how it earns that, by theme:

- **Safe writes.** Steward is dry-run by default, refuses to write without a
  recent verified backup, journals pre-state before every write (reversible
  `rollback`), and *never deletes* — its whole write surface is two POST
  endpoints touching only `collections`/`tags`/`extra`. The memory hub's
  writebacks are append-only inside a frozen marker region (your prose is never
  overwritten), journaled, single-writer-locked, and idempotent.
- **Privacy.** Secrets and PII are redacted at *ingest*; the names you list are
  pseudonymized via a re-id key (`aliases.json`) that stays local and
  git-ignored. Your personal profile never enters cross-session memory, and the
  MCP server is read-only.
- **Local-first.** Stdlib-only cores, no telemetry. Steward's read paths need no
  credentials; Lectern's compiler is offline and deterministic. The one
  configurable egress is the LLM call *you* configure — point the OpenAI-compatible
  adapter at a local endpoint to keep prompts on your hardware.
- **Human-in-the-loop.** Steward proposals are offline files you review (not chat
  bubbles); the memory hub gates high-value claims behind an Approvals queue;
  Lectern's web flow has a mandatory human outline-approval gate.
- **Least-trust edges.** The mobile remote (openclaw) writes to one isolated
  inbox only, reads everything else read-only, and its proposals merely *stage* a
  human-gated draft — never auto-applied, never running code.

**Honest limits** (the doc spells these out): the LLM call leaves your machine
*by default* unless you point it at a local endpoint (the Anthropic path always
does); redaction is pattern-based and can miss novel/free-text PII; openclaw's
containment rests on host filesystem ACLs you apply manually, not the tool layer;
and the runtime validator is structural, not a full schema engine.

Every guarantee above traces to a concrete mechanism in code. Full reference,
with per-tool citations and the complete limits section:
[**scriptorium-spec/specs/trust-model.md**](https://github.com/scriptorium-suite/scriptorium-spec/blob/main/specs/trust-model.md).

## Related / 相关文档

**Suite / 套件:** [scriptorium-spec](https://github.com/scriptorium-suite/scriptorium-spec) (contract SSoT) · [steward](https://github.com/scriptorium-suite/steward) · [Provenance](https://github.com/foxsplendid/Provenance) · [Academic-Slides-Agent / Lectern](https://github.com/foxsplendid/Academic-Slides-Agent) · [.github](https://github.com/scriptorium-suite/.github)
> This org profile mirrors the contract/tool table canonical in scriptorium-spec/README — the spec repo is the source of truth.
