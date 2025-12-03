# PCB Design Review Guide

# Schematic Validation - “Is the electrical design correct?”

Validate the schematic before beginning layout work. Most PCB issues originate from schematic errors rather than routing.

## 1. Electrical Rules Check (ERC)

- Run the ERC tool in your CAD platform. It detects unconnected pins, incorrect pin types, missing power flags, and structural issues
- Only ignore “Not Connected” warnings when the pin is purposefully marked NC.
- Confirm all IC power pins are connected.

## 2. Pinout and Footprint Orientation Review

A common source of design failure is incorrect footprint orientation.

Frequent issues include:

- Pin 1 mismatch between schematic and footprint
- Mirrored connectors
- Incorrect transistor/MOSFET pinouts
- Using the wrong footprint library entry

Verify orientation visually against component datasheets.

## 3. Power Path and Load Analysis

Confirm all power rails and devices are adequately sized.

Checklist:

* Estimate maximum current draw
* Validate regulator headroom
* Check diode polarity
* Confirm power sequencing when required

Consider worst-case conditions.

---

## 4. Safety and Protection Components

Confirm appropriate protection measures exist:

* ESD protection on external interfaces
* Series resistors on sensitive signals
* Fuses on external power inputs
* Input filtering on noisy rails

## 5. Stackup and Impedance Planning

Important for high-speed or RF designs.

Verify:

* Trace widths
* Via sizes
* Return paths

**Tool:** Saturn PCB Toolkit.
Use your manufacturer’s default 4-layer stackup if unsure.

## 6. Mechanical and 3D Fit Verification

Import the board into a 3D tool to detect:

* Height conflicts
* Misaligned connectors
* Incorrect button or mechanical placements
* Blocked mounting holes

When uncertain, print the PCB outline at 1:1 scale to validate fit.

# Layout and Routing Review

## 7. Physical Footprint Validation (“Paper Test”)

If possible, print the PCB at 1:1 scale and place components on it. This quickly exposes footprint errors, incorrect spacing, or misalignment.

## Design Rules Check (DRC) Throughout Layout

Run DRC frequently rather than at the end.

Configure and verify:

* Minimum clearance and safety margin
* Via/annular ring settings
* Constraints for high-speed signals

The board must reach **zero DRC errors** before generating Gerbers.

## Power and Ground Integrity Review

Inspect highlighted power nets and ground planes.

Check for:

* Undersized traces
* Long or broken return paths
* Ground plane gaps under high-speed lines
* Insufficient stitching vias
* Potential thermal bottlenecks

## High-Speed Signal Routing Checks

Verify fundamentals:

* Differential pair length matching
* Use 45° corners
* Avoid routing over ground-plane gaps
* Keep clocks short
* Separate noisy digital lines from analog circuits

# Manufacturing Output Verification

Ensure the exported files accurately represent the intended design.

## External Gerber Verification

Use an external viewer (e.g., Gerbv) to review:

* Pad-to-drill alignment
* Board outline integrity
* Silkscreen clearance
* Text orientation and polarity

Zoom in significantly to catch subtle issues.

# Final Human Review - “Checks tools cannot perform”

These items frequently escape automated checks and require manual inspection.

## Common Errors to Review

### Connectivity and Orientation

* Swapped RX/TX
* Reversed MOSFETs, diodes, LEDs
* Incorrect connector pin orientation

### Power Integrity

* Missing bulk capacitors
* Regulator thermal issues
* Missing ground pours
* Incorrect placement of ferrite beads

### Analog and Mixed-Signal

* Long analog paths near switching regulators
* Improper filtering on ADC references

### Digital Design

* Missing pull-ups for I²C
* Floating reset or boot-mode pins

### RF and Wireless

* Incorrect antenna placement
* Inadequate ground clearance
* Failure to follow manufacturer layout guidelines

### Manufacturability and Assembly

* Silkscreen covering pads
* Missing fiducials
* Lack of test pads
* Components placed too close (risk of tombstoning)
* Via-in-pad without filling

### Mechanical Checks

* Misaligned connectors and buttons
* Incorrect battery holder orientation
* Mounting hole clearance issues

# Pre-Submission Checklist

Confirm the following before sending the design to fabrication:

1. The board can be assembled by hand if necessary.
2. All polarized components are clearly indicated.
3. PCB and connector orientations have been double-checked.
4. Critical test points are accessible.
5. A debugging plan exists for first prototypes.

Any “No” requires correction before submission.
