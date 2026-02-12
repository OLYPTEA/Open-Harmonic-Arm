# Hardware Notes — MotorDriver-42 (partial)

## Key ICs observed
- MCU: STM32F103CBT6
- CAN transceiver: SN65HVD230 (or similar family)
- Motor driver: TB67H450FNG
- Absolute magnetic encoder: MT6816
- Power: ME3116 (buck), LP2992-3V3 (LDO 3.3V)

## Purpose of board
Axis node: local control + CAN + encoder + motor drive.

## Open points
- full BOM not yet extracted
- connector pinout to confirm (power + CAN + debug)
- thermal design / current limits to confirm
