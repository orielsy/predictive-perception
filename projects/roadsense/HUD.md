# RoadSense HUD — compact surround-awareness display

**Status:** Proposed UI research specification; not prototyped or usability-tested.  
**Reference concept:** The owner's supplied overhead road image: **the white car is the ego vehicle**, while surrounding traffic is represented by **small chevrons**. The owner also identified the **upper-left windshield region** as the preferred future HUD placement, with no detailed imagery.

## Product intent

Provide a glanceable, minimal top-down awareness map of traffic around the vehicle. The display is **not** a navigation map, camera feed, or augmented object-overlay on the road.

Keep perception complexity behind a simple UI: a white ego car (or equally unmistakable white icon), nearby direction-aligned chevrons, and sparse alert emphasis.

### Core concept

```text
         FORWARD
  ┌────────────────────┐
  │       ˄     ˄      │
  │  ˄                 │
  │         ▰          │   ▰ = user's white vehicle, fixed and forward-facing
  │    ˄         ˄     │   ˄ = direction-aligned vehicle chevron
  │  ◇             ˄   │   ◇ = uncertain / occluded estimate
  │        ˄           │
  └────────────────────┘
          REAR
```

ASCII is illustrative, not a literal arrangement or final graphic. Real positions and orientations must derive from observations/tracks, not aesthetically chosen placements.

## Visual encoding

| Element | Proposed visual treatment | Meaning |
| --- | --- | --- |
| Ego vehicle | Solid white simplified car shape, fixed center, oriented up | The monitored vehicle |
| Direct observation | Solid sharp chevron | Recently verified sensor observation |
| Predicted / occluded | Outlined or translucent chevron plus uncertainty envelope | An estimated vehicle location, **not currently observed** |
| Unknown space | No vehicle icon; optional unobtrusive shaded uncertain sector | Presence/absence cannot be concluded |
| Elevated relative relevance | Brief emphasis (e.g. brightness/outline), separately tested | Interesting relative motion or proximity, *not a validated danger warning* |
| Lost / stale track | Disappear on expiry; log why | Prediction no longer reliable enough to visualize |
| Sensor failure / degraded mode | Small unmistakable status indicator | Awareness may be incomplete |

**Do not** use a missing chevron to imply that a lane is safe or empty. Do not promote a predicted position to a solid observed symbol without a new supporting observation.

Use accessible luminance/shape contrasts; the observed/predicted distinction cannot rely on color alone.

## Information density and placement

- Initial mockup: **small top-left display**, not a full windshield overlay.
- Restrict graphic detail. Prefer **5–7 salient vehicle chevrons** in the visible window and group/clip extras; do not falsely suppress nearby hazards simply to hit a count.
- No car photography, road textures, individual speed numbers, paragraphs, or moving decorative animations.
- Map is **ego-fixed** with forward up; relative locations update around it.
- Provide selectable spatial scale in research replay (e.g. near field versus extended), with unmistakable scale feedback; do not imply precise distances when calibration is uncertain.
- The windshield placement is an *interaction concept*, not approval to mount equipment or project on glazing. Any real automotive implementation needs visibility, human-factors, optical and legal review. Start on a desktop or bench display.

## State machine for each tracked object

```text
UNOBSERVED --direct sensor support--> OBSERVED
OBSERVED --temporarily occluded--> PREDICTED
PREDICTED --reacquired--> OBSERVED
PREDICTED --stale/high uncertainty--> EXPIRED (remove)
any state --unresolved sensor data--> UNKNOWN/DEGRADED (do not invent position)
```

Track age and uncertainty determine display treatment. No hard-coded track expiry is assumed; evaluation should compare candidate timeouts and error costs.

## Interaction research

1. **Normal:** Quiet map; observed surrounding vehicle chevrons.
2. **Occlusion:** A confirmed chevron becomes outlined/faded, with uncertainty growing over time.
3. **Reacquisition:** Same vehicle is matched and returns to observed styling without a misleading jump where possible.
4. **Unknown:** Occlusion with insufficient history removes the predicted marker rather than inventing a confident vehicle.
5. **Clutter:** More objects than can be legibly displayed; test prioritization rather than hiding inconvenient data.
6. **Degradation:** Sensor stopped or miscalibrated; visualization clearly signals degraded confidence.

### Evaluation

- At a brief glance, can participants correctly identify **front/rear/left/right** occupancy?
- Can they distinguish **observed vs predicted vs unknown**?
- Does occlusion visualization improve or harm comprehension compared with camera-only blind-spot indicators?
- Does the display invite over-trust, distract from simulated driving tasks, or lead to false assumptions about empty space?
- Measure response accuracy, reaction time, mistakes, workload, and annoyance on **replayed/simulated scenarios**, not while participants drive.

## Acceptance gate for UI v1

A replay of synchronized camera + LiDAR tracks renders the ego symbol and chevrons with stable relative placement, appropriate heading and clearly differentiated observed/predicted/unknown states. A simulated occlusion causes uncertainty and expiry to appear; the display never manufactures a confirmed car.

**Next:** [Passenger-car and trucking architecture](ARCHITECTURE.md) · [Sensor range and prototype purchasing plan](SENSOR_PLAN.md) · [Safety policy](../../SAFETY.md)
