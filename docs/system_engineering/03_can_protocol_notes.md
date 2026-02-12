# CAN Protocol Notes (from code reading)

## CAN ID format (observed in CtrlStepMotor)
StdId = (nodeID << 7) | mode
- nodeID: axis identifier
- mode: command code

## Example command modes (from ctrl_step.hpp/cpp)
0x01: Enable / Disable (payload uint32: 1 or 0)
0x02: Calibration request
0x03: Current setpoint (float)
0x04: Velocity setpoint (float)
0x05: Position setpoint (float) + ACK request in byte[4]=1
0x07: Position + velocity limit (float pos + float vel)
0x11: Set node ID (uint32) + save flag in byte[4]
0x12: Set current limit (float) + save flag
0x13: Set velocity limit (float) + save flag
0x14: Set acceleration (float)
0x15: Apply current position as home
0x16: Enable-on-boot (uint32) + save flag
0x1B: Stall protection enable (uint32) + save flag
0x23: Request angle update
0x7E: Erase configs
0x7F: Reboot

## Notes
- Payload encoding appears little-endian (raw float/int bytes copied to canBuf).
- Units/scaling for position/velocity depend on axis firmware implementation (to be confirmed).
