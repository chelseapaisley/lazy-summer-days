# Lazy summer days

I spent some spare time building a swimming pool on the internet.

Not a metaphor: a top-down, ambient browser animation of a backyard pool. Water gradient, caustic light rippling across the tile floor, a handful of floaties drifting slowly in the simulated breeze. The kind of thing you'd stare at for way too long if it was playing on a screen in a hotel lobby.

[See it live 🌞](https://chelseapaisley.com)

## Why this exists

I'd already been putting AI to real use — automating the boring parts of getting a PM, digging through the codebase in Cursor, scripting my way out of busywork. Then an [animated koi pond](https://www.linkedin.com/posts/riu-fukazawa-a2277a21b_i-built-a-koi-pond-on-the-internet-and-you-activity-7464804999248711681-MRxJ) scrolled past on LinkedIn and something clicked: this could make something simple and beautiful too, not just efficient. It was the start of summer, I was already nostalgic for long lazy pool days, and that was enough of an excuse. I decided to just make it real.

I could describe the target with total precision: stylised but grounded, not photorealistic, not cartoon, somewhere between a graphic poster and a memory you're not sure is real. What I couldn't do was write a Worley-noise caustic shader myself. So I didn't. I wrote the brief the way I'd brief an engineer, then kept sending it back until the details actually held: water that looked alive instead of like a screensaver, a bite that looked torn instead of erased.

If you're here to review my JavaScript, this'll disappoint you. If you're here to see how I actually work, keep going.

## Calls I made along the way

- **Knew when to stop.** Late-stage tuning — entrance timing, exact collision shapes — hit real diminishing returns: more precision meant worse performance, for gains nobody would notice. Called it and shipped.
- **Cut the noodle.** Built a seventh toy — a pool noodle — and it just wasn't good enough next to the other six. Cut it rather than trying to save it.
- **Caught a physics bug that took way too long to track down.** Overlapping toys would randomly shudder instead of pushing apart smoothly — subtle, easy to miss on a quick look, but exactly the kind of thing that makes a finished-looking thing feel unfinished. Traced the actual cause and fixed it properly rather than masking the symptom.
- **Blur value came from a device test, not a guess.** Caustic highlights looked jagged at the pixel level, but the smoother they got the more performance suffered. Tested 0/0.5/1/2px side by side on a real device — 2px softened real structure, 1px stayed sharp while calming the aliasing.
- **Toys started as six separate files, merged into one shared `Toy` class later.** Fast to iterate on one floatie solo early on; worth consolidating into a single pipeline once the shapes stopped changing.

## What's actually here

This is a single self-contained page — `pool.html` — and it's what's actually running on my personal site. No build step, no dependencies, no other files: water, floor, and everything on top of it lives in one file.

Six floaties drift across the surface: a donut with a bite taken out, a watermelon donut, a pastel striped one, a swan, a flamingo, and an inflatable lounge chair. Every toy shares the same underlying approach: its own shadow, rendered separately and pinned to a fixed offset so it reads as consistent overhead sun rather than spinning with the toy above it.

## The stack, such as it is

Canvas 2D, no frameworks, no build step. One Web Worker doing the caustic math so the render thread never blocks — the main loop just composites whatever frame the worker last finished. Collision detection handles circles, capsules, boxes, and triangles depending on the toy's shape, with a point-sampling fallback for anything irregular. Click and tap handling covers both bumping a toy and rippling the water, with real momentum transfer between toys on contact. The caustic and collision math is written as plain, self-contained functions specifically so the same code can run inside the worker, on the main thread, and inside a small regression test suite without three separate copies drifting out of sync with each other.

## Palette

| Element | Value |
|---|---|
| Pool deep | `#086b8e` |
| Pool mid | `#17a8cc` |
| Pool shallow | `#25c0d8` |
| Donut icing | `#f48fb1` |
| Watermelon flesh | `#e8384a` |
| Swan white | `#f0eeea` |
| Flamingo pink | `#f9719a` |
| Chair yellow | `#f5c842` |
| Floor shadow | `rgba(0,20,45,0.28)` |
