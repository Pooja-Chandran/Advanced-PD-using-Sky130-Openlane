# Advanced-PD-using-Sky130-Openlane

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/Advanced-Physical-Design-using-OpenLANE_Sky130_1.PNG)

**CONTENTS**

##### 1. Inception of open-source EDA, OpenLANE, Sky 130 PDK
<ul>
  <li> How to talk to computers
  <li> SoC Design and OpenLANE
  <li> Get familiar to open-source EDA tools
</ul>

##### 2. Good floorplan vs bad floorplan and introduction to library cells
<ul>
  <li> Chip Floorplanning considerations
  <li> Library Binding and Placement
  <li> Cell design and characterization flows
  <li> General timing characterization parameters
 </ul>

##### 3. Design library cell using Magic layout and ngspice characterization
<ul>
  <li> Labs for CMOS Inverter ngspice simulations
  <li> CMOS Fabrication Process
  <li> SKY130 Tech File Labs
</ul>
 
 ##### 4. Pre layout timing analysis and importance of good clock tree
<ul>
  <li> Timing modelling using delay tables
  <li> Timing analysis with ideal clocks using openSTA
  <li> Clock tree synthesis TritonCTS and signal integrity
  <li> Timing analysis with real clocks using openSTA
 </ul>
         
 ##### 5. Final steps for RTL2GDS using tritonRoute and OpenSTA
<ul>
  <li> Routing and design rule check (DRC)
  <li> Power Distribution Network and routing
  <li> TritonRoute Features
 </ul>
 


## Day1: Introduction to OpenLANE, Sky130 and EDA Tools

#### RTL2GDS OpenLANE ASIC Flow

OpenLANE is an automated RTL2GDSII flow. It is based on several open source components including OpenROAD, Yosys, Magic, Netgen, Fault, OpenPhySyn, CVC, SPEF-Extractor, Klayout and custom methodology scripts for design exploration and optimization.

 ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/intro2.png)
 
 **OpenLANE** is an automated RTL2GDSII flow including many open source softwares.
 
 **1.Synthesis**
  
        a. Yosys - Performs RTL synthesis
        b. abc - Performs technology mapping
        c. OpenSTA - Performs STA on the resulting netlist.
        d. Fault- Performs DFT,Scan chain insertion,ATPG,Test pattern generation.
        
 **2.Floorplan**
 
        a. init_fp - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
        b. ioplacer - Places the macro input and output ports
        c. pdn - Generates the power distribution network
        d. tapcell - Inserts welltap and decap cells in the floorplan
        
  **3.Placement**
  
         a. RePLace - Performs global placement
         b. Resizer - Performs optional optimizations on the design
         c. OpenPhySyn - Performs timing optimizations on the design
         d. OpenDP - Perfroms detailed placement to legalize the globally placed components
  
  **4.CTS**
  
         a.TritonCTS - Performs Clock tree synthesis
         
  **5.Routing**
  
         a.FastRoute - Performs global routing 
         b.TritonRoute - Performs detailed routing
         c.SPEF-Extractor - Performs SPEF extraction
         
  **6.Physical Verification**
         
         a.Magic and Netgen- Physical Verification
         
  **7.GDSII Generation**
        
        a.Magic - GDSII Layout is generated from routed def
 
### How to talk to computers?

A RISC-V core computer uses RISC-V Instruction Set Architecture (ISA). If a user wishes to run a certain application software on a computer, its corresponding C/C++/Java program must be converted into assembly language (instructions) by the compliler. These instructions go as inputs to the assembler which outputs binary language in the form of bits. An RTL code for the corresponding function is coded. And through the APR flow GDSII is generated. 

### Skywater PDK Files

The Skywater PDK files we are working with are described under pdks directory. There are three subdirectories needed for the workshop:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/12.PNG)

1. Skywater-pdk – Contains all the PDK related files.
2. Open_pdks – Contains scripts and files that converts foundry level pdksto be compatible with open-source EDA tools.
3. Sky130A – The open-source compatible PDK files.


