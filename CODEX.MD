# Bird-to-Composite Plate Benchmark for Aerospace Portfolio

## Companion documents
This file is the full project playbook. The following companion documents break the work into recruiter-facing and execution-facing formats:

- `COMPANY_TARGETING.md`: how to tailor the same project to Anduril, Joby, Archer, Boom, and SpaceX
- `PORTFOLIO_BRIEF.md`: condensed recruiter- and hiring-manager-facing project summary
- `ABAQUS_BUILD_CHECKLIST.md`: practical build order, modeling gates, and output checklist for execution in ABAQUS

## Why this project
This is a strong side project because it sits at the intersection of:

- high-rate impact simulation,
- composite progressive damage,
- manufacturing-defect sensitivity,
- certification-aware structural analysis,
- and practical aerospace decision-making under uncertainty.

It is also narrow enough to finish quickly if you keep the geometry simple and focus on a panel-level benchmark instead of a full aircraft component.

## Project definition
Build a **certification-informed but research-scaled** benchmark in ABAQUS for a soft projectile impacting a flat composite laminate plate. The benchmark should answer five questions:

1. Does the panel prevent penetration?
2. What is the peak contact force and impulse?
3. What damage area develops, and which failure modes dominate?
4. How sensitive is the response to embedded manufacturing defects?
5. What is the residual stiffness or residual load-carrying capability after impact?

## Recommended project scope
Keep the first version intentionally simple:

- Geometry: flat rectangular composite plate
- Projectile: gelatin-like cylindrical or capsule bird surrogate
- Analysis type: ABAQUS/Explicit
- Plate support: clamped or simply supported along all edges
- Baseline laminate: quasi-isotropic aerospace CFRP
- Defect cases:
  - local fiber waviness or ply-angle misalignment surrogate,
  - resin-rich pocket / local matrix weakness surrogate,
  - local thickness reduction or missing-ply surrogate,
  - local delamination starter zone surrogate

Do not start with a curved fuselage or leading-edge part. Finish the plate benchmark first, then extend only if the results are clean and publishable.

## Certification framing
This benchmark is **not** a direct certification compliance test article. It is a **panel-level, certification-aware screening model** aligned with the type of impact, damage tolerance, and residual strength questions aerospace companies care about during early design.

Use the certification basis only to define credible impact masses, velocities, pass/fail criteria, and reporting language.

### Relevant certification references
- **FAA 14 CFR 25.631**: bird strike damage requirement for transport-category empennage structures
- **FAA 14 CFR 25.775**: bird impact requirement for cockpit windshields and supporting structure
- **FAA 14 CFR 29.631**: bird strike requirement for rotorcraft
- **FAA AC 20-107B**: composite aircraft structure guidance
- **FAA AC 25.571-1D**: damage tolerance and fatigue evaluation guidance
- **EASA CS-25** and **EASA CM-S-001**: bird strike compliance context for transport aircraft

The important point for your project is this: industry does not just care whether a panel survives impact. They care whether the structure still has meaningful residual capability after impact, whether the analysis is traceable to test evidence, and whether manufacturing variability changes the answer.

## Step-by-step execution plan

### Phase 0: Define the portfolio question
Before building the model, write one sentence that will control all later decisions:

> How do manufacturing defects change bird-strike damage extent, force history, and residual stiffness in a composite aerospace panel?

Your deliverables should map directly to that sentence.

### Phase 1: Freeze the benchmark configuration
Select one baseline configuration and do not keep changing it.

Recommended starting point:

- Plate: 300 mm x 300 mm
- Total thickness: 2.5 to 4.0 mm
- Layup: `[45/0/-45/90]s` or `[0/45/90/-45]s`
- Boundary condition: all edges clamped
- Projectile mass: start with 1.0 kg to 1.8 kg surrogate range
- Projectile velocity: start with 100 to 180 m/s for a panel-level study
- Impact location: plate center
- Impact angle: 90 degrees for first study

Why these values:

- the panel is large enough to develop meaningful bending and local damage,
- the layup is recognizable to aerospace reviewers,
- the mass and speed range is energetic enough to create visible trends,
- the setup is still computationally manageable on a workstation.

Do not start from the full certification bird mass unless the panel dimensions and support conditions are chosen to represent a real certified subcomponent. Otherwise the model looks arbitrary.

