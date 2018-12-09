---
title: VHDL 
categories: School Notes
date: 2018-12-08 16:15:45
tags: VHDL
---

## Entity

Identify a distinct logic block

- Consists of the input and output ports (I/O pins)
- Each port can have several variables

```vhdl
entity nand2 is
port (a,b : in std_logic;
		y : out std_logic);
end nand2;
```

- Ports can be in (read), out (write), inout (bi-directional)
- Std_logic is the type of signal being carried at the ports

> - Std_logic can have values such as ‘0’, ‘1’, ‘X’, ‘Z’, ‘-’ and others.
>   (' z ' means high impedance (tristate))

- Another signal type is “bit” which only assumes binary values of ‘0’ and ‘1’.

## Architecture

Follow immediately after the entity declaration, 
to define the internal operation (function) of the logic module

- Defines the relationship between the input and output signals
- May have **internal signals** in the definition
- AN entity can have **several** different architecture specifications 
     to describe different views or different levels

```vhdl
entity inverter_gate is
	port (A: in std_logic;
		Z: out std_logic);
end inverter_gate;
architecture DATAFLOW of inverter_gate is
begin
        z <= not A after 10 ns;
end DATAFLOW;	
```

```vhdl
Entity OR3 is
port (I1,I2,I3: in std_logic; O: out std_logic);
end OR3;
architecture BEHAVIOR of OR3 is
begin
	O <= I1 or I2 or I3;
end BEHAVIOR;	
```
### Internal signals

used to simplify the model or 
define connections between sub-components inside the architecture

```vhdl
architecture example of special_mus is 
	signal control, tmp : std_logic;
begin
end example;
```

### Concurrent Statements

1. [Signal Assignments ](#signal-assignments)
2. [Component Instantiations for structural description)](#component-Instantiations-for-structural-description)
3. [Processes IN ORDER](#processes-in-order)

#### Signal Assignments

```vhdl
C <= '0';
```

##### Delayed 

```vhdl
c <= A and B after 10ns; --Only for simulation.
```

##### Selected (outside PROCESS)

connect target to many cases of **one** source.

```vhdl
with sel select
	DAta_out <= a when '0',
                b when '1',
                'X' when others;
```

##### Conditional (outside PROCESS)

connect target to many cases of **several** sources.

```vhdl
Y <= a when en = ‘1’ else
    ‘Z’ when en = ‘0’ else
        ‘X’;
```

#### Component Instantiations for structural description

```vhdl
COMPONENT xor_gate
	PORT ( A, B: IN STD_LOGIC; C: OUT STD_LOGIC);
END COMPONENT;
```

#### Processes IN ORDER

##### If-else Branch

many signals many cases.
```vhdl
if sth = '0' then
    A = '1'; B = '0';	-- IN-ORDER processes
elsif sth = '1' then	-- optional
    B = '1'; C = '0';	-- optional
else					-- IMPORTANT!
    C = '1';
end if；
```

##### case-when Branch

one signal many cases.
```vhdl
case  sth  is
    when '0' => A = '1'; B = '0';
	when '1' => B = '1'; C = '0';
	when others => C = '1';	-- IMPORTANT!
end case;
```

##### Variable and Signal

The **variable** is updated without any delay as soon as the statement is executed.
Variables must be declared inside a process (and are local to the process).
**Assignment `:=` executed sequentially!**

```vhdl
architecture VAR of EXAMPLE is
	signal TRIGGER, RESULT: integer :=0;
begin
	process
		variable variable1: integer := 1;
		variable variable2: integer := 2;
		variable variable3: integer := 3;
	begin 
		wait on TRIGGER;
		variable1 := variable2;	--2
		variable2 := variable1 + variable3; --2+3=5
		variable3 := variable2; --5
		RESULT <= variable1 + variable2 + variable3; --2+5+5
	end process;
end VAR;
```
The **signal** is updated after a time delay (delta delay if no delay is specified). 
Signals must be declared outside the process.
**Assignment `<=` executed concurrently!**

```vhdl
architecture SIGN of EXAMPLE is
	signal TRIGGER, RESULT: integer :=0;
	signal signal1: integer := 1;
	signal signal2: integer := 2;
	signal signal3: integer := 3;
begin
	process
	begin 
		wait on TRIGGER;
		signal1 <= signal2;	--2
		signal2 <= signal1 + signal3; --1+3
		signal3 <= signal2; --2
		RESULT <= signal1 + signal2 + signal3; --1+2+3
	end process;
end VAR;
```

### Dataflow architecture

Specify input/output relations in Boolean expression or conditional statements

```vhdl
ENTITY 2_to_4_decoder IS
	PORT (A, B, E: IN STD_LOGIC;
		D: OUT STD_LOGIC_VECTOR (3 downto 0);
END 2_to_4_decoder;
ARCHITECTURE dataflow OF 2_to_4_decoder IS
BEGIN
	D(0) <=‘0’ WHEN (A=‘0’ AND B=‘0’ AND E=‘0’) ELSE ‘1’;
	D(1) <=‘0’ WHEN (A=‘0’ AND B=‘1’ AND E=‘0’) ELSE ‘1’;
	D(2) <=‘0’ WHEN (A=‘1’ AND B=‘0’ AND E=‘0’) ELSE ‘1’;
	D(3) <=‘0’ WHEN (A=‘1’ AND B=‘1’ AND E=‘0’) ELSE ‘1’;
END dataflow;
```

### Structural architecture

Describes a set of interconnected components.

```vhdl
ARCHITECTURE structural OF four_bit_adder_sub IS
	COMPONENT four_bit_adder
		PORT ( A: IN STD_LOGIC_VECTOR (3 downto 0);
			B: IN STD_LOGIC_VECTOR (3 downto 0);
			C0: IN STD_LOGIC; C4: OUT STD_LOGIC;
			S: OUT STD_LOGIC_VECTOR (3 downto 0));
	END COMPONENT;
	COMPONENT xor_arrays
	PORT ( X: IN STD_LOGIC_VECTOR (3 downto 0);
		Z: IN STD_LOGIC;
		Y: OUT STD_LOGIC_VECTOR (3 downto 0));
	END COMPONENT;
	signal Y: STD_LOGIC_VECTOR (3 downto 0);
BEGIN
	Block1: xor_arrays PORT MAP (B, M, Y);
	Block2: four_bit_adder PORT MAP (A, Y, M, C, S);
END structural;
```

### Behavioral architecture

Any change in the values of **Sensitivity List** will cause immediate execution of the process

```vhdl
ENTITY mux4 IS
PORT ( S: IN STD_LOGIC_VECTOR (1 downto 0);
		I: IN STD_LOGIC_VECTOR (3 downto 0);
		Y: OUT STD_LOGIC);
END mux4;
ARCHITECTURE behavioral OF mux4 IS
BEGIN
	PROCESS (S, I)		-- Sensitivity List
	BEGIN
		CASE S IS
			WHEN “00” => Y <= I(0);
			WHEN “01” => Y <= I(1);
			WHEN “10” => Y <= I(2);
			WHEN “11” => Y <= I(3);
		END CASE;
	END PROCESS;
END behavioral;
```