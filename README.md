# SuperJupiter ThresholdEvents

<!-- SUPERJUPITER-CONSOLIDATION-MIGRATION:START -->
## Consolidated library available

For new projects, start with **[SuperJupiter Sensor Toolkit](https://github.com/Super-Jupiter/superjupiter-sensor-toolkit)**. This standalone package and its published releases remain available. Existing installations need no change.

If migrating with PlatformIO, replace the standalone dependency with:

```ini
lib_deps = superjupiter/SuperJupiterSensorToolkit@0.1.0
```

Replace this standalone include:

```cpp
#include <SuperJupiterThresholdEvents.h>
```

with this consolidated include:

```cpp
#include <SuperJupiter/SensorToolkit/ThresholdEvents.h>
```

The component namespace and behaviour are preserved from standalone **0.1.1**. If using an earlier version, review its API migration requirements first. Use one implementation of this component per application; do not mix standalone and consolidated copies.

See the [consolidated migration guide](https://github.com/Super-Jupiter/superjupiter-sensor-toolkit/blob/v0.1.0/docs/MIGRATION.md) and consolidated README for Arduino/CMake installation. **Hardware validation pending.**
<!-- SUPERJUPITER-CONSOLIDATION-MIGRATION:END -->

Persistent threshold alarms with hysteresis and explicit unknown state.

**Version 0.1.1: evolving preview. Hardware validation pending.**

Original MIT-licensed portable C++11 by [SuperJupiter](https://www.superjupiter.net).
No mandatory service, hidden clock reads, network calls or heap allocation in the core.

## What it does

High alarms enter at >=high and recover at <=low, requiring consecutive qualifying observations spanning the configured duration.
Opposite/intermediate observations cancel a pending transition. Invalid readings explicitly clear certainty to Unknown.
After a gap or invalid reading the next valid input restarts evaluation; no persistence is inferred through missing observations.
Durations and gaps must be below 2^31 ms. No hidden clock, callbacks, latching acknowledgement or safety-controller certification.

## Install

PlatformIO exact release (after registry publication):

```ini
lib_deps = superjupiter/SuperJupiterThresholdEvents@0.1.1
```

Arduino: use the versioned [GitHub release ZIP](https://github.com/Super-Jupiter/superjupiter-threshold-events/releases/tag/v0.1.1).
Library Manager registration and index propagation are separate steps; check the official index before relying on IDE availability.

## Example

```cpp
#include <SuperJupiterThresholdEvents.h>
using namespace superjupiter::threshold_events;
int main() {
ThresholdEvents alarm(Config(28,30,1000,2000,5000));
alarm.push(31,0);
Result result=alarm.push(31,1000);
(void)result;
return 0;
}

```

For Arduino, open `examples/Minimal`. It uses synthetic data only.
Results are deliberately local so applications can inspect outcomes before adding I/O.

## CMake

```cmake
add_subdirectory(path/to/superjupiter-threshold-events)
target_link_libraries(your_app PRIVATE SuperJupiter::ThresholdEvents)
```

For an installed package use `find_package(SuperJupiterThresholdEvents 0.1 CONFIG REQUIRED)`.
Top-level CMake builds tests/examples; a subproject does not enable developer tools by default.

## Documentation and verification

- [API and boundaries](https://github.com/Super-Jupiter/superjupiter-threshold-events/blob/v0.1.1/docs/API.md)
- [Tests](https://github.com/Super-Jupiter/superjupiter-threshold-events/blob/v0.1.1/tests/test.cpp)
- [Validation status](https://github.com/Super-Jupiter/superjupiter-threshold-events/blob/v0.1.1/docs/VALIDATION.md)
- [Shared conventions](https://github.com/Super-Jupiter/superjupiter-threshold-events/blob/v0.1.1/docs/CONVENTIONS.md)
- [Migration policy](https://github.com/Super-Jupiter/superjupiter-threshold-events/blob/v0.1.1/docs/MIGRATION.md)

```sh
cmake -S . -B build/cmake
cmake --build build/cmake
ctest --test-dir build/cmake --output-on-failure
python3 tools/check_release.py --tag v0.1.1
```

Keep raw observations and their provenance in your application. Derived results are not physical truth.
Do not use fast-math or finite-math-only compiler assumptions. AVR double may have float precision.
Software tests, board compilation and physical validation are reported separately.

## Fits the SuperJupiter measurement portfolio

Complements [SensorQuality](https://github.com/Super-Jupiter/superjupiter-sensor-quality) and
[SampleWindow](https://github.com/Super-Jupiter/superjupiter-sample-window) without a required dependency.
Each component can be used independently. Domain models, customer data and proprietary applications are excluded.

## Support and licence

MIT. Report reproducible bugs through repository issues; private security reports: info@superjupiter.net.
Include version, configuration, inputs, expected and observed behaviour. No credentials or private customer data.


## Compatibility review 0.1.1

This patch includes independent regression tests and a reviewed [interoperability contract](https://github.com/Super-Jupiter/superjupiter-threshold-events/blob/v0.1.1/docs/INTEROPERABILITY.md). Hardware validation pending.

- Removed unused duplicated math helpers and unnecessary includes.
- Invalid numeric observations clear continuity while preserving monotonic observation time; out-of-order invalid data cannot mutate state.
