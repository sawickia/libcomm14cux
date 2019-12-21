# Overview #

Libcomm14cux is a cross-platform library that is capable of communicating with the Lucas 14CUX engine management system via its diagnostic serial port. It supports both reading and writing arbitrary memory locations in the ECU (although the contents of the ROM cannot be changed in this way.) Using this software with a 14CUX requires an appropriate hardware interface, which is described on the HardwareInterface page.

In most places, including the United States, the 14CUX system is most well known for having been paired with the Rover V8 engine in Land Rover vehicles between 1990 and 1995. Worldwide, it appeared in vehicles produced by a number of low-volume manufacturers, such as Morgan, TVR, and Ginetta.

The measured values for road speed, engine speed, fuel temperature, coolant temperature, and other factors are always stored at the same locations in the ECU's RAM, so these can be read and returned by libcomm14cux. In fact, the entire ROM (16KB in total) can also be read through the serial port, because it is mapped into the processor's address space at $C000.

The libcomm14cux library by itself doesn't have a user interface, although it does come with a very simple command-line utility named "read14cux". This is capable of reading and displaying bytes from a given offset. Libcomm14cux has a sister project named [RoverGauge](https://github.com/colinbourassa/rovergauge), which uses this library to retrieve data that is then displayed graphically with gauges.
