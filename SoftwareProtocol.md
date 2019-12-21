_This is a mirror of the information in [my personal webspace](https://colinbourassa.github.io)._

# Software protocol #

The protocol works as follows: the client sends a series of bytes to the 14CUX, which together specify the memory location to read or write and the number of bytes to read (if reading). When reading, a total of three bytes are sent before the 14CUX begins responding with the requested data. When writing, a total of four bytes are sent; the first three set the address (as with the "read" command) and the fourth is the value that will be written to the specified location.

With one exception, each byte sent to the 14CUX will be echoed back and the client should wait to receive each echo before sending the next byte. The single exception to this is the final byte of the "read" command -- instead of its echo, the 14CUX will immediately begin sending the requested data.

After a client has started sending a command, it is important that the remainder of the command be sent quickly: to avoid the possibility of waiting indefinitely for a partially-completed command, the 14CUX starts a timer when the first byte is received and can time-out if the client waits too long to finish.

The amount of time required for the 14CUX to send a character over the serial port may change depending on its current state; for example, read requests may be serviced slightly faster before the engine has been started due to the reduced load on the processor.

## Command byte sequence and structure ##

The first command byte (call it Command Byte A) contains three fields: (a) a fixed identifier of 0, (b) the quantity of bytes to read (if applicable) and (c) the top two bits of the address that will be read/written. If performing a write command, the Quantity field is ignored. This command byte is structured like so:

| **7** | **6** | **5** | **4** | **3** | **2** | **1** | **0** |
|:------|:------|:------|:------|:------|:------|:------|:------|
|Fixed ID (set to 0)|Quantity| | | | |Bits 15:14 of address| |
_Command Byte A_

After the 14CUX receives this command byte, it will be echoed over the serial port. The client should not send the next command byte until the echo of the first is received.

Because of the limited width of the Quantity field, the 14CUX uses an encoding scheme to select a quantity. If reading between 1 and 16 bytes, the Quantity field must be set to (n - 1), where n is the number of bytes desired. Otherwise, n must be one of four predefined values and use the corresponding 5-bit code:

| **n (decimal)** | **Encoding (binary)** |
|:----------------|:----------------------|
|1 through 16|00000 through 01111|
|80|10000|
|100|10001|
|400|10010|
|512|10011|

The second command byte (Command Byte B) sets the next eight bits of the read/write address. This byte is also echoed back over the serial link upon receipt, and the client should wait to receive the echo before continuing. This byte has the following structure:

| **7** | **6** | **5** | **4** | **3** | **2** | **1** | **0** |
|:------|:------|:------|:------|:------|:------|:------|:------|
|Bits 13:6 of address| | | | | | | |
_Command Byte B_

The third command byte (Command Byte C) contains the last six bits of the address, and it finally determines whether this is a read or a write operation:

| **7** | **6** | **5** | **4** | **3** | **2** | **1** | **0** |
|:------|:------|:------|:------|:------|:------|:------|:------|
|11 for read or 10 for write| |Bits 5:0 of address| | | | | |
_Command Byte C_

If the third command byte indicates a read operation, then the command string is complete and the 14CUX will begin to respond with the requested byte(s) from memory. Note that the third command byte is **not** echoed in this case.

Otherwise, if the third command byte indicates a write operation, it **is** echoed, and the client should wait to receive the echo before it sends the fourth and final byte, which is the value to be written. The 14CUX echoes this last byte as well to confirm completion of the write operation.

An example: to read 512 bytes from location $C562, the bytes 4F 15 E2 should be sent to the 14CUX.
