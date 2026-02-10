# Hardware Detailed Analysis — MotorDriver-42

## 1. Purpose of the Board

The MotorDriver-42 board is a distributed axis controller used in the Dummy-Robot architecture.

It is designed to:
- Drive one stepper motor (high torque class, e.g., NEMA42)
- Read absolute position using a magnetic encoder (MT6816)
- Execute local motion control (current / velocity / position shaping)
- Communicate with the main controller via CAN bus
- Store configuration parameters in non-volatile memory

Each robotic joint has its own MotorDriver-42 board.

This architecture distributes control and reduces central computation load.

## 2. System Role in Global Architecture

### Global chain:

PC (DummyStudio / CLI)
→ USB
→ Controller board (STM32F4)
→ CAN bus
→ MotorDriver-42 (STM32F1)
→ TB67H450 driver
→ Stepper motor
→ Harmonic / cycloidal reducer
→ Mechanical joint

The MotorDriver-42 is responsible for real-time low-level control.

## 3. Functional Block Breakdown

### 3.1 Microcontroller — STM32F103CBT6

Role:
- Executes motion control firmware
- Handles CAN communication
- Reads encoder via SPI
- Generates PWM / control signals to motor driver
- Manages safety and state machine

Key peripherals likely used:
- CAN peripheral
- SPI (encoder MT6816)
- Timers (PWM generation)
- ADC (current measurement if implemented)
- Flash memory (config storage)

Why STM32F103:
- Reliable and widely supported
- Integrated CAN controller
- Sufficient performance for 1 kHz+ control loop

### 3.2 Motor Driver — TB67H450FNG

Role:
- Power stage between MCU and stepper motor
- Controls phase current
- Handles high current switching

Features:
- H-bridge architecture
- External current control capability
- Designed for industrial stepper applications

Critical engineering points:
- Thermal dissipation
- Copper thickness on PCB
- Proper decoupling capacitors
- Current limit configuration

### 3.3 Encoder — MT6816 (Magnetic Absolute Encoder)

Role:
- Provides absolute rotor position
- Enables closed-loop stepper control

Interface:
- SPI communication

Advantages:
- Absolute position (no homing required after power cycle)
- High resolution
- Compact

Engineering impact:
- Enables DCE (Dynamic Closed-loop Execution)
- Improves precision and eliminates lost steps

### 3.4 CAN Communication Interface

Transceiver example: SN65HVD230 (3.3V CAN transceiver)

Role:
- Converts MCU CAN logic level to differential CAN bus
- Ensures robust multi-axis communication

Topology:
- Likely linear CAN bus with 120Ω termination at ends

Engineering considerations:
- Termination resistor placement
- Noise immunity
- Cable length constraints
- Bus speed configuration

### 3.5 Power Architecture

Likely stages observed:

- Input supply (external motor supply, e.g., 24V–48V)
- Buck converter (e.g., ME3116) → intermediate voltage
- LDO (e.g., LP2992-3V3) → 3.3V logic rail

Power domains:
- Motor power domain (high current)
- Logic domain (3.3V MCU + encoder + CAN)

Critical considerations:
- Ground separation strategy
- High current trace routing
- EMI mitigation
- Bulk + decoupling capacitor placement

## 4. Local Control Capabilities

The board supports:

- Enable / disable control
- Current control mode
- Velocity control mode
- Position control mode
- Position + velocity limited move
- Calibration routine
- EEPROM configuration storage
- Stall protection
- Home position storage

All commands are received via CAN.

## 5. Engineering Strengths of This Design

- Fully distributed architecture
- Local closed-loop execution
- Absolute encoder integration
- Industrial communication protocol (CAN)
- Configurable control gains (DCE Kp, Ki, Kv, Kd)
- Safety timeout behavior (from firmware)

This is significantly more advanced than typical hobby robotics designs.

## 6. Open Technical Questions

To be validated from schematic or firmware:

- Exact motor supply voltage range?
- Maximum continuous current?
- Current sensing method (shunt value?)
- CONTROL_FREQUENCY used in firmware?
- Exact encoder resolution?
- Thermal design margin?
- PCB copper thickness?

These must be confirmed before reproducing the board.

## 7. Risk Assessment (Reproduction)

Primary risks when reproducing:

1) Thermal dissipation
2) Incorrect current limit configuration
3) CAN bus termination mistakes
4) Encoder noise / SPI instability
5) Power ground layout errors

Mitigation strategy:
- Build and test a single axis first
- Validate power rails before motor connection
- Use lab power supply with current limit
