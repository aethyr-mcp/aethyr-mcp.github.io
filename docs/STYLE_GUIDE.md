# BlueprintReader style guide

One reference for everyone (and every system) working on this project: AI coding assistants, contributors, and artists. It collects the conventions that are otherwise easy to miss. When something here conflicts with a more specific instruction in [`CLAUDE.md`](CLAUDE.md) or a skill, the more specific one wins; otherwise, follow this.

Scope: [Writing and docs](#writing-and-docs) (for AI systems and writers), [Brand and visual identity](#brand-and-visual-identity) (for artists), [Code and commits](#code-and-commits) (pointers into the deeper docs).

## Writing and docs

These are hard rules. They apply to prose, code, comments, commit messages, UI copy, and site copy alike.

**No em dashes or en dashes, ever.** Do not use the em dash (Unicode U+2014) or the en dash (U+2013) in any output. Use a comma, colon, period, or parentheses for a break in thought, and a hyphen or the word "to" for ranges (`5 to 10`, `2026-06-17`). This is the rule contributors break most often, so check before you commit. A quick audit: search the repo for those two code points and expect zero hits in anything you wrote. (This guide names them by code point on purpose, so it stays clean itself.)

**No downstream or third-party project names.** This is an open plugin; keep its code, comments, docs, and messages free of any proprietary downstream project name. Use neutral Unreal Engine placeholders instead: `MyGame`, `a separate game project`, `MyGameEditor`, `/Game/AI/BP_Foo`. The only host-specific names that may appear are in the maintainer's own untracked build-host config (for example `CLAUDE.md`'s build commands), never in shipped plugin content.

**Do not position the BP to C++ transpile track in docs.** The transpile family (`transpile_*`, `decompile_*`, `parse_cpp_function`, BPIR) is deferred and gated off by default, so do not feature it in comparisons, marketing, the README, the site, or roadmap framing. The tools still exist in code; this is about not selling a non-shipping capability. Lead instead with the shipping moat: node-level Blueprint graph authoring, cooked/packaged-build introspection, out-of-process plus mock testing, and corruption-safety.