### Phase 2: Decide the modeling strategy
For best return on effort, use:

- **Projectile**: CEL or SPH soft body in ABAQUS/Explicit
- **Plate**: continuum shell or layered solid composite model
- **Damage**:
  - first choice: built-in Hashin-based intralaminar damage for rapid delivery,
  - second choice: your own VUMAT/UMAT only if you already have a stable progressive damage model for rate-dependent composite failure,
  - optional: cohesive interfaces for delamination if runtime remains acceptable

Recommended order:

1. Start with intralaminar damage only.
2. Validate force-time and damage footprint trends.
3. Add interlaminar delamination only if the baseline model is already behaving physically.

This sequence matters. Too many bird-strike composite models fail because the analyst introduces every damage mechanism before the baseline contact mechanics are stable.

### Phase 3: Build the bird surrogate
Treat the bird as a soft hydrodynamic projectile, not a deformable elastic solid.

Recommended options in ABAQUS:

- **CEL** if you want robust large-deformation behavior and cleaner contact for a portfolio-quality model
- **SPH** if you want a simpler projectile workflow and easier mesh distortion handling

Typical bird surrogate assumptions from open literature:

- density around `900 to 1000 kg/m^3`
- water/gelatin-like behavior
- cylindrical slug with `L/D ~= 2`
- equation of state: Mie-Gruneisen or equivalent hydrodynamic EOS

Practical portfolio advice:

- pick **one** bird geometry and stick to it,
- justify it using open bird-strike literature,
- report sensitivity to velocity, not endless geometry variations.

### Phase 4: Select the composite material system
Choose one realistic aerospace laminate system and gather lamina properties consistently.

You need:

- `E1, E2, E3`
- `nu12, nu13, nu23`
- `G12, G13, G23`
- density
- tensile/compressive strengths in fiber and matrix directions
- shear strengths
- fracture energy or damage evolution inputs
- rate sensitivity assumptions, if used

Recommended baseline material families:

- IM7/8552-type CFRP
- T700/epoxy-type CFRP
- AS4/3501-6-type CFRP

Pick one system with enough public data to avoid stitching together incompatible sources.

### Phase 5: Translate manufacturing defects into FEA-ready variables
This is where your profile becomes differentiated. Do not merely say "defects matter." Encode them parametrically.

Use 3 to 4 defect archetypes:

#### Defect A: Ply misalignment / fiber waviness surrogate
Model as:

- local ply-angle perturbation in a circular or elliptical region,
- for example, `+/-5 degrees`, `+/-10 degrees`, `+/-15 degrees` from nominal.

Outputs to track:

- matrix damage onset,
- local shear-dominated damage growth,
- change in peak force,
- change in residual stiffness.

#### Defect B: Resin-rich pocket / local matrix weakness surrogate
Model as:

- reduced transverse modulus and matrix-dominated strengths in a small zone,
- or reduced damage initiation thresholds for matrix tension/compression.

Outputs to track:

- earlier matrix cracking,
- larger damage footprint,
- larger permanent deformation.

#### Defect C: Missing ply / local thickness loss surrogate
Model as:

- remove one ply locally or reduce effective thickness in a bounded patch.

Outputs to track:

- local bending compliance,
- fiber failure concentration,
- penetration tendency.

#### Defect D: Embedded delamination starter
Model as:

- cohesive interface patch with reduced interface strength,
- or contact-separation interface seeded over a local area.

Outputs to track:

- delamination growth,
- energy dissipation shift,
- post-impact stiffness loss.

Do not model all real manufacturing flaws. Model a few that are interpretable, reproducible, and easy to explain to recruiters and hiring managers.

### Phase 6: Create the ABAQUS baseline model
Build the clean laminate first.

#### Plate modeling
Recommended options:

- **Continuum shell (SC8R)** for good through-thickness representation at moderate cost
- **Layered solid (C3D8R)** if you need explicit ply-wise resolution and can afford runtime
- avoid simple shell elements if your main story depends on through-thickness damage localization

Recommended mesh strategy:

- fine mesh in impact zone
- gradually coarsened mesh away from impact center
- at least 10 to 15 elements across the expected contact diameter
- perform one mesh sensitivity study before introducing defects

#### Contact
- general contact in Explicit
- frictionless as the baseline
- hard normal contact

