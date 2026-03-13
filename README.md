## Data generation

### `generateValues(n, skew, mode)`

Produces `n` informativeness values. Base value depends on mode:

- **`random`**: `Math.random()` — uniform noise
- **`front`**: `1 - i/n` — linearly decreasing
- **`ends`**: `abs(i - n/2) / (n/2)` — peaks at edges, zero at centre
- **`middle`**: `1 - abs(i - n/2) / (n/2)` — peaks at centre, zero at edges

To adjust skewness, base values are raised to a power derived from the skew slider. Low skew flattens the distribution (all items similarly important); high skew concentrates it (a few items carry most of the information). Floor of 0.02 prevents zero-weight items.

## Accuracy model

All methods map bracket removed `f ∈ {0, 0.2, 0.4, 0.6, 0.8}` to:

```
accuracy = 0.3 + 0.7 * (retained / total)
```

Where `retained` is the sum of informativeness over all kept items and `total` is the sum of informativeness of all items in the original distribution. 0.3 is the chance-level floor (i.e., no worse than chance accuracy). 0.7 = max gain from full information.

### `randomRemovalSimulated(vals, fracs, nTrials)`

Performs random removal. For each bracket `f`, runs 10,000 trials: each trial picks `round(f * n)` random items to remove, sums the retained informativeness and computes accuracy. Returns the mean across trials.

By linearity of expectation, this converges to `0.3 + 0.7 * (1 - f)`, i.e., a straight line, regardless of the distribution of `vals`. You can make the distribution as skewed as you want and the simulated curve still lands on the same line w/random lesion removal.

### `seqFromStart(vals, fracs)`

Deterministically removes the first `round(f * n)` items. Accuracy computed on the remaining suffix.

### `seqFromEnd(vals, fracs)`

Deterministically removes the last `round(f * n)` items. Accuracy computed on the remaining prefix.

## Charts

- **`lesionChart`** — accuracy vs. bracket removed for all three methods.
- **`distChart`** — raw informativeness values by transcript position.

The simulation runs in a `setTimeout` so the sequential curves render immediately while the 10k-trial computation finishes in the background. Both charts re-render on any control change via `update()`.
