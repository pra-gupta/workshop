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
**Fig01: Workflow**

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
![image](https://user-images.githubusercontent.com/107258443/175495536-5cb700b5-2039-48d7-a26e-b37adf9e0cb0.png)
**Fig02: PDKs used**

### Designs Available
We are going to use picorv32a
![image](https://user-images.githubusercontent.com/107258443/175495764-b4a0e916-ae98-4bf3-b54c-cd152ec1ce4a.png)
**Fig03: Design for analysis**
  
### Docker & OpenLane
  1. run "docker" command to create required environment.
  2. execute ./flow.tcl -interactive to run the OpenLANE flow in interactive mode.
  3. import opemnlane package using "package require openlane" 
![image](https://user-images.githubusercontent.com/107258443/175498800-09f00eef-4b79-425b-aa56-c4e7252c4318.png)
**Fig04: Commands: docker & flow.tcl**

### Prepare design Stage
    Command: "prep -design picorv32a"
    This creates requried directory structure. User must edit config.tcl for desired settings for different flows as per need of design & technology.
![image](https://user-images.githubusercontent.com/107258443/175499097-42603857-4e09-4977-822e-fc91e1886b73.png)
**Fig05: Prep stage**

![image](https://user-images.githubusercontent.com/107258443/175499497-125382d9-b025-4c8e-b7db-95cbf87e072e.png)
**Fig05a: Directory structure after prep stage**

![image](https://user-images.githubusercontent.com/107258443/175499672-38ba6db5-4838-4630-81b1-d13ded25a3ba.png)
**Fig05b: Directory structure after prep stage**


### Synthesis
      command: run_synthesis
      Reports: 
      1. 1-yosys_pre.stats shows different componenets count like reg, mem, combo counts per functionality.
      2. 1-sta.rpt has timing related data - transition time, clock instances etc.
![image](https://user-images.githubusercontent.com/107258443/175500991-bbc7bf3b-75c6-40df-be73-d07576b85c28.png)
**Fig06: Command: run_synthesis**

![image](https://user-images.githubusercontent.com/107258443/175511831-e576cdea-36f1-4c7c-81bc-9d3ea18880a6.png)
**Fig06a: Synthesized netlist generated**

![image](https://user-images.githubusercontent.com/107258443/175510707-9674bd67-817d-492a-9225-9d9d836f441e.png)
**Fig06b: Reports generated after "run_synthesis" step**

![image](https://user-images.githubusercontent.com/107258443/175509117-ad5ff683-e8f1-4e99-8cdf-8c93193d94a1.png)
**Fig06c: Reports: Cells used (based on functionality)**

![image](https://user-images.githubusercontent.com/107258443/175501519-463bc031-a6de-4980-9f82-ed75c6f496b0.png)
**Fig06d: Reports: More detailed Cells distribution**

![image](https://user-images.githubusercontent.com/107258443/175510097-0dc1b6ec-d53a-4c1b-b5aa-968dab114724.png)
**Fig06e: Timing report from openSTA**

## Day2
### Floorplanning
   Needs to set folllwoing varialbles in config.tcl:
      1. Die/Core Area
      2. Core Utilization
      3. Aspect Ratio
      4. Macros Placement
      5. Placing input and output pins

![image](https://user-images.githubusercontent.com/107258443/175511257-23741a33-ded9-4d53-9d8c-ef1d22ec4e6f.png)
**Fig07: Command: "run_floorplan"**

![image](https://user-images.githubusercontent.com/107258443/175512279-7f47033c-0a6b-40c8-8923-9001c5183635.png)
**Fig07a: Floorplan DEF**

![image](https://user-images.githubusercontent.com/107258443/175512477-b0c85b91-c359-4669-9df1-abeac9ed16a2.png)
**Fig07b: Floorplan DEF**
 
#### Viewing Floorplan in Magic
To view our floorplan in Magic we need to provide three files as input:
        1. Magic technology file (sky130A.tech)
        2. Def file (generated from floorplan step)
        3. Merged LEF file (generated during design prep stage)
      Command: magic -T <tech file> lef read <lef file> def read <def file>

![image](https://user-images.githubusercontent.com/107258443/175514443-a5f3f143-6f7b-4554-b528-b6bdac0a8b60.png)
**Fig07c: Floorplan DEF in Magic layout window**

![image](https://user-images.githubusercontent.com/107258443/175514234-64155dd4-ef64-4ce3-9bc8-df6737f69422.png)
**Fig07d: I/O Pins in magic (zoomed view)**
 
### Placement
     Placement of standard cells takes place in this stage after floor planning. The synthesized netlist has been mapped to standard cells and floorplanning phase has determined the standard cells rows, enabling placement. 
          Multiple Optimizations: usign repeaters (based on wire length & cap etc)
          
![image](https://user-images.githubusercontent.com/107258443/175515647-a3841170-11c2-4d6c-a431-b0883aeba05d.png)
**Fig08: Placement completed**
  
![image](https://user-images.githubusercontent.com/107258443/175515889-c4344104-8d6c-40c8-b5a7-30b6b93fafdf.png)
**Fig08a: Placement Results**

#### Viewing Placement in Magic

![image](https://user-images.githubusercontent.com/107258443/175516112-0017809a-47fd-48cb-8263-e80624148f25.png)
**Fig08b: Magic Layout View**
  
![image](https://user-images.githubusercontent.com/107258443/175516432-8da63f5e-93d5-49be-85ea-68a97a49a967.png)
**Fig08c: Zoomed Magic Layout View**
  
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
          
![image](https://user-images.githubusercontent.com/107258443/175517598-86c516de-199e-4a3a-aca3-9565549acf28.png)
**Fig09: Inverter layout in Magic**

#### Viewing metal connects in magic:
      Command: hover & press "S" multiple times.
![image](https://user-images.githubusercontent.com/107258443/175517817-b111671d-effe-45e0-b820-98b92084dad7.png)
**Fig09a: Gate connection of nmos & pmos**
  
### DRC Errors
  DRC errors in magic will be highlighted with white dotted lines:
  Steps to create DRC error: 
  1. remove some metal area to see DRC errors.
  2. Select area & press "d"

![image](https://user-images.githubusercontent.com/107258443/175520227-c5595c01-1168-4312-ac41-17b97207d8c1.png)
**Fig09b: DRC Check highlighted in Magic**

![image](https://user-images.githubusercontent.com/107258443/175522815-2977426e-0e14-4d9c-a10c-93a0ee3349fc.png)
**Fig09c: DRC Error details in Magic's tkcon shell**
  
### Extraction with Magic:
  Parasitics can be extracted using magic for the standard cell for spice simulation using following:
        1. "extract all" - generates *.ext file
        2. This needs to be converted to spice file using 
          "ext2spice cthresh 0 rthresh 0"
          "ext2spice"
![image](https://user-images.githubusercontent.com/107258443/175523562-3b9e2001-587c-4c65-9349-f7f8ce081a85.png)
**Fig09d: Commands to extract spice file from magic**
  
![image](https://user-images.githubusercontent.com/107258443/175523970-e2a9c4df-16a7-4ec0-b5e0-fda18e3dc29e.png)
**Fig09e: Extracted spice file from magic**
  
 
          <image of spice wrapper>
            
### Spice simulation of Inverter
Above information is used to write wrapper for spice simulation
Commands for same are shared above while documenting traing (start of Day3)
            
**Note:** In spice, X subscript is used when we are invoking existing subckt (i.e., if we have .subckt nshort_model.0 ).
Since here we are invoking nmos and pmos models directly, we need to change X0 to M0 and X1 to M1.
            
**Snapshots below:**
![image](https://user-images.githubusercontent.com/107258443/175542294-effbb3f8-f0ef-4e09-b9fa-8e63c1d5a958.png)
**Fig10: Modified Spice wrapper for simulation**
            
![image](https://user-images.githubusercontent.com/107258443/175543610-7aba2967-2a68-49c0-81d6-ff818c1d1e53.png)
**Fig10a: Spice simulation complete (transient)**

![image](https://user-images.githubusercontent.com/107258443/175544420-f46cc12f-82a2-42fe-b83c-39e8832229e0.png)
**Fig10b: Spice simulation Waveform (transient)**

![image](https://user-images.githubusercontent.com/107258443/175547695-4548db90-5032-43e9-8cae-ff38767ef108.png)
**Fig10b: Spice simulation: Output rise transition calculation**
            
## Day 4
### Generate Layout info from Magic
   Magic can extract layout info in LEF format for PNR Tools. FOllowign rules need to be taken care:
       1. I/P & O/P port must lie on the intersection of vertical & horizontal tracks
       2.Width of std cell should odd multiple of track pitch & Height should be odd multiple of track vertical pitch

track.info 
<metal layer> <direction> <offset> <pitch>
![image](https://user-images.githubusercontent.com/107258443/175540934-73a5571d-bc66-43ea-96eb-2a90cb7c7c17.png)
**Fig11: tracks.info for sky130_fd_sc_hd**

![image](https://user-images.githubusercontent.com/107258443/175549109-fdd4dd0a-2cc7-475d-bdf1-50a97ef8a767.png)
**Fig11a: correctness of port location as per track.info**

 ### LEF Generation in Magic
![image](https://user-images.githubusercontent.com/107258443/175551967-cf05c0e3-352c-4aee-a3f6-7460490266b1.png)
**Fig11b: Ouptput of "lef write" from magic**

![image](https://user-images.githubusercontent.com/107258443/175552639-81406624-a767-4742-85c4-a6a9889d662b.png)
**Fig11c: sky130_pratinv.lef**
  
###  Including Custom Cells in OpenLANE
In order to include the new cells in OpenLANE we need to do some initial configuration:

1. Fully characterize new cell with GUNA for specified corners (already provided for this lab)
2. Include cell level liberty file in top level liberty file
3. Reconfigure synthesis switches in design's config.tcl:
_  set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]_
**Note: **This step will also include any extra LEF files generated for the custom standard cell(s)

![image](https://user-images.githubusercontent.com/107258443/175564088-55d19677-97cb-4759-9f68-6f70cd0241f8.png)
**Fig11d: modified config.tcl**
  
4. Overwrite previous run to include new configuration switches.
  _prep -design picorv32a -tag modified -overwrite_

  ![image](https://user-images.githubusercontent.com/107258443/175564387-569a4412-e003-40cd-b3dd-0c5473b35f1e.png)
**Fig11e: prep design complete**
  
5. Add additional statements to include extra cell LEFs:
_      set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
      add_lefs -src $lefs_
![image](https://user-images.githubusercontent.com/107258443/175565042-dff6807b-1312-4ba6-9337-bf243113e7b9.png)
**Fig11f: Merge lef**
  
6. Check synthesis reports to ensure cell has been integrated correctly
![image](https://user-images.githubusercontent.com/107258443/175565830-d7c0ffb0-e6ab-44ce-98f4-dc9d9461ccc7.png)
 **Fig11g: Synthesis took modified "pratinv" cell** 
  
### Fixing Slack Violations

For the design to be complete, the worst negative slack needs to be above or equal to 0. If the slack is outside of this range we can do one of multiple things:
  1. Review our synthesis strategy in OpenLANE
  2. Enable cell buffering
  3. Perform manual cell replacement on our WNS path with the OpenSTA tool
  4. Optimize the fanout value with OpenLANE tool 

  Following iterative steps are done for same:
  1. report_check
  2. Change Fanout - for higher slews SYNTH_MAX_FANOUT
  3. report_check
  4. Replace cells (if cap is high ). This will take hit on area.
  5. report_check
  
### Clock Tree Synthesis
  
  ![image](https://user-images.githubusercontent.com/107258443/175641966-24db190a-7393-40b8-b3ac-d52bdefab688.png)
Fig12: run_cts
  ![image](https://user-images.githubusercontent.com/107258443/175643629-963cb532-9f37-4736-b501-c580a3d5da91.png)
  Fig12a: CTS done

  ![image](https://user-images.githubusercontent.com/107258443/175644428-1399bd33-9ef2-4473-8f07-ef6ec3efac8c.png)
Fig12a: New CTS netlsit avialable
  
  ![image](https://user-images.githubusercontent.com/107258443/175648244-4cb911a6-b689-4332-a385-5890ca739a52.png)
Fig12b: hold slack after cts
  
  ![image](https://user-images.githubusercontent.com/107258443/175648412-0d9a7c7d-6235-4ffe-a5b2-161e6ae866fb.png)
Fig12c: setup slack after cts

  Typical 
  ![image](https://user-images.githubusercontent.com/107258443/175649552-17616bd6-cba0-4dc2-8eab-0833e22ec660.png)
Fig12d: hold violated - Typical
  
  ![image](https://user-images.githubusercontent.com/107258443/175649740-76ad42f2-2334-4899-a738-2284bd2105a6.png)
Fig12d: hold MET - Typical
  
## Day5
###Routing
Maze Routing:
1. Connect source to Target
2. Priority: L shape, Z shape, zigzag
3. Rule of thumb: Try to have minimum number of bends in routing.

Routing Rules: 
1. Routing first happens on lower metal layers. Once its complete, it does upper layer routing. 
Ex: First M1 routing happen & then M2
2. Via creation happens only during upper layer routing (M2) & not during lower layer routing (M1)


Processing Routing GUides:
1. Initial route guides
2. Splitting (if there are any horizontal guide), if tool is routing on vertical layer
3. Merging
4. Bridging
5. Pre-processed route Guide.
![image](https://user-images.githubusercontent.com/107258443/177988059-0da5ccb9-dc8f-4f93-bff0-546e58fe3455.png)



###DRC:
Exmaple: DRCviolation: signal short
Solution: use different metal layers. (M1,M3)

