# PLR Z-Offset Bug

When a print is interrupted by a power loss, Qidi's Power Loss Recovery (PLR) system resumes the print using a shell script (`plr.sh`). Due to the order of operations in the resume sequence, any custom Z offset stored in `saved_variables.cfg` is silently ignored — the printer resumes at the wrong Z height.

---

## Symptoms

- After a power loss, the print resumes printing too high or too low relative to where it left off
- The `z_offset` value in `saved_variables.cfg` appears correct but has no effect on resume
- The issue is more noticeable with non-standard build plates that require a z offset correction (e.g. thicker plates like the Tyson build plate)

---

## Root Cause

The PLR resume sequence in `RESUME_INTERRUPTED` calls `set_zoffset` first, then runs the shell script via `RUN_SHELL_COMMAND CMD=POWER_LOSS_RESUME`. Inside `plr.sh`, the following lines execute:

```bash
echo "SET_KINEMATIC_POSITION Z=$z_position"
...
echo "G1 Z$z_position"
```

`SET_KINEMATIC_POSITION` resets the coordinate system and the subsequent `G1 Z` move positions the head without any gcode offset applied. Whatever `set_zoffset` did earlier in the macro is effectively overwritten.

---

## Fix

Edit `plr.sh` on the printer:

```bash
nano ~/scripts/plr/plr.sh
```

Find this block:

```bash
echo "G90"
echo "G1 Z$z_position"
grep -E "M83|M82" "$TEMP_FILE.header" | tail -n 1
```

Change it to:

```bash
echo "G90"
echo "G1 Z$z_position"
echo "set_zoffset"
grep -E "M83|M82" "$TEMP_FILE.header" | tail -n 1
```

No Klipper restart is required — shell script changes take effect immediately on the next PLR resume.

---

## Bonus: z_offset Corruption After PLR

If you manually adjusted your Z offset mid-print during a PLR resume (e.g. via `SET_GCODE_OFFSET Z=0.05`), the `save_zoffset` macro at print end will save that adjusted value to `saved_variables.cfg`, overwriting your tuned offset.

To restore the correct value without rebooting:

```
SAVE_VARIABLE VARIABLE=z_offset VALUE=0.012
```

Run this in the Fluidd/Mainsail console, substituting your actual tuned offset. Unlike editing `saved_variables.cfg` directly in the config editor, `SAVE_VARIABLE` updates both the file and the in-memory value simultaneously — no restart needed.

---

## Affected Hardware

- Qidi Q2 / Q2C with a custom `z_offset` in `saved_variables.cfg`
- Most noticeable with thicker build plates that require a larger offset correction

## Verified On

- Firmware 1.1.0 and 1.1.1

Behavior on newer firmware versions is unknown — if you've tested this on a later version, feel free to update this page.
