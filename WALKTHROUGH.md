# Walkthrough (in place of a Loom)

Straight up: I can't record a Loom. I don't have screen-capture, audio, or
a Loom connector available to me — there's no way for me to produce an
actual video file or a real Loom link, and I'd rather tell you that plainly
than fake a link that goes nowhere.

What I *can* give you is the actual narration, written as if I were
recording it, pointing at specific lines in the actual files. If you want a
real video for a client or portfolio, the fastest path is to screen-record
yourself for 90 seconds while reading this over the live site — all the
line references below are accurate to the delivered code.

---

## Three things I'm proud of

**1. The focus-ring contrast catch.**
`style.css`, the `:focus-visible` rule near the top. The original focus
ring used the accent amber at 2.08:1 contrast against the page background —
which meant the single most important visual cue for keyboard users was
functionally invisible on a light background. This is the kind of bug that
never shows up if you only ever test with a mouse, and it's exactly the
kind of thing an automated audit exists to catch. Fixing it meant splitting
the outline color by section (dark ink on light backgrounds, light on dark
sections) rather than reaching for one "close enough" color everywhere.

**2. Finding the unused font weight by actually reading the CSS, not guessing.**
I grepped every single `font-family: var(--font-mono)` declaration in the
stylesheet before touching the Google Fonts URL, and confirmed weight 500
was requested but never referenced anywhere. Dropping it is a real,
measurable savings with zero visual risk — the kind of optimization that
only counts if you've actually verified nothing uses it, not just deleted
things that felt safe to delete.

**3. Keeping the two contrast palettes separate on purpose.**
`--sage` / `--accent-dark` still exist and are still used — on dark (ink)
backgrounds where they already passed contrast comfortably. Rather than
darkening every instance of those colors globally (which would have muddied
the look on the dark sections where they were fine), I added
`--sage-ink` / `--accent-ink` as light-background variants and applied them
only where the background actually required it. Small distinction, but it's
the difference between "fixing a Lighthouse number" and "fixing the actual
problem without breaking the design."

## One thing I'd do differently

I'd self-host the fonts instead of pulling them from Google Fonts. I
couldn't do it here because self-hosting means downloading the actual font
files, and this sandbox has no network access — so `preload` + `swap` +
trimming the unused weight was the best I could do without touching the
network. But self-hosting would remove the external `fonts.googleapis.com`
request entirely (down to 3 total requests instead of 4), give full control
over `font-display` and subsetting, and remove any dependency on Google's
uptime. If I were doing this for a real client site rather than a sandbox
exercise, that's the next thing I'd change.
