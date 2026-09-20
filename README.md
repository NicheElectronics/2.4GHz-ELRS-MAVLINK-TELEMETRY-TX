# AIRWINGS ESP32-C3 + SX1281 2.4 GHz MAVLink Telemetry TX

A custom **2.4 GHz ExpressLRS telemetry transmitter** based on the ESP32-C3 and Semtech SX1281 RF transceiver.

This project reuses the hardware platform developed for the [AIRWINGS 2.4 GHz ELRS ESP32-C3 + SX1281 Receiver](https://github.com/NicheElectronics/2.4GHz-ELRS-ESP32C3-SX1281-RX) and reconfigures the same ESP32-C3 + SX1281 hardware to operate in the **transmitter role** using a custom ExpressLRS TX target.

The project demonstrates reuse of a single RF hardware platform for both:

- ExpressLRS receiver operation
- MAVLink telemetry transmitter operation

---

## Project Overview

The original AIRWINGS hardware was developed as a compact ExpressLRS receiver using:

- ESP32-C3 microcontroller
- Semtech SX1281 2.4 GHz RF transceiver
- 2.4 GHz antenna
- UART interface
- RGB status LED
- 3.3 V power regulation

The **SX1281 is a bidirectional RF transceiver**, so the RF hardware is capable of both transmitting and receiving.

Instead of designing a completely separate telemetry transmitter PCB, the existing ESP32-C3 + SX1281 hardware platform was reconfigured using a custom ExpressLRS **TX firmware target**.

The resulting device operates as:

```text
AIRWINGS ESP32C3 2.4GHz Telemetry TX
```

with the ExpressLRS Lua name:

```text
AIRWINGS TLM TX
```

---

## System Architecture

```text
Ground Station / Telemetry Interface
                │
                │ MAVLink
                ▼
┌─────────────────────────────────────┐
│       AIRWINGS Telemetry TX         │
│                                     │
│   ESP32-C3  +  SX1281 Transceiver   │
│                                     │
│   ExpressLRS TX Firmware            │
└──────────────────┬──────────────────┘
                   │
                   │ 2.4 GHz ExpressLRS
                   │
                   ▼
┌─────────────────────────────────────┐
│          AIRWINGS ELRS RX           │
│                                     │
│   ESP32-C3  +  SX1281 Transceiver   │
└──────────────────┬──────────────────┘
                   │
                   │ UART / MAVLink
                   ▼
            Flight Controller
```

The same RF hardware platform therefore supports two operating roles:

```text
ESP32-C3 + SX1281 Hardware
           │
           ├── RX Firmware → ELRS Receiver
           │
           └── TX Firmware → MAVLink Telemetry TX
```

---

## Hardware Reuse

The telemetry transmitter uses the same custom ESP32-C3 + SX1281 PCB architecture developed for the AIRWINGS ExpressLRS receiver.

Because the SX1281 is a transceiver, the hardware is not inherently restricted to receiver operation.

The operating role is primarily determined by:

- ExpressLRS firmware
- RX/TX target selection
- hardware-layout definition
- ESP32-C3 GPIO configuration
- SX1281 RF control configuration
- UART configuration

For the telemetry project, the hardware was configured using the ExpressLRS ESP32-C3 2.4 GHz TX firmware family:

```text
UNIFIED_ESP32C3_2400_TX
```

This allowed the existing receiver hardware to be repurposed as the transmitter side of the telemetry link.

---

## Firmware

The telemetry transmitter firmware was built using **ExpressLRS 4.1.0**.

| Parameter | Configuration |
|---|---|
| Firmware | ExpressLRS |
| Version | 4.1.0 |
| MCU | ESP32-C3 |
| RF Transceiver | SX1281 / SX128X |
| RF Band | 2.4 GHz ISM |
| Device Type | TX |
| Firmware Family | `UNIFIED_ESP32C3_2400_TX` |
| Build Environment | `Unified_ESP32C3_2400_TX_via_UART` |
| Product Name | `AIRWINGS ESP32C3 2.4GHz Telemetry TX` |
| Lua Name | `AIRWINGS TLM TX` |

### Firmware Files

```text
firmware/
├── AIRWINGS-ELRS-Telemetry-TX-4.1.0.bin
├── AIRWINGS-C3-2400-TX.json
└── AIRWINGS-Targets-Entry.json
```

### Compiled Firmware

`AIRWINGS-ELRS-Telemetry-TX-4.1.0.bin`

Compiled ExpressLRS 4.1.0 firmware flashed onto the telemetry transmitter hardware.

### Custom Hardware Layout

`AIRWINGS-C3-2400-TX.json`

Custom ExpressLRS hardware-layout definition used to configure the ESP32-C3 and SX1281 hardware for transmitter operation.

### Custom Target Entry

`AIRWINGS-Targets-Entry.json`

Custom ExpressLRS target entry linking the AIRWINGS hardware definition to the ESP32-C3 2.4 GHz TX firmware family.

---

## ExpressLRS WebUI Validation

The custom AIRWINGS telemetry TX target was successfully detected through the ExpressLRS WebUI.

The WebUI reports:

```text
Product:     AIRWINGS ESP32C3 2.4GHz Telemetry TX
Lua Name:    AIRWINGS TLM TX
Version:     4.1.0
Device Type: TX
Firmware:    UNIFIED_ESP32C3_2400_TX
Radio:       SX128X
Domain:      ISM2G4
```

This confirms that the ESP32-C3 hardware is operating with the custom ExpressLRS TX target and that the SX1281 radio is correctly detected.

### TX Information

![AIRWINGS Telemetry TX WebUI](validation/webui/airwings-telemetry-tx-webui-information.png)

### Binding Configuration

![AIRWINGS Telemetry TX Binding](validation/webui/airwings-telemetry-tx-webui-binding.png)

> Binding UID information can be obscured in public screenshots.

---

## Hardware Validation

The custom TX firmware was flashed and tested on the fabricated ESP32-C3 + SX1281 hardware.

### Telemetry TX Hardware

![AIRWINGS Telemetry TX Hardware](validation/hardware/Telemetry-TX-Hardware.jpg)

### Wired Test Setup

![AIRWINGS Telemetry TX Wired Setup](validation/hardware/Telemetry-TX-Wired-Setup.jpg)

The wired setup was used during firmware flashing, configuration, and telemetry testing.

---

## RGB LED Status

The onboard RGB LED provides a visual indication of the current ExpressLRS transmitter state.

### General TX Status

| RGB LED Indication | TX Status |
|---|---|
| Rainbow fade | Device startup |
| Green heartbeat | Wi-Fi / Web update mode |
| Blue heartbeat | Bluetooth joystick mode |
| Rapid red flashing | RF radio chip not detected |
| Orange flash approximately once per second | No handset / input connection |
| Solid packet-rate color | Receiver connected |
| Fading packet-rate color | Receiver disconnected |

### 2.4 GHz Packet Rate Colors

| LED Color | Packet Rate |
|---|---:|
| Red | F1000 |
| Yellow | F500 |
| Yellow-Green | D500 |
| Green | D250 |
| Cyan | 333 Full |
| Light Blue | 500 Hz |
| Blue | 250 Hz |
| Blue-Purple | 150 Hz |
| Purple | 100 Full |
| Magenta | 50 Hz |

The RGB LED provides a quick visual indication of transmitter operating state, receiver connectivity, and configured packet rate.

---

## MAVLink Telemetry

ExpressLRS can be used as a bidirectional telemetry link with MAVLink-compatible systems.

For this project, the AIRWINGS telemetry transmitter provides the TX-side radio interface while the AIRWINGS receiver provides the vehicle-side radio interface.

The telemetry path is:

```text
Ground Station
     ↕
   MAVLink
     ↕
AIRWINGS Telemetry TX
     ↕
2.4 GHz ExpressLRS
     ↕
AIRWINGS ELRS Receiver
     ↕
   MAVLink
     ↕
Flight Controller
```

This allows the ExpressLRS RF link to operate as a compact wireless telemetry system without requiring a separate conventional telemetry radio architecture.

---

## Functional Validation

The telemetry transmitter was tested for:

- Successful ESP32-C3 firmware boot
- SX1281/SX128X radio detection
- ExpressLRS WebUI access
- Correct identification as `Device Type: TX`
- Custom AIRWINGS telemetry target detection
- Binding configuration
- 2.4 GHz RF operation
- Telemetry link functionality

---

## Telemetry Test

The telemetry test demonstrates the AIRWINGS transmitter operating as part of the wireless telemetry system.

### Test Video

[View ELRS MAVLink Telemetry Test](validation/telemetry-test/ELRS-MAVLink-Telemetry-Test.mp4)

---

## Repository Structure

```text
AIRWINGS-ELRS-MAVLink-Telemetry-TX/
│
├── README.md
├── .gitignore
│
├── firmware/
│   ├── AIRWINGS-ELRS-Telemetry-TX-4.1.0.bin
│   ├── AIRWINGS-C3-2400-TX.json
│   └── AIRWINGS-Targets-Entry.json
│
└── validation/
    ├── hardware/
    │   ├── Telemetry-TX-Hardware.jpg
    │   └── Telemetry-TX-Wired-Setup.jpg
    │
    ├── webui/
    │   ├── airwings-telemetry-tx-webui-information.png
    │   └── airwings-telemetry-tx-webui-binding.png
    │
    └── telemetry-test/
        └── ELRS-MAVLink-Telemetry-Test.mp4
```

The complete PCB design, schematic, PCB layers, manufacturing files, and original receiver implementation are maintained in the receiver repository.

---

## Related Project

### AIRWINGS 2.4 GHz ELRS ESP32-C3 + SX1281 Receiver

The telemetry transmitter is based on the same custom RF hardware platform originally developed for the AIRWINGS ExpressLRS receiver.

**Repository:**

[2.4GHz ELRS ESP32-C3 + SX1281 Receiver](https://github.com/NicheElectronics/2.4GHz-ELRS-ESP32C3-SX1281-RX)

This telemetry repository focuses specifically on:

- TX firmware adaptation
- Custom ExpressLRS TX target
- Firmware configuration
- WebUI validation
- Telemetry-system validation

rather than duplicating the complete receiver hardware repository.

---

## Project Status

- ✅ Custom ESP32-C3 + SX1281 hardware fabricated
- ✅ Original receiver hardware reused for TX operation
- ✅ Custom ExpressLRS TX hardware layout created
- ✅ Custom ExpressLRS TX target created
- ✅ ExpressLRS 4.1.0 TX firmware compiled
- ✅ TX firmware flashed successfully
- ✅ ExpressLRS WebUI verified
- ✅ Device identified as TX
- ✅ SX1281 radio detected
- ✅ Binding configuration verified
- ✅ 2.4 GHz RF operation tested
- ✅ Telemetry functionality tested

---

## Key Engineering Work

This project demonstrates:

- Reuse of an existing embedded RF hardware platform for a different system role
- ESP32-C3 firmware configuration
- SX1281 2.4 GHz RF integration
- Custom ExpressLRS target development
- ExpressLRS firmware compilation and flashing
- UART-based telemetry integration
- MAVLink telemetry system integration
- RF link configuration
- Hardware bring-up and functional validation
- Embedded-system debugging and testing

---

## Technologies

- ESP32-C3
- Semtech SX1281
- ExpressLRS 4.1.0
- 2.4 GHz RF
- MAVLink
- UART
- Embedded Firmware
- RF Hardware
- Telemetry Systems
- UAV Electronics

---

## References

- [ExpressLRS](https://www.expresslrs.org/)
- [ExpressLRS MAVLink Documentation](https://www.expresslrs.org/software/mavlink/)
- [ExpressLRS LED Status Documentation](https://www.expresslrs.org/quick-start/led-status/)
- [Semtech SX1281](https://www.semtech.com/products/wireless-rf/24-ghz-transceivers/sx1281)

---

## Acknowledgements

Firmware functionality is based on the open-source **ExpressLRS** project.

The SX1281 is a Semtech 2.4 GHz RF transceiver.

This repository documents the custom AIRWINGS firmware adaptation, TX target configuration, firmware build, hardware reuse, and telemetry-system validation performed using the custom ESP32-C3 + SX1281 hardware platform.
