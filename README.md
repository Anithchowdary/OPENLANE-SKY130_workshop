# OPENLANE-SKY130_workshop
## workshop on openlane/sky130
## ![Screenshot (65)](https://user-images.githubusercontent.com/64426746/105518682-94a19c80-5cfe-11eb-9325-9c638ffb3f53.png)


## Table of contents
* [WORKSHOP DAY 1](#day1)
* [WORKSHOP DAY 2](#day2)
* [WORKSHOP DAY 3](#day3)
* [WORKSHOP DAY 4](#day4)
* [WORKSHOP DAY 5](#day5)


## WORKSHOP DAY 1
     Inception of open-source EDA, OpenLANE and Sky130 PDK
### Architecture of a chip
 chip is centered in the package.package and chip are connected by wires so that they can send the signals in and out.the packages has PADS:these pads are used to send signals in and out of the chip.the size of the package is given the size of the die.A core consists of foundary IP's and macros
   
  * MACROS(all the digital blocks are called macros)  eg:Socs,SP1
  * FOUNDARY IP's(they are manufactured in the foundary,it is a place where manufacturing is done)  eg: PLL,adc0,ac1,dac,SRAM
 
<img src="https://user-images.githubusercontent.com/64426746/105568308-4e7c2580-5d5e-11eb-8f70-2530339f5d31.png" alt="drawing" width="200"/>

## PHYSICAL DESIGN FLOW


### steps involved in Physical design flow(RTL TO GDS)
   we design the circuit by writing the hardware description language and we do RTL synthesis such that we get the circuit netlist.the we use PDK's which are process design kits which are used in between the foundary and the designers. it is the collectionof data which are used to model a fabrication process for the EDA tools used to design.they contain designs rules:DRC,LVSetc..,Device models,Digital standard cell libraries and input output libraries.
######  The flow from RTL to GSD is called pnr flow or physical design flow 
#### 1.Synthesis:
   we design the ciricuts by using HDL.the designed rtl is converted into circuits made up of different components from the standard cell library.the resultant circuit is in the HDL as gate level netlist.these standard cells have regular layouts which has fixed height rectangle and width is in the integer multiple of units.
 
#### 2.FLOOR&POWER PLANNING:
   The aim is to design the silicon area and create a robust power distribution network (PDN) to power each of the synthesized netlist's individual components. Additionally, to ensure a legalized GDS file, macro placement and blockages must be identified before placement occurs. We generate the ring that is linked to the pads in power planning, which carries power around the chip's edges. We also have power straps that use higher metal layers to carry power to the middle of the chip, minimizing IR fall and the issue of electro-migration.

#### 3.Placement:
   Spot the standard cells on the floorplane lines, lined up with destinations characterized in the innovation lef file. Position is done in two stages: Global and Detailed. In Global situation attempts to discover ideal situation for all cells yet they might be covering and not adjusted to columns, itemized arrangement takes the worldwide position and authorizes the entirety of the arrangements attempting to cling to what the worldwide situation needs.

#### 4.CTS(CLOCK TREE SYNTHESIS):
   Clock tree synthesis is utilized to make the clock circulation network that is utilized to convey the clock to every consecutive component. The principle objective is to make an organization with insignificant slant across the chip. H-trees are a typical organization geography that is utilized to accomplish this objective.

#### 5.ROUTING:
implements the interconnect framework between standard cells utilizing the leftover accessible metal layers after CTS and PDN age. The steering is performed on directing matrices to guarantee negligible DRC mistakes.
                              ![Screenshot (138)](https://user-images.githubusercontent.com/64426746/105970804-c4122980-60af-11eb-80cb-814871d500ed.png)
## INTRODUCTION OF OPENLANE

##### Process Design Kit (PDK) is the interface between the CAD  and the foundry. The PDK is an assortment of records used to demonstrate a creation cycle for the EDA apparatuses utilized in planning an IC. PDK's are generally shut source and thus are the restricting element to open-source Digital ASIC Design. Google and Skywater have broken this disgrace and distributed the world's first open-source PDK on June 30th, 2020. This advancement has been an impetus for open-source EDA apparatuses. This workshop centers around utilizing the open-source RTL2GDS EDA apparatus, Open LANE, related to the Skywater 130nm PDK to play out the full RTL2GDS stream as demonstrated underneath:
  ![image](https://user-images.githubusercontent.com/64426746/105976767-9381be00-60b6-11eb-9dc8-822a6929694e.png)
OpenLANE flow consists of several stages. By default, all flow steps are run in sequence. Each stage may comprise of different sub-stages. OpenLANE can likewise be run intelligently as demonstrated here.

#### 1.Synthesis
     Yosys - Performs RTL synthesis using GTech mapping
     abc - Performs technology mappin to standard cells described in the PDK. We can adjust synthesis techniques using different integrated abc scripts to get desired results
     OpenSTA - Performs static timing analysis on the resulting netlist to generate timing report
     Fault – Scan-chain insertion used for testing post fabrication. Supports ATPG and test patterns compaction
  
#### 2.Floorplan and PDN
      Init_fp - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
      Ioplacer - Places the macro input and output ports
      PDN - Generates the power distribution network
      Tapcell - Inserts welltap and decap cells in the floorplan
      Placement – Placement is done in two steps, one with global placement in which we place the designs across the chip, but they will not be legal placement with some           standard cells overlapping each other, to fix this we perform a detailed placement which legalizes the design and ensures they fit in the standard cell rows
      RePLace - Performs global placement
      Resizer - Performs optional optimizations on the design
      OpenPhySyn - Performs timing optimizations on the design
      OpenDP - Perfroms detailed placement to legalize the globally placed components
#### 4. CTS
      TritonCTS - Synthesizes the clock distribution network
#### 5.Routing
      FastRoute - Performs global routing to generate a guide file for the detailed router
      TritonRoute - Performs detailed routing from global routing guides
      SPEF-Extractor - Performs SPEF extraction that include parasitic information
#### 6.GDSII Generation
      Magic - Streams out the final GDSII layout file from the routed def
####  Checks
      Magic - Performs DRC Checks & Antenna Checks
      Netgen - Performs LVS Checks

  
   