#### Time step
- total simulation time long enough to capture full contact pulse and rebound
- typically on the order of a few milliseconds

#### Mass scaling
- avoid aggressive mass scaling in the baseline validation model
- if used later, document exactly how much kinetic/internal energy distortion it introduces

#### Energy checks
Track:

- internal energy
- kinetic energy
- contact energy
- artificial strain energy
- hourglass energy

Your model is not credible if energy balance is poor, even if the animations look good.

### Phase 7: Define damage and failure metrics
You need outputs that map to hiring-manager language, not only thesis language.

Report:

- peak impact force
- contact duration
- total impulse
- maximum back-face displacement
- damaged area by failure mode
- fiber tension failure area
- fiber compression failure area
- matrix tension/compression failure area
- delamination area if modeled
- penetration or no-penetration
- residual stiffness proxy

Create a clean pass/fail definition:

- `Pass`: no penetration and residual stiffness remains above a chosen threshold
- `Conditional pass`: no penetration but severe damage and large stiffness loss
- `Fail`: penetration, perforation, or collapse of residual capability

### Phase 8: Run the model in the correct sequence
Use this exact order.

#### 8.1 Rigid-wall calibration
Before impacting the composite panel, fire the bird surrogate at a rigid plate.

Purpose:

- calibrate projectile behavior,
- verify force pulse is reasonable,
- check numerical stability,
- tune EOS and particle/cell resolution.

This step is standard good practice and makes your later panel results more defensible.

#### 8.2 Clean-panel impact study
Run velocity sweep on the defect-free panel.

Recommended first sweep:

- 100 m/s
- 130 m/s
- 160 m/s
- 180 m/s

Identify:

- no-damage threshold,
- first major matrix-damage threshold,
- onset of fiber-dominated failure,
- first near-penetration / penetration threshold.

#### 8.3 Defect sensitivity study
Introduce one defect type at a time.

For each defect family:

- low severity
- medium severity
- high severity

This gives you interpretable plots instead of a combinatorial mess.

#### 8.4 Residual stiffness study
For fastest execution, do the post-impact step as a **quasi-static continuation in Explicit** with very low loading rate and low kinetic energy ratio.

Recommended approach:

- after impact and stabilization, apply displacement-controlled central indentation or edge compression/tension,
- extract secant stiffness, peak load, or energy-to-failure proxy,
- compare damaged vs pristine panel.

Why this approach:

- it avoids fragile state-transfer workflows,
- it is easier to reproduce,
- it is good enough for a side-project portfolio benchmark.

If you later want a more certification-like post-impact metric, convert the damaged region into a separate compression-after-impact style sub-study.

### Phase 9: Validate the model
Validation matters more than sophistication.

Use three levels of validation:

#### Level 1: Projectile validation
Match qualitative force-time behavior against published rigid-target bird impact studies.

#### Level 2: Composite panel trend validation
Compare:

- damage footprint shape,
- peak force range,
- whether the dominant damage is matrix-first then fiber,
- penetration threshold trends

against published composite bird-strike panel literature.

#### Level 3: Numerical credibility checks
Show:

- mesh sensitivity,
- time increment sensitivity,
- bird resolution sensitivity,
- energy balance,
- contact robustness.

If you skip validation, the project will read like a rendering exercise instead of engineering.

### Phase 10: Package the results like an industry analyst
Produce the following figures:

1. Impact setup schematic
2. Mesh and laminate layup figure
3. Force-time curves for baseline velocity sweep
4. Damage maps by failure mode
5. Defect sensitivity chart
6. Residual stiffness bar chart
7. Penetration / no-penetration design map
8. Short table of key assumptions and limitations

Produce the following tables:

1. Lamina properties
2. Bird surrogate properties
3. Defect parameter definitions
4. Solver settings
5. Summary results for all cases

### Phase 11: Write the engineering story
Frame the conclusion around decisions, not just observations.

For example:

- Which defect most strongly reduces residual stiffness?
- Which defect barely changes peak force but drastically increases hidden damage?
- Which impact velocity range is most sensitive to manufacturing quality variation?
- Which failure mode is the earliest indicator of loss of structural margin?

That is the part recruiters remember.

## Recommended ABAQUS setup

