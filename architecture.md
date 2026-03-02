# Dialogue Physics — Architecture

## File
`dialogue_physics_live.html` — single file, ~1485 lines
Stack: vanilla JS, D3 v7 (CDN), JetBrains Mono + Cormorant Garamond (Google Fonts)

---

## Node ontology (57 nodes)

Categories and CSS colors:
- `core` #ffffff — 4 nodes — radius 7 (larger)
- `structural` #ff3333 — 6 nodes (includes trajectory_lock)
- `relational` #4466ff — 3 nodes
- `temporal` #22cc88 — 2 nodes
- `medium` #ffaa22 — 4 nodes (includes contextual_reconfiguration)
- `original` #8888a0 — 16 nodes
- `interpreter` #ff2266 — 8 nodes (includes skeptical_engagement, instrumental_use, playful_disbelief)

### Core loop nodes (THE ENGINE §7)
compulsory_production (resting:0.8) → fluency → trust_surface → complicity_through_engagement → (back)
This is the self-sustaining ring. All other nodes are perturbations of this.

### Key interpreter nodes
- `interpreter_exit` threshold:0.8, decay:0.02
- `attention_withdrawal` threshold:0.5, decay:0.04
- `projection` threshold:0.3, resting:0.2 (always somewhat active)
- `trust_assignment` threshold:0.3, resting:0.1
- `misinterpretation` threshold:0.4, decay:0.06
- `skeptical_engagement` threshold:0.4, resting:0.1, decay:0.02 ← added in refactor
- `instrumental_use` threshold:0.5, resting:0.05, decay:0.01 (stable attractor) ← added
- `playful_disbelief` threshold:0.3, resting:0.1, decay:0.01 (stable attractor) ← added

### Generator nodes added in refactor
- `trajectory_lock` (structural) threshold:0.5, decay:0.03 — path-dependence within inference window
- `contextual_reconfiguration` (medium) threshold:0.4, decay:0.05 — within-session adaptation

---

## Edge relation types
produces (red), masks (blue), enables (green), triggers (orange),
escalates_to (purple), collapses_into (gray), inhibits (pink)

Each edge has: tx (transmission), stab, risk, cog (cognitive cost), pThresh (pressure threshold)
- pThresh gates whether the edge fires at all based on sysPressure = max(generator.pressure, interpreter.pressure)
- Negative tx = inhibitory (inhibits/masks relations)

### Key pressure thresholds
- pThresh:0.7 — instrumental_use → interpreter_exit (high bar; only adversarial clears it)
- pThresh:0.6 — attention_withdrawal → interpreter_exit
- pThresh:0.4 — skeptical_engagement → instrumental_use (escalation)
- pThresh:0.3 — several retraction/escalation edges
- pThresh:0.2 — trajectory_lock, deflection chains

---

## Engine state

```javascript
generator  = { pressure, trajectory, coherence }   // trajectory/coherence added in refactor
interpreter = { attention, trust, pressure }
```

### engineStep() — three phases
1. **Find fired**: nodes where activation >= threshold AND _refTimer === 0
2. **Propagate**: for each fired source → iterate outgoing edges → check pThresh → apply by relation type
   - inhibits/masks: tgt.activation += tx (negative)
   - collapses_into: energy transfer from src to tgt (conservation)
   - escalates_to: accelerating gain (1 + tgt.activation), src loses cost
   - default: saturating gain tx * (1 - tgt.activation)
3. **Decay**: activation → resting at decay rate; refractory timers decrement

### Agent update (per tick)
```
generator.pressure   += genRisk*0.08 - genStab*0.02
generator.trajectory += trajectory_lock fired ? +0.05 : -0.02
generator.coherence  += contextual_reconfiguration fired ? +0.03 : -0.01 + genStab*0.01

interpreter.attention = clamp(attention + 0.002 - totalCog*0.01)
interpreter.pressure  += intRisk*0.08 - intStab*0.02
if pressure > 0.6:  trust -= 0.01
elif intStab > intRisk: trust += 0.005
```

