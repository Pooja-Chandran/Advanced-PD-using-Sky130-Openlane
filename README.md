# Advanced-PD-using-Sky130-Openlane

![FIG:1](https://github.com/Pooja-Chandran/Advanced-PD-using-Sky130-Openlane/blob/main/Advanced-Physical-Design-using-OpenLANE_Sky130_1.png)

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
        
        
 The first lab was to synthesise the RTL code of a picorv32a in the interactive mode using OpenLANE.
 The script ot invoke OpenLANE in interactive mode is: 
              ./flow.tcl -interactive
  
  Include the package by using the below command
             package require openlane 0.9
             
  After that you need to prepare your design files "picorv32a" with below command
             prep -design ./designs/picorv32a
             
  When the design is prepared run syntheis by using below command
              run_synthesis
          
  Finally we need check the total area occupied by picorv32a in form of flipflops, adders, AND, OR gates etc. :
        ![FIG:3](https://github.com/ripudamank2/OpenLANE-Sky130-Physical-Design-Workshop/blob/main/image.png)
 


