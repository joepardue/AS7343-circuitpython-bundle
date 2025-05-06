AS7343 CircuitPython Library
============================

.. image:: https://img.shields.io/badge/AS7343-CircuitPython-blue.svg
   :target: https://github.com/yourusername/AS7343-circuitpython-bundle

CircuitPython driver for the AMS AS7343 14-channel spectral sensor. This sensor provides high-resolution spectral measurements across the visible and near-infrared spectrum with a compact I2C interface.

Description
-----------

The AS7343 is a 14-channel multi-spectral sensor with the following key capabilities:

- 14 distinct wavelength bands from ~380–1000nm:
  - 11 visible/NIR bands (F1–F8, FY, FZ, FXL)
  - 1 clear/unfiltered channel (CLR)
  - 1 near-infrared channel (NIR)
- Adjustable integration time and gain
- Multiple SMUX channel configurations
- Low power and sleep-after-interrupt modes

This driver supports full-channel scans or selective reads using SMUX modes. It also includes power management and threshold alert support.

Hardware
--------

- `AMS AS7343 Datasheet <https://ams.com/as7343>`_
- I2C address: 0x39
- Recommended breakout: Custom AS7343 module or Adafruit-compatible Qwiic boards

Dependencies
------------

This driver depends on:

- `Adafruit CircuitPython <https://github.com/adafruit/circuitpython>`_
- `Adafruit Bus Device <https://github.com/adafruit/Adafruit_CircuitPython_BusDevice>`_

Installation
------------

To install this library, copy `as7343.py` to your device, along with the dependencies listed above. For full bundles, visit:

https://circuitpython.org/libraries

Quickstart
----------

Basic usage example:

.. code-block:: python

    import board
    import as7343

    i2c = board.STEMMA_I2C()
    sensor = as7343.AS7343(i2c)

    sensor.gain = as7343.GAIN_64X
    sensor.integration_time = 100000  # 100 ms

    data = sensor.read_all()
    print("Full Spectral Data:")
    for k, v in data.items():
        print(f"{k}: {v}")

SMUX Mode Reads
---------------

Use `read_smux_mode()` for efficient grouped reads:

.. code-block:: python

    vis_data = sensor.read_smux_mode(as7343.SMUX_VISIBLE)
    nir_data = sensor.read_smux_mode(as7343.SMUX_NIR)

Threshold Monitoring
--------------------

To detect saturated or high-value channels:

.. code-block:: python

    flagged = sensor.check_thresholds(60000)
    if flagged:
        print("High values detected:")
        for channel, value in flagged:
            print(f"{channel}: {value}")

Power Management
----------------

The sensor supports low-power and sleep modes:

.. code-block:: python

    sensor.enable_low_power_mode(True)
    sensor.shutdown()
    sensor.wake()

Channel Summary
---------------

The `read_all()` function provides these 14 channel readings:

- F1  (405nm)
- F2  (425nm)
- F3  (475nm)
- F4  (515nm)
- FY  (555nm)
- F5  (550nm)
- F6  (640nm)
- F7  (690nm)
- F8  (745nm)
- FZ  (450nm)
- FXL (600nm)
- NIR (~855nm)
- CLR (Clear)

These can be accessed via:

- `sensor.data` – dictionary of last values
- `sensor.channels` – list in standard order

Contributing
------------

Improvements are welcome! Please file issues or submit PRs via:

https://github.com/yourusername/AS7343-circuitpython-bundle

License
-------

This library is licensed under the MIT License. See ``LICENSE`` for full details.
