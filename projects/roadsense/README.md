# RoadSense — predictive traffic-awareness research

**Status:** Concept documented; no implementation or validation yet.  
**Concept:** Halo-inspired, glanceable awareness visualization of surrounding traffic.  
**Initial test environment:** Recorded footage, synthetic driving scenarios, or simulation only.

## Vision

Explore whether **camera + LiDAR sensor fusion**, tracking, and temporal reasoning can provide a coherent spatial view of nearby vehicles, including **uncertain estimates** for vehicles temporarily occluded by other traffic, while respecting the difference between direct observation and prediction.

The goal is to study prediction and visualization, not to build a ready-to-use driver-assistance system.

## Core idea

A driver cannot look in every direction simultaneously. An observed vehicle can become hidden, and its most recent trajectory may offer a useful *probabilistic* estimate of where it might be now. Such estimates must be shown as uncertain and must expire quickly rather than being presented as measured facts.

## First demonstration

1. Ingest **synchronized camera and LiDAR recordings**, or synthetic scenarios containing both image frames and 3D ranging data. Physical LiDAR is **not necessary for the first software experiment**, but it is part of the intended design.
2. Detect visible vehicles, associate their image observations with LiDAR depth/geometry where available, and assign tracks across frames.
3. Present a simplified top-down / radar-like display inspired by situational-awareness UIs in games.
4. Differentiate three classes of display information:
   - **Observed:** detected vehicle with a recent supporting observation.
   - **Predicted:** extrapolated position range for a temporarily occluded track, visibly uncertain and time-limited.
   - **Unknown:** insufficient observations; deliberately blank or unknown, never fabricated.
5. Log track observations, occlusions, extrapolations, confidence/uncertainty and expiration.
6. Evaluate all visualization and tracking **offline**, without live driving use.

### Conceptual pipeline

```text
Synchronized camera + LiDAR recordings / multisensor simulator
  → sensor timestamps + extrinsic/intrinsic calibration
  → image-based vehicle detection + LiDAR ranging/3D measurements
  → cross-sensor association and fusion
  → multi-object tracking / relative position, motion and lane context
  → occlusion reasoning + uncertainty propagation
  → observed / predicted / unknown scene model
  → radar-like visualization + replay + evaluation log
```

**LiDAR is a core sensor in the RoadSense concept**, alongside cameras. The research questions are which LiDAR hardware and mounting geometry to use, how to synchronize and calibrate both sensors, and what a fused system adds versus each sensor alone. The earliest milestone may use recorded or synthetic LiDAR instead of buying hardware.

## Suggested milestones

| Milestone | Deliverable | Evidence |
| --- | --- | --- |
| A. Scenario runner | Time-aligned recorded/synthetic camera + LiDAR replay | Reproducible dual-sensor clips |
| B. Perception and fusion | Image vehicle detections plus associated LiDAR ranges / 3D positions | Spatial overlays and calibration checks |
| C. Tracking and occlusion | Persistent track IDs, motion estimates, uncertainty, and track expiry | Hidden/reappearing-vehicle scenarios |
| D. HUD | Top-down display with distinct observed/predicted/unknown styling | Replayable screen recording |
| E. Research evaluation | Compare camera-only, LiDAR-only and fused tracking against ground truth | Missed-track, false-track, latency, lead time and spatial error measurements |

## Metrics and failure cases

- Detection recall/precision and identity-switch frequency.
- Position error for **directly observed** vehicles using cameras, LiDAR and fusion separately.
- Camera–LiDAR temporal synchronization, calibration drift, sensor disagreement and field-of-view coverage.
- Predicted position error against ground truth after 0.5 s, 1 s, 2 s, etc. of occlusion.
- Calibration quality and how lane changes degrade extrapolation.
- Probability calibration and premature/stale track persistence.
- End-to-end processing latency and dropped frames.
- Heavy traffic, poor lighting, rain, glare, road curvature, occluding trucks, and unexpected maneuvers.

Any forward extrapolation is conditional; predictions never imply certainty about an unseen vehicle.

## Future investigation

- LiDAR hardware selection, placement, range limitations, weather behavior and power/bandwidth requirements.
- Radar as an additional or alternative sensor, distinct from the core camera + LiDAR concept.
- Lane-relative positioning and relative velocity estimates; potentially incorporate the time since the driver last checked mirrors as contextual research data.
- Visualizing uncertainty with fading regions / envelopes rather than fixed ghost cars.
- Human-factors study: does the visualization improve understanding in controlled, non-driving experiments, or increase distraction?
- A compact embedded version using the Alta A311D, if performance and sensor support make sense.

## Safety and privacy boundaries

No prototype may be marketed or relied upon as a blind-spot monitor, collision-avoidance system, driving decision aid or autonomous driving controller. Do not connect to steering, braking or throttle, and do not run distracting live-display tests with a person driving. Recorded driving data may include sensitive location and identifiable third-party information; minimize and protect it.

See [repository safety policy](../../SAFETY.md).

## Open questions

- What information is actually helpful in a fast glance?
- Which representations distinguish observed, predicted, and unknown most unambiguously?
- How quickly should an occluded track expire?
- Is a first-person augmented view useful, or is a minimal top-down scene more legible?
- How much does **camera + LiDAR fusion** improve distance estimation, positioning, tracking continuity and confidence over camera-only or LiDAR-only baselines?
- What mounting and calibration configuration provides useful coverage around a vehicle?
