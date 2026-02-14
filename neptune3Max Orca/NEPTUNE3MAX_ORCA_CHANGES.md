# OrcaSlicer Profile Optimization - Elegoo Neptune 3 Max
## Changes from original Elegoo_Neptune_3_Max_0_4_nozzle_-_Klipper.orca_printer

---

## 🔴 Printer Profile - Issues Fixed

### 1. Firmware Retraction: DISABLED → ENABLED
**This is a critical fix.** `use_firmware_retraction` was set to `0`.
With Klipper, you always want firmware retraction enabled so OrcaSlicer
sends G10/G11 and Klipper handles it via [firmware_retraction] in printer.cfg.

### 2. Retraction Length: 0.4mm → 0.6mm
With firmware retraction now enabled, OrcaSlicer passes this to Klipper.
0.6mm is the correct value for direct drive. Your original 0.4mm would have
underperformed on stringing.

### 3. Start G-code: Fixed
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

### 4. Pause G-code: "M0" → "PAUSE"
M0 is a program stop command - it halts everything without saving state.
Your Klipper PAUSE macro parks the toolhead and handles resume properly.

### 5. Machine Accelerations: Inconsistent → Unified at 3000

| Setting | Old | New | Impact |
|---------|-----|-----|--------|
| X accel | 5000 | 3000 | ✅ Matches Klipper max_accel |
| Y accel | 5000 | 3000 | ✅ Matches Klipper max_accel |
| Extruding | 3000 | 3000 | ✅ Already correct |
| **Retracting** | **1000** | **3000** | ✅ Faster retractions |
| **Travel** | **500** | **3000** | ✅ Same issue as Pro - was crawling! |
| E accel | 5000 | 3000 | ✅ Consistent |

X/Y were set to 5000 which exceeds your Klipper max_accel of 3000 - OrcaSlicer
would be telling the printer to do things Klipper would cap anyway.
Travel was 500 again - same problem as the Ender 3 Pro.

### 6. Max Speed X/Y: 5000 → 500 mm/s
These were set to 5000 mm/s - completely unrealistic. 500 mm/s is the actual
printer max_velocity from your Klipper config.

### 7. Bed Mesh Limits: Unlimited → Correct values
- Old: -99999 to 99999
- New min: 33, 16
- New max: 397, 415
Matches your [bed_mesh] section in printer.cfg exactly.

### 8. Z-hop: 0.4mm → 0.2mm
Same fix as the Enders - 0.4mm is excessive for direct drive.

### 9. Z-hop Type: "Slope Lift" → "Normal Lift"
Slope lift adds complexity without benefit on a direct drive setup.

### 10. Retract When Changing Layer: 0 → 1
Was disabled. Retracting on layer changes reduces blobs at seams.

### 11. Retraction Minimum Travel: 1mm → 1.5mm
Slightly less aggressive - avoids unnecessary retractions on very short moves
which can cause wear and inconsistency.

### 12. printer_structure: "undefine" → "i3"
Same issue as the Ender 3 Pro profile.

### 13. support_air_filtration: 1 → 0
Neptune 3 Max doesn't have active air filtration. This was incorrectly enabled.

### 14. support_chamber_temp_control: 1 → 0
Neptune 3 Max doesn't have a heated chamber. This was incorrectly enabled.

---

## 📊 Full Comparison Table

| Setting | Old | New | Impact |
|---------|-----|-----|--------|
| firmware_retraction | **0 (OFF!)** | 1 | ✅ Critical |
| Retraction length | 0.4mm | 0.6mm | ✅ Better stringing |
| Start G-code | Broken | Clean | ✅ Critical |
| Pause G-code | M0 | PAUSE | ✅ Proper pause |
| X/Y max speed | 5000 mm/s | 500 mm/s | ✅ Realistic |
| X/Y max accel | 5000 | 3000 | ✅ Matches Klipper |
| Travel accel | 500 | 3000 | ✅ 6x faster! |
| Retract accel | 1000 | 3000 | ✅ Faster retracts |
| Bed mesh min | -99999 | 33,16 | ✅ Correct |
| Bed mesh max | 99999 | 397,415 | ✅ Correct |
| Z-hop | 0.4mm | 0.2mm | ✅ Less Z movement |
| Z-hop type | Slope Lift | Normal Lift | ✅ Simpler |
| Retract on layer change | 0 | 1 | ✅ Cleaner seams |
| Retract min travel | 1mm | 1.5mm | ✅ Fewer retracts |
| printer_structure | "undefine" | "i3" | ✅ Correct type |
| Air filtration | 1 | 0 | ✅ Not installed |
| Chamber temp control | 1 | 0 | ✅ Not installed |

