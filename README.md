# Rokumon Brain Trainer

Original short-session brain training games for the Rokumon Games platform.

A single self-contained `index.html` — no build step, no external images, fonts or audio files. It plays offline and inside a hub iframe.

## Modes

Sessions are meant to be short. Leave with Back at any time. Pause hides the board so a frozen frame cannot be studied.

### Sequence

A 3×3 pad lights a path that grows by one cell each level. Pads may repeat, including back-to-back. Wait until the path goes quiet, then tap it in order. Keys `1`–`9` work as well (left-to-right, top-to-bottom).

One miss ends the run. **Best** is the highest level reached — the length of the longest path the game has presented — stored on this device. Completing a path reports `sequence-completed`.

### Reaction

Tap the panel to arm a round. It turns red for a random 0.8–3.0s. Tap only when it turns mint and says TAP. An early tap is a miss and is not recorded. A fair hit saves the time, updates the best when it is faster, and keeps the last five for the average.

Pausing during a live round throws that attempt away so the clock cannot be frozen.

### Pattern

Sixteen tiles. One is a different colour. Tap it. Each correct pick starts the next round with the odd colour closer to the rest, down to a small remaining gap so it stays solvable. A wrong tile ends the run and rings the odd tile. **Best** is the longest streak of correct picks.

## Personal bests

Stored in `localStorage` under `rbt-stats`:

```json
{ "seq": 0, "react": null, "pat": 0, "reacts": [] }
```

- `seq` — highest sequence level reached
- `react` — fastest reaction in milliseconds, or `null`
- `pat` — best pattern streak
- `reacts` — up to five most recent fair reaction times

Mute is stored separately as `rbt-muted` (`"1"` or `"0"`) and does not affect scores. Reset from the main menu clears `rbt-stats` only.

## Hub integration

Drop `index.html` in an iframe. The hub entry stays `/games/rokumon-brain-trainer/index.html` (see `game-hub-manifest.json`). The page calls `window.Hub` when those methods exist. The first `ready` retries for a few seconds so a host that attaches `Hub` on the iframe `load` event is not missed. Later calls are immediate.

```js
iframe.contentWindow.Hub = {
  ready() {},
  score(value) {},
  event(name, payload) {},
  gameOver(score, extra) {},
};
```

| When | Call |
| --- | --- |
| Boot, and again when Sequence or Pattern starts | `ready()` |
| Sequence level exceeds the stored best | `score(level)` |
| Sequence path repeated correctly | `event("sequence-completed", { level })` |
| Sequence miss | `gameOver(bestLevel, 0)` |
| Reaction faster than the stored best | `score(1000 - min(ms, 999))` and `event("reaction-best", { ms })` |
| Every fair reaction hit | `event("reaction-completed", { ms })` |
| Pattern streak exceeds the stored best | `score(streak)` |
| Pattern correct tile | `event("pattern-correct", { round })` |
| Pattern miss | `gameOver(bestStreak, 0)` |

`gameOver` reports the stored best, matching the original contract, not only the current attempt. Leaving via Back or Reset does not send `gameOver`.

Catalogue metadata, theme and the bridge event list live in `game-hub-manifest.json`.

## Branding note

Using the “Rokumon” prefix creates recognition and ties the title into the platform catalogue. Keep the game content original so the name functions as a brand mark rather than implying a licensed product. Avoid any visual or mechanical copying of the historical board game.

## Files

| File | Role |
| --- | --- |
| `index.html` | The whole game |
| `game-hub-manifest.json` | Catalogue record for the hub |
| `README.md` | This note |
