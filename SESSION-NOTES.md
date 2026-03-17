# CurvyWalls13 — Session Notes

## What this is
Fork of `df-curvy-walls` FoundryVTT module (Bezier curve wall drawing tools), originally by flamewave000, forked from N7Huntsman.

## What's been done

### Repo setup
- **Forked** from `N7Huntsman/CurvyWalls13` → `MarkPearce/CurvyWalls13` → **transferred to `rune-goblin/CurvyWalls13`**
- **Local repo moved** to `/Users/mark/Documents/repos/runegoblin/CurvyWalls13`
- **Remotes configured:**
  - `origin` → `git@github.com:rune-goblin/CurvyWalls13.git`
  - `upstream` → `git@github.com:N7Huntsman/CurvyWalls13.git`

### Files created/modified (uncommitted)
- **`module.json`** — Updated manifest/download/url/bugs/readme/changelog URLs to point to `rune-goblin/CurvyWalls13`. Replaced `{{sources}}`/`{{css}}` template placeholders with actual paths: `["src/df-curvy-walls.mjs"]` and `["css/curvy-walls-base.css", "css/curvy-walls-icons.css"]`
- **`common/Settings.mjs`** — Fetched from original monorepo (`flamewave000/dragonflagon-fvtt`). Was a shared utility missing from the fork.
- **`css/curvy-walls-base.css`** — Compiled from `.scss` (Foundry can't load SCSS directly)
- **`css/curvy-walls-icons.css`** — Compiled from `.scss`
- **`.github/workflows/release.yml`** — CI pipeline: triggers on `v*` tags, stamps version from tag into module.json, builds zip, creates GitHub release

### Symlink for local testing
```bash
ln -sf /Users/mark/Documents/repos/runegoblin/CurvyWalls13 "/Users/mark/Library/Application Support/FoundryVTT/Data/modules/df-curvy-walls"
```

## Current blocker
**Module not showing in Foundry v13's module list.** Likely causes to investigate:
1. **Compatibility field** — currently `"minimum": 12, "verified": 12.331`. Needs updating to `"verified": 13` per the v13 update guide at `/Users/mark/Documents/repos/foundry-v13-module-update-guide.md` (Step 4)
2. **Symlink** — verify it's correct and Foundry can follow it
3. **module.json validation** — confirm it's valid JSON and structurally correct for v13

## V13 compatibility issues found (not yet fixed)

### Critical — will likely break
| Issue | Location | Details |
|---|---|---|
| PIXI interaction events | `CurvyWallToolManager.mjs`, `ToolInputHandler.mjs` | Code accesses `event.interactionData.origin/destination` and `event.data.originalEvent` — v13 changed PIXI interaction system |
| libWrapper method names | `CurvyWallToolManager.mjs:348-365` | Wraps `WallsLayer.prototype._onClickLeft`, `_onDragLeftStart` etc. — handler names may have changed in v13 |

### Moderate — should fix
| Issue | Location | Details |
|---|---|---|
| `foundry.utils.duplicate()` | Multiple files (6+ usages) | Deprecated in v13, replace with `structuredClone()` |
| `foundry.utils.deepClone()` | `CurvyWallToolManager.mjs:40` | Deprecated, use `structuredClone()` |
| Direct `ui.notifications.active` manipulation | `CurvyWallToolManager.mjs:155-172` | Fragile internal API access |
| Hook name verification | `df-curvy-walls.mjs` | `renderSceneControls`, `renderControlManager`, `getModuleToolGroupsPre` need testing |

### Low priority
- `canvas.walls._forceSnap` private property access
- `PreciseText` class usage — needs v13 verification
- `canvas.walls.bezier` direct assignment

## Reference
- V13 update guide: `/Users/mark/Documents/repos/foundry-v13-module-update-guide.md`
- Security review: **clean** — no malicious code found
- No packs/compendiums in this module (it's purely a UI tool), so NeDB→LevelDB migration is not applicable
