# Smart-water-tank
A pure digital-logic design for monitoring four one-hot water-level sensors, controlling a pump, displaying the current tank level, and activating an overflow alarm after three consecutive clock cycles at full level.
# IFT 211 — Smart Water Tank Monitoring & Control System

A pure digital-logic design for monitoring four one-hot water-level sensors, controlling a pump, displaying the current tank level, and activating an overflow alarm after three consecutive clock cycles at full level.

## Project author

- **Name:** Amina Kyari
- **Email:** [a.kyaro0119@miva.edu.ng](mailto:a.kyaro0119@miva.edu.ng)
- **Department:** Software Engineering
- **Matriculation number:** 2025/B/SENG/0403

## System overview

The design is implemented with OR, AND, XOR, and NOT gates; D flip-flops; constants; and explicit seven-segment logic. It contains a main interconnected circuit and five component circuits in the Logisim Evolution project.

![Smart water tank system block diagram](block_diagram.svg)

### Sensor encoding

The sensor inputs are assumed to be **one-hot**: exactly one of `S0`–`S3` is high at a time.

| Sensor | Tank level | Encoded `L1L0` |
|---|---:|:---:|
| `S0` | Empty | `00` |
| `S1` | 25% | `01` |
| `S2` | 50% | `10` |
| `S3` | Full | `11` |

The encoder equations are:

```text
L1 = S2 OR S3
L0 = S1 OR S3
```

The encoded level is stored in two D flip-flops on the active clock edge. `RESET` is active high and clears the stored level and overflow counter.

### Pump and overflow logic

```text
FULL = L1 AND L0
PUMP = NOT FULL
E    = FULL AND NOT RESET
D0   = E AND NOT Q0
D1   = E AND (Q1 XOR Q0)
ALARM = Q1 AND Q0
```

Both counter flip-flops share the same clock, so the counter is synchronous. While the tank remains full, its states are `00 → 01 → 10 → 11`; the alarm turns on at `11`, after the third full clock edge. A clock edge with `FULL=0` clears the count. The specified modulo-4 counter wraps to `00` on a fourth full edge, so the decoded alarm also turns off after that edge.

### Seven-segment display

The display shows decimal digits 0–3 for the registered level (`L1L0`). Outputs are active high for a common-cathode display. With `A=L1` and `B=L0`, the gate-level equations are:

```text
a = A OR NOT B     b = 1
c = NOT A OR B     d = A OR NOT B
e = NOT B          f = NOT A AND NOT B
g = A
```

## Circuit contents

The file [`smart_water_tank.circ`](smart_water_tank.circ) contains these circuits:

- `main` — interconnected complete system
- `sensor_encoder` — one-hot sensor encoder
- `level_register` — two-bit clocked level register with reset
- `pump_control` — full comparator and pump output
- `overflow_counter` — synchronous two-bit overflow counter and alarm
- `seven_segment_decoder` — gate-level display logic

## Files in this project

| File | Description |
|---|---|
| [`smart_water_tank.circ`](smart_water_tank.circ) | Editable Logisim Evolution project |
| [`IFT211_Smart_Water_Project_Report.pdf`](IFT211_Smart_Water_Project_Report.pdf) | Formatted project report with equations, tables, and K-maps |
| [`Project_Report.md`](Project_Report.md) | Editable report source |
| [`block_diagram.svg`](block_diagram.svg) | System block diagram |
| [`Simulation_Test_Results.csv`](Simulation_Test_Results.csv) | Required test cases and recorded outcomes |

## Verification

The circuit XML was parsed and the generated gate-level netlist was run through an independent custom logic-level simulation. The recorded cases cover all four sensor levels, pump control, one through three consecutive full clock edges, clearing after the level drops below full, and reset.

This was **not** a native Logisim Evolution simulation. Logisim Evolution could not be opened in the available desktop session, so loading and visual inspection in the application, along with native circuit screenshots, remain unverified and are not included. The CSV labels the custom simulation method for each case.

## Opening the project

1. Download or clone this repository.
2. Open `smart_water_tank.circ` in Logisim Evolution.
3. Select the `main` circuit or one of the named component circuits from the circuit list.
4. Use the input pins to set one sensor high at a time. Apply `CLOCK` edges to capture the level and advance the overflow counter. Assert `RESET` high to clear the sequential state.

## License

Prepared as an IFT 211 academic project submission. No separate software license is specified.

