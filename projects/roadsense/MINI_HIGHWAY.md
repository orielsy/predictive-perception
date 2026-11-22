# RoadSense Mini Highway — low-cost physical research testbed

**Status:** Proposed. No purchases, construction, code, or measurements yet.  
**Primary goal:** Reproduce occlusion, spatial tracking, and a simple white-car/chevron HUD with repeatable ground truth, at very low cost.

## Physical layout

- Start with a **4–6 ft cardboard/foam-board highway** with several marked lanes. Modules can be straight before introducing merges or curves.
- Suggested **1:32** scale for the larger toy-car geometry, where practical (approx. 11–12 cm lanes). Other scales are acceptable if mounting and sensing constraints favor them.
- **White car:** RoadSense ego vehicle, held fixed initially; treat moving traffic as relative trajectories or translate observations into an ego-centered coordinate frame.
- Other cars/trucks: toy cars and cardboard-box trucks. Include a **long truck** that can occlude one or more nearby vehicles.
- Place fiducial marks, lane centerlines and distance marks for calibration and repeatability.
- Use **manually moved** toy traffic initially; add guided/motorized motion only when human movement prevents accurate testing.

### Sensor/test arrangement (three different roles)

1. **RoadSense test sensor:** Candidate webcam / planar LiDAR / 3D LiDAR / radar, positioned so it sees the modeled traffic from the ego perspective. Its limits and self-occlusion are part of the experiment.
2. **Overhead ground-truth camera:** A separate elevated camera that sees the whole table, including vehicles occluded from RoadSense's perspective. *Do not feed these hidden positions to the algorithm under test*.
3. **Desktop / Alta compute:** Ingest sensor data; perform calibration, tracking, prediction, HUD rendering and event logging. Start on the desktop and assess Alta after proving the pipeline.

### Basic research diagram

```text
                 OVERHEAD GROUND-TRUTH CAMERA
                             ↓
        ┌─────────────────────────────────┐
        │  CAR  CAR      [LONG TRUCK]      │
        │      CAR       [LONG TRUCK]      │
        │      WHITE EGO CAR    CAR        │
        │  CAR                            │
        └─────────────────────────────────┘
                 ↑
            ROADSense test sensor(s)
                 ↓
   sensor processor → tracks / uncertainty → chevron HUD
                 ↓
       offline scoring against overhead ground truth
```

This represents the logical experiment; sensor placement needs to preserve the actual line-of-sight constraints we want to study.

## Staged experiment plan

| Step | Cost level | Experiment / evidence |
| --- | --- | --- |
| 0. No physical rig | $0 sensor hardware | Simulated object tracks, HUD render, state transitions, prediction expiry and event logs |
| 1. Cardboard + overhead webcam | Very low | Record ground truth and manually move cars through lane changes and truck occlusion |
| 2. Ego-perspective camera | Low | Detect toy vehicles and test tracking independently from ground truth |
| 3. Short-range 2D LiDAR | Optional, modest | Measure planar object positions and line-of-sight gaps |
| 4. Low-cost radar | Optional, modest | Check if the module reports **any meaningful toy-car returns**; do not assume it will |
| 5. Affordable 3D 360° LiDAR | Optional, hundreds | Compare 3D object position and occlusion fidelity if lower-cost sensors prove inadequate |
| 6. Automated traffic | Optional | Repeat speeds and lane-change trajectories with guided/motorized mechanisms |

### Example benchmark scenarios

- A car moves alongside the white car and is continuously observed.
- A smaller car **disappears behind a box truck**, stays hidden, and reappears.
- A hidden car slows, stops or changes lanes unexpectedly; prediction should become uncertain and **not falsely claim continued exact position**.
- A car approaches from the rear and overtakes; track identity persists across front/side/rear sectors.
- Two cars cross or briefly overlap in the sensor field; monitor ID switches.
- Truck/trailer obscures side and rear objects; identify spatial unknown zones.
- A sensor is disconnected or fed missing frames; show degraded state.

### Evaluation and logging

Record timestamps, per-sensor detections, track IDs, observed/predicted/unknown status, 2D/3D positions where measurable, confidence/covariance, sensor health, and associated ground-truth positions.

Measure:
- Detection recall/precision and identity continuity.
- Prediction drift after different occlusion durations.
- Reacquisition time and ID correctness after occlusion.
- False 'clear' claims — blank space is **not** asserted to be empty.
- End-to-end latency from observation to display, including p95/p99.
- Performance of camera-only, radar-only, LiDAR-only and fusion variants **where relevant hardware actually produces useful data**.

## Important limits of scaling

- A 1:32 model does not automatically recreate real vehicle sensing. Sensor wavelength, reflectivity, radar target signature, angular resolution and minimum measuring range do not scale.
- A model can test relative geometry and tracking logic, but **cannot validate real highway-speed capability** or weather robustness.
- Test the separately proposed **30 mph nominal relative-velocity requirement** on realistic recorded data/synthetic scenarios rather than claiming it from toy-car movements.
- No active-road testing and no reliance on a prototype HUD while driving.

## Kit options discussed, not chosen

- Cardboard/foam board + road tape + model vehicles: **recommended lowest-cost** starting point.
- Candylab Roadworks: modular roadway pieces, but manual movement.
- Faller Car System: automated HO-scale guided cars, useful for repeatable traffic but too small for carrying typical development LiDAR hardware.
- Scalextric/Carrera: motorized slot cars; basic tracks constrain lane changes, but larger models can be easier to detect.
- No model-road kit has been purchased or selected.

## Acceptance criteria for the first physical experiment

A correctly calibrated ego-perspective test sensor detects at least one moving model vehicle; the independent overhead camera provides ground truth; the chevron HUD represents observed tracks, then marks a temporarily occluded vehicle as **predicted** (not observed), with growing uncertainty, and expires it when the estimate is no longer defensible. All transitions are logged for offline comparison.

The first build can succeed without neural networks, LiDAR, radar, motors, or a car-sized prototype.

**References:** [Decisions](DECISIONS.md) · [HUD](HUD.md) · [Sensor plan](SENSOR_PLAN.md) · [Safety](../../SAFETY.md)
