# SGI Iris Indigo R4k (IP20) Troubleshooting

Troubleshooting the case where the indigo will immediately show a green status light and will progress no further.  There will be nothing on the serial console, nothing on the video display, and no startup chime. Some SCSI disks may show signs of life by spinning up, but this is likely just because they are powered on.

The boot process is as follows:
 * LED is green on power up
 * CPU starts POST and sets LED to amber and chimes <validate>
 * once POST is complete, CPU sets LED back to green and boot commences

If the LED stays green then something is preventing the CPU from executing. Unfortunately there could be a range of reasons for this

<link to specs, manuals etc>

# Minimal configuration

This should be enough for the Indigo to get past the POST stage.  By removing as many things as possible we can focus on the immediate issue.
 * All drives removed
 * Graphics board removed
 * CPU installed
 * GIO boards removed
 * 4 SIMMs installed (any size) into bank A
 * nothing connected except power

# Steps

In rough order (IMO)

## Physical inspection

These machines are old and likely have been through multiple owners / configurations.  Check for signs of physical trauma.  Magnifiers / loupe / optical scope will come in handy
 * Check all connectors and sockets for bent / missing / corroded pins
 * Check for damaged / scorched / missing components and replace if possible
  * One report of expired cap on ZB4 board <link>
  * I had a missing C502 on the underside of my R4400 CPU module <what value to replace with?>
 * Damaged traces - especially around the battery area.  This is soldered to the main board and may have already been replaced (maybe more than once)
 * dry solder joints - look for cracking around connector solder joints
  * consider reflowing connections if they look suspect

## Check battery

There has been at least one report of a "dead" indigo due to battery issues.  <link>
 * remove battery if installed and try to boot
 * check voltage and replace if low.  Reports that lithium cells (2.5v) work <link>.  Or replace with Tadiran (3.6v)
 * re-install and try to boot

## Physical connections

Deoxit on all sockets / connectors may be a good idea
 * reseat CPU board in chassis
 * reseat CPU module on CPU board
 * reseat all SIMMs
 * reseat PROM on CPU board
 * reseat backplane EEPROM (8 pin)
 * reseat 8 pin EEPROM in CPU module (contains clock multiplier info)
 * check reset switch - at least one report of reset switch being permanently closed <link>
  * Removing the PSU will be required.  There is one screw at the back and the PSU should slide out with a bit of encouragement. 
 
## PSU voltage levels

This is quite tricky to do given the enclosed nature of the machine.
 * drive sled connectors have all voltages?
 * backplane connector (pinout?)

## Tantalum Capacitors
Tantalum capacitors are Yellow, square(ish), and have a red/orange stripe on one end.  When these fail, they cause a short
 * check resistance across each cap and anything below 10 ohm should be treated as suspect
 * remove any suspect caps from the board and check continuity in isolation
 * replace as needed

## Memory 

 * Try alternate SIMM sets in bank A.  You may have a bad module

## (E)EPROMs

There are multiple non-volatile chips which may be involved here.

### Backplane 8 pin EEPROM

<link to image> <chip number>
Stores the MAC address (anything else?)
 * check that it can be read from and written to using an EEPROM programmer.
 * The machine should write to a blank chip on boot

### CPU module 8 pin EEPROM

<link to image> <chip number>
Stores the CPU clock multiplier
 * check that it can be read from and written to (make a backup first and write this back to the chip when done) 

### PROM EPROM

This stores the boot machine code.  There are several revisions for the R4k Indigo which add support for newer CPUs and more memory.
<SGI part number to revision table>
 * check that the version you have is compatible with the CPU you are using (e.g. R4400 support was not in earlier revisions <validate>)
  * there should be a label on the top with the SGI part number
 * check that it can be read using an EPROM programmer.  The system would never write to it.

<process for upgrading / reflashing>

### Probing PROM signals for activity

This is usually one of the first steps when diagnosing simpler systems, but access to the PROM is impossible when the board is installed in the chassis
 * solder some test leads to the back of the board and hook them up to the scope / analyzer
 * looking for activity on the enable, CS, address lines to indicate that the CPU is somewhat alive

<ROM pinout>

### Probing CPU card for clock, reset, etc

 * <pinout of connector>
 * Probe to see if reset is being de-asserted and that we have a clock <is clock generated on module, or main board?> 
