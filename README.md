# Fractional Divider based 3-bit Fractional 4-div PLL
A submission for Cloud Based Analog IC Design Hackathon conducted by IIIT-H by Rohin Bhandari, MS in EE, Columbia University

## Table of Contents
  * [Abstract](#Abstract)
  * [Tools Used](#Tools-Used)
  * [Explanation](#Explanation)
  * [Schematics](#Schematics)
  * [Simulation Results](#Simulation-Results)
  * [Netlist File](#Netlist-File)
  * [Acknowledgements](#Acknowledgements)
  * [References](#References)

## Abstract
The objective was to create a Fractional Divider based 3-bit Fractional 4-div PLL using 28 nm CMOS technology. A 4-div 3-bit Fractional PLL implies that the frequency division is going to be of the form: _f_<sub>VCO</sub> = (4 + (0.125 x m)) _f_<sub>ref</sub> where m is the 3-bit number (0 to 7).

## Tools Used
 * Synopsys Custom Compiler: The Synopsys Custom Compiler™ design environment is a modern solution for full-custom analog, custom digital, and mixed-signal IC design. As the heart of the Synopsys Custom Design Platform, Custom Compiler provides design entry, simulation management and analysis, and custom layout editing features. This tool was used to design the circuit on a transistor level.
 * Synopsys Primewave: PrimeWave™ Design Environment is a comprehensive and flexible environment for simulation setup and analysis of analog, RF, mixed-signal design, custom-digital and memory designs within the Synopsys Custom Design Platform. This tool helped in various types of simulations of the above designed circuit.
 * Synopsys 28nm PDK: The Synopsys 28nm Process Design Kit (PDK) was used in creation and simulation of the above designed circuit.

## Explanation

The schematic for the PLL block is as follows:

![Fractional_div_PLL_schematic_with_explanation](https://user-images.githubusercontent.com/77510951/156219863-ff400813-80b7-47a6-ae53-571c2c335d55.png)

The schematic for the Digital Phase Accumulator is as follows:

![digital_phase_accumulator_schematic](https://user-images.githubusercontent.com/77510951/156223228-666911d7-1208-4cd7-b9a8-ef3ca4bc6214.png | width=100)

The idea behind a Fractional divider based N-div M-bit Fractional Divider PLL is to divide the output of a VCO into fractional delays wrt. one cycle (i.e. 1/2<sup>M</sup>) and select the relevant parts to supply the output. A Delay Locked Loop (DLL) made by the cascading tunable delay elements and the phase frequency detector ensures that the delay caused by the cascade is exactly one cycle of the input. We can use a programmable divider controlled by an accumulator to help reach the DLL into a lock.

The adder feeded in a loop through the register acts as an accumulator counting the steps depending on the fractional bits provided. The carry from the adder controls the frequency divider, which increments the divisor by 1 so that the average of the division occuring across the loop is a fraction. The count in the accumulator controls a multiplexer, which selects the desired output from the delay line.

## Schematics

All the schematics made for this project are shown below. Circuit symbols were custom designed to ease recognizibility. Feel free to browse the directory to view the screenshots of the schematics without the explanation.

### NAND Gate
The schematic for the NAND gate is as follows:

![nand_gate_schematic](https://user-images.githubusercontent.com/77510951/156221129-4deed2bb-ee22-4f7f-ad6d-b10af3eeb772.png)

### D Flip-Flop
The schematic for the D Flip-Flop is as follows:

![d_flip_flop_schematic](https://user-images.githubusercontent.com/77510951/156221143-1fd582ee-170b-41c5-88ca-bc1e1b331f6b.png)

This was designed using Complementary Pass Logic (CPL). Two latch designs were cascaded to make a 1-bit Flip-Flop.

This was used in realizing a 3-bit register, with the same unit used in parallel 3 times. The 3-bit register was used in the Digital Phase Accumulator.

### Adder
The schematic for the Adder is as follows:

![full_adder_schematic](https://user-images.githubusercontent.com/77510951/156220644-49034502-76dd-4fea-a50b-d64dcf339da5.png)

The schematic was designed using the self-dual properties, which makes the PMOS structure a mirror image of the NMOS structure. 

This was used in realizing a 3-bit adder, with the same unit used in parallel 3 times. The 3-bit adder was used in the Digital Phase Accumulator.

### Multiplexer
The schematic for the Multiplexer is as follows:

![multiplexer_schematic_full_view_with_explanation](https://user-images.githubusercontent.com/77510951/156219991-80a7f945-bf8a-4da5-8881-a04be4991b0a.png)

This was designed using CPL. Complementary bits were gated together to reduce design effort. Strong inverters were used at the output to increase input sensitivity.

### Current-Starved Tunable Delay Element
The schematic for the Delay Element is as follows:

![current_starved_tunable_delay_element_schematic_with_explanation](https://user-images.githubusercontent.com/77510951/156219637-c97a423b-456e-4f92-8f69-d83b99c49aff.png)

The technology file provided was unfit to make a varactor since the tuning range seen during tests was close to 0.0001%. Hence a current starved element was realised instead.

When tested, the V<sub>ctrl</sub> signal was increasing delay at low values and was giving inadequete results when V<sub>ctrl</sub> went less than 0.4 V. Hence a skewed inverter was used which was biased by a resistive divider (assuming the device would be operated at V<sub>DD</sub> = 1 V).

### Programmable Frequency Divider
The schematic for the Programmable Frequency Divider is as follows:

![programmable_frequency_divider_schematic](https://user-images.githubusercontent.com/77510951/156221183-2b2fcc49-efcb-4b7c-b6a1-58e03ff0b544.png)

The design was custom made by merging the logic for a _f/4_ divider and a _f/5_ divider with a 50% duty cycle. Relevant inputs were taken out with CPL.

### Phase Detector & Charge Pump
The schematic for the Phase Detector & Charge Pump block is as follows:

![Phase_detector_and_Charge_Pump_schematic](https://user-images.githubusercontent.com/77510951/156221224-e772ba82-a907-4d9f-a463-515845167c55.png)


## Simulation Results

NOTE: All blocks and testbenches were run at V<sub>DD</sub> = 1 V

The Phase Detector & Charge Pump block was tested with two square wave signals: Input was at 2.4 GHz (i.e. Period of 416 ps) and the reference was at 600 MHz (i.e. Period of 1666 ps). The output is a growing ramp signal, as expected. 

![Phase_detector_and_Charge_Pump_transient_results](https://user-images.githubusercontent.com/77510951/156228147-6e1dffff-9eaf-429f-8ddc-906022593e37.png)

The Digital Phase Accumulator's operation was tested at 5 GHz (i.e. period of 200 ps). The waveforms oberserved were logically correct.

![digital_phase_accumulator_transient_result](https://user-images.githubusercontent.com/77510951/156228201-1f6697df-ff91-4927-b32b-f58c610dc664.png)

When control is logic 0, the programmable divider divdes by 4; else it divides by 5. The frequency divider's transient results are as follows:

![frequency_divider_transient_results](https://user-images.githubusercontent.com/77510951/156228255-dad6112c-3e74-4ec5-a8b0-03fcf90e661f.png)

The current-starved tunable delay element was tested by cascading two element blocks and given varying control voltages to test the delay at their ends. With an input of 2.5 GHz, the delay element failed to respond at control voltages less than 0.4 V and the delay observed was inversely proportional to the control voltage:

![tunable delay_transient_results](https://user-images.githubusercontent.com/77510951/156228377-0d4ac40c-01d7-4c12-9104-aec72f90e6b0.png)

At control = 0.5 V, delay given by one element was ~35ps

At control = 0.75 V, delay given by one element was ~5ps

At control = 1 V, delay given by one element was ~1ps

Fractional PLL operating at a fraction of 5/8:

![fractional_div_PLL_transient_results](https://user-images.githubusercontent.com/77510951/156228310-f872cb16-964b-45e5-bc69-74ec166ee699.png)

Fractional PLL at 3/8:

![pll_3_by_8_transient_results](https://user-images.githubusercontent.com/77510951/156228412-43f8c46d-f223-4996-a1b1-ebb0e763ba79.png)

Possible issue: The Digital Phase Accumulator fails to reset in time, giving an incorrect value of the control signal for the programmable frequency divider.

## Netlist File

```
*  Generated for: PrimeSim
*  Design library name: project1
*  Design cell name: Fractional_N_PLL_test
*  Design view name: schematic
.lib 'saed32nm.lib' TT
.lib 'saed32nm.lib' TT
.param m0=1 m1=0 m2=1
*Custom Compiler Version S-2021.09
*Tue Mar  1 15:24:54 2022

.global gnd!
********************************************************************************
* Library          : project1
* Cell             : inverter_2u_1u
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt inverter_2u_1u gnd_1 in out vdd
xm0 out in gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm1 out in vdd vdd p105 w=2u l=30n nf=1 m=1
.ends inverter_2u_1u

********************************************************************************
* Library          : project1
* Cell             : mux_3_selects
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt mux_3_selects gnd_1 in0 in1 in2 in3 in4 in5 in6 in7 out s0 s1 s2 vdd
xm52 s2_ s2 vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm50 s1_ s1 vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm49 s0_ s0 vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm35 net56 s2 net35 vdd p105 w=2u l=30n nf=1 m=1
xm34 net39 s0_ in3 vdd p105 w=2u l=30n nf=1 m=1
xm33 net35 s1_ net39 vdd p105 w=2u l=30n nf=1 m=1
xm32 net56 s2_ net34 vdd p105 w=2u l=30n nf=1 m=1
xm31 net38 s0 in4 vdd p105 w=2u l=30n nf=1 m=1
xm30 net34 s1 net38 vdd p105 w=2u l=30n nf=1 m=1
xm29 net56 s2 net33 vdd p105 w=2u l=30n nf=1 m=1
xm28 net37 s0 in2 vdd p105 w=2u l=30n nf=1 m=1
xm27 net33 s1_ net37 vdd p105 w=2u l=30n nf=1 m=1
xm26 net56 s2_ net32 vdd p105 w=2u l=30n nf=1 m=1
xm25 net36 s0_ in5 vdd p105 w=2u l=30n nf=1 m=1
xm24 net32 s1 net36 vdd p105 w=2u l=30n nf=1 m=1
xm17 net56 s2 net25 vdd p105 w=2u l=30n nf=1 m=1
xm16 net27 s0_ in1 vdd p105 w=2u l=30n nf=1 m=1
xm15 net25 s1 net27 vdd p105 w=2u l=30n nf=1 m=1
xm14 net56 s2_ net24 vdd p105 w=2u l=30n nf=1 m=1
xm13 net26 s0 in6 vdd p105 w=2u l=30n nf=1 m=1
xm12 net24 s1_ net26 vdd p105 w=2u l=30n nf=1 m=1
xm8 net56 s2 net14 vdd p105 w=2u l=30n nf=1 m=1
xm7 net15 s0 in0 vdd p105 w=2u l=30n nf=1 m=1
xm6 net14 s1 net15 vdd p105 w=2u l=30n nf=1 m=1
xm4 net56 s2_ net12 vdd p105 w=2u l=30n nf=1 m=1
xm2 net12 s1_ net10 vdd p105 w=2u l=30n nf=1 m=1
xm0 net10 s0_ in7 vdd p105 w=2u l=30n nf=1 m=1
xm53 s2_ s2 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm51 s1_ s1 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm48 s0_ s0 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm47 net35 s2_ net56 gnd_1 n105 w=1u l=30n nf=1 m=1
xm46 in3 s0 net39 gnd_1 n105 w=1u l=30n nf=1 m=1
xm45 net39 s1 net35 gnd_1 n105 w=1u l=30n nf=1 m=1
xm44 net34 s2 net56 gnd_1 n105 w=1u l=30n nf=1 m=1
xm43 in4 s0_ net38 gnd_1 n105 w=1u l=30n nf=1 m=1
xm42 net38 s1_ net34 gnd_1 n105 w=1u l=30n nf=1 m=1
xm41 net33 s2_ net56 gnd_1 n105 w=1u l=30n nf=1 m=1
xm40 in2 s0_ net37 gnd_1 n105 w=1u l=30n nf=1 m=1
xm39 net37 s1 net33 gnd_1 n105 w=1u l=30n nf=1 m=1
xm38 net32 s2 net56 gnd_1 n105 w=1u l=30n nf=1 m=1
xm37 in5 s0 net36 gnd_1 n105 w=1u l=30n nf=1 m=1
xm36 net36 s1_ net32 gnd_1 n105 w=1u l=30n nf=1 m=1
xm23 net25 s2_ net56 gnd_1 n105 w=1u l=30n nf=1 m=1
xm22 in1 s0 net27 gnd_1 n105 w=1u l=30n nf=1 m=1
xm21 net27 s1_ net25 gnd_1 n105 w=1u l=30n nf=1 m=1
xm20 net24 s2 net56 gnd_1 n105 w=1u l=30n nf=1 m=1
xm19 in6 s0_ net26 gnd_1 n105 w=1u l=30n nf=1 m=1
xm18 net26 s1 net24 gnd_1 n105 w=1u l=30n nf=1 m=1
xm11 net14 s2_ net56 gnd_1 n105 w=1u l=30n nf=1 m=1
xm10 in0 s0_ net15 gnd_1 n105 w=1u l=30n nf=1 m=1
xm9 net15 s1_ net14 gnd_1 n105 w=1u l=30n nf=1 m=1
xm5 net12 s2 net56 gnd_1 n105 w=1u l=30n nf=1 m=1
xm3 net10 s1 net12 gnd_1 n105 w=1u l=30n nf=1 m=1
xm1 in7 s0 net10 gnd_1 n105 w=1u l=30n nf=1 m=1
xi55 gnd_1 net66 out vdd inverter_2u_1u
xi54 gnd_1 net56 net66 vdd inverter_2u_1u
.ends mux_3_selects

********************************************************************************
* Library          : project1
* Cell             : adder_1bit
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt adder_1bit a b cin cout s gnd_1 vdd
xm26 cout cout_ vdd vdd p105 w=2u l=30n nf=1 m=1
xm24 s s_ vdd vdd p105 w=1u l=30n nf=1 m=1
xm11 net26 cin vdd vdd p105 w=3u l=30n nf=1 m=1
xm10 net24 a vdd vdd p105 w=2u l=30n nf=1 m=1
xm9 s_ a net15 vdd p105 w=3u l=30n nf=1 m=1
xm8 net15 b net26 vdd p105 w=3u l=30n nf=1 m=1
xm7 s_ cout_ net24 vdd p105 w=2u l=30n nf=1 m=1
xm6 net24 cin vdd vdd p105 w=2u l=30n nf=1 m=1
xm5 net24 b vdd vdd p105 w=2u l=30n nf=1 m=1
xm4 cout_ b net11 vdd p105 w=2u l=30n nf=1 m=1
xm3 net11 a vdd vdd p105 w=2u l=30n nf=1 m=1
xm2 cout_ cin net6 vdd p105 w=2u l=30n nf=1 m=1
xm1 net6 b vdd vdd p105 w=2u l=30n nf=1 m=1
xm0 net6 a vdd vdd p105 w=2u l=30n nf=1 m=1
xm27 cout cout_ gnd_1 gnd_1 n105 w=0.5u l=30n nf=1 m=1
xm25 s s_ gnd_1 gnd_1 n105 w=0.5u l=30n nf=1 m=1
xm23 net51 cin gnd_1 gnd_1 n105 w=1.5u l=30n nf=1 m=1
xm22 net49 a gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm21 s_ cout_ net49 gnd_1 n105 w=1u l=30n nf=1 m=1
xm20 net49 b gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm19 net49 cin gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm18 net41 b net51 gnd_1 n105 w=1.5u l=30n nf=1 m=1
xm17 s_ a net41 gnd_1 n105 w=1.5u l=30n nf=1 m=1
xm16 cout_ cin net35 gnd_1 n105 w=1u l=30n nf=1 m=1
xm15 net35 a gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm14 net35 b gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm13 net31 a gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm12 cout_ b net31 gnd_1 n105 w=1u l=30n nf=1 m=1
.ends adder_1bit

********************************************************************************
* Library          : project1
* Cell             : adder_3bit
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt adder_3bit a0 a1 a2 b0 b1 b2 cout gnd_1 s0 s1 s2 sub vdd
xi2 a2 b2 net21 cout s2 gnd_1 vdd adder_1bit
xi1 a1 b1 net14 net21 s1 gnd_1 vdd adder_1bit
xi0 a0 b0 sub net14 s0 gnd_1 vdd adder_1bit
.ends adder_3bit

********************************************************************************
* Library          : project1
* Cell             : d_flipflop
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt d_flipflop clk d q rst gnd_1 vdd
xm36 net66 rst_ net70 net67 n105 w=1u l=30n nf=1 m=1
xm35 rst rst net70 net67 n105 w=1u l=30n nf=1 m=1
xm31 rst_ rst gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm30 rst rst net62 net63 n105 w=1u l=30n nf=1 m=1
xm29 net69 rst_ net62 net63 n105 w=1u l=30n nf=1 m=1
xm23 net56 net66 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm22 net56 clk net69 gnd_1 n105 w=1u l=30n nf=1 m=1
xm21 net54 q gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm20 net54 clk_ net69 gnd_1 n105 w=1u l=30n nf=1 m=1
xm17 clk_ clk gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm12 net47 clk net50 gnd_1 n105 w=1u l=30n nf=1 m=1
xm11 net47 net70 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm5 net45 clk_ net50 gnd_1 n105 w=1u l=30n nf=1 m=1
xm15 net66 net50 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm1 net45 d gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm19 q net62 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm32 rst_ rst vdd vdd p105 w=2u l=30n nf=1 m=1
xm28 net56 net66 vdd vdd p105 w=2u l=30n nf=1 m=1
xm27 net69 clk_ net56 vdd p105 w=2u l=30n nf=1 m=1
xm26 net54 q vdd vdd p105 w=2u l=30n nf=1 m=1
xm25 net69 clk net54 vdd p105 w=2u l=30n nf=1 m=1
xm24 q net62 vdd vdd p105 w=2u l=30n nf=1 m=1
xm18 clk_ clk vdd vdd p105 w=2u l=30n nf=1 m=1
xm10 net50 clk net45 vdd p105 w=2u l=30n nf=1 m=1
xm13 net47 net70 vdd vdd p105 w=2u l=30n nf=1 m=1
xm6 net45 d vdd vdd p105 w=2u l=30n nf=1 m=1
xm16 net66 net50 vdd vdd p105 w=2u l=30n nf=1 m=1
xm14 net50 clk_ net47 vdd p105 w=2u l=30n nf=1 m=1
.ends d_flipflop

********************************************************************************
* Library          : project1
* Cell             : register_3bit
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt register_3bit b0 b1 b2 clk gnd_1 out0 out1 out2 rst vdd
xi2 clk b2 out2 rst gnd_1 vdd d_flipflop
xi1 clk b1 out1 rst gnd_1 vdd d_flipflop
xi0 clk b0 out0 rst gnd_1 vdd d_flipflop
.ends register_3bit

********************************************************************************
* Library          : project1
* Cell             : digital_phase_accumulator
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt digital_phase_accumulator b0 b1 b2 carry gnd_1 ref sel0 sel1 sel2 vdd
xi0 b0 b1 b2 sel0 sel1 sel2 carry gnd_1 net20 net21 net22 gnd_1 vdd adder_3bit
xi1 net20 net21 net22 ref gnd_1 sel0 sel1 sel2 gnd_1 vdd register_3bit
.ends digital_phase_accumulator

********************************************************************************
* Library          : project1
* Cell             : d_flipflop_with_bar
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt d_flipflop_with_bar clk d q q_ rst gnd_1 vdd
xm36 net66 rst_ net70 net67 n105 w=1u l=30n nf=1 m=1
xm35 rst rst net70 net67 n105 w=1u l=30n nf=1 m=1
xm31 rst_ rst gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm30 rst rst q_ net63 n105 w=1u l=30n nf=1 m=1
xm29 net69 rst_ q_ net63 n105 w=1u l=30n nf=1 m=1
xm23 net56 net66 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm22 net56 clk net69 gnd_1 n105 w=1u l=30n nf=1 m=1
xm21 net54 q gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm20 net54 clk_ net69 gnd_1 n105 w=1u l=30n nf=1 m=1
xm17 clk_ clk gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm12 net47 clk net50 gnd_1 n105 w=1u l=30n nf=1 m=1
xm11 net47 net70 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm5 net45 clk_ net50 gnd_1 n105 w=1u l=30n nf=1 m=1
xm15 net66 net50 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm1 net45 d gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm19 q q_ gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm32 rst_ rst vdd vdd p105 w=2u l=30n nf=1 m=1
xm28 net56 net66 vdd vdd p105 w=2u l=30n nf=1 m=1
xm27 net69 clk_ net56 vdd p105 w=2u l=30n nf=1 m=1
xm26 net54 q vdd vdd p105 w=2u l=30n nf=1 m=1
xm25 net69 clk net54 vdd p105 w=2u l=30n nf=1 m=1
xm24 q q_ vdd vdd p105 w=2u l=30n nf=1 m=1
xm18 clk_ clk vdd vdd p105 w=2u l=30n nf=1 m=1
xm10 net50 clk net45 vdd p105 w=2u l=30n nf=1 m=1
xm13 net47 net70 vdd vdd p105 w=2u l=30n nf=1 m=1
xm6 net45 d vdd vdd p105 w=2u l=30n nf=1 m=1
xm16 net66 net50 vdd vdd p105 w=2u l=30n nf=1 m=1
xm14 net50 clk_ net47 vdd p105 w=2u l=30n nf=1 m=1
.ends d_flipflop_with_bar

********************************************************************************
* Library          : project1
* Cell             : nand
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt nand a b gnd_1 out vdd
xm1 net5 b gnd_1 gnd_1 n105 w=2u l=30n nf=1 m=1
xm0 out a net5 gnd_1 n105 w=2u l=30n nf=1 m=1
xm3 out b vdd vdd p105 w=2u l=30n nf=1 m=1
xm2 out a vdd vdd p105 w=2u l=30n nf=1 m=1
.ends nand

********************************************************************************
* Library          : project1
* Cell             : f_divider_4_5_50percentcycles
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt f_divider_4_5_50percentcycles control fin fout gnd_1 rst vdd
xi8 net61 net50 net56 rst gnd_1 vdd d_flipflop
xi2 fin net25 net50 rst gnd_1 vdd d_flipflop
xi0 fin net20 net12 rst gnd_1 vdd d_flipflop
xi1 fin net12 net74 net28 rst gnd_1 vdd d_flipflop_with_bar
xi9 net50 net56 gnd_1 net60 vdd nand
xi4 net28 control gnd_1 net25 vdd nand
xi3 net74 net81 gnd_1 net20 vdd nand
xi14 gnd_1 control control_ vdd inverter_2u_1u
xi11 gnd_1 fin net61 vdd inverter_2u_1u
xi10 gnd_1 net60 net81 vdd inverter_2u_1u
xm15 net81 control fout gnd_1 n105 w=1u l=30n nf=1 m=1
xm12 net74 control_ fout gnd_1 n105 w=1u l=30n nf=1 m=1
xm16 fout control_ net81 vdd p105 w=2u l=30n nf=1 m=1
xm13 fout control net74 vdd p105 w=2u l=30n nf=1 m=1
.ends f_divider_4_5_50percentcycles

********************************************************************************
* Library          : project1
* Cell             : Phase_Detector_and_Pump
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt phase_detector_and_pump gnd_1 osc_by_n out ref vdd
xi1 osc_by_n gnd_1 net14 net44 gnd_1 vdd d_flipflop
xi0 ref vdd net13 net44 gnd_1 vdd d_flipflop
xi2 net13 net14 gnd_1 net40 vdd nand
xm4 net22 net22 gnd_1 gnd_1 n105 w=2u l=30n nf=1 m=1
xm3 out net14 net22 gnd_1 n105 w=2u l=30n nf=1 m=1
xm6 net32 net32 vdd vdd p105 w=4u l=30n nf=2 m=1
xm5 out net13 net32 vdd p105 w=4u l=30n nf=2 m=1
xi7 gnd_1 net40 net44 vdd inverter_2u_1u
.ends phase_detector_and_pump

********************************************************************************
* Library          : project1
* Cell             : tunable_delay_current_starved
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt tunable_delay_current_starved gnd_1 in out vctrl vdd
xm42 net141 vctrl net148 gnd_1 n105 w=1u l=0.03u nf=1 m=1
xm16 out net73 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm15 net73 in net52 gnd_1 n105 w=1u l=30n nf=1 m=1
xm14 net52 net141 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm13 net75 net141 gnd_1 gnd_1 n105 w=1u l=30n nf=1 m=1
xm43 net141 vctrl vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm20 out net73 vdd vdd p105 w=2u l=30n nf=1 m=1
xm19 net73 in net66 vdd p105 w=2u l=30n nf=1 m=1
xm18 net66 net75 vdd vdd p105 w=2u l=30n nf=1 m=1
xm17 net75 net75 vdd vdd p105 w=2u l=30n nf=1 m=1
r47 net148 gnd_1 r=0.4k
r46 vdd net148 r=0.6k
.ends tunable_delay_current_starved

********************************************************************************
* Library          : project1
* Cell             : Fractional_N_PLL
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt fractional_n_pll gnd_1 in m0 m1 m2 out ref vdd
xi0 gnd_1 in0 in1 in2 in3 in4 in5 in6 in7 out sel0 sel1 sel2 vdd mux_3_selects
xi1 m0 m1 m2 control_4_or_5 gnd_1 ref sel0 sel1 sel2 vdd
+ digital_phase_accumulator
xi13 control_4_or_5 in in0 gnd_1 gnd_1 vdd f_divider_4_5_50percentcycles
xi11 in in0 delayed_in gnd_1 gnd_1 vdd d_flipflop
xi12 gnd_1 delayed_in dll_loop in7 vdd phase_detector_and_pump
xi8 gnd_1 in6 in7 dll_loop vdd tunable_delay_current_starved
xi7 gnd_1 in5 in6 dll_loop vdd tunable_delay_current_starved
xi6 gnd_1 in4 in5 dll_loop vdd tunable_delay_current_starved
xi5 gnd_1 in3 in4 dll_loop vdd tunable_delay_current_starved
xi4 gnd_1 in2 in3 dll_loop vdd tunable_delay_current_starved
xi3 gnd_1 in1 in2 dll_loop vdd tunable_delay_current_starved
xi2 gnd_1 in0 in1 dll_loop vdd tunable_delay_current_starved
.ends fractional_n_pll

********************************************************************************
* Library          : project1
* Cell             : Fractional_N_PLL_test
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xi0 gnd_1 input m0 m1 m2 output reference vdd fractional_n_pll
v5 gnd_1 gnd! dc=0
v4 vdd gnd_1 dc=1
v3 m0 gnd_1 dc='m0'
v2 m2 gnd_1 dc='m2'
v1 m1 gnd_1 dc='m1'
v7 reference gnd_1 dc=0 pulse ( 0 1 0 2p 2p 204p 416p )
v6 input gnd_1 dc=0 pulse ( 0 1 0 2p 2p 246p 500p )
c8 output gnd_1 c=1f








.tran '10p' '20n' name=tran

.option primesim_remove_probe_prefix = 0
.probe v(*) i(*) level=1
.probe tran v(xi0.control_4_or_5) v(xi0.dll_loop) v(input) v(output)
+ v(reference)

.temp 25



.option primesim_output=wdf


.option parhier = LOCAL






.end


```

## Author

* Rohin Bhandari, MS in EE, Columbia University in the City of New York, NYC, USA

## Acknowledgements

  * [Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd.](https://www.iith.ac.in/events/2022/02/15/Cloud-Based-Analog-IC-Design-Hackathon/)
  * [Cloud Based Analog IC Design Hackathon](https://www.iith.ac.in/events/2022/02/15/Cloud-Based-Analog-IC-Design-Hackathon/')
  * [Synopsys India](https://www.synopsys.com/)
  * [Sameer Durgoji, NIT Karnataka](https://www.iith.ac.in/events/2022/02/15/Cloud-Based-Analog-IC-Design-Hackathon/)
  * [Chinmay panda, IIT Hyderabad](https://www.iith.ac.in/events/2022/02/15/Cloud-Based-Analog-IC-Design-Hackathon/)
  
## References

[1]	Wanghua Wu, Chih-Wei Yao, Kunal Godbole , Ronghua Ni , Pei-Yuan Chiang , Yongping Han , Yongrong Zuo , Ashutosh Verma , Ivan Siu-chuang Lu , Sang Won Son , Thomas Byunghak Cho, “A 5.5-7.3 GHz Analog Fractional-N Sampling PLL in 28-nm CMOS with 75 fsrms Jitter and -249.7 dB FoM”, 2018 IEEE Radio Frequency Integrated Circuits Symposium

[2]	Curtis Barrett, Fractional/Integer-N  PLL Basics, Technical Brief SWRA029

[3]	Basics of Dual Fractional-N Synthesizers/PLLs, Whitepaper by Skyworks 



