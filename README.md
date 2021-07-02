# Advanced-PD-using-Sky130-Openlane

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/Advanced-Physical-Design-using-OpenLANE_Sky130_1.png)

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
 
### Skywater PDK Files

The Skywater PDK files we are working with are described under pdks directory. There are three subdirectories needed for the workshop:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/12.PNG)

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
       
                
 ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/1.PNG)
 
  During this command, the cell level lef and the technology lef are merged into one. So this merge file will have cell and metal layer informations.
  
 ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/11.PNG)
 
  The following command is used to do synthesis
  
              run_synthesis
              
  ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/2.PNG)
  
  The below folders contains different reports for each stage.
  
  ![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/4.PNG)
  
  From the reports we can find the flop ratio and the buffer ratio.
  
  Flop ratio = Number of flipflops/ Total number of the cells.
  
  Buffer ratio= Number of buffers/ Total number of the cells.
  
  ## Day2: Floorplan and Placement
  
1. The first step of physical design is to define the width and height of the die and core. Each silicon wafer contains several number of dies so that yield is maximum. A die contains a core region where the logic is placed.

While building a chip, two terms needs to be understood- utilization factor and aspect ratio.

Utilization factor = area occupied by the netlist/total area of the core.

Aspect ratio= height of core/ width of the core.

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

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/floorplan_1.PNG)


### Viewing Floorplan in Magic

To view the floorplan on Magic,the following inputs are needed:

  1.Magic technology file (sky130A.tech)
  2..def file of floorplan
  3.Merged LEF file

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/1_floorplan.PNG)
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/floorplan_2.PNG)

During the floorplan stage, IO pins are placed equidistantly. On the Magic tool, keep the cursor on the any IO pin ans press 's' and type 'what' on tkon window. The what command queries what material is in the current selection.

Reference http://opencircuitdesign.com/magic/userguide.html.

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/floorplan_4.PNG)

### Placement

After floorplanning, the placement of standard cells is done. The synthesized netlist OpenLANE does placement in two stages:

Global Placement - Optimized but not legal placement. Optimization works to reduce wirelength by reducing half parameter wirelength.

Detailed Placement - Legalizes placement of cells into standard cell rows while adhering to global placement.

To do placement in OpenLANE the following command is used.

      run_placement

 ### Viewing Placement in Magic
 
The placement can be viewed on Magic tool as:

![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/1_placement.PNG)
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/2_placement.PNG)

      
 During the run_placement, global placement is happening. The standard cells will be placed as shown in next figure.
 
![](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/std%20cells1.PNG)

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




  
          

 


