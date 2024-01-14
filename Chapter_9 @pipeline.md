# Pipelining
Pipelining is executing multiple instructions in one go.
$$T_{i}=\frac{T_{u}}{P_{s}}$$ Where $T_{i}$ is time per instruction, $T_{u}$ is unpipelined execution time, and $P_{s}$ is pipeline stages.

Recall [Fetch-Decode-Execute cycle](Chapter_4%20@basic_architecture.md#Fetch-Decode-Execute%20cycle). While the processor is decoding or executing, you can fetch a new instruction while waiting. This is the process of pipelining.

## Non-pipelined CPUs
In early CPUs, the CPU cannot use pipelines due to limitations in VLSI technology. Due to that, processors cannot start the next instruction until the current one finishes. These cause idling, that reduces CPU performance.

## Pipelined CPUs
Pipelined CPUs can have different stages running simultaneously. This reduces idling, and hence increase CPU performance.

## Branch prediction
An issue arises when using pipelining, when we face a conditional jump. Without evaluating the comparison instruction, the CPU has to pre-fetch more instructions in order to utilize the benefits of pipelining and reduce the time a branch takes.

Branch prediction is utilized to guess which branch might be taken, based on current states, as well as historical data.
### Cycle count
Each instruction takes a certain amount of time to execute, and may not necessarily take only 1 clock cycle, depending on how complex the instruction is. Hence, each instruction has a cycle count, that basically tells us how much cycles an instruction should take when being executed in the CPU.

## Pipeline hazards
### 1. Structural
Structural hazards include resource conflicts, unsupported instructions, etc. This can be resolved by duplicating resources.

An example of resource conflict will be if fetching/writing uses the same memory bus. This makes it impossible for it to do both at the same time. It can be resolved if we have separate caches(one for data, one for instructions).

Another issue that might arise is when an instruction takes multiple clock cycles to execute. This means that the next instruction will have to wait until the current instruction finishes execution in order to start execution.
### 2. Data
Data hazards can happen when the current instruction relies on the output of the previous instruction. For example:
```assembly
mov AX, 0x23
xor AX, 0x41
sub BX, AX
```

In a pipelined machine, when the first instruction is executed, the result still has to be written back to `AX`, which takes another clock cycle. However, the next instruction **requires** the result of the previous instruction which is getting written. Hence, `xor AX, 0x41` can only be executed **after** `AX` is written with the new value, a cycle later. Hence stalling the pipeline.
### 3. Control
Control hazards are hazards that arise from changes in flow. Other then jump, call, and interrupt routines also interrupt the instruction flow.

Some ways to resolve control hazards include [Branch predictors](#Branch%20prediction)(both static and dynamic) and instruction buffers.

Instruction buffers are storages that store instructions, before it gets passed into the pipeline. It checks for branch instructions, and if there is, it fetches and stores both adjacent and and targeted instructions into different instruction buffers. Whatever the outcome of the conditional check, the instructions would have been loaded and ready.

#### Control Hazard Prevention Strategies
Based on analyzing programs, we have the following data
- 20% of the program instructions are branches
- 80% are conditional(of the 20%)
- 75% of the time, they are taken, 25% not

There are two methods of prediction: Static, and Dynamic.

In dynamic prediction, the branch predictor checks guesses if the conditional branch will be taken or not. In static prediction, the binary is analyzed beforehand, and guesses are made depending on the branch instruction, and depending on if its a forward or backward jump.

##### Dynamic prediction
Used when the program is running. Dynamic prediction is generally better then static prediction, but increases hardware complexity which also increases cost.

Dynamic prediction takes to account the following
- Branch history(if past branches have been taken, or not)
- How the program is being run

###### Dynamic branch prediction(1-bit)
In a 1-bit dynamic branch predictor, it can only store the state of the previous branch, and predicts the next branch based on that. The state is then updated after the branch.

###### Dynamic branch prediction(2-bits)
In a 2-bit dynamic branch predictor, it has 4 output states(who could've guessed?) being Strongly taken/not taken, and Weakly taken/not taken.

It takes the history of two branches, and predicts if the branch is taken or not, depending on the history. It favors the result of the previous branch if status is weakly taken/not taken.
