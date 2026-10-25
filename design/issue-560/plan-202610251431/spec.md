# Issue #560: foldable app screens: design spec

Source: the maintainer's "Foldables" boards (Claude Design canvas v51): DobFoldCapa, DobFoldAberto,
DobFoldClaro, DobFlip. Renders here are static exports (pt-BR strings as drawn, artwork replaced by
placeholders). The dashed yellow/brown line and its label are design annotations, never drawn by the app.

## Forbidden band
- Band = FoldingFeature bounds (window px) widened by 12dp on each side, clamped to the window.
- Horizontal fold → band is a horizontal strip (top..bottom); vertical fold → vertical strip.
- In the band: only backgrounds and pane dividers. No text, icon, image, button or touch target.
- Tabletop: the band shows the screen background with a 1dp `Oe.Surface2` hairline centred on the fold
  (light board: `#ECEAF2` strip). No content scrolls through it: top and bottom halves scroll separately.

## 01 Fold outer (Compact, 360x860)
Phone layout unchanged (bottom bar, one-column shelf). No fold → band null.

## 02 Fold open (Book / FLAT vertical fold, 840x900)
- Tablet rail (#557) 80dp + list pane + detail pane; the list | detail divider sits on the fold:
  list width = foldLeft - railWidth (here 340dp), detail = the rest (420dp).
- Each pane keeps ≥ 16dp inner padding, so nothing lands in the band (12dp each side of the fold).
- Selected row: `Oe.AccentTint` fill + 1.5dp inset accent ring, radius 14, 72dp rows.
- Detail pane: favourite + more (44dp) top right, cartridge 236dp, title 23sp, meta 13sp,
  Continue (52dp, weight 1) + Restart (116dp), Saved states + "See all (N)", 3 thumbs (66dp),
  settings card (Core / Skin / Shader rows 50dp).
- Same rules for Settings list + detail (#558).

## 03 Tabletop (HALF_OPENED horizontal fold), ROM detail, light
- Top half (0..bandTop): back link (left) + favourite / more (right) at 20dp top, 24dp sides;
  cartridge 320dp (scaled to fit the half's height) on the left with the radial glow; on the right
  overline "<SHORT> · <core>" 12sp/700 accent, title 32sp Space Grotesk, meta 14sp muted
  ("<time> played · <total>").
  Compact width (Flip): cartridge above the title, centred, scaled to the half's height.
- Band 24dp (fold ± 12dp): background only.
- Bottom half: padding 20dp top / 28dp sides; Continue (54dp, weight 1, accent) + Restart (160dp);
  "Saved states" 15sp/600 + "See all (N)"; states grid 4 columns (3 on Compact), thumbs 76dp, radius 12;
  settings tiles grid (Core, Shader, Controller skin, Cheats: today's four entries), 58dp, radius 14,
  label 12sp muted + value 15sp/600, 4 columns ≥ 600dp, 2 columns on Compact. The bottom half scrolls
  on its own when it does not fit.
- Tabletop bar (only on routes that show tabs today): 68dp, horizontal items (icon 20dp + label 13sp
  inline), active item in a 44dp pill `Oe.AccentTint` / `Oe.AccentText`, `Oe.Surface` background,
  1dp top border `Oe.Surface2`. Replaces both the bottom bar and the rail in tabletop.

## 04 Flip open, FLAT horizontal fold (412x915)
Fold FLAT → ignored: phone layout, content crosses the hinge. Only HALF_OPENED horizontal folds split.

## Gamepad
- Book split: unchanged from #558 (right from the list enters the detail, B returns).
- Tabletop: two focus groups, top then bottom; D-pad down from the last top item enters the bottom
  half, up returns; the tabletop bar is the last group. L1/R1 unchanged. Focus survives the posture
  change (restorable keys).

## Accessibility
- Panes keep their `contentDescription`s / headings; band has no semantics.
- Tabletop bar items: `Role.Tab`, selected state, 44dp min touch height.
