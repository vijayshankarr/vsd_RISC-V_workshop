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

##Combinational Circuts:
We implement the basic logic gates and then implement a simple calculator.
![image](https://user-images.githubusercontent.com/94952142/144738064-2f3aef06-ee02-4c77-abef-7df0a755f510.png)




# Acknowledgement
- [https://www.sifive.com/blog/all-aboard-part-1-compiler-args](https://www.sifive.com/blog/all-aboard-part-1-compiler-args)
- [https://riscv.org/wp-content/uploads/2015/01/riscv-calling.pdf](https://riscv.org/wp-content/uploads/2015/01/riscv-calling.pdf)
- [https://arxiv.org/abs/1811.01780](https://arxiv.org/abs/1811.01780)





