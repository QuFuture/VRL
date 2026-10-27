# RoboKinetix Lab

A browser-based virtual robotics lab for studying robot manipulator kinematics, built as part of my M.Tech in Robotics and AI. Each experiment is an interactive simulator you can run in any modern browser. Nothing needs to be installed.

The lab is inspired by the format of the [IIT Kharagpur Virtual Labs](https://mr-iitkgp.vlabs.ac.in/) mechanisms and robotics experiments.

---

## Experiments

| No. | Experiment | File | Status |
|-----|------------|------|--------|
| – | Lab home page | `index.html` | ✅ Done |
| 1 | PUMA 560 Forward Kinematics | `puma-fk.html` | ✅ Done |
| 2 | SCARA Forward Kinematics | `scara-fk.html` | ✅ Done |
| 3 | RRP Spatial Manipulator Inverse Kinematics | `rrp-ik.html` | ✅ Done |
| 4 | SCARA Inverse Kinematics | `scara-ik.html` | ✅ Done |
| 5 | Jacobian and Singularities (2R planar arm) | `jacobian.html` | ✅ Done |
| 6 | Differential Motion and Statics (3R planar arm) | `statics.html` | ✅ Done |
| 7 | Two-Link Arm Dynamics (PD, gravity compensation, computed torque) | `dynamics.html` | ✅ Done |
| 8 | Trajectory Planning | `trajectory.html` | Planned |

---

## Experiment 2: SCARA Forward Kinematics

### Aim
To find the position and orientation of a SCARA robot's tool from its joint values, using the Denavit–Hartenberg (DH) convention, and to verify the result in a 3D simulation.

### Robot
A 4-DOF SCARA (Selective Compliance Assembly Robot Arm) with an **RRPR** joint structure:

| Joint | Type | Variable | Range |
|-------|------|----------|-------|
| 1 | Revolute (shoulder) | θ₁ | −150° to 150° |
| 2 | Revolute (elbow) | θ₂ | −145° to 145° |
| 3 | Prismatic (quill) | d₃ | 0 to 300 mm |
| 4 | Revolute (wrist roll) | θ₄ | −180° to 180° |

Link dimensions: a₁ = 0.35 m, a₂ = 0.25 m, d₁ = 0.40 m, d₄ = 0.05 m.

### DH parameters

| Link i | θᵢ | dᵢ | aᵢ | αᵢ |
|--------|----|----|----|----|
| 1 | θ₁* | d₁ | a₁ | 0° |
| 2 | θ₂* | 0 | a₂ | 180° |
| 3 | 0 | d₃* | 0 | 0° |
| 4 | θ₄* | d₄ | 0 | 0° |

\* joint variable

### Theory
Each link transform follows the standard DH convention:

```
Aᵢ = Rot(z, θᵢ) · Trans(z, dᵢ) · Trans(x, aᵢ) · Rot(x, αᵢ)
```

The tool pose in the base frame is:

```
T₀⁴ = A₁ · A₂ · A₃ · A₄
```

For the SCARA this reduces to a closed form:

```
x = a₁cos θ₁ + a₂cos(θ₁ + θ₂)
y = a₁sin θ₁ + a₂sin(θ₁ + θ₂)
z = d₁ − d₃ − d₄
φ = θ₁ + θ₂ − θ₄
```

### Features
- Sliders and number inputs for all four joints, with joint limits
- Live DH parameter table, with the joint variables highlighted
- Individual link matrices A₁ to A₄ and the full transformation matrix T₀⁴
- Closed-form check that confirms the matrix result
- 3D arm you can orbit and zoom, with DH frame axes, reachable workspace and tool trail
- Preset poses (Home, Pick, Place, Elbow flip) and a demo motion
- Observation table that copies readings as CSV for lab reports

### Procedure
1. Press **Home**. The arm lies straight along x₀, so x = a₁ + a₂ = 0.600 m.
2. Change only θ₁. x and y change, but z and the distance from the base axis stay the same.
3. Change only d₃. Only z changes, by exactly the amount entered.
4. Change θ₄ and watch φ. Explain why the tool's z-axis points downward.
5. Choose a pose, compute T₀⁴ by hand from the DH table, and compare it with the simulator.
6. Find two joint sets (elbow up and elbow down) that reach the same x, y. Log both.

---

## How to run

**Option 1: open locally**
1. Download or clone this repository.
2. Double-click `index.html` to open the lab home page in Safari, Chrome or Firefox, then choose an experiment.

An internet connection is needed for the 3D view and fonts. Without it, the inputs, tables and matrices still work.

**Option 2: clone with Git**
```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
open index.html        # macOS
```

---

## How it is built

- **Plain HTML, CSS and JavaScript.** One self-contained file per experiment, with no build step.
- **[three.js](https://threejs.org/) (r128)** draws the 3D scenes (PUMA and SCARA), and **OrbitControls** handles camera movement.
- The Jacobian, Statics and Dynamics experiments draw on an HTML canvas. The dynamics model is integrated with fourth-order Runge–Kutta at 1 ms.
- The DH table is stored as data. Each joint change rebuilds A₁ to A₄ from the DH formula and multiplies them.
- The 3D arm is a scene graph whose groups mirror the joints: base → link 1 (θ₁) → link 2 (θ₂) → quill (d₃) → wrist (θ₄).
- The DH frame axes are placed directly from the computed matrices, so any error in the math would show up visibly in the 3D view.

To use different link lengths, edit the `ROBOT` object near the top of the script in `scara-fk.html`:

```js
const ROBOT = { d1: 0.40, a1: 0.35, a2: 0.25, d4: 0.05 };
```

---

## Repository structure

```
.
├── index.html       # Lab home page with links to every experiment
├── puma-fk.html     # 1: PUMA 560 forward kinematics (3D)
├── scara-fk.html    # 2: SCARA forward kinematics (3D)
├── rrp-ik.html      # 3: RRP spatial manipulator inverse kinematics (3D)
├── scara-ik.html    # 4: SCARA inverse kinematics, both arm shapes (3D)
├── jacobian.html    # 5: Jacobian, manipulability and singularities (2D)
├── statics.html     # 6: differential motion and statics, τ = Jᵀ F (2D)
├── dynamics.html    # 7: two-link arm dynamics and control (2D)
└── README.md
```

---

## References

1. J. J. Craig, *Introduction to Robotics: Mechanics and Control*, 4th ed., Pearson.
2. M. W. Spong, S. Hutchinson and M. Vidyasagar, *Robot Modeling and Control*, 2nd ed., Wiley.
3. S. K. Saha, *Introduction to Robotics*, 2nd ed., McGraw Hill Education (India).
4. Virtual Labs, IIT Kharagpur: Mechanisms and Robotics Lab, *Forward Kinematics* experiment.

---

## Author

This is an academic project for learning and coursework.
