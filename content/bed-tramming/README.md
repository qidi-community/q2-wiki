# Bed Tramming with Nylok Nuts

Stock bed leveling nuts loosen during operation, requiring frequent re-leveling. Replace with Nylok nuts and use `screws_tilt_adjust` for consistent bed tramming.

## Installation

### Hardware Replacement

Based on this model for Q1 Pro (which applies to Q2): https://odysee.com/@The_Mi3_Channel:f/Q1-Pro-Locking-Thumbwheels:4

> [!NOTE]
> For the Q2, you don't need to print the tensioning block and don't need the brass tubing. Instead of the tensioning block from this, use the one [provided by Qidi](https://drive.google.com/drive/folders/1KoiGkiaRWKcxMwc2mgmzVIZlkQ3vs_Pp) (the L shaped block).

Follow the assembly instructions included with the download PDF for mounting the Nylok nuts.

### Klipper Configuration

Add this to your printer.cfg:

```ini
[screws_tilt_adjust]
screw1:30,30
screw1_name: front left screw

screw2: 240,30
screw2_name: front right screw

screw3: 240,240
screw3_name: rear right screw

screw4: 30,240
screw4_name: rear left screw

horizontal_move_z: 10
speed: 50
screw_thread: CW-M4
```

Restart the printer after adding the configuration.

## Usage

Home your printer, then run:

```
SCREWS_TILT_CALCULATE
```

Adjust each screw as indicated and repeat until all values are green.

## Video Tutorial

This video has all the instruction you need:
https://www.youtube.com/watch?v=APAbl5PGEh0

This video by Qidi will show you how to get the correct tension on the nuts (first minute):
https://drive.google.com/file/d/1_EPVwa4XIsTpGCTIq4ZdUJT0OzW9BRJR/view
