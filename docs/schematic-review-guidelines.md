# Schematic review guideline

## 1. Review process

### 1.1 Before you open the schematic

Clarify the context before you comment on anything.

- Understand the product

  - What is the device or system supposed to do
  - Target environment (lab, industrial, outdoor, automotive)
  - Expected lifetime and reliability level

- Capture key electrical constraints

  - Input power range and type (for example 12–24 V DC, USB, PoE, mains)
  - Power budget for each rail and overall current limits
  - Safety or isolation requirements
  - Compliance targets (EMC, safety, surge, ESD)

- Gather reference material

  - Datasheets for all major ICs
  - Vendor reference designs and evaluation board schematics
  - System block diagram or architecture document

If any of these are missing, note this as a process issue before deep review.

### 1.2 First pass overview

Goal: understand the big picture and find obvious show-stoppers.

- Identify major blocks

  - Power input and protection
  - Power regulation and sequencing
  - MCU, SoC or FPGA section
  - Memories
  - Communication interfaces
  - Sensors and analog front ends
  - Drivers and actuators
  - Connectors and external interfaces
  - Test and debug interfaces

- Check basic connectivity

  - Every block should have a power source
  - No use of rails that do not exist anywhere
  - Key interfaces clearly connected between blocks

- Look for immediate red flags

  - MCUs or ICs with no supply or missing ground pins
  - Devices with no decoupling capacitors
  - No obvious way to program or debug the main controller
  - Nets with names that suggest “TODO” or “TBD” on critical signals

Do not worry about component values yet. Focus on structure and intent.

### 1.3 Structure and readability pass

Goal: confirm the schematic is readable and maintainable.

- Sheet organization

  - Group related circuits into separate sheets per function
  - Use clear sheet titles such as “PWR_12V_IN”, “MCU_CORE”, “SENSORS”

- Net naming strategy

  - Use consistent names for power rails (for example 12V, 5V0, 3V3, VBAT)
  - Use clear names for clocks, resets and boot signals
  - For differential pairs, use P and N suffixes consistently
  - Avoid leaving important nets with auto-generated names

- Symbol and library consistency

  - Same part type uses the same symbol everywhere
  - Power pins are either visible on the symbol or handled consistently on a dedicated page
  - Pin numbers and pin names match the datasheet

Ask yourself whether someone else could understand the design in less than thirty minutes.

### 1.4 Detailed functional review

Process for the deep technical review:

- Review block by block using the technical checklists in section 2
- For each block

  - Compare against datasheets and reference designs
  - Check operating conditions, absolute maximum ratings and margins
  - Consider normal operation, start-up and fault conditions
  - Note any unclear design intent or missing comments

Typical order for the detailed pass:

1. Power input and protection
2. Power conversion and rails
3. Decoupling and grounding
4. MCU or main controller (including clock and reset)
5. Memories
6. High speed interfaces
7. Analog and sensor circuits
8. Drivers and actuators
9. Connectors and external I or O
10. Test, debug, safety and EMC

### 1.5 System level “what if” pass

After the detailed review, do a scenario based sweep.

- Power and faults

  - What happens if the input is reversed
  - What happens if the input voltage is too high or too low
  - What happens during brownouts or fast power cycling

- Cables and environment

  - What happens if external cables are long and noisy
  - What happens during ESD events on connectors
  - What happens if connectors are partially inserted

- Firmware and behavior

  - Can the device still be programmed if firmware is bad
  - Are boot mode and recovery paths available on the final product

- Maintenance and service

  - Can a technician probe key rails and signals easily
  - Are errors recoverable without rework

Record any issues or questions that come out of these scenarios.

## 2. Technical checklists

### 2.1 Power input and protection

Goals: correct operating range, protection against common faults and basic EMC robustness.

- Input range and polarity

  - Confirm components on the input path are rated for maximum expected voltage plus margin
  - Provide reverse polarity protection for field wiring where mis-wiring is likely
  - Include overvoltage protection such as TVS diodes or clamps as appropriate

- Inrush and current limiting

  - Consider inrush current caused by large bulk capacitors
  - Use fuses or resettable protection with the correct current and breaking capacity

