# Safety and ethics policy

These projects explore **perception and prediction**, not certified protective functions. Early demonstrations must not imply that an alert reliably prevents injury.

## SawGuard

- Test with recorded footage, a mock work surface, or a **fully de-energized** saw. Never conduct hazardous hand-approach experiments with a rotating blade.
- Do **not** wire the prototype to saw motors, switches, emergency-stop circuits, blade brakes, or retraction mechanisms.
- Do **not** bypass blade guards, riving knives, push sticks, or other established protections.
- The prototype's warning sounds, including any dramatic "virtual contact" sound, are simulation feedback. They are **not** proof of an injury or a protective intervention.
- A prototype detecting unsafe approach must not be treated as a substitute for guards, safe technique, or a certified safety system.
- Any eventual protective control or blade withdrawal must be independently engineered and validated by competent machinery-safety professionals, including risk assessment and applicable standards. A generic aftermarket actuator does not make this software safety-rated.

## RoadSense

- Develop on recorded video, synthetic scenarios, simulation, and controlled non-driving bench tests.
- Do not require a driver to interact with prototype displays during driving.
- Do not offer unvalidated live warnings or inferred blind-spot positions as facts.
- Do not connect experimental predictions to steering, braking, throttle, or any vehicle safety controls.
- Clearly label observations, predictions, and unknown regions, especially through occlusion. Expire stale tracks rather than displaying them as confirmed vehicles.
- Handle video, location, and license plate data carefully; minimize storage and respect privacy and local laws.

## Shared engineering requirements

- Instrument timestamps for acquisition, inference, alerting, and user-visible output.
- Evaluate false negatives, false positives, dropped frames, low light, occlusion, out-of-distribution scenes, and degraded sensors.
- Treat uncertainty and model failure as first-class outputs; don't silently produce false precision.
- Keep a manual test mode and clear indication when models are disabled or unavailable.
- Separate research software outputs from any future safety-rated control or certified actuation system.
- Record limitations in demos, documentation, and distribution materials.

**Status:** These are research prototypes only; no safety performance has been verified.
