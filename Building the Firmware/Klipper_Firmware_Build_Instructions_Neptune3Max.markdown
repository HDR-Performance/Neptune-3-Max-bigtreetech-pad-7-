# Building Klipper Firmware for Elegoo Neptune 3 Max with BIGTREETECH Pad 7 using PuTTY

This guide provides step-by-step instructions to build and flash Klipper firmware for the Elegoo Neptune 3 Max equipped with a ZNP Robin Nano v2.2 board (STM32F401 processor) using a BIGTREETECH Pad 7 and PuTTY for SSH access. The firmware output is renamed to `ZNP_ROBIN_NANO.bin` before transferring to an SD card, as required by the ZNP Robin Nano board. The `printer.cfg` provided by the user is used as the default configuration, and additional configuration files are sourced from the GitHub repository: [HDR-Performance/Neptune-3-Max-bigtreetech-pad-7](https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-/tree/main).

## Prerequisites
- **BIGTREETECH Pad 7**: Powered on, connected to Wi-Fi, running a Klipper-compatible OS (e.g., MainsailOS or Fluidd).
- **PuTTY**: Installed on your computer for SSH access.
- **WinSCP**: Optional, for easier file transfers.
- **MicroSD Card**: FAT32-formatted, for flashing firmware to the printer.
- **Elegoo Neptune 3 Max**: With ZNP Robin Nano v2.2 board (STM32F401).
- **Computer**: For SSH access and file management.
- **Wi-Fi Router Admin Access**: To find the Pad 7’s IP address or set a static IP.
- **UART Wiring**: If using J17 (Wi-Fi section), ensure correct RX/TX wiring for USART2 (PA3/PA2).
- **Configuration Files**: Download all necessary files (`printer.cfg`, `KAMP_Settings.cfg`, `timelapse.cfg`, `mainsail.cfg`, `Line_Purge.cfg`, `Smart_Park.cfg`, `Adaptive_Meshing.cfg`) from [HDR-Performance/Neptune-3-Max-bigtreetech-pad-7](https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-/tree/main).

## Step-by-Step Instructions

