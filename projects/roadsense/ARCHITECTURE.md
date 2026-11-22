# RoadSense sensor-fusion architecture: passenger car vs. truck

**Status:** Research design, not a validated vehicle installation.  
**Core modalities:** Cameras **and 3D LiDAR**, each with independent timestamped observations. Radar is a potential later supplement, especially for weather and radial velocity.

## Shared system architecture

```text
SYNCHRONIZED SENSOR INPUTS / RECORDED DATA
   ├── wide-angle cameras  → semantic detections, lane/context
   └── 360° 3D LiDAR       → 3D ranges, object clusters and geometry
        [optional radar]   → radial velocity / degraded-visibility context
                     |
       calibration + timestamp alignment + ego-motion
                     |
       candidate detections + cross-sensor association
                     |
       multi-object tracker (IDs, pose, velocity, uncertainty)
                     |
       occlusion model + track prediction + confidence decay
                     |
       scene model (OBSERVED / PREDICTED / UNKNOWN)
                     |
       chevron HUD replay + evaluation metrics + event log
```

### Sensor principles

- **360° horizontal FOV is not 360° visible roadway**: the sensor may be blocked by roof, hood, bodywork, road geometry, trailer, cab and other traffic.
- **Distance capability changes with reflectivity, angle, rain/fog/snow, optics and mounting**; a listed cutoff range is not guaranteed vehicle detection range.
- **LiDAR does not see through a truck**: prediction is informed extrapolation, not recovered observation.
- **Camera** helps classify vehicles/lane markings; **LiDAR** helps with measured 3D geometry/range. Sensor fusion requires calibration, time synchronization and disagreement handling.
- Relative speed from successive position observations is noisy without ego-motion compensation; radar may materially help but is not assumed for v1.
- Unknown sectors must remain explicitly unknown when sensor coverage is absent or blocked.

## Passenger-car architecture

### Intended full concept (not initial purchase list)

- High-mounted **360° 3D LiDAR**, in a position with minimal self-occlusion.
- Wide-angle cameras with useful front, rear and side views (layout to be experimentally derived).
- Vehicle-motion context (IMU/GNSS where available in recorded datasets).
- Optional corner radar for radial speed and poor-weather resilience.
- Processing: high-performance desktop during research; later characterize an embedded target such as the Libre Alta A311D for limited workloads.

**Strength:** One high-mounted LiDAR may capture much of the surrounding road; cameras provide recognizable objects and lane cues.

**Limitations:** Adjacent vehicles can disappear below the sensor's clear line of sight. Mounting and calibration on a moving road vehicle require serious engineering, mechanical/electrical robustness, and review of windshield visibility constraints. Single-sensor claims of 360° blind-spot elimination are not defensible.

### Prototype path

1. Replay synchronized, calibrated camera + LiDAR datasets.
2. Build fixed-ego top-down tracks + chevron UI.
3. Compare camera-only, LiDAR-only and fused systems against labeled ground truth.
4. Optional **stationary, supervised, non-driving** sensor rig to characterize range, reflections and near-field gaps.
5. Do not test a windshield projection or a live in-vehicle display with an active driver.

## Truck / articulated vehicle architecture

The trailer fundamentally changes sensing: a cab-mounted 360° scanner cannot reliably observe beside and behind a long trailer.

### Intended geometry

- Cab-mounted 360° 3D LiDAR for broad surroundings.
- **Side/near-field sensors** at locations chosen to cover cab sides and trailer-adjacent regions.
- Rearward sensors or camera coverage near the trailer end if viable.
- Additional **side and rear cameras**, especially covering the swept path and vulnerable road users.
- Optional corner/rear radar for closing traffic and bad weather.
- Articulation-aware geometry: relative cab/trailer pose, varying trailer lengths, trailer side occlusion, swing-out and turning envelope.
- Rugged communication, power, timestamps, weather resistance and sensor-health monitoring.

**Key limitation:** A single cab-top 360° LiDAR is particularly insufficient for truck near-field trailer coverage. Do not claim it makes trucks 'no longer blind'; the research aim is **reducing unobserved space and clarifying residual uncertainty**.

### Prototype path

