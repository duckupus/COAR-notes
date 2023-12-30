# IO Basics
Computers are made out of 3 fundamental elements
1. INPUT
2. PROCESS
3. OUTPUT

## I/O
### Memory vs IO
For example,
1. Memory uses `MOV AL, [200h]`
2. IO uses `IN AL, 200h`

### Interrupt driven I/O
- **Processor** issues a READ/WRITE command to **I/O** module
	- Continue execution of instructions
- **I/O** gets command from **Processor**
	- gets required read/writes required data to said hardware
	- sends interrupt signal back to **Processor**
- **Processor** receives interrupt signal. Saves context of current program, and reads data from **I/O**
- **I/O** sends data to **Processor** on data bus
- **Processor** restores program context, and continues execution

In the case of multiple I/O modules, the processor will have a hard time figuring out which module gave an interrupt. This can be resolved in a few ways.
1. **Multiple interrupt lines.**
The easiest way to resolve this issue is to simply add more interrupt lines, for each module required.
2. **Software poll.**
When processor detects an interrupt, branches into ISR routine to check the module that triggered the interrupt.
3. **Daisy chain**(vectored interrupt)
Connecting inputs in series. It's a hardware polling method. When processor detects an interrupt, it iterates through all I/O modules until it reaches the requesting module. A unique vector is then returned via the bus from the I/O module. The Processor then uses the vector to interact with the module.
1. **Bus arbitration**(vectored interrupt)
One module can raise an interrupt at a time. Interrupting I/O Module gains control of the Bus before triggering the interrupt. After acknowledging the interrupt, the processor receives a vector that is used to interact with the I/O module.

### Direct Memory Access(DMA)
Used for transferring a lot of data. Additional module on system bus that transfers data on behalf of the processor. It can transfer data to/fro memory over system bus directly.

Only uses the bus when the processor isn't using it. However, can force the processor to suspend operations in a technique called Cycle stealing.

## External Interconnection Standards
I'll be skimming through these, so speeed
### USB
Industry standard that provides charging and data transfer capabilities.

| Gen     | info           |
| ------- | -------------- |
| USB 1.0 | Speed: 12Mbps  | 
| USB 2.0 | Speed: 480Mbps |
| USB 3.0 | Speed: 4Gbps   |
| USB 3.1 | Speed: 9.7Gbps |

<sub>While links to every single website ever aren't optimal, here's a pretty good reference for USB specs. https://fabiensanglard.net/usbcheat/index.html</sub>
### Thunderbolt
Developed by Intel + Apple, Thunderbolt is a general-purpose connection technology. It is able to be used in many scenarios that would usually require different cables.
In Thunderbolt 5, it supported 80Gbit/s bidirectional bitrate.

### Ethernet
Wired networking technology that supports 400Gbit/s(as of writing), with 1.6Tbit/s coming soon

### Wi-Fi
Wireless networking technology that can support 9.6Gbit/s(2019) in optimal conditions.