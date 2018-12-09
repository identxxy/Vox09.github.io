---
title: Finite State Machine 
categories: School Notes
date: 2018-12-08 16:20:45
tags: FSM
---

## State Machine

- **State**: A set of particular condition that the machine is in at a specific time
- FSM is a generic synchronous sequential circuit
- FSM has finite state memory, inputs & outputs
- State **memory** is implemented using **flip-flops**
- State **transition logic** implemented using **combinational circuit**
- Output logic implemented using combinational circuit
  - Output depends on state **only** - [**Moore**](#moore-machine)
  - Output depends on state **and** input - [**Mealy**](#mealy-machine)

### State equations

Describe the behavior of a clocked sequential circuit algebraically.
e.g.

> A(t+1) = D~A~(t) = A(t)x(t) + B(t)x(t)
> Simple form:
> A(t+1) = Ax + Bx	or A^+^ = Ax + Bx

### State Diagram and Transition Tables

![1544317606823](/assets/Finite-State-Machine/1544317606823.png)

The diagram is a **Mealy Machine**

## Moore Machine vs. Mealy Machine

### Moore Machine

The output value is inside the state bubbles.
Outputs are function **solely** of the current states.
Outputs change **synchronously** with states.

### Mealy Machine

The output value is on the transition edge.
Outputs depend on state **AND** inputs
Change of inputs causes an **immediate change** of outputs.

### Example

![1544320429228](/assets/Finite-State-Machine/1544320429228.png)

## Basic Design Steps of FSM

1. Understand the Specifications
   with a block diagram
2. Obtain an abstract specification of the FSM
   in state-transition diagram or table
3. Perform state reduction
   [equivalent states](#equivalent-states) can be merged
4. Perform state assignment
   assign binary values to the state in a way that next-state logic can be simplified
5. Choose FF to implement the FSM state register
   - D-FF : Q+ = D
   - T-FF  :  Q^+^ = TQ' + T'Q
     T = 0 hold  ;T = 1 toggle;
   - JK-FF:  Q^+^ = JQ' + K'Q
     |  JK  | Data | function |
     | :--: | :--: | :------: |
     | 0/0  |  1   |   hold   |
     | 0/1  |  0   |  reset   |
     | 1/0  |  Q   |   set    |
     | 1/1  |  Q'  |  toggle  |


6. [Implement the FSM](#fsm-implementation-procedure)
   design of next-state and output logic
7. Happy DEBUGGING!!

## FSM Implementation Procedure

1. Start with a state diagram (bubble diagram) 
    Describes the Finite State Machine according to the specification

2. Reduce the number of states if necessary/possible 
   (state reduction)

   ##### equivalent-states
   > Two internal states are said to be **equivalent** 
   > if for each input combination they give exactly the **same output AND send the circuit to the same(equivalent) state.**

3. Decide on the State Encoding 
    (how many flip-flops to use and what should be the FF outputs be for each state)

4. Produce the binary-code state table.

5. Decode what kind of FFs to use. 
    D-type FFs are normally used 
    (JK or T are more complicated and may give you smaller circuit if you play with TTLs)

6. Determine the FF input equations 
    (use FF excitation rules for JK and T type FFs) and general output equations from the transition tables

7. Implement the next state logic, output logic using combinational circuit techniques

8. Draw the complete logic diagram

