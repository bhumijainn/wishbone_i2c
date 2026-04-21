
**I2C Cores & Controllers**

This repo houses a collection of I2C cores I've built, mostly utilizing Wishbone and AXI-Lite interfaces. Whether you just need a basic master/slave setup or a more advanced, scriptable controller for telemetry, you'll find it here.

**The Basic Hardware: Master & Slave Cores**

Both of these rely on a small block of shared memory to handle transactions:

Wishbone I2C Slave: Think of this as a simple two-port memory interface (128 octets). One side talks to your 32-bit Wishbone bus, and the other sits on the I2C bus. You can read/write to both simultaneously—if there’s a collision, it handles arbitration gracefully by stalling the Wishbone line.

Wishbone I2C Master: A solid workhorse we frequently use for querying monitor EDID info. You load up the shared memory and issue a command to push/pull data to the slave.

A quick heads-up: It's hardcoded for a specific command sequence (I2C ADDR, SLV ADDR, SEND/READ data). If you have a device with a non-standard address format, this specific master won't play nice with it (though the I2C CPU below can handle it!).

**The I2C CPU (The Scripted FSM)**

This is a newer addition and a personal favorite. While I call it a "CPU," it’s really a highly capable, scripted Finite State Machine (FSM) that is absolutely perfect for telemetry and configuration tasks.

Bus Flavors: I’ve included both a Wishbone and an AXI-Lite version. Aside from the physical bus (and potential endianness quirks), the software side is identical.

How it Works: You feed it a script (either via a bus port or external memory) and it executes the I2C commands—sending START/STOP bits, reading/writing data, looping, or halting.

The Output: It kicks out a clean AXI stream containing all the bytes it read during the script execution.

Assembler Included: Head over to the sw/ directory to find a custom assembler/disassembler for writing your I2C scripts. Run make test to see it compile and reverse-engineer a test program.

**Battle-Tested Status**

These IPs aren't just sitting in simulation—they are actively deployed in real hardware. We rely on them for:

EDID handling across multiple video applications.

Reading hardware temps, DDR3 configs, and SFP+ data.

Controlling Si5324 clocks and simple I2C OLED displays.

Managing TCA9548 hubs to resolve I2C address conflicts.
