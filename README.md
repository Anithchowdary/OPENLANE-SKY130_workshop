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
                           
<img src="https://user-images.githubusercontent.com/64426746/105970804-c4122980-60af-11eb-80cb-814871d500ed.png" alt="drawing" length="500/" width="500"/>
 
## INTRODUCTION OF OPENLANE

##### Process Design Kit (PDK) is the interface between the CAD  and the foundry. The PDK is an assortment of records used to demonstrate a creation cycle for the EDA apparatuses utilized in planning an IC. PDK's are generally shut source and thus are the restricting element to open-source Digital ASIC Design. Google and Skywater have broken this disgrace and distributed the world's first open-source PDK on June 30th, 2020. This advancement has been an impetus for open-source EDA apparatuses. This workshop centers around utilizing the open-source RTL2GDS EDA apparatus, Open LANE, related to the Skywater 130nm PDK to play out the full RTL2GDS stream as demonstrated underneath

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
      TritonRoute - Performs detailed routing from global routing guides'
      SPEF-Extractor - Performs SPEF extraction that include parasitic information
      
#### 6.GDSII Generation
      Magic - Streams out the final GDSII layout file from the routed def
      
####  Checks
      Magic - Performs DRC Checks & Antenna Checks
      Netgen - Performs LVS Checks
      
      
## WORKSHOP DAY 1 Inception of Open Source EDA

