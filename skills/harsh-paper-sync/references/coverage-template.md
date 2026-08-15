# Coverage template

Use during inventory and the final report. Keep entries short.

## Inventory

### Tokens
- [ ] Colors
- [ ] Typography (size / weight / leading / tracking)
- [ ] Spacing
- [ ] Radius
- [ ] Other visual tokens (overlay, border, icon size, …)

### Components (specimen boards)
- [ ] Buttons
- [ ] Inputs / controls
- [ ] Rows / lists
- [ ] Chrome (title bar, sheet, menu, status bar, dividers)
- [ ] _Add families from this product's inventory only_

### Flows (unique visual states only)

For each area, list artboards:

```
Area:
- Screen · state
- Screen · state
```

Mark `__DEV__`-only as skip unless requested.

## Final report skeleton

```
Source: live working tree | <git ref>
Paper file: <name / URL>
Mode: full build | incremental sync

Foundations: Colors, Type, Spacing and radius
Components: …
Flows shipped:
- …
Skipped:
- … (reason)
Blocked:
- … (MCP limit / missing asset / …)
```