### Solver
- ABAQUS/Explicit for impact
- also ABAQUS/Explicit for the residual stiffness continuation in the first version

### Projectile formulation
- CEL preferred
- SPH acceptable and widely used

### Composite damage
- built-in Hashin for first version
- VUMAT/UMAT only if already verified and stable

### Delamination
- optional cohesive surfaces or cohesive elements between plies

### Element suggestions
- plate: `SC8R` or `C3D8R`
- bird: Eulerian brick or SPH particles

### Output frequency
Save:

- force history,
- displacement history,
- energy terms,
- damage variables,
- status variables,
- contact variables,
- section point outputs by ply.

### Quality checks
Watch for:

- excessive element deletion that creates nonphysical holes,
- unstable contact chatter,
- unrealistic projectile fragmentation,
- artificial energy spikes,
- hourglass energy contamination.

## Model parameters and property resources

### Certification and structural guidance
- FAA 14 CFR 25.631, Bird strike damage: https://www.law.cornell.edu/cfr/text/14/25.631
- FAA 14 CFR 25.775, Windshields and windows: https://www.law.cornell.edu/cfr/text/14/25.775
- FAA 14 CFR 29.631, Bird strike: https://www.law.cornell.edu/cfr/text/14/29.631
- FAA AC 20-107B, Composite Aircraft Structure: https://www.faa.gov/airports/resources/advisory_circulars/index.cfm/go/document.information/documentNumber/20-107B
- FAA AC 25.571-1D, Damage Tolerance and Fatigue Evaluation of Structure: https://www.faa.gov/regulations_policies/advisory_circulars/index.cfm/go/document.information/documentid/865446
- EASA CM-S-001, Compliance with CS-25 Bird Strike Requirements: https://www.easa.europa.eu/en/document-library/product-certification-consultations/easa-cm-s-001
- EASA CS-25 consolidated version: https://www.easa.europa.eu/en/cs-25-consolidated-versionpdf

### Composite allowables and material data
- CMH-17 composite materials handbook
- NCAMP public material data packages when available
- OEM or supplier datasheets for aerospace prepregs
- open literature for complete lamina strength sets when handbook data is unavailable

### Bird surrogate and impact modeling literature
- Soft body impact on laminated composite materials, Composites Part A (2007): https://www.sciencedirect.com/science/article/pii/S1359835X06001059
- Modelling soft body impact on composite structures, Composite Structures (2003): https://www.sciencedirect.com/science/article/pii/S0263822303000333
- Hybrid approach in bird strike damage prediction on aeronautical composite structures, Composite Structures (2011): https://www.sciencedirect.com/science/article/pii/S026382231100287X
- Bird strike damage and analysis of UAV's airframe, Procedia Structural Integrity (2019): https://www.sciencedirect.com/science/article/pii/S245232161930054X
- Bird-Strike Resistance of Composite Laminates with Different Materials, Materials (2020): https://www.mdpi.com/1996-1944/13/1/129
- Experimental and Simulation Studies on Bird Strike Impact of Carbon Fiber Composite Laminates with Different Ply Directions, Aerospace (2025): https://www.mdpi.com/2226-4310/12/11/1017

### Manufacturing-defect and inspection relevance
- JEC Composites Innovation Awards 2023, NIAR/WSU AFP inspection system: https://www.jeccomposites.com/press/the-jec-composites-innovation-awards-winners-revealed/
- NDT review for aerospace composites (2025): https://www.mdpi.com/2813-477X/3/1/3

### ABAQUS documentation to use
Use the official SIMULIA documentation sections for:

- ABAQUS/Explicit dynamic analysis
- CEL
- SPH conversion and particle behavior
- general contact
- equations of state
- Hashin damage for fiber-reinforced composites
- damage evolution and element deletion
- cohesive behavior / cohesive elements
- field and history output for energy accounting

## Suggested case matrix
Keep the matrix small enough to finish.

### Baseline cases
- 4 velocities
- 1 pristine laminate

### Defect cases
- 4 defect families
- 3 severities each
- 1 representative velocity near the damage threshold

That gives:

- 4 pristine impact cases
- 12 defect cases
- optional 4 residual stiffness follow-on cases

This is enough for a credible portfolio without becoming a six-month research program.

## Recommended timeline

### Week 1
- freeze geometry, layup, and certification framing
- collect material properties
- build rigid-wall bird calibration model

