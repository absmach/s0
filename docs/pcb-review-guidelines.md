# PCB design review guide

This guide is formatted to be compatible with common markdownlint rules and can be used as a reusable PCB review checklist.

## Schematic validation: electrical design correctness

Validate the schematic before beginning layout work. Many PCB issues originate from schematic errors rather than routing.

### 1. Electrical rules check

* Run the ERC tool in your CAD platform to detect:

  * Unconnected pins
  * Incorrect pin types
  * Missing power flags
  * Structural issues
* Only ignore “not connected” warnings when the pin is purposefully marked as not connected.
* Confirm all IC power pins are connected.

### 2. Pinout and footprint orientation review

A common source of design failure is incorrect footprint orientation.

Frequent issues include:

* Pin 1 mismatch between schematic and footprint
* Mirrored connectors
* Incorrect transistor or MOSFET pinouts
* Using the wrong footprint library entry

Visually verify the orientation of each footprint against the component datasheet.

### 3. Power path and load analysis

Confirm all power rails and devices are adequately sized.

Checklist:

* Estimate maximum current draw for the whole board and for each rail
* Validate regulator headroom for voltage and current
* Check diode polarity and ratings
* Confirm power sequencing where required

Always consider worst case environmental and load conditions.

---

### 4. Safety and protection components

Confirm appropriate protection measures exist:

* ESD protection on external interfaces
* Series resistors on sensitive signals
* Fuses or resettable protection on external power inputs
* Input filtering and surge suppression on noisy or exposed rails

### 5. Stackup and impedance planning

This is important for high speed or RF designs.

Verify:

* Trace widths and spacing for controlled impedance lines
* Via sizes and types suitable for the stackup
* Clean and continuous return paths under high speed traces

Use tools such as Saturn PCB Toolkit and your manufacturer’s stackup data.
If unsure, start from your manufacturer’s default four layer stackup.

### 6. Mechanical and 3D fit verification

Import the board into a 3D tool to detect:

* Height conflicts with enclosures or other boards
* Misaligned connectors relative to panel cut outs
* Incorrect button and mechanical placements
* Blocked or mislocated mounting holes

When uncertain, print the PCB outline at one to one scale and place it in the enclosure to validate the fit.

## Layout and routing review

### 7. Physical footprint validation (paper test)

If possible, print the PCB at one to one scale and place the actual components on it.

This helps expose:

* Footprint errors
* Incorrect spacing
* Mechanical interference
* Misalignment of connectors and buttons

### 8. Design rules check during layout

Run design rules checks frequently rather than only at the end.

Configure and verify:

* Minimum clearance and additional safety margin where needed
* Via, annular ring and hole size settings
* Constraints and classes for high speed signals and special nets

The board should reach zero design rules errors before generating manufacturing files.

### 9. Power and ground integrity review

Inspect highlighted power nets and ground planes.

Check for:

* Undersized traces on power nets
* Long or broken return paths
* Ground plane gaps under high speed or sensitive lines
* Insufficient stitching vias between ground planes
* Potential thermal bottlenecks on high current devices

### 10. High speed signal routing checks

Verify the fundamentals:

* Apply length matching for differential pairs where required
* Maintain consistent spacing and reference planes
* Prefer 45 degree or gentle routing over sharp angles
* Avoid routing over ground plane gaps or splits
* Keep clock lines short and well referenced to ground
* Separate noisy digital lines from sensitive analog circuits

## Manufacturing output verification

Ensure the exported files accurately represent the intended design.

### 11. External Gerber verification

Use an external viewer to review:

* Pad to drill alignment and hole positions
* Board outline and any internal cutouts
* Silkscreen clearance from pads and solder mask openings
* Text orientation and polarity on all layers

Zoom in significantly to catch subtle alignment and clearance issues.

## Final human review: checks tools cannot perform

These items frequently escape automated checks and require manual inspection.

### 12. Common errors to review

#### 12.1 Connectivity and orientation

* Swapped receive and transmit signals
* Reversed MOSFETs, diodes and LEDs
* Incorrect connector pin orientation
* Inconsistent pin numbering between schematic and layout

#### 12.2 Power integrity

* Missing bulk capacitors near major loads
* Regulator thermal issues and insufficient copper area
* Missing ground pours in key areas
* Incorrect placement or use of ferrite beads

#### 12.3 Analog and mixed signal

* Long analog paths routed near switching regulators or digital clocks
* Improper filtering or layout on ADC references
* Sensitive nodes crossing splits or noisy ground returns

#### 12.4 Digital design

* Missing pull ups or pull downs for buses such as I2C
* Floating reset or boot mode pins
* Overloaded or heavily shared GPIOs

#### 12.5 RF and wireless

* Incorrect antenna placement relative to ground and enclosure
* Inadequate ground clearance under or around antennas
* Failure to follow manufacturer layout guidelines and keep out zones
* Ground cuts or copper near antenna areas where they should be clear

#### 12.6 Manufacturability and assembly

* Silkscreen covering pads or solder mask openings
* Missing global or local fiducials
* Lack of test pads for key signals and rails
* Components placed too close, increasing risk of solder defects
* Via in pad without filling or capping where it can cause solder wicking

#### 12.7 Mechanical checks

* Misaligned connectors and buttons relative to enclosure features
* Incorrect battery holder orientation and polarity marking
* Mounting hole clearance issues and interference with hardware
* Keep out violations around mounting hardware and edges

## Pre submission checklist

Confirm the following before sending the design to fabrication:

1. The board can be assembled by hand if necessary.
2. All polarized components are clearly indicated and oriented.
3. PCB and connector orientations have been double checked.
4. Critical test points are accessible for probing.
5. A debugging and bring up plan exists for the first prototypes.

Any answer of “no” to these points requires correction before submission.
