# Beginner’s Guide to Installing KAMP on BIGTREETECH Pad 7 for Elegoo Neptune 3 Max

Welcome to this beginner-friendly guide for installing the **Klipper Adaptive Meshing and Purging (KAMP)** feature set on your **Elegoo Neptune 3 Max** with a **BIGTREETECH Pad 7** and **ZNP Robin Nano v2.2 board (STM32F401)**. KAMP enhances your 3D printer with **adaptive bed meshing** (only probes where you’re printing), **adaptive purging** (cleans the nozzle with a cool purge line or logo near your print), and **smart parking** (moves the printhead to a smart spot during pauses). This guide uses **PuTTY** to connect to your Pad 7 and sets up KAMP using pre-optimized configuration files from a trusted GitHub repository. We assume you’ve already installed Klipper and flashed the firmware to your Neptune 3 Max. If not, you’ll need to do that first (check r/ElegooNeptune3 for a guide).

## Why Use KAMP?
- **No Wasted Probes**: Creates a dense mesh only where your print is (e.g., a super-detailed mesh for a small 3DBenchy).
- **Cool Purges**: Draws a purge line or logo right next to your print, saving filament and looking awesome.
- **Smart Parking**: Moves the printhead near the print area during pauses for faster resumes.
- **Easy Setup**: Just add a few files to your Klipper config, and you’re good to go!
- **Works with Your Probe**: Compatible with BLTouch, inductive probes, and more (Neptune 3 Max uses a probe at X:-28.5, Y:22).