### Week 2
- build pristine composite panel model
- run mesh and stability checks
- complete pristine velocity sweep

### Week 3
- implement defect surrogates
- run defect sensitivity matrix
- produce damage maps and force-time plots

### Week 4
- run residual stiffness cases
- build figures, tables, and short technical report
- prepare LinkedIn and portfolio packaging

## What to include in the final portfolio package
- 1 concise technical report in PDF
- 1 public GitHub repository with redacted inputs if needed
- 1 short slide deck with the engineering story
- 1 LinkedIn post with one strong animation and one summary figure
- 1 README focused on aerospace relevance, not software tooling

## What gap this fills in the current industry
This project is well aligned with real industry pain points because aerospace companies, especially fast-moving startups, are under pressure to do three things at once:

1. reduce physical test iteration cost,
2. certify increasingly composite-heavy structures,
3. understand how manufacturing variability changes structural risk.

Public signals support that framing:

- EASA's 2025 Rotorcraft Structures Workshop explicitly listed bird strike, materials, fatigue, and damage tolerance among major active structural topics.
- FAA and EASA guidance still anchor compliance around demonstrated structural capability after bird impact, not just pretty simulations.
- NIAR/WSU's award-recognized in-process AFP inspection work shows that manufacturing defects and traceable defect data remain a live industrial problem, especially for certification and quality assurance.

Your project fills a useful gap because most publicly visible bird-strike demos stop at:

- pretty impact animations,
- one force-time curve,
- or a pristine structure.

Very few portfolio projects connect all four of these together in one coherent workflow:

- soft-body impact,
- composite progressive damage,
- manufacturing defects,
- and post-impact residual capability.

That combination is much closer to how an aerospace structures team actually thinks.

## How to market this on LinkedIn and to recruiters
Do not market it as "I ran an ABAQUS bird strike simulation."

Market it as:

> I built a certification-aware composite bird-strike benchmark in ABAQUS/Explicit to quantify how manufacturing defects change impact damage, penetration resistance, and post-impact residual stiffness.

That wording signals:

- structural analysis,
- composites expertise,
- explicit dynamics,
- damage modeling,
- manufacturing awareness,
- and engineering judgment tied to certification reality.

### What recruiters should immediately notice
- You understand composite failure beyond static strength checks.
- You can translate manufacturing defects into parametric simulation inputs.
- You think in terms of validation, residual capability, and certification context.
- You can build a decision-oriented analysis, not just a thesis-style study.

### Suggested recruiter-facing bullet points
- Developed an ABAQUS/Explicit bird-strike benchmark for composite laminates using soft-body impact modeling and progressive damage analysis.
- Quantified the effect of manufacturing-defect surrogates such as ply misalignment, local stiffness knockdown, and delamination starters on impact response.
- Evaluated penetration resistance, damage footprint, force-time history, and post-impact residual stiffness as structural decision metrics.
- Framed the study against FAA/EASA bird-strike and composite damage-tolerance guidance to keep the analysis certification-aware.

### Suggested LinkedIn post structure
Use this structure:

1. One-sentence hook
2. One image or animation
3. Three technical findings
4. One paragraph on why it matters to aerospace
5. One line stating you are open to structural analysis roles

Example hook:

> Composite structures are often analyzed as-built in design, but they are manufactured imperfectly. I built a bird-strike benchmark to quantify how those defects can change damage and residual capability.

Example findings:

- A modest local ply-angle deviation increased matrix-dominated damage area disproportionately relative to peak force change.
- Some defect types barely changed the impact force history but caused much larger residual stiffness loss.
- The most decision-relevant regime was near the no-penetration threshold, where small manufacturing variations changed the structural outcome.

### Portfolio title options
- Manufacturing-Defect Sensitivity in Composite Bird-Strike Response
- Certification-Aware Bird-Strike Benchmark for Composite Laminates
- Post-Impact Residual Capability of Defect-Driven Composite Panels

## Final recommendation
If your goal is hiring ROI, the winning version of this project is:

- one clean baseline laminate,
- one defensible bird surrogate,
- three to four defect families,
- one residual-stiffness metric,
- strong figures,
- and a report written like an aerospace structures engineer rather than a student.

That is enough to make the project memorable and directly relevant to structural analyst roles.
