# Resolving Moonraker "Configuration File Not Found" Error on BIGTREETECH Pad 7

This guide provides detailed steps to fix the `moonraker.confighelper.ConfigError: Configuration File Not Found` error on a BIGTREETECH Pad 7 running Klipper and Moonraker. This error occurs when Moonraker cannot locate its configuration file at `/home/biqu/printer_data/config/moonraker.conf`, often due to a missing, deleted, or corrupted file. By creating a new `moonraker.conf` file using PuTTY and a known working configuration, you can restore Moonraker functionality and access Mainsail or Fluidd.

## Prerequisites

- **BIGTREETECH Pad 7** with Klipper and Moonraker installed (e.g., Moonraker v0.9.3 or similar).
- **PuTTY** installed on a Windows, macOS, or Linux computer for SSH access.
- **IP address** of your Pad 7 (find it by running `ip addr` on the device or checking your router’s DHCP list).
- **SSH credentials**: Default username is `biqu`, password is typically `biqu` (unless changed).
- **Basic terminal knowledge** for executing commands and editing files.
- **Internet connection** on the Pad 7 to verify connectivity.
- **Backup** of any existing `printer_data` configs (recommended):
  ```bash
  cp -r /home/biqu/printer_data ~/backup
  ```

## Step-by-Step Instructions

### 1. Connect to the Pad 7 via PuTTY

