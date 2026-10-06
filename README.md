# iot-drone-controller-bhone

This repository contains documentation for configuring a Raspberry Pi 5 to act as a MAVProxy server communicating with a Pixhawk 6C over a USB connection.

Below are step-by-step instructions to prepare the hardware, install MAVProxy, configure the Pixhawk serial port parameters, and forward telemetry to your ground control station (GCS).

---

## Prerequisites
- Raspberry Pi 5 with Raspberry Pi OS installed and network access
- Pixhawk 6C flight controller
- USB-C or USB cable to connect the Pi to the Pixhawk
- A laptop or ground control station (Mission Planner, QGroundControl) if you want to forward telemetry

## 1. Connect hardware via USB (Physical setup)
1. Connect a USB-C or USB cable from one of the Raspberry Pi 5's USB ports to the USB/USER or TELEM1/USB port on the Pixhawk 6C.
2. On the Raspberry Pi, verify the Pixhawk serial device appears:

```bash
ls /dev/ttyACM*
ls /dev/ttyUSB*
```

You should see a device like `/dev/ttyACM0` appear when the Pixhawk is plugged in.

## 2. Install dependencies and MAVProxy (Software setup)
Update package lists and install Python pip plus MAVProxy prerequisites:

```bash
sudo apt update
sudo apt install python3-pip python3-dev python3-opencv libxml2-dev libxslt1-dev -y
```

Install MAVProxy using pip. It's recommended to use a virtual environment; if you install globally on modern Raspberry Pi OS you may need `--break-system-packages`:

```bash
pip3 install MAVProxy --break-system-packages
```

Verify installation:

```bash
mavproxy.py --version
```

## 3. Configure Pixhawk serial port parameters (Flight controller parameters)
1. Connect your ground station (Mission Planner or QGroundControl) directly to the Pixhawk (or use existing telemetry) to configure the port parameters for the USB connection.
2. Locate the SERIAL ports corresponding to the USB connection. Depending on firmware and build these can be `SERIAL0`, `SERIAL1`, or another index (consult ArduPilot/PX4 docs for exact mapping to USB).
3. For the port used by USB, set the protocol to `2` (MAVLink 2). For native USB the baud rate may be ignored, but set a standard value if you are using a serial converter.

## 4. Test MAVProxy connection (Initialization)
Start MAVProxy on the Raspberry Pi and point it at the USB device node found in Step 1. To forward telemetry to your GCS over UDP, include an `--out` argument:

```bash
mavproxy.py --master=/dev/ttyACM0 --baudrate 115200 --out=udp:YOUR_GCS_IP:14550
```

- Replace `YOUR_GCS_IP` with the IP address of your laptop/ground station if forwarding over Wi‑Fi.
- Omit the `--out` option if you are testing locally or only need the MAVProxy console on the Pi.

Verification: MAVProxy should connect, download parameters from the Pixhawk, and display the command prompt (e.g. `STABILIZE>`).

---

If you'd like, I can also:
- Add a troubleshooting section for common issues (no /dev/ttyACM device, permission errors, systemd service to run MAVProxy at boot)
- Create a systemd service file to run MAVProxy on boot and forward telemetry automatically
- Add commands to set up a Python virtual environment and install MAVProxy there

Tell me which of those you'd like added and I'll update the README accordingly.
