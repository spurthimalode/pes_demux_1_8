# Implementation of 1:8 Demultiplexer
The Demultiplexer is a one-to-many circuit. By using it, the transmission of data can be done through one single input to a number of output data lines.
Generally, Demultiplexers are used in decoder circuits and Boolean function generators.

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/20d30743-cb7b-4a42-bd65-221141ed1301)

## 1:8 Demultiplexer Truth Table
The Truth Table for a 1:8 Demultiplexer is given below. ( Where A is the Input and , S0,S1,S2 are the three select lines and Y0,Y1,Y2,Y3,Y4,Y5,Y6,Y7 are the eight outputs of 1:8 Demultiplexer.

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/c698958b-f52b-45c0-9857-c5c64b88321b)

## Verilog Code 1:8 Demultiplexer

The Verilog code for 1:8 Demultiplexer is given below :
```
module demux_1_8 (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i , input clk);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (posedge clk)
begin
y_int = 8'b0;
	case(sel)
		3'b000 : y_int[0] = i;
		3'b001 : y_int[1] = i;
		3'b010 : y_int[2] = i;
		3'b011 : y_int[3] = i;
		3'b100 : y_int[4] = i;
		3'b101 : y_int[5] = i;
		3'b110 : y_int[6] = i;
		3'b111 : y_int[7] = i;
	endcase

end
endmodule
```
## Test Bench Code for 1:8 Demultiplexer

The test bench check that each combination of input lines that connects the appropriate input to the output. The test bench code in Verilog for 1:8 Demultiplexer is given below :
```
`timescale 1ns / 1ps
module demux_1_8_tb();
	// Inputs
	reg i;
	reg [2:0] sel;
	
	//TB Signals
	reg clk,reset;

	// Outputs
	wire o7,o6,o5,o4,o3,o2,o1,o0;

        // Instantiate the Unit Under Test (UUT)
	demux_1_8 uut (
		.sel(sel),
		.o0(o0),
		.o1(o1),
		.o2(o2),
		.o3(o3),
		.o4(o4),
		.o5(o5),
		.o6(o6),
		.o7(o7),
		.i(i)
	);

	initial begin
	$dumpfile("pes_demux_1_8_tb.vcd");
	$dumpvars(0,pes_demux_1_8_tb);
	// Initialize Inputs
	i = 1'b0;
	clk = 1'b0;
	reset = 1'b0 ;  #1;
	reset = 1'b1 ;  #10;
	reset = 1'b0;

	#3900 $finish;
	end

always #17 i = ~i;
always #300 clk = ~clk;

always @ (posedge clk , posedge reset)
begin
	if(reset)
		sel <= 3'b000;
	else
		sel <= sel + 1;
end
endmodule
```
## Simulation
Code for the simulation is given below:
```
iverilog demux_1_8.v demux_1_8_tb.v
./a.out
gtkwave demux_1_8_tb.vcd
```
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/b3cd69e1-4827-4396-a4bb-bc9f8e803e46)

## Synthesis
Code for the synthesis is given below:
```
yosys:read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
yosys:read_verilog demux_1_8.v
yosys:synth -top demux_1_8
yosys:abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
yosys:show
```
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/fc622d88-83dc-4597-b3f3-0d0d573b68e6)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/39d1fbd9-7fbe-4bee-a169-cdc7328c3629)

## Gate level simulation
Code for gate level simulation is given below:
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v demux_1_8.v demux_1_8_tb.v
./a.out
gtkwave demux_1_8_tb.vcd
```
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/1afee132-c851-42ab-82ec-aec29e282197)

## OpenLane Flow

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/c7bfc467-da6d-4bb6-8cae-31f7d55cc573)

OpenLANE flow consists of several stages. By default, all flow steps are run in sequence. Each stage may consist of multiple sub-stages. OpenLANE can also be run interactively as shown here.

  1. Synthesis

  <ul>
      <li>Yosys - Performs RTL synthesis using GTech mapping</li>
      <li>abc - Performs technology mappin to standard cells described in the PDK. We can adjust synthesis techniques using different integrated abc scripts to get desired results</li>
      <li>OpenSTA - Performs static timing analysis on the resulting netlist to generate timing reports</li>
      <li>Fault – Scan-chain insertion used for testing post fabrication. Supports ATPG and test patterns compaction</li>
  </ul>

  2. Floorplan and PDN

  <ul>
      <li>Init_fp - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)</li>
      <li>Ioplacer - Places the macro input and output ports</li>
      <li>PDN - Generates the power distribution network</li>
      <li>Tapcell - Inserts welltap and decap cells in the floorplan</li>
      <li>Placement – Placement is done in two steps, one with global placement in which we place the designs across the chip, but they will not be legal placement with some standard cells overlapping each other, to fix this we perform a detailed placement which legalizes the design and ensures they fit in the standard cell rows</li>
      <li>RePLace - Performs global placement</li>
      <li>Resizer - Performs optional optimizations on the design</li>
      <li>OpenPhySyn - Performs timing optimizations on the design</li>
      <li>OpenDP - Perfroms detailed placement to legalize the globally placed components</li>
  </ul>
  3. CTS

  <ul>
      <li>TritonCTS - Synthesizes the clock distribution network</li>
  </ul>


4. Routing

  <ul>
      <li>FastRoute - Performs global routing to generate a guide file for the detailed router
      </li>
      <li>TritonRoute - Performs detailed routing from global routing guides</li>
      <li>SPEF-Extractor - Performs SPEF extraction that include parasitic information</li>
  </ul>

