# Motion Planner Notes (Ctrl-Step-Driver-STM32F1-fw)

## Purpose
Generate "soft goals" (smoothed setpoints) for current/velocity/position/trajectory at CONTROL_FREQUENCY.

## Modules
### CurrentTracker
- ramps current toward goal using ratedCurrentAcc
- prevents sign-cross overshoot
- outputs goCurrent

### VelocityTracker
- ramps velocity toward goal using ratedVelocityAcc
- outputs goVelocity

### PositionTracker (trapezoidal profile)
- uses ratedVelocityAcc and ratedVelocity
- anticipates braking using need_down_location ≈ v^2/(2a)
- locks velocity to 0 near target using speedLockingBrake
- outputs go_location and go_velocity

### PositionInterpolator
- estimates velocity from successive position goals (fixed-point filter)
- outputs goPosition and goVelocity

### TrajectoryTracker
- computes constant acceleration:
  a = (v_g^2 - v^2)/(2*(x_g-x))
- timeout behavior: if no updates for updateTimeout, slows down to stop
- outputs goPosition and goVelocity

## Open points
- exact CONTROL_FREQUENCY / CONTROL_PERIOD
- exact units: position ticks vs steps vs encoder counts
