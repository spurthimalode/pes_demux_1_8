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
