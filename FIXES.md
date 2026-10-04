# SENTINEL Fixes — 3 Oct 2026

## Root-cause summary

- **Map:** the application depended on the public MapLibre demo style and did not explicitly resize the map after it mounted inside page/tab layouts. This could leave the canvas blank or incorrectly sized. The map now uses an explicit OpenStreetMap raster style with attribution, explicit dimensions, and `resize()` after load/mount.
- **Prioritize:** the Patients page always rendered a pre-sorted copy, while there was no explicit state-changing Prioritize handler. The new handler sorts the actual patient queue by severity rank and score and immediately recomputes assignments.
- **Simulate Admission:** the previous handler only decremented a general bed in display state. It did not consume ICU capacity, update baseline capacity, recalculate hospital status, or propagate through the allocation engine. The new high-acuity simulated admission updates persistent hospital baseline capacity, ICU capacity, patient count, blood stock, status, alerts, and then drives recalculation.
- **Adapt/Recalculate:** delayed callbacks could call `recompute()` with stale React state, and repeated recomputations consumed already-projected hospital capacity again. The engine now recomputes from a persistent operational baseline and accepts explicit current-state overrides for event-driven changes.
- **Run Simulation:** previously it mainly advanced the clock. It now advances live operational ticks, resource consumption and allocation refreshes.

## Implemented updates

1. WCAG-focused contrast patch; stronger text/borders and red LIVE badges.
2. Reliable OSM/MapLibre map with explicit sizing, attribution and resize handling.
3. Simulation speed selector: 1x / 2x / 5x / 10x with one clean interval.
4. ICU metrics: total, occupied, occupancy %, 90% overflow headroom, average turnaround; values are clamped.
5. Recompute buttons refresh the complete allocation model.
6. Patient PRIORITIZE button now reorders the actual queue and reallocates.
7. SIMULATE ADMISSION consumes general + ICU capacity, updates status and activity alerts.
8. ADAPT MODEL uses current parameters and persistent baseline state.
9. RUN SIMULATION now advances operational state, not just a decorative clock.

## Run

```bash
npm install
npm run dev
```

Then open the Vite localhost URL. The live map requires internet access to fetch OpenStreetMap tiles.

## 10. Map constructor crash fix
- Renamed the `react-map-gl/maplibre` React component import from `Map` to `MapGL` so the native JavaScript `Map` constructor remains available to the allocation engine at `new Map(...)`.
- Updated the rendered map component to use `<MapGL>...</MapGL>`.
