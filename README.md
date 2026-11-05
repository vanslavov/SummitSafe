# SummitSafe
Capstone Household

File structure: 
SummitSafe/
├── README.md
├── docs/                     # Flowcharts, schematics, pinout tables
├── include/                  # Config, pin assignments, global types
│   ├── config.h              # Pin definitions, thresholds, timing constants
│   └── state_types.h         # SystemState enum (POLLING, PENDING, SENDING)
└── src/
    ├── main.cpp (or .ino)    # setup(), loop(), and top-level Finite State Machine (FSM) - keeping this minimal and clean for ease
    │
    ├── drivers/              # HAL & lower-level abstraction 
    │   ├── sensor_pulseox.h / .cpp
    │   ├── sensor_accel.h / .cpp
    │   ├── sensor_temp.h / .cpp    
    │   ├── gps_module.h / .cpp     
    │   ├── rf_transmitter.h / .cpp 
    │   └── user_feedback.h / .cpp  # Buzzer, LEDs, Two-step button
    │
    ├── logic/                
    │   ├── health_monitor.h / .cpp # Is this normal behavior? evaluation
    │   └── emergency_fsm.h / .cpp  # Manages [Polling -> Pending -> Sending] state transitions
    │
    └── utils/                # Power management & other helpers if needed
        └── power_manager.h / .cpp  # ESP32 light/deep sleep routines




Overview of sensors to code:
[ Hardware Sensors ] (TMP119, Accelerometer, PulseOx)
        │
        ▼
[ drivers/ ]  --> Reads raw registers and converts them to human numbers (i.e. 25098 = 37.2 °C)
        │
        ▼
[ logic/health_monitor ] --> "are these numbers ok or not?" 
        │
        ▼
[ logic/emergency_fsm ]  --> Decides state: POLLING -> PENDING -> SENDING
        │
        ├─► Controls [ drivers/user_feedback ] (Triggers Buzzer & LEDs in PENDING)
        ├─► Controls [ drivers/gps_module ]   (Powers on GPS in PENDING)
        └─► Controls [ drivers/rf_transmitter ] (Fires 5W RF Amp in SENDING)
