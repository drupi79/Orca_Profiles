# OrcaSlicer Profile Optimization - Ender 3 Pro
## Changes from original Creality_Ender-3Pro.orca_printer

---

## 🔴 Issues Found & Fixed

### 1. Retraction: 2mm → 0.6mm
Same fix as both v2s. Direct drive needs 0.6mm, not 2mm.

### 2. Bed Size: 220x220 → 250x240
Was severely undersized. Your Pro bed is 240mm deep and your X goes to 250mm.

### 3. Bed Mesh Limits: Unlimited → 20,20 to 186.5,224.5
Was -99999 to 99999. Now correctly matches your probe offset (-53.5, -10.5).

### 4. Start G-code: Fixed
OLD (broken):
```
M117
M190 S0      ← Disables bed heating!
M109 S0      ← Disables hotend heating!
PRINT_START EXTRUDER_TEMP=...
```
NEW (clean):
```
PRINT_START EXTRUDER_TEMP=[first_layer_temperature] BED_TEMP=[first_layer_bed_temperature]
```

### 5. Pause G-code: "M25 ;pause print" → "PAUSE"
M25 is a Marlin command. Your printer uses Klipper's PAUSE macro.

### 6. Machine Accelerations: Inconsistent → Unified at 5000
Your old profile was a mess:

| Setting | Old | New |
|---------|-----|-----|
| X accel | 2500 | 5000 |
| Y accel | 2400 | 5000 |
| Extruding | 5000 | 5000 |
| **Retracting** | **3000** | **5000** |
| **Travel** | **500 ← !!!!** | **5000** |

Travel at 500 mm/s² means your travel moves were barely faster than print moves!
All unified to 5000 to match your Klipper max_accel.

### 7. Extruder Offset: "0x-9" → "0x0"
Your profile had a mysterious -9mm Y offset on the extruder. This would shift
every print 9mm in Y. With a BLTouch, offsets are handled in Klipper, not here.

### 8. Z-hop: 0.4mm → 0.2mm
0.4mm is double what's needed for a direct drive setup. 0.2mm clears all surfaces
with less Z movement time.

### 9. change_filament_gcode: "" → "M600"
Was blank! M600 (filament change) wasn't being passed to Klipper at all.

### 10. printer_structure: "undefine" → "i3"
Was literally set to "undefine". Your printer is a standard i3 cartesian.

### 11. Retraction Minimum Travel: 2mm → 1.5mm
Slightly lower threshold means fewer oozing moves on short travels.

### 12. Thumbnails: Updated to include both sizes
"48x48/PNG, 300x300/PNG" for better thumbnail support.

---

## ✅ Good Things Already In Your Profile

- `use_firmware_retraction: 1` ✅ (Klipper handles retraction)
- `use_relative_e_distances: 1` ✅
- `gcode_flavor: klipper` ✅
- `auxiliary_fan: 1` ✅ (you have the aux fan)
- `max_layer_height: 0.32` ✅ (80% of nozzle, correct)
- `machine_end_gcode: PRINT_END` ✅

---

## 📊 Full Comparison: Old vs New

| Setting | Old | New | Impact |
|---------|-----|-----|--------|
| Retraction | 2mm | 0.6mm | ✅ Critical |
| Bed size | 220x220 | 250x240 | ✅ More usable area |
| Bed mesh min | -99999 | 20,20 | ✅ Correct |
| Bed mesh max | 99999 | 186.5,224.5 | ✅ Correct |
| Start G-code | Broken | Clean | ✅ Critical |
| Pause G-code | M25 | PAUSE | ✅ Uses Klipper macro |
| X max accel | 2500 | 5000 | ✅ 2x faster! |
| Y max accel | 2400 | 5000 | ✅ 2x faster! |
| Travel accel | **500** | 5000 | ✅ 10x faster! |
| Extruder offset | 0x-9 | 0x0 | ✅ No phantom shift |
| Z-hop | 0.4mm | 0.2mm | ✅ Faster Z moves |
| change_filament | blank | M600 | ✅ Filament change works |
| printer_structure | "undefine" | "i3" | ✅ Correct type |
| Retract min travel | 2mm | 1.5mm | ✅ Fewer ooze moves |

---

## 🚀 Process Profiles

Use the SAME process profiles created for the v2s — they work on the Pro too.
The only Pro-exclusive profile is ULTRA SPEED (needs 5000 accel).

### All Available Profiles for This Printer:

| Profile | Outer Wall | Accel | Benchy | Use For |
|---------|-----------|-------|--------|---------|
| **QUALITY** | 70mm/s | 2500 | ~2h | Display pieces |
| **BALANCED** | 90mm/s | 3500 | ~1h 25m | Everyday prints |
| **SPEED** | 120mm/s | 4200 | ~1h | Functional parts |
| **ULTRA SPEED** ⚡ | 140mm/s | 4800 | ~50m | Prototypes |
| **PRINT-IN-PLACE** | 45mm/s | 1500 | N/A | Mechanisms |

---

## 📦 Installation

1. Open OrcaSlicer
2. Settings → Printer → Import
3. Select `ender3pro_orca_optimized.json`
4. Appears as "Creality Ender-3 PRO - Klipper OPTIMIZED"
5. Use existing process profiles from v2 optimization (+ ULTRA SPEED for Pro)

---

## 💡 Notable: Travel Acceleration Was 500 mm/s²

This deserves special mention. Your old travel acceleration was **500 mm/s²** —
the same as your first layer acceleration. This means:

- Travel moves couldn't accelerate quickly
- The printer was essentially crawling between print moves
- Estimated 15-20% extra time on every print just from slow travels

With 5000 mm/s² travel acceleration, your travel moves will now snap to full
speed almost instantly. Combined with 250-400mm/s travel speeds in the process
profiles, your inter-move times will drop dramatically.
