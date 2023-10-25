# Implementation of 1:8 Demultiplexer
The Demultiplexer is a one-to-many circuit. By using it, the transmission of data can be done through one single input to a number of output data lines.
Generally, Demultiplexers are used in decoder circuits and Boolean function generators.

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/7c55d595-222d-4350-bbc6-f89ee98fe604)

## 1:8 Demultiplexer Truth Table
The Truth Table for a 1:8 Demultiplexer is given below. ( Where A is the Input and , S0,S1,S2 are the three select lines and Y0,Y1,Y2,Y3,Y4,Y5,Y6,Y7 are the eight outputs of 1:8 Demultiplexer.

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/c698958b-f52b-45c0-9857-c5c64b88321b)

## Verilog Code 1:8 Demultiplexer

The Verilog code for 1:8 Demultiplexer is given below :
```
module pes_demux_1_8 (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
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
module pes_demux_1_8_tb();
	// Inputs
	reg i;
	reg [2:0] sel;
	
	//TB Signals
	reg clk,reset;

	// Outputs
	wire o7,o6,o5,o4,o3,o2,o1,o0;

        // Instantiate the Unit Under Test (UUT)
	pes_demux_1_8 uut (
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
iverilog pes_demux_1_8.v pes_demux_1_8_tb.v
./a.out
gtkwave pes_demux_1_8_tb.vcd
```
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/b3cd69e1-4827-4396-a4bb-bc9f8e803e46)

## Synthesis
Code for the synthesis is given below:
```
yosys:read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
yosys:read_verilog pes_demux_1_8.v
yosys:synth -top pes_demux_1_8
yosys:abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
yosys:show
```
![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/fc622d88-83dc-4597-b3f3-0d0d573b68e6)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/39d1fbd9-7fbe-4bee-a169-cdc7328c3629)

## Gate level simulation
Code for gate level simulation is given below:
```
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v pes_demux_1_8.v pes_demux_1_8_tb.v
./a.out
gtkwave pes_demux_1_8_tb.vcd
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

## Invoking Openlane

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

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/1609f2cb-42c5-4bfb-b27f-5f8e4a9c9dad)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/4434c837-0cd7-4504-8d8f-9bbe4bab946b)


## Floorplan

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

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/e4f61e5d-4b36-43db-a00a-f74982f938d2)

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/d5ce18ea-5538-4443-a7c0-45bb05355e9f)

## Placement

To run placement : Use `run_placement` command

![image](https://github.com/spurthimalode/pes_demux_1_8/assets/142222859/76d5c171-ab27-4bf6-b1d3-249e2c2b759d)

