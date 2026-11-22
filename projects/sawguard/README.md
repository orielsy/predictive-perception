# SawGuard — predictive machine-hazard awareness

**Status:** Concept documented; no implementation or validation yet.  
**Candidate platform:** Libre Computer Alta AML-A311D-CC, inexpensive USB camera, speaker.  
**Initial test environment:** Mock workstation or completely de-energized saw; no operating blade.

## Vision

Investigate whether computer vision can recognize potentially hazardous hand approaches to a machine-tool danger area *before* a hand enters that area. The goal is measurable anticipation, not an assertion of safety or a replacement for physical protection.

## Hypothesis

Tracking hand positions and motion over time may produce useful warning lead time beyond static proximity checks. It might also create excessive false alerts or miss rapid or obscured movements. Those outcomes must be measured.

## First demonstration

1. Display live camera feed from a mock table-saw work area.
2. Calibrate a **virtual blade/hazard region** on the image.
3. Detect and track a colored glove/marker first; progress to natural-hand tracking if reliable.
4. Estimate hand trajectory and proximity with a simple, inspectable baseline.
5. Produce three distinct audio cues and log transitions:
   - **Awareness:** approach to monitored area; soft tone.
   - **Warning:** estimated trajectory intersects hazard region; urgent pulses.
   - **Simulated contact:** hand enters virtual hazard zone; distinctive "you're dead" sound as an **experimental event only**.
6. Save synchronized timestamps, tracks, predicted events, and ground-truth annotations to replay trials.

### Conceptual pipeline

```text
Camera or recorded video
  → frame timestamps + calibration
  → hand/marker detector
  → tracking + velocity/trajectory estimate
  → virtual hazard-zone evaluator
  → awareness/warning/simulated-contact events
  → audio cues + video overlays + structured log
```

The neural inference stage is optional initially. Validate acquisition and geometry before migrating compatible models onto the Alta NPU.

## Suggested milestones

| Milestone | Deliverable | Evidence |
| --- | --- | --- |
| A. Perception | Camera preview, virtual zone, hand/marker bounding region | Repeatable overlay demo |
| B. Motion | Track IDs, direction/speed, predicted intersection | Annotated replay sequences |
| C. Feedback | Three audio states and event timeline | Audio/event timestamps matched to video |
| D. Evaluation | Labeled recordings, metrics dashboard, error cases | Lead-time and error-rate report |
| E. Acceleration | Compare CPU and NPU with compatible model, if useful | Model compatibility, end-to-end latency and stability measurements |

## Evaluation design

Use a **simulated hazard**, with labeled trajectories covering normal feeding-like movement, reaching across a marked zone, withdrawing, approaching and stopping, false approaches, occlusion, and lighting changes. No powered cutting.

Capture:
- Ground-truth timestamp for virtual-zone crossing.
- Warning timestamp and resulting **lead time** for each staged hazard.
- False warning rate on safe trials and missed warning rate on hazardous trials.
- Detector/tracker stability, detection dropouts, and uncertainty.
- Video capture → detection → audio output latency distribution (not just NPU inference time).
- Behavior under occlusion, low light, gloves, non-hand objects and camera changes.

Do not optimize for a single easy demo or publish a safety rating based on staged data.

## Architecture notes

- **Baseline:** OpenCV + simple marker tracking and explicit geometry.
- **Later models:** Hand/pose detection if model performance and hardware compatibility justify it.
- **Alta NPU:** The advertised accelerator performance is not an end-to-end guarantee. Test driver stability, model operator support, quantization, thermal behavior, and preprocessing overhead.
- **Data:** Save videos only when needed, plus reproducible event records and calibration metadata.
- **Extensibility:** A typed event stream can support new *simulated* outputs and offline analyses. It is **not** an interface to live machine controls.

## Open questions

- Which camera perspective minimizes blind spots and occlusion?
- Can a single camera offer useful trajectory estimates, or is depth needed?
- Does a rules-based tracker outperform a temporal neural model on small datasets?
- How early can warnings occur without intolerable false positives?
- What happens when a hand disappears behind a workpiece?

## Out of scope for the first version

Running saw tests, any motor/brake/retraction command, certification, retrofit promises, claims of injury prevention, or treating sound cues as protective safeguards.

See [repository safety policy](../../SAFETY.md).
