# RoadSense — continuation handoff

**As of:** 2026-11-22  
**Repository:** `orielsy/predictive-perception`  
**Status:** Documented concept / research design, **no implementation, sensor purchases or tests yet**.

## Read in order

1. [DECISIONS.md](DECISIONS.md): owner intent, hard distinctions, relative-motion targets, affordability principle.
2. [HUD.md](HUD.md): white ego vehicle, surrounding directional chevrons, observed/predicted/unknown visual states.
3. [MINI_HIGHWAY.md](MINI_HIGHWAY.md): proposed cardboard highway, hidden-car experiment and separate overhead ground-truth camera.
4. [ARCHITECTURE.md](ARCHITECTURE.md): camera + LiDAR fusion, optional radar, car vs truck/trailer.
5. [SENSOR_PLAN.md](SENSOR_PLAN.md): ranges, candidate hardware, staged budget and comparisons.
6. [../../SAFETY.md](../../SAFETY.md): strict research-only scope.

## Concise goal

Affordable 360° surrounding-traffic *awareness*, not autonomous driving. Represent the white ego car in a compact overhead windshield-HUD concept; draw nearby cars as directional chevrons. Distinguish **measured / presently observed**, **predicted while occluded**, and **unknown**, without fabricating awareness. Truck blind-spot reduction is a key future use case, not guaranteed elimination.

## Latest clarified decisions

- Cameras **and 3D LiDAR** are intended core sensors; radar is a low-cost experimental complement, **not yet mandatory**.
- Do not over-spec for absolute road speed. **Nominal >=30 mph differential**, stretch **50–60 mph differential**, and test high-closing-speed exceptions. Proposed tracking **10–20 Hz** and HUD **30–60 FPS** are **targets**, not measured.
- A small **upper-left** HUD would be the future interaction form, but no on-road or active driving display test is approved.
- Lower the spend barrier: use the already-owned **Libre Alta AML-A311D-CC** where useful, but begin with desktop simulation/recordings.
- Build **cardboard highway** with model cars, a box truck for occlusion, and a **separate overhead ground-truth camera**. Manual toy movement is fine; automated kits are optional.
- Low-cost sensors are valid research tools; cheap 2D LiDAR and motion radar **do not imply** full-scale 360° automotive awareness.
- Budget claims are estimates from exploratory listings, **not current verified purchase quotes**. Sub-$200 may cover a basic model test, **not** automotive-grade or reliable 3D LiDAR.
- NLOS indirect-radar/LiDAR sensing was discussed but **not demonstrated** for any candidate inexpensive module.

## Next implementation slice (when owner resumes)

**Goal:** One repeatable offline demo with visible state transitions, before purchasing hardware.

1. Create a minimal test runner with an ego-fixed 2D coordinate system and timestamped **synthetic vehicle tracks**.
2. Render white ego car plus vehicle chevrons; encode observed/predicted/unknown distinctly and keep orientation/position stable.
3. Simulate a box truck blocking one car, predict the hidden track with increasing uncertainty, then either reacquire it or expire it.
4. Persist structured time series of sensor observations, predicted positions, uncertainty, and HUD state transitions.
5. Add a replay viewer and benchmark lead time, position error through occlusion, false track persistence, dropped updates.
6. Next, use recorded camera + LiDAR datasets; then the miniature physical rig; only buy additional sensor hardware to answer an identified unanswered question.

## Decisions not made

No sensors selected or purchased; no final RADAR inclusion, LiDAR mount, camera count, roadworthy HUD, truck rollout, or certified protection design. No model or algorithm has proven suitability yet.

## Non-goals

No vehicle control, no safety claim, no misleading 'empty lane' indicator, no live on-road experiment with a driver looking at prototype HUD, and no implication that LiDAR/radar can reliably see through metal trucks.

## First milestone success

Replay shows a known car becoming occluded, chevron changing from solid to uncertain, uncertainty expanding through time, and eventual re-observation or deletion; tests and logs let a future developer reproduce this transition.

**Owner preference:** Prefer quick targeted tests during implementation and avoid expensive build/CI loops for each small change; run heavier validation at integration milestones.
