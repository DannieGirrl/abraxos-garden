# CLAUDE.md — DTC

## Resuming a session (read this first)

**Do not rely on memory or on a summary Danielle pastes in.** Memory is a point-in-time snapshot and
goes stale; the code is the truth. Before saying anything about the state of this app:

1. `git log --oneline -15` and `git status --short` — what shipped, what is uncommitted.
2. Grep the actual file for the feature in question rather than recalling whether it exists.
3. Read `~/.claude/projects/C--Users-dchambers-Documents-DTC-life-management-app/memory/MEMORY.md`
   for *why* decisions were made — that is what memory is good for, not for current state.
4. The live plan is `~/.claude/plans/snug-twirling-parnas.md`.

Danielle should never need a magic phrase. "Check the state of the app and tell me what's left" is
enough, and answering it means actually looking.

**Her side of the work** (things Claude cannot do) is tracked in memory under `deploy_and_migration`.
Check it before asking her for something she has already been asked for twice.

## What this is

**DTC** — Danielle's personal life-management PWA. Single user, single file, no build step, no
framework. Five tabs: **Now · People · Life · Care · Learn**, plus `financial.html` and `meals.html`
as sub-pages.

## Where the files are

**The live app is HERE.** Sibling projects are indexed in `../CLAUDE.md`; their design systems are
deliberately different, so do not carry conventions across.

| File | Role |
|---|---|
| `index.html` | The app |
| `app.html` | **Byte-identical copy.** `cp index.html app.html` then `cmp -s` before every push |
| `financial.html` | The money page and the gold hoard |
| `meals.html` | Meals and shopping |
| `design-preview.html` | Throwaway design mockup, fake data. Not the app. |
| `assets/` | Reference art used only by the mockup — the real app uses **no image files** |

## Deploy

**Vercel is home: `https://dtc-life-app.vercel.app`**, auto-deploying from GitHub
`DannieGirrl/abraxos-garden` on push, live in ~20s. Claude can `git push` directly; credentials are
cached. Verify a deploy by curling the URL with `--ssl-no-revoke` (the sandbox fails TLS revocation
checks otherwise) and grepping for the new code.

## Data

`localStorage` only, no backend. Local dates via `isoDate()` — **never `toISOString()`**, which is a
day ahead all evening in Seattle and has already caused one silent bug. Keys include `day_YYYY-MM-DD`,
`lm_people`, `lm_rhythms`, `lm_energy`, `lm_xp`, `lm_pb`, `lm_walks`, `lm_letters`, `lm_wx`,
`fin_hoard`, `flowers`, `diamonds`.

There is an in-app **Back up my data** button; it exports every key. Use it before anything risky.

## Theme system

Twelve themes: four seasons × day/dusk/night, **light by day and dark at night**, driven by real
sunrise/sunset from a keyless Open-Meteo call. `applyTheme()` works by **overriding the existing token
names** (`--ivory`, `--white`, `--espresso`, `--line`…) rather than touching the 250+ `var()` call
sites. To restyle, change what a token *means*; do not rewrite usages.

A handful of rules bake in pale backgrounds and need `html.dark` overrides — check for that whenever
adding a card with a hardcoded color.

## Design laws — these are settled, do not relitigate

- **No mascot.** Bear, fox and princess all went stale the same way: a character *reflects* state she
  already knows, so it carries no information. The hero is the **constellation** — permanent stars.
- **Nothing on screen may go down.** No losable streaks, no decaying meters. Personal bests and
  trophies only accumulate. Below-baseline weeks say *nothing* rather than reporting a decline.
- **No weight, measurements or photos.** Ever. Frame movement as capability and attention.
- **No high-impact movement by default.** Opt-in only.
- **Ceremony over efficiency, and it must end.** Rituals wait for her, every step is skippable without
  penalty, and the final screen has exactly one button.
- **Cartoon, not realistic** — bold outlines, saturated flat fills. Claude cannot generate images;
  hand-written SVG matches the palette by construction and reseasons itself.
- **Propose boldly and render it. Do not poll her with multiple-choice questions.** She designs better
  than she specifies, and reacts to pixels, not descriptions.

## Testing

No test runner. Verify logic with a Node harness: extract the `<script>` block, stub `localStorage`,
`document`, `navigator` (use `Object.defineProperty` — Node 22 defines a read-only `navigator`), then
call functions directly. `const` and `let` at the top level of the script are **not** reachable from
outside the eval; only `function` declarations are, so test through the setter functions.

Visual checks at **390px** (iPhone 14 Pro). Claude cannot see the rendered page — say so rather than
claiming a visual result.