---

## 📦 Process Profiles - New for Neptune 3 Max

Four profiles created, all tuned to Neptune's 3000 mm/s² max accel:

### QUALITY
- Outer walls: 60mm/s @ 1200 accel
- Inner walls: 80mm/s @ 1500 accel
- Travel: 300mm/s @ 2500 accel
- Best surface finish, slowest

### BALANCED ⭐ Recommended
- Outer walls: 80mm/s @ 2000 accel
- Inner walls: 100mm/s @ 2500 accel
- Travel: 300mm/s @ 3000 accel
- Best everyday profile

### SPEED
- Outer walls: 100mm/s @ 2500 accel
- Inner walls: 130mm/s @ 2800 accel
- Travel: 350mm/s @ 3000 accel
- Functional parts and fast iterations

### PRINT-IN-PLACE
- Outer walls: 45mm/s @ 1000 accel
- 0.40mm outer wall width (precise!)
- Overhang speed control
- For collapsible swords, fidgets, mechanisms

---

## ⚡ Neptune vs Ender Speeds

The Neptune's max_accel is 3000 (vs 4400-5000 on the Enders), but it has a
MUCH bigger print volume (420x420x500 vs 250x235x250). This means:

- Benchy is similar speed (Neptune has bigger moves)
- Large flat prints are FASTER on Neptune (less travel per mm²)
- Small detailed prints slightly slower than Pro (less accel)

| Profile | Neptune 3 Max | Ender 3 Pro |
|---------|--------------|-------------|
| QUALITY | ~2h 10m | ~2h 00m |
| BALANCED | ~1h 40m | ~1h 25m |
| SPEED | ~1h 15m | ~1h 00m |

*Benchy estimates. Neptune's bigger bed = less wasted travel.*

---

## 💡 Copperhead Hotend Notes

The Slice Engineering Copperhead is an all-metal hotend designed for:
- High-temp materials (ABS, ASA, PA, PC)
- Abrasive materials (carbon fiber, glow-in-dark)
- Long prints without heat creep

**Temperature adjustments vs stock hotend:**

| Material | Stock Hotend | Copperhead |
|---------|-------------|------------|
| PLA | 200-210°C | 205-215°C (+5°C) |
| PETG | 235-245°C | 240-250°C (+5°C) |
| ABS | 240-250°C | 245-255°C (+5°C) |
| ASA | 245-255°C | 250-260°C (+5°C) |

The Copperhead's bimetal heat break requires slightly higher temps than PTFE-lined
hotends. If you see underextrusion, bump up 5°C before changing other settings.

**Minimum travel retraction: 1.5mm**
The Copperhead has minimal ooze due to the tight heat break - 1.5mm minimum
travel avoids unnecessary retractions on short moves.

---

## 🚀 Installation

1. Open OrcaSlicer
2. **Printer Profile:**
   - Settings → Printer → Import
   - Select `neptune3max_orca_optimized.json`
   - Appears as "Elegoo Neptune 3 Max - Klipper OPTIMIZED"

3. **Process Profiles:**
   - Settings → Process → Import each:
   - `neptune_process_quality.json`
   - `neptune_process_balanced.json`
   - `neptune_process_speed.json`
   - `neptune_process_print_in_place.json`

4. **Set Default Profile:**
   - Printer: "Elegoo Neptune 3 Max - Klipper OPTIMIZED"
   - Process: "0.20mm BALANCED @Neptune3Max"
