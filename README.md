# ☕ Chai Coin Toss Machine

> One coin flip. Twenty-one steps. Zero reason.

A deliberately over-engineered way to decide who pays for chai. Instead of flipping a coin (1 step, 1 second), our machine uses two ESP32 boards, a cloud MQTT broker, two random-number APIs, a Telegram approval, and a marble run, all in **21 steps**.

Built for **Mad Hacks · Over-Engineered Solutions** by team **NullByte** (Bearys Institute of Technology).

This repository holds the **presentation website** (`index.html`): a single self-contained page with a live simulation, a 3D product viewer, the parts list, wiring, and software architecture.

---

## Contents

- [How it works](#how-it-works)
- [The website](#the-website)
- [Running it](#running-it)
- [Funny extras](#funny-extras)
- [Hardware](#hardware)
- [Software stack](#software-stack)
- [Customising](#customising)
- [Event rules we followed](#event-rules-we-followed)
- [Team](#team)

---

## How it works

| Stage | Steps | What happens |
|---|---|---|
| 1. Input | 1-2 | A human presses Button 1, and ESP32 #1 wakes up and debounces it |
| 2. Cloud and Decision | 3-8 | The request goes over MQTT to Node-RED. It fetches random numbers from two free APIs, ESP32 #1's hardware RNG adds a third, and a 3-way majority vote picks Left (Person A pays) or Right (Person B pays) |
| 3. Human Approval | 9-10 | The result is sent to a Telegram bot, and the friends tap **Approve** |
| 4. Mechanical Chain | 11-18 | ESP32 #2 gets the command. Servo A releases a marble, Servo B routes it left or right, two IR sensors confirm its path, and Servo C swings a lever that rings a bell |
| 5. Result | 19-21 | The OLED shows who pays, the complexity score (21 steps instead of 1), and someone buys the chai |

**Fail-safe:** if Wi-Fi, an API, Telegram or a sensor stalls for more than 10 seconds, ESP32 picks the result itself, so the chai decision is never stuck.

---

## The website

`index.html` contains:

- **Hero:** animated coin and the "21 steps instead of 1" counter
- **Problem vs our solution:** normal way compared with ours
- **Live simulation:** click-through flow diagram of all 21 steps, animated marble run, OLED display, fake Telegram chat, and a live log. Options: speed (0.5x/1x/2x) and Random / Force Left / Force Right
- **3D product model:** interactive Three.js viewer (drag, zoom, explode layers, fullscreen)
- **Components:** flip cards with quantities, roles and prices, plus a cost total
- **Hardware and wiring:** block diagram and pin tables for both ESP32s
- **Software architecture:** layered stack and the Node-RED flow
- **Judging criteria checklist, fail-safe section and team**
- **Presentation mode:** press **P** (or the nav button) to show only the simulation full screen; **Esc** exits

---

## Running it

No build step and no dependencies to install.

1. Download `index.html`.
2. Open it in a modern browser (Chrome, Edge, Firefox, Safari).
3. Scroll to **Live simulation** and press **START**.

> **Internet needed once:** the 3D viewer loads Three.js from a CDN. Everything else works offline.

---

## Funny extras

Extra features layered onto the simulation:

- **Excuse generator:** the loser picks a random excuse ("My UPI is down", "I only have a ₹2000 note"...) and the two friends argue in a chat panel.
- **Googly-eyed talking coin:** the hero coin has moving eyes and a moustache and shouts "Wheee!" mid-flight. In forced-result modes it whispers "I am double-headed, don't tell."
- **Soft sound effects:** a quiet trumpet for the winner and a sad trombone for the loser, made in the browser. Toggle with the **Soft sound** checkbox.
- **Certificate of Chai Debt:** a printable certificate with 21 silly line items, a total, the excuse used, the time wasted, and a stamp.
- **Silly loading messages:** "Asking mom for permission...", "Calibrating chai quantum..." and more.
- **Pointless extra steps:** a toggle that adds 3 useless steps, ending at *"Step 24 / 21 (we added 3 just because)"*.
- **Chai tapri scene:** the chai-wala shakes his head at every step and holds an "Udhar band hai" board (no credit). A cow wanders across during the toss, a stray dog sits by the stall, and at the end a biscuit dunks into the tea, the kettle whistles softly, and the chai-wala hands over the bill.

---

## Hardware

| Part | Qty | Role |
|---|---|---|
| ESP32 DevKit | 2 | #1 = controller (button, OLED, MQTT publish). #2 = mechanism (servos, IR, bell) |
| SG90 micro servo | 3 | A = marble release, B = left/right gate, C = bell lever |
| IR obstacle sensor | 2 | Confirm the marble's path and arrival at the lever |
| 0.96" I2C OLED (SSD1306) | 1 | Step counter, winner, complexity score |
| Push button | 2 | Button 1 = start, Button 2 = reset |
| Active buzzer / small bell | 1 | Announces the loser |
| LEDs + resistors | 1 set | One LED per stage group |
| Breadboard + jumper wires | 2 + set | Connections |
| 5V 2A adapter / power bank | 1 | Powers the servos separately |
| Marbles + cardboard track | - | Physical marble run with a left/right gate |

Servos run from the external 5V supply, and its ground is shared with the ESP32 (common ground) so signals stay clean.

**Pins, ESP32 #1:** Button 1 GPIO 4 · Button 2 GPIO 5 · OLED SDA 21 / SCL 22 · LEDs 25, 26, 27, 32, 33
**Pins, ESP32 #2:** Servo A 13 · Servo B 12 · Servo C 14 · IR 1 = 34 · IR 2 = 35 · Buzzer 18

---

## Software stack

- Arduino IDE (firmware for both ESP32s)
- Node-RED (flow: 2 APIs + hardware RNG, majority vote)
- MQTT via the free HiveMQ public broker
- Telegram Bot (created with BotFather)
- 2 free random-number APIs
- Wi-Fi hotspot

All free tools.

---

## Customising

The top of the main `<script>` in `index.html` has blocks marked `EDIT ME`:

| Block | What it controls |
|---|---|
| `CFG` | Team name, members, college, event, our approximate cost |
| `STAGES` | Stage names and colours |
| `STEPS` | The 21 step labels, devices and descriptions |
| `COMPS` | Parts list (name, qty, role, price range) |
| `PINS1`, `PINS2` | Pin tables |
| `CHECKS` | Judging checklist items |

The funny additions (excuses, loading messages, certificate items, extra steps) are arrays near the end of the file, inside the last `<script>` block.

---

## Event rules we followed

- No flashing lights
- No very loud sounds (all sounds are soft and can be muted)
- No jokes aimed at individuals: the two friends are always generic **Person A** and **Person B**
- Motion respects `prefers-reduced-motion`

---

## Team

**NullByte**: Suhail, Mahammadkaif Shaikh, Mahek Banu, Abida Khanum
Bearys Institute of Technology · Mad Hacks · Over-Engineered Solutions
