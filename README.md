# UnityPlayModeSimulationToolkit

A focused Unity Editor toolkit that extends the Play Mode lifecycle to support deterministic simulation configuration and execution.
This project demonstrates clean separation between Editor, Simulation, and Runtime layers, mirroring patterns used in engine and platform tooling.

------------------------------------------------------------

PURPOSE

Unity Play Mode is optimized for rapid iteration, but testing deterministic simulation behavior or platform-style runtime configuration often requires custom tooling.

This toolkit demonstrates how to:

- Hook into the Play Mode lifecycle from the Editor
- Inject simulation configuration before runtime startup
- Run a fixed-step deterministic simulation loop
- Maintain clear architectural boundaries between editor and runtime code
- Build maintainable internal tooling rather than gameplay systems

The goal is not to replace Unity systems, but to augment Play Mode with structured simulation control in a safe and extensible way.

------------------------------------------------------------

HIGH LEVEL ARCHITECTURE

Editor
- SimulationSettingsWindow
- PlayModeSimulationHook

Configuration Injection

Runtime
- SimulationBootstrap
- SimulationRunner
- DeterministicClock

Simulation Core
- ISimulationStep
- Deterministic execution loop

Key design principle:
Editor code configures behavior.
Runtime code executes it.
Editor logic never directly drives the simulation state at runtime.

------------------------------------------------------------

PLAY MODE INTEGRATION

The toolkit integrates with Unity Play Mode lifecycle using EditorApplication.playModeStateChanged.
Execution is intercepted during ExitingEditMode.

Responsibilities of the Play Mode hook include:

- Ensuring a valid SimulationConfig asset exists
- Preparing simulation configuration before runtime startup
- Avoiding modification of global engine state, such as Time.timeScale

SimulationBootstrap and SimulationRunner handle all runtime behavior.

------------------------------------------------------------

DETERMINISM STRATEGY

Deterministic behavior is achieved through:

- Fixed timestep simulation
- Seeded System.Random usage
- Explicit simulation clock using DeterministicClock
- Bounded step execution per frame to prevent spiral of death scenarios

The simulation loop is intentionally minimal and designed to be extended through systems implementing ISimulationStep.

------------------------------------------------------------

EDITOR TOOLING

The toolkit includes a lightweight Editor window accessible via:

Tools > Simulation Toolkit > Settings

The window allows:

- Enabling or disabling simulation mode
- Configuring fixed timestep and random seed
- Inspecting and pinging the active configuration asset

Configuration is stored in a ScriptableObject in Resources to ensure runtime accessibility without Editor-only APIs.

------------------------------------------------------------

FOLDER STRUCTURE

Assets/SimulationToolkit/
- Config
  - SimulationConfig.cs
- Core
  - DeterministicClock.cs
  - ISimulationStep.cs
- Runtime
  - SimulationBootstrap.cs
  - SimulationRunner.cs
  - SimulationRuntimeConfig.cs
- Editor
  - PlayModeSimulationHook.cs
  - SimulationSettingsWindow.cs

This structure enforces a clear separation between editor-only tooling, runtime safe systems, and simulation core logic.

------------------------------------------------------------

DESIGN TRADEOFFS

ScriptableObject-based configuration was chosen for editor usability and clarity, at the cost of requiring a Resources asset.

The simulation runner is intentionally framework-light to avoid coupling with gameplay systems.

Global engine time settings are not modified to keep the toolkit non-invasive.

This project prioritizes clarity, maintainability, and architectural correctness over feature completeness.

------------------------------------------------------------

FUTURE EXTENSIONS NOT IMPLEMENTED

Potential extensions include:

- Live simulation state inspection in the Editor
- Snapshot and replay support
- Platform-specific simulation profiles
- Automated simulation test runs

These are intentionally omitted to keep the core focused.

------------------------------------------------------------

UNITY VERSION

Developed and tested using Unity LTS.

------------------------------------------------------------

LICENSE

MIT License
