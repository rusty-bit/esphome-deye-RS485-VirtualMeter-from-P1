# Deye Virtual Meter (ESPHome + HomeWizard P1)

This ESPHome project creates a "Virtual Meter" for Deye 3-Phase inverters. It pulls live local data from a HomeWizard P1 meter via its local API and emulates an Eastron SDM630 Modbus meter. 

This allows the Deye inverter to perform rapid "Zero Export" throttling without needing a physical CT clamp or a real Eastron meter installed in your breaker box.

## Hardware Used
* **Microcontroller:** Waveshare ESP32-S3-RS485-CAN
* **Smart Meter:** HomeWizard P1 Dongle (with Local API enabled)
* **Inverter:** Deye 3-Phase Hybrid Inverter

## Why this is needed
Deye inverters expect Modbus RTU communication over RS485 from an Eastron SDM630 meter (Slave ID 1). This ESP32 acts as a Modbus server, intercepting the Deye's requests and instantly replying with the live grid voltage, power, and lifetime energy totals fetched from the HomeWizard P1.
It is especially helpful if your inverter is mounted away from the grid feed line which does not allow you to use the clamp meters and if you do not have space to mount a Modbus RS485 meter (or if you do not want to pull another wire through your house..)

## Features
* **Correct Waveshare S3 Pins:** Pre-configured for the specific internal TX (17), RX (18), and Flow Control (21) pins of the Waveshare ESP32-S3 board.
* **Network Stability:** Uses background API polling (`interval`), a 5-second HTTP timeout, and `Connection: close` headers to prevent the HomeWizard P1 web server from locking up or crashing the ESP32.
* **Merged Tariffs:** Automatically adds T1 (Off-Peak) and T2 (Peak) energy totals together to satisfy the Deye's request for absolute lifetime import/export (Registers 72 & 74), preventing `0x48` exception errors.

## Installation
1. Enable the **Local API** in your HomeWizard Energy App.
2. Update the `p1_meter_ip` in the YAML to match your P1 meter's IP address.
3. Fill out your `secrets.yaml` with your Wi-Fi credentials.
4. Flash the code to your ESP32 via ESPHome.
5. Wire the RS485 A+ and B- terminals on the Waveshare board to the `Meter` RJ45 port on the Deye inverter (Usually Pin 1 / Pin 2, or Pin 7 / Pin 8 depending on the firmware).
6. Set the Deye inverter to `Eastron` in the Advanced Meter settings.