1. Download and install PuTTY from [www.putty.org](https://www.putty.org/) if not already installed.
2. Open PuTTY and configure the session:
   - In the **Host Name (or IP address)** field, enter the Pad 7’s IP address (e.g., `192.168.1.100`).
   - Set **Port** to `22`.
   - Select **Connection type: SSH**.
3. Click **Open** to start the SSH session.
4. Log in with:
   - Username: `biqu`
   - Password: `biqu` (or your custom password).
   - If login fails, verify credentials or reset them via the Pad 7’s documentation.

### 2. Verify and Create the Configuration Directory

1. Check if the configuration directory exists:
   ```bash
   ls -l /home/biqu/printer_data/config
   ```
2. If the directory is missing, create it:
   ```bash
   mkdir -p /home/biqu/printer_data/config
   ```

### 3. Create the `moonraker.conf` File

1. Navigate to the configuration directory:
   ```bash
   cd /home/biqu/printer_data/config
   ```
2. Open a new `moonraker.conf` file using the `nano` text editor:
   ```bash
   nano moonraker.conf
   ```
3. Copy the following known working configuration and paste it into the `nano` editor:
   - Right-click in the PuTTY window to paste (or use `Shift+Insert` if right-click doesn’t work).
   - Use the configuration below, which supports Klipper, Mainsail, Crowsnest, Sonar, Timelapse, KlipperScreen, and Klipper-Adaptive-Meshing-Purging.

   ```ini
   [server]
   host: 0.0.0.0
   port: 7125
   klippy_uds_address: /home/biqu/printer_data/comms/klippy.sock

   [authorization]
   trusted_clients:
       10.0.0.0/8
       127.0.0.0/8
       169.254.0.0/16
       172.16.0.0/12
       192.168.0.0/16
       FE80::/10
       ::1/128
   cors_domains:
       *.lan
       *.local
       *://localhost
       *://localhost:*
       *://my.mainsail.xyz
       *://app.fluidd.xyz

   [octoprint_compat]

   [history]

   [file_manager]
   enable_object_processing: True

   [update_manager]
   channel: dev
   refresh_interval: 168

   [update_manager mainsail-config]
   type: git_repo
   primary_branch: master
   path: ~/mainsail-config
   origin: https://github.com/mainsail-crew/mainsail-config.git
   managed_services: klipper

   [update_manager mainsail]
   type: web
   channel: stable
   repo: mainsail-crew/mainsail
   path: ~/mainsail

   [update_manager crowsnest]
   type: git_repo
   path: ~/crowsnest
   origin: https://github.com/mainsail-crew/crowsnest.git
   install_script: tools/install.sh

   [update_manager sonar]
   type: git_repo
   path: ~/sonar
   origin: https://github.com/mainsail-crew/sonar.git
   primary_branch: main
   managed_services: sonar
   install_script: tools/install.sh

   [update_manager timelapse]
   type: git_repo
   primary_branch: main
   path: ~/moonraker-timelapse
   origin: https://github.com/mainsail-crew/moonraker-timelapse.git
   managed_services: klipper moonraker

   [update_manager KlipperScreen]
   type: git_repo
   path: ~/KlipperScreen
   origin: https://github.com/KlipperScreen/KlipperScreen.git
   env: ~/.KlipperScreen-env/bin/python
   requirements: scripts/KlipperScreen-requirements.txt
   install_script: scripts/KlipperScreen-install.sh
   managed_services: KlipperScreen

   [update_manager Klipper-Adaptive-Meshing-Purging]
   type: git_repo
   channel: dev
   path: ~/Klipper-Adaptive-Meshing-Purging
   origin: https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging.git
   managed_services: klipper
   primary_branch: main
   ```

4. Save the file:
   - Press `Ctrl+O`, then `Enter` to write the file.
   - Press `Ctrl+X` to exit `nano`.

### 4. Set File Permissions

1. Set the correct ownership and permissions for `moonraker.conf`:
   ```bash
   sudo chown biqu:biqu /home/biqu/printer_data/config/moonraker.conf
   sudo chmod 644 /home/biqu/printer_data/config/moonraker.conf
   ```

### 5. Restart Moonraker Service

1. Restart Moonraker to apply the new configuration:
   ```bash
   sudo systemctl restart moonraker
   ```
2. Verify the service is running:
   ```bash
   sudo systemctl status moonraker
   ```
   - Look for `Active: active (running)` in the output.
   - If the service fails, check the Moonraker log for errors:
   ```bash
   cat /home/biqu/printer_data/logs/moonraker.log
   ```

### 6. Test Moonraker Connectivity

1. Test Moonraker’s API to confirm it’s running:
   ```bash
   curl http://<Pad7_IP_address>:7125
   ```
   - Replace `<Pad7_IP_address>` with your Pad 7’s IP address (e.g., `192.168.1.100`).
   - A response like `"Welcome to Moonraker"` indicates success.
2. Open a web browser and navigate to `http://<Pad7_IP_address>` to access Mainsail or Fluidd.
   - If the frontend loads, Moonraker is working correctly.

### 7. Verify Klipper Integration

1. Ensure the Klipper socket exists, as specified in `[server]` (`klippy_uds_address`):
   ```bash
   ls -l /home/biqu/printer_data/comms/klippy.sock
   ```
2. If the socket is missing, restart Klipper:
   ```bash
   sudo systemctl restart klipper
   ```
3. Check Klipper’s status:
   ```bash
   sudo systemctl status klipper
   ```
4. If Klipper fails, inspect its log:
   ```bash
   cat /home/biqu/printer_data/logs/klippy.log
   ```

## Troubleshooting Tips

- **If Moonraker Still Fails**:
  - Double-check the `moonraker.conf` syntax for errors (e.g., incorrect indentation or missing colons).
  - Verify all paths in `[update_manager]` sections exist:
    ```bash
    ls -l ~/mainsail ~/crowsnest ~/sonar ~/moonraker-timelapse ~/KlipperScreen ~/Klipper-Adaptive-Meshing-Purging
    ```
  - If any paths are missing, reinstall the component using KIAUH:
    ```bash
    cd ~
    git clone https://github.com/th33xitus/kiauh.git
    ./kiauh/kiauh.sh
    ```
    - Use KIAUH to reinstall missing components (e.g., Mainsail, Crowsnest).
- **SD Card Corruption**:
  - If `moonraker.conf` disappears after a reboot, the SD card may be corrupted. Back up configs and re-flash the SD card with the latest CB1 image from [BIGTREETECH’s GitHub](https://github.com/bigtreetech/Pad7):
    ```bash
    cp -r /home/biqu/printer_data ~/backup
    ```
  - Use Raspberry Pi Imager to flash the new image, then restore configs.
- **Network Issues**:
  - If Mainsail/Fluidd doesn’t load, ensure your network subnet is included in `[authorization]`’s `trusted_clients` (e.g., `192.168.0.0/16`).
  - Verify the Pad 7’s IP address:
    ```bash
    ip addr
    ```
- **BIGTREETECH Pad 7 Specifics**:
  - Ensure the CS-Choose switch is set to CB1 (or CM4 if upgraded).
  - If using a CAN bus, verify settings in `klipper.conf`:
    ```bash
    dmesg | grep can
    ```
  - Update firmware if outdated, following BIGTREETECH’s instructions.

## Notes

- **Configuration Details**:
  - The provided `moonraker.conf` is tailored for a Klipper setup with Mainsail, Crowsnest, Sonar, Timelapse, KlipperScreen, and Klipper-Adaptive-M"],
Purging. It uses standard paths and settings for the Pad 7.
  - The `[authorization]` section allows broad network access for common home network subnets and supports Mainsail/Fluidd CORS domains.
  - The `[update_manager]` sections manage updates for various components, ensuring compatibility with the latest GitHub repositories.
- **Why This Happens**:
  - The error typically occurs due to a missing or deleted `moonraker.conf` file after updates, manual changes, or SD card issues.
- **Further Assistance**:
  - If issues persist, share the output of the Moonraker log (`/home/biqu/printer_data/logs/moonraker.log`) and Klipper log (`/home/biqu/printer_data/logs/klippy.log`) with your printer model (e.g., Ender-3, Voron) and any recent changes (e.g., CAN bus setup or updates) in your GitHub issue or community forum.

## License

This guide is provided under the [MIT License](https://opensource.org/licenses/MIT). Feel free to share and adapt it for your community.