### Invoking OpenLANE

 The first lab was to synthesise the RTL code of a picorv32a in the interactive mode using OpenLANE.
 The command to invoke OpenLANE in interactive mode is: 
              
              ./flow.tcl -interactive
  
  Include the package by using the below command
             
             package require openlane 0.9
             
  Inorder to prepare design setup files 
  
             prep -design picorv32a
       
                
 ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/1.JPG)
 
  During this command, the cell level lef and the technology lef are merged into one. So this merge file will have cell and metal layer informations.
  
 ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/11.PNG)
 
  The following command is used to do synthesis
  
              run_synthesis
          
  
  The yosys and ABC tools are used to convert RTL to gate level netlist (GLN). The ABC maps netlist to the cells the library.
              
  ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/2.PNG)
  
   ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/10.PNG)
  
  The below folders contains different reports for each stage.
  
  ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/4.PNG)
  
  From the reports we can find the flop ratio and the buffer ratio.
  
  Flop ratio = Number of flipflops/ Total number of the cells.
  
  Buffer ratio= Number of buffers/ Total number of the cells.
  
  ## Day2: Floorplan and Placement
  
1. The first step of physical design is to define the width and height of the die and core. Each silicon wafer contains several number of dies so that yield is maximum. A die contains a core region where the logic is placed.

While building a chip, two terms needs to be understood- utilization factor and aspect ratio.

Utilization factor = Area occupied by the netlist/total area of the core.

Aspect ratio= Height of core/ Width of the core.

2. Defining the location of preplaced cells

Some logic can be implemented once and instantiated many times on to a netlist. These logic are functionally implemented only once. These logic receives some input and generates corresponding output logic. This is a part of the top level netlist. And these cells are called preplaced cells, since these logics are implemented only once. The location for these cells need to be defined and this is done before the placement and routing. The placement of these cells will be fixed on a top level chip. Since they are done before the placement, they are called pre placed cells. These cells will not be touched by the APR tools.

3. Using Decoupling capacitors

If the circuit is physically far away from the supply voltage, the output logic may not be the expected value. The switching activity in the circuit may not be within the noise margin. Inorder to avoid this, we can use a decoupling capacitor.
These capacitors are huge capacitors fully filled with charges. The voltage across the capacitor is almost equivalent to the charge across the power supply. If a circuit is connected to a decoupling capacitor, the current is supplied by the capacitor. The capacitor decouples the circuit from the power supply. The preplaced cells will be provided with decoupling capacitors so that they get supply from these capacitor. This will ensure that no switching activity gets missed and there will be no crosstalk problems.

4.Powerplanning

A single power supply connected to large number of circuits will result in voltage droop or ground bounce, when the circuits switches. Power planning ensures there is no ground bounce and voltage droop. During power planning, power and ground straps are laid in such a way that they form a mesh structure and each logic can tap power or drop the power to respective rails. 

5. Pin Placement

The pin placement is done in according to the logic. So the RTL team defines the netlist connectivity, the PD team will do the pin placement. The clock ports are bigger in size since clock ports drives the chip. So as the port size increases, the resistance increases. Now the APR tool should not place any cells where the pins are placed. The area should be blocked so that APR tool will not place any cells there.

Once the synthesis is completed ,next step is the floorplan. For that the below command is used

        run_floorplan

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/floorplan_1.PNG)


### Viewing Floorplan in Magic

To view the floorplan on Magic,the following inputs are needed:

  1.Magic technology file (sky130A.tech)
  2..def file of floorplan
  3.Merged LEF file

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/1_floorplan.PNG)
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/floorplan_2.PNG)

During the floorplan stage, IO pins are placed equidistantly. On the Magic tool, keep the cursor on the any IO pin ans press 's' and type 'what' on tkon window. The what command queries what material is in the current selection.

Reference http://opencircuitdesign.com/magic/userguide.html.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/floorplan_4.PNG)

### Placement

After floorplanning, the placement of standard cells is done. The synthesized netlist OpenLANE does placement in two stages:

Global Placement - Optimized but not legal placement. Optimization works to reduce wirelength by reducing half parameter wirelength.

Detailed Placement - Legalizes placement of cells into standard cell rows while adhering to global placement.

