---
title: COMP2611 Final Review
categories: School Notes
date: 2018-12-07 16:02:45
tags: [MIPS, assemble]
---
@[TOC]

## Arithmetic for Computers

### Overflow Detection

Operation|Sign bit of X|Sign Bit of Y|Sign Bit of Result
--|--|--|--
X+Y|0|0|1
X+Y|1|1|0
X-Y|0|1|1
X-Y|1|0|0

### Multiplication

![1544252231121](/assets/COMP2611-Final-Review/1544252231121.png)

![1544252074288](/assets/COMP2611-Final-Review/1544252074288.png)

### Division

![1544252200005](/assets/COMP2611-Final-Review/1544252200005.png)

![1544252118812](/assets/COMP2611-Final-Review/1544252118812.png)

## Pipeline

### Stages:

**IF** : Fetch the instructions from meomry  
**ID** : Instruction decode & register read   
**EX** : Perform ALU operation  
**MEM** : Memory access (if necessary)
**WB** : Write result back to register

### Registers:

located between the stages:
​	IF/ID, ID/EX, EX/MEM, MEM/WB

### Control:

**IF** : no control signals
**ID** : no control signals
**EX** : *RegDst, ALUOP, ALUSrc*
**MEM** : *Branch, MemRead, MemWrite*
**WB** : *MenToReg, RegWrite*

### Hazards:

cause: Data dependence and Control dependence.
types: Structural hazards, Data hazards, Control hazards

#### Structural hazards

Conflict for use of memory(MEM/IF) and registers(WB/ID).

Solution:

1. separate instruction and data memories
2. **Fact: Register very fast.** Registers can Write during 1st half of clock cycle and Read during 2nd half.  

#### Data hazards

> add $s0, $t0, $t1
> sub $t2, $s0, $t3  

Solution:

