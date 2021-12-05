# Introduction
The aim of this workshop is to implement a RISC-V based processor.

# Introduction to RISC-V ISA and GNU compilar toolchain

ISA - Instruction Set Architcture - at the top level is used to interact with the computer/CPU. For example, if a C program needs to be run on a computer hardware, then 

- first the C-program it is compiled to an assembly language program(here, RISC-V). 
- then the assembly language program is converted into machine language(1's and 0's).
- finally, these 1's and 0's are understood by the computer hardware/electronic circuit

RISC V - is in hexadecimal which needs to be converted to binary. This is done using the Assebmbler.
Another interface is required between the RISC-V architecture and hardware layout, which is the HDL - Hardware descriptive language. This is used to implement the required hardware.

So, we need to implement the RISC-V specifications using RTL and then we can use the RTL2GDS flow to get the final layout GDS.

## Applications to Hardware
Application Software, like Microsoft Word and VLC player, runs on computer hardware. Lets try to track this flow,
- Application interacts with System software(OS, Compiler, Assembler)
- OS --> converts the App into C, C++, VB(Visual Basics) or Jave program.
- Compiler --> converts these programs from OS into instructions, i.e., the executable(.exe) files.
   The instructions depend on the hardware were have in the computer.
   If a ARM hardware is used, then the instructions will be using ARM instruction set. We are going to implement in RISC - V hardware, so we will have the instructions in RISC-V instruction set.
- Assembler --> Converts the instructions to into binary language / machine understandable language.

- Finally, we need a hardware which understands the RISC-V instruction specs, which is implemented using a HDL.

The hardware implementation consists of the following steps:
- RTL implementation -> where hardware is implemented using a RTL descriptive language. (We are going to use TL verilog !)
- Synthesis -> This RTL code is synthesised into a netlist, which contains gates and circuit description.
- Physical Design implementation -> Implementation of the netlist into GDS.

## C - Program
To understand the complete flow from sotware to hardware, we start with a C- program.
C-program to calculate sum from 1 to N - sum1toN.c:

![image](https://user-images.githubusercontent.com/94952142/144735874-b638da4d-b507-4e96-8114-f79cc00bd90a.png)

To run the program, first, compile the program using `gcc` and then execute the generated `a.out` file :
![image](https://user-images.githubusercontent.com/94952142/144735994-9b288e2f-39a1-4edc-a979-d8bb9f2adef0.png)

## C- program compilation using riscv compiler
The C-program can be compiled using RISC V compiler. 
To install the RISC V GNU compliler run the following script in your terminal:
[https://github.com/kunalg123/riscv_workshop_collaterals/blob/master/run.sh](https://github.com/kunalg123/riscv_workshop_collaterals/blob/master/run.sh)

You may not see the risc-gnu compiler command in terminal when you reopen the terminal session. This is caused because the $PATH variable is not pointed to the installed directory. You can fix this by running the following command :
![image](https://user-images.githubusercontent.com/94952142/144735811-761c2b87-824c-43af-928a-9708e79d6bf6.png)

Here, the `$pwd` is your install directory.

To run compiler, we use either of the following commands :
```
$ riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1toN.o sum1toN.c
$ riscv64-unknown-elf-gcc -Ofast -mabi=lp64 -march=rv64i -o sum1toN.o sum1toN.c
```

More details of the command can be found at [https://www.sifive.com/blog/all-aboard-part-1-compiler-args](https://www.sifive.com/blog/all-aboard-part-1-compiler-args)

## Diassembling the assembling code
This generates the sum1toN.o file which contains the assembly level code. To see the instructions, we need to disassemble the code. For this we will be using the following command (Its always better to redirect the output to a file for easy debugging) :

![image](https://user-images.githubusercontent.com/94952142/144736183-7100ce5d-ea5a-4ae8-a0ed-c171bcb94063.png)

The file contents looks something like the following :
![image](https://user-images.githubusercontent.com/94952142/144736200-9680d9fa-ba68-4a5a-971d-17b5b4e33b46.png)

The disassemble is performed for files run using `-O1` and `-Ofast` option. It was seen that `-Ofast` disassembled code has less number of instructions.

## Spike simulation and debugging
Spike is installed when the run.sh script was executed previously.
Spike tool can be used to simulate the assembly level code.
![image](https://user-images.githubusercontent.com/94952142/144738197-f7a4593f-088b-4519-9a82-db36e768cf82.png)

It can also be used for debugging the code. The main commands used for debugging can be seen in the below snap :

![image](https://user-images.githubusercontent.com/94952142/144736542-51747c4f-5397-4857-8710-c3ee9391fb34.png)

## Understanding the Unsigned and Signed Integers
To understand the unsigned and signed integers, we write a C program and compile it. Following snap shows the C - program to print the range for signed int and the output.

![image](https://user-images.githubusercontent.com/94952142/144736884-74c46ffb-acd1-4816-b711-e01e44ef8f40.png)

Similarly ranges for all the following datatypes are checked:
![r1](https://user-images.githubusercontent.com/94952142/144736958-51f6c0d7-7d47-492f-bfaf-9b7839706950.png)

- Make sure to use the right formatter to print the datatypes.

# ABI - Application Binary Interface
It is also called as System Call Interface. This is a feature used by the system software to directly access the registers in the hardware.
Let's get to know something about the RISCV registers.
- RISC V has got 32 registers.
- Width of each register is XLEN-1, where if
  - XLEN = 32bit --> RV32
  - XLEN = 64bit --> RV64
- RISC V belongs to the little endian memory addresing system, so
  - m\[0\] - LSB 
  - m\[7\] - MSB
  - This is reverse in case of big endian memory addressing system.

Following shows the RISCV register calling convention:

![image](https://user-images.githubusercontent.com/94952142/144737218-638ee9e4-3a18-4366-9560-e37e428a0781.png)

## Creating Assembly level code using ABI instructions:
- C program :

![image](https://user-images.githubusercontent.com/94952142/144746760-aaf8f486-5d50-4aa9-8b8a-e6cda735e2e1.png)

- load.S :

![image](https://user-images.githubusercontent.com/94952142/144746785-860039eb-8c60-495d-9fa7-dc1e45672dba.png)

- Creating the disassembled instruction file :

![image](https://user-images.githubusercontent.com/94952142/144747051-513e2f6e-d1ae-4b4c-b059-36996bce3c68.png)

![image](https://user-images.githubusercontent.com/94952142/144747086-5a1d6448-11d1-40a6-8f32-a023ab00d012.png)


## Running C-program on PicoRV32
We run the compiled assembly level C program on picoRV32, which is a RISCV core.

# DIgital Logic implementation using TL Verilog

## TL Verilog
This is the Verilog implementation of TL-X.
TL-X is a set of HDL (Hardware Description Language) features defined as extensions to existing HDL languages, including Verilog (as "TL-Verilog"), VHDL (as "TL-VHDL"), and SystemC (as "TL-C"). This is a wrapper to any HDL to extend it with transaction-level modeling. This makes it more powerful and has a significant code reduction as compared to other HDL languages.
A transaction, in this methodology, is an entity that moves through structures like pipelines, arbiters, and queues, A transaction might be a machine instruction, a flit of a packet, or a memory read/write. Transaction logic, like packet header decode or instruction execution, that operates on the transaction can be placed anywhere along the transaction's flow. Tools produce the logic to carry signals through their flows to stitch the transaction logic.

## Makerchip
Makerchip provides free and instant access to the latest tools from your browser and from your desktop. This includes open-source tools and proprietary ones. We can code, compile, simulate, and debug Verilog designs, all from the browser.

In this workshop, our aim is to create a RISCV core using TL Verilog. So, to get the understanding of basics of Digital design and a feel of Makerchip IDE to write TL-verlog codes, we start with Combinational Circuits.

## Simple Calculator :
We start by implementing the basic logic gates and then finally desgin a simple calculator. This performs a add, sub, mult and div operation on the two input and gives one of the result as output based on the select line value.
![image](https://user-images.githubusercontent.com/94952142/144738064-2f3aef06-ee02-4c77-abef-7df0a755f510.png)

Next, we use the stored output as input by feeding the output back as one of the input. This way the output stored till the next clock edge can be used as input. 

![image](https://user-images.githubusercontent.com/94952142/144740130-0b4717cc-a87b-461b-b68a-2bea05336ad4.png)

If there is too much path delay between two flip flops, then this circuit becomes slow. This happens when the signal has to travel from one end to another. So, we need to use slow clocks. This problem is overcome by pipelining the logic. Pipelining is done by dividing the signal path and introducing flip-flops at each stage. By doing so, we can increase the clock speed.

Pipelined calculator is implemented as follows :
![image](https://user-images.githubusercontent.com/94952142/144741050-5a7e069c-002a-48af-bbc4-72fc47a02938.png)

We can control the stage or an logic based on a condition, hence making sure the logic is working only when valid. The validity can be implemented as follows:
![image](https://user-images.githubusercontent.com/94952142/144741163-4c8ac18c-de5b-45c2-8b84-9d10a9847b5a.png)

Finally, we add a memory block to store the output.
![image](https://user-images.githubusercontent.com/94952142/144741245-1fdddc66-7c11-41a3-8413-f11b33745586.png)


# Basic RISC V CPU microarchitecture
We now will implement the RISC V CPU. The top level block diagram is as follows:
![image](https://user-images.githubusercontent.com/94952142/144741397-debf1a8b-6f8a-478f-9ab5-6b5cc3d5169b.png)

The pipelined logical flow plan for the same looks as follows:

![image](https://user-images.githubusercontent.com/94952142/144741433-142d3229-c7e2-406e-98ab-d809fd040b6f.png)

First the program counter and cicuit to fetching the instruction from memory is implemented. This process is called Fetching.

Then the decoding cicuit, which will decode the instruction is implemented. Here, the instruction and data is separated and the type of instruction is understood. This stage is called Decoding.

Next, the logic for reading from registers is implemented. The register addresses from the previous step is taken and the contents of the registers are accessed.

After this, the logic to execute the instruction is implemented. This is similar to the calculator we implemented in the beginning. This can also be seen as ALU. The types of operation that this ALU can do is defined and then the ALU multiplexer is defined. Based on the instruction the operations of the multiplexer can be controlled.

Then the logic to write into the registers back is implemented. Here, the register address to write into is taken from the Decoding step and then the output or result from the ALU is written into the register.

At last, we add the validity and branch target to compute the program counter. The final pipelined flow is as follows :

![image](https://user-images.githubusercontent.com/94952142/144744409-e209e4e1-afec-4a8f-88cb-f31383873ace.png)

The final code looks as follows :

![image](https://user-images.githubusercontent.com/94952142/144742773-4bf68b6b-cc5f-4b0c-bd03-2287d3ff8a62.png)

![image](https://user-images.githubusercontent.com/94952142/144742791-8b5c3e8b-2a03-42e5-8c47-df5212b2b647.png)

![image](https://user-images.githubusercontent.com/94952142/144742803-25d08b2a-5eb8-4f49-bc8b-317503837a94.png)

![image](https://user-images.githubusercontent.com/94952142/144742817-6763bc77-98d8-458d-bda4-4a45d21ed584.png)

![image](https://user-images.githubusercontent.com/94952142/144742827-e7adfd73-4a94-4d49-b1a5-05c96378aea5.png)

## Pipelining Hazards
Two of the pipelining hazards are discussed and solutions for the same are provided. 
- Register Read after Write hazard.
  This happens when there is a read instruction from a register, where the register write operation happened in just the previous instruction. So, the read operation has to wait another cycle to get the value written to the register, which can affect the flow.
  This can be solved by introducing the Register File Bypass. Here, if the current instruction is read and the previous instruction is write, and the registers are same, then we try to bypass the register read and take the previous ALU output as input for the ALU in the present instruction.
  
- Branch target is calculated in the 3rd stage of pipeline, but the PC that has to be calucated for the next instruction needs to wait for two cycles to get the branch target from the previous instruction. This creates problem is calculating the right program counter.
  This is solved by checking for the previous two taken branches.

## Completing the RISCV CPU
All the instructions are decoded and complete ALU logic is implemented. At the end, the D-Memory block is added and load and store logics are implemented to get the complete CPU.
Complete CPU code :
![image](https://user-images.githubusercontent.com/94952142/144743934-d6406233-b25c-4949-be62-b23c05172ec9.png)
![image](https://user-images.githubusercontent.com/94952142/144744250-1c50db7f-c87e-492b-b5b4-dfb82bc255e5.png)
![image](https://user-images.githubusercontent.com/94952142/144744261-f2dcc389-c889-4b0a-a524-15c78903a620.png)
![image](https://user-images.githubusercontent.com/94952142/144744271-b6aa7ac9-2cfa-4be1-b259-29c1ed453534.png)
![image](https://user-images.githubusercontent.com/94952142/144744277-96be59b3-37d7-4c35-aa85-a774932ca15e.png)
![image](https://user-images.githubusercontent.com/94952142/144744291-a0dff973-f081-403e-89ac-69bfd726dd3e.png)
![image](https://user-images.githubusercontent.com/94952142/144744308-2333a67d-bffe-41ed-ae7a-07026b018620.png)
![image](https://user-images.githubusercontent.com/94952142/144744318-4815d9ad-4c7f-43fd-ab35-ffc06e70eb03.png)
![image](https://user-images.githubusercontent.com/94952142/144744346-14ac35ed-a3d9-4847-bf57-408f906dbade.png)



# Acknowledgement
- [https://www.sifive.com/blog/all-aboard-part-1-compiler-args](https://www.sifive.com/blog/all-aboard-part-1-compiler-args)
- [https://riscv.org/wp-content/uploads/2015/01/riscv-calling.pdf](https://riscv.org/wp-content/uploads/2015/01/riscv-calling.pdf)
- [https://arxiv.org/abs/1811.01780](https://arxiv.org/abs/1811.01780)