### Skywater PDK Files
The Skywater PDK files ae in this location. There directories needed for the workshop:
![Screenshot (53)](https://user-images.githubusercontent.com/64426746/106017539-8da3d100-60e6-11eb-97bb-d3f8e49acdbc.png)
it contains all these files in pdks
Skywater-pdk – it Contains all the foundry provided PDK related files
Open_pdks –it Contains scripts which are used to bridge the gap between closed-source and open-source PDK to EDA tool compatibility
Sky130A – These are open-source compatible PDK files

### Invoking OpenLane
### ![Screenshot (57)](https://user-images.githubusercontent.com/64426746/106018177-423df280-60e7-11eb-88ed-a69bd9ec2b1d.png)
     1.The script which runs the openlane flow is ./flow.tcl
     2.OpenLANE can be run interactively or in autonomous mode
     3.To run openlane interactively use the command -interactive option with the ./flow.tcl script
     
### Package Importing
To import these into the OpenLANE tool we need to run this command
![Screenshot (60)](https://user-images.githubusercontent.com/64426746/106018813-f2abf680-60e7-11eb-81b7-23486bfc4d2d.png)

### Prepare Design
Prep is used to make file structure for our design. To set this up do
![Screenshot (60)](https://user-images.githubusercontent.com/64426746/106019417-a4e3be00-60e8-11eb-8c5b-cd6210573430.png)

### Synthesis
![Screenshot (62)](https://user-images.githubusercontent.com/64426746/106019688-ef653a80-60e8-11eb-9cd4-ab4fb7b03ed3.png)

## WORKSHOP DAY 2 Chip Floorplanning and Standard Cells
In Floorplanning we typically set
1.Die Area
2.Core Area
3.Core Utilization
4.Aspect Ratio
5.Place Macros
6.Power distribution network 
7.Place input and output pins

### Aspect Ratio and Utilization Factor
utilization factor is the ratio of the size of the occupied core to that of the die.typicallically we go for utilization factor of 0.5-0.7.by placing them in this range allows us to do  optimization of placement and to do realizable routing.Aspect ratio is the shape of your chip by the height of the core area divided by the width of the core area. if the aspect ratio is 1 means the chip as a square. for all other the chip is in rectangle shape.
![Screenshot (62)](https://user-images.githubusercontent.com/64426746/106019688-ef653a80-60e8-11eb-9cd4-ab4fb7b03ed3.png)

### Preplaced Cells
The cells which are used for major purposes by placing in them in a group we form preplaced cell.these preplaced cells are placed in the circuit once they are they cannot be removed.

### Decoupling Capacitors
we use these Decoupling Capacitors near the preplaced cells because the cells require the voltage which helps to run.when the voltage to the circuit from the source before reaching the cells ttheir is a voltage drop to address these problem we use Decoupling Capacitors which generates the voltage which is suffient for the cell and again after the discharge the Decoupling Capacitors get charged in this way they act as a power supply.

### Power Planning
During the Floorplanning phase is essential to lower noise in digital circuits attributed to voltage drop.Coupling capacitance is formed between interconnect wires and the which are useful to either for charged or discharged which represent either the logic 1 or the logic 0.charge associated with coupling capacitors may be dumped to ground. If there are not enough ground taps charge will accumulate at the tap and the ground line will act like a large resistor, raising the ground voltage and lowering our noise margin. To resolve this problem a robust PDN with consists many  power strap taps are needed to lower the resistance  with the PDN.

### Pin Placement
Pin placement is an important part of floorplanning to minimize buffering and to improve the power consumption and to have effective timing delays. TIn many cases, optimal pin placement will be accompanied with less buffering and uses less power consumption. After doing pin placement is formed we need to place logical cell blockages along the I/O ring to discriminate between the core area and I/O area.

### Floorplanning with OpenLANE
To run floorplan in OpenLANE
floorplan defaults in tcl

### ![Screenshot (68)](https://user-images.githubusercontent.com/64426746/106023931-48cf6880-60ed-11eb-87f5-5b7a9c7fb8d9.png)
      we need to run
      run_floorplan
      
after floorplan execution
![Screenshot (69)](https://user-images.githubusercontent.com/64426746/106024082-6b618180-60ed-11eb-8af6-86067e6afff1.png)
As with all other stages, the floorplanning will be run according to configuration settings in the design specific config.tcl file. The output the the floorplanning phase is a DEF file which describes core area and placement of standard cell SITES:\
![Screenshot (72)](https://user-images.githubusercontent.com/64426746/106024552-e460d900-60ed-11eb-83f8-afe56d1583ff.png)

### Viewing Floorplan in Magic
     To view the floorplan in Magic we need to provide three files as input
     Magic technology file (sky130A.tech)
     Def file of floorplan
     Merged LEF file
![Screenshot (73)](https://user-images.githubusercontent.com/64426746/106024986-520d0500-60ee-11eb-8bdc-e4fc022b333a.png)

### Placement
The next step after floorplanning is placement. The synthesized netlist is mapped to standard cells and floorplanning phase has to determined the standard cells rows. OpenLANE does placement in two stages

Global Placement - It is to reduce the wirelength
Detailed Placement - Legalizes placement of cells into standard cell rows while adhering to global placement
To do placement in OpenLANE
run_placement

### Viewing Placement in Magic
 placement in Magic the command is used for viewing placement:
 ![Screenshot (75)](https://user-images.githubusercontent.com/64426746/106025977-4cfc8580-60ef-11eb-867e-bd63c1874792.png)
 
### LAYOUT
 ![Screenshot (76)](https://user-images.githubusercontent.com/64426746/106025736-0ad34400-60ef-11eb-8da1-d527ded04acc.png)
 
### Standard Cell Design Flow
Cell design is done in 3 parts:

1.Inputs - PDKs (Process design kits), DRC & LVS rules, SPICE models, library & user-defined specs.
2.Design Steps - Design steps of cell design involves Circuit Design, Layout Design, Characterization. The software GUNA used for characterization. The characterization can     be classified as Timing characterization, Power characterization and Noise characterization.
3.Outputs - Outputs of the Design are CDL (Circuit Description Language), GDSII, LEF, extracted Spice netlist (.cir), timing, noise, power.libs, function.

### Standard Cell Characterization
Characterization flow consists of these steps:

1.Link Model File of CMOS containing property definitions
2.Specify process corner(s) for the cell to be characterized
3.Specify cell delay and slew thresholds percentages
4.Specify timing and power tables
5.Read the parasitic extracted netlist
6.Apply input or stimulus
7.Provide necessary simulation commands

## WORKSHOP DAY 3 Design Library Cell
#### Magic Layout View of Inverter Standard Cell
Refer to: https://github.com/nickson-jose/vsdstdcelldesign for cell files.
![Screenshot (77)](https://user-images.githubusercontent.com/64426746/106026901-5df9c680-60f0-11eb-9c2e-de0d5deec3be.png)

#### invoke Magic
![Screenshot (79)](https://user-images.githubusercontent.com/64426746/106027222-b29d4180-60f0-11eb-8ed5-c5a51e80fc56.png)

LAYOUT MAGIC
![Screenshot (81)](https://user-images.githubusercontent.com/64426746/106027084-8f729200-60f0-11eb-87e7-57dbd470fe74.png)

### Magic Key Features
1.Color Palette - Defines layers and associated colors Continuous DRC
2.Device Inference - Automatic recognition of NMOS and PMOS devices

### PEX Extraction with Magic
![Screenshot (83)](https://user-images.githubusercontent.com/64426746/106027772-3b1be200-60f1-11eb-9e81-5450736decca.png)

### ngspice stimulation
![Screenshot (89)](https://user-images.githubusercontent.com/64426746/106027948-5f77be80-60f1-11eb-8783-2a2246938d93.png)

#### output
<img src="https://user-images.githubusercontent.com/64426746/106028256-baa9b100-60f1-11eb-99d2-6b3bcd5f3174.png" alt="drawing" width="500"/>

#### plotting the output vs time while sweeping the input:
<img src="https://user-images.githubusercontent.com/64426746/106028428-ee84d680-60f1-11eb-98d3-10ec15b399e2.png" alt="drawing" width="800"/>


## WORKSHOP DAY 4 Layout Timing Analysis and CTS
Place and routing is performed using the GDS files generated by Magic . The abstract information will present in  metal and pin information. The PnR tool will use the abstract view as LEF information to perform interconnect routing  generated from the PnR flow.
<img src="https://user-images.githubusercontent.com/64426746/106099398-9b963800-6160-11eb-928e-60e982c331d0.png" alt="drawing" width="200"/>


Tracks are used in the routing.routes can go over the tracks or metal traces can go over the tracks. What the file is saying is that for the li1 layer the x or horizontal track is at an offset of 0.23 and a pitch of 0.46. The offset is the distance from the origin to the routing track in either the x or y direction. It is half the pitch so that means the tracks are centered around the origin.
 
 ### Standard Cell Pin Placement
   On-track standard cell pin placement is important for DRC free PnR flow. Pins should align with the li1 and met1 most popular routing directions. throughout voltaic cell creation this idea should be accounted for. to confirm a cell is aligned with routing grids in Magic we are able to show a grid on high of the gds file.
 
 display the grid in magic
 ![Screenshot (142)](https://user-images.githubusercontent.com/64426746/106103209-92a86500-6166-11eb-9e45-690e7d910c00.png)
 
 grid view  we can ensure that our pin placement is optimized for PnR flow
 ![Screenshot (94)](https://user-images.githubusercontent.com/64426746/106103332-caafa800-6166-11eb-8a77-03dfe1f6d64f.png)
### LEF Generation in Magic
Magic allows users to generate the cell LEF which takes information directly from the Magic terminal.
![Screenshot (98)](https://user-images.githubusercontent.com/64426746/106103645-46115980-6167-11eb-947a-8967c68f140c.png)

### Generated cell LEF file:
![Screenshot (100)](https://user-images.githubusercontent.com/64426746/106103832-8ec91280-6167-11eb-8fed-4509355e0bba.png)
In order to include the new cells in OpenLANE we need to do some initial configuration:

1.Fully characterize new cell with GUNA for specified corners
2.Include cell level liberty file in top level liberty file
3.Reconfigure synthesis switches in the config.tcl file
4.Overwrite previous run to include new configuration switches
![Screenshot (101)](https://user-images.githubusercontent.com/64426746/106104065-eb2c3200-6167-11eb-8e9b-f3868af1ffea.png)
5.Add additional statements to include extra cell LEFs:
![Screenshot (102)](https://user-images.githubusercontent.com/64426746/106104173-144cc280-6168-11eb-9437-748bbb7e34ec.png)
6.Check synthesis logs to ensure cell has been integrated correctly

### Clock Tree Synthesis
by completing the floorplan and standard cell placement in OpenLANE.we insert the clock tree for sequential elements in our design.  main concerns regarding the generation of the clock tree are the two which are mentioned below :
1.Clock skew - Difference in arrival times of the clock for sequential elements across the design
2.Delta delay - Skew introduced through capacitive coupling of the clock tree nets
run clock tree synthesis in OpenLANE:
![Screenshot (144)](https://user-images.githubusercontent.com/64426746/106356168-89182c00-6323-11eb-9959-d37877eaad0b.png)

### Post-CTS STA Analysis
#### invoke OpenROAD from OpenLANE:

the timinig analysis is done by creating  .db database file. the database file is created from the post-cts LEF and DEF files. To generate the .db files within OpenROAD:

![image](https://user-images.githubusercontent.com/64426746/106356718-afd86180-6327-11eb-801d-e6a059f93be9.png)

## Day 5 Final Steps in RTL to GDSII
After generating our clock tree network and verifying post routing STA checks we are ready to generate the power distribution network in OpenLANE
![Screenshot (148)](https://user-images.githubusercontent.com/64426746/106356795-41e06a00-6328-11eb-9a1d-c76dde679148.png)

### The PDN feature within OpenLANE will create:
     1.Power ring global to the entire core
     2.Power halo local to any preplaced cells
     3.Power straps to bring power into the center of the chip
     4.Power rails for the standard cells
![Screenshot (151)](https://user-images.githubusercontent.com/64426746/106356848-971c7b80-6328-11eb-8efc-bd956a11d614.png)

### Routing
##### Global and Detailed Routing

OpenLANE uses TritonRoute as the routing tool for implementation  of designs.Routing consists of two stages thay are mentioned below

1.Global Routing - Routing guide is generated for interconnects on our netlist defining what layers on the chip each of the nets will be reputed
2.Detailed Routing - Metal traces are iteratively laid across the routing guides to physically implement the routing guides
![Screenshot (153)](https://user-images.githubusercontent.com/64426746/106356943-1b6efe80-6329-11eb-8239-c5fea2f038e5.png)
##### In case of DRC errors persist after routing the user has two options:
       1.Re-run routing with higher QoR settings
       2.Manually fix DRC errors specific in tritonRoute.drc file
### SPEF Extraction
After routing competion interconnect parasitics can be extracted to perform sign-off post-route STA analysis. The parasitics are extracted into a SPEF file.

### Acknowledgements
@ Nickson Jose - VSD VLSI Engineer
@ Kunal Ghosh - Co-founder (VSD Corp. Pvt. Ltd)
@ Praharsha
     