1. Forwarding (partial, can't solve below code)

   > lw $s0, 20$(t1)
   > sub $t2, $s0, $t3

2. Scheduling (assume forwarding is used)

#### Control hazards

When we need to `beq` , `bne` operations

Solution:

1. Fetch instruction after branch. If wrong, flush them away.
2. Add comparator to compare earlier

###  Datapath with Hazards

####  Data forwarding

Two types forwarding: EX/MEM -> ID/EX		and		MEM/WB -> ID/EX
Using two **multiplexers** to decide what is the input of operands A and B of the ALU.
note: A is for Rs. B is for Rt.

EX forwarding:
```assembly
if (EX/MEM.RegWrite and (EX/MEM.RegisterRd ≠ 0) 
and (EX/MEM.RegisterRd = ID/EX.RegisterRs )) 
	ForwardA = 10 
if (EX/MEM.RegWrite and (EX/MEM.RegisterRd ≠ 0) 
and (EX/MEM.RegisterRd = ID/EX.RegisterRt )) 
	ForwardB = 10
```
MEM forwarding (only when **NOT** EX forwarding ):
```assembly
if (MEM/WB.RegWrite and (MEM/WB.RegisterRd ≠ 0) 
and not (EX/MEM.RegWrite and (EX/MEM.RegisterRd ≠ 0) 
		and (EX/MEM.RegisterRd = ID/EX.RegisterRs ))
and (MEM/WB.RegisterRd = ID/EX.RegisterRs )) 
	ForwardA = 01 
if (MEM/WB.RegWrite and (MEM/WB.RegisterRd ≠ 0) 
and not (EX/MEM.RegWrite and (EX/MEM.RegisterRd ≠ 0) 
		and (EX/MEM.RegisterRd = ID/EX.RegisterRt ))
and (MEM/WB.RegisterRd = ID/EX.RegisterRt )) 
	ForwardB = 01
```

#### Load-use detection

When Load-use hazard occur, we can do nothing but stall a clock cycle.
To detect it:

```assembly
Load-use hazard = ID/EX.MemRead and 
((ID/EX.RegisterRt = IF/ID.RegisterRs ) or 
(ID/EX.RegisterRt = IF/ID.RegisterRt ))
```

#### How to Stall

1. Force control values in ID/EX register to 0 :
   ​	EX, MEM and WB do EX, MEM and WB do nop (no -operation)
2. Prevent update of PC and IF/ID register
   Using instruction is decoded again
   Following instruction is fetched again
   1-cycle stall allows MEM to read data for `lw` (with forwarding)

#### Data Hazard when branch

In pipeline datapath, branch target address calculation is executed in **another ALU NOT in EXE stage**. It is **executed during ID staged.**

ID Stage: Target address calculator, Register file, Register comparator.

> add $1, $2, $3
> add $4, $5, $6
> add $7, $8, $9	# another instruction
> beq $1, $4, target	

Can resolve using forwarding. From **MEM/WB** and **EXE/MEM** to **ID**

> add $1, $2, $3
> add $4, $5, $6
> beq stalled...
> beq $1, $4, target	

Need one cycle stalled.

> lw $1,addr	
> beq stalled...
> beq stalled...
> beq $1, $0, target

Need two cycles stalled.

## Memory

RAM(Random Access Memory) technology includes two types:
1. Static RAM -- mostly used for **cache**
   0.5ns-2.5ns, $2000 -$5000 per GB
   consists only **Transistors**
2. Dynamic RAM(DRAM) -- mostly used for **main memory** 
    50ns - 70ns, $20-$75 per GB
    consists of **Transistor** and **Capacitor**

Magnetic disk: 5ms - 20ms, $0.2-$2 per GB

### Disk Sectors and Accesses

**Each sector records:**
Sector ID
Data (512 bytes, 4096 bytes proposed)
Error correcting code (ECC)
Synchronization fields and gaps

**Access to a sector involves:**
Queuing delay if other accesses are pending
Seek: move the heads
Rotational latency
Data transfer
Controller overhead

### Principle of Locality

> Programs access a small proportion of their address space at any time

1. **Temporal locality**

   Items accessed recently are likely to be accessed again soon

2. **Spatial locality**
   Items near those accessed recently are likely to be accessed soon

**This property is the KEY to memory hierarchy!**

### Memory Hierachy

Initially,
Instructions and data are loaded into **DRAM** from disk.

Upon first access,
A copy of the referenced instruction or data item is kept in **cache** 

In subsequent accesses,
First, look for the requested item in the cache
​	If the item is in the cache, return the item to the CPU
​	If NOT in the cache, look it up in the memory

We can say:

> If not found in this level, look it up at next level until found
> Keep (cache) a copy of the found item at this level after use

### Cache

**Each memory locations is mapped to ONE location in the cache.**

Three basic organizations:

1. Direct-mapped( one memory block to one possible cache block)
2. Set-mapped( one memory block to one set of possible cache blocks)
3. Fully-mapped( one memory block to all possible cache blocks)

#### Direct Mapped Cache

If the number of cache blocks (N) is a power of 2; **N=2^m**
cache_location = the low-order **m** bits of block address
e.g. map the value1200 to a 8 blocks, 32 byte/block cache.

> Block address = 1200/32 = 37
> Block number = 37 % 8 = 5

Disadvantage:
block access sequence: 100011, 001011, 100011
later arrival will **kill out** previous one. called **cache conflict.**

#### Tags and Valid Bits

**Tags:** decide which particular block is stored in a cache location.
Store block address as well as the data.
Only need the high-order bits.
**tags size= original address size- block size- blocks number(binary) - 1(valid bit)**

**Valid Bits:** decide whether the data exists. Initially 0.

Consider a direct-mapped cache with 8 cache frames and a block size of 32 bytes
Memory (byte) address generated by CPU|Block address|Hit or miss|Assigned cache block
---|---|--|---
0010 1100 0010|0010 110|Miss|110
0011 0100 0000|0011 010|Miss|010
0010 1100 0100|0010 110|Hit|110
0010 1100 0010|0010 110|Hit|110
0010 0000 1000|0010 000|Miss|000
0000 0110 0000|0000 011|Miss|011
0010 0001 0000|0010 000|Hit|000
0010 0100 0001|0010 010|Miss|010

#### Associate Cache

Fully associative(FA):
Each block can be placed **anywhere** in the cache.
pros: No cache conflict. But still have misses due to size.
cons: Costly (hardware and time)

N-way Set associative(SA):
Each block can be placed in a **certain number(N)** of cache locations.
A good compromise between DM & FA 

#### Block Replacement

1. Random
2. Least recently used(LRU):
   Upon miss:
   Replace the LRU with missed address -> Move the miss address to MRU position
   Upon hit:
   Move the hit address to MRU position -> Pack the rest

#### Deal with Dirty Data

##### Write-back

CPU only write  to cache.
CPU can write individual words at the rate of the cache.
Multiple writes to a block are merged into one write to main memory.
*more and more caches use this strategy.*

##### Write-through

CPU write both on cache and memory.(memory always up-to-date)