![picokit-36-scheduler](https://raw.githubusercontent.com/mytechnotalent/picokit-36-scheduler/main/picokit-36-scheduler.png)

<br>

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/reverse-engineering)
## FREE Embedded Hacking Course [HERE](https://github.com/mytechnotalent/Embedded-Hacking)

<br>

# PICOKIT-36 SCHEDULER

### Timed Vent and LED Actions with an Authenticated Heartbeat
#### Lesson 36 of the Picokit Series

<br>

***
**LEGAL DISCLAIMER:**
The information, tools, and code provided in this repository and course are strictly for educational, research, and defensive purposes only.

You are explicitly prohibited from using any materials contained herein to access, test, modify, or exploit any device, network, or system that you do not own 100% or for which you do not have explicit, documented, and legally binding authorization to interact with.

By using this repository and course, you acknowledge and agree that:

1. Any illegal, unauthorized, or malicious use of this information is solely your responsibility.
2. The author(s) and contributor(s) of this repository and course shall not be held liable for any damages, legal repercussions, criminal charges, or unauthorized actions resulting from the use, misuse, or abuse of the contents herein.
3. You will comply with all applicable local, state, national, and international laws regarding cybersecurity and computer fraud.

**IF YOU DO NOT AGREE WITH THESE TERMS, DO NOT USE THIS REPOSITORY AND COURSE.**
***

<br>
<br>

## Overview

The thirty-sixth Picokit lesson. The node runs a repeating schedule of timed
actions. Each tick advances to the next event, drives the vent servo and the
status lamps for that event, and reports the event index in the authenticated
heartbeat.

<br>

## What it teaches

- Running timed actions from a schedule.
- Cycling through a fixed set of events.
- Driving a servo and lamps per event.
- Reporting the current event in the heartbeat body.

<br>

## Hardware

| Peripheral | Pico 2 pin | Role |
| --- | --- | --- |
| SG90 servo | GP14 | vent actuator |
| Red / Yellow / Green | GP16 / GP18 / GP17 | LED phase |
| Onboard LED | GP25 | heartbeat, one blink per transmit |
| RYLR998 | GP8 TX / GP9 RX | LoRa heartbeat |
| Debug Probe | SWCLK/SWDIO/GND, GP0/GP1 | SWD and the console |

<br>

## How it works

The node runs `monitor_step` in a loop. Every 5 seconds the schedule advances
to the next event: event 0 opens the vent at 90 degrees and lights green, event
1 closes the vent and lights yellow, and event 2 holds the vent closed and
lights red. Every 5 seconds the node also transmits an authenticated heartbeat
whose body is `{"n":36,"s":<seq>,"e":<event>}` sealed with the field key.

<br>

## Build and flash

```bash
cd firmware
cmake -S . -B build -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s
cmake --build build
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg \
  -c "program build/picokit_36_scheduler.elf verify reset exit"
```

<br>

## Watch the node

Open the console at 115200 and reset:

```text
BOOT
=== PICOKIT-36 SCHEDULER // TIMED VENT + LED ACTIONS + AUTHENTICATED HEARTBEAT ===
EVENT 0 ANGLE 90
EVENT 1 ANGLE 0
EVENT 2 ANGLE 0
```

<br>

## The gateway

```bash
cd gateway
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python3 listen.py --port /dev/cu.usbserial-A50285BI --hub 0001 --network 18 --db gateway.db
```

It prints `OK node=36 rssi=...` per authenticated heartbeat. The terminal
dashboard `python3 tui.py --db gateway.db` and the web dashboard
`python3 web/app.py --db gateway.db` show the same rows.

<br>

## Verify

```bash
python3 .opencode/skill/embedded-c-standard/audit_c_standard.py
python3 .opencode/skill/embedded-python-standard/audit_python_standard.py
python3 .opencode/skill/iot-readme-standard/validate_readme.py
python3 .opencode/skill/iot-banner-standard/validate_banner.py
python3 scripts/run_tests.py
python3 scripts/check_coverage.py
```

<br>

# Next
[picokit-37-reaction-timer](https://github.com/mytechnotalent/picokit-37-reaction-timer)

<br>

# License
[MIT License](https://github.com/mytechnotalent/picokit-36-scheduler/blob/main/LICENSE)
