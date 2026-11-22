# Predictive Perception

**Research incubator for systems that recognize developing hazards from sensor observations.**

This repository holds two independent concept-stage projects:

| Project | Purpose | First prototype |
| --- | --- | --- |
| [SawGuard](projects/sawguard/README.md) | Explore early recognition of potentially hazardous hand movements around machine tools | Camera + mock saw area + graded audio cues + event logging |
| [RoadSense](projects/roadsense/README.md) | Explore predictive situational awareness of nearby traffic, including temporarily occluded vehicles | Recorded driving footage + tracking/uncertainty visualization + offline evaluation |

## Common hypothesis

Sensors plus motion history may help detect or anticipate hazards **earlier** than waiting for direct contact or immediate proximity. The goal is to **measure** whether prediction provides useful, reliable lead time, not to assume it does.

## Guiding principles

1. **Research first:** Initial tests run on recorded media, simulators, or completely de-energized physical mockups.
2. **Measure, don't assume:** Record prediction lead time, false positives, false negatives, latency, and uncertainty.
3. **Separate observation from prediction:** Distinguish measured object positions from inferred or unknown states.
4. **No safety claims:** An experimental model or alert is not a certified protective system.
5. **Modular implementation:** Treat sensors, detection, tracking, prediction, visualization, and logging as separable components.
6. **Preserve failures:** Log occlusions, dropped frames, missed detections, and uncertain estimates instead of hiding them.

## Project status

Both projects are currently **documented concepts**. No prototype is claimed to be built or validated.

## Safety and deployment boundaries

Read [SAFETY.md](SAFETY.md) before implementing either project. SawGuard must not connect to an operating saw's controls; RoadSense must not be used as a driving aid or trusted for live driving decisions. Any future safety-critical application is a separate engineering and validation undertaking.

## Repository organization

```text
README.md
SAFETY.md
projects/
  sawguard/
    README.md
  roadsense/
    README.md
```

Project briefs capture the working hypothesis, first experiment, proposed architecture, evaluation plan, risks, and future directions. They are not product specifications or proof that the methods will work.

## Hardware context

A potential research target is the Libre Computer Alta AML-A311D-CC (Amlogic A311D) with a CPU/GPU and neural accelerator, but a desktop proof of concept and ordinary CPU processing are acceptable starting points. NPU model support and end-to-end timing need measurement before adopting accelerated inference.