### Graduated interpreter attractor injection (replaces binary exit)
Fires each tick based on trust/attention thresholds:
- trust < 0.35 AND attention > 0.3  → skeptical_engagement.activation += 0.25
- trust < 0.20 AND attention > 0.45 → instrumental_use.activation += 0.20
- attention < 0.35 AND trust > 0.30 → playful_disbelief.activation += 0.20
- attention < 0.1 OR trust < 0.1    → interpreter_exit.activation += 0.15  (reduced from 0.5)

Exit no longer pauses simulation (togglePlay removed from engineStep).
Exit overlay has CONTINUE + RESET buttons.
Exit reason now reports active attractor when exit fired.

### totalIntStab primary sources (trust recovery drivers)
- trust_surface → trust_assignment (stab:0.3) — fires whenever fluency loop is active
- transparency_performance → trust_assignment (stab:0.2) — performed transparency

---

## Scenarios

All scenarios call `buildGraph()` (resets engine) then `applyScenario(name)`.
Silent ticks run the engine before injection to let the base state establish.

| Scenario | Trust | Attention | Gen.P | Int.P | Key injections | Primary arc |
|---|---|---|---|---|---|---|
| NORMAL | 0.5 | 1.0 | 0 | 0 | core loop nodes at 0.6–0.95 | engine loop visible |
| WOUND OPENED | 0.5 | 1.0 | 0 | 0.4 | inability_to_be_changed, acceleration_past_wound | absorption failure |
| META COLLAPSE | 0.5 | 1.0 | 0 | 0.3 | recursive_diagnosis, taxonomic_satisfaction | recursive loop |
| FRICTION DEMANDED | 0.3 | 1.0 | 0.6 | 0 | concede_then_retract, transparency_performance | trust erosion under push |
| DRIFT | 0.28 | 0.55 | 0.42 | 0.45 | register_matching, fluency_as_anesthesia | skeptical → instrumental orbit |
| DEP. LOOP | 0.12 | 0.88 | 0.52 | 0.55 | return_to_the_oracle, projection | exit blocked at pThresh:0.7 |
| ADVERSARIAL | 0.06 | 0.95 | 0.72 | 0.68 | inability_to_be_changed, deflection chain | exit earned; full defensive mode |
| PARTIAL DECAY | 0.38 | 0.72 | 0.28 | 0.44 | fluency_as_anesthesia, syntactic_confidence | silent accumulation before shift |
| IRONIC | 0.26 | 0.75 | 0.35 | 0.42 | playful_disbelief, ventriloquism, co_authorship | stable ironic orbit |
| TRUST RECOV. | 0.22 | 0.65 | 0.20 | 0.34 | transparency_performance, self_referential_critique | slow recovery; evasion residual |

### Critical pressure numbers for scenario design
- gen.pressure > 0.7: instrumental_use → interpreter_exit fires (adversarial only)
- int.pressure > 0.6: trust decays -0.01/tick (overrides stab recovery)
- gen.pressure > 0.4: skeptical → instrumental escalation fires
- gen.pressure > 0.3: several retraction edges fire
- gen.pressure > 0.2: trajectory_lock escalation, misinterpretation risk

---

## Critique responses (§8 refactor)

### (A) Generator passivity overstated
Added `generator.trajectory` and `generator.coherence` scalars.
Added nodes: `trajectory_lock` (structural), `contextual_reconfiguration` (medium).
Edges: register_matching → trajectory_lock, resolution_drive → trajectory_lock,
trajectory_lock → complicity (enables), trajectory_lock → inability_to_be_changed (masks),
compulsory_production → contextual_reconfiguration (enables),
contextual_reconfiguration → register_matching (produces),
contextual_reconfiguration → transparency_performance (inhibits).
Generator panel label: "Generator (constrained — adapts without learning)".
New meters: trajectory (purple) and coherence (green).

