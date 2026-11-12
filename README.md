# Rokumon Brain Trainer

Six short drills for the Rokumon Games platform, built phone-first. Each drill is one gate of the Rokumon crest. Passing gates fills the crest for the day and builds a streak.

A single self-contained `index.html`: no build step, no external images, fonts or audio files. It plays offline and inside a hub iframe.

The game makes no health or cognitive-improvement claims. Keep it that way in store listings and marketing copy.

## The six gates

Sessions are short. Leave with Back at any time. Pause hides the board and stops the clock, so a frozen frame cannot be studied. The game pauses itself when the tab or app is hidden.

| Gate | Drill | What you do | Best is |
| --- | --- | --- | --- |
| 1 Memory | Sequence | Watch a growing path on a 3×3 pad, then repeat it. It speeds up with each level. One miss ends the run. | Highest level reached |
| 2 Speed | Reaction | Five tries. Arm the panel, wait out the red, tap on mint. Early taps do not count. | Fastest single hit (the result shows the average of five) |
| 3 Attention | Pattern | Sixteen tiles, one a different colour. The gap narrows and the timer shortens each round. | Longest streak |
| 4 Focus | Count | Tap 1 to 16 in order on a shuffled grid. A wrong tap adds one second. | Fastest clear |
| 5 Sums | Quick Sums | 45 seconds of mental arithmetic with four answers. A wrong answer costs three seconds. | Most correct |
| 6 Control | Colour Clash | A colour word printed in another colour. Answer with the ink colour. 30 seconds, a wrong answer costs two. | Most correct |

Keyboard: `1` to `9` for Sequence pads, `Space` or `Enter` for Reaction, `1` to `4` for the answer buttons, `Esc` to pause.

## Daily crest and streak

- Finishing a run of a drill passes that gate for the day, whatever the score.
- Three gates in a day keeps the streak. Six fills the crest.
- The home screen shows the crest, the streak message and the last seven days.
- The main button always starts the next gate you have not passed today.

## Mobile behaviour

- The app fills the screen and never scrolls during play. Boards size themselves to the space left.
- Game inputs fire on touch-down, not on finger lift. This matters most for Reaction times.
- Answer buttons sit at the bottom, in thumb reach. Landscape puts the board on the right.
- Vibration on taps where the phone supports it (Android browsers; iOS Safari does not). It can be turned off in Settings.
- Safe-area insets are respected, and long-press menus, text selection and pull-to-refresh are suppressed.

## Storage

`localStorage`, all on this device:

| Key | Holds |
| --- | --- |
| `rbt-stats` | `{ "seq": 0, "react": null, "pat": 0, "reacts": [], "count": null, "sums": 0, "colour": 0 }`. `react` and `count` are milliseconds, lower is better. `reacts` is the last five fair reaction times. |
| `rbt-daily` | `{ "days": { "2026-11-12": ["sequence", "count"] } }`. Gates passed per local date, last 90 days. |
| `rbt-seen` | Drills whose how-to sheet has been shown once. |
| `rbt-muted` | `"1"` or `"0"`. |
| `rbt-haptics` | `"1"` or `"0"`. |

Reset in Settings clears `rbt-stats` only. The streak and today's gates stay. Version 1.0.0 saves load unchanged.

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
| Boot, and again when any drill starts | `ready()` |
| Sequence level exceeds the stored best | `score(level)` |
| Sequence path repeated correctly | `event("sequence-completed", { level })` |
| Sequence miss | `gameOver(bestLevel, 0)` |
| Reaction faster than the stored best | `score(1000 - min(ms, 999))` and `event("reaction-best", { ms })` |
| Every fair reaction hit | `event("reaction-completed", { ms })` |
| Pattern streak exceeds the stored best | `score(streak)` |
| Pattern correct tile | `event("pattern-correct", { round })` |
| Pattern miss or timeout | `gameOver(bestStreak, 0)` |
| Count cleared | `event("count-completed", { ms })`, and `score(max(0, 600 - tenths of a second))` on a new best |
| Quick Sums time up | `event("sums-completed", { correct })`, `gameOver(best, 0)`, and `score(correct)` on a new best |
| Colour Clash time up | `event("colour-completed", { correct })`, `gameOver(best, 0)`, and `score(correct)` on a new best |
| A gate is passed for the first time today | `event("gate-passed", { gate, drill, today })` |
| All six gates passed today | `event("crest-complete", { streak })` |

The calls for Sequence, Reaction and Pattern are unchanged from 1.0.0. `gameOver` reports the stored best, not only the current attempt. Leaving via Back does not send `gameOver`.

## Branding note

Using the "Rokumon" prefix creates recognition and ties the title into the platform catalogue. Keep the game content original so the name functions as a brand mark and does not imply a licensed product. Avoid any visual or mechanical copying of the historical board game, or of any commercial brain-training title.

## Files

| File | Role |
| --- | --- |
| `index.html` | The whole game |
| `game-hub-manifest.json` | Catalogue record for the hub |
