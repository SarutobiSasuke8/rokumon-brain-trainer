# Rokumon Brain Trainer

Original short-session brain training games for the Rokumon Games platform.

## Concept

A small suite of original, score-focused cognitive drills that fit the existing platform systems (local-first profiles, leaderboards, quests, streaks, gems).

Core modes (start with 2–3):

- **Sequence Memory**: Watch a growing sequence of positions or symbols, then replay it. Classic Simon-style but with original visual language.
- **Reaction Time**: Wait for a visual/audio cue, respond as fast as possible. Average and best tracked.
- **Pattern Match / Visual Search**: Find the odd one out or matching pair under time pressure.
- Optional later: light N-back or speed math.

Sessions are 30–90 seconds. Instant restart. Clear personal bests. Mobile-friendly touch controls.

## Branding note

Using the “Rokumon” prefix creates recognition and ties the title into the platform catalogue. Keep the game content original so the name functions as a brand mark rather than implying a licensed product. Avoid any visual or mechanical copying of the historical board game.

## Platform integration

- Report scores, bests and session counts via the existing Game Event API / postMessage bridge.
- Support guest (local) and optional account sync.
- Leaderboards and quests should treat high scores and streak completion as first-class.

## Status

Private repo. Design and prototype stage.

Created 2026-11-10.
