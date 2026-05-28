# Pico2W DualSense 5 Bridge

[中文](./README.CN.md)

> Turn a Raspberry Pi Pico2W into a wireless adapter for the DualSense (DS5) controller.

## Overview

This project enables the Raspberry Pi Pico2W to function as a Bluetooth bridge for the DualSense controller, allowing wireless connectivity with enhanced haptics support.

## Features

- 🎮 Full DualSense connectivity via Pico2W
- 🔊 Supports HD haptics (advanced vibration feedback)
- 📡 Wireless Bluetooth bridging

## Getting Started

### Flashing Firmware

1. Hold the BOOTSEL button on the Pico2W
2. Connect the Pico2W to your computer via USB
3. The device will mount as a USB storage device
4. Drag and drop the .uf2 firmware file onto the device

### Pairing the Controller

1. Put the DualSense controller into Bluetooth pairing mode
2. Wait for the Pico2W to detect and connect
3. Once connected, the device will appear on the host system

***You may need to replug the Pico when the controller is in pairing mode.***

## Configuration

You can modify the Pico settings via the web config.

- For release: https://ds5.awalol.eu.org
- For development: https://ds5-dev.awalol.eu.org

## Notes

The Pico device will only be visible to the system after the controller is connected

Some behaviors depend on reconnection cycles to take effect

### Low-battery LED indicator

When the connected DualSense reports its battery at or below 10% (and it is not charging), the Pico onboard LED switches from solid-on to a 1 Hz blink so you can see the warning at a glance. The LED returns to solid-on as soon as the controller is plugged in or its reported level rises again. The blink also fires when `disable_pico_led` is set — the warning is treated as critical and overrides the LED-off preference; the LED returns to its disabled (off) state once the battery recovers or the controller starts charging.

To opt out at build time, configure with `-DENABLE_BATT_LED=OFF`. Default is ON.

### Pico W Version

Pico W only has haptics support, no speaker. You can enable Pico W firmware compilation with `-DPICO_W_BUILD=ON`, or download precompiled firmware from GitHub Actions.

### USB Wake Feature

This feature is experimental. If you need this functionality, please check out the feat/usb-wake branch to compile it, or use the precompiled firmware from GitHub Actions under that branch. The `ds5-bridge-wake.uf2` is the firmware with this feature enabled.

It is recommended to read #60 and #61 before using this feature.

### Community Fork
https://github.com/MarcelineVPQ/DS5Dongle-OLED-Edition
https://github.com/zurce/DS5Dongle-OLED

## Known Issues

- ⚠️ Audio may experience slight stuttering
- ⚠️ Overclocking is required for proper performance

## Performance / Overclocking

Due to encoding requirements, the Pico2W must be overclocked:

Current settings:

- Voltage: 1.2V
- Frequency: 320 MHz

If your device fails to boot:

- Increase voltage slightly or Reduce CPU frequency

## Build Instructions

To build the project from source:

1. ***Update TinyUSB in the Pico SDK to the latest version***
2. Compile using standard Pico SDK toolchain

On macOS, `tools/build-macos.sh` can prepare a repo-local Pico SDK checkout, prompt to install missing Homebrew build tools, initialize submodules, pin TinyUSB, and build the wake firmware:

```sh
tools/build-macos.sh
```

Use `tools/build-macos.sh --standard` for the non-wake firmware, `--clean` to rebuild from scratch, or `--sdk-dir <path>` to use an existing SDK checkout. When using `--sdk-dir`, the script asks before checking that SDK out to the required Pico SDK and TinyUSB versions. If Homebrew's `arm-none-eabi-gcc` formula is installed without standard C headers, the script asks to install the complete `gcc-arm-embedded` cask and points CMake at that toolchain.

## Wake-on-PS (optional)

A `-DENABLE_WAKE_HID=ON` build adds a second HID interface (a boot keyboard) that injects an **F15** keypress when any controller button is pressed while the host is suspended, waking the PC from **S3 sleep**. F15 was chosen because it has no default Windows or app binding — a stray fire never inserts characters or triggers shortcuts.

Scope: **S3 only.** Modern Standby (S0ix) is not supported. To check your machine, run `powercfg /a` — you need "Standby (S3)" listed under available sleep states.

After flashing the wake build:

1. Open Device Manager → the new **HID Keyboard Device** (and its parent **USB Composite Device**) → Properties → Power Management → tick **"Allow this device to wake the computer."**
2. Verify with `powercfg /devicequery wake_armed`.
3. Sleep the PC; press any button on the controller; the PC should wake within ~1 s.
4. After a wake, `powercfg /lastwake` should attribute the wake to the HID Keyboard Device.

## Roadmap
- Please check out [DS5Dongle plan](https://github.com/users/awalol/projects/5)

## Community
- Join the Discord server: [Discord Server](https://discord.gg/hM4ntchGCa)
- If you have a bug, please open an issue instead.

## References

- [rafaelvaloto/Pico_W-Dualsense](https://github.com/rafaelvaloto/Pico_W-Dualsense) — Project inspiration
- [egormanga/SAxense](https://github.com/egormanga/SAxense) — Bluetooth Haptics POC
- [https://controllers.fandom.com/wiki/Sony_DualSense](https://controllers.fandom.com/wiki/Sony_DualSense) - DualSense data report structure documentation
- [Paliverse/DualSenseX](https://github.com/Paliverse/DualSenseX) — Speaker report packet
