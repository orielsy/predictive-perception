# RoadSense — decision record

**Updated:** 2026-11-22  
**Status:** Concept / research only. Preserves owner intent and distinguishes decisions from untested targets.  
**Purpose:** Reconstruct the project accurately after interruptions or lost chat context.

## Product definition — agreed direction

RoadSense is an **affordable, 360° surrounding-traffic awareness and prediction system**. Its purpose is to help a person quickly grasp which nearby spaces are occupied, which vehicle locations are merely predicted, and where sensing is incomplete. It is **not** an autonomous-driving system or a collision-avoidance system.

- **Visual style:** Halo-like tactical awareness, deliberately *non-detailed*. The user's vehicle is the **white car** in the center of a simple **top-down view**; surrounding cars are **directional chevrons**, not detailed car models.
- **Proposed placement:** A compact **upper-left windshield HUD** rather than a sprawling augmented-reality windshield overlay. This is a **design concept**, not an approved installation or driver-facing prototype.
- **Sensor architecture:** Cameras and **LiDAR are both core**. Radar is a promising, low-cost **additional experimental modality**, particularly for relative speed and possibly indirect returns, but it is **not yet adopted as a mandatory final sensor**.
- **360° awareness:** Desired coverage around the ego vehicle, including sides, rear and forward traffic. A device advertising 360° scanning is **not** equivalent to 360° unobstructed visibility; roof/body/trailer geometry creates blind regions.
- **Persistent tracking:** Maintain identity and time-aware trajectory estimates when another vehicle blocks a known track. Predicted chevrons **must look different** from observed ones and fade/expire as uncertainty grows.
- **Truck application:** A strong potential use case is reducing practical blind spots along cab and trailer. It cannot make trucks literally 'no longer blind'; unknown sectors must remain explicit.

## Motion requirements — clarified

**Prior framing rejected:** Buying long-range hardware simply because vehicles can travel at 50–100+ mph in absolute road speed. RoadSense's nearby scene is expressed in the **ego vehicle's coordinate frame**.

**Owner target:**
- **Nominal:** Track objects up to at least **30 mph relative speed difference** (about 13.4 m/s).
- **Stretch:** Investigate **50–60 mph relative differential** or greater if sensing allows; *not yet validated*.
- **Proposed research rates:** Tracking estimates approximately **10–20 Hz** and HUD rendering **30–60 FPS**. These are provisional engineering goals, not customer-facing or safety-certified guarantees.
- Relative motion must include both overtaking and falling behind, side-to-side movement, and objects with near-zero radial velocity.
- High **relative closing** speeds absolutely still matter: an ego car at 65 mph approaching a stopped vehicle has roughly 65 mph closing speed. Merely being in motion is **not necessarily safer** than standing still.
- Sensor acquisition/inference latency, time alignment, identity continuity, correct geometry, and coverage are more relevant to the UX than maximum advertised speed.
- A 30 mph closing differential closes ~13.4 m per second under constant-speed and converging-path assumptions. This is a **geometry illustration**, not a safety response criterion.

## Sensor physics: confirmed distinctions to preserve

| Modality | Can contribute | Important limitation |
| --- | --- | --- |
| Camera | Classify vehicles, lane/context cues, optical tracking | Can lose objects to occlusion, glare, weather, darkness |
| 3D LiDAR | Measure 3D ranges and surfaces directly | Cannot generally see **through opaque vehicles**; returns may pass through visible gaps/under vehicles when geometry permits |
| Radar | Direct Doppler/radial velocity, ranges on suitable hardware, better performance in some weather | Strong metal obstacles still block/reflect; inexpensive presence radar is *not* automotive multi-object tracking |
| Temporal tracking | Estimate movement of an already-observed object while hidden | Prediction is **not direct sensing**, degrades rapidly under unexpected maneuvers |

Research into **non-line-of-sight (NLOS) optical/radar sensing** and indirect/multipath returns is interesting, but is a **separate experimental research track**, not a demonstrated capability of our inexpensive candidate sensors.

