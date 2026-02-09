# Firmware Architecture (baseline understanding)

## Two-firmware split
### 1) Core-STM32F4-fw (master/controller)
Role:
- PC communication (USB stack)
- robot coordination / high-level logic
- CAN message routing to axis nodes
Main folders:
- Drivers / Middlewares / USB_DEVICE (CubeMX style)
- Robot (robot-level logic, kinematics modules)
- UserApp/protocols (ASCII/CAN/CMD protocols)

### 2) Ctrl-Step-Driver-STM32F1-fw (axis node)
Role:
- local closed-loop control execution (position/velocity/current shaping)
- encoder reading (MT6816)
- motor drive (TB67H450)
- CAN command handling and feedback
Key folders:
- Port: hardware drivers (tb67h450_stm32, mt6816_stm32, led/button, encoder_calibrator)
- Ctrl: control algorithms + motion planning
- Application user: axis state machine (enable/run/error)