### 1. **Set Up PuTTY and Connect to the Pad 7**
   - **Find the Pad 7’s IP Address**:
     - Access your Wi-Fi router’s admin panel to locate the IP address assigned to the BIGTREETECH Pad 7 (e.g., `192.168.54.42`). Setting a static IP is recommended.
   - **Open PuTTY**:
     - Download and install PuTTY from [putty.org](https://www.putty.org/) if not already installed.
     - Launch PuTTY, enter the Pad 7’s IP address in the "Host Name (or IP address)" field, set port to `22` (SSH), and click "Open".
   - **Log In**:
     - Username: `biqu`
     - Password: `biqu`
     - Verify the IP and network connection if login fails.

### 2. **Verify the ZNP Robin Nano Board Version**
   - Confirm the printer’s motherboard is ZNP Robin Nano v2.2 (STM32F401). Check the label on the board or refer to the Neptune 3 Max documentation.
   - These instructions are tailored for v2.2 with STM32F401. For other versions (e.g., v1.2/v1.3 with STM32F103), adjust settings accordingly.

### 3. **Install Klipper on the Pad 7 (if not pre-installed)**
   - Check if Klipper is installed:
     ```bash
     ls ~/klipper
     ```
     If the directory exists, skip to Step 4.
   - Install Klipper via SSH:
     ```bash
     cd ~
     git clone https://github.com/Klipper3d/klipper
     ```

### 4. **Configure Klipper for the ZNP Robin Nano v2.2 Board**
   - Navigate to the Klipper directory:
     ```bash
     cd ~/klipper
     ```
   - Start the configuration tool:
     ```bash
     make menuconfig
     ```
   - Set the following for ZNP Robin Nano v2.2:
     - Micro-controller Architecture: `STM32`
     - Processor model: `STM32F401`
     - Flash size: `256KiB`
     - Bootloader offset: `32KiB bootloader`
     - Communication interface: `Serial (on USART1 PA10/PA9)` (or `Serial (on USART2 PA3/PA2)` if using J17 Wi-Fi section pins)
     - Baud rate: `250000`
   - Press `Q` to exit, then `Y` to save.
   - Note: The `make flash` command is not supported for ZNP Robin boards; manual copying is required.

### 5. **Compile the Klipper Firmware**
   - Compile the firmware:
     ```bash
     make
     ```
   - The compiled firmware will be in `~/klipper/out/klipper.bin`.

### 6. **Rename the Firmware to ZNP_ROBIN_NANO.bin**
   - Rename the firmware to match the ZNP Robin Nano board’s requirement:
     ```bash
     cp ~/klipper/out/klipper.bin ~/klipper/out/ZNP_ROBIN_NANO.bin
     ```
   - Verify the file exists:
     ```bash
     ls ~/klipper/out/
     ```
     You should see `ZNP_ROBIN_NANO.bin`.

### 7. **Transfer the Firmware to an SD Card**
   - **Using WinSCP (Recommended)**:
     - Install WinSCP from [winscp.net](https://winscp.net/) if needed.
     - Connect to the Pad 7:
       - Hostname: Pad 7’s IP address
       - Username: `biqu`
       - Password: `biqu`
       - Protocol: SCP or SFTP
     - Navigate to `/home/biqu/klipper/out/`.
     - Download `ZNP_ROBIN_NANO.bin` to your computer.
     - Insert a FAT32-formatted MicroSD card into your computer and copy `ZNP_ROBIN_NANO.bin` to its root.
   - **Using PuTTY and a USB Drive**:
     - Mount a USB drive connected to the Pad 7:
       ```bash
       sudo mkdir /mnt/usb
       sudo mount /dev/sda1 /mnt/usb
       ```
       (Replace `/dev/sda1` with the correct device; check with `lsblk`.)
     - Copy the firmware:
       ```bash
       cp ~/klipper/out/ZNP_ROBIN_NANO.bin /mnt/usb/ZNP_ROBIN_NANO.bin
       ```
     - Unmount the USB:
       ```bash
       sudo umount /mnt/usb
       ```
     - Transfer `ZNP_ROBIN_NANO.bin` from the USB to a FAT32-formatted MicroSD card via your computer.
   - Safely eject the MicroSD card.

### 8. **Flash the Firmware to the Neptune 3 Max**
   - Power off the Neptune 3 Max.
   - Insert the MicroSD card with `ZNP_ROBIN_NANO.bin` into the printer’s SD card slot.
   - Power on the printer. The firmware will update automatically, and the screen may show progress.
   - After the update, the printer should restart. Remove the SD card to prevent re-flashing.

### 9. **Configure the Printer with a `printer.cfg` File**
   - Access the Klipper configuration directory:
     ```bash
     cd ~/printer_data/config
     ```
   - Upload the configuration files from your GitHub repository:
     - Download `printer.cfg`, `KAMP_Settings.cfg`, `timelapse.cfg`, `mainsail.cfg`, `Line_Purge.cfg`, `Smart_Park.cfg`, and `Adaptive_Meshing.cfg` from [HDR-Performance/Neptune-3-Max-bigtreetech-pad-7](https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-/tree/main).
     - Use WinSCP to transfer these files to `/home/biqu/printer_data/config/` on the Pad 7:
       - Connect using the same credentials as PuTTY.
       - Drag and drop the files to the `config` directory.
     - Alternatively, use `wget` to download directly via PuTTY:
       ```bash
       cd ~/printer_data/config
       wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/printer.cfg
       wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/KAMP_Settings.cfg
       wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/timelapse.cfg
       wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/mainsail.cfg
       wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/Line_Purge.cfg
       wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/Smart_Park.cfg
       wget https://raw.githubusercontent.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7/main/Adaptive_Meshing.cfg
       ```
   - Verify the files are present:
     ```bash
     ls ~/printer_data/config
     ```
   - Edit `printer.cfg` to ensure the `[mcu]` section has the correct serial port (see Step 10):
     ```bash
     nano printer.cfg
     ```
   - The `printer.cfg` from your repository is used as the default and includes:
     ```ini
     # Klipper configuration for Elegoo Neptune 3 Max with ZNP Robin Nano v2.2
     # Sourced from https://github.com/HDR-Performance/Neptune-3-Max-bigtreetech-pad-7-/tree/main

     [include KAMP_Settings.cfg]
     [include timelapse.cfg]
     [include mainsail.cfg]
     [include Line_Purge.cfg]
     [include Smart_Park.cfg]
     [include Adaptive_Meshing.cfg]

     [exclude_object] # Comment out for Sonic Pad

     [virtual_sdcard]
     #path: ~/gcode_files

     [pause_resume]

     [display_status]

     [gcode_macro START_PRINT]
     gcode:
        SET_LED LED=LED_Light WHITE=0.25 SYNC=0 TRANSMIT=1 ; 25 Percent Led Light
        {% set BED_TEMP = params.BED_TEMP|default(60)|float %}
        {% set EXTRUDER_TEMP = params.EXTRUDER_TEMP|default(200)|float %}
        M140 S{BED_TEMP}
        M104 S{EXTRUDER_TEMP}
        G28
        M190 S{BED_TEMP}
        M109 S{EXTRUDER_TEMP}
        SETUP_KAMP_MESHING DISPLAY_PARAMETERS=1 LED_ENABLE=1 FUZZ_ENABLE=0 ADAPTIVE_ENABLE=1 ; Setup KAMP
        BED_MESH_CALIBRATE ADAPTIVE=1 ; Calibrate bed mesh
        Smart_Park
        LINE_PURGE

     [gcode_macro CANCEL_PRINT]
     description: Cancels the print, parks the toolhead like PAUSE, and shuts down like END_PRINT
     gcode:
         M117 Cancelling print
         {% set x_park = printer.toolhead.axis_maximum.x|float - 5.0 %}
         {% set y_park = printer.toolhead.axis_maximum.y|float - 5.0 %}
         {% set max_z = printer.toolhead.axis_maximum.z|float %}
         {% set act_z = printer.toolhead.position.z|float %}
         {% if act_z < (max_z - 2.0) %}
             {% set z_safe = 2.0 %}
         {% else %}
             {% set z_safe = max_z - act_z %}
         {% endif %}
         G91  ; Relative positioning
         {% if "xyz" in printer.toolhead.homed_axes %}
             G1 Z{z_safe} F900  ; Lift Z like PAUSE
             G90  ; Absolute positioning
             G1 X{x_park} Y{y_park} F6000  ; Move to park position like PAUSE
         {% else %}
             {action_respond_info("Printer not homed, skipping park")}
         {% endif %}
         M104 S0  ; Turn off extruder heater
         M140 S0  ; Turn off bed heater
         M106 S0  ; Turn off fan and reset override
         SET_GCODE_VARIABLE MACRO=SET_FAN_SPEED VARIABLE=fan_override VALUE="False"  ; Disable fan override
         SET_LED LED=LED_Light WHITE=0.0 SYNC=0 TRANSMIT=1  ; Turn off LED
         M84      ; Disable motors
         BED_MESH_CLEAR  ; Clear adaptive mesh
         CLEAR_PAUSE     ; Clear any pause state
         SDCARD_RESET_FILE  ; Reset SD print file (if using SD)
         M117 Print cancelled

     [gcode_macro PAUSE]
     description: Pause the actual running print
     rename_existing: PAUSE_BASE
     variable_extrude: 1.0
     gcode:
         {% set E = printer["gcode_macro PAUSE"].extrude|float %}
         {% set x_park = printer.toolhead.axis_maximum.x|float - 5.0 %}
         {% set y_park = printer.toolhead.axis_maximum.y|float - 5.0 %}
         {% set max_z = printer.toolhead.axis_maximum.z|float %}
         {% set act_z = printer.toolhead.position.z|float %}
         {% if act_z < (max_z - 2.0) %}
             {% set z_safe = 2.0 %}
         {% else %}
             {% set z_safe = max_z - act_z %}
         {% endif %}
         PAUSE_BASE
         G91
         {% if printer.extruder.can_extrude|lower == 'true' %}
           G1 E-{E} F2100
         {% else %}
           {action_respond_info("Extruder not hot enough")}
         {% endif %}
         {% if "xyz" in printer.toolhead.homed_axes %}
           G1 Z{z_safe} F900
           G90
           G1 X{x_park} Y{y_park} F6000
         {% else %}
           {action_respond_info("Printer not homed")}
         {% endif %}

     [gcode_macro RESUME]
     description: Resume the actual running print
     rename_existing: RESUME_BASE
     gcode:
         {% set E = printer["gcode_macro PAUSE"].extrude|float %}
         {% if 'VELOCITY' in params|upper %}
           {% set get_params = ('VELOCITY=' + params.VELOCITY) %}
         {% else %}
           {% set get_params = "" %}
         {% endif %}
         {% if printer.extruder.can_extrude|lower == 'true' %}
           G91
           G1 E{E} F2100
         {% else %}
           {action_respond_info("Extruder not hot enough")}
         {% endif %}
         RESUME_BASE {get_params}

     [gcode_macro END_PRINT]
     gcode:
        M104 S0 ; Turn off extruder heater
        M140 S0 ; Turn off bed heater
        M106 S0 ; Turn off fan and reset override
        SET_GCODE_VARIABLE MACRO=SET_FAN_SPEED VARIABLE=fan_override VALUE="False"  ; Disable fan override
        G91 ; Relative positioning
        G1 Z10 F300 ; Move Z axis up 10mm to avoid collision
        G90 ; Absolute positioning
        G1 Y200 F3000 ; Move Y axis forward to deliver the print
        SET_LED LED=LED_Light WHITE=0.0 SYNC=0 TRANSMIT=1
        M84 ; Disable motors

     [homing_override]
     gcode:
       G28 X Y                    ; Home X and Y axes to endstops
       G90                        ; Absolute positioning
       G0 X241 Y193 F6000         ; Move to safe Z homing position at 100 mm/s
       G28 Z                      ; Home Z axis with probe
       G91                        ; Relative positioning
       G0 Z10 F900                ; Z-hop 10 mm after homing
       G90                        ; Back to absolute positioning
     set_position_z: 0            ; Reset Z position to 0 after homing

     [gcode_macro MANUAL_BED_TRAMMING]
     description: Home and measure for manual leveling adjustments
     gcode:
       G28
       SCREWS_TILT_CALCULATE
       G90
       G1 F3000 X213 Y213 Z25

     [gcode_macro MANUAL_Z_OFFSET_ADJUST]
     description: Manually adjust Z offset by moving to current offset and allowing incremental changes
     gcode:
         {% set current_z_offset = printer.configfile.config.probe.z_offset | float | default(0.0) %}
         {% set center_x = printer.toolhead.axis_maximum.x / 2 | float %}
         {% set center_y = printer.toolhead.axis_maximum.y / 2 | float %}
         {% set travel_speed = printer.toolhead.max_velocity * 60 | float %}
         {action_respond_info("Current Z offset: %.3f mm" % current_z_offset)}
         G28
         {action_respond_info("Printer homed.")}
         G90
         G0 X{center_x} Y{center_y} Z10 F{travel_speed}
         G0 Z{current_z_offset} F900
         {action_respond_info("Nozzle is at current Z offset (%.3f mm)." % current_z_offset)}
         {action_respond_info("Use G0 Z+0.1 or G0 Z-0.1 to adjust (e.g., 0.1 mm steps).")}
         {action_respond_info("Use G0 Z+0.01 or G0 Z-0.01 for finer adjustments.")}
         {action_respond_info("When satisfied, note the final Z position and run SAVE_Z_OFFSET Z=<value>.")}

     [mcu]
     serial: /dev/ttyUSB0
     restart_method: command

     [led LED_Light]
     white_pin: PB9
     initial_white: 1.0

     [printer]
     kinematics: cartesian
     max_velocity: 400
     max_accel: 3000
     max_z_velocity: 25
     max_z_accel: 200
     square_corner_velocity: 8

     [stepper_x]
     step_pin: !PC12
     dir_pin: PB3
     enable_pin: !PD2
     microsteps: 16
     rotation_distance: 39.965
     endstop_pin: PA13
     position_endstop: 0
     position_min: 0
     position_max: 430
     homing_speed: 50

     [stepper_y]
     step_pin: PC11
     dir_pin: PA15
     enable_pin: !PC10
     microsteps: 16
     rotation_distance: 39.88
     endstop_pin: PB8
     position_endstop: -6
     position_min: -6
     position_max: 430
     homing_speed: 50

     [stepper_z]
     step_pin: PC7
     dir_pin: !PC9
     enable_pin: !PC8
     rotation_distance: 8.0
     microsteps: 16
     position_min: -4.0
     position_max: 506
     endstop_pin: probe:z_virtual_endstop
     homing_speed: 10
     homing_retract_speed: 15

     [extruder]
     max_extrude_only_distance: 100.0
     max_extrude_cross_section: 10
     step_pin: PB10
     dir_pin: PB1
     enable_pin: !PC6
     microsteps: 16
     nozzle_diameter: 0.600
     filament_diameter: 1.750
     heater_pin: PA6
     sensor_type: EPCOS 100K B57560G104F
     sensor_pin: PC1
     min_temp: 0
     max_temp: 290
     rotation_distance: 7.65
     pressure_advance: 0.12

     [verify_heater extruder]
     hysteresis: 5
     max_error: 120
     check_gain_time: 25

     [firmware_retraction]
     retract_length: 0.5
     retract_speed: 35
     unretract_extra_length: 0
     unretract_speed: 35

     [heater_bed]
     heater_pin: PA5
     sensor_type: EPCOS 100K B57560G104F
     sensor_pin: PC0
     pwm_cycle_time: 0.020
     max_temp: 110
     min_temp: 0
     control: pid
     pid_kp: 71.562
     pid_ki: 0.778
     pid_kd: 1645.031

     [heater_fan hotend_fan]
     pin: PB0
     heater: extruder
     heater_temp: 50.0

     [fan]
     pin: PA7

     [gcode_macro SET_FAN_SPEED]
     description: Sets a manual fan speed with optional override persistence
     variable_fan_speed: 0
     variable_fan_override: False
     gcode:
         {% set SPEED = params.SPEED|default(100)|float %}
         {% set OVERRIDE = params.OVERRIDE|default(0)|int %}
         {% if SPEED >= 0 and SPEED <= 100 %}
             {% set FAN_SPEED = (SPEED / 100.0) * 255|int %}
             M106 S{FAN_SPEED}
             SET_GCODE_VARIABLE MACRO=SET_FAN_SPEED VARIABLE=fan_speed VALUE={FAN_SPEED}
             SET_GCODE_VARIABLE MACRO=SET_FAN_SPEED VARIABLE=fan_override VALUE={OVERRIDE > 0}
             M117 Fan speed set to {SPEED}% {", override " + ("enabled" if OVERRIDE > 0 else "disabled")}
         {% else %}
             M117 Invalid speed! Use 0-100.
         {% endif %}

     [gcode_macro UPDATE_FAN_SPEED]
     description: Internal macro to enforce fan speed override during print
     gcode:
         {% if printer['gcode_macro SET_FAN_SPEED'].fan_override %}
             M106 S{printer['gcode_macro SET_FAN_SPEED'].fan_speed}
         {% endif %}

     [force_move]
     enable_force_move: True

     [probe]
     pin: ^PA8
     speed: 15
     lift_speed: 40
     samples: 3
     x_offset: -28.5
     y_offset: 22

     [filament_switch_sensor filament_sensor]
     pause_on_runout: true
     switch_pin: PB4

     [bed_mesh]
     probe_count: 11,9
     algorithm: bicubic
     speed: 500
     horizontal_move_z: 10
     mesh_min: 33, 16
     mesh_max: 397, 415
     fade_start: 1.0
     fade_end: 10.0

     [input_shaper]
     #shaper_type_x: ei
     #shaper_freq_x: 62.0
     #shaper_type_y: zv
     #shaper_freq_y: 31.6

     [temperature_sensor raspberry_pi]
     sensor_type: temperature_host

     [temperature_sensor mcu_temp]
     sensor_type: temperature_mcu
     sensor_temperature1: 25
     sensor_adc1: 0.210317

     [gcode_macro M420]
     description: Load the current mesh
     gcode:
       BED_MESH_PROFILE LOAD=default

     [gcode_macro G29]
     description: Creates automated homing and bed mesh
     gcode:
       G28
       BED_MESH_CALIBRATE
       DATA_SAVE

     [screws_tilt_adjust]
     screw_thread: CW-M3
     speed: 200
     screw1: 243.5, 193
     screw1_name: center
     screw2: 421, 370.5
     screw2_name: right back screw
     screw3: 421, 193
     screw3_name: right middle screw
     screw4: 421, 15.5
     screw4_name: right front screw
     screw5: 66, 15.5
     screw5_name: left front screw
     screw6: 66, 193
     screw6_name: left middle screw
     screw7: 66, 370.5
     screw7_name: left back screw

     [mcu CB1]
     serial: /tmp/klipper_host_mcu

     [adxl345]
     cs_pin: CB1:None
     spi_bus: spidev1.1
     axes_map: z,y,-x

     [resonance_tester]
     accel_chip: adxl345
     probe_points:
         100, 100, 20

     [gcode_macro BED_PID_TUNE]
     description: Run PID tuning for the heated bed at a specified temperature
     gcode:
         {% set TEMP = params.TEMP|default(60)|float %}
         {% if TEMP >= 0 and TEMP <= 110 %}
             M117 Running Bed PID Tuning at {TEMP}°C
             G28
             PID_CALIBRATE HEATER=heater_bed TARGET={TEMP}
             M117 Bed PID Tuning Complete! Check terminal for values.
             SAVE_CONFIG
         {% else %}
             M117 Error: TEMP must be between 0 and 110°C
             {action_respond_info("Invalid temperature! Use TEMP between 0 and 110°C.")}
         {% endif %}

     [gcode_macro NOZZLE_PID_TUNE]
     description: Run PID tuning for the extruder at a specified temperature and fan speed
     gcode:
         {% set TEMP = params.TEMP|default(200)|float %}
         {% set FAN_SPEED = params.FAN_SPEED|default(0)|float %}
         {% if TEMP >= 0 and TEMP <= 290 %}
             {% if FAN_SPEED >= 0 and FAN_SPEED <= 100 %}
                 M117 Running Nozzle PID Tuning at {TEMP}°C with fan at {FAN_SPEED}%
                 G28
                 SET_HEATER_FAN_SPEED HEATER_FAN=hotend_fan VALUE=0
                 SET_FAN_SPEED FAN=fan SPEED={FAN_SPEED}
                 PID_CALIBRATE HEATER=extruder TARGET={TEMP}
                 M117 Nozzle PID Tuning Complete! Check terminal for values.
                 SET_FAN_SPEED FAN=fan SPEED=0
                 SET_HEATER_FAN_SPEED HEATER_FAN=hotend_fan VALUE=255
                 SAVE_CONFIG
             {% else %}
                 M117 Error: FAN_SPEED must be between 0 and 100%
                 {action_respond_info("Invalid fan speed! Use FAN_SPEED between 0 and 100%.")}
             {% endif %}
         {% else %}
             M117 Error: TEMP must be between 0 and 290°C
             {action_respond_info("Invalid temperature! Use TEMP between 0 and 290°C.")}
         {% endif %}

     # Auto-generated SAVE_CONFIG section
     [probe]
     z_offset = 1.470

     [input_shaper]
     shaper_type_y = ei
     shaper_freq_y = 38.0
     shaper_type_x = 3hump_ei
     shaper_freq_x = 68.4

     [bed_mesh default]
     version = 1
     points =
         0.122500, 0.163333, 0.131667
         0.158333, 0.161667, 0.126667
         0.138333, 0.155833, 0.120833
     x_count = 3
     y_count = 3
     mesh_x_pps = 2
     mesh_y_pps = 2
     algo = lagrange
     tension = 0.2
     min_x = 187.954
     max_x = 227.014
     min_y = 182.458
     max_y = 236.098

     [extruder]
     control = pid
     pid_kp = 30.437
     pid_ki = 2.670
     pid_kd = 86.746
     ```
   - Notes:
     - The `[mcu]` section uses `/dev/ttyUSB0`. Update to the correct serial port (e.g., `/dev/serial/by-id/...`) in Step 10 if using USART1 or USART2 (J17).
     - The bed size (`position_max: 430`, `mesh_min: 33, 16`, `mesh_max: 397, 415`) is set for the Neptune 3 Max (420x420mm build area). Verify these match your printer.
     - Calibrate PID, pressure advance, and Z-offset as needed (see comments in the config).
     - Ensure all included files (`KAMP_Settings.cfg`, etc.) are in `~/printer_data/config/`.

### 10. **Verify the Serial Port**
   - List available serial ports:
     ```bash
     ls /dev/ttyS* /dev/ttyUSB* /dev/ttyACM* /dev/serial/by-id/*
     ```
   - Identify the printer’s serial port (e.g., `/dev/serial/by-id/usb-1a86_USB_Serial-if00-port0` for USART1, or another for USART2 if using J17).
   - Edit `printer.cfg` to update the `[mcu]` section:
     ```bash
     nano ~/printer_data/config/printer.cfg
     ```
     Set `serial` to the correct port, e.g.:
     ```ini
     [mcu]
     serial: /dev/serial/by-id/usb-1a86_USB_Serial-if00-port0
     restart_method: command
     ```
   - Save (`Ctrl+O`, `Enter`, `Ctrl+X`).
   - If no port is found, verify UART wiring (RX to TX, TX to RX) between the Pad 7 and the ZNP Robin Nano board (PA10/PA9 for USART1 or PA3/PA2 for J17).

### 11. **Restart Klipper and Test the Printer**
   - Restart Klipper:
     ```bash
     sudo systemctl restart klipper
     ```
   - Access the web interface (Mainsail/Fluidd) via the Pad 7’s IP (e.g., `http://192.168.54.42`).
   - Test functionality:
     - Home the printer (“Machine” section, click “Homing”).
     - Verify extruder and bed temperatures using `M104` and `M140`.
     - Run `BED_MESH_CALIBRATE` or `MANUAL_Z_OFFSET_ADJUST` to calibrate the bed and Z-offset.
     - Test `START_PRINT`, `PAUSE`, `RESUME`, and `CANCEL_PRINT` macros.
     - Verify KAMP features (e.g., `Smart_Park`, `LINE_PURGE`) work as expected.

### 12. **Troubleshooting**
   - **Firmware Update Fails**:
     - Ensure the SD card is FAT32-formatted and the file is named `ZNP_ROBIN_NANO.bin`.
     - Verify `make menuconfig` settings (STM32F401, 32KiB bootloader, correct USART).
   - **Klipper Errors**:
     - If included files are missing, ensure all files from the GitHub repository are in `~/printer_data/config/`.
     - Check `printer.cfg` for correct serial port and pin assignments.
   - **Serial Connection Issues**:
     - Run `dmesg | grep tty` to diagnose port issues.
     - Verify UART wiring (USART1: PA10/PA9 or USART2: PA3/PA2 for J17).
   - **Display Issues**:
     - The stock Neptune 3 Max screen may not work with Klipper. Use the Pad 7’s touchscreen or web interface.
   - **Resources**:
     - GitHub repository: [HDR-Performance/Neptune-3-Max-bigtreetech-pad- Hawkins]
     - Klipper documentation: [klipper3d.org](https://www.klipper3d.org/) or `~/klipper/docs/`
     - BIGTREETECH docs: [bigtreetech/docs](https://github.com/bigtreetech/docs)

## Notes
- The `ZNP_ROBIN_NANO.bin` filename is critical for the ZNP Robin Nano board.
- Do not use `make flash` for ZNP Robin boards.
- Remove the SD card after flashing to prevent re-flashing.
- For input shaping, use the `[adxl345]` and `[resonance_tester]` sections and calibrate per Klipper’s documentation.
- Back up the original firmware from [elegoo.com](https://www.elegoo.com) before flashing.
- The configuration files from your repository are optimized for the Neptune 3 Max. Ensure all calibrations (PID, Z-offset, input shaper) are performed for optimal performance.