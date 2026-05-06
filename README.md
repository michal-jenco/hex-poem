# nyxphaea — hex decoded

A single-page interactive decoder for an Instagram story by [@nyxphaea](https://instagram.com/nyxphaea) that encoded a gothic poem as ASCII hex bytes. Renders the hex grid alongside the decoded poem with a heavy glitch / dark-purple aesthetic.

**Live:** https://michal-jenco.github.io/hex-poem/

The whole thing is a single self-contained `index.html` — no build step, no dependencies beyond two Google Fonts.

---

## Interactions

### On the byte and word level

| Action | What happens |
| --- | --- |
| Hover a hex byte | Scrambles into its decoded character with a glitch-flicker; the corresponding word in the poem lights up red |
| Hover a poem word | Scrambles into its space-separated hex bytes; clones of those bytes arc out from the hex panel toward the word and fade back to origin |
| Click a hex byte or poem word | Freezes it in its current state (dashed red outline). Click again to unfreeze. Frozen elements are ignored by every other mode and effect |
| Click the title ("The Conduit Speaks") | Triggers a stanza-by-stanza retransmission: each stanza's words scramble in sequence, then decode again |

### On page load

- Everything renders **scrambled junk** at first
- An immediate top-left → bottom-right wave decodes whatever is in the viewport over ~1.6s
- Anything below the fold decodes via `IntersectionObserver` as you scroll into it

---

## Modes (togglable)

A bar at the bottom-center of the page (dim until hovered) toggles three modes. State persists across reloads via `localStorage`.

### `heat zone` (default off)

Only bytes/words within ~200px of the cursor stay decoded. Everything else re-scrambles to junk. Move the cursor like a flashlight in a dark room. Per-element 320ms cooldown prevents flickering at the threshold.

### `cipher rain` (default on)

Full-viewport canvas with falling hex/glyph columns at varying speeds and alphas, screen-blended over the page. Each glyph randomly mutates as it falls. Slight purple → pink chromatic offset per character.

Occasionally a column drops out of pure-noise mode and spells a thematic word from the poem vertically (top-to-bottom in the trail). Word columns are slightly larger, brighter, and haloed by a soft vertical flare. Very rarely (~1 in ~28 word events), the author handle `nyxphaea` surfaces — those columns crawl much slower, render in ivory-pink with chromatic aberration, and get a wider, brighter flare that lingers ~7s.

### `drag corrupt` (default on)

Click + drag inside either panel to draw a selection rectangle. Any byte or word the rectangle touches scrambles back to junk and gets a red linked-flash. Linked counterparts in the other panel corrupt simultaneously. Each touched element self-heals after a jittered 1.6–3.6s delay so the smear fades organically. Defers to heat-zone if both are on.

---

## Ambient effects (always on)

- **Cursor scanlines** — a horizontal scanline tracks your cursor Y and a vertical one tracks your X; they cross at the cursor with chromatic-aberration glow and a slight repeating-line texture (CRT vibe)
- **Cross sparkles** — every ~80px of cursor travel, a small radial pop spawns at the intersection with a triangle-wave chime
- **Phosphor trail** — every ~80ms a fading horizontal ghost line drops behind the cursor with random Y jitter
- **Background corruption blocks** — small striped flicker rectangles spawn at random positions every 1.4–3.8s (sometimes in small clusters), each ~220ms
- **Margin ghost text** — fragments drift up the viewport edges. Each picks a thematic word from the poem and cycles three phases during its drift: **hex bytes → scrambled glitch → ASCII word** (e.g. `6C 6F 76 65` → `╫░§∴` → `love`), with re-rolls between cycles. The author handle `nyxphaea` is in the pool and gets distinctly poetic visuals when it surfaces — italic serif font, ivory-pink glow, slower drift, longer lifetime, and a brightness throb
- **Phantom characters** — every scramble animation adds a chromatic text-shadow and 0.4px blur, giving each character a motion-blur trail
- **Title glitch** — chromatic ghost layers (red + blue) flicker over "The Conduit Speaks" in irregular bursts at four beats per cycle, while the real title stays fully readable

---

## Audio

Web Audio synthesizes a different sound for every category of scramble. Master gain is low; a global ~35ms cooldown prevents wall-of-noise during cascades.

| Category | Sound |
| --- | --- |
| `letter` (printable chars) | Bandpass-filtered noise burst |
| `control` (`0A` newlines) | Low sine thump (110 → 40 Hz) |
| `punct` (`.`, `'`, `:`) | Crisp square-wave click |
| `space` | Soft lowpass puff |
| `cross` (scanline sparkle) | Triangle-wave chime |

Browsers require a user gesture before the AudioContext can play — the first pointermove / click / keypress initializes it.

---

## Source

The poem was encoded as 41 lines of 8 hex bytes each (328 bytes total), decoded as plain ASCII. Hex source is embedded as the `raw` array near the top of the script in `index.html`.

```
54 68 65 20 43 6F 6E 64    →   "The Cond"
75 69 74 20 72 65 74 72    →   "uit retr"
65 61 74 73 20 74 6F 20    →   "eats to "
...
```

---

## License

The code is freely usable. The poem itself belongs to [@nyxphaea](https://instagram.com/nyxphaea).
