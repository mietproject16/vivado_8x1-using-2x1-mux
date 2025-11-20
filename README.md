## vivado_8x1-using-2x1-mux
## REG NUM :
## NAME :
## EXPERIMENT – 1  Design and Implementation of 8×1 Multiplexer Using 2×1 Multiplexers

## AIM
To design, simulate and implement an 8×1 multiplexer using 2×1 multiplexers in Verilog and verify its operation on the Boolean Board (Spartan-7).

## TOOLS REQUIRED
Vivado Design Suite 2023/2024
Boolean Board – Spartan-7 FPGA
Computer with USB connection
Micro USB cable

## THEORY
A multiplexer (MUX) is a combinational circuit that selects one input from multiple inputs and forwards it to the output.
An 8×1 MUX selects one of 8 inputs using 3 select lines (S2 S1 S0).

Truth Table Concept:
```
S2 S1 S0	Output
0  0  0	   d0
0  0  1	   d1
0  1  0	   d2
0  1  1	   d3
1  0  0	   d4
1  0  1	   d5
1  1  0	   d6
1  1  1	   d7
```
To build an 8×1 MUX using 2×1 MUXes:
First stage: 4 multiplexers → choose between pairs of inputs
Second stage: 2 multiplexers → choose between outputs of previous stage
Final stage: 1 multiplexer → final output
Total used = 7 multiplexers of 2×1

VERILOG PROGRAM
```
mux2x1.v
module mux2x1(input d0, d1, input sel, output y);
    assign y = sel ? d1 : d0;
endmodule

mux8x1.v
module mux8x1(
    input [7:0] d,
    input [2:0] sel,
    output y
);
    wire [3:0] w1;
    wire [1:0] w2;

    mux2x1 m1(d[0], d[1], sel[2], w1[0]);
    mux2x1 m2(d[2], d[3], sel[2], w1[1]);
    mux2x1 m3(d[4], d[5], sel[2], w1[2]);
    mux2x1 m4(d[6], d[7], sel[2], w1[3]);

    mux2x1 m5(w1[0], w1[1], sel[1], w2[0]);
    mux2x1 m6(w1[2], w1[3], sel[1], w2[1]);

    mux2x1 m7(w2[0], w2[1], sel[0], y);

endmodule
```
## TESTBENCH
```
module tb_mux8x1;
    reg [7:0] d;
    reg [2:0] sel;
    wire y;

    mux8x1 DUT (.d(d), .sel(sel), .y(y));

    initial begin
        $monitor("Time=%0t | d=%b | sel=%b | y=%b", $time, d, sel, y);

        d = 8'b10101010;
        sel = 3'b000; #10;
        sel = 3'b001; #10;
        sel = 3'b010; #10;
        sel = 3'b011; #10;
        sel = 3'b100; #10;
        sel = 3'b101; #10;
        sel = 3'b110; #10;
        sel = 3'b111; #10;
        d = 8'b11001100; sel = 3'b000; #10;
        sel = 3'b111; #10;
        $finish;
    end
endmodule
```
## XDC FILE (Boolean Board – Spartan-7)

8 switches used for d[7:0]
3 switches used for sel[2:0]
1 LED used for output y
```
# Data Inputs d[7:0]
set_property -dict {PACKAGE_PIN V2 IOSTANDARD LVCMOS33} [get_ports {d[0]}]
set_property -dict {PACKAGE_PIN U2 IOSTANDARD LVCMOS33} [get_ports {d[1]}]
set_property -dict {PACKAGE_PIN U1 IOSTANDARD LVCMOS33} [get_ports {d[2]}]
set_property -dict {PACKAGE_PIN T2 IOSTANDARD LVCMOS33} [get_ports {d[3]}]
set_property -dict {PACKAGE_PIN T1 IOSTANDARD LVCMOS33} [get_ports {d[4]}]
set_property -dict {PACKAGE_PIN R2 IOSTANDARD LVCMOS33} [get_ports {d[5]}]
set_property -dict {PACKAGE_PIN R1 IOSTANDARD LVCMOS33} [get_ports {d[6]}]
set_property -dict {PACKAGE_PIN P2 IOSTANDARD LVCMOS33} [get_ports {d[7]}]

# Select Inputs
set_property -dict {PACKAGE_PIN N2 IOSTANDARD LVCMOS33} [get_ports {sel[0]}]
set_property -dict {PACKAGE_PIN N1 IOSTANDARD LVCMOS33} [get_ports {sel[1]}]
set_property -dict {PACKAGE_PIN M2 IOSTANDARD LVCMOS33} [get_ports {sel[2]}]

# Output LED
set_property -dict {PACKAGE_PIN G1 IOSTANDARD LVCMOS33} [get_ports {y}]
```
## PROCEDURE
Open Vivado → Create New Project
Add Verilog files (mux2x1.v, mux8x1.v, testbench)
Set mux8x1 as Top Module
Add XDC constraints file
Run Behavioral Simulation
Verify output waveforms
Run Synthesis → Implementation → Generate Bitstream
Program Boolean Board
Change switches and verify LED output

## OUTPUT

## RESULT
The 8×1 multiplexer was successfully designed using 2×1 multiplexers, simulated in Vivado, and implemented on the Boolean Board (Spartan-7).
The output LED correctly displayed the selected input based on the select switch values.