To do placement in OpenLANE the following command is used.

      run_placement

 ### Viewing Placement in Magic
 
The placement can be viewed on Magic tool as:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/placement_1.PNG)
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/2_placement.PNG)

      
 During the run_placement, global placement is happening. The standard cells will be placed as shown in next figure.
 
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/std%20cells1.PNG)
 
### Standard Cell Design Flow

Cell design is done in 3 parts:

   a. Inputs - PDKs (Process design kits), DRC & LVS rules, SPICE models, library & user-defined specs.
   
   b. Design Steps - This involves Circuit Design, Layout Design, Characterization. The software GUNA is used for characterization. The characterization can be classified as   Timing, Power and Noise characterization.
   
   c. Outputs - Outputs of the Design are CDL (Circuit Description Language), GDSII, LEF, extracted Spice netlist (.cir), timing, noise, power.libs, function.


## Day3: Design Library Cells

First part is to create SPICE deck. Spice deck is the connectivity information about netlist.

SPICE Deck contains

    1. Component connectivity
    
    2. Component values (W/L),load capacitance, Input and supply voltage
    
    3. Identify nodes
    
    4. Name the nodes
    
    5. Simulation commands
    
    6. Describe model file
    
### Switching Threshold of CMOS

Switching threshold is the point where Vin=Vout.This depends on the W/L ratio of the PMOS and NMOS transistor.

### 16-mask CMOS process

  1. Select a substrate- Selecting the base layer in which other regions are made.

  2. Create active region for transistors-Create an insulator layer using SiO2 and Si3N2.Pockets are created using photoresist and lithography process.

  3. Formation of N-well and P-well formation-Ion Implantation is used for this purpose.

  4. Creating Gate terminal- For desired threshold,doping Concentration and oxide thickness needs to be set.

  5. Lightly Doped Drain (LDD) formation- LDD is done to avoid short channel effect and hot electron effect.

  6. Source and Drain formation- Formation of the source and drain.

  7. Contacts and local interconnect creation- SiO2 removed using HF etching. Titanium is deposited using sputtering.

  8. Higher Level metal layer formation- Upper metal laters are deposited.


### CMOS INVERTER ngspice SIMULATIONS

The tech file for the magic present in the pdk directory is copied to the vsdstdcelldesign directory.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/stdcell1.PNG)

To view the layout on the magic, the following command is used.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/stdcell2.PNG)
     
The layout of the inverter appears in the magic:       

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/magic1.PNG)

When the polysilicion crosses and n diffusion, its an NMOS.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/magic2.PNG)

As in above figure, keep the mouse on the highlighted region and press 's'. Type 'what" on tkon window, and we can see that the above statement holds true.

When the polysilicion crosses and p diffusion, its an PMOS.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/magic3.PNG)

As in above figure, keep the mouse on the highlighted region and press 's'. Type 'what' on tkon window, and we can see that the above statement holds true.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/magic4.PNG)

As in above figure, keep the mouse on the highlighted region and press 's'. Type 'what' on tkon window, and see that the output Y is selected.


### PEX Extraction with Magic

To extract the parasitic spice file, an extraction file needs to be created and that can be done by typing following command on tkon:
        
        extract all
        
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/magic7.PNG)

If we check the corresponding directory we can see that the extraction file is created (sky130inv.ext)


![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/magic8.PNG)

After generating the extracted file we need to output the .ext file to a spice file. The following commands can be used:

    ext2spice cthresh 0 rthresh 0
    ext2spice
    
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/magic9.PNG)

The SPICE deck looks like as follows:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/stdcell5.PNG)

Here the SPICE deck is editted according to the layout to run transient analysis as follows:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/ngspice1.PNG)

The following command is used to invoke ngspice tool:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/ngspice1_1.PNG)

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/ngspice2.PNG)

To plot transient analysis:

          plot y vs time a
          
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/ngspice4.PNG)

The following timing parameters are calculated.

Rise transition delay = Time taken for the output signal to reach from 20%-80% of maximum value.

Fall transition delay = Time taken for the output signal to reach from 80%-20% of maximum value.

