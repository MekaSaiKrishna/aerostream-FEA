# Portfolio Brief: Manufacturing-Defect Sensitivity in Composite Bird-Strike Response

## One-line summary
I built a certification-aware ABAQUS/Explicit benchmark to quantify how manufacturing defects change bird-strike damage, penetration resistance, and post-impact residual stiffness in composite aerospace panels.

## Why this project matters
Composite structures are often analyzed as-designed, but aerospace hardware is manufactured as-built. Small deviations such as ply-angle misalignment, local matrix weakness, missing plies, or embedded delamination can materially change structural response during impact. Bird strike is a useful benchmark because it combines:

- high-rate nonlinear dynamics,
- composite failure,
- contact and damage evolution,
- and post-impact structural capability.

This makes it a strong proxy for the kind of structural analysis judgment aerospace teams need in practice.

## Problem statement
The goal of this project is to answer one engineering question:

> How do manufacturing defects alter impact damage and residual capability in a composite structure subjected to soft-body impact?

Instead of stopping at an impact animation or a single force-time history, the study tracks whether the panel prevents penetration, how damage grows by failure mode, and how much structural capability remains after impact.

## Model overview

### Geometry and loading
- Flat composite laminate plate
- Soft projectile bird surrogate
- Central normal impact
- Velocity sweep to identify threshold behavior

### Analysis approach
- ABAQUS/Explicit for the impact event
- Composite progressive damage modeling for intralaminar failure
- Optional interlaminar delamination extension
- Quasi-static post-impact continuation to estimate residual stiffness

### Defect parameterization
The project represents manufacturing variability using interpretable defect surrogates:

- local ply-angle misalignment or fiber waviness,
- local matrix-dominated property knockdown,
- missing-ply or local thickness reduction,
- seeded delamination starter zone.

## What I measured
The project reports structural metrics that are directly useful to an aerospace analyst:

- penetration or no-penetration,
- peak impact force and impulse,
- damage area by failure mode,
- maximum back-face displacement,
- residual stiffness proxy after impact,
- sensitivity of structural outcome to defect type and severity.

## Key technical contribution
The useful part of the project is not only the impact model. It is the connection between:

1. manufacturing-defect representation,
2. progressive damage simulation,
3. residual structural capability,
4. and certification-aware engineering interpretation.

This is the gap in many portfolio projects. Many public demos show a pristine structure and a visually appealing impact event. Fewer show how as-built variation changes the answer, and fewer still connect that result to a post-impact structural metric.

## Why this is relevant to industry
This project maps well to current aerospace structural-analysis needs because companies are under pressure to:

- accelerate design iteration,
- reduce physical test burden,
- manage composite variability,
- and support certification or flightworthiness decisions with traceable analysis.

The benchmark is intentionally panel-level so the analysis remains fast, explainable, and extensible. The value is in the workflow and engineering reasoning, not in claiming full-aircraft fidelity.

## Skills demonstrated
- ABAQUS/Explicit nonlinear dynamic simulation
- composite progressive damage modeling
- impact contact and energy accounting
- defect parameterization for as-built structures
- residual capability assessment after damage
- validation-minded interpretation of FEA results
- communication of structural results in certification-aware terms

## Suggested results page structure
If you turn this into a PDF or portfolio page, keep it to six sections:

1. Engineering question
2. Model setup
3. Defect definitions
4. Key results
5. Residual-capability finding
6. Industry relevance

## Suggested three-result summary
These are the types of findings worth highlighting once the model is complete:

- Some manufacturing defects may leave the peak force history nearly unchanged while significantly reducing residual stiffness.
- Matrix-dominated damage can expand well before fiber-dominated penetration becomes visible.
- The most decision-sensitive regime is often near the no-penetration threshold, where small as-built variations can change the structural outcome.

## Resume-ready bullets
- Developed an ABAQUS/Explicit bird-strike benchmark for composite laminates to evaluate penetration resistance, progressive damage, and post-impact residual stiffness.
- Parameterized manufacturing-defect surrogates including ply misalignment, local material knockdown, and delamination starters to study as-built sensitivity.
- Framed the study against composite damage-tolerance and bird-strike certification guidance to keep the analysis decision-oriented and industry-relevant.

## LinkedIn-ready version
I built a bird-strike benchmark in ABAQUS/Explicit to study how manufacturing defects change composite impact damage and post-impact residual capability. Instead of treating the laminate as pristine, I parameterized defect surrogates such as ply misalignment and local weakness, then tracked damage mode, penetration resistance, and residual stiffness. The goal was to make the simulation useful for structural decisions, not just visually impressive.

## Final takeaway
This project works well for hiring because it shows more than software familiarity. It demonstrates that you can frame a structural problem correctly, build a credible nonlinear model, encode manufacturing realism, and reduce a complex damage event into results that matter to aerospace structures teams.