## What You’ll Need
- **BIGTREETECH Pad 7**: Powered on, connected to Wi-Fi, running Klipper with Mainsail or Fluidd.
- **PuTTY**: Free software to connect to the Pad 7 (download from [putty.org](https://www.putty.org/)).
- **WinSCP**: Optional for easier file transfers (download from [winscp.net](https://winscp.net/)).
- **Elegoo Neptune 3 Max**: Running Klipper firmware on the ZNP Robin Nano v2.2 board.
- **Computer**: Windows, Mac, or Linux to run PuTTY and access the internet.
- **Wi-Fi Router Access**: To find the Pad 7’s IP address (e.g., `192.168.54.42`).
- **Internet Access**: To download files from [HDR-Performance/Neptune-3-Max-bigtreetech-pad-7](https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-/tree/main).
- **Slicer Setup**: Your slicer (e.g., Cura, PrusaSlicer) must label objects for KAMP’s adaptive features (check your slicer’s Klipper settings to enable “Label Objects”).

## Step-by-Step Instructions

### 1. **Set Up PuTTY and Connect to Your Pad 7**
   - **Install PuTTY**:
     - Download PuTTY from [putty.org](https://www.putty.org/), install it, and open it.
   - **Find Your Pad 7’s IP Address**:
     - Log into your Wi-Fi router’s admin page (often `192.168.0.1` or `192.168.1.1` in a browser; check your router’s manual).
     - Find the BIGTREETECH Pad 7 in the list of devices (might show as “BTT” or similar) and note its IP address (e.g., `192.168.54.42`).
     - Tip: If you can’t access the router, ask your network admin or check the Pad 7’s settings for its IP.
   - **Connect with PuTTY**:
     - In PuTTY, enter the Pad 7’s IP address in “Host Name (or IP address)”.
     - Set “Port” to `22` and select “SSH”.
     - Click “Open”. Accept any security warning by clicking “Yes”.
     - Log in with:
       - Username: `biqu`
       - Password: `biqu`
     - If you see a prompt like `biqu@cb1:~$`, you’re connected! If not, verify the IP and ensure the Pad 7 is on Wi-Fi.

### 2. **Verify Klipper and Moonraker Are Running**
   - Check if Klipper is installed:
     ```bash
     ls ~/klipper
     ```
     - You should see folders like `docs`, `src`. If not, you need to install Klipper first (ask in r/ElegooNeptune3 for help).
   - Check if Klipper is running:
     ```bash
     sudo systemctl status klipper
     ```
     - Look for “active (running)”. Press `Ctrl+C` to exit.
   - Check if Moonraker is running (needed for KAMP’s object detection):
     ```bash
     sudo systemctl status moonraker
     ```
     - Look for “active (running)”. If it’s not running, install Moonraker (check [moonraker.readthedocs.io](https://moonraker.readthedocs.io/)).
   - Confirm `printer.cfg` exists:
     ```bash
     ls ~/printer_data/config/printer.cfg
     ```
     - If it’s missing, you need to set up Klipper first.

### 3. **Install KAMP Dependencies**
   - KAMP needs the `numpy` library for mesh calculations. Install it:
     ```bash
     pip install numpy
     ```
     - If you get “pip: command not found”, install `pip`:
       ```bash
       sudo apt update
       sudo apt install python3-pip
       ```
       - This updates your system and installs `pip`. It may take a minute.
       - Then retry `pip install numpy`.
   - Verify `numpy` is installed:
     ```bash
     pip show numpy
     ```
     - You should see a version number (e.g., `1.26.4`). If not, repeat the install or ask for help.

### 4. **Download Optimized Configuration Files**
   - We’ll use pre-tuned KAMP configuration files for the Neptune 3 Max from [HDR-Performance/Neptune-3-Max-bigtreetech-pad-7](https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-/tree/main).
   - Navigate to the Klipper config folder:
     ```bash
     cd ~/printer_data/config
     ```
   - Download the files:
     ```bash
     wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/printer.cfg
     wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/KAMP_Settings.cfg
     wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/Line_Purge.cfg
     wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/Smart_Park.cfg
     wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/Adaptive_Meshing.cfg
     wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/mainsail.cfg
     wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/timelapse.cfg
     ```
     - Each command downloads one file. Wait for each to complete (you’ll see the prompt return).
   - **Optional: Use WinSCP**:
     - Install WinSCP from [winscp.net](https://winscp.net/).
     - Connect to the Pad 7:
       - Hostname: Pad 7’s IP address
       - Username: `biqu`
       - Password: `biqu`
       - Protocol: SCP or SFTP
     - Download all files (`printer.cfg`, `KAMP_Settings.cfg`, `Line_Purge.cfg`, `Smart_Park.cfg`, `Adaptive_Meshing.cfg`, `mainsail.cfg`, `timelapse.cfg`) from the GitHub link to your computer.
     - In WinSCP, navigate to `/home/biqu/printer_data/config/` and drag the files there.
   - Verify the files are present:
     ```bash
     ls ~/printer_data/config
     ```
     - You should see `printer.cfg`, `KAMP_Settings.cfg`, `Line_Purge.cfg`, `Smart_Park.cfg`, `Adaptive_Meshing.cfg`, `mainsail.cfg`, and `timelapse.cfg`.

### 5. **Configure Moonraker for KAMP**
   - KAMP uses `[exclude_object]` to know where your print is, so Moonraker needs to enable object processing.
   - Back up your current `moonraker.conf`:
     ```bash
     cd ~/printer_data/config
     mv moonraker.conf moonraker.conf.bak
     ```
   - Download the optimized `moonraker.conf` from your GitHub:
     ```bash
     wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/moonraker.conf
     ```
   - Verify it includes:
     ```ini
     [file_manager]
     enable_object_processing: True
     ```
     - If you prefer to edit manually:
       ```bash
       nano ~/printer_data/config/moonraker.conf
       ```
       - Add or confirm the `[file_manager]` section above. Save (`Ctrl+O`, `Enter`, `Ctrl+X`).
   - Add KAMP to Moonraker’s update manager for future updates:
     ```bash
     nano ~/printer_data/config/moonraker.conf
     ```
     - Add this section if not already present:
       ```ini
       [update_manager Klipper-Adaptive-Meshing-Purging]
       type: git_repo
       channel: dev
       path: ~/Klipper-Adaptive-Meshing-Purging
       origin: https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging.git
       managed_services: klipper
       primary_branch: main
       ```
     - Save (`Ctrl+O`, `Enter`, `Ctrl+X`).
   - Restart Moonraker to apply changes:
     ```bash
     sudo systemctl restart moonraker
     ```

### 6. **Verify Printer Configuration**
   - The `printer.cfg` from your repository is pre-configured for KAMP. Check it:
     ```bash
     cat ~/printer_data/config/printer.cfg
     ```
     - Ensure these lines are near the top:
       ```ini
       [include KAMP_Settings.cfg]
       [include Line_Purge.cfg]
       [include Smart_Park.cfg]
       [include Adaptive_Meshing.cfg]
       [include mainsail.cfg]
       [include timelapse.cfg]
       [exclude_object]
       ```
     - Also confirm the `[extruder]` section includes:
       ```ini
       max_extrude_cross_section: 10
       ```
       - And check for:
         ```ini
         [firmware_retraction]
         retract_length: 0.5
         retract_speed: 35
         unretract_extra_length: 0
         unretract_speed: 35
         ```
     - If anything is missing, edit `printer.cfg`:
       ```bash
       nano ~/printer_data/config/printer.cfg
       ```
       - Add the missing lines. Save (`Ctrl+O`, `Enter`, `Ctrl+X`).
   - Verify the serial port in the `[mcu]` section:
     ```bash
     ls /dev/ttyS* /dev/ttyUSB* /dev/ttyACM* /dev/serial/by-id/*
     ```
     - Look for a port like `/dev/serial/by-id/usb-1a86_USB_Serial-if00-port0` (for USART1) or another if using J17 (USART2).
     - Update `printer.cfg` if needed:
       ```bash
       nano ~/printer_data/config/printer.cfg
       ```
       - Change the `[mcu]` section, e.g.:
         ```ini
         [mcu]
         serial: /dev/serial/by-id/usb-1a86_USB_Serial-if00-port0
         restart_method: command
         ```
       - Save (`Ctrl+O`, `Enter`, `Ctrl+X`).

### 7. **Restart Klipper**
   - Apply all changes:
     ```bash
     sudo systemctl restart klipper
     ```
   - Check that Klipper is running:
     ```bash
     sudo systemctl status klipper
     ```
     - Look for “active (running)”. Press `Ctrl+C` to exit.

### 8. **Test KAMP Features**
   - Open your browser and go to the Pad 7’s web interface (e.g., `http://192.168.54.42` for Mainsail/Fluidd).
   - Test adaptive meshing:
     - In the “Console” or “Terminal” section, type:
       ```bash
       SETUP_KAMP_MESHING DISPLAY_PARAMETERS=1 LED_ENABLE=1 FUZZ_ENABLE=0 ADAPTIVE_ENABLE=1
       ```
       - This sets up KAMP. You should see a confirmation message.
     - Type:
       ```bash
       BED_MESH_CALIBRATE ADAPTIVE=1
       ```
       - The printer will probe the bed (11x9 points for the Neptune 3 Max). Check the “Bed Mesh” section in the web interface to see the mesh.
   - Test a print:
     - Slice a small model (e.g., 3DBenchy) in your slicer with “Label Objects” enabled.
     - Upload the G-code to the web interface and start the print.
     - Watch for:
       - A **purge line** near the print area (configured by `Line_Purge.cfg`).
       - **Smart parking** if you pause the print (head moves near the print area).
       - A compact bed mesh covering only the print area.
   - If the purge or mesh fails, ensure `[exclude_object]` is in `printer.cfg` and `enable_object_processing: True` is in `moonraker.conf`.

### 9. **Fine-Tune KAMP Settings**
   - The `KAMP_Settings.cfg` from your repository is pre-tuned but can be adjusted:
     ```bash
     nano ~/printer_data/config/KAMP_Settings.cfg
     ```
   - Key settings (already set for Neptune 3 Max, but you can tweak):
     - `mesh_margin: 0` – Adds space around the print area for the mesh. Increase to `10` if using brims.
     - `fuzz_amount: 0` – Randomizes probe points slightly to reduce bed wear. Not needed for the Neptune 3 Max’s probe.
     - `probe_dock_enable: False` – Leave as `False` (Neptune 3 Max’s probe is fixed, not detachable).
     - `purge_height: 0.8` – Height for the purge line. Adjust if the line is too thick/thin.
     - `purge_margin: 10` – Space between the purge line and print. Increase if the purge interferes with brims.
     - `purge_amount: 30` – Amount of filament purged. Adjust for filament changes (e.g., `50` for color swaps).
     - `flow_rate: 12` – Purge speed. Suitable for the Neptune 3 Max’s standard hotend.
     - `smart_park_height: 10` – Height for smart parking. Adjust if the head is too high/low during pauses.
   - Save changes (`Ctrl+O`, `Enter`, `Ctrl+X`) and restart Klipper:
     ```bash
     sudo systemctl restart klipper
     ```

### 10. **Calibrate Your Printer**
   - For best results:
     - **Z-Offset**: Run `MANUAL_Z_OFFSET_ADJUST` in the web interface’s terminal to set the nozzle-to-bed distance.
     - **Bed Leveling**: Run `SCREWS_TILT_CALCULATE` to check bed levelness and adjust screws if needed.
     - **PID Tuning**: Run `BED_PID_TUNE TEMP=60` and `NOZZLE_PID_TUNE TEMP=200` to optimize heating.
     - These macros are included in your `printer.cfg`.

### 11. **Troubleshooting**
   - **PuTTY Connection Fails**:
     - Verify the IP address and ensure the Pad 7 is on Wi-Fi.
     - Restart the Pad 7 or router.
   - **KAMP Features Don’t Work**:
     - Check that all config files (`printer.cfg`, `KAMP_Settings.cfg`, etc.) are in `~/printer_data/config/`.
     - Ensure `[include]` lines and `[exclude_object]` are in `printer.cfg`.
     - Verify `[file_manager]` has `enable_object_processing: True` in `moonraker.conf`.
   - **Printer Not Responding**:
     - Check the serial port in `printer.cfg` (Step 6).
     - Run `dmesg | grep tty` to debug connection issues.
     - Ensure the USB cable between the Pad 7 and printer is secure (RX to TX, TX to RX).
   - **No Purge Line**:
     - Confirm `[extruder]` has `max_extrude_cross_section: 10` and `[firmware_retraction]` is defined.
     - Ensure your slicer has “Label Objects” enabled.
   - **Help Resources**:
     - Config files: [HDR-Performance/Neptune-3-Max-bigtreetech-pad-7](https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-/tree/main)
     - KAMP guide: [Klipper-Adaptive-Meshing-Purging](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging)
     - Klipper docs: [klipper3d.org](https://www.klipper3d.org/)
     - Community: r/ElegooNeptune3, r/klipper

## Notes
- The configuration files from the GitHub repository are optimized for the Neptune 3 Max’s 420x420mm bed and fixed probe.
- The stock Neptune 3 Max screen may not work with Klipper; use the Pad 7’s touchscreen or web interface.
- Always back up your original `printer.cfg` and `moonraker.conf` before overwriting.
- Ensure your slicer labels objects for adaptive meshing and purging to work.
- KAMP’s adaptive features make printing faster and more precise—enjoy your upgraded Neptune 3 Max!