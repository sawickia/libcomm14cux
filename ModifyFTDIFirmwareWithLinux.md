# ft232r\_prog #
As an alternative to the Windows-only [FT\_PROG utility from FTDI](http://www.ftdichip.com/Support/Utilities.htm#FT_Prog), the Linux utility [ft232r\_prog](http://rtr.ca/ft232r/) can be used to perform the same function. This allows Linux users to invert the FTDI converter's RXD line without any dependence on Windows.

## Building from source ##
To build ft232r\_prog from source, first ensure that you have GNU Make, gcc, and libftdi installed (along with the libftdi development headers.) If using Ubuntu, this can be done with:

`sudo apt-get install make gcc libftdi-dev`

Then, download the ft232r\_prog source archive from its web site and extract it. In the directory containing `ft232r_prog.c` and `Makefile`, run:

`make`

## Usage ##
Connect the FTDI USB converter to your computer, and run the ft232r\_prog executable with superuser privileges and the "--dump" parameter to show the current contents of the FTDI EEPROM:

`sudo ./ft232r_prog --dump`

To invert the RxD line, run it again with the "--invert\_rxd" parameter:

`sudo ./ft232r_prog --invert_rxd`

You can optionally run it with "--dump" again to verify that RXD is inverted and TXD is not. Then, unplug and re-plug the FTDI device and it should be ready to use.