# Unit Ranger

A single-file math practice game for 3rd graders, built around the six modules of
**Eureka Math² Grade 3**. Six trail stations, one per module. Eight freshly generated
questions each. No build step, no dependencies, no accounts — open `index.html` and play.

## The stations

| # | Station | Module | What it drills |
|---|---------|--------|----------------|
| 1 | Equal Groups Camp | Multiplication and Division with Units of 2, 3, 4, 5, and 10 | Arrays, equal groups, both interpretations of division, unknown factors, commutativity |
| 2 | Metric Ridge | Place Value Concepts Through Metric Measurement | Place value to 10,000, rounding to tens and hundreds on a number line, kg/g, L/mL, km/m, m/cm, add and subtract within 1,000 |
| 3 | Hard Facts Hollow | Multiplication and Division with Units of 0, 1, 6, 7, 8, and 9 | The harder facts, the nines pattern, ×0 and ×1, multiples of ten, break-apart (distributive) strategy |
| 4 | Square Unit Flats | Multiplication and Area | Counting square units, area from side lengths, missing side, splitting a rectangle, area vs. perimeter |
| 5 | Fraction Falls | Fractions as Numbers | Naming fractions from a bar, locating fractions on a number line, comparing same-numerator and same-denominator, equivalence, n/n = 1, counting unit fractions |
| 6 | Surveyor's Point | Geometry, Measurement, and Data | Telling time, elapsed time, quadrilateral attributes, perimeter, reading scaled bar graphs |

Module titles follow the published Great Minds scope and sequence for Eureka Math² Grade 3.

## How it plays

- Pick a station from the trail map or the module cards.
- Eight questions. Most are multiple choice; some use an on-screen number pad.
- Every answer — right or wrong — shows the **strategy**, not just the answer.
  Miss `9 × 7` and it says *"Think 10 × 7 = 70, then take away one group of 7."*
- Missed questions are collected into **Trail notes** on the results screen. That's the
  page worth reading with your kid.
- Stars: 3 for 8/8, 2 for 6–7, 1 for 4–5. Eighteen stars fills the trail.

Questions are generated at runtime, so a station never serves the same eight twice.

## Accessibility and input

- Mouse, touch, or keyboard: `1`–`4` select a choice, digits and `Backspace` drive the
  number pad, `Enter` advances.
- Every figure carries an `aria-label` describing what it shows.
- Light and dark themes, following the OS setting.
- Honors `prefers-reduced-motion`.

## Running it

Open `index.html` in any modern browser. That's the whole install.

To serve it locally:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

Progress is stored in the browser's `localStorage` under `unitranger.v1`, so it stays on
whichever device it was played on. **Reset trail** in the header clears it.

The only network request is to Google Fonts. Offline, the page falls back to system
fonts and plays exactly the same.

## Structure

Everything lives in `index.html`:

- **Figure helpers** — `dotArray`, `areaGrid`, `numberLine`, `fracBar`, `twoBars`,
  `clockFig`, `barGraph`, `shapeFig`, `perimFig`, `pvDisks`. Each returns an inline SVG
  string that inherits the page's theme tokens.
- **Question banks** — `M1` through `M6`, one array of generator functions per module.
  A generator returns `{q, sub, fig, choices, answer, why}`, or `{type:'input', ans}` for
  number-pad questions.
- **State** — the trail map, the quiz loop, and the results screen.

### Adding a question type

Push a generator onto the module's bank:

```js
M1.push(() => {
  const a = pick([2, 3, 4, 5, 10]), b = R(3, 9);
  return {
    q: `${a} × ${b} = ?`,
    sub: 'Skip-count to check.',
    fig: dotArray(a, b),
    ...mc(a * b, [a + b, a * b + a, a * b - b]),  // correct value, then distractors
    why: `${a} groups of ${b} is ${a * b}.`
  };
});
```

`mc(correct, distractors)` builds four unique non-negative integer options, shuffles
them, and returns `{choices, answer}`. `mcFrac(correct, pool)` does the same for
fractions given as `[numerator, denominator]` pairs.

## Notes

This is an independent practice tool. It follows the Eureka Math² Grade 3 sequence so it
lines up with what a class is working on, but it is not affiliated with or endorsed by
Great Minds PBC. "Eureka Math" is their trademark.

## License

MIT — see [LICENSE](LICENSE).
