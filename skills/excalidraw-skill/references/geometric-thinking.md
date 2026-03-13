# Geometric Thinking — Detailed Technique Catalogs

## Technique Catalog: Illustrative Diagrams

| Visual Element | Primitive Composition | Key Parameters |
|---------------|----------------------|----------------|
| **Tiled roof** | Grid of diamonds, rows reduce by 2, interleave offset rows | tile_size, row_count, center_x |
| **Brick wall** | Grid of rectangles, alternating rows offset by half-width | brick_w, brick_h, mortar_gap |
| **Cloud** | 5-7 overlapping ellipses of varying sizes | center, radii, overlap% |
| **Tree** | Rectangle trunk + 3-4 overlapping ellipses (canopy) | trunk_w, canopy_r |
| **Fence** | Repeated thin rectangles with pointed-top triangles | post_spacing, post_h |
| **Road/path** | Two parallel lines + dashed center line | width, dash_pattern |
| **Water/waves** | Repeating sine-curve approximated by overlapping ellipses | amplitude, wavelength |
| **Sun rays** | Central ellipse + rotated lines at equal angles | ray_count, ray_length |
| **Smoke/steam** | 3+ ellipses of increasing size, ascending and drifting | size_step, drift_x |
| **Window (classic)** | Rectangle + 2 thin rectangle cross-bars (H and V) | win_w, win_h, bar_thickness=3 |
| **Stairs** | Stacked rectangles, each offset right and down | step_w, step_h, count |

## Technique Catalog: Flow Diagrams (Sugiyama-Inspired Layout)

For flowcharts and directed graphs, use the Sugiyama hierarchical layout algorithm:

**Step 1 — Layer Assignment:**
Assign each node to a horizontal layer. Entry nodes at top (layer 0), each subsequent step increases layer number. Nodes in the same layer share the same Y coordinate.

```
layer_y[n] = start_y + n * (node_height + vertical_gap)
vertical_gap: 120px minimum (for visible arrows)
```

**Step 2 — Ordering Within Layers:**
Position nodes within each layer to minimize edge crossings. Place each node at the average X of its connected neighbors in the layer above.

```
node_x = average(connected_parent_x_positions)
If two nodes overlap: spread by node_width + horizontal_gap
```

**Step 3 — Coordinate Assignment:**
Center nodes around the diagram midpoint. Distribute evenly within each layer.

```
layer_width = count * node_width + (count - 1) * horizontal_gap
first_x = center_x - layer_width / 2
node_x[i] = first_x + i * (node_width + horizontal_gap)
```

**Step 4 — Edge Routing:**
Create arrows after all shapes. Use `startElementId`/`endElementId` for binding.
- Same-layer connections: horizontal arrows
- Cross-layer connections: vertical arrows
- Multi-layer spans: consider adding routing waypoints

**Decision branches:**
```
YES path → horizontal right to answer box (same layer)
NO path  → vertical down to next decision (next layer)
```

## Technique Catalog: Architecture Diagrams (Zone-Grid Layout)

**Zone-based layout** for microservices, infrastructure, and system architecture:

**Step 1 — Define zones as large translucent rectangles:**
```
zone_width = max(services_count * (service_w + gap) + padding * 2, 400)
zone_height = rows * (service_h + gap) + padding * 2 + title_height
zone_bg = "#e9ecef", opacity = 30
```

**Step 2 — Grid-pack services within zones:**
```
col = service_index % cols_per_row
row = service_index / cols_per_row (integer division)
service_x = zone_x + padding + col * (service_w + gap)
service_y = zone_y + title_height + padding + row * (service_h + gap)
```

**Step 3 — Connect zones with arrows:**
- Solid arrows for synchronous calls
- Dashed arrows (`strokeStyle: "dashed"`) for async/event-driven
- Label arrows with protocol/method (REST, gRPC, Kafka, etc.)

**Step 4 — Layer zones top-to-bottom by dependency depth:**
```
Client layer:      y = 0
API Gateway:       y = zone_height + 80
Services:          y = 2 * (zone_height + 80)
Data stores:       y = 3 * (zone_height + 80)
```

## Technique Catalog: Isometric / 2.5D Diagrams

For infrastructure and deployment diagrams with depth:

```
Isometric grid formulas:
  screen_x = origin_x + (col - row) * cell_width / 2
  screen_y = origin_y + (col + row) * cell_height / 2

For a server rack at grid position (2, 3):
  screen_x = 500 + (2 - 3) * 60 / 2 = 470
  screen_y = 100 + (2 + 3) * 30 / 2 = 175
```

Use diamonds for floor tiles, parallelogram-approximated rectangles for side faces. Stack elements vertically (subtract from y) to show height.

## Technique Catalog: Repeating Patterns

For any repeating visual pattern (fences, grids, timelines, Gantt bars):

```
Generic repeat formula:
  element_x[i] = start_x + i * (element_width + gap)
  element_y[i] = start_y  (same for horizontal repeat)

With alternating offset (brick pattern):
  offset = (row % 2) * (element_width / 2 + gap / 2)
  element_x[i] = start_x + offset + i * (element_width + gap)
```

## Anti-Patterns for Geometric Composition

| Mistake | Why It Fails | Do This Instead |
|---------|-------------|-----------------|
| Single large primitive for complex shape | Looks flat, unrealistic | Compose from many small primitives |
| Same-row diamonds without interleaving | Visible triangular gaps | Add offset rows at midpoint Y |
| Guessing coordinates | Misaligned elements, uneven spacing | Use parametric formulas |
| Same tile size for all containers | Looks wrong at different scales | Scale tile size to container width |
| Too few primitives | Sparse, gappy appearance | Use enough tiles to achieve ≥80% coverage |
| Forgetting z-order | Background elements cover foreground | Create back-to-front: background first, details last |
