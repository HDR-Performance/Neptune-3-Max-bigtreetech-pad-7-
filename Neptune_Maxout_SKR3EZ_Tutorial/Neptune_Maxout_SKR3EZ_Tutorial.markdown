# 🚀 Neptune Maxout: Upgrade Your Elegoo Neptune 3 Max with SKR 3 EZ & TMC5160 Pro Drivers

Welcome to the ultimate guide for supercharging your **Elegoo Neptune 3 Max** with a **BIGTREETECH SKR 3 EZ** mainboard and **TMC5160 Pro EZ** drivers! 🎉 This tutorial walks you through wiring, firmware flashing, and Klipper setup for a seamless upgrade. Whether you're coming from a stock Robin Nano 2.2 or a Maxout mod, this guide has you covered. Let’s make your 3D printer faster, smarter, and more precise! 💪

> **🌟 Why Upgrade?**  
> The SKR 3 EZ with TMC5160 drivers unlocks advanced Klipper features like adaptive meshing, input shaping, and precise control, all while keeping wiring plug-and-play for the Neptune 3 series. Plus, you get a modern interface with the BTT Pad 7 or web-based Mainsail on a Raspberry Pi!

## 🛠️ Prerequisites

Before diving in, gather these essentials:

- **Tools**: Screwdrivers, computer with internet access.
- **Components**:
  - BIGTREETECH SKR 3 EZ mainboard
  - TMC5160 Pro EZ drivers (for X, Y, Z, extruder)
  - BTT Pad 7 or Raspberry Pi 4/5
  - Elegoo Neptune 3 Max components (stepper motors, hotend, heated bed, fans, probe, filament sensor, 24V LED lights)