**Tesla comparison from discussion:** Earlier Tesla radar approaches reportedly exploited signals under preceding vehicles; camera-based Tesla Vision can use temporal context and prediction. A rendered car icon is not proof a car was directly sensed through an obstruction. We should test sensor contributions empirically instead of imitating an OEM architecture.

## Affordability: preserved design principle

**Spend money only once an experiment establishes why more expensive sensing is needed.** We can demonstrate the tracking/HUD concept without an automotive-grade sensor.

Indicative exploratory prices discussed in November 2026 (NOT verified current offers or guaranteed complete-kit prices):

| Candidate experiment | Indicative additional spend | Purpose / limitation |
| --- | --- | --- |
| Recorded/simulated sensor replay | $0 sensor hardware | Build chevron HUD, observed vs predicted logic, evaluate replay |
| Low-cost proximity ToF | ~$20 for sensor | Very short range; not a 360° traffic sensor |
| Budget 24 GHz radar such as LD2450/LD2451 | ~$10–40 module/basics | Exploratory short-range object/motion tests; human-optimized firmware and limited output |
| Cheap 360° **2D** LiDAR | ~$70–110 for some examples | Single scanning plane; potentially useful at miniature scale; not 3D road perception |
| Unitree L1-class **3D 360°** LiDAR | ~$249–309 starting sensor listing | Robotics-grade; modest distance on dark targets, not highway-ready |
| Unitree L2-class 3D LiDAR | ~$419 sensor listing | Denser data, still not safety-rated automotive sensing |
| Livox Mid-360-class 3D research setup | ~$900–2,000+ complete-bench allowance | Higher capability, more cost and accessory/integration burden |

The previously discussed **under-$200** idea applies to a **basic model-highway research prototype**, especially if we already own the Alta and a webcam. It does **not** buy a proper 360° 3D LiDAR automotive system.

Do not assume low-cost radar tracks toy vehicles: miniature targets have small radar cross-sections, motion and firmware heuristics are often human-tuned, and cheap modules may return processed targets rather than raw signal data.

## Miniature highway — adopted as a proposed research testbed

- Build a **cardboard or foam-board multilane highway** with a white ego model car and other model cars/trucks.
- Vary toy-car occlusion, passing, lane changes, rear approaches and long truck/trailer blind regions.
- Use an **independent overhead camera as ground truth**; it is for evaluating our algorithm, not a RoadSense sensing channel.
- Begin with stationary sensors, manually moved cars and a simulation of ego-motion if necessary; optional motorized track later.
- Consider 1:32 for larger vehicles if adding instrumentation; **Faller Car System (1:87)** offers automated paths but is too small for mounting common LiDAR units inside the miniature car. Other discussed options were **Candylab Roadworks**, **Scalextric/Carrera slot cars**, and road tape/cardboard.
- Scale can test geometry, identification, occlusion and UI behavior, **not certify real-world highway sensing, radar returns, reaction time or vehicle safety**.

Details: [MINI_HIGHWAY.md](MINI_HIGHWAY.md).

## Deferred / not yet decided

- Exact cameras, number/placement of LiDAR sensors, and whether radar is a permanent required modality.
- Choice of physical LiDAR/radar kit or automatic miniature-traffic system.
- Physical vehicle HUD implementation, optics or windshield mounting.
- Public product, commercialization, driving trials, automotive integrations, or protection claims.
- Whether the **Alta A311D NPU** is useful for the eventual pipeline. NPU only accelerates compatible neural-network inference; LiDAR point clouds and fusion do not automatically use it.

## Next-time resume point

Open [HANDOFF.md](HANDOFF.md), then **build the first offline top-down chevron replay with correct observed/predicted/unknown states**. Use synthetic tracks or synchronized camera/LiDAR dataset frames. After that, build the cardboard model highway and instrument it with a separate overhead ground-truth camera.

Nothing in this document claims implementation or field-tested performance.

**Related:** [HUD](HUD.md) · [Architecture](ARCHITECTURE.md) · [Sensors](SENSOR_PLAN.md) · [Safety](../../SAFETY.md)
