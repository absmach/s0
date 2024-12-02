# Panelizing

```bash
kikit panelize \
    --layout 'grid; rows: 1; cols: 3; space: 2mm' \
    --tabs 'fixed; width: 3mm; vcount: 2' \
    --cuts 'vcuts; clearance: 1mm' \
    --framing 'railslr; width: 5mm; space: 3mm;' \
    --fiducials '4fid; hoffset: 2mm; voffset: 2mm; coppersize: 2mm; opening: 1mm;' \
    --text 'simple; text: Abstract Machines; anchor: ml; hoffset: 2mm; hjustify: center; vjustify: center; orientation: 90deg;' \
    --post 'millradius: 1mm' \
    am-iot-gateway.kicad_pcb output.kicad_pcb
```