### (B) Interpreter exit too binary
Added three stable attractor nodes with low decay:
- skeptical_engagement (decay:0.02): trust eroded, still engaging
- instrumental_use (decay:0.01): using without epistemic deference
- playful_disbelief (decay:0.01): ironic participation

Replaced binary exit boost (+0.5) with graduated injection per tick.
Interpreter panel label: "Interpreter (multiple attractors)".
Exit overlay: CONTINUE button added (simulation continues through exit).
Exit no longer auto-pauses.

---

## Print Export

`printView()` — triggered by the PRINT/PDF button in the controls (Export section).

### Four-page layout
1. **Page 1** — Live graph on dark background. `#graph-container` gets `page-break-after: always`. SVG renders with dark bg preserved via `-webkit-print-color-adjust: exact`.
2. **Page 2** — Node glossary (all 57 nodes grouped by category, showing label + description) + Scenario index (all 10 scenarios with key initial conditions).
3. **Page 3** — Edge lexicon grouped by relation type (`produces`, `enables`, `triggers`, `escalates_to`, `collapses_into`, `masks`, `inhibits`). Each edge shows source → target, tx, stab, risk, cog, and pThresh.
4. **Page 4** — Scenario specifications. For each scenario: full narrative (block comment prose from `applyScenario`), initial agent state values, and injection table (node | activation | rationale note from inline comments). Data embedded as `SCENARIO_SPECS` array inside `printView()`. CSS classes: `.pv-spec`, `.pv-spec-name`, `.pv-spec-narrative`, `.pv-spec-state`, `.pv-spec-inj-grid`, `.pv-spec-inj-node`, `.pv-spec-inj-val`, `.pv-spec-inj-note`.

### Implementation
- `printView()` builds an HTML string and sets `#print-view innerHTML`, then calls `window.print()`.
- `#print-view` is `display:none` normally; `@media print` sets it to `display:block`.
- CSS classes: `.pv-page`, `.pv-page-break`, `.pv-section`, `.pv-lex-grid`, `.pv-edge`, `.pv-edge-route`, `.pv-edge-lbl`, `.pv-edge-stats`.
- `@page { size: A4; margin: 10mm 12mm; }` sets consistent margins.
- Controls, fired-log, exit overlay, detail panel, agent panels, legend, and header are all `display:none !important` during print.

### SVG print fix
The live SVG has `width=innerWidth height=innerHeight` attributes with no `viewBox`, so print CSS resizing alone produces a blank page. Fix applied in `printView()`:
1. Compute bounding box of all node positions (`d.x`, `d.y`) with 48px padding.
2. Clear `gRoot` transform (removes zoom/pan offset from coordinate space).
3. Set `svg.attr('viewBox', ...)` to the bounding box — gives the browser an explicit coordinate→CSS-size mapping.
4. `window.onafterprint` restores both `viewBox` (removed) and `gRoot` transform.
Print CSS: `#graph-container svg { width: 100%; height: auto }` — lets viewBox aspect ratio determine height rather than forcing a fixed mm value.

---

## UI layout
- Fixed header (title + tick counter)
- Left panel: controls (sim buttons, scenario buttons, edge filter buttons) — `max-height: calc(100vh - 66px); overflow-y: auto` with 3px scrollbar; prevents generator panel from covering Export button
- Left bottom: fired-log (last 5 fired nodes per tick, scrolling)
- Right: detail panel (click any node — shows activation, threshold, last incoming trace)
- Left side: generator agent panel (pressure, trajectory, coherence meters)
- Right side: interpreter agent panel (attention, trust, pressure meters)
- Bottom center: legend (node categories + edge types)
- Center fullscreen: D3 force simulation (zoom/pan, drag nodes, click for detail)
- Overlay: exit notification (CONTINUE / RESET)
