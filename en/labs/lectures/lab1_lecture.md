# Lab 1 Lecture: Physical & Link Layers — Why Radio Choice Shapes Everything Above It

**Duration**: 40 min (delivered before the hands-on lab)
**Audience**: Students about to run Lab 1 (RF characterization on ESP32-C6)
**Pairs with**: [lab1.md](lab1.md)

---

## Learning goals

By the end of the lecture, students should be able to:

1. Place the PED/SCD boundary correctly for a radio-based IoT device.
2. Explain the Component Capabilities model (ISO/IEC 30141 Fig 8 / Tables 9-10) well enough to fill the DDR table in Lab 1.
3. Describe what the 802.15.4 PHY and MAC do, and why 127-byte frames drive everything above them.
4. Contrast Thread (this course) with LoRaWAN on range, power, topology, and IP-native operation — and justify when each wins.
5. Predict what they will see in the lab (RSSI drop with distance, noise floor, channel selection) before touching hardware.

---

## Structure at a glance

| Time | Segment | One-line purpose |
|---|---|---|
| 0–8 min | ISO/IEC 30141 focus | Where does "the air" end and "the device" begin? |
| 8–20 min | Thread stack layer of the week: 802.15.4 PHY + MAC | What the radio actually does before any software runs |
| 20–32 min | Alternative stack: LoRaWAN | Same architectural role, different physics and tradeoffs |
| 32–40 min | Lab bridge | Preview what they'll measure and why |

---

## Segment 1 — ISO/IEC 30141 focus (0–8 min)

### PED vs SCD, drawn on the board

Draw two boxes. Left box: **PED (Physical Entity Domain)** — the physical world that IoT observes or affects. Right box: **SCD (Sensing & Controlling Domain)** — the devices that bridge the physical world to the digital one.

Ask: *"Where does the ESP32-C6 live? Where does the RF link live?"*

Expected answer after discussion:
- ESP32-C6 chip, antenna, radio → **SCD**
- Radio waves, WiFi interference, vegetation blocking signal → **PED** (electromagnetic field is a physical phenomenon)
- The antenna is the boundary: it converts SCD bits into PED waves and back.

This is exactly what students will fill into the DDR Section 4 after the lab.

### Component Capabilities model (Fig 8 / Tables 9-10)

Introduce the five capability categories — write them on the board:

1. **Transducer** — sensing or actuating on the physical world
2. **Data** — processing, storing, transferring
3. **Interface** — network, application, human UI
4. **Supporting** — time sync, security, power management
5. **Latent** — hardware present but unused

Emphasize the **latent** category. The ESP32-C6 has BLE and WiFi radios they will not use in the sensor role — but those capabilities exist on the board and an architect should be aware of them. This is why Lab 1 asks them to fill the full capabilities table, not just the active parts.

> **Teaching hook**: "If next semester the client asks for a BLE commissioning flow, you won't need new hardware — you'll activate a latent capability." This framing makes the exercise feel forward-looking rather than bureaucratic.

---

## Segment 2 — Thread stack layer of the week: 802.15.4 PHY + MAC (8–20 min)

### The PHY: why 2.4 GHz + O-QPSK + DSSS

**The constraint driving everything**: sensors must run 3 months on 2× AA batteries. Average power budget ≈ 4.2 mW. WiFi at 200 mA per transmission blows the budget even at 1% duty cycle. So the PHY has to be designed for efficient transmission, not peak throughput.

Three design choices that fall out of this:

1. **O-QPSK** (Offset Quadrature Phase-Shift Keying) — constant-envelope modulation. Amplitude never changes, only phase. Lets the radio use a non-linear power amplifier (70-80% efficient) instead of a linear one (30-40%). *Double the battery life for free.* See [O-QPSK](https://www.youtube.com/watch?v=lDSzyEQKE6o)

2. **DSSS** (Direct Sequence Spread Spectrum) — each data bit is spread across a 32-chip sequence. Gives ~9 dB of processing gain: the signal can be recovered even when it's buried below the noise floor. This is why an 802.15.4 link can coexist with a much louder WiFi transmitter on the same band.

3. **2.4 GHz band, channels 11–26** — 16 channels of 2 MHz each, 5 MHz spacing. The band is license-free globally, which is why everyone uses it (WiFi, Bluetooth, microwaves). Channel selection matters: some channels overlap WiFi 1/6/11, some don't.

> Draw the 2.4 GHz band with WiFi channels 1/6/11 overlaid on 802.15.4 channels 11-26. Circle channels 15, 20, 25, 26 as the "usually quietest" ones. This is the picture students should have in their head when they run `scan energy` in Part 2 of the lab.

### The MAC: CSMA-CA, ACK/ARQ, and the 127-byte frame

**CSMA-CA** (Carrier Sense Multiple Access with Collision Avoidance). Listen before you talk:

1. Radio listens for 128 µs (CCA — Clear Channel Assessment).
2. If energy above threshold → channel busy → back off random time → retry.
3. If clear → transmit.

This is how 802.15.4 coexists with interference. If you lower the CCA threshold too far, the radio becomes "paranoid" and never transmits. If you raise it too far, it talks over other devices. [SOP-01](sops/sop01_advanced_mac.md) has the experiment for students who want to push further.

**ACK/ARQ**: every unicast frame can request an acknowledgment. No ACK within a few ms → hardware auto-retries (up to 3 times by default). The upper layers never see most losses — the MAC hides them.

**The 127-byte frame** — this is the number to memorize.

```
┌─────────────────────────────────────────────────────────┐
│ 802.15.4 frame: 127 bytes total                         │
├─────┬───────────┬──────────────────────────────┬────────┤
│ MHR │ Sec hdr   │ Payload                      │ FCS    │
│ ~9B │ 0–14B     │ ≤104B (less with security)   │ 2B     │
└─────┴───────────┴──────────────────────────────┴────────┘
```

After MAC header, security, and FCS, you have roughly **80–100 bytes** for everything above: IPv6 (40-byte header!), UDP (8), CoAP, payload. A raw IPv6 header eats half the budget. This is the problem **6LoWPAN** (Lab 2) exists to solve. Foreshadow it.

### Where this lives in ISO/IEC 30141

- **PHY** → PED boundary (electromagnetic propagation) + SCD (radio hardware)
- **MAC** → SCD (communication subsystem)
- This lab is the **Proximity Networking** communication type in the standard.

---

## Segment 3 — Alternative stack: LoRaWAN (20–32 min)

Same architectural role in ISO/IEC 30141 (PED boundary + SCD comms). Radically different physics.

### Why LoRa exists

GreenField's scenario is a 10-hectare field. Fine for Thread. But imagine a **10 km remote pasture** — Thread at 2.4 GHz with 15m hop spacing would need hundreds of routers. LoRa solves this with sub-GHz physics.

### Key tradeoffs — write this table on the board

| Axis | Thread (802.15.4) | LoRaWAN |
|---|---|---|
| **Frequency** | 2.4 GHz (global) | Sub-GHz (868 MHz EU, 915 MHz US, 433 MHz some regions) |
| **Range** | ~30–100 m indoors, ~300 m LoS | 2–15 km rural, 1–3 km urban |
| **Modulation** | O-QPSK + DSSS | Chirp Spread Spectrum (CSS) — trades data rate for sensitivity |
| **Data rate** | 250 kbps | 0.3–50 kbps (spreading-factor dependent) |
| **Topology** | IPv6 mesh (self-healing, multi-hop) | Star-of-stars (devices → gateways → network server) |
| **IP-native?** | Yes (IPv6 end-to-end) | No (LoRaWAN frames, translated at the server) |
| **Duty cycle** | Essentially unlimited | **Regulated**: 1% in EU868 → ~36 sec of airtime per hour |
| **Power** | ~20 mA TX, months on AA | ~40 mA TX but rare transmissions → years on AA |
| **Security** | DTLS, AES-128 (link + app) | AES-128 (network + app keys) |

### Why the physics flip everything above

Two consequences worth driving home:

1. **Duty-cycle regulation changes the application design**. You cannot "just send a reading every 5 seconds" on LoRaWAN in Europe — the radio is legally off the air most of the time. This forces application-layer design decisions (which readings matter, how to batch, how to handle downlink).

2. **No IP means no end-to-end CoAP**. LoRaWAN terminates at the network server; the application server talks HTTP/MQTT downstream. Thread lets a cloud service address a sensor by IPv6 — LoRaWAN does not. This is a **Functional viewpoint** difference, not just a physical one.

### When would GreenField pick LoRa?

- Remote pastures, 10+ km spans → **LoRaWAN wins on range**.
- Dense in-field sensing with occasional commands to actuators → **Thread wins on latency + IP-native control**.
- Hybrid is common: LoRa for long-haul telemetry, Thread/BLE for in-barn equipment.

> **Teaching hook**: "The ISO/IEC 30141 architecture doesn't care which you pick. The six domains and the Component Capabilities table work identically. What changes is the content of the cells." This is the whole point of a reference architecture.

---

## Segment 4 — Lab bridge (32–40 min)

### What they are about to measure

Walk through Parts 2 and 3 of [lab1.md](lab1.md) at high speed:

1. **`scan energy`** — they'll see real noise floors on each channel. Remind them: lower (more negative) dBm = quieter. Expected idle noise floor in a classroom is around -90 to -100 dBm; if they see -70 dBm on a channel, that channel is being used.

2. **Two-device ping** — one device becomes `leader`, the other `child`. Behind the scenes this is Thread's MLE (Mesh Link Establishment) — but for today all they need is that the link comes up.

3. **RSSI vs distance** — they will see ~6 dB drop per doubling of distance in free space. They will see *much more* than 6 dB per doubling through walls or vegetation. Both are correct; the delta is what "fade margin" accounts for.

### The puzzle to seed before they start

Ask the class:

> *"The ESP32-C6 datasheet says the receiver can decode signals down to -100 dBm. But you'll find in the lab that packets start dropping when RSSI falls below about -70 dBm. Where did the other 30 dB go?"*

Don't answer. They will debate this during the lab and write up the answer in Question 2 of the DDR. The answer involves:
- Noise floor (you don't need to beat sensitivity, you need to beat noise + sensitivity)
- Fade margin (multipath, obstacles, movement)
- SNR requirement (DSSS needs a few dB SNR to decode reliably)
- Minimum RSSI = Receiver Sensitivity + Fade Margin + SNR Requirement

### Practical reminders

- Set the same `channel` and `panid` on both devices. Easiest: `dataset init new` on Device A, then read A's dataset and apply the same values on Device B.
- Use `ping <addr> <size> <count> <interval>` — default count is 1, which is not enough for PER.
- Turn off radios when not testing. Shared spectrum is a shared resource. This is a **trustworthiness** concern in ISO/IEC 30141 (Segment 1 again).

### What Lab 2 will answer

> *"How do we fit IPv6 into 127-byte 802.15.4 frames?"*

Ask them to read RFC 6282 (6LoWPAN header compression) before next class. The motivation — the 127-byte ceiling — was established today.

---

## Instructor checklist

- [ ] Board ready with PED/SCD box diagram (Segment 1).
- [ ] Slide or board sketch of 2.4 GHz band with WiFi vs 802.15.4 channels (Segment 2).
- [ ] 127-byte frame drawn on the board — this is the hook for Lab 2.
- [ ] Thread-vs-LoRa comparison table visible during Segment 3.
- [ ] One live `scan energy` demo before students start Part 2, so they've seen the real output once.
- [ ] The -100 dBm vs -70 dBm puzzle posed at the end, left unanswered.

---

## References for students

- [lab1.md](lab1.md) — the hands-on guide they'll follow next.
- [2_iso_architecture.md](../2_iso_architecture.md) — full domain and viewpoint definitions.
- [5_theory_foundations.md](../5_theory_foundations.md) §1 — deeper first-principles on O-QPSK and DSSS.
- [5_technology_landscape.md](../5_technology_landscape.md) — stack-by-stack comparison across the whole course.
- IEEE 802.15.4-2020, Clauses 8 (PHY) and 6 (MAC).
- ISO/IEC 30141:2024, Section 9 + Tables 9-10 (Component Capabilities).