- EMI and surge protection

  - Add common mode chokes or LC filters for long cable runs when needed
  - Add surge protection devices for interfaces exposed to harsh environments

Common mistakes:

- No reverse polarity protection on user accessible power connectors
- TVS diode rated for insufficient power or incorrect standoff voltage
- Components on the input rated only slightly above nominal voltage with no margin

### 2.2 Power regulation and rails

For each regulator (buck, boost, buck-boost, LDO):

- Topology and connections

  - Verify all pins match the datasheet typical application circuit
  - Check feedback network values and desired output voltage
  - Check enable, power good and soft start pins where available

- Passive components

  - Input and output capacitors meet value, voltage rating and ESR requirements
  - Inductors meet inductance, current rating and saturation current requirements
  - Sense resistors and compensation components are present and sized correctly

- Current and thermal considerations

  - Regulator maximum current significantly exceeds maximum load current
  - Worst case power dissipation is within safe limits with margin
  - Heat dissipation and copper area on the PCB are considered in the design

- Power sequencing

  - Verify required power up order for core, IO and analog domains
  - Ensure enable and reset pins enforce proper sequencing where needed

Typical error patterns:

- Trying to drop a large voltage across a small LDO at high current
- Using “typical” capacitor values but ignoring ESR and derating
- Forgetting to handle power up order for complex ICs that require it

### 2.3 Decoupling and bypassing

Decoupling is essential for stability and noise control.

- Per IC

  - Place at least one small ceramic capacitor close to each power pin
  - Include a local bulk capacitor on each rail feeding a group of ICs

- Important rails

  - Follow vendor recommendations for decoupling count and placement on MCUs, FPGAs and SoCs
  - Provide separate or filtered decoupling for analog reference and analog supply pins when specified

- Ground return

  - Ensure each decoupling capacitor connects directly between the power pin and its local ground reference
  - Avoid routing decoupling returns through narrow or long ground paths

Common mistakes:

- Missing small local capacitors on some power pins of a large IC
- Relying only on one or two larger capacitors far away from the IC
- Sharing decoupling components between unrelated functional blocks

### 2.4 Grounding and reference strategy

- Ground topology

  - Use a continuous ground plane for most digital designs
  - In mixed signal designs, keep noisy and quiet sections separated but joined at a controlled point

- High current paths

  - Separate high current and switching returns from sensitive analog returns
  - Keep motor or load currents out of the ground area under ADCs and precision circuitry

- Connectors and shielding

  - Decide how cable shields are terminated (chassis, RC network, ferrite bead)
  - Avoid routing cable shield currents through signal reference grounds

Red flags:

- Multiple ground symbols without clear documentation of their relationship
- High current load returns routed through the same narrow trace as MCU ground

### 2.5 Clocks and crystals

- Crystal based clocks

  - Verify connection of crystal pins to the correct MCU or IC pins
  - Size load capacitors according to the crystal load capacitance and datasheet formulas
  - Add series resistance if recommended by the vendor

- Oscillator modules

  - Confirm supply decoupling for oscillator modules
  - Ensure output format and voltage are compatible with the clock input

- Clock tree

  - Check main system clock frequency versus maximum rated frequency
  - Confirm any derived clocks are within valid ranges

Typical mistakes:

- Using approximate capacitor values instead of calculated ones
- Leaving crystal load capacitors out, assuming the MCU will still start
- Forgetting supply decoupling on clock modules

### 2.6 Reset and boot configuration

- Reset circuits

  - Provide a defined reset with a pull up or pull down as required
  - Consider supervisor or power on reset ICs in designs with slow or noisy supplies
  - Connect reset buttons safely and consider simple debounce if required

- Boot and configuration pins

  - Tie boot mode pins to defined logic levels with resistors
  - Ensure that any shared boot and GPIO pins have biasing compatible with both functions
  - Provide a method to force bootloader or recovery mode in production

- Recovery paths

  - Confirm there is a way to reflash or rescue devices with bad firmware without physical rework

Common mistakes:

- Leaving boot configuration pins floating
- Driving boot pins directly from other logic without reliable pull devices
- No way to enter a bootloader except through application firmware

### 2.7 MCU, SoC or FPGA section

