# Sanity check — u1-homepage branch

Policy validation against known hotspots on `u1-homepage` diff. Confirms checkpoint tags match expected dispositions.

| Hotspot | File | Expected tags | Rationale |
|---------|------|---------------|-----------|
| Inline pressed color | `src/components/Warmup.tsx` L39 `{ color }` | **T1 ask** | Dynamic style on Text — design state |
| Pressed render prop | `Warmup.tsx` L32–44 | **Q1 ask** if reviewer suggests deriving pressed without render prop | Interaction timing |
| Button layout branches | `src/components/PrimaryButton.tsx` | **Q3 ask** if merge with SecondaryButton proposed | Alignment variants differ |
| Button content helper | `src/components/buttonContentLayout.ts` | **Q3 ask** if merge into buttons inline | Named rule already exists — extend, don't collapse |
| Secondary disabled opacity | `src/components/SecondaryButton.tsx` `opacity: 0.5` | **T2 ask** | Literal vs token pattern |
| WorkOutScreen wrappers | `src/screens/WorkOutScreen.tsx` session/footer Views | **Q6 ask** | Footer/scroll alignment |
| Scroll fade offsets | `src/components/ScrollFadeView.tsx` | **T3 ask** if gradient duplicated inline | Theme helper exists |
| Warmup domain logic | `src/domain/warmup.ts` | **Q9 auto**, **Q3 ask** only if duplicate with test | Pure functions — dead code safe; dup extract may auto-propose but Q3 still asks |
| Session totals | `src/domain/session-totals.ts` | **Q9 auto**, **R4 auto** for stdlib idioms | Logic layer |
| Store hydrate | `src/stores/todaySlice.ts` | **E4 auto** for no-op guards, **E3 ask** if moving hydrate to render | Hot-path sensitivity |
| Deleted Button.tsx | diff removes `src/components/Button.tsx` | **Q9 auto** for orphaned imports | Structural verify first |
| Contract test | `__tests__/components/button-content-layout.test.ts` | No change without **Q3 ask** | Tests lock layout matrix |

## Expected ask-loop volume

Low-to-moderate: Warmup T1, possible Q6 on WorkOutScreen, possible Q3 on button consolidation proposals. Domain/store files should mostly produce auto Q9/E4 findings only.

## Expected auto volume

Unused imports after Button split, string unions in domain, no-op store guards, dead exports verified via structural search.
