# Demo library art: spec (issue #513)

The app UI does not change. What is designed here is the *content* shown in every
published image: a fictional library with original, generated artwork.

## Cover (box art)
- Size 512x768 PNG (2:3), TrueColor. No rounding, no shadow (the app draws the card).
- Top band: 14% of the height, solid `band` color, era word in bold caps, letter-spacing ~0.12em,
  left padding 20px at 512 wide: `8-BIT`, `16-BIT`, `64-BIT`, `HANDHELD`, `CD`. Never a console
  or company name.
- Ground: solid `bg` color.
- Emblem: centred in the area between band and title; one of four motifs built from primitives
  (`sun`: disc + dashed ring; `peak`: two overlapping triangles; `rings`: two strokes + dot;
  `blocks`: three squares). Two foreground colors `fg`, `fg2`.
- Title: bottom-left, bold sans, ~34px at 512 wide, up to two lines, color `title` with >= 4.5:1
  contrast on `bg`.
- Font: the bold sans fontconfig resolves inside the devbox (the mock uses Rubik as a stand-in).
  No font file is downloaded at generation time.

## Label (cartridge)
- Same ground, same emblem (reduced to a single disc of `fg`), title to its right; vertically
  centred. Aspect = the label aspect of that console's cartridge frame (read from the frame's
  `label-clip` rect), so the composite fills the label without distortion.

## Palette
- Each title has its own (bg, band, fg, fg2, title) tuple, taken from the GNOME palette
  (`#1c71d8`, `#2ec27e`, `#f5c211`, `#e66100`, `#c01c28`, `#813d9c`, `#865e3c` and their shades) so
  the set looks at home next to libadwaita. Neighbouring titles in one console never share a ground.

## Titles
- Fictional, two or three plain English words ("Comet Courier", "Lantern Keep"). Checked against a
  web search before use so none matches a real published game.

## States shown
- Covers for 8 consoles, labels for 4 label aspects (see demo-library-art.png). Dark ground only:
  the art is the same in light and dark themes, the app supplies the chrome.

## Accessibility
- Title contrast >= 4.5:1. Grounds differ in lightness across a console, not hue alone.
