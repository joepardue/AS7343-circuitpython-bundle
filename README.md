AS7343 CircuitPython Library
============================

.. image:: https://github.com/yourusername/AS7343-circuitpython-bundle/workflows/Build%20CI/badge.svg
   :target: https://github.com/yourusername/AS7343-circuitpython-bundle/actions

A CircuitPython library for the AMS AS7343 14-channel spectral sensor. This sensor provides high-resolution spectral measurements across the visible and near-infrared spectrum, making it suitable for color matching, fluid analysis, spectral identification, and environmental monitoring applications.

Description
-----------

The AS7343 is a 14-channel spectral sensor that can detect light across multiple wavelength bands from approximately 380nm to 1000nm:

- 11 channels in the visible spectrum (F1-F8, FZ, FY, FXL)
- 1 near-infrared channel (NIR)
- 1 clear channel with no filtering (CLR)
- 1 flicker detection channel (not implemented in this library)

Each channel is sensitive to a specific wavelength band, allowing for detailed spectral analysis of light sources, reflected colors, or transmitted light through fluids.

.. image:: https://github.com/yourusername/AS7343-circuitpython-bundle/raw/main/docs/as7343_spectral_response.png

Features
--------

- Simple initialization with I2C
- Configurable gain settings (0.5x to 2048x)
- Adjustable integration time
- Complete 14-channel spectral readings
- Power management and low power modes
- Threshold detection
- Channel grouping using SMUX

Dependencies
------------

This driver depends on:

- `Adafruit CircuitPython <https://github.com/adafruit/circuitpython>`_
- `Bus Device <https://github.com/adafruit/Adafruit_CircuitPython_BusDevice>`_

To install the dependencies, use the Adafruit CircuitPython bundle:
https://github.com/adafruit/Adafruit_CircuitPython_Bundle

Usage Example
-------------

::

    import board
    import time
    import as7343

    i2c = board.STEMMA_I2C()
    sensor = as7343.AS7343(i2c)

    sensor.gain = as7343.GAIN_64X
    sensor.integration_time = 100000

    data = sensor.read_all()

    print("Spectral Data:")
    for key, val in data.items():
        print(f"{key}: {val}")

Advanced Usage
--------------

**SMUX Channel Modes**

::

    visible_data = sensor.read_smux_mode(as7343.SMUX_VISIBLE)
    nir_data = sensor.read_smux_mode(as7343.SMUX_NIR)
    fzf5_data = sensor.read_smux_mode(as7343.SMUX_FZF5)

**Power Management**

::

    sensor.enable_low_power_mode(True)
    sensor.shutdown()
    sensor.wake()

**Gain & Integration**

::

    sensor.gain = as7343.GAIN_2048X
    sensor.integration_time = 200000

Interpreting Spectral Data
--------------------------

- Relative ADC counts (max 65535) per channel
- Create ratios or visualize as spectra
- For calibrated irradiance, reference a known spectroradiometer

Hardware Considerations
-----------------------

- Mount sensor facing light source
- Use diffusers for consistency
- For reflective color use, synchronize with illumination:

::

    import digitalio
    led = digitalio.DigitalInOut(board.D5)
    led.direction = digitalio.Direction.OUTPUT
    led.value = True
    data = sensor.read_all()
    led.value = False

Troubleshooting
---------------

**Saturation (65535 readings):**

- Lower gain or integration time
- Move sensor farther
- Use filters

**Low Readings:**

- Increase gain/integration
- Check light path
- Use stronger source

License
-------

This library is released under the MIT license. See LICENSE file for details.
