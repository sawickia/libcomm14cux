_This is a mirror of the information in [my personal webspace](https://colinbourassa.github.io)._

# Hardware interface #

The serial port used by the 14CUX uses nonstandard signalling. A normal PC serial port (RS-232) cannot communicate with it. Fortunately, a 5V TTL FTDI cable is perfectly suited for this interface.

## FTDI cable ##

You'll need to get a 5-volt FTDI USB-to-serial converter cable. Either the [TTL-232R-5V-WE](http://www.mouser.com/ProductDetail/FTDI/TTL-232R-5V-WE/?qs=OMDV80DKjRpkYYDGO9eaujGTH0wnKdmG) or the [TTL-232R-5V](http://www.mouser.com/ProductDetail/FTDI/TTL-232R-5V/?qs=OMDV80DKjRrD9MyCA82%252bP%252bSWgdIEaA5p) works fine. (If you don't get the wire-ended ("-WE") cable, you'll need to cut the header connector off the end before attaching your own connector.) There are also other manufacturers that build cables using FTDI chips; I've successfully tested the similar [Gearmo cable](http://www.amazon.com/USB-Header-Like-FTDI-TTL-232R-5V/dp/B004LC28G2/ref=pd_sxp_f_pt). When using one of these cables, the software will set the correct baud rate automatically.

On the cables I've used, the Tx and Rx lines were orange and yellow, respectively. Check the documentation for your cable to be sure about the wiring.

Once you have the FTDI cable, it's a good idea to test it with a loopback: short the Tx and Rx lines, connect the USB end to a PC, and start a terminal emulator (such as [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/), if you're running Windows.) Be sure to turn off local echo, and any characters you type should appear in the terminal.

Then, using the [FT\_PROG utility from FTDI](https://www.ftdichip.com/Support/Utilities.htm#FT_PROG), invert the polarity of the Rx line (click screenshot to enlarge) and program the new configuration to the device (by selecting "Devices" -> "Program" or simply pressing Ctrl-P). After this, unplug and re-plug the USB end of the FTDI cable for the change to take effect.

![FT_PROG screenshot](https://colinbourassa.github.io/car_stuff/images/ft_prog.png)

If you're using Linux, you can [use the ft232r\_prog utility](ModifyFTDIFirmwareWithLinux.md) instead of the Windows-only FT\_PROG utility.

A loopback test in a terminal should no longer work once the Rx line is inverted.

## Connector and wiring ##

The wiring harness in 14CUX-equipped vehicles already has a beige 5-pin connector on the serial port lines. It's a "TTS"-type ("Total Terminal Security") connector, which was used on many British vehicles until the mid-1990s, but the mating shell is no longer available from retailers. You will probably want to purchase a more common pair of mating connectors (such as a DB9), and use these instead. If you want to retain the original 5-pin connector on the vehicle's wiring harness, you can splice a different one in parallel.

The serial connector in the vehicle is usually located in the cabin. In the 1993 Range Rover (NAS), it is under the passenger seat; in the 1994 Defender (NAS), it is behind the passenger footwell kickpanel; in the 1994 Discovery (NAS), it is below the glovebox. **Note:** From model year 1993 to 1995 (with the exception of the 1993 SWB), the Range Rover Classic used an electronic air suspension system. On these vehicles, the air suspension ECU has a diagnostic port with a TTS connector as well. Be sure that you are using the port for the engine ECU.

In most vehicles, the connector is mated to a grounding plug (i.e. the mating half of the connector with a pin installed to ground the ECU's Rx line.) You need to remove the grounding plug before connecting your FTDI cable.

**Important:** When you attach a connector to the wire end of the FTDI converter cable, you also need to add a resistor of about 390Ω between the FTDI's Rx line (usually yellow) and GND (usually black). This doesn't need to dissipate a lot of power, so a low-wattage resistor is fine. Cut back the FTDI cable's brown, green, and red wires, as they are not used. The connector wiring must allow the wires to mate up as shown in this table:

| **FTDI cable signal** | **FTDI cable wire color** | **14CUX harness signal** | **14CUX harness wire color** | **14CUX TTS connector pin** |
|:----------------------|:--------------------------|:-------------------------|:-----------------------------|:----------------------------|
|Tx|Orange|Rx|White w/ pink tracer|1 |
|Rx|Yellow|Tx|White w/ light green tracer|4 |
|GND|Black|GND|Black w/ gray tracer|5 |

The TTS connector pin numbers in the table above are only important if you've managed to find a mating connector and want to wire it to match the existing TTS connector in the vehicle. The pins in the vehicle side are numbered like so:

![14CUX TTS Data Link Connector](https://colinbourassa.github.io/car_stuff/images/14cux_tts_data_link_connector_vehicle-side.png)

If you **are** using a TTS connector on the FTDI cable, the back of the connector shell will look like the photo below when it's wired correctly. Note the 390Ω resistor between pins 4 and 5 (click photo to enlarge):

![FTDI cable with TTS connector](https://colinbourassa.github.io/car_stuff/images/ftdi_with_tts_rear.jpg)

That's it. The cable should now allow this software to communicate with the 14CUX.

## Technical background ##

This section explains the reasons behind the unusual signalling characteristics of the 14CUX serial port. The output from the microprocessor (MPU) in the 14CUX's ECU looks like this:

!(https://colinbourassa.github.io/car_stuff/images/14cux_serial_schematic.png)

The Q10 transistor acts like a switch. When the signal from the MPU is high (5V), the switch is ON and the low side of `R150` is shunted to ground, so Pin 9 is zero volts. When the MPU signal goes low, the switch is OFF and the low side of `R150` is pulled up, so Pin 9 goes to 12V. This transistor circuit is designed to buffer and protect the MPU signal, but it also inverts and changes the voltage level. This is why the FTDI USB device must have its RxD signal inverted (to match the 14CUX's inverted TxD.) However, because the FTDI converter is a 5V device, the 12V signal from the ECU must be attenuated with an external resistor to prevent damage. I've found that the 5V signal from the FTDI converter is sufficient to drive the RxD line in the 14CUX.

The resistor needs to be about 400Ω and soldered between the ECU's output signal and ground. The total resistance will be 400Ω + 100Ω + 500Ω (1000Ω total). The voltage across the 400Ω resistor will be about 4.8V (400/1000 x 12V). The resistor will dissipate only about 57mW when the output goes high (and since the idle state is low, the average will be much lower.) The instructions above recommend 390Ω as a resistor value because it's a common rating.

The serial port pins are brought out from the harness via the "Data Link Connector", which is C245 in the Land Rover service documentation. Its grounding plug is connector C244.
