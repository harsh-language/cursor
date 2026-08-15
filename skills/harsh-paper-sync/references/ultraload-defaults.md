# UltraLoad defaults

Apply when the workspace is UltraLoad (or the user points at that product).
Other projects: use the same process; ignore product-specific rows.

## Source defaults

- Invoke with no pin → **live working tree** (includes in-progress stages like U5).
- “Through U4” / ship commit → pin that ref; exclude later uncommitted UI.
- Respect `AGENTS.md` build stage notes and `__DEV__` guards: production Paper
  handoff omits simulator-only demo/reset chrome unless asked.

## Token sources

- `src/theme/tokens.ts` (never invent; regenerate from Figma in-app, read as-is for Paper)
- `src/theme/typography.ts`, `src/theme/textCase.ts`
- Font: Geist — confirm with `get_font_family_info` before painting type
- Missing Figma-linked asset → compose from the nearest sibling in code using
  existing tokens; never invent a parallel palette or type scale

## Paper page map

| Page | Artboards |
| --- | --- |
| Foundations | Colors · Type · Spacing and radius |
| Components | Buttons · Inputs · Log rows · Chrome |
| Onboarding | Splash · Profile · Exercises · Rest timer · Warmup |
| Work Out | Empty · Logged · Options · Add set · Delete set · Rest timer |
| Settings | Hub · Add exercises |
| History | Empty · List · Session detail · (+ Chart when live U5+ includes it) |

Doc boards: width 1200 (Foundations) / 800 (Components); padding **48**; gap **48**.
Phone flows: **390×844**; Apple status bar on all except Splash.

## Copy

Author lowercase in source; Paper shows the rendered case (`text-transform`)
so the handoff matches the simulator.

## Demo content

Prefer `docs/demo-data.md` / seed values for realistic weights, dates, and
exercise names.
