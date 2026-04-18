Here is a rewritten, more conversational version of the README. I've grouped the commands logically to make it much quicker to read and easier to digest.

**I2C CPU Assembler**
Simply put, this tool takes your assembly scripts (like testfil.s) and compiles them into a binary file that the I2CCPU can understand and execute.

Here is a quick breakdown of the commands you can use in your scripts:

**I2C Commands**
These directly control the underlying I2C hardware:

START / STOP: Triggers the standard I2C start and stop conditions.

SEND: Transmits a byte.

Pro tip: Use SEND <device_id>,WR or SEND <device_id>,RD to start communication. The assembler will automatically handle the bit-shifting and append the correct Read/Write flag to the sequence for you.

Receive Commands (RX): Reads a byte and forwards it out to the AXI stream. There are four flavors depending on how you need to handle it:

RXK: Receive and ACK.

RXN: Receive but don't ACK (NACK).

RXLK / RXLN: Same as above (ACK or NACK), but these also raise the AXI TLAST signal to tell the system it's the last byte.

**Control & Logic Commands**
These are handled by the instruction decoder to manage the flow of your program:

NOOP: Do nothing, but burn one clock cycle.

WAIT: Pauses execution until an external signal is received.

HALT: Stops issuing any further commands to the I2C controller.

ABORT: Your safety net. If a command receives a NAK (like losing arbitration), the execution will immediately jump to the address defined here.

TARGET & JUMP: Since the controller doesn't support conditional logic, you can only do straight runs or infinite loops. Use TARGET to mark a line, and JUMP to loop back to it.

CHANNEL: Sets the AXI TID field so you can route your output to different targets in shared systems.

(Note: Don't worry about keeping track of memory addresses. Jump and abort locations are automatically handled based on where you place the instructions.)

**Variables**
Need a quick constant? You can easily define variables (e.g., A=0xff) and plug them into your SEND or CHANNEL commands later to keep your code clean.

**Quick Start & Testing**
You can test the assembler and check your work right from the command line:

View Help Menu: i2casm -h

Compile a Script to Binary: i2casm testfil.s -o dump.bin

Disassemble (Verify your compiled binary): i2casm -d dump.bin
