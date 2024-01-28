# Types of microprocessor architectures
## Von Neumann Arch
- Doesn't differentiate program and data memory; simplified memory interface
- Same memory holds data and instruction code
- High-performance systems will run into the Von Neumann bottleneck, which only allows one instruction or piece of data to be executed at one time.
- Capacity between memory and CPU is limited. Channel capacity can be improved by making it faster, or increasing width size. However, the CPU is still limited in memory accessing.
- 
## Harvard Arch
- Instructions and data split apart
- Security provided by preventing data from being executed by hardware implementation
- Two sets of address and data buses between CPU and memory
- Most Advanced processors use DSPs for streaming data for more memory bandwidth

## Pipelined CPUs
- Increase processing power
- 80486 has 5 pipeline stages(80386 did not)
- Pentium 4 had a 20 stage pipeline with branch prediction
	- In an event of a misprediction of the branch, the whole pipeline has to be flushed

## Parallel Processor Systems
### SISD(Single Instruction, Single Data) stream
- One processor executes one instruction stream, and works on data in one memory area.
### SIMD(Single Instruction, Multiple Data) stream
- One instruction executes on multiple processing units at the same time, but each unit handles different data.
- Most GPUs use SIMD
### MISD(Multiple Instruction, Single Data) stream
- A stream of data is sent to a set of processors that each execute a unique instruction.
- Not implemented in commercial use

### MIMD(Multiple Instruction, Multiple Data) stream
- Different processors execute different instructions on different data

### SIMD
SIMD works well when there is no/little data dependencies. For example, calculating the dot product of two factors can be executed using SIMD

Maximum speedup obtainable in parallel architectures are limited by the Serial portions, also known as a serial bottleneck.

Recall: Amdahl's law: $$S(N) = \frac{1}{((1-P)+(\frac{P}{N}))}$$ where $P$ = Parallelizable fraction of a program and $N$ = Number of processors
### MIMD
In MIMD, there are a few ways processors communicate with each other.
1. Direct communication via a network connecting processors
2. Shared memory that all processors are able to access
	1. Most commonly implemented as symmetric microprocessor(SMP), being multiple processors that share the same memory with a shared bus
	2. Memory access timings are(mostly) the same for each processor
	3. Non-Uniform Memory Access(NUMA), makes access to local memory(of the processor in question) to be faster then accesses to non-local memory(of other processors). Benefits in certain workloads(like when data is associated strongly with certain users, etc.)
3. Advantages
	- Programming can be focused on a single processor style
	- Communication between processors are efficient
4. Disadvantages
	- Need to sync accesses to shared data
	- Difficult to scale because of memory contention limits
#### Distributed memory
Each processor has memory and interact with each other via paths or a network.
##### Linear connection
- Maximum connection distance of `N-1` in `N` amount of processors
- For 8 processors, maximum distance is 7
##### Cube connection
- Connected in a cube form. 
- For 8 processors, maximum distance is 3
##### Advantages of Distributed memory
- Scalable design
- No sync issues
##### Disadvantages of distributed memory
- load balancing
- Messages passing through processors might form a deadlock
- Shared data needs to be copied between processors

## Distributed Multiprocessors
Distributed Microprocessors System is a system of many independent processors that are networked together although they might not be physically close to each other.

# Distributed Computing
Distributed Computing is when multiple software components run on different machines, but work together as one system

Distributed Computing is not tied by geography. The computers can be connected via local network, or far away, connected via WAN.

A distributed system can have any possible network configuration. The idea of distributed systems is to run multiple computing units as one system.

## Advantages of distributed computing
- Scalability: We can add more machines if we need them
- Redundancy: As multiple machines can be providing the same system, if one goes down, the service can continue to function
- Redundancy: As multiple machines can be providing the same system, if one goes down, the service can continue to function

Computers can be running different systems or configurations. Communication is done via different techniques like SNA, TCP/IP, Ethernet, Token Ring, etc.

## Applications
Applications for distributed computing systems include:
- Healthcare and life sciences
	- Modelling and simulating data
	- Image analysis
	- Drug research
	- Gene structure analysis
	- etc.
- Engineering
	- Simulating physics, mechanics and even fluid dynamics
	- etc.
- Energy and environment
	- Monitoring sensors for pro-active risk management for dangerous places like oil wells, etc.

# Instruction Level Parallelism
- Intel's MMX technology utilizes SIMD to accelerate media and communication applications
## MMX technology
Along with MMX(MultiMedia EXtensions)
- New datatypes
- New instruction set
- 8 64-bit wide registers(MM0...MM7)

MMX instructions support
- Basic arithmetic
- Comparison
- Conversion(pack/unpack)
- Logical operations
- Shift operations
- Move register-to-register
- Load/Store

## Streaming SIMD extensions
- Uses data parallelism
- Applies same instruction to multiple pieces of data
- SIMD arches offer high performance for vector operations

Instructions supported by SIMD extensions
- Movement(_`MOVUPS`_, _`MOVHPS`_, _`MOVLPS`_, etc.)
- Arithmetic(_`ADD`_, _`SUB`_, _`MUL`_, _`SQRT`_, etc.)
- Logical(_`AND`_, _`OR`_, _`XOR`_, etc.)
- Comparison(_`CMPPS`_, _`CMPSS`_, etc.)
- Others(shuffle, conversion, cache, etc.)

SSE programming in C/C++
You can use intrinsics(function that is mapped to assembly, inlined instructions.) to use SSE.

If you want to access SSE, you can access them by including said headerfiles:
```c++
#include <mmintrin.h> //MMX
#include <xmmintrin.h> //SSE
#include <emmintrin.h> //SSE2
#include <pmmintrin.h> //SSE3
#include <smmintrin.h> //SSE4
/* etc. */
```
<sub>For the most updated headerfiles, check the documentation.</sub>

Microarchitecture support
- 8 128-bit vector registers: XMM0-7
	- Fits 4 integers(32-bit), 2 64-bit(long long), or 1 DQWORD)
	- 4 floats(32-bit)
	- 2 doubles(64-bit)

### Extra improvements
- MMX became 128-bit XMM registers, then 256-bit YMM registers
- AVX extends x86 instruction set
	- AVX2 expands int commands to 256-bits and provides new instructions
	- AVX-512 extends AVX to 512-bits

# Processors that work with AI/ML
AI/ML has become very popular recently, and with it's advent, it has been applied in many different areas. As such, recent processors has started including design features that accelerate deep learning and ML algorithms. AMX for example, is introduced to help with NLP, image recognition, and recommendation systems, among other things.