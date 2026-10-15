# VLSI Design and Physical Design: Complete RTL-to-GDSII Learning Guide

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Repository Links and Learning Order](#2-repository-links-and-learning-order)
3. [Complete RTL-to-GDSII Flow](#3-complete-rtl-to-gdsii-flow)
4. [Verilog RTL Design and Simulation](#4-verilog-rtl-design-and-simulation)
5. [Digital Logic and Boolean Algebra](#5-digital-logic-and-boolean-algebra)
6. [Combinational and Sequential Logic](#6-combinational-and-sequential-logic)
7. [Logic Synthesis Using Yosys](#7-logic-synthesis-using-yosys)
8. [Synthesis Optimization and Standard Cell Selection](#8-synthesis-optimization-and-standard-cell-selection)
9. [Open-Source EDA, OpenLANE and Sky130 PDK](#9-open-source-eda-openlane-and-sky130-pdk)
10. [Floorplanning](#10-floorplanning)
11. [Standard Cell Libraries and CMOS Design](#11-standard-cell-libraries-and-cmos-design)
12. [Placement and Power Distribution Network](#12-placement-and-power-distribution-network)
13. [Timing Analysis](#13-timing-analysis)
14. [Clock Tree Synthesis](#14-clock-tree-synthesis)
15. [Routing and Parasitic Extraction](#15-routing-and-parasitic-extraction)
16. [Design Rule Checking and Layout Verification](#16-design-rule-checking-and-layout-verification)
17. [Power, Performance and Area Formulas](#17-power-performance-and-area-formulas)
18. [Important Commands Reference](#18-important-commands-reference)
19. [Expected Design Outputs](#19-expected-design-outputs)
20. [Learning Outcomes](#20-learning-outcomes)
21. [Conclusion](#21-conclusion)
22. [References](#22-references)

---

## 1. Project Overview

This project documents the concepts, formulas, commands, and implementation stages involved in digital VLSI design and ASIC physical design.

It combines RTL design and verification with logic synthesis, standard-cell mapping, physical implementation, timing analysis, and final layout generation.

The work is organized into two repositories.


Main topics:

- Verilog HDL and RTL design
- Testbench development
- Functional simulation
- Combinational and sequential logic
- Logic optimization
- Synthesis
- Gate-level simulation
- Synthesis-simulation mismatches
- Waveform analysis
- Open-source EDA tools
- OpenLANE
- Sky130 PDK
- Floorplanning
- Standard-cell libraries
- Power distribution networks
- Placement
- Clock Tree Synthesis (CTS)
- Static Timing Analysis (STA)
- Global and detailed routing
- Parasitic extraction
- Design Rule Checking (DRC)
- Layout Versus Schematic (LVS)
- RTL-to-GDSII implementation

Recommended learning sequence:

1. Learn Verilog and digital logic.
2. Write RTL modules and testbenches.
3. Run functional simulations.
4. Inspect waveforms using GTKWave.
5. Synthesize RTL using Yosys.
6. Analyze the synthesized netlist and cell statistics.
7. Understand standard-cell libraries and the Sky130 PDK.
8. Perform floorplanning and power planning.
9. Place standard cells.
10. Perform clock-tree synthesis.
11. Route the design.
12. Analyze timing and parasitic effects.
13. Verify the physical layout.
14. Generate and inspect the final layout database.

---

## 3. Complete RTL-to-GDSII Flow

```text
Design Specification
        |
        v
RTL Design
        |
        v
Testbench Development
        |
        v
Functional Simulation
        |
        v
Logic Synthesis
        |
        v
Technology Mapping
        |
        v
Gate-Level Netlist
        |
        v
Floorplanning
        |
        v
Power Distribution Network
        |
        v
Placement
        |
        v
Clock Tree Synthesis
        |
        v
Global Routing
        |
        v
Detailed Routing
        |
        v
Parasitic Extraction
        |
        v
Static Timing Analysis
        |
        v
Physical Verification
        |
        v
GDSII Layout Generation
```

### Explanation

**RTL design:** Describes circuit behavior using a hardware description language.

**Functional simulation:** Checks whether the RTL meets the functional specification.

**Logic synthesis:** Converts RTL into an optimized gate-level representation.

**Technology mapping:** Maps the optimized logic to cells available in a target standard-cell library.

**Floorplanning:** Defines the physical organization of the chip.

**Placement:** Assigns physical positions to standard cells.

**Clock Tree Synthesis:** Builds a clock distribution network.

**Routing:** Creates the physical interconnections between cells.

**Static Timing Analysis:** Checks whether the design meets timing constraints.

**Physical verification:** Checks geometric design rules and circuit connectivity.

**GDSII generation:** Produces the physical layout database for downstream manufacturing preparation.

---

## 4. Verilog RTL Design and Simulation

### 4.1 What is Verilog?

Verilog is a Hardware Description Language used to describe digital circuits.

It supports behavioral, dataflow, and structural modeling.

### 4.2 Basic Verilog module

```verilog
module and_gate (
    input  wire a,
    input  wire b,
    output wire y
);

assign y = a & b;

endmodule
```

Boolean equation:

\[
Y=A\cdot B
\]

The output is HIGH only when both inputs are HIGH.

### 4.3 Testbench example

```verilog
`timescale 1ns/1ps

module tb_and_gate;

reg a, b;
wire y;

and_gate uut (
    .a(a),
    .b(b),
    .y(y)
);

initial begin
    $dumpfile("and_gate.vcd");
    $dumpvars(0, tb_and_gate);

    a = 0; b = 0; #10;
    a = 0; b = 1; #10;
    a = 1; b = 0; #10;
    a = 1; b = 1; #10;

    $finish;
end

initial begin
    $monitor("Time=%0t a=%b b=%b y=%b",
             $time, a, b, y);
end

endmodule
```

### 4.4 Simulation commands

Compile the design and testbench:

```bash
iverilog -g2012 -o and_gate_sim and_gate.v tb_and_gate.v
```

Run the simulation:

```bash
vvp and_gate_sim
```

Open the waveform:

```bash
gtkwave and_gate.vcd
```

### 4.5 Important Verilog constructs

| Construct | Purpose |
|---|---|
| `module` | Defines a hardware module |
| `input` | Declares an input port |
| `output` | Declares an output port |
| `wire` | Represents a net |
| `reg` | Represents a procedural variable in Verilog |
| `assign` | Describes a continuous assignment |
| `always @(*)` | Describes combinational procedural logic |
| `always @(posedge clk)` | Describes positive-edge-triggered logic |
| `initial` | Executes a procedural block once in simulation |
| `#10` | Introduces a simulation delay |
| `$monitor` | Prints changes in selected signals |
| `$dumpfile` | Specifies a waveform file |
| `$dumpvars` | Selects signals for waveform dumping |
| `$finish` | Ends the simulation |

**Note:** `reg` does not automatically mean a physical register. The synthesized hardware depends on the procedural assignments and inferred behavior.

---

## 5. Digital Logic and Boolean Algebra

Boolean algebra provides the mathematical foundation for digital logic optimization.

### 5.1 Basic Boolean operations

| Operation | Expression | Meaning |
|---|---|---|
| AND | \(Y=A\cdot B\) | HIGH when both inputs are HIGH |
| OR | \(Y=A+B\) | HIGH when at least one input is HIGH |
| NOT | \(Y=\overline A\) | Inverts the input |
| NAND | \(Y=\overline{AB}\) | Complement of AND |
| NOR | \(Y=\overline{A+B}\) | Complement of OR |
| XOR | \(Y=A\oplus B\) | HIGH when inputs differ |
| XNOR | \(Y=\overline{A\oplus B}\) | HIGH when inputs match |

### 5.2 Important Boolean laws

**Identity laws**

\[
A+0=A
\]

\[
A\cdot1=A
\]

**Null laws**

\[
A+1=1
\]

\[
A\cdot0=0
\]

**Idempotent laws**

\[
A+A=A
\]

\[
A\cdot A=A
\]

**Complement laws**

\[
A+\overline A=1
\]

\[
A\cdot\overline A=0
\]

**Double negation**

\[
\overline{\overline A}=A
\]

**Commutative laws**

\[
A+B=B+A
\]

\[
AB=BA
\]

**Associative laws**

\[
(A+B)+C=A+(B+C)
\]

\[
(AB)C=A(BC)
\]

**Distributive laws**

\[
A(B+C)=AB+AC
\]

\[
A+BC=(A+B)(A+C)
\]

**Absorption laws**

\[
A+AB=A
\]

\[
A(A+B)=A
\]

### 5.3 De Morgan's theorems

\[
\overline{A+B}=\overline A\cdot\overline B
\]

\[
\overline{AB}=\overline A+\overline B
\]

These theorems help convert logic expressions into NAND, NOR, and other gate implementations.

### 5.4 Multiplexer equation

For a 2-to-1 multiplexer:

\[
Y=\overline S D_0+SD_1
\]

Where:

- \(S\) is the select signal.
- \(D_0\) is the first data input.
- \(D_1\) is the second data input.

### 5.5 Half adder

Sum:

\[
S=A\oplus B
\]

Carry:

\[
C=AB
\]

### 5.6 Full adder

Sum:

\[
S=A\oplus B\oplus C_{in}
\]

Carry:

\[
C_{out}=AB+AC_{in}+BC_{in}
\]

An equivalent carry expression is:

\[
C_{out}=AB+C_{in}(A\oplus B)
\]

### 5.7 Decoder example

For a 2-to-4 active-HIGH decoder:

\[
Y_0=\overline A\overline B
\]

\[
Y_1=\overline A B
\]

\[
Y_2=A\overline B
\]

\[
Y_3=AB
\]

Only one output is HIGH for each valid binary input combination.

---

## 6. Combinational and Sequential Logic

### 6.1 Combinational logic

Combinational logic depends on the current inputs.

Examples include adders, multiplexers, decoders, comparators, and arithmetic logic units.

Its abstract functional relationship is:

\[
Y=f(X)
\]

Where \(X\) represents the inputs and \(Y\) represents the outputs.

### 6.2 Sequential logic

Sequential logic depends on the current inputs and stored state.

The general state equation is:

\[
Q_{next}=F(Q_{current},X)
\]

Where:

- \(Q_{current}\) is the current state.
- \(Q_{next}\) is the next state.
- \(X\) represents the current inputs.

### 6.3 D flip-flop

At the active clock edge:

\[
Q_{next}=D
\]

This equation assumes a conventional edge-triggered D flip-flop without additional enable or reset behavior.

### 6.4 Register with enable

For a register with enable \(E\):

\[
Q_{next}=
\begin{cases}
D, & E=1\\
Q, & E=0
\end{cases}
\]

### 6.5 Counter

For an \(n\)-bit binary counter that increments by one:

\[
Q_{next}=(Q+1)\bmod 2^n
\]

The modulo operation causes the counter to wrap around after its maximum representable value.

### 6.6 Example of a clocked counter

```verilog
module counter4 (
    input wire clk,
    input wire rst,
    output reg [3:0] count
);

always @(posedge clk) begin
    if (rst)
        count <= 4'b0000;
    else
        count <= count + 1'b1;
end

endmodule
```

This example uses a synchronous, active-HIGH reset. The counter resets to zero at the rising clock edge when `rst` is HIGH.

---

## 7. Logic Synthesis Using Yosys

### 7.1 What is logic synthesis?

Logic synthesis converts RTL into a gate-level representation.

The synthesis process typically involves:

1. Reading the RTL source.
2. Parsing the design.
3. Elaborating modules and parameters.
4. Converting processes into logic.
5. Performing logic optimizations.
6. Mapping logic to the target technology.
7. Generating a synthesized netlist.

### 7.2 Important Yosys commands

Start Yosys:

```bash
yosys
```

Read Verilog:

```yosys
read_verilog and_gate.v
```

Select the top module:

```yosys
hierarchy -top and_gate
```

Convert processes:

```yosys
proc
```

Optimize the logic:

```yosys
opt
```

Display statistics:

```yosys
stat
```

Generate a synthesized Verilog file for a generic design:

```yosys
write_verilog synthesized.v
```

Exit Yosys:

```yosys
exit
```

### 7.3 Basic generic synthesis sequence

For a simple design without a technology-specific library:

```yosys
read_verilog design.v
hierarchy -check -top design
proc
opt
check
stat
write_verilog synthesized.v
```

Replace `design` with the actual top-module name.

This is a basic generic synthesis example. It does not perform technology mapping to a specific Sky130 standard-cell library.

### 7.4 Technology mapping

A typical Liberty-based mapping sequence, when the appropriate library is available, is:

```yosys
read_verilog design.v
read_liberty -lib path/to/library.lib
hierarchy -check -top design
synth -top design
dfflibmap -liberty path/to/library.lib
abc -liberty path/to/library.lib
clean
stat -liberty path/to/library.lib
write_verilog -noattr mapped_netlist.v
```

**Important:** The exact sequence depends on the Yosys version, library contents, design requirements, and target technology. This example illustrates the main commands; use the technology-specific flow for a real implementation. Sequential mapping and combinational mapping must be configured appropriately for the library.

### 7.5 Important Yosys commands

| Command | Purpose |
|---|---|
| `read_verilog` | Reads Verilog files |
| `hierarchy` | Checks and organizes module hierarchy |
| `proc` | Converts procedural blocks |
| `opt` | Runs logic optimizations |
| `check` | Checks for structural problems |
| `synth` | Runs a synthesis flow |
| `dfflibmap` | Maps supported flip-flops to library cells |
| `abc` | Performs logic optimization and technology mapping |
| `stat` | Displays synthesis statistics |
| `write_verilog` | Exports a Verilog netlist |
| `write_json` | Exports the design as JSON |
| `show` | Displays a schematic representation where supported |

### 7.6 Example schematic generation

```yosys
read_verilog design.v
hierarchy -top design
proc
opt
show
```

### 7.7 Gate-level simulation

A synthesized netlist can be simulated using a compatible cell model library and a suitable testbench.

```bash
iverilog -g2012 -o gate_sim tb_design.v mapped_netlist.v
vvp gate_sim
```

If the netlist instantiates technology-specific cells, their simulation models must also be included.

For timing-aware simulation, the simulator must support the timing constructs used in the cell models, and the required timing annotation must be provided.

---

## 8. Synthesis Optimization and Standard Cell Selection

### 8.1 Why optimize logic?

Logic optimization aims to improve the circuit without changing its required behavior.

The main objectives are:

- Reduce area.
- Improve timing.
- Reduce power.
- Remove redundant logic.
- Avoid unnecessary hardware.
- Select suitable library cells.

### 8.2 Area estimation

The mapped standard-cell area can be calculated as:

\[
A_{cells}=\sum_{i=1}^{N} A_i
\]

Where:

- \(N\) is the number of mapped cell instances.
- \(A_i\) is the area of the \(i\)-th cell.

This is the sum of cell areas, not the total die area.

### 8.3 Cell count

\[
N_{cells}=\sum_{i=1}^{K}N_i
\]

Where \(N_i\) is the number of instances of cell type \(i\).

### 8.4 Logic depth

Logic depth is the number of logic stages traversed along a path.

A simplified path-delay estimate is:

\[
T_{path}\approx\sum_{i=1}^{N}t_i
\]

Where \(t_i\) represents the delay of each stage.

Actual timing depends on input slew, output load, interconnect, cell characterization, and timing constraints.

### 8.5 Technology mapping

Technology mapping selects standard cells from a target library to implement a logic network.

The selected cells may differ in:

- Function
- Drive strength
- Area
- Input capacitance
- Propagation delay
- Power characteristics

A larger drive-strength cell may improve delay under some conditions but typically increases area and input capacitance.

---

## 9. Open-Source EDA, OpenLANE and Sky130 PDK

### 9.1 Open-source EDA

Electronic Design Automation tools automate circuit design, verification, synthesis, and physical implementation.

Tools used in open-source digital ASIC flows include:

- Yosys
- OpenLANE
- OpenROAD
- OpenSTA
- Magic
- Netgen
- Icarus Verilog
- GTKWave
- ngspice

Not every tool is required for every stage.

### 9.2 OpenLANE

OpenLANE is an RTL-to-GDSII implementation flow that coordinates multiple EDA tools.

The precise commands depend on the installed OpenLANE version and flow configuration.

### 9.3 Sky130 PDK

The Sky130 PDK provides technology-specific information for the Sky130 process.

Depending on the flow configuration, the PDK and associated libraries supply:

- Standard-cell definitions
- Physical cell abstracts
- Timing libraries
- Technology and layer definitions
- Design rules
- Transistor models
- Layout and verification information

### 9.4 Basic environment inspection

Check the current directory:

```bash
pwd
```

List files:

```bash
ls
```

Display hidden files:

```bash
ls -la
```

Check the Yosys version:

```bash
yosys -V
```

Check Icarus Verilog:

```bash
iverilog -V
```

Check the Git version:

```bash
git --version
```

Check available OpenROAD commands:

```bash
openroad -version
```

The availability and exact syntax of version commands vary between tools.

### 9.5 OpenLANE classic-flow commands

The following commands illustrate a common OpenLANE classic-flow environment, typically used with a compatible OpenLANE installation.

Start the flow container or environment according to its installation instructions, then launch the classic flow:

```bash
flow.tcl -interactive
```

Inside the OpenLANE Tcl shell, load the design configuration:

```tcl
package require openlane
prep -design picorv32a
```

Run the flow:

```tcl
run_synthesis
run_floorplan
run_placement
run_cts
run_routing
```

The exact commands, stage names, and required configuration depend on the installed OpenLANE release. These commands should not be assumed to work unchanged in OpenLane 2, which uses a different interface and configuration model.

For the classic flow, inspect the run directories and reports after each stage. Use the configuration and flow documentation corresponding to the installed version.

---

## 10. Floorplanning

### 10.1 What is floorplanning?

Floorplanning defines the physical organization of an integrated circuit before detailed placement and routing.

It establishes the die and core dimensions, placement regions, pin locations, macro locations, and initial power-distribution structures.

### 10.2 Core area

For a rectangular core:

\[
A_{core}=W_{core}\times H_{core}
\]

Where:

- \(W_{core}\) is the core width.
- \(H_{core}\) is the core height.

### 10.3 Die area

For a rectangular die:

\[
A_{die}=W_{die}\times H_{die}
\]

The core is generally a region within the die; the die area and core area are not interchangeable.

### 10.4 Core utilization

A common placement-utilization definition is:

\[
U=\frac{A_{cells}}{A_{core}}\times100\%
\]

Where:

- \(A_{cells}\) is the sum of the relevant placed-cell areas.
- \(A_{core}\) is the available core area used for the calculation.

Some reports use different area definitions or exclude certain cell categories. Always check the report's definition.

### 10.5 Estimating core area

If the target utilization is represented as a fraction \(u\), where \(0<u<1\):

\[
A_{core}\approx\frac{A_{cells}}{u}
\]

For example, if the cell area is \(10{,}000\ \mu m^2\) and the target utilization is 50%:

\[
A_{core}\approx\frac{10{,}000}{0.5}
\]

\[
A_{core}\approx20{,}000\ \mu m^2
\]

This is a preliminary estimate. Actual core sizing must also account for macros, blockages, routing needs, power structures, and other physical constraints.

### 10.6 Aspect ratio

\[
AR=\frac{W_{core}}{H_{core}}
\]

If the aspect ratio is 1, the core is square. If it differs from 1, the core is rectangular.

### 10.7 Utilization trade-off

Higher utilization may reduce the required core area but can increase congestion and make timing closure more difficult.

Lower utilization can provide more routing space but may increase the die area.

The best target depends on the design and technology.

---

## 11. Standard Cell Libraries and CMOS Design

### 11.1 Standard-cell library

A standard-cell library contains reusable logic cells with characterized physical and electrical properties.

Common cells include:

- Inverters
- Buffers
- NAND gates
- NOR gates
- XOR gates
- Multiplexers
- Flip-flops
- Clock buffers

### 11.2 CMOS inverter

A CMOS inverter uses complementary PMOS and NMOS transistors.

Its ideal logic equation is:

\[
Y=\overline A
\]

Truth table:

| Input \(A\) | Output \(Y\) |
|---|---|
| 0 | 1 |
| 1 | 0 |

### 11.3 CMOS power

A commonly used approximation for dynamic switching power is:

\[
P_{dynamic}=\alpha C V_{DD}^{2} f
\]

Where:

- \(\alpha\) is the activity factor under the chosen convention.
- \(C\) is the effective switched capacitance.
- \(V_{DD}\) is the supply voltage.
- \(f\) is the clock or reference frequency.

The exact activity-factor convention varies. Some definitions count transitions differently, so the convention used in a calculation should be stated.

Total power can be represented as:

\[
P_{total}=P_{dynamic}+P_{short\text{-}circuit}+P_{leakage}
\]

### 11.4 Transistor resistance-capacitance delay

A simplified first-order delay estimate is:

\[
t_d\approx0.69R_{eq}C_{load}
\]

Where:

- \(R_{eq}\) is an effective resistance.
- \(C_{load}\) is the effective load capacitance.

This approximation is useful for understanding delay trends. Real cell delays are obtained from characterized timing models and depend on operating conditions, slew, and load.

### 11.5 Propagation delay

\[
t_{pd}=t_{output}-t_{input}
\]

Propagation delay is measured between specified input and output threshold crossings.

Rise and fall delays are often reported separately.

### 11.6 Rise and fall time

\[
t_r=t_{90\%}-t_{10\%}
\]

\[
t_f=t_{10\%}-t_{90\%}
\]

The second expression refers to a falling transition, where the signal moves from the high threshold to the low threshold. The threshold percentages are defined relative to the applicable signal swing.

### 11.7 Cell characterization

Characterization determines the electrical behavior of a cell for different operating conditions.

Important parameters include:

- Propagation delay
- Rise and fall transition times
- Input capacitance
- Output loading
- Leakage power
- Dynamic power

These values are stored in timing and power models used by implementation tools.

---

## 12. Placement and Power Distribution Network

### 12.1 Placement

Placement determines the physical positions of standard cells inside the placement region.

The placement process considers:

- Timing requirements
- Cell density
- Congestion
- Wire length
- Power requirements
- Legal placement locations

### 12.2 Placement objectives

A simplified optimization objective may be written as:

\[
Cost=w_1T+w_2A+w_3C
\]

Where:

- \(T\) represents a timing cost.
- \(A\) represents an area-related cost.
- \(C\) represents a congestion-related cost.
- \(w_1,w_2,w_3\) are weighting factors.

This is a conceptual expression rather than a universal placement-tool objective. Real tools use multiple algorithms, constraints, and optimization stages.

### 12.3 Wire length

A simple estimate of rectilinear distance between two points is:

\[
L=|x_2-x_1|+|y_2-y_1|
\]

This is the Manhattan distance. A real routed net may have a longer path due to obstacles, layer changes, and routing constraints.

### 12.4 Power Distribution Network

The power distribution network delivers supply voltage and ground connections to cells.

Common components include power rings, straps, rails, and vias.

The design must manage:

- Voltage drop
- Current density
- Electromigration
- Power connectivity
- Metal-layer availability

### 12.5 IR drop

A simplified voltage-drop relationship is:

\[
V_{drop}=IR
\]

Where \(I\) is the current through an effective resistance \(R\).

The delivered voltage is approximately:

\[
V_{cell}=V_{source}-V_{drop}
\]

This simplified model does not capture the full distributed behavior of a real power network.

---

## 13. Timing Analysis

### 13.1 What is Static Timing Analysis?

Static Timing Analysis evaluates whether signal paths meet the timing requirements without simulating every possible input sequence.

It uses:

- Timing constraints
- Cell timing models
- Clock definitions
- Input and output delays
- Interconnect estimates or extracted parasitics
- Operating conditions

### 13.2 Clock period and frequency

\[
T=\frac{1}{f}
\]

Where:

- \(T\) is the clock period.
- \(f\) is the clock frequency.

For a 100 MHz clock:

\[
T=\frac{1}{100\times10^6}
\]

\[
T=10\ ns
\]

### 13.3 Setup time

Setup time is the minimum interval for which data must remain stable before the active clock edge.

For a simplified register-to-register path with zero clock skew:

\[
T_{cq}+T_{comb}+T_{setup}\leq T_{clk}
\]

Where:

- \(T_{cq}\) is clock-to-Q delay.
- \(T_{comb}\) is combinational path delay.
- \(T_{setup}\) is the receiving register's setup time.
- \(T_{clk}\) is the clock period.

### 13.4 Setup slack

For the same simplified case:

\[
Slack_{setup}=T_{clk}-(T_{cq}+T_{comb}+T_{setup})
\]

Interpretation:

- Positive slack: The simplified setup requirement is satisfied.
- Zero slack: The requirement is met exactly.
- Negative slack: The path violates the setup requirement.

Real STA calculations also account for launch and capture clock arrival times, uncertainty, and other timing constraints.

### 13.5 Setup timing with clock skew

Define clock skew as:

\[
T_{skew}=T_{capture}-T_{launch}
\]

For a simplified register-to-register path:

\[
Slack_{setup}=T_{clk}+T_{skew}
-T_{cq}-T_{comb}-T_{setup}
\]

Positive capture skew can improve setup timing in this simplified equation, but it may adversely affect hold timing.

### 13.6 Hold time

Hold time is the minimum interval for which data must remain stable after the active clock edge.

For a simplified register-to-register path:

\[
T_{cq,min}+T_{comb,min}\geq T_{hold}
\]

This form assumes coincident launch and capture clock edges and omits additional clock and uncertainty terms.

### 13.7 Hold slack

Under the same simplified assumptions:

\[
Slack_{hold}=T_{cq,min}+T_{comb,min}-T_{hold}
\]

Negative hold slack indicates a hold violation.

### 13.8 Hold timing with clock skew

Using the skew convention above, a simplified hold condition is:

\[
T_{cq,min}+T_{comb,min}
\geq T_{hold}+T_{skew}
\]

This illustrates why positive capture skew can make hold timing more difficult.

### 13.9 Critical path

The critical path is the path with the worst relevant timing margin for the timing check being considered.

For a setup-critical path, the maximum path delay is particularly important.

A simplified maximum data-path delay is:

\[
T_{data,max}=T_{cq,max}+T_{comb,max}
\]

### 13.10 Maximum operating frequency

A simplified estimate is:

\[
f_{max}\approx\frac{1}{T_{min}}
\]

Where \(T_{min}\) is the minimum clock period allowed by the relevant setup constraints.

This estimate assumes the path and constraints used in the calculation represent the limiting condition. Real sign-off frequency also depends on clock uncertainty, skew, operating corners, and other timing requirements.

### 13.11 Timing constraints

A simplified SDC example:

```tcl
create_clock -name clk -period 10 [get_ports clk]
```

This creates a clock with a 10 ns period, assuming the port is named `clk`.

Other common SDC commands include:

```tcl
set_clock_uncertainty 0.1 [get_clocks clk]
set_input_delay 1.0 -clock clk [get_ports data_in]
set_output_delay 1.0 -clock clk [get_ports data_out]
```

These are illustrative values, not recommended constraints for every design. Real constraints must reflect the actual interface timing, clocking scheme, and design requirements.

---

## 14. Clock Tree Synthesis

### 14.1 What is CTS?

Clock Tree Synthesis creates a physical clock distribution network that delivers clock signals to sequential cells.

It aims to control clock arrival times, skew, transition times, and clock insertion delay.

### 14.2 Clock latency

Clock latency is the time between a defined clock reference point and its arrival at a clock endpoint.

A simplified clock-path delay is:

\[
T_{clock}=T_{buffers}+T_{interconnect}
\]

### 14.3 Clock skew

\[
T_{skew}=T_{capture}-T_{launch}
\]

Clock skew is the difference in arrival times between the relevant capture and launch clock endpoints.

### 14.4 Clock insertion delay

Clock insertion delay is the delay through the clock distribution network from its reference point to a clock endpoint.

It includes contributions from buffers and interconnect, depending on the reference and reporting convention.

### 14.5 Clock power

Because the clock network switches frequently, it can contribute substantially to dynamic power.

A simplified estimate is:

\[
P_{clock}\approx\alpha C_{clock}V_{DD}^{2}f
\]

The effective activity factor depends on the convention used, and actual clock power includes cell and interconnect effects.

### 14.6 Ideal clock versus propagated clock

Before CTS, the clock may be treated as ideal during certain stages of implementation.

After CTS, timing analysis can use the physical clock network's propagated delays.

The exact transition between ideal and propagated clocks depends on the configured flow and analysis stage.

### 14.7 CTS command reference

In a compatible OpenROAD environment, the CTS operation is commonly associated with:

```tcl
clock_tree_synthesis
```

The command requires appropriate design, clock, library, and flow configuration. In OpenLANE, CTS is normally invoked through the configured flow rather than by issuing this command in an arbitrary shell.

---

## 15. Routing and Parasitic Extraction

### 15.1 Global routing

Global routing estimates paths and assigns routing resources to nets.

It helps evaluate congestion and determine whether the design can be routed.

### 15.2 Detailed routing

Detailed routing creates the physical interconnections, including metal segments and vias, while respecting the technology's routing rules.

### 15.3 Wire resistance

For a uniform conductor:

\[
R=\rho\frac{L}{A}
\]

Where:

- \(\rho\) is the material resistivity.
- \(L\) is the conductor length.
- \(A\) is its cross-sectional area.

This idealized equation does not account for all effects present in integrated-circuit interconnects.

### 15.4 Wire capacitance

A simple parallel-plate approximation is:

\[
C=\epsilon\frac{A}{d}
\]

Where:

- \(\epsilon\) is the effective permittivity.
- \(A\) is the overlapping area.
- \(d\) is the separation between conductors.

Real interconnect capacitance also includes fringing and coupling effects.

### 15.5 RC delay

A simple first-order approximation is:

\[
T_d\approx0.69RC
\]

Distributed RC networks require more detailed models, and real routed-path delay includes both cell and interconnect contributions.

### 15.6 Parasitic extraction

Parasitic extraction estimates or calculates resistance and capacitance associated with the routed layout.

The extracted parasitics are used for more realistic timing and power analysis.

### 15.7 SPEF

Standard Parasitic Exchange Format (SPEF) stores extracted parasitic information for design analysis.

A SPEF file may contain:

- Net capacitances
- Resistance values
- Coupling capacitances
- Parasitic network connectivity

The exact contents depend on the extraction tool and settings.

---

## 16. Design Rule Checking and Layout Verification

### 16.1 Design Rule Checking

DRC checks whether a physical layout follows technology-specific geometric rules.

Examples include:

- Minimum metal width
- Minimum spacing
- Via enclosure
- Minimum area
- Layer-specific restrictions

### 16.2 Layout Versus Schematic

LVS compares the circuit extracted from the layout with the intended schematic or reference netlist.

It checks whether the layout represents the correct circuit connectivity and devices.

### 16.3 Antenna checks

Antenna checks identify potential manufacturing-related charge-accumulation problems associated with interconnects during fabrication.

The relevant checks and required fixes depend on the technology and manufacturing rules.

### 16.4 Timing verification

Timing verification checks that the implementation satisfies setup, hold, and other relevant timing requirements.

### 16.5 Verification summary

| Check | Main purpose |
|---|---|
| RTL simulation | Functional correctness |
| Gate-level simulation | Synthesized logic behavior |
| STA | Timing requirements |
| DRC | Geometric design-rule compliance |
| LVS | Layout-to-reference connectivity |
| Antenna checks | Manufacturing-related antenna-rule compliance |
| Power integrity | Supply voltage and current-related reliability |

A successful simulation does not automatically imply that the physical layout is DRC-clean, LVS-correct, or timing-clean.

---

## 17. Power, Performance and Area Formulas

### 17.1 Total cell area

\[
A_{cells}=\sum_{i=1}^{N}A_i
\]

This measures the sum of the areas of the included cell instances.

### 17.2 Core utilization

\[
U=\frac{A_{cells}}{A_{core}}\times100\%
\]

### 17.3 Core area estimate

\[
A_{core}\approx\frac{A_{cells}}{U_f}
\]

Here \(U_f\) is utilization expressed as a fraction, such as 0.60 for 60%.

### 17.4 Clock period

\[
T_{clk}=\frac{1}{f_{clk}}
\]

### 17.5 Dynamic power

\[
P_{dynamic}=\alpha CV_{DD}^{2}f
\]

### 17.6 Approximate energy per cycle

For a dynamic power model using the same activity convention:

\[
E_{cycle}=\frac{P_{dynamic}}{f}
\]

Thus:

\[
E_{cycle}\approx\alpha CV_{DD}^{2}
\]

This is a simplified estimate for the stated switching model.

### 17.7 Setup slack

For the simple zero-skew case:

\[
Slack_{setup}=T_{clk}-T_{cq}-T_{comb}-T_{setup}
\]

### 17.8 Hold slack

For the simple zero-skew case:

\[
Slack_{hold}=T_{cq,min}+T_{comb,min}-T_{hold}
\]

### 17.9 Approximate maximum frequency

\[
f_{max}\approx\frac{1}{T_{min}}
\]

### 17.10 RC delay

\[
T_d\approx0.69RC
\]

### 17.11 Ohm's law

\[
V=IR
\]

### 17.12 Interconnect resistance

\[
R=\rho\frac{L}{A}
\]

### 17.13 Rectilinear distance

\[
L=|x_2-x_1|+|y_2-y_1|
\]

### 17.14 Aspect ratio

\[
AR=\frac{W}{H}
\]

### 17.15 PPA comparison

A design's power, performance, and area can be compared against a baseline.

For area improvement:

\[
Improvement_A=
\frac{A_{baseline}-A_{new}}{A_{baseline}}\times100\%
\]

For power improvement:

\[
Improvement_P=
\frac{P_{baseline}-P_{new}}{P_{baseline}}\times100\%
\]

For delay reduction:

\[
Improvement_D=
\frac{D_{baseline}-D_{new}}{D_{baseline}}\times100\%
\]

A positive value indicates a reduction in the corresponding metric.

For frequency improvement:

\[
Improvement_f=
\frac{f_{new}-f_{baseline}}{f_{baseline}}\times100\%
\]

These comparisons are meaningful only when the same measurement definitions, design requirements, and operating conditions are used.

---

## 18. Important Commands Reference

The following commands are organized by task. They are reference examples; paths, installed versions, design names, and configurations may require adjustment.

### 18.1 Linux terminal commands

Print the current directory:

```bash
pwd
```

List files:

```bash
ls
```

List files including hidden files:

```bash
ls -la
```

Change directory:

```bash
cd path/to/directory
```

Create a directory:

```bash
mkdir my_design
```

Copy a file:

```bash
cp source.v destination.v
```

Search for a file:

```bash
find . -name "*.v"
```

Search for a text pattern:

```bash
grep -R "module" .
```

### 18.2 Git commands

Check repository status:

```bash
git status
```

Download a repository:

```bash
git clone https://github.com/sailikitha248-del/VLSI_VSD.git
```

Enter the repository:

```bash
cd VLSI_VSD
```

View commit history:

```bash
git log --oneline
```

Stage changes:

```bash
git add .
```

Commit changes:

```bash
git commit -m "Update VLSI documentation"
```

Push to the configured remote:

```bash
git push
```

### 18.3 Icarus Verilog and GTKWave

Compile:

```bash
iverilog -g2012 -o sim design.v testbench.v
```

Run:

```bash
vvp sim
```

View the waveform:

```bash
gtkwave dump.vcd
```

### 18.4 Yosys

Launch:

```bash
yosys
```

Read source:

```yosys
read_verilog design.v
```

Check hierarchy:

```yosys
hierarchy -check -top design
```

Process procedural blocks:

```yosys
proc
```

Optimize:

```yosys
opt
```

Check design consistency:

```yosys
check
```

Display statistics:

```yosys
stat
```

Export netlist:

```yosys
write_verilog synthesized.v
```

Export JSON:

```yosys
write_json synthesized.json
```

### 18.5 OpenROAD

Launch OpenROAD:

```bash
openroad
```

Display help in the OpenROAD Tcl shell:

```tcl
help
```

Load a design database when the relevant files and flow setup are available:

```tcl
read_db path/to/design.odb
```

Read timing constraints:

```tcl
read_sdc path/to/design.sdc
```

Read parasitic information when generated:

```tcl
read_spef path/to/design.spef
```

Check timing:

```tcl
report_checks
```

The exact command arguments and required files depend on the OpenROAD version and the stage of the flow. Loading a database alone does not guarantee that all required libraries, constraints, and parasitic models are available.

### 18.6 OpenLANE classic flow

Start the classic interactive flow in a correctly configured installation:

```bash
flow.tcl -interactive
```

Prepare a design:

```tcl
package require openlane
prep -design picorv32a
```

Run common stages when supported by the installed flow:

```tcl
run_synthesis
run_floorplan
run_placement
run_cts
run_routing
```

The design name `picorv32a` is an example. Use a design that actually exists in the installed configuration.

### 18.7 Finding design outputs

List files under the current directory:

```bash
find . -type f
```

Search for Verilog netlists:

```bash
find . -name "*.v"
```

Search for timing reports:

```bash
find . -iname "*sta*" -o -iname "*timing*"
```

Search for layout databases:

```bash
find . -name "*.gds" -o -name "*.gdsii"
```

Search for extracted parasitics:

```bash
find . -name "*.spef"
```

These commands locate files by name; they do not confirm that the files are complete, valid, or generated by a successful flow.

---

## 19. Expected Design Outputs

### RTL and simulation

- Verilog source files
- Testbench source files
- Simulation output
- VCD waveform files
- Functional verification results

### Synthesis

- Synthesized Verilog netlist
- Cell statistics
- Area reports
- Synthesis logs
- Warnings and error reports

### Physical design

- Floorplan database
- Placement results
- Power distribution network files
- CTS results
- Routing results
- Timing reports
- Parasitic extraction data
- DRC reports
- LVS results, where configured
- GDSII layout database

The actual files depend on the configured design flow. These are expected artifact types, not a guarantee that every file exists in either repository.

## 20. Learning Outcomes

After studying these repositories, the expected learning outcomes are:

1. Understand Verilog RTL design.
2. Write and simulate testbenches.
3. Apply Boolean algebra to simplify logic.
4. Understand combinational and sequential circuits.
5. Use Yosys for synthesis and inspect netlists.
6. Understand technology mapping and standard-cell selection.
7. Calculate core area, utilization, delay, and other basic metrics.
8. Explain the OpenLANE flow and Sky130 PDK.
9. Understand floorplanning, placement, and power distribution.
10. Explain standard-cell characterization and CMOS operation.
11. Analyze setup time, hold time, and timing slack.
12. Understand clock-tree synthesis and clock skew.
13. Explain routing and parasitic extraction.
14. Understand DRC and LVS.
15. Interpret important tool commands and reports.
16. Describe the full RTL-to-GDSII flow.

21.# VSDBabySoC Pre-Synthesis Simulation

## Introduction

**VSDBabySoC** is a small System-on-Chip based on a **RISC-V processor**. It integrates digital processing with **PLL** and **DAC** blocks.

Pre-synthesis simulation is performed to verify the functionality of the RTL design before synthesis.

## Main Blocks

- **RISC-V CPU** – Executes instructions and generates digital data.
- **PLL** – Generates the required clock signal.
- **DAC** – Converts the 10-bit digital data into an analog output.

## Important Signals

| Signal | Description |
|---|---|
| `CLK` | Main clock |
| `reset` | Reset signal |
| `ENb_CP` | Charge pump enable |
| `ENb_VCO` | VCO enable |
| `VCO_IN` | VCO input |
| `VCO_OUT` | VCO output |
| `VREFH` | Reference voltage |
| `RV_TO_DAC[9:0]` | 10-bit DAC input |

## Waveform Observation

The GTKWave pre-synthesis waveform shows:

- Continuous clock switching.
- Proper reset and control signals.
- Changing `RV_TO_DAC[9:0]` values.
- Individual DAC bits switching at different rates.
- A periodic output waveform generated from the DAC data.

## Simulation Flow

text
VSDBabySoC RTL
      ↓
Testbench
      ↓
Pre-Synthesis Simulation
      ↓
GTKWave
      ↓
Waveform Verification
      ↓
Logic Synthesis

## 21. Conclusion

The VLSI learning path begins with digital logic, Verilog RTL, and functional verification. It progresses through synthesis, technology mapping, physical implementation, timing analysis, and layout verification.

The **VLSI_VSD** repository focuses on the RTL and synthesis foundations, while **VLSI_PHYSICAL-DESIGN** focuses on physical design and the RTL-to-GDSII implementation flow.

The formulas in this guide provide the mathematical foundation for estimating area, utilization, power, timing, clock behavior, and interconnect delay. The command reference provides examples for simulation, synthesis, repository management, and physical-design tools.

The ultimate objective is to understand not only how a digital circuit works, but also how it can be transformed into a physically implemented layout that meets functional, timing, area, power, and design-rule requirements.

## 22. References

- [VLSI_VSD GitHub Repository](https://github.com/sailikitha248-del/VLSI_VSD)
- [VLSI_PHYSICAL-DESIGN GitHub Repository](https://github.com/sailikitha248-del/VLSI_PHYSICAL-DESIGN)
- [Yosys Documentation](https://yosyshq.readthedocs.io/)
- [OpenROAD Documentation](https://openroad.readthedocs.io/)
- [OpenLane 2 Documentation](https://openlane2.readthedocs.io/)
- [SkyWater PDK](https://github.com/google/skywater-pdk)
- [Sky130 PDK Documentation](https://skywater-pdk.readthedocs.io/)

**Documentation note:** Formulas are simplified where necessary to explain the underlying concepts. Real implementation results depend on timing constraints, cell libraries, process corners, tool versions, and physical design conditions. Always use the reports and documentation for the exact flow being executed.
