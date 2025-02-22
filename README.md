# My Experience with Computer Architecture

## My Experience Learning the Basics of Digital Electronics and How a Processor is Built Using 'Logisim Evolution' in One Week

My ultimate goal is to learn about 'SoC' development at the 'Bare-Metal' level. To achieve this, I decided to start by developing a basic processor using a simulator.

### First Day:
On the first day, I began familiarizing myself with the Logisim Evolution tool [Analog Gates, Multiplexers, Arithmetic, Memory, ...] and started developing parts of the processor. The processor was designed with a structured approach, dividing it into smaller components.

I also built the 'ALU' and the 'Register Bank'. This part was far from simple and left me confused while trying to grasp the expected complexity of a processor.

### Second Day:
On the second day, I started planning the cycle and machine timing (which I admit was a bit difficult to understand at first, especially without any prior knowledge or examples). At this stage, I struggled to decide whether to make a 'single-cycle' processor (which seems to be the most common for study purposes) or a 'multi-cycle' processor (which appeared to be a more complex yet more challenging project). Logically, I chose to go with 'multi-cycle'.

By the end of the day, I had completed only the following components: 'IR', 'PC', and 'Cycle Counter'. Later, I would discover that these are among the most important parts for fetching instructions from ROM.

**Note:** There are several types of memory that can be used for instructions, such as:
- ROM
- PROM
- EPROM
- EEPROM

Each has its own method of recording data.

### Third Day:
On the third day, I finalized the general communication between each component and the initial functionality. I also gained a better understanding of how the overall clock and response decoding work.

### Fourth Day:
On this day, I completed the entire processor and established communication between the ROM and RAM. Additionally, I clearly defined the instructions for the 'Control Unit' (CU), which I found to be the most tedious part of the process.

## Processor:
The processor operates based on cycles, where each clock pulse updates a specific component until the final step, after which it returns to the beginning. During each machine cycle, the processor essentially:
- Determines the memory address in ROM to be loaded.
- Loads the new value into the Control Unit.
- The Control Unit processes this value and makes a decision based on the input binary code.
- Updates the RAM with a new memory location, a new input value, and read/write permissions.
- Finally, it updates the register bank, completing the last clock cycle and machine time.

## ALU:
The ALU has two 8-bit inputs (A and B) and a 3-bit input (OpCode). It determines the operation to be performed based on the OpCode. The OpCode input returns a 3-bit value passed to a MUX, which selects the output response. In essence, all operations are performed simultaneously, but only the one chosen by the MUX is output. Example:

```
A = 00000011
B = 00000001
OpCode = 011 == 3 == (ADD or Addition)

Output:
R = 00000100 (Common response)
NOT_R = 11111011 (Negated response)
ND = 0 (Most significant bit)
ZR = 0 (if R == 0)
CARRY = 0 (If there was overflow)
```
[image]

## Register Bank:
The register bank receives the following inputs:
- **DATA_in** (Value to be stored)
- **SLC_in** (Which register to store in)
- **SLCA_out, SLCB_out** (Which register to output to A and B)
- **Write permission** for the respective register

The outputs are the values stored in the respective registers (A_out and B_out). The register bank essentially stores temporary values for use in the following machine cycles.
[image]

## PC (Program Counter):
The PC is relatively simple. It determines the new ROM memory address to be read. It has the following inputs:
- **SLC_JMP (SELECT_JUMP)**: Decides if the next instruction should be a direct memory address or an incremented value.
- **JMP_in (JUMP_in)**: Specifies the memory address if jumping.
- **FREEZE**: Freezes the PC.
- **RESET**: Resets the PC.
- **CLK (CLOCK)**: Updates the PC output.

This output is directly connected to the ROM address input.
[image]

## IR (Instruction Register):
The IR updates the value of the Control Unit (CU) by fetching the value directly from ROM. The response to the Control Unit is updated on the **CLOCK (IR_write)** signal.

## CYCLE_COUNTER:
This is one of the most critical components, as it updates the entire system. It controls the processor by using cycles and determines machine time.

- **CLK** = Clock signal for the new instruction/cycle.
- **HALT** = Stops the processor (similar to hibernation).
- **RESET** = Resets the machine time to the first cycle.

Each output represents the CLOCK/update for a specific component. In this specific example, the sequence is:
- **Cycle 1** = Updates the PC.
- **Cycle 2** = Updates the IR.
- **Cycle 3** = Updates the RAM.
- **Cycle 4** = Updates the register bank.
[image]

## CU (Control Unit):
This is the most complex part in my opinion. The Control Unit (CU) determines what actions should be taken based on the input from ROM.

The ROM input is a 16-bit binary value, where each segment defines a function. The first 3 bits define the function to be executed.

### Functions:
- **ADD (000):**
  - Performs addition between two registers with the following flags:
    - bit 3-5 = ToSave (Where to store the sum result)
    - bit 6-8 = VAR_A (First register)
    - bit 9-11 = VAR_B (Second register)
    - bit 12 = Save (Whether to store the result)
    - bit 13-15 = 'arithmetic' (Which ALU response to store [R, NOT_R, CARRY, ...])

- **SUB (001):**
  - Works like ADD, but performs subtraction.

- **AND (010):**
  - Works like ADD, but performs a bitwise AND operation.

- **OR (011):**
  - Works like AND, but performs a bitwise OR operation.

- **LOAD (100):**
  - Loads a value from RAM into a chosen register.

- **STORE (101):**
  - Stores a value from a register into RAM.

- **HALT (111):**
  - Sends a signal to the cycle counter to stop execution.
[image]

## Processor:
In the end, you will have an example like the one below, where execution always starts with the cycle counter and sends instructions to the rest of the circuit.
[image]

Lastly, the final image shows the complete project with ROM, RAM, and the general clock/reset buttons.
[image]

Before judging or criticizing, please remember that this is my first project to learn about computer architecture in less than a week. I imagine there are many errors and skipped steps, but that's part of the learning process.


