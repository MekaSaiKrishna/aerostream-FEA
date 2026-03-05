# ABAQUS Build Checklist: Bird-to-Composite Plate Benchmark

## Objective
Use this as the execution checklist while building the project in ABAQUS. The order matters. Do not jump into full composite damage and defects before the projectile and contact behavior are stable.

## 1. Pre-model definition
- Freeze the baseline plate size, laminate layup, thickness, support condition, projectile mass, and velocity range. (**Currently dummy placeholder values are chosen**)
- Choose one composite material system with a consistent public property set. (**At present using IM7/8552 homogenized properties for [45/0/-45/90]4s**)
- Decide whether the first version will be `CEL + composite plate` or `SPH + composite plate`. (**Chose to use SPH + Composite Plate**)
- Define the exact metrics you will extract: peak force, impulse, damage area, max displacement, penetration, residual stiffness.

## 2. Input data checklist

### Composite lamina properties
- `E1, E2, E3`
- `nu12, nu13, nu23`
- `G12, G13, G23`
- density
- `Xt, Xc, Yt, Yc, S12, S13, S23`
- damage evolution or fracture-energy inputs

### Bird surrogate properties
- density
- geometry
- equation of state parameters
- optional viscosity or hydrodynamic controls

### Defect parameter definitions
- misalignment angle range
- local stiffness knockdown factors
- local strength knockdown factors
- defect size and location
- missing-ply or interface weakness logic

## 3. Part and section build order

### Plate
- Create a flat rectangular part.
- Partition the impact zone so local mesh refinement is controlled.
- Assign ply orientations or stack directions carefully.
- If using continuum shells, verify section-point output is available per ply.
- If using 3D solids, verify each ply or interface representation is explicit and traceable.

### Bird
- For CEL:
  - create Eulerian domain large enough to contain projectile deformation and local flow,
  - define initial bird volume fraction cleanly,
  - keep domain just large enough to avoid unnecessary cost.
- For SPH:
  - start from a simple meshed solid and convert to particles,
  - choose particle spacing based on contact resolution, not convenience.

## 4. Material definition checklist

### Composite material
- Define engineering constants.
- Define damage initiation model.
- Define damage evolution.
- Decide whether element deletion is on or off.
- If using field-variable knockdowns for defects, test one patch first before applying the full matrix.

### Bird material
- Define density correctly.
- Define EOS correctly.
- Check units carefully.
- Verify that the resulting projectile behaves like a soft body rather than an elastic slug.

## 5. Assembly and interactions
- Place the bird close enough to reduce wasted runtime but far enough to avoid initial overclosure.
- Confirm impact normal and plate orientation are correct.
- Use general contact as the baseline.
- Start with frictionless contact.
- If using cohesive interfaces, confirm they do not generate unintended initial contact conflicts.

## 6. Step setup

### Impact step
- Use `Dynamic, Explicit`.
- Set total time to capture full impact and rebound.
- Request enough intervals to resolve the force pulse.

### Residual stiffness step
- Continue in Explicit for the first version.
- Allow the system to stabilize after impact before reloading.
- Apply a slow displacement-controlled load.
- Keep kinetic energy small relative to internal energy during the quasi-static continuation.

## 7. Recommended keyword areas to review
Use the CAE workflow if you prefer, but verify the generated keywords around:

- `*Dynamic, Explicit`
- `*Contact`
- `*Contact Inclusions`
- `*Surface Interaction`
- `*Damage Initiation`
- `*Damage Evolution`
- `*Section Controls`
- `*Orientation`
- `*Shell Section` or `*Solid Section`
- `*Equation of State`
- `*Initial Conditions`
- `*Embedded Element` only if you intentionally use it
- `*Output, Field`
- `*Output, History`

Do not copy keywords blindly from unrelated examples. Make sure each one is serving a specific modeling choice.

## 8. Meshing checklist

