# Neptune 3 Max Klipper Firmware & Image Installation Guide

## Overview
This guide provides a streamlined process for setting up **Klipper** on the **Neptune 3 Max** using a **BigTreeTech Pad7** running a **CB1 Board**. Follow the steps below to get your printer operational as quickly as possible.

📺 **[Video Demo](#)** | 📥 **[Download Firmware & Image](#)**

---

## Requirements
- **BigTreeTech Pad7** with **CB1 Board**
- **32GiB microSD card**
- Raspberry Pi Imager
- Prebuilt image and firmware files from **"BTT Pad7 Neptune 3 Max IMG"** folder

---

## Flashing the Image to BigTreeTech Pad7
This process installs the custom Klipper environment onto your **BigTreeTech Pad7**.

### Steps:
1. **Download and Extract Files**  
   - Locate the **"BTT Pad7 Neptune 3 Max IMG"** folder.
   - Inside, you'll find the **prebuilt image file**.
   
2. **Flash the Image to the SD Card**  
   - Insert a **32GiB microSD card** into your computer.
   - Open **Raspberry Pi Imager**.
   - Click **"Choose OS"** → **"Use Custom"** and select the extracted **image file**.
   - Click **"Choose Storage"** and select your microSD card.
   - Click **"Write"** and wait for the process to complete.
   
3. **Insert the SD Card into the BigTreeTech Pad7**  
   - Power off the Pad7.
   - Insert the flashed SD card into the slot.
   - Power on the Pad7 and allow it to boot.

---

## Flashing Custom Firmware to Neptune 3 Max
Once the Pad7 is set up, you need to flash the **custom firmware** to your Neptune 3 Max.

### Steps:
1. **Locate the Firmware File**  
   - In the **"BTT Pad7 Neptune 3 Max IMG/Neptune 3 Max Klipper Matched Firmware"** folder, find the firmware file for your printer.

2. **Prepare the SD Card**  
   - Use a separate **microSD card** (not the Pad7 image card).
   - Format it as **FAT32**.
   - Copy the firmware file onto the SD card.

3. **Flash the Firmware to the Printer**  
   - Power off the **Neptune 3 Max**.
   - Insert the SD card into the printer’s mainboard slot.
   - Power on the printer and wait for the firmware to install (LEDs may flash, and the screen may stay blank temporarily).
   - Once complete, remove the SD card and restart the printer.

---

## Features & Functionality
Once installed, your Neptune 3 Max will include:
- **Smart Meshing**: Auto mesh generation based on print size.
- **Smart Purge & Park**: Adaptive purge and parking.
- **Manual Bed Tramming**: Assisted bed leveling.
- **Input Shaper Sensor Support**: Integrated with Pad7.
- **Vertical Screen Orientation Flip**: Optimized for display.

---

## Start & End G-codes
### Start G-code for Cura:
```
M117 Initializing print

SET_LED LED=LED_Light WHITE=0.90 SYNC=0 TRANSMIT=1

M140 S{material_bed_temperature}
M104 S{material_print_temperature_layer_0}

G28
G90
G92 E0

M190 S{material_bed_temperature}
M109 S{material_print_temperature_layer_0}

SETUP_KAMP_MESHING DISPLAY_PARAMETERS=1 LED_ENABLE=1 FUZZ_ENABLE=0 ADAPTIVE_ENABLE=1
BED_MESH_CALIBRATE ADAPTIVE=1

G0 X50 Y50 Z10 F12000
SMART_PARK
LINE_PURGE

G90
G92 E0
G0 X210 Y210 Z0.2 F12000
M117 Printing...
```

### End G-code for Cura:
```
M117 Finishing print

M104 S0
M140 S0

G91
G1 Z10 F600
G90
G0 X0 Y313 F12000

M84
BED_MESH_CLEAR

SET_LED LED=LED_Light WHITE=0 SYNC=0 TRANSMIT=1

M117 Print complete!
```

### Start G-code for Orca Slicer:
```
M117 Initializing print

SET_LED LED=LED_Light WHITE=0.90 SYNC=0 TRANSMIT=1

M140 S[first_layer_bed_temperature]
M104 S[first_layer_temperature]

G28
G90
G92 E0

M190 S[first_layer_bed_temperature]
M109 S[first_layer_temperature]

SETUP_KAMP_MESHING DISPLAY_PARAMETERS=1 LED_ENABLE=1 FUZZ_ENABLE=0 ADAPTIVE_ENABLE=1
BED_MESH_CALIBRATE ADAPTIVE=1

G0 X50 Y50 Z10 F12000
SMART_PARK
LINE_PURGE

G90
G92 E0
G0 X210 Y210 Z0.2 F12000
M117 Printing...
```

### End G-code for Orca Slicer:
```
M117 Finishing print

M104 S0
M140 S0

G91
G1 Z10 F600
G90
G0 X0 Y313 F12000

M84
BED_MESH_CLEAR

SET_LED LED=LED_Light WHITE=0 SYNC=0 TRANSMIT=1

M117 Print complete!
```

Your printer is now ready with **Klipper** running on the **BigTreeTech Pad7**! 🚀 Happy printing! 🎨🖨