5. GDSII Generation

  <ul>
      <li>Magic - Streams out the final GDSII layout file from the routed def</li>
  </ul>

 6. Checks

  <ul>
      <li>Magic - Performs DRC Checks & Antenna Checks</li>
      <li>Netgen - Performs LVS Checks </li>
  </ul>

## Openlane Automated Flow
The command to run the automated flow :
```
./flow.tcl -design openlane/<design folder name> -tag <run folder name>

```
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/48b8f037-79c0-41e3-ae8e-cb3f194fb1dd)

Flow completed without any fatal errors

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/60b24d98-797b-45eb-8266-73cc241d0407)

## Openlane interactive mode
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/84ca3818-e45c-41fd-9ea7-442508693c30)

 - ./flow.tcl is the script which runs the OpenLANE flow
 - OpenLANE can be run interactively or in autonomous mode 
 - To run interactively, use the -interactive option with the ./flow.tcl script 

## Package Importing
Different software dependencies are needed to run OpenLANE. To import these into the OpenLANE tool we need to run:

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/cd8912a2-c032-4992-acc9-92bc96c2abfc)

## Design Folder Heirarchy
Each design hierarchy comes with two distinct components:
1. Src folder - Contains verilog files and sdc constraint files
2. Config.tcl files - Design specific configuration switches used by OpenLANE
 
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/c9ab9c48-1948-46e6-b6ba-57b381446df0)

## Prepare Design
prep is used to make file structure for our design. To set this up do:

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/2fce0666-b289-489a-aa5b-4bac18ec9d16)

## Synthesis

To run synthesis: Use `run_synthesis` command

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/9e2358d4-0d57-4b1c-8748-50ece7e882e4)

Flop ratio : Flop ratio = Number of D Flip flops / Total number of cells
Flop ratio for my design is 8/32 = 0.25

Synthesis was Successful

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/4434c837-0cd7-4504-8d8f-9bbe4bab946b)


## Floorplan
In the VLSI physical design, floorplanning is an essential design step, as it determines the size, shape, and locations of modules in a chip and as such it estimates the total chip area, the interconnects, and, delay.

To run floorplan : Use `run_floorplan` command

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/817eeba9-d188-4259-8290-dea04dda332b)

The output the the floorplanning phase is a DEF file which describes core area and placement of standard cell SITES:

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/918dbbc2-f927-4bdf-b081-9e2a4f992a21)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/a70cc5b7-27f7-4832-b932-435b26a6ef40)

## Viewing Floorplan In Magic
To view our floorplan in Magic we need to provide three files as input:

1. Magic technology file (sky130A.tech)
2. Def file of floorplan
3. Merged LEF file
   
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/5d144aa8-aac9-4cd8-b951-64d4cb0b1d62)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/e4f61e5d-4b36-43db-a00a-f74982f938d2)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/dc5106bf-d35a-432b-b992-f139cf79cdfe)

## Placement

Placement is the process of placing the standard cells inside the core boundary in an optimal location.

To run placement : Use `run_placement` command

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/76d5c171-ab27-4bf6-b1d3-249e2c2b759d)

Placement Analysis

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/bd686abf-23cc-45c9-8b1e-b95923fa5f15)

Post the placement run, a .def file will have been created within the results/placement directory.

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/f07134b2-b178-4cf7-99c6-97353448b49a)

To view placement in magic:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read demux_1_8.placement.def &
```

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/0c7d03d9-2c6e-477a-8c34-6ccc0a838cf1)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/73e80e7c-b144-49ad-891c-ad61acabb864)

demux_1_8.placement.def.png

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/c4cde960-a4aa-4f83-bd64-9f86c687875b)

## Clock Tree Synthesis
Clock Tree Synthesis is a technique for distributing the clock equally among all sequential parts of a VLSI design. The purpose of Clock Tree Synthesis is to reduce skew and delay. Clock Tree Synthesis is provided the placement data as well as the clock tree limitations as input.

To run clock tree synthesis : Use `run_cts` command

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/3c1ad4d0-73dc-422e-b857-fa5da3e9cb56)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/31d62538-d764-4eb9-b0b6-d30214aa4031)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/662443f2-3b2b-4cc3-8d5f-6470da302b43)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/52854649-f4fc-4860-a756-fad0caaa6cab)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/c5d0bd6c-cb53-4ffc-b14e-b77b29f82a31)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/9ebeed8d-76a7-4681-98fa-33231c653907)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/8017e562-cc84-4d7f-bd54-18e0f3f4dd72)

## Routing
Implements the interconnect system between standard cells using the remaining available metal layers after CTS and PDN generation. The routing is performed on routing grids to ensure minimal DRC errors.

To run routing : Use `run_routing` command

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/5aa8f304-d667-41a5-a98a-af12af958073)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/45189536-49db-48bb-8f6b-f005f8305506)

Routing was successful 

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/bf886907-52d3-4dc0-a6ca-259a2d76054b)

Post the routing run, a .def file will have been created within the results/routing directory.

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/cbe86c0a-2a41-4d05-a062-e38e5bed301e)

To view routing in magic:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read demux_1_8.def &
```

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/038a7ef7-2e43-4c7c-812d-89284c77f696)

Area Report

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/0e53ca50-3811-4234-82a5-f59426c360ad)

demux_1_8.def.png

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/c5631e78-596f-4d94-a282-70da018c3bec)

## Reports
Die Area

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/559fa849-6552-4ad0-9728-85162bfdb4b2)

Core Area

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/5282a930-6353-4cc3-9c98-de05c4d6c1e9)

Power Report

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/b1f89ee5-6419-410f-81c6-a767b3cdbd32)

Summary

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/55610b25-8dae-4ddb-bd94-1c85f6c9452c)

Manufacturability

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/e3fc36a1-05fd-4023-87c4-69a2885173a6)
