# AS7343 CircuitPython Library

[![Build Status](https://github.com/yourusername/AS7343-circuitpython-bundle/actions/workflows/build.yml/badge.svg)](https://github.com/yourusername/AS7343-circuitpython-bundle/actions)

A CircuitPython driver for the AMS AS7343 14-channel spectral sensor. This device offers precise spectral measurements across the visible and near-infrared range, supporting applications like color matching, light source profiling, and reflectance-based analysis.

---

## Description

The AMS AS7343 provides spectral data from approximately 380 nm to 1000 nm through 14 independent channels:

- **Visible spectrum (F1–F8, FZ, FY, FXL)**
- **Near-infrared (NIR)**
- **Clear (unfiltered)**
- (Flicker detection is not included in this driver)

Due to its multiplexed design, the AS7343 uses an internal SMUX (sensor multiplexer) to switch among groups of photodiodes. This allows measurement across the full spectrum using only a small number of ADCs.

---

## Features

- Fully configurable via I2C (address: `0x39`)
- Spectral data from 14 channels:
  - `F1–F4`, `FZ`, `FY`, `F5`, `F6–F8`, `FXL`, `NIR`, `CLR`
- Adjustable gain:
  - `0.5x` to `2048x` via `GAIN_` constants
- Programmable integration time (μs resolution)
- SMUX mode selection to target spectral ranges
- On-demand and periodic measurements
- Power management options:
  - Low power idle
  - Sleep After Interrupt (SAI)
  - Full shutdown/wake
- Threshold detection with high-channel alerts
- Simple interface using CircuitPython and BusDevice

---

## Hardware

- **Sensor Datasheet**: [AS7343 at ams.com](https://ams.com/as7343)
- **I2C Address**: `0x39`
- Compatible with STEMMA QT, Qwiic, or direct I2C wiring
- Supports 3.3V I2C logic levels

---

## Dependencies

This library requires:

- [Adafruit CircuitPython](https://github.com/adafruit/circuitpython)
- [Adafruit CircuitPython BusDevice](https://github.com/adafruit/Adafruit_CircuitPython_BusDevice)

To install all dependencies, download the [CircuitPython bundle](https://circuitpython.org/libraries), and copy the following files into your device’s `lib/` directory:

- `as7343.py` (from this repo)
- `adafruit_bus_device/` (from the bundle)

---

## Installation

To install:

1. Connect your CircuitPython device (e.g., Pico W, Feather, QT Py).
2. Mount it as a USB drive.
3. Copy `as7343.py` into the `lib/` directory.
4. Ensure `adafruit_bus_device` is also present in `lib/`.

Once installed, import and begin using the library in `code.py`.

---

## Basic Usage

```python
import board
import time
import as7343

i2c = board.STEMMA_I2C()
sensor = as7343.AS7343(i2c)

# Configure sensor
sensor.gain = as7343.GAIN_64X
sensor.integration_time = 100000  # 100 ms

# Perform full scan
data = sensor.read_all()

# Print results
print("AS7343 Full Spectrum Read:")
for channel, value in data.items():
    print(f"{channel}: {value}")
```

---

## SMUX Mode Reads

Use `read_smux_mode()` to selectively access different groups of channels:

```python
visible = sensor.read_smux_mode(as7343.SMUX_VISIBLE)
nir = sensor.read_smux_mode(as7343.SMUX_NIR)
extra = sensor.read_smux_mode(as7343.SMUX_FZF5)
```

Each call triggers an appropriate SMUX setup, performs a scan, and returns a dictionary of results.

---

## Gain and Integration Time

Gain amplifies the analog signal before ADC conversion:

```python
sensor.gain = as7343.GAIN_2048X
```

Integration time sets the light collection period in microseconds:

```python
sensor.integration_time = 200000  # 200 ms
```

Adjust both for optimal results in varying light levels.

---

## Power Management

To save energy during idle times:

```python
sensor.enable_low_power_mode(True)
```

Sleep After Interrupt (SAI):

```python
sensor.enable_sleep_after_interrupt(True)
```

Full shutdown and wake:

```python
sensor.shutdown()
...
sensor.wake()
```

---

## Threshold Monitoring

You can check for oversaturation:

```python
data = sensor.read_all()
alert_channels = sensor.check_thresholds(60000)

for label, value in alert_channels:
    print(f"WARNING: {label} = {value}")
```

---

## Channel Map

- F1–F4: Violet to green (~405–515 nm)
- F5, FY: Green/yellow (~550–560 nm)
- F6–F8: Red/NIR (~640–745 nm)
- FZ, FXL: Intermediate bands (~450, 600 nm)
- NIR: ~855 nm
- CLR: Full-spectrum unfiltered

---

## License

This library is released under the MIT License.  
Copyright (c) 2025.

---

## Author

Your Name – [GitHub Repository](https://github.com/yourusername/AS7343-circuitpython-bundle)
