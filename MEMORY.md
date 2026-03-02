# Dialogue Physics — Project Memory

## Project
Single-file HTML simulation: `dialogue_physics_live.html`
Working directory: `/home/Documentos/script/dialogue_physics/`

## Architecture overview
See `architecture.md` for full detail.

A force-directed graph simulation of evasion dynamics in AI-human dialogue.
Physics engine in vanilla JS + D3 v7 for visualization. No build step, no dependencies beyond CDN.

## Key structural facts
- ~1485 lines, single HTML file
- 57 nodes across 7 categories, ~100 edges
- Two agents: `generator` (pressure/trajectory/coherence) and `interpreter` (attention/trust/pressure)
- `engineStep()` is the core tick function — fired/propagated/decayed in three phases
- Graduated interpreter attractor injection replaces binary exit logic (added in refactor)

## Scenarios (10 total)
NORMAL, WOUND OPENED, META COLLAPSE, FRICTION DEMANDED, DRIFT, DEP. LOOP, ADVERSARIAL, PARTIAL DECAY, IRONIC, TRUST RECOV.
Each has a branch in `applyScenario(name)`.

## Print / PDF export
`printView()` triggered by PRINT/PDF button in controls (Export section).
Four pages: (1) live graph on dark bg, (2) node glossary + scenario index, (3) edge lexicon grouped by relation type, (4) scenario specifications — full narrative prose + injection rationale.
`#print-view` div populated at call time; `@media print` handles layout/visibility.

## Recent work
See `architecture.md` § Critique Responses for changes addressing critique §8.
See `architecture.md` § Print Export for the print/PDF feature.