- Power and decoupling

  - All power pins are connected to the correct rails
  - All ground pins are connected and exposed pad is tied correctly
  - Decoupling follows vendor guidelines

- Special pins

  - Analog and reference pins are treated according to the datasheet
  - Clock and oscillator pins are wired correctly

- Debug and program interfaces

  - JTAG, SWD or equivalent debug pins are accessible on a connector or pads
  - Any required mode pins for debugging are available

- GPIO assignments and limits

  - No pin is overloaded beyond its current capability
  - Total current per port and per device is within limits
  - Pins used for analog inputs are not shared with heavy digital loads

Typical issues:

- Exposed thermal pad not tied to ground or tied incorrectly
- Using strapping or boot pins as general IO without understanding reset behavior
- Leaving critical debug pins inaccessible in the final assembly

### 2.8 Memories

- Power and decoupling

  - Each memory IC has appropriate supply decoupling

- Interface wiring

  - For SPI memories, verify MOSI, MISO, SCK and chip select orientation and naming
  - For parallel or high speed memories, check every address, data and control pin mapping
  - For QSPI or similar interfaces, ensure data lines are correctly numbered

- Pull ups and terminations

  - I2C memories have appropriate pull up resistors on SCL and SDA
  - Required termination or bias networks for high speed memory are included

- Multiple devices on a bus

  - Each device has its own chip select
  - No floating chip select pins

Common problems:

- Swapping MOSI and MISO because of naming differences between IC and MCU
- Forgetting pull up resistors on I2C buses
- Leaving unused address or configuration pins floating

### 2.9 Communication interfaces

#### 2.9.1 USB

- Topology

  - Confirm correct connector type and pin mapping
  - Ensure D plus and D minus are not swapped

- Protection

  - ESD protection near the connector on data and VBUS
  - Overcurrent protection on host ports

- Power and detection

  - VBUS detection signals wired correctly where required
  - Power switching follows USB specifications if the device can source power

#### 2.9.2 Ethernet

- PHY and magnetics

  - Verify all pairs match the reference design pinout
  - Check center tap connections and biasing resistors

- Clocking

  - Confirm crystal or oscillator values and load components for the PHY

- Protection

  - Include ESD and surge protection where needed
  - Confirm common mode chokes and filters are used per reference design

#### 2.9.3 UART, RS-485, CAN, I2C and SPI

- Signal levels

  - Ensure level shifting where voltage domains differ

- Bus management

  - Provide pull ups on I2C
  - Provide termination and biasing for RS-485 and CAN as appropriate

- Direction control

  - Correctly drive direction and enable pins on transceivers

- Shared buses

  - Each peripheral has a unique chip select or address
  - Consider mixed mode compatibility when multiple devices share one bus

### 2.10 Analog and sensors

- Input ranges

  - Ensure sensor outputs and analog signals stay within ADC or amplifier limits
  - Provide scaling or level shifting where needed

- Filtering

  - Add appropriate RC or active filters for noisy signals and anti aliasing where necessary

- References and grounds

  - Provide stable reference voltages and follow decoupling recommendations
  - Keep analog ground and reference paths clean and away from switching currents

- Protection

  - Add series resistors and clamps for external or field connected analog inputs
  - Use ESD protection for sensor connectors exposed to users

Common issues:

- Connecting external sensors directly to MCU pins without any protection
- Ignoring sensor output impedance when designing ADC input filters
- Placing sensitive analog circuits near noisy digital or power sections

### 2.11 Drivers and loads

- Driver selection

  - Verify MOSFET and driver choice suits voltage, current and speed requirements
  - Confirm gate drive voltage is sufficient for required Rds(on)

- Protection

  - Provide flyback diodes for inductive loads
  - Use snubbers or clamps to manage switching edges if required

- Power and thermal

  - Confirm voltage, current and power ratings with sufficient margin
  - Check thermal dissipation paths and package capability

- Control interfaces

  - Ensure MCUs are not driving heavy loads directly without drivers
  - Use appropriate gate resistors and pull downs

Common mistakes:

- No flyback paths for inductive loads such as relays and motors
- Overstressing MCU pins by using them directly on high current loads
- Underestimating MOSFET or driver heat dissipation