Cell rise delay = Time difference between 50% of rising output and 50% of falling output.

Cell fall delay = Time difference between 50% of falling output and 50% of rising output.

## Day 4: Timing Analysis and Clock Tree Synthesis

### Extracting LEF file

LEF file protects the IP. Extract LEF file out of .mag file. The extracted LEF file will be plugged into picorv32a flow.

Certain guidelines need to be followed while making standard cell set:

1.	The input and output port must lie on the intersection of horizontal and vertical tracks.
2.	Width of the standard cells must be odd multiple of horizontal tracks pitch. Height must be odd multiple of vertical tracks.

Tracks are used during routing stage. Routes are traces of metals. Only over the routes PNR tool can do routing.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4.PNG)

For example,

li1 layer (X) the horizontal track is at an offset of  0.23 having a pitch of 0.46

li1 layer (Y) the vertical track is at an offset of  0.17 having a pitch of 0.34

These grids are the routes taken for PNR flow. The grid sizes can be changed according to the track definition

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_1.PNG)

So if we check the below image, the input and output port and on the intersection of horizontal and vertical tracks. So having the ports on horizontal and vertical tracks ensures that routes can reach the ports from X and Y direction.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_2.PNG)


Also if we analyse the below figure, width of the standard cells is odd multiple of horizontal track pitch.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_3.PNG)

When we extract the lef files ,the ports will be considered as pins.

To extract the lef file, the following command is used on tckon window.
      
      lef write
      
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_4.PNG)

This will create a new lef file in the corresponding directory with an extension of .lef

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_5.PNG)

The lef file shows that the ports have been assigned as pins and all the changes that were made in magic has been reflected in here. 

Generated LEF file:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_6.PNG)

To plug this LEF file into picorv32a flow,first we need to copy the lef file and the library files into the /design/src folder, so that all design files are in one folder itself. 

The config.tcl file sets the location where the lef file is present which is needed for the spice extraction.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_11.PNG)

The below command is included to add lef into the flow:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_9.PNG)

Run the entire flow and we can see that sky130_vsdinv, is added into the netlist.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_10.PNG)

We can see this cell in the magic gui once we finish the placement. So if we zoomin we can see sky130_vsdinv.
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_13.PNG)

Place the cursor on sky130_vsdinv,press 's'. Type *expand* on tkon window and we can see the connection of that particular cell to the adjacent cells.
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/day4_14.PNG)

### Timing Analysis using OpenSTA

Initially the slack was neagtive and a huge value.
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/sta1.PNG)

OpenSTA tool is used to do STA analysis. Negative slack is not ideal for any design,so the slack values have to be optimized and bought up to a positive value.
The STA configuration file and run it to find the timing parameters.

The config file looks like the following:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/sta%20config.PNG)

Some changes need to be done in the synthesis.tcl file by setting some of the environmental values.
For example,
SYNTH_STRATEGY value was 'AREA 0'. Area was the preference. This can be changed to 'DELAY 1' such that delay becomes the preference.
Also, SYNTH_SIZING can be set to 1.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/err2.PNG)

Once these changes are made, the slack reduces to an acceptable number.

Now the flow needs to be done again. We will invoke the docker and overwrite on the existing file.

The following commands are used after invoking the docker:

    ./flow.tcl -interactive
    package require openlane 0.9
    prep -design picorv32a -tag 03-07_15-55 -overwrite
    
The switch -overwrite overwrites the existing file 03-07_12-55. Once synthesis is done and timing is under control, we will do the floorplan and placement. The next step will be CTS.

### Clock Tree Synthesis
The process of connecting clock pins of all sequential cells to the clock net such that clock skew is minimized is called CTS. Clock nets are set as ideal during synthesis and placement. Ideal network means there is no interconnect delys or wire delays are not taken into account. We do so because if we are not setting clock net as ideal, the interconnect delays degrade the clock signal and lead to timing violations, and worst some cells may not get the clock signal.

During CTS, clock buffers and inverters are added to achieve minimal clock skew. These clock buffers are different from normal buffers. CTS buffers have equal rise and fall times. 

