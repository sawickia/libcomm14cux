# Frequently Asked Questions #
**Q:** Is this an alternative to OBD-II code readers or OBD-II diagnostic software?

**A:** No. The 14CUX system doesn't conform to the OBD-II standard. This software uses a library that I wrote specifically to communicate with the 14CUX, using the ECU's unusual baud rate and proprietary software protocol. The details of all of this were discovered through reverse-engineering the code in the 14CUX PROM.

As far as I know, this library is only useful for communicating with the Lucas 14CUX (and probably the earlier 14CU.) If you are aware of any other engine management system that uses the same software protocol, please let me know; it might be possible to expand the capability of the library to other systems.


---


**Q:** Can this software be used to modify the code or data in the ECU (such as the fuel maps)?

**A:** Unfortunately, no. When the ECU is running, it reads fueling values from the PROM. Modifying the PROM requires removing and reprogramming the chip.