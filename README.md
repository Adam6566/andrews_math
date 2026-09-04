# Unit Ranger

A single-file math practice game for 3rd graders, built around the six modules of
**Eureka Math² Grade 3**. No build step, no dependencies, no accounts — open
`index.html` and play.

Every problem is **build-then-solve under test conditions**. The student reads a word
problem, works out what the array has to be, draws it, and commits. There is one attempt
per step and no coaching while they work — the same conditions as the paper test.

## How a problem works

A problem opens with a story and nothing else:

> Mr. Diaz sets out 24 chairs in 4 equal rows for the assembly.
>
> **Step 1 — Draw the array of chairs.**

No dimensions are supplied. The student has to reason that 24 ÷ 4 = 6 before a single dot
goes down, then drag out a 4 × 6 array. Later steps build on that drawing:

> **Step 2** — How many chairs are in each row?
> **Step 3** — Two more students arrive, so he adds 4 more chairs, one to each row. How
> many chairs now?

### No feedback while they work

The builder reports only what has been drawn — *"3 rows of 6"* — never the product, never
whether it's right, and never how far off it is. Nothing changes color to signal an error.
The student has to decide for themselves when the drawing is finished.

### One attempt per step

Pressing **Check** commits. The step is marked correct or not, the correct answer is shown
alongside what they drew, and the problem moves on. There is no retrying a step. The
teaching happens afterward, in the explanation at the end of each problem and in the
**Trail notes** review at the end of the station.

## The stations

| # | Station | Module | What the student builds |
|---|---------|--------|-------------------------|
| 1 | Equal Groups Camp | Multiplication and Division with Units of 2, 3, 4, 5, and 10 | Arrays worked out from a story, tape diagrams for sharing, and a follow-up operation after the total |
| 2 | Metric Ridge | Place Value Concepts Through Metric Measurement | Place value disks that need regrouping (17 tens won't fit in a column), rounding to two different places, and metric conversion before arithmetic |
| 3 | Hard Facts Hollow | Multiplication and Division with Units of 0, 1, 6, 7, 8, and 9 | Facts through 12 broken into two drawn pieces (8×10 and 8×2 for 8×12), division from a total and a row size, and week-to-day conversions |
| 4 | Square Unit Flats | Multiplication and Area | A missing side deduced from area, two-bed gardens added together, and one rectangle measured for both area and perimeter in the same problem |
| 5 | Fraction Falls | Fractions as Numbers | Shading what's *left* rather than what's named, moving a fraction to the number line, comparing and subtracting, and slices past one whole |
| 6 | Surveyor's Point | Geometry, Measurement, and Data | Perimeter from an area and one side, elapsed time crossing the hour, a missing triangle side from the perimeter, and four-step graph reading |

Module titles follow the published Great Minds scope and sequence for Eureka Math² Grade 3.

## How it plays

- Six problems per station, each two to four steps — 14 to 19 graded steps per station.
- Completed steps stay on screen, so the array from step 1 is visible while answering
  step 2. A wrong build shows the correct drawing there instead.
- A problem counts only if **every** step in it was right. Stars: 3 for 6/6, 2 for 4–5,
  1 for 2–3. The results screen also reports step-level accuracy, which is the more useful
  number for spotting where things broke down.
- Some problems carry numbers that aren't needed, the way test items do.
- Problems with any wrong step are collected into **Trail notes** on the results screen,
  each with the reasoning written out. That's the page worth reading together.

Questions are generated at runtime, so a station never serves the same six twice.

## Accessibility and input

- Mouse, touch, or keyboard. Arrow keys resize the array, `1`–`4` pick a choice, digits
  and `Backspace` drive the number pad, `Enter` checks and advances.
- Every builder has explicit `+` / `−` steppers, so nothing depends on being able to drag.
- Readouts are `aria-live` regions, so what has been drawn is announced as it changes.
- Light and dark themes following the OS setting; honors `prefers-reduced-motion`.

## Running it

Open `index.html` in any modern browser. To serve it locally:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

Progress is stored in `localStorage` under `unitranger.v3`, so it stays on whichever
device it was played on. **Reset trail** clears it. The only network request is to Google
Fonts; offline it falls back to system fonts and plays identically.

## Structure

Everything is in `index.html`.

**Builders.** Each is a `{html, init, paint, wire, ok, recap, mini}` object registered in
`BUILDERS`: `array`, `tape`, `disks`, `frac`, `nline`. `html` renders the controls, `paint`
redraws from state and reports *only what has been drawn*, `ok` decides whether the build
satisfies the step, `recap` and `mini` summarise it afterward. `solutionOf(step)` renders
the correct build for the post-commit reveal.

**Step engine.** A question is `{q, steps[], why}`. Each step is one of the builder kinds
above, or `choice` / `input`. `renderStep` draws the current step plus every completed one;
`checkBuild` and `judgeAnswer` advance the sequence.

**Question banks.** `M1` through `M6`, one array of generator functions per module. A
generator returns a fresh randomized question each call.

### Adding a question

```js
M1.push(() => {
  const shelves = pick([3,4,5]), per = pick([6,7,8]), tot = shelves * per;
  return {
    q: `A library cart holds ${tot} books on ${shelves} equal shelves.`,
    steps: [
      // note: the instruction names no dimensions — the student derives them
      { kind:'array', instruct:'Draw the array of books.',
        need:{rows:shelves, cols:per, total:tot}, max:{r:6,c:10} },
      { kind:'input', instruct:'How many books are on each shelf?', ans:per }
    ],
    why: `${tot} ÷ ${shelves} = ${per} books per shelf.`
  };
});
```

`need` takes `rows`, `cols`, and `total`; give all three so the student has to get the
shape right, not just the count. Keep `instruct` mechanical — any arithmetic in it hands
the answer over. Omit `hint` on build steps.

## Notes

An independent practice tool. It follows the Eureka Math² Grade 3 sequence so it lines up
with what a class is working on, but it is not affiliated with or endorsed by Great Minds
PBC. "Eureka Math" is their trademark.

## License

MIT — see [LICENSE](LICENSE).
