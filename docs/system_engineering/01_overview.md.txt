# Open Harmonic Arm — Baseline: Dummy-Robot (peng-zhihui)

## Goal
Reproduce and understand the Dummy-Robot architecture (hardware + firmware + PC software), then build:
1) a 3D-printed prototype for validation,
2) a final aluminum structure with harmonic reducers.

## Baseline project structure (Dummy-Robot)
- Hardware (Altium): Controller board + MotorDriver boards (e.g., MotorDriver-42)
- Firmware: STM32F4 (core/master) + STM32F1 (axis node)
- PC software: DummyStudio (GUI) + CLI Tool
- 3D models: STEP assemblies and submodules
- Documentation / images

## System-level architecture (L0)
PC (DummyStudio/CLI)
  ↕ USB (or bridge)
Controller (STM32F4, master)
  ↕ CAN bus
Axis nodes (STM32F1 per axis)
  ↕ motor driver (TB67H450) + encoder (MT6816)
Stepper motor + reducer (harmonic or cycloidal)
