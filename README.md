# Thermal Comfort System — Arduino PMV Controller

Embedded system that quantitatively estimates thermal comfort using the **ISO 7730 PMV algorithm** and autonomously acts to maintain the environment within acceptable comfort ranges. Implements RFID user authentication, multi-sensor data acquisition, relay-based climate control and a 7-state finite state machine with non-blocking async tasks.

---

## State Machine

```
┌──────────┐   PIN OK   ┌──────────┐   card read   ┌──────────┐
│  INICIO  │──────────▶│  CONFIG  │──────────────▶│ MONITOR  │
└──────────┘            └──────────┘               └────┬─────┘
      ▲                                                  │
      │ PIN wrong ──▶ BLOQUEADO ──▶ (timeout)           │ PMV calc
      │                                           ┌──────┴──────┐
      │                                     PMV   │             │  PMV
      │                                    HIGH   ▼             ▼  LOW
      │                                  PMV_ALTO           PMV_BAJO
      │                                     │  3 retries failed  │
      │                                     ▼                    │
      │                                  ALARMA ◀───────────────-┘
      └──────────────────────── IR / keypad ──────────────────────
```

---

## Hardware

### Sensors

| Component | Pin | Variable measured |
|---|---|---|
| DHT11 | Digital | Air temperature (°C) and relative humidity (%) |
| NTC Thermistor | A0 | Mean radiant temperature via Steinhart-Hart equation |
| IR Sensor | Digital | Human presence detection (500 ms debounce) |
| RFID MFRC522 | SPI | User card UID and stored comfort profiles |
| 4×4 Keypad | Digital | PIN entry and system commands |

### Actuators

| Component | Function |
|---|---|
| Servo Motor | Door control — 0° closed / 90° open |
| Relay | Cooling system activation when PMV is high |
| RGB LED | Green (PMV low) · Blue (PMV high) · Red (locked / alarm) |
| Buzzer | Audio alarm on threshold exceeded |
| LCD 16×2 | Real-time display of temperature and PMV values |

---

## PMV Algorithm — ISO 7730

The system computes the Predicted Mean Vote index using the following fixed parameters:

| Parameter | Value |
|---|---|
| Metabolic rate (met) | 1.0 |
| Clothing insulation (clo) | 0.61 |
| Air velocity (va) | 0.1 m/s |
| PMV comfort threshold | ± 0.2 |

PMV ranges from **−3 (too cold)** to **+3 (too hot)**. The system targets the neutral zone (−0.2 to +0.2).

---

## Libraries

| Library | Purpose |
|---|---|
| `StateMachineLib` | Finite state machine management |
| `AsyncTaskLib` | Non-blocking periodic tasks |
| `DHT` | DHT11 sensor driver |
| `MFRC522` | RFID reader over SPI |
| `LiquidCrystal` | LCD 16×2 control |
| `Servo` | Servo motor control |
| `Keypad` | 4×4 matrix keypad scanning |
| `EEPROM` | Persistent storage of user profiles |

---

## Data Persistence

Up to **16 RFID user profiles** are stored in EEPROM. Each profile contains:

1. 4-byte card UID
2. Username (16 characters)
3. Preferred temperature range

---

## How to Upload

1. Open `Comfort-termico-CODE/Comfort_Termico.ino` in Arduino IDE
2. Install required libraries via **Sketch → Include Library → Manage Libraries**
3. Select the correct board and port under **Tools**
4. Click **Upload**

---

## Documentation

Full API documentation generated with Doxygen is available in the `Doxygen/` folder.

---

## Course

Project developed for the **Embedded Systems** course · Universidad del Cauca