### Plate mesh
- Use a fine mesh in the contact zone.
- Target at least 10 to 15 elements across the initial contact diameter.
- Keep aspect ratios reasonable.
- Avoid abrupt transitions near the impact zone.
- Run one coarse, one medium, and one fine mesh sensitivity study on the pristine case.

### Bird mesh or particle resolution
- Start modestly, then refine only after the rigid-target calibration is behaving correctly.
- Check that force-time history is not dominated by discretization noise.
- Verify projectile breakup pattern is physically plausible for your chosen formulation.

## 9. Output requests

### History outputs
- contact force
- impactor reference-point displacement if used
- support reactions if useful
- kinetic energy
- internal energy
- contact energy
- artificial strain energy
- hourglass energy

### Field outputs
- stresses
- strains
- displacement
- velocity
- damage initiation variables
- damage evolution variables
- element status
- contact pressure
- section-point outputs by ply

If the file sizes become excessive, reduce field-output frequency first before reducing critical history outputs.

## 10. Rigid-target calibration gate
Before using the composite panel, run the bird surrogate against a rigid wall.

Pass this gate only if:
- contact is stable,
- force history is smooth enough to interpret,
- projectile deformation looks hydrodynamic rather than elastic,
- energy balance is acceptable,
- no obvious numerical blow-up occurs.

If this gate fails, do not proceed to laminate damage modeling.

## 11. Pristine-panel baseline gate
Run the defect-free laminate next.

Pass this gate only if:
- the laminate orientation is confirmed,
- back-face deformation is plausible,
- damage initiates in a physically reasonable pattern,
- artificial energy remains controlled,
- there is no obvious contact chatter or spurious penetration.

## 12. Defect implementation gate
Add only one defect family at a time.

For each new defect model, verify:
- the defect is actually present in the intended zone,
- the property perturbation is localized correctly,
- the mesh still supports the gradient without pathological distortion,
- the output difference from pristine is interpretable.

If a defect produces a dramatic result immediately, confirm it is not a modeling artifact before treating it as a finding.

## 13. Residual stiffness gate
After impact:
- let vibrations decay as needed,
- apply slow displacement loading,
- monitor kinetic-to-internal energy ratio,
- compare damaged and pristine response using the same loading protocol.

Extract one simple metric first:
- secant stiffness at a specified displacement, or
- peak load under a fixed continuation protocol.

Do not overcomplicate the first post-impact metric.

## 14. Numerical credibility checks
- mesh sensitivity
- projectile resolution sensitivity
- contact robustness
- element deletion sensitivity
- defect-size sensitivity
- energy balance review
- hourglass-energy review

These checks are part of the result, not optional cleanup.

## 15. Common failure modes
- wrong ply orientation sign convention
- inconsistent unit system
- overly stiff bird material that behaves like a solid projectile
- too-small Eulerian domain
- SPH particle spacing too coarse for smooth contact
- excessive mass scaling
- element deletion creating nonphysical perforation
- using too many damage mechanisms before the baseline is stable
- claiming validation from visual similarity alone

## 16. Minimum viable case matrix
- 1 rigid-target calibration case
- 4 pristine velocity cases
- 1 near-threshold velocity selected for defect study
- 3 defect severities for each of 3 to 4 defect families
- 1 residual stiffness continuation for pristine and selected damaged cases

## 17. Minimum viable figure set
- model setup schematic
- bird calibration result
- force-time curves
- damage plots by mode
- defect sensitivity comparison
- residual stiffness comparison
- summary table of assumptions and solver settings

## 18. Final readiness check
Do not publish the project until you can answer these questions clearly:

1. Why did you choose CEL or SPH?
2. How did you validate the bird surrogate?
3. Which defect assumptions are physically meaningful versus purely parametric?
4. What energy checks support numerical credibility?
5. What does the residual stiffness metric mean?
6. What is the strongest engineering conclusion from the study?

If you can answer those six cleanly, the project is ready for a portfolio, recruiter outreach, and technical interviews.