- **Software**:
  - [PuTTY](https://puttygen.com/download-putty) (SSH client)
  - [Raspberry Pi Imager](https://www.raspberrypi.com/software/) (for SD card flashing)
  - Klipper firmware for STM32H723 or STM32H743
- **Safety**: **Always disconnect power** before wiring to avoid short circuits. Double-check connections!

> **📌 Note**  
> All Neptune 3 series harness connectors are **plug-and-play** with the SKR 3 EZ—no rewiring needed! The SKR 3 EZ configures TMC5160 drivers for SPI mode via software in `printer.cfg`, so no physical jumpers are required. The display is either the BTT Pad 7 or web-based Mainsail (Raspberry Pi 4/5).

## 📋 Step-by-Step Instructions

### 🧰 Step 1: Wiring the SKR 3 EZ to Neptune 3 Max Components

The Neptune 3 series (including the Max) uses **plug-and-play harnesses** that fit directly into the SKR 3 EZ ports, making wiring a breeze! 🌬️ Follow these steps to connect everything.

#### 1.1 Install TMC5160 Pro EZ Drivers

- **Action**: Before mounting the board, install TMC5160 Pro EZ drivers into the X, Y, Z, and E0 slots on the SKR 3 EZ.
- **Tip**: Ensure drivers are firmly seated and pins aligned to avoid damage.
- **Note**: SPI mode is set via `printer.cfg` (see `[tmc5160]` sections). No jumpers needed!
- **⚠️ Caution**: Handle drivers on a non-conductive surface to prevent static discharge.

#### 1.2 Prepare the Harness

- Disconnect all harnesses from the stock Robin Nano board.
- Identify the bundles: motors (X, Y, Z, Extruder), heaters (hotend, bed), fans, sensors (endstops, probe, filament sensor), power, and 24V LED lights.
- **Pro Tip**: Label connectors to keep track during reconnection.

#### 1.3 Mount the SKR 3 EZ

- Secure the SKR 3 EZ (with drivers installed) in the printer’s electronics enclosure, replacing the stock board.
- Ensure proper ventilation and use screws or standoffs for stability.

#### 1.4 Connect the Main Power Harness

- Locate the 24V power harness (2-pin connector, red/black wires) from the Neptune 3 PSU.
- Plug into the `POWER IN` terminals (`V+` for red/positive, `V-` for black/negative).
- **Do not power on yet**—keep going!

#### 1.5 Connect Motor Harnesses (Plug-and-Play)

- **X-Axis Motor**: Plug the 4-pin JST harness into `X-MOT`.
- **Y-Axis Motor**: Plug into `Y-MOT`.
- **Z-Axis Motor**: Plug into `Z-MOT`.
- **Extruder Motor**: Plug into `E0-MOT`.
- **Note**: These standard 4-pin connectors are plug-and-play. Ensure a snug fit.

#### 1.6 Connect Heater and Fan Harnesses (Plug-and-Play)

- **Hotend Heater**: Plug the 2-pin harness into `HE0`.
- **Heated Bed**: Plug into `BED` (align red to positive if marked).
- **Hotend Fan**: Plug into `HEATER_FAN`.
- **Part Cooling Fan**: Plug into `FAN0`.
- **Note**: These 2-pin connectors are plug-and-play. Secure them firmly.

#### 1.7 Connect Sensor Harnesses (Plug-and-Play)

All sensor harnesses are plug-and-play. The stock Neptune 3 display is not used; use the BTT Pad 7 or Mainsail via Raspberry Pi.

- **X-Axis Endstop**: Plug into `X-STOP` (signal to `PC1`).
- **Y-Axis Endstop**: Plug into `Y-STOP` (signal to `PC3`).
- **Hotend Thermistor**: Plug into `TH0` (signal to `PA2`).
- **Bed Thermistor**: Plug into `TB` (signal to `PA1`).
- **Probe**: Plug into `PROBE` (signal to `PC0`, includes 5V/GND).
- **Filament Sensor**: Plug into `FIL-DET` (signal to `PC2`, includes 5V/24V).
- **Display**:
  - **BTT Pad 7**: Primary display/control interface. Connect to SKR 3 EZ via USB (Step 8) and power separately (per BTT Pad 7 manual). Stock display harness (`EXP1`/`EXP2`) is unused.
  - **Raspberry Pi 4/5**: No physical display needed. Access Mainsail via the Pi’s IP in a browser (Step 6). `EXP1`/`EXP2` headers remain unused.

#### 1.8 Verify Plug-and-Play Compatibility

- All harnesses (motors, heaters, fans, endstops, thermistors, probe, filament sensor, power, LEDs) plug directly into SKR 3 EZ ports (`X-MOT`, `Y-MOT`, `Z-MOT`, `E0-MOT`, `HE0`, `BED`, `HEATER_FAN`, `FAN0`, `X-STOP`, `Y-STOP`, `TH0`, `TB`, `PROBE`, `FIL-DET`). Keyed connectors align without force.
- Test fit all connections before powering on.

#### 1.9 Optional Components

- **LED (Optional)**: The stock 24V LED lights use a fan PWM output for control.
  - **Connection**: Plug the LED harness into `PB9` (signal, per the commented `[led LED_Light]` section). Connect the positive wire to a 24V power terminal and the negative to `PB9`. The connector is plug-and-play and PWM-compatible.
  - **Settings**: Controlled via `SET_LED` (e.g., 25% brightness in `START_PRINT`). Uncomment `[led LED_Light]` in `printer.cfg`:
    ```ini
    [led LED_Light]
    white_pin: PB9
    shutdown_speed: 1.0
    cycle_time: 0.010
    ```
- **ADXL345 (Accelerometer)**: Connect to the SPI bus on the BTT Pad 7 or Raspberry Pi (`spi_bus: spidev1.1`).

### 💾 Step 2: Install Required Software

1. **PuTTY**: Download for SSH access: [https://puttygen.com/download-putty](https://puttygen.com/download-putty).
2. **Raspberry Pi Imager**: Download for SD card flashing: [https://www.raspberrypi.com/software/](https://www.raspberrypi.com/software/).

### 📀 Step 3: Flash the BTT Pad 7 or Raspberry Pi

1. **Download Firmware Image**: Get the Neptune Maxout SKR 3 EZ image for BTT Pad 7 or a Klipper-compatible image for Raspberry Pi 4/5. Check your supplier or community forums for links.
2. **Flash SD Card**: Use Raspberry Pi Imager to flash the image onto a microSD card.
3. **Insert SD Card**: Insert into BTT Pad 7 or Raspberry Pi and power on (no USB to SKR 3 EZ yet).

### ⚙️ Step 4: Flash the SKR 3 EZ Firmware

1. **Identify Processor**: Check your SKR 3 EZ for STM32H723 or STM32H743. Newer boards use STM32H723 (550 MHz).
2. **Compile Klipper Firmware**:
   - Compile for STM32H723 (or STM32H743) with 128KiB bootloader and USB on `PA11/PA12`.
   - See [Klipper docs](https://www.klipper3d.org/Installation.html).
3. **Flash Firmware**:
   - Rename `klipper.bin` to `firmware.bin`.
   - Copy to a blank microSD card’s root.
   - Insert into SKR 3 EZ and power on.
   - Verify with `firmware.cur` file on the card.

### 🔌 Step 5: Configure USB Mode on SKR 3 EZ

1. **Set USB Mode**: Set the jumper/switch behind the USB port to USB mode (highest position). Most boards ship in CAN mode (lowest).
2. **No USB Yet**: Keep the USB cable between BTT Pad 7 (or Raspberry Pi) and SKR 3 EZ disconnected.

### 🌐 Step 6: Connect BTT Pad 7 or Raspberry Pi to Network

1. **Boot Device**: Power on BTT Pad 7 or Raspberry Pi with flashed SD card.
2. **Connect to Network**:
   - Use Wi-Fi (via settings menu on BTT Pad 7 or Raspberry Pi config) or an RJ45 LAN cable.
   - Find the device’s IP address in settings or your router (e.g., `192.168.1.100`).

### 🔑 Step 7: SSH into BTT Pad 7 or Raspberry Pi and Find MCU Serial ID

1. **Open PuTTY**: Launch PuTTY.
2. **SSH into Device**:
   - Enter the device’s IP in the “Host Name” field.
   - BTT Pad 7: Username `biqu`, Password `biqu` (unless changed).
   - Raspberry Pi: Username `pi`, Password `raspberry` (unless changed).
   - Click “Open” to connect.
3. **Find Serial ID**:
   - Run: `ls /dev/serial/by-id/`
   - Example output: `usb-Klipper_stm32h723xx_3A000F001251333031373138-if00`
   - Copy the serial ID.

### ⚙️ Step 8: Update `printer.cfg` with Serial ID

1. **Access Mainsail**:
   - Open a browser and enter the device’s IP (e.g., `192.168.1.100`) to access Mainsail.
2. **Edit `printer.cfg`**:
   - Navigate to `printer.cfg` in Mainsail’s configuration section.
   - Update `[mcu] serial: /dev/serial/by-id/` with the copied serial ID, e.g.: `serial: /dev/serial/by-id/usb-Klipper_stm32h723xx_3A000F001251333031373138-if00`.
3. **Save and Restart**: Click “Save & Restart” in Mainsail to apply changes.

### 🧪 Step 9: Test the Setup

1. **Power On**: Connect the USB cable between SKR 3 EZ and BTT Pad 7 or Raspberry Pi, then power on.
2. **Verify Connections**:
   - In Mainsail, run `G28` to home all axes.
   - Test hotend: `M104 S200` (200°C).
   - Test bed: `M140 S60` (60°C).
   - Test fan: `M106 S255` (full speed).
   - Test LEDs: `SET_LED LED=LED_Light WHITE=0.25` (25% brightness, if `[led LED_Light]` is enabled).
   - Check probe: `QUERY_PROBE`.
   - Check filament sensor: `QUERY_FILAMENT_SENSOR SENSOR=filament_sensor`.
3. **Calibrate PID Settings**:
   - **Hotend PID Tuning**: Run `NOZZLE_PID_TUNE TEMP=200 FAN_SPEED=0` to tune at 200°C with fan off. The macro homes the printer, disables the hotend fan, calibrates, and saves results to `printer.cfg`. Check Mainsail’s terminal for PID values.
   - **Bed PID Tuning**: Run `BED_PID_TUNE TEMP=60` to tune at 60°C. The macro homes, calibrates, and saves results. Check terminal for PID values.
   - **Note**: Use 200°C (hotend) and 60°C (bed) for PLA. Adjust `TEMP` for other materials (e.g., 240°C for ABS hotend, 80°C for PETG bed). Tune in a well-ventilated area.

### 🎉 Step 10: Final Configuration

1. **Copy Additional Files**: Place `KAMP_Settings.cfg`, `mainsail.cfg`, `timelapse.cfg`, `Line_Purge.cfg`, `Smart_Park.cfg`, and `Adaptive_Meshing.cfg` in your Klipper config directory. Find them in this repository: [HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-](https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-).
2. **Add Slicer G-code**: For start and end G-code to use in slicers like Orca or Cura, download the files from this repository: [HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-](https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-).

## 🛠️ Troubleshooting

- **No MCU Connection**: Check USB mode jumper, serial ID in `printer.cfg`, and USB connection.
- **Motor Issues**: Ensure connectors are secure in `X-MOT`, `Y-MOT`, `Z-MOT`, `E0-MOT`. Adjust `dir_pin` inversion (`!`) in `printer.cfg` if motors move incorrectly.
- **Probe Issues**: Verify `PROBE` connector and test with `QUERY_PROBE`.
- **SPI Errors**: Check TMC5160 CS and SPI settings in `printer.cfg`.
- **LED Issues**: Confirm 24V LED connector in `PB9` and 24V power. Uncomment `[led LED_Light]` in `printer.cfg`.
- **Mainsail Issues**: Ensure BTT Pad 7 or Raspberry Pi is networked and IP is correct.
- **PID Tuning Issues**: Home the printer (`G28`) before `NOZZLE_PID_TUNE` or `BED_PID_TUNE`. Check Mainsail terminal for errors.

## 🎯 Conclusion

Your Neptune 3 Max is now a high-performance beast with the SKR 3 EZ and TMC5160 Pro EZ drivers, powered by Klipper! 🖨️ All harnesses are plug-and-play, SPI is software-configured, and the BTT Pad 7 or Mainsail offers a sleek interface. The 24V LEDs shine via `PB9`, and PID tuning ensures precise temperatures. Grab the start/end G-code from the repository and enjoy top-tier printing! For support, check the [Klipper documentation](https://www.klipper3d.org/) or BIGTREETECH resources.

## 📄 Reference: Klipper `printer.cfg`

Find the complete `printer.cfg` in this repository: [printer.cfg](printer.cfg). Copy it to your Klipper configuration directory, update the MCU serial ID (Step 8), and uncomment `[led LED_Light]` for LED control if needed.