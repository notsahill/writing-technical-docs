# writing-technical-docs

Two Claude Code skills for engineering documentation that people actually read.

Ask Claude for a design doc, an LLD, an RFC, or documentation for a repo, and these decide the
structure, write it in a consistent visual system, and publish it as an Artifact page — with
hand-authored SVG diagrams where a picture shows something the prose can't.

## The two skills

| Skill | Fires on | Supplies |
|---|---|---|
| **writing-technical-docs** | design doc, LLD, HLD, RFC, repo or service documentation, onboarding guide, runbook — published as an Artifact page or written to a file in the repo | Content structure, page composition, the palette and type system (`doc-kit.css`) |
| **drawing-workflow-diagrams** | a system or request flow, swimlane, state machine, retry path, before/after — the figure itself | Swimlane geometry, encodings, inline-SVG mechanics (`diagram-kit.css`, `lane-skeleton.svg`) |

The first calls the second when a diagram is warranted. Either works alone.

## Why it produces better docs

- **Context before architecture.** A doc that opens with a component list makes the reader hold
  everything in suspense. These lead with the problem.
- **A source map, always.** `path → what it does`, with real paths, because that is the section
  engineers open first.
- **Invariants and failure modes get their own section** — the half most design docs skip and every
  incident needs.
- **Encodings that mean something in diagrams.** Solid is yours, dashed is external, accent-filled is
  a datastore, amber is failure or removal. Accent edges are the success path. Colour is never
  decorative, and every arrow carries a verb or a payload.
- **Eight geometry rules** that each cost a rewrite when missed — connectors crossing nodes, marker
  ids colliding between figures, chips placed where no arrow can reach them.
- **Both themes handled**, including the un-stamped system default that most viewers actually see.

## Install

```
/plugin marketplace add notsahill/writing-technical-docs
/plugin install writing-technical-docs@writing-technical-docs
```

Then just ask — "write an LLD for this service", "document how this repo works", "diagram the
request flow". The skills fire on their own.

Prefer not to use plugins? Copy either directory from `skills/` into `~/.claude/skills/`.

## Make it yours

The shipped look is greenbar continuous-form paper with IBM Plex — it suits systems, data and
infrastructure subjects, and will fight a consumer brand. Rebranding is three edits in
`doc-kit.css`:

1. Replace the colour values under bare `:root`, then the same token names in both dark blocks.
2. Swap the Google Fonts link and the `--sans` / `--serif` / `--mono` tokens.
3. Nothing else. Classes, layout and encodings are palette-independent.

Two rules are worth keeping whatever you choose: accent means success or the recommended path,
amber means failure, risk or removal — and neutrals carry a slight hue bias toward your accent
rather than sitting on pure grey.

## Requires

Claude Code with Artifacts enabled. The skills load Anthropic's `artifact-design` and
`artifact-diagramming` as background before writing anything.

## License

Apache-2.0
