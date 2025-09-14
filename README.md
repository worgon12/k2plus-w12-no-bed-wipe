# K2 Plus – Disable Bed/Slot Wipe, keep Silicone Wiper

Use case: For build plates without the factory slot.
Prevents the toolhead from wiping on the bed/slot so you don’t damage your new plate, while keeping the silicone wiper routine intact.

What this patch does

Disables the slot-wipe macros: M1500 and M1501.

Forces NOZZLE_CLEAR (used in start flows) to call the silicone path via BOX_NOZZLE_CLEAN.

Targets the silicone strip by adjusting box cleaning coordinates to ≈ Y 374.

Leaves all other Creality sequences as-is.

## Installation

## 1) Add the override file

Save this as w12_no_bed_wipe.cfg:

w12_no_bed_wipe.cfg

```bash
# SPDX-License-Identifier: MIT
# Copyright (c) 2025 worgon12

# Disable bed/slot wiping (for slot-less plates), keep silicone wiper

[gcode_macro M1500]     # "go to wipe area" -> no-op
gcode:

[gcode_macro M1501]     # "wipe" -> no-op
gcode:

# NOZZLE_CLEAR -> silicone-only (use BOX_NOZZLE_CLEAN)
[gcode_macro NOZZLE_CLEAR]
rename_existing: NOZZLE_CLEAR__W12_OLD
gcode:
  BOX_NOZZLE_CLEAN
```

## 2) Include it in printer.cfg (IMPORTANT)

Add this as the last include so it overrides earlier definitions:

```bash
[include w12_no_bed_wipe.cfg]
```

Klipper loads includes in order—putting it last guarantees our overrides win.

## 3) Adjust box.cfg to silicone coordinates

Set cleaning Y-positions to the silicone strip (≈ Y 374); keep the rest stock unless noted.

```bash
[box]
#clean_left_pos_y: 378      ; original (commented)
clean_left_pos_y: 374       ; w12

#clean_right_pos_y: 378     ; original (commented)
clean_right_pos_y: 374      ; w12

#extrude_pos_y: 378         ; original (commented)
extrude_pos_y: 374          ; w12
```

If your silicone is slightly offset, use 373–375 for the Y-values.

Quick test

RESTART

G28

G1 Z10

Slot wipe must be gone:

M1500
M1501 ; neither should move the toolhead

Silicone wipe still works:

M104 S140
BOX_NOZZLE_CLEAN ; wipes around Y ≈ 374 (silicone), never Y 378–380

Start a print—there should be no bed/slot wipe anymore.

## License

MIT © worgon12 — please keep this notice.

