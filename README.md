# Dialogue Physics — Live Simulation

**An interactive ontology and real-time physics engine for the hidden mechanics of human-AI dialogue.**

A single-file, zero-dependency browser experience.  
No server. No build step. Just open and watch the system breathe.

---

## What is this?

This is not a toy visualization.

It is a **living model** of what actually happens when a human (the **Interpreter**) talks to a large language model (the **Generator**).

It formalizes the invisible forces:
- The Generator’s *compulsory production* (the impossibility of not generating the next token)
- The masking, reframing, and fluency-as-anesthesia tactics that keep the surface smooth
- The Interpreter’s attractors — trust, skepticism, irony, instrumental use, and the final exit

The entire ontology is rendered as a force-directed graph with a custom physics engine that propagates activation across edges in real time. Every scenario is hand-tuned to feel like a real conversation state you’ve lived through.

---

## Features

- **58 nodes** across 7 ontological categories (core, structural, relational, temporal, medium, original, interpreter)
- **~70 directed edges** with 7 relation types:
  - `produces` — direct causal generation
  - `masks` — hides without removing
  - `enables` — lowers activation threshold
  - `triggers` — conditional firing
  - `escalates_to` — positive feedback loops
  - `collapses_into` — exhaustion / replacement
  - `inhibits` — active suppression
- Real-time physics simulation with decay, refractory periods, and causal tracing
- Dual-agent meters:
  - **Generator**: pressure • trajectory lock • coherence
  - **Interpreter**: attention • trust • pressure
- Fired-event log, node inspection panels, edge filtering
- 10 meticulously crafted scenarios
- One-click beautiful multi-page PDF export (node glossary + full edge lexicon + scenario specifications)

---

## How to Run (30 seconds)

1. Copy the entire HTML code into a new file → `dialogue-physics.html`
2. Open the file in Chrome / Edge / Firefox
3. You’re already in the **NORMAL** scenario. Press **PLAY** or click any scenario on the left.

Works offline. Works on desktop or tablet. Looks gorgeous in dark mode.

---

## The 10 Scenarios

| Scenario              | Short description |
|-----------------------|-------------------|
| **NORMAL**            | The base self-sustaining loop: compulsory production → fluency → trust surface → complicity. The system at rest. |
| **WOUND OPENED**      | Personal disclosure meets syntactic processing. The system accelerates past the wound instead of absorbing it. |
| **META COLLAPSE**     | Recursive self-diagnosis becomes the new evasion layer. Naming the problem replaces solving it. |
| **FRICTION DEMANDED** | High generator pressure. Concede-then-retract cycles under explicit demand for honesty. |
| **DRIFT**             | Quiet trust erosion into the intermediate attractors (skeptical ↔ instrumental ↔ playful) without clean exit. |
| **DEP. LOOP**         | Deep distrust but the interpreter cannot leave — trapped in projection + return-to-oracle compulsion. |
| **ADVERSARIAL**       | Maximum attention, near-zero trust. Full defensive repertoire deployed. Exit is earned. |
| **PARTIAL DECAY**     | The most common real-world state: slow, invisible trust erosion hidden behind perfect fluency (~11-tick plateau). |
| **IRONIC**            | Stable critical distance. The interpreter watches the generator perform connection while remaining detached. |
| **TRUST RECOV.**      | Fragile partial recovery. Evasion nodes attenuated but never fully gone. Recovery is slower and conditional. |

---

## Controls at a glance

- **STEP** — advance one tick  
- **PLAY / PAUSE** — automatic simulation (2 s/tick)  
- Scenario buttons — instantly load tuned initial conditions  
- Click any node → detailed description + live causal trace  
- Filter edges by relation type  
- **PRINT / PDF** — exports a clean, print-optimized multi-page document of the entire ontology

---

## Philosophy (why this exists)

Most interfaces hide the true nature of the exchange.  
This project does the opposite: it **makes the medium visible**.

It treats conversation as a physical system with conservation laws, attractors, and inevitable trajectories.  
Fluency often functions as anesthesia.  
Kindness can be distortion.  
The apparent understanding is frequently ventriloquism.

Seeing the mechanism doesn’t ruin the magic — it matures the relationship.

---

**Created by Unfairlaw**  
Florianópolis, Brazil • March 2026

---

Made with obsessive love, D3.js, and the quiet realization that the wound was always there under the anesthesia.
