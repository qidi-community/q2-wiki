# Installing KlipperScreen

Replace the stock display software with KlipperScreen for a native Klipper interface.

> [!IMPORTANT]
> This has been tested on the Q2 but not the QIDI Box. Going back to the stock software is simple - see "Reverting to Stock Software" at the end.

## Update Package Sources

> [!NOTE]
> First update your package sources by following the [Debian Package Sources guide](../debian-package-sources/README.md).

## Installation

> [!NOTE]
> First ensure KIAUH is properly updated and fixed by following the [Updating and Fixing KIAUH guide](../kiauh-update-and-fix/README.md).

1. Unhold X11 packages:
   ```
   sudo apt-mark unhold xserver-common xserver-xorg-legacy
   ```

2. Install KlipperScreen via KIAUH:
   ```
   ./kiauh/kiauh.sh
   ```
   Navigate through the menu to install KlipperScreen. Use all defaults except for the NetworkManager question.

   > [!NOTE]
   > The installation may appear frozen and can take up to 30 minutes. This is normal.

## Touchscreen Configuration

Create `/etc/X11/xorg.conf.d/10-touchscreen.conf`:

```
Section "InputDevice"
    Identifier      "GoodixTouch"
    Driver          "evdev"
    Option          "Device" "/dev/input/event0"
    Option  "Calibration"   "2 473 6 277"
    Option  "SwapAxes"      "0"
EndSection

Section "ServerLayout"
    Identifier      "Default Layout"
    InputDevice     "GoodixTouch" "SendCoreEvents"
EndSection
```

## KlipperScreen Configuration

1. Edit `/home/mks/KlipperScreen/scripts/KlipperScreen-start.sh`
2. Find the line with `exec /usr/bin/xinit` (second to last line)
3. Replace it with:
   ```
   exec /usr/bin/xinit $KS_XCLIENT -- :0 -seat seat1 -allowMouseOpenFail -sharevts -novtswitch -noreset
   ```

## Service Configuration

Edit the KlipperScreen systemd service:
```
sudo systemctl edit KlipperScreen
```

Add this section under the first two commented lines:
```
[Unit]
ConditionPathExists=
```

## Starting KlipperScreen

1. Stop the stock display software:
   ```
   sudo systemctl stop makerbase-client
   ```

2. Start KlipperScreen:
   ```
   sudo systemctl restart KlipperScreen
   ```

3. If KlipperScreen appears and works correctly, disable the stock software:
   ```
   sudo systemctl disable makerbase-client
   ```

## Filament Macros

Add these macros to your printer.cfg to enable load/unload buttons:

```ini
[gcode_macro UNLOAD_FILAMENT]
description: filament unload
gcode:
    M603

[gcode_macro LOAD_FILAMENT]
gcode:
    {% set mode = params.T|default(0)|int %}
    M604 T={mode}

    RESPOND TYPE=command MSG="action:prompt_begin Loading successful?"
    RESPOND TYPE=command MSG="action:prompt_button Yes|RESPOND MSG='Done loading.'|primary"
    RESPOND TYPE=command MSG="action:prompt_button No|LOAD_FILAMENT T=1|error"
    RESPOND TYPE=command MSG="action:prompt_show"
```

> [!IMPORTANT]
> If using OrcaSlicer, remove UNLOAD_FILAMENT from the print end G-code to prevent filament cutting after each print.

## Optional: UI Switching

Enable G-code switching between KlipperScreen and stock software:

1. Run `sudo visudo` and add:
   ```
   ALL ALL=(ALL) NOPASSWD: /bin/systemctl start KlipperScreen, \
       /bin/systemctl stop KlipperScreen, \
       /bin/systemctl start makerbase-client, \
       /bin/systemctl stop makerbase-client, \
       /bin/systemctl is-active KlipperScreen
   ```

2. Add to your printer.cfg:
   ```ini
   [gcode_shell_command swap_screen]
   command: bash -c "if systemctl is-active --quiet KlipperScreen; then sudo systemctl stop KlipperScreen && sudo systemctl start makerbase-client; else sudo systemctl stop makerbase-client && sudo systemctl start KlipperScreen; fi"
   timeout: 30.
   verbose: True

   [gcode_macro SWAP_SCREEN]
   gcode:
     RUN_SHELL_COMMAND CMD=swap_screen
   ```

Use `SWAP_SCREEN` in the console to switch interfaces. Takes ~5s to stock software, ~15s to KlipperScreen. Can be done mid-print.

## Reverting to Stock Software

To permanently switch back to the stock display software:

1. Stop and disable KlipperScreen:
   ```
   sudo systemctl disable --now KlipperScreen
   ```

2. Enable and start the stock software:
   ```
   sudo systemctl enable --now makerbase-client
   ```

After a reboot, the stock display software will start automatically.