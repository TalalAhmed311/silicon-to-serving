# assets/ — the repo mark

The mark is a chip package with a bolt on the die: **silicon** on the outside, the **serving** path running through it. Colours come from the lesson palette in `lessons/assets/lesson.css` (`--accent` `#0d7a5f` → `--c-k` `#1f8fb8` → `--s-k` `#38c6e8`).

| File | Size | Used by |
|---|---|---|
| `logo.svg` | 512 px square (64-unit viewBox) | the README header; scales to any size |
| `social-preview.svg` | 1280 × 640 | source for the social card |
| `social-preview.png` | 1280 × 640 | GitHub **Settings → General → Social preview** (upload by hand) |
| `../lessons/assets/favicon.svg` | 64-unit viewBox | tab icon for `lessons/index.html` and every built lesson page |

The favicon lives under `lessons/assets/` because the lesson app is served with `lessons/` as its document root — a path above it would not resolve.

## Regenerating the PNG

macOS, no extra tooling. Quick Look fits an SVG to a *square* thumbnail, so the card is first padded to 1280 × 1280 and then cropped back:

```bash
sed '1s|width="1280" height="640" viewBox="0 0 1280 640"|width="1280" height="1280" viewBox="0 -320 1280 1280"|' \
  assets/social-preview.svg > /tmp/card.svg
qlmanage -t -s 1280 -o /tmp /tmp/card.svg
sips -c 640 1280 /tmp/card.svg.png --out assets/social-preview.png
```

Text is rendered with whatever system fonts are installed (`Avenir Next` / `SF Mono` on macOS, falling back to Helvetica / Menlo), so re-renders on another machine may differ slightly in metrics. Keep it lossless RGB: quantizing to 256 colours bands the gradient on the die.
