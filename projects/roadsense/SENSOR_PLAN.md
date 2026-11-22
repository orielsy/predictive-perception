# RoadSense LiDAR range and prototype sensor plan

**Status:** Research estimate as of 2026-11-22, not a purchase order or product certification.  
**Sensor architecture decision:** Cameras + LiDAR are core to the intended RoadSense system; initial **software** experiments should use existing synchronized recordings before purchasing hardware.

## What range do we actually need?

These are **design targets for experiments**, not proven protective distances:

| Area | Working research range of interest | Motivation |
| --- | --- | --- |
| Immediate sides / trailer flanks | 0–10 m | Near-field occlusion and blind zones |
| Adjacent lanes / rear corners | 10–40 m | Tracking vehicles alongside or approaching |
| Extended side/rear | 40–100 m | More history for closing traffic; hardware/weather constraints rise |
| Forward roadway | 50–150 m or more, aspirational | Earlier perception at speed; often needs a different forward sensor |

The range of a sensor depends on target reflectivity and angle, not just its headline maximum. A sensor rated **40 m on a dark (10% reflective) target** is not a reliable 100 m dark-car detector even if its electronics permit longer returns.

### Range is not safety response time

Illustration only: with **20 m/s relative closing speed**, an object at 40 m has **2 seconds** before its relative separation would close under constant-speed assumptions. This excludes perception gaps, lane changes, driver reaction and vehicle dynamics and is **not** a safe stopping model. A shorter-range sensor can still be valuable for nearby traffic awareness but insufficient for some high-speed scenarios.

## Sensor candidates

| Category | Example | Verified range / geometry | Limitation for RoadSense |
| --- | --- | --- | --- |
| 2D educational scanner | SLAMTEC RPLIDAR S3 | 360° planar scan; 0.05–40 m at 70% reflectivity, only ~5 m at 2% reflectivity | One scanning plane; misses objects above/below it; cannot directly provide 3D boxes, not the intended car/truck sensor |
| Compact 3D 360° LiDAR | Livox Mid-360 | 360° horizontal, 59° vertical; 40 m at 10% reflectivity, 70 m at 80% reflectivity | Useful near/mid-range research sensor, but limited for long-range highway awareness |
| Compact 3D 360° LiDAR | Livox Mid-360S | 360° horizontal, 59° vertical; typical 40 m at 10% reflectivity; up to 100 m **cutoff** | The 100 m figure is a cutoff, **not** the demonstrated range on a dark car |
| Higher-end 3D 360° family | Hesai XT or Ouster OS1-class | Model-specific 360° coverage, more options for density/range | Much more expensive; exact model specs, vendor integration and quotes required |

**Source links:**
- [SLAMTEC RPLIDAR S3 specification](https://www.slamtec.com/en/s3/spec)
- [Livox Mid-360 specifications](https://www.livoxtech.com/mid-360/specs)
- [Livox Mid-360S specifications](https://www.livoxtech.com/mid-360s/specs)
- [Hesai XT-series](https://www.hesaitech.com/product/xt16-32-32m/)
- [Ouster OS1](https://ouster.com/products/hardware/os1)

These devices are **not automotive safety-certified retrofit kits**. Weather, protective windows, vibration, power and mounting need separate analysis.

## Cost bands (planning allowances, not supplier quotes)

| Stage | Rough hardware/data allowance | Purchases |
| --- | --- | --- |
| A. Sensor-data replay | $0 sensor hardware; storage/cloud costs may apply | None; use nuScenes mini and desktop |
| B. Planar LiDAR bench experiment | $150–700 | Optional 2D scanner + cable, camera and a non-driving static rig |
| C. Compact 360° 3D LiDAR bench experiment | **$900–2,000+** | 3D sensor, power/network adapter/cable, camera, mounting, compute as needed |
| D. Full passenger-car multi-sensor research fixture | Several thousand dollars and up | 3D LiDAR, multiple cameras, optional radar, calibration and enclosure |
| E. Truck cab + trailer coverage | Higher, architecture dependent | Multiple sensor nodes, trailer connectivity, ruggedization and calibration |

Retail examples observed in November 2026: a Livox Mid-360S listing around **$789** from SensorLidar and an RPLIDAR S3 listing around **$620** from AIFITLAB. The listed prices vary substantially between vendors and accessories; compare complete kit, model revision, shipping, warranty, connector/cable, power and support. This is not a price guarantee.

Examples:
- https://www.sensorlidar.com/products/mid-360s-small-but-mighty-3d-lidar
- https://aifitlab.com/products/slamtec-rplidar-s3-lidar

## Recommended first hardware purchase — **none yet**

### Phase 0 — Data-first

Use the [nuScenes mini](https://www.nuscenes.org/) dataset (1 LiDAR, 6 cameras, 5 radars in the full sensor suite). Ingest timestamps and calibration, replay one scene with multiple cars, generate 2D chevrons and measure observed/predicted track state. This demonstrates RoadSense's core concept without the complications of outdoor hardware.

### Phase 1 — Sensor validation on a static bench

If the replay produces useful results and a physical demonstration is justified, prefer **one true 3D 360° sensor**, such as the Livox Mid-360S class, rather than buying an inexpensive 2D scanner and assuming it can deliver full vehicular 3D awareness.

- Obtain a genuine supported revision with compatible Livox SDK / drivers and full power/data breakout.
- Use a stationary test fixture, with people/objects moving around it in a controlled non-driving environment.
- Test dark/bright targets at known distances and azimuths, near-field gaps, occlusion, multi-target identity stability and total pipeline latency.
- Do not attach a prototype HUD to a car used for driving or claim blind-spot protection.

### Phase 2 — Compare sensing designs

Evaluate **one elevated 360° sensor** against **distributed side/rear nodes** in simulation or carefully controlled static measurement. For trucks, specifically quantify the unknown space alongside the trailer and the rear shadow.

### Phase 3 — Embedded migration

After a workstation-based reference implementation succeeds, profile the Libre Computer Alta A311D: capture/network, 3D point-cloud processing, camera inference, cross-sensor fusion, HUD rendering, memory and sustained thermal load. Its NPU is not automatically useful for generic point-cloud algorithms.

## Measurements to collect

- Actual detection probability at distance by dark/light target and angle.
- Near-field visibility map around vehicle/cab/trailer geometry.
- Scan/frame time synchronization error; camera/LiDAR calibration residual.
- Object association accuracy; persistence and position error during occlusion.
- Sensor/processor end-to-end latency and p50/p95/p99.
- False claims of free/empty space (especially important).
- Sensor outage, rain/fog/dirt and thermal behavior in controlled conditions.

## References and software

- [nuScenes data and sensor configuration](https://www.nuscenes.org/data-format)
- [Livox SDK2](https://github.com/Livox-SDK/Livox-SDK2)
- [Livox ROS/ROS2 driver](https://github.com/Livox-SDK/livox_ros_driver2)

**Decision gate:** Spend on hardware only after the sensor-data replay shows a credible, legible, uncertainty-aware chevron HUD and identifies a specific physical experiment that recorded datasets cannot answer.
