---
name: writing-technical-docs
description: Use when asked for a technical document that will be published as an Artifact page — a design doc, LLD, HLD, RFC, architecture writeup, repo or service documentation, onboarding guide, runbook, or a written proposal an engineering team will read
---

# Writing Technical Docs

## Overview

A technical doc earns its place when a reader finishes it able to make a decision or change code they could not before. Restating what the code already says is worse than writing nothing — it goes stale and it costs review time.

This skill supplies the house style (palette, type, page composition) and the content structure for engineering documents. Diagrams have their own skill; this one decides when to call it.

**REQUIRED BACKGROUND:** load `artifact-design` before writing the file.

**When a diagram would show a mechanism prose can't:** invoke `drawing-workflow-diagrams`. Data flowing between services, a request's state machine, a before/after of two designs, a retry path — those earn a picture. A three-box relationship a sentence covers does not.

## When to Use

- "write an LLD / HLD / design doc / RFC for this"
- "document this repo / service / module", onboarding guides, runbooks
- A written proposal or recommendation an engineering team will read and act on

**Do not use for:** code comments and docstrings; a chat answer that fits in the terminal; user-facing marketing pages; slide decks.

## Content structure

Order by what the reader needs, not by how the code is laid out.

| Section | Carries |
|---|---|
| **Context** | Why this exists — the problem, what prompted it, what changes if nothing is done. First, always. A doc that opens with architecture makes the reader hold everything in suspense |
| **The mechanism** | How it actually works: what calls what, what crosses a boundary, in what shape. Prose plus one diagram, not an inventory |
| **Interfaces and contracts** | What each boundary promises. Payload shapes, endpoints, table schemas, event types |
| **Invariants and failure modes** | What must stay true, what happens when it doesn't, who retries. The half most docs skip and every incident needs |
| **Source map** | `path → what it does`. Engineers open this first, so make it a real table with real paths |
| **Out of scope** | Named explicitly. Unstated scope is the most expensive ambiguity in a design doc |
| **Open questions** | Each with the decision it blocks. "TBD" alone is not an open question |

Scale each section to its complexity — a sentence where a sentence suffices. Drop any section that has nothing true to say; an empty heading is worse than a missing one.

## Common Mistakes

| Mistake | Fix |
|---|---|
| A section per class or file | The doc restates the code and rots on the next refactor. Describe the mechanism; put paths in the source map |
| Architecture before context | Lead with the problem. Nobody can evaluate a design they don't know the purpose of |
| Numbered sections that aren't a sequence | Number a real order (a pipeline, a migration). Otherwise label by owner, subject, or scope |
| Passive voice hiding the actor | "The message is deleted" → "the worker deletes the message". Docs are about who does what |
| Prose where a table belongs | Any time three or more items share the same fields, it's a table |
| Claims not traced to code | Read the file before asserting what it does. The source map is where the receipts go |
| "TBD", empty sections, placeholder text | Fill it or cut it before publishing |

## The style

**Encodings carry meaning and stay fixed; hues and typefaces are a default you can swap.**

Copy `doc-kit.css` into the page's `<style>` verbatim, then build on it.

| Element | Choice |
|---|---|
| Palette | Greenbar continuous-form paper. Ground `#F2F4EF`, accent `#2E6B4C` (success, the recommended path), amber `#94590F` (failure, risk, removed), neutrals biased green |
| Type | IBM Plex Serif 600 headings · IBM Plex Sans body · IBM Plex Mono every label, table header, caption, path and code span |
| Sections | Kicker (mono, uppercase, names the owner or scope) → serif `h2` → one prose line → the content |
| Tables | `.src` for source maps and reference rows; mono in the first column, `tabular-nums` wherever digits align |
| Callouts | `.notes` definition list for the two or three facts that don't fit the flow |
| Prose width | Running text near 68ch. Tables and code go full width in their own `overflow-x: auto` container |

Themes: define the full light palette on bare `:root`, redefine tokens under both `@media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) }` and `:root[data-theme="dark"]`. Never define a colour only inside a media or `[data-theme]` block.

## Make it yours

Three edits rebrand every page this skill produces.

1. **Colours** — replace the values under bare `:root` in `doc-kit.css`, then the same token names in both dark blocks. Keep the names; every rule downstream reads them.
2. **Typefaces** — swap the Google Fonts `<link>` and the `--sans` / `--serif` / `--mono` tokens. Keep a real fallback stack on each.
3. **Nothing else.** Classes, layout and encodings are palette-independent.

Two rules survive any rebrand: `--accent` means success or the recommended path and `--amber` means failure, risk or removal — never spend either on decoration; and neutrals should carry a slight hue bias toward your accent, because a pure mid-grey reads as unconsidered.

## Page composition

1. **Masthead** — mono eyebrow (project · doc type), serif `h1` naming the subject in 2–4 words, one-sentence deck saying what the doc is for, and a `.facts` rule-bounded row of the counts that frame the problem.
2. **Context** — two or three sentences. Not a summary of the doc; the reason it exists.
3. **The body** — kicker → `h2` → prose → table, figure or code, repeating.
4. **Source map** — last substantive section.
5. **Footer** — what the doc was traced against (branch, commit, date) and what is not yet implemented.

## Verify before publishing

- [ ] Every claim traced to a file actually read, and the paths in the source map are real
- [ ] Context appears before architecture
- [ ] No section restates code that the source map already points to
- [ ] Tables where three or more items share fields; prose only where it earns its place
- [ ] Both themes resolve: no colour defined only inside a media or `[data-theme]` block; `body` background from a token
- [ ] No "TBD", no empty sections, no placeholder text
- [ ] Any diagram was drawn with `drawing-workflow-diagrams` and shows a mechanism, not an inventory