### 2.12 Connectors and external IO

- Pinouts

  - Provide clear pin names on the connector symbol
  - Confirm pin order against mechanical drawings or cable specs

- Power and polarity

  - Make reversed connections unlikely with keyed connectors when feasible
  - Add protection where mis-wiring is possible

- Protection and robustness

  - Use ESD and surge protection on user accessible connectors
  - Add series resistors or filters on long signal lines

### 2.13 Test and debug features

- Debug access

  - Expose programming and debug interfaces on connectors or pads
  - Ensure access in the assembled product or test jigs

- Test points

  - Add test points for each power rail
  - Add test points for reset, clocks and key communication signals
  - Provide at least one easy ground reference test point

- Production and service

  - Mark do not populate components clearly where used
  - Provide alternate power entry for lab testing when useful

### 2.14 Safety, isolation and EMC

- Isolation

  - Maintain clear separation between high and low voltage domains
  - Use isolation components correctly powered on both sides

- Creepage and clearance

  - Ensure PCB layout can meet required creepage and clearance distances
  - Reflect isolation boundaries clearly in the schematic

- Surge, ESD and EFT protection

  - Include appropriate protection parts on mains and long external links
  - Consider common mode chokes, filters and RC snubbers for EMC

## 3. Tools and techniques for review

### 3.1 Using features in the EDA tool

- Net highlighting

  - Trace important nets such as power rails, resets, clocks and communication buses

- Cross probing

  - When layout is available, cross probe between schematic and PCB

- Automated checks

  - Run electrical rules checks to find unconnected pins and multi drivers
  - Run design rule checks on the PCB when available to find simple connection errors

Treat ERC and DRC as helpers, not as replacements for manual thinking.

### 3.2 Datasheet and reference comparison

- Place the datasheet and schematic side by side
- For each critical IC

  - Compare pin by pin with the typical application circuit
  - Confirm presence of all required support components
  - Check recommended operating conditions and margins

### 3.3 Bill of materials and netlist checks

- Bill of materials review

  - Verify capacitor and diode voltage ratings against rail voltages
  - Verify resistor power ratings where high dissipation is expected

- Netlist review

  - Look for multiple net names that should be one rail due to spelling differences
  - Confirm all intended power rails are present and consistently named

## 4. Recording and communicating feedback

### 4.1 Comment format

Use a structured format so issues are easy to track.

- Assign an identifier such as “PWR-01” or “MCU-03”
- Specify the location

  - Sheet name
  - Reference designator
  - Net name

- Describe the issue briefly
- Explain impact
- Suggest a fix or request clarification

Example:

- Identifier

  - MCU-02

- Location

  - Sheet “MCU_CORE”
  - U3 boot configuration pin

- Issue

  - Boot pin left floating

- Impact

  - Boot mode may be unpredictable and cause start up failures

- Suggestion

  - Add a pull down resistor to ground according to the datasheet and provide a way to override if needed

### 4.2 Prioritizing issues

Not all issues are equal. Mark them clearly.

- Blocker

  - Must be fixed before proceeding
  - Safety issues or high likelihood of complete failure

- High

  - Significant functional risk or reliability concern

- Medium

  - Recommended for robustness, noise margin or clarity

- Low

  - Style, readability or minor cleanup

### 4.3 Reviewing your own design

If you are both designer and reviewer, try to combat familiarity.

- Take a break between design and review
- Use this guideline as a checklist and actively try to find faults
- Ask “how could this fail in the field” and trace those scenarios

## 5. Practical review sequence

You can follow this sequence during an actual review.

1. Read the product description and constraints
2. Scan the schematic for major blocks and overall signal flow
3. Review power input and protection
4. Review power regulators and derived rails
5. Check decoupling and ground strategy across all sheets
6. Deep review of MCU or main controller including clock, reset and boot pins
7. Review memories and their interfaces
8. Review communication interfaces
9. Review analog and sensor circuits
10. Review drivers and loads
11. Review connectors and external IO
12. Review test, debug, safety and EMC aspects
13. Run ERC and DRC, check BoM and do a final system level “what if” pass
