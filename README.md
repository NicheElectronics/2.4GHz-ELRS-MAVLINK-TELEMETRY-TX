# AIRWINGS ESP32-C3 + SX1281 2.4 GHz MAVLink Telemetry TX

A custom **2.4 GHz ExpressLRS telemetry transmitter** based on the ESP32-C3 microcontroller and Semtech SX1281 RF transceiver.

This project reuses the same ESP32-C3 + SX1281 hardware originally designed for ExpressLRS receiver operation and reconfigures it to operate in the **transmitter role** using a custom ExpressLRS TX hardware target.

The project demonstrates how the same embedded RF platform can support both receiver and telemetry-transmitter operation through firmware and hardware-target configuration.

---

## Project Overview

The hardware platform is based on:

- ESP32-C3 microcontroller
- Semtech SX1281 2.4 GHz RF transceiver
- 2.4 GHz antenna
- UART interface
- RGB status LED
- 3.3 V power regulation

The **SX1281 is a bidirectional RF transceiver**, allowing the RF hardware to both transmit and receive.

Instead of designing a separate telemetry transmitter PCB, the existing ESP32-C3 + SX1281 platform was reconfigured using an ExpressLRS **TX firmware target**.

The resulting device operates as:

```text
AIRWINGS ESP32C3 2.4GHz Telemetry TX
```

ExpressLRS Lua name:

```text
AIRWINGS TLM TX
```

---

## Hardware Reuse Concept

The same RF hardware platform can operate in two different roles:

```text
ESP32-C3 + SX1281 Hardware
           │
           ├── RX Firmware
           │      ↓
           │  ELRS Receiver
           │
           └── TX Firmware
                  ↓
             Telemetry TX
```

The SX1281 itself is not limited to receiver operation.

The operating role is primarily determined by:

- ExpressLRS firmware
- RX or TX firmware target
- Hardware-layout definition
- ESP32-C3 GPIO configuration
- SX1281 interface configuration
- UART configuration

For the telemetry implementation, the hardware was configured using the ExpressLRS ESP32-C3 2.4 GHz TX firmware family:

```text
UNIFIED_ESP32C3_2400_TX
```

---

## Telemetry Architecture

The telemetry transmitter is designed to operate as part of a bidirectional wireless telemetry link.

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

This allows the ExpressLRS RF link to be used as a compact wireless telemetry communication system.

---

## Firmware

The telemetry transmitter firmware is based on **ExpressLRS 4.1.0**.

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

---

## Custom ExpressLRS Target

A custom ExpressLRS TX target was created for the AIRWINGS hardware.

The target defines the configuration required to operate the ESP32-C3 + SX1281 platform as a 2.4 GHz ExpressLRS transmitter.

The custom target includes:

- ESP32-C3 platform selection
- SX1281 RF interface configuration
- SPI pin mapping
- RF control signals
- UART configuration
- RGB LED configuration
- ExpressLRS TX firmware selection

The target uses:

```text
Firmware Family:
UNIFIED_ESP32C3_2400_TX
```

and the PlatformIO environment:

```text
Unified_ESP32C3_2400_TX_via_UART
```

---

## Firmware Files

```text
firmware/
├── AIRWINGS-ELRS-Telemetry-TX-4.1.0.bin
├── AIRWINGS-C3-2400-TX.json
└── AIRWINGS-Targets-Entry.json
```

### AIRWINGS-ELRS-Telemetry-TX-4.1.0.bin

Compiled ExpressLRS 4.1.0 firmware for the AIRWINGS telemetry transmitter.

### AIRWINGS-C3-2400-TX.json

Custom hardware-layout definition describing the ESP32-C3 and SX1281 pin mapping required for TX operation.

### AIRWINGS-Targets-Entry.json

Custom ExpressLRS target entry connecting the AIRWINGS hardware-layout definition with the ESP32-C3 2.4 GHz TX firmware family.

---

## RX to TX Firmware Adaptation

The original hardware was configured as an ExpressLRS receiver using:

```text
UNIFIED_ESP32C3_2400_RX
```

For the telemetry transmitter implementation, the firmware role was changed to:

```text
UNIFIED_ESP32C3_2400_TX
```

The main concept is:

```text
Same Hardware
     │
     ├── Unified ESP32-C3 2400 RX
     │          ↓
     │     Receiver Mode
     │
     └── Unified ESP32-C3 2400 TX
                ↓
          Transmitter Mode
```

This allows the same PCB architecture to support multiple RF-system roles without redesigning the core ESP32-C3 + SX1281 hardware.

---

## RGB LED Status

The onboard RGB LED is used by ExpressLRS to provide visual information about transmitter state and configured packet rate.

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

The RGB LED therefore provides a compact visual interface for identifying the current transmitter state and packet-rate configuration.

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

The repository focuses primarily on the **firmware adaptation and system configuration** required to operate the existing ESP32-C3 + SX1281 hardware platform as an ExpressLRS telemetry transmitter.

---

## Key Engineering Work

- Reused an existing ESP32-C3 + SX1281 RF hardware platform
- Reconfigured receiver hardware for transmitter operation
- Developed a custom ExpressLRS TX hardware layout
- Created a custom ExpressLRS TX target
- Configured ESP32-C3 GPIO assignments
- Configured the SX1281 RF interface
- Built ExpressLRS 4.1.0 TX firmware
- Implemented UART-based telemetry interfacing
- Integrated the platform for MAVLink telemetry
- Used onboard RGB LED indication for transmitter status

---

## Technologies

- ESP32-C3
- Semtech SX1281
- ExpressLRS 4.1.0
- 2.4 GHz ISM RF
- MAVLink
- UART
- SPI
- Embedded Firmware
- RF Hardware
- Telemetry Systems
- UAV Electronics

---

## References

- [ExpressLRS Documentation](https://www.expresslrs.org/)
- [ExpressLRS MAVLink Documentation](https://www.expresslrs.org/software/mavlink/)
- [ExpressLRS LED Status Documentation](https://www.expresslrs.org/quick-start/led-status/)
- [Semtech SX1281](https://www.semtech.com/products/wireless-rf/24-ghz-transceivers/sx1281)

---

## Acknowledgements

Firmware functionality is based on the open-source **ExpressLRS** project.

The SX1281 is a Semtech 2.4 GHz RF transceiver.

This repository documents the AIRWINGS firmware adaptation, custom ExpressLRS TX target configuration, and reuse of the ESP32-C3 + SX1281 RF hardware platform for MAVLink telemetry transmission.
