# SummitSafe

---

## Repo File Structure

```text
SummitSafe/
├── README.md
├── docs/                     # Flowcharts, schematics, pinout tables
├── include/                  # Config, pin assignments, global types
│   ├── config.h              # Pin definitions, thresholds, timing constants
│   └── state_types.h         # SystemState enum (POLLING, PENDING, SENDING)
└── src/
    ├── main.cpp              # setup(), loop(), and top-level Finite State Machine (FSM)
    │
    ├── drivers/              # Hardware Abstraction Layer (HAL) - Low-level drivers
    │   ├── sensor_pulseox.h / .cpp
    │   ├── sensor_accel.h / .cpp
    │   ├── sensor_temp.h / .cpp    # TMP119 I2C wrapper
    │   ├── gps_module.h / .cpp     # Adafruit PA1010D wrapper
    │   ├── rf_transmitter.h / .cpp # RFM96W + MOSFET 5W PA control
    │   └── user_feedback.h / .cpp  # Buzzer, LEDs, Two-step button
    │
    ├── logic/                # Business logic & health algorithms
    │   ├── health_monitor.h / .cpp # Evaluates "Normal behavior?" condition
    │   └── emergency_fsm.h / .cpp  # Manages Polling -> Pending -> Sending state transitions
    │
    └── utils/                # Power management & helpers
        └── power_manager.h / .cpp  # ESP32 light/deep sleep routines


[ Hardware Sensors ] (TMP119, Accelerometer, PulseOx)
        │
        ▼
[ drivers/ ]                  --> Reads raw registers and converts to human values
        │
        ▼
[ logic/health_monitor ]      --> Evaluates if sensor values are normal or dangerous
        │
        ▼
[ logic/emergency_fsm ]       --> Manages state: POLLING -> PENDING -> SENDING
        │
        ├─► Controls [ drivers/user_feedback ]  (Triggers Buzzer & LEDs in PENDING)
        ├─► Controls [ drivers/gps_module ]     (Powers on GPS in PENDING)
        └─► Controls [ drivers/rf_transmitter ] (Fires 5W RF Amp in SENDING)
