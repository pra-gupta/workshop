# OpenLANE Workshop

## About The Project
This project gives an interactive tutorial experied during the VSD Advanced Physical Design workshop using OpenLANE.
OpenLANE is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, Fault, OpenPhySyn, SPEF-Extractor and custom methodology scripts for design exploration and optimization. The goal of OpenLANE is to produce clean GDSII without any human intervention. OpenLANE is tuned for Skywater 130nm open-source PDK.

## Terminologies:
- PDK: Process Design Kit (PDK) is the interface between the CAD designers and the foundry.
- CTS: Clock Tree Synthesis: It implements clock distribution network for sequential cells.
- RTL: Register Transfer Level: provides logic connection information of componenets (Seq, memory, combinational,etc) for design
- PDN - Stands for Power Distribution Network


## WorkFlow:
![image](https://user-images.githubusercontent.com/107258443/175244818-882577f4-60ac-4698-bddd-8aa0074ea9cf.png)

## Stages
OpenLANE flow consists of several stages. 

### 1. Synthesis
- Yosys: Used for RTL synthesis
- abc: Performs cell mapping as per PDK.
- OpenSTA: Used for static timing analysis on the resulting netlist for timing closure.

### 2. Floorplan and PDN
- OpenRoad: is used for this step. Placement is done in two steps, one with global placement in which tool roughly places the design across the chip. In detailed placement, proper placemennts happens which fit in the standard cell rows.
  
### 3. CTS
- TritonCTS - Synthesizes the clock distribution network
  
### 4. Routing
- FastRoute - Performs global routing to generate a guide file for the detailed router
- TritonRoute - Performs detailed routing from global routing guides
- SPEF-Extractor - Performs SPEF extraction that include parasitic information
  
### 5. GDSII Generation
- Magic - Streams out the final GDSII layout file from the routed def
  
=======================================================================================

## Day 1
### Skywater PDK files:
1. Sky130A – The open-source compatible PDK files which are used in this project
< PDK Image>

### Designs Available
We are going to use picorv32a
<Design folder image>
  
### Docker & OpenLane
  1. run "docker" command to create required environment.
  2. execute ./flow.tcl -interactive to run the OpenLANE flow in interactive mode.
  3. import opemnlane package using "package require openlane" 
  <Docker, OepnLane image with flow.tcl >

### Prepare design Stage
    Command: "prep -design picorv32a"
    This creates requried directory structure. User must edit config.tcl for desired settings for different flows as per need of design & technology.
    <Directory structure image>

### Synthesis
      command: run_synthesis
      Reports: 
      1. 1-yosys_pre.stats shows different componenets count like reg, mem, combo counts per functionality.
      2. 1-sta.rpt has timing related data - transition time, clock instances etc.
      
## Day2
### Floorplanning
      Needs to set folllwoing:
      1. Die/Core Area
      2. Core Utilization
      3. Aspect Ratio
      4. Macros Placement
      5. Placing input and output pins
      
      command: "run_floorplan"
      < command image>
      <def image>
 
#### Viewing Floorplan in Magic
To view our floorplan in Magic we need to provide three files as input:
        1. Magic technology file (sky130A.tech)
        2. Def file (generated from floorplan step)
        3. Merged LEF file (generated during design prep stage)
      Command: magic -T <tech file> lef read <lef file> def read <def file>
        <Magic image>
 
### Placement
     Placement of standard cells takes place in this stage after floor planning. The synthesized netlist has been mapped to standard cells and floorplanning phase has determined the standard cells rows, enabling placement. 
          Multiple Optimizations: usign repeaters (based on wire length & cap etc)
          
          Command: "run_placement"
    <run image> 
      <Output image>
#### Viewing Placement in Magic
        <Image>
          
### Standard Cell Characterization

- Inputs - PDKs, DRC & LVS rules, SPICE models, library & user-defined specs.
- Design Steps - Software GUNA used for characterization.
- Outputs - Outputs are CDL, GDSII, LEF, extracted Spice netlist (.cir), & provide Timing (delays, slews), Power, Noise, function informataion in .lib format
==> .lib information is important for PD analysis.
          
## Day 3

          
          
### Plotting spice w/f:
To plot the output waveform of the spice deck we will use ngspice. The steps to run the simulation on ngpice are as follows:

1. Source the .cir spice deck file
2. Run the spice file by: run
3. Run: setplot → allows you to view any plots possible from the simulations specified in the spice deck
4. Select the simulation desired by entering the simulation name in the terminal
5. Run: display to see nodes available for plotting
6. Run: plot  vs  to obtain output waveform       
          
          
Robustness of Inverter: Switching threshold (Vm) i.e. when Vin = Vout 
          ==>calculated using spice
          
### Inverter cell fabrication:
  Using 16 Mask process includes following:
  1. Substrate selection
  2. Creating Active reagions
  3. Nwell / Pwell creation
  4. Source and Drain formation
  5. Contacts & local interconnect Creation using SiO2 for isolation & TitaniumNitrate for connects.
  6. Higher metal layer connects using aluminium

### Layout View of above Inverter Cell in Magic Software
   1. Gitclone files from https://github.com/nickson-jose/vsdstdcelldesign
   2. Load them in Magic to view:
    magic -T sky130A.tech sky130A_inverter.mag &
          
    <image of Inverter>
#### Viewing metal connects in magic:
      Command: hover & press "S" multiple times.
      <Image of connect>

### DRC Errors
        
        
### Extraction with Magic:
  Parasitics can be extracted using magic for the standard cell for spice simulation using following:
        1. "extract all" - generates *.ext file
        2. This needs to be converted to spice file using 
          "ext2spice cthresh 0 rthresh 0"
          "ext2spice"
        <image of extract.spice>
 
  Above information is used to write wrapper for spice simulation
          <image of spice wrapper>
            
### Spice simulation of Inverter
     Commands for same are shared above while documenting traing (start of Day3)
     Snapshots below:
     <image of ngspice>
     <image of simulation w/f>
       
## Day 4
       