The following command is used to do CTS.

       run_cts
     
Since clock tree is built, now the clocks can be propogated. Post CTS timing analsysis can be done by writing a .db file from lef and def file. Read the .db file along with liberty file, cts netlist,propogate the clocks and get the reports.

    read_lef /openLANE_flow/designs/picorv32a/runs/03-07_12-55/tmp/merged.lef
    read_def /openLANE_flow/designs/picorv32a/runs/03-07_12-55/results/cts/picorv32a.cts.def
    write_db pico.cts.db
    read pico.cts.db
    read_verilog /openLANE_flow/designs/picorv32a/runs/03-07_12-55/results/synthesis/picorv32a.synthesis_cts.v
    read_liberty $::env(LIB_SYNTH_COMPLETE) 
    link_design picorv32a
    read_sdc ...../src/my_base.sdc
    set_propagated_clock [all_clocks]
    report_checks -path_delay min_max -format full_clock_expanded -digits 4
   
   ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/cts5.PNG)
    
After CTS slack is increased. To reduce slack violation we have to edit the variables for clock buffers and replace the buffers. The CTS buffers used in oplane are as shown in figure. 

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/cts2.PNG)

We can replace some of the buffers with higher driving strength buffers there by improving the slack. Clock tree optimization is achieved by buffer sizing, gate sizing, buffer relocation, level adjustment and High Fanout Nets synthesis (HFNS).

## DAY 5: RTL2GDSII

The following commands perform the synthesis to routing:
  
    1.run_synthesis 
    
    2.init_floorplan 
  
    3.place_io
  
    4.global_placement_or
  
    5.detailed_placement 
  
    6.tap_decap_or detailed_placement
  
    7.gen_pdn
  
    8.run_routing
   
 ### Power Distribution Network
 
The primary goal in power network design is to provide enough power lines across a chip to reduce voltage drops from the power pads to the center of the chip. Voltage drop is caused by the power network's metal lines coupled with transistor switching currents on the chip cause power supply noises that can affect circuit timing and performance, thus providing a constant challenge for designers of high-performance chips.

The PDN feature within OpenLANE will create:

  1.Power ring global to the entire core.
  
  2.Power halo local to any preplaced cells.

  3.Power straps to bring power into the center of the chip.

  4.Power rails for the standard cells.

To generate the power distribution network use the following command:

      gen_pdn

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/afterpdn.PNG)

The pitch of the metal 1 power rails defines the height of the standard cells

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/afterpdn1.PNG)

 The horizontal and vertical blue lines show the power and ground network built.
 
 ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/pdn2.PNG)
      
 ### Routing
 TritonRoute is used for routing of the designs.It is done in two stages.

1.Global Routing - The input to global router is a floorplan that includes the locations of all fixed and flexible blocks. It generates a loose layout for each net .Assign a list of routing region to each net and without specifying the actual layout of wires .

2.Detailed Routing- Find the actual geometry of each net with in the assigned routing region.

To run routing in OpenLANE execute the command
  
    run_routing
    
ROUTING_STRATEGY (0 to 3) uses Triton-13 engine (faster runtime)

ROUTING_STRATEGY (14) uses Triton-14 engine (better DRCs, but more runtime) 

 We can invoke the magic window  and see the result.
 
 ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/route4.PNG)
 
 So here we can see that all standard cells are placed in the standarc cell rows, they are legalized and routed.
 
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/route1.PNG)

If we zoom in we can find the sky130_vsdinv, press 's' and type 'what' pn tkon window.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/route2.PNG)

Type 'expand on the tkon window, we can see the inverter is conneceted to the adjacanet cells. Pink lines represent the connection.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/images/route3.PNG)

### SPEF Extraction

After routing has been completed interconnect parasitics can be extracted to perform sign-off post-route STA analysis. The parasitics are extracted into a SPEF file. The SPEF extractor is not included within OpenLANE as of now.

## Acknowledgements:

Kunal Ghosh, Co-founder (VSD Corp. Pvt. Ltd)

Nickson P Jose, Teaching Assistant (VSD Corp. Pvt. Ltd)