**Comparisons render a verdict, succinctly.** When you compare BlueprintReader to anything (for example Epic's UE MCP plugin), do not just describe what each side does. For every point, say who wins and why in one short clause. In tables, use an `Edge` column: `**Us** / **Epic** / **Even**: <one-line reason>`. See [`docs/research/epic-mcp-vs-blueprintreader.md`](docs/research/epic-mcp-vs-blueprintreader.md) for the canonical example.

**Do not hard-wrap prose.** Write each paragraph as one line and let the editor or renderer soft-wrap it. Manual mid-paragraph line breaks make noisy diffs and render inconsistently. Markdown collapses single newlines to spaces anyway, so the wrapping buys nothing. Lists and table rows are naturally one item per line; that is fine.

**Never hardcode the tool count.** The generated catalog [`docs/TOOLS.md`](docs/TOOLS.md) is the single source of truth. Refer to it; do not type a number that will drift.

**Be honest about limitations.** Report real results, including failures and skips. If a capability is headless-limited (PIE, screenshots, viewport), say so rather than implying a phantom success. Verify against a real project before asserting something works.

**Voice and tone.** Direct, concrete, and confident without hype. Prefer the specific verb to the vague adjective. Short sentences. Explain the "why," not just the "what."

## Brand and visual identity

For artists and anyone touching the site, icons, or marketplace gallery. The concept is the **Arcane Terminal**: cyberpunk neon crossed with holographic sci-fi and a thread of arcane fantasy. The source of truth is `site/css/styles.css` (`:root`); the tokens below mirror it.

**Original art only.** No Epic, Unreal Engine, or other intellectual-property assets, logos, fonts, or recognizable likenesses. Stay clear of any specific game's IP. "Unreal Engine" may be named only as a compatibility statement, with the trademark attribution already in the site footer.

### Color

| Token | Value | Role |
|---|---|---|
| `--void` | `#070710` | page background, near-black |
| `--void-2` | `#0b0b1a` | secondary background, panels behind glass |
| `--panel` | `rgba(20,22,46,.46)` | translucent "glass" panel fill |
| `--cyan` | `#1ce6ff` | primary neon accent (links, focus, the "read" idea) |
| `--magenta` | `#ff2bd6` | secondary accent (the "write" idea, text selection) |
| `--violet` | `#9b6bff` | tertiary accent (arcane depth, gradients) |
| `--ember` | `#ff8a4c` | warm accent, used sparingly for contrast |
| `--ink` | `#e8ecff` | primary text |
| `--ink-dim` | `#8b93c4` | muted text and captions |
| `--line` | `rgba(130,150,230,.16)` | hairline borders |

Dominant dark field, sharp neon accents. Do not flatten the palette into evenly weighted colors; the cyan/magenta/violet trio carries the energy against the void. Glows come from `--glow-cyan` and `--glow-mag`; keep them tight, not blurry haze.

### Typography

| Token | Family | Role |
|---|---|---|
| `--f-display` | Chakra Petch | display and headings (technical edge) |
| `--f-arcane` | Cinzel | the arcane flourish, used sparingly for ritual or "summon" moments |
| `--f-body` | Sora | body copy |
| `--f-mono` | JetBrains Mono | code, terminal output, technical labels |

Pair the technical display face with the arcane serif only at deliberate accent moments; never set body copy in Cinzel. Avoid generic system fonts (Inter, Roboto, Arial).

### Motif and motion

The sigil is `◈`. Recurring motifs: orthogonal "Blueprint wire" elbows (right-angle connectors, not curves), node and pin glyphs, a drifting node constellation backdrop, a perspective grid horizon, scanlines, and a faint film grain.

Motion is atmospheric, not loud. Animations must loop **seamlessly**: no visible snap-back at the end of a cycle (translate-preserving keyframes, mirrored marquees, constant scroll speed). Use the shared easing `--ease: cubic-bezier(.2,.7,.2,1)`. Always honor `prefers-reduced-motion: reduce` by disabling or simplifying motion. One well-orchestrated load reveal beats scattered micro-animations.

### Marketplace and gallery assets

For a Fab/marketplace submission: `Icon128.png` (128x128), gallery images at 1920x1080 or larger, and a third-party `NOTICE`. Keep every asset original and consistent with the palette and motifs above.

## Code and commits

The deep conventions live where the code does; this is a map, not a copy.

- **Building, testing, and maintaining the plugin and server:** [`CLAUDE.md`](CLAUDE.md). It covers the build commands, the backend chain, the "adding a new tool" checklist, and the gotchas learned the hard way.
- **Using the MCP tools** (wire format, per-tool guidance): the `bp-reader` skill under `Plugins/BlueprintReader/Claude/skills/bp-reader/`.
- **The live backlog:** [`docs/research/improvement-roadmap.md`](docs/research/improvement-roadmap.md). Keep it current as work lands.

**Commits.** Use a conventional prefix (`feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`) with a scope when it helps (`docs(roadmap):`). Write a body that explains the why and records verification (for example "mock suite 894/894"). End AI-authored commit messages with the co-author trailer the project uses:

```
Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>
```

**Branches and pushes.** Branch off `main` for non-trivial work unless the established flow says otherwise, and only push when asked. Do not skip hooks or bypass signing.

**PowerShell scripts.** Any `Scripts/*.ps1` that writes files through an encoding-sensitive cmdlet (`Set-Content`, `Out-File`, `Add-Content`, `Tee-Object`, `Export-*`, or a file redirection) must carry `#requires -Version 7`, or write via `[System.IO.File]::WriteAllText(..., (New-Object System.Text.UTF8Encoding($false)))`. A CI gate (`Scripts/Check-InstallerEncoding.ps1`) enforces this so a PowerShell 5.1 run cannot ship BOM or ANSI corruption.
