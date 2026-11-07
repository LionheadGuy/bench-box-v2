# Custom boards

Two small boards made for the Bench Box V2. Both are designed to be **made and assembled by PCBWay** (turnkey): every
part has an exact manufacturer part number in the BOM, so you upload three files and they build it.

> **Status: designed and checked in KiCad (ERC clean, DRC 0 errors, 0 unconnected), checked against the datasheets,
> but not yet built or tested.** The charger board charges a lithium pack. Have someone who knows power electronics
> look over the schematic before you order, and test the first board on a bench supply with a current limit, without the
> pack, before it goes in a box.

## Charger board (`charger/`)

USB-C in, battery charging and the box's power path, in one 52 x 68 mm board. It replaces a USB-C "PD trigger" module,
a separate 4S charger and two ideal-diode modules.

- **USB-C in at 20 V** (WCH CH224A asks any USB-C PD charger for 20 V).
- **4S lithium charging** (TI BQ24610): **16.4 V** (4.10 V per cell, kinder to a pack that is kept full), **4.5 A**
  charge, 0.3 A precharge and end of charge, ~9 h safety timer.
- **Input current limit**: the board measures the total current from the USB-C charger (box running + charging) and
  turns the charging down to stay under the limit, so the box can never overload the charger.
  - **As built: 2.92 A** (any 60 W+ USB-C charger, any normal cable).
  - **Cut jumper JP1: 4.38 A** (needs a 100 W charger AND a 5 A e-marked USB-C cable).
- **Power path**: the box runs from the board's SYS output: from USB-C when plugged in, from the pack when not, with no
  break in between.
- **Battery temperature**: a 10 k NTC (103AT) taped to the pack, into J4 (JST PH 2-pin), gives a 0-45 °C charge
  window. **It will not charge with nothing in J4.** For a bench test without the NTC, close JP2 (fixed 10 k; this
  removes the temperature cut-out).
- **LEDs**: green = 20 V agreed, yellow = input OK, red = charging, green = full (all dark when running on the pack).
- **Connectors** (all vertical, plugs from above): J1 USB-C (through the box's left wall), J2 XT30 male = battery
  (through the BMS and the pack fuse), J3 XT30 female = SYS out to the power switch, J4 NTC.
- Charge time, 6.6 Ah pack: about 1.5 h with the box off (JP1 cut, 100 W charger) or 2.5 h (as built); longer while
  the box runs.

### Ordering at PCBWay (PCB + assembly)

Upload `charger-gerbers.zip`, `charger-bom-pcbway.csv`, `charger-positions.csv`.

| Option | Value |
|---|---|
| Size / layers | 68 x 52 mm, **4 layers**, FR-4 TG150-160, 1.6 mm |
| Min track / spacing | **5 / 5 mil** |
| Min hole | **0.2 mm** |
| Copper | **1 oz outer, 1 oz inner** (do not pick 2 oz: the layout is at 5 mil) |
| Surface finish | **ENIG** |
| Vias | tented |
| Assembly | yes, top side; 63 SMD + 3 through-hole parts (both XT30s, the JST) |

Parts with no DigiKey/Mouser listing (CH224A, HRO USB-C socket, AMASS XT30s) have their LCSC numbers in the BOM;
PCBWay buys from Shenzhen suppliers first. Plain resistors and capacitors may be swapped for equivalents; everything
marked "exact part only" may not.

## Converter board (`converter/`)

Splits the power rail and makes the computer's 12 V. 70 x 60 mm, 2 layers.

```
IN (XT30 male, 12-21 V from the power switch)
 ├─ 5 A MINI blade fuse ──────────────────────────────> HUB (XT30 female) -> USB hub power (7-24 V)
 └─ 10 A MINI blade fuse -> 220 uF + 24 V TVS -> TRACO TEN 60-2412WIN -> 12 V OUT (XT30 female) -> ODROID H5
                                                                       └─ "12 V ON" LED
```

- TRACO TEN 60-2412WIN: 9-36 V in, 12 V 5 A out, isolated, with TRACO's recommended input and output parts.
- Fuses are MINI (ATM) automotive blades in holders, replaceable by hand. **Buy them separately** (they are not in the
  assembly BOM): Littelfuse **0297010.WXNV** (10 A) and **0297005.WXNV** (5 A).

### Ordering at PCBWay

Upload `converter-gerbers.zip`, `converter-bom-pcbway.csv`, `converter-positions.csv`. 2 layers, 1.6 mm, 1 oz, ENIG,
0.3 mm minimum gap, 0.4 mm minimum hole: any standard 2-layer option. Order it together with the charger board to share
set-up and shipping.

## Both boards

- **XT30 polarity**: + is the **square end** of the housing (pin 2 in KiCad). Check with a meter before the first
  plug-in.
- Mounting: four M3 holes each, onto heat-set inserts in the printed box, M3 x 8 screws.
- `kicad/` holds the KiCad 10 projects (open `*.kicad_pro`), and `*-schematic.pdf` is the printed schematic.