1. Simulate an articulated truck and its masked fields of view.
2. Compare cab-only versus cab + trailer-side/rear sensing in replay.
3. Model trailer articulation, predicted motion and near-field occlusions.
4. Evaluate cyclists/motorcycles as well as passenger vehicles; measure false emptiness assertions.
5. Consider purpose-built safety regulations and domain specialists before any operational testing.

## Relative-motion tracking requirements (research targets)

RoadSense prioritizes **ego-relative position and velocity**, not absolute highway speed. The owner selected **30 mph nominal differential** (~13.4 m/s) with **50–60 mph differential as stretch targets**. Proposed software-update goals are **10–20 Hz tracking** and **30–60 FPS HUD rendering**; none have been implemented, benchmarked or validated. Coverage and detection across the near-zero-radial-velocity case matter too (e.g., neighboring vehicle moving at the same speed).

Do not assume co-moving highway traffic is inherently safe: stopped vehicles, oncoming traffic or a large same-direction differential create serious closing rates. Sensor field-of-view, correct time alignment, ego-motion compensation, tracking continuity and uncertainty handling remain essential. Validate higher differentials on recorded/full-scale synthetic data; the tabletop miniature cannot establish full-scale performance.

The planned **[cardboard miniature highway](MINI_HIGHWAY.md)** can answer questions about relative geometry, occlusion and HUD interpretation before purchasing long-range sensing hardware.

## Workload partition

| Stage | Practical research implementation |
| --- | --- |
| Dataset ingestion and calibration | Desktop Linux/Windows capable of large sensor logs; use standard formats |
| LiDAR preprocessing | Geometry/voxel/point-cloud algorithms; typically CPU- or GPU-heavy |
| Camera classification | Desktop GPU first; compatible compact networks might later use Alta NPU |
| Cross-sensor association | CPU tracking, calibration and uncertainty-aware data association |
| Prediction | Start with constant-velocity + uncertainty growth, then assess more advanced filters |
| HUD | Lightweight 2D renderer consuming a typed scene-model stream |
| Logging | Timestamped ground truth, track IDs, sensor health, predictions and UI transitions |

**Alta caveat:** Its NPU accelerates **compatible neural-network inference**, not arbitrary LiDAR point-cloud fusion, tracking, and scene rendering. Six-core CPU and available memory may limit how many streams can be handled at once. Do not claim real-time end-to-end 360° processing without benchmarking the exact workload.

## Typed track schema (proposal)

```json
{
  "track_id": "example-42",
  "timestamp_ms": 1800,
  "state": "predicted",
  "position_vehicle_frame_m": {"x_forward": 12.4, "y_left": -3.0},
  "velocity_relative_mps": {"x_forward": -1.5, "y_left": 0.2},
  "heading_rad": 0.0,
  "uncertainty": {"position_sigma_m": 2.1},
  "last_observed_ms": 1250,
  "sources": ["lidar", "camera"]
}
```

Coordinates and sample values are illustrative. Clarify whether `sources` identifies **last measurement contributors** rather than fresh support for a predicted track. Future models should include covariance and calibration metadata.

## Evaluation matrix

| Scenario | Passenger car | Truck |
| --- | --- | --- |
| Adjacent-lane car hidden by SUV | Track continuity and uncertainty expiry | Same, plus longer side occlusions |
| Fast-approaching rear vehicle | Relative velocity, range and data age | Trailer-rear and long flank coverage |
| Low-speed near-field object | Bodywork and sensor elevation occlusion | Cab corner, trailer side and swept path |
| Lane change/turn | Ego-motion correction | Cab/trailer articulation |
| Rain/glare/poor returns | Confidence downgrade | Confidence downgrade over larger sensor layout |
| Sensor fails or loses clock sync | Status and no false clear-state | Same, additional trailer-cable/connectivity failures |

## Primary data source for the initial experiment

[nuScenes](https://www.nuscenes.org/) provides real synchronized camera, LiDAR and radar sensor data, including six cameras and a 360° LiDAR, and offers an official mini subset for early development. Review its non-commercial data-use terms before downloading or redistributing recordings.

## Non-negotiable safety boundary

No code from this research design may operate vehicle steering, braking, throttle, or safety systems. Do not present the HUD as a certified blind-spot monitor or use it for active driving decisions.

**Next:** [HUD specification](HUD.md) · [Sensor selection](SENSOR_PLAN.md) · [Safety policy](../../SAFETY.md)
