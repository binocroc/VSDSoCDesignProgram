# **SoC Design Program using OpenLANE: Digital VLSI SoC Design and Planning**

 *Author: Srinivasa Yashwanth*

 *Mail: yashwanthm09@gmail.com*
 
 *_Acknowledgements: SoC design Program by [Mr. Kunal Ghosh](https://github.com/kunalg123) , [VLSI System Design](https://www.vlsisystemdesign.com/)_*

 ## **Introduction**
Hardware and software are used in SoC design to manage the CPU, peripherals, and interfaces. In an architectural co-design approach, a robust design workflow simultaneously creates hardware and software.

In terms of hardware, many SoCs are created utilising hardware component IP core specifications, sometimes referred to as blocks, just like in integrated circuit design. EDA tools are used to generate hardware blocks. OpenLANE, an open source tool is used for the entire flow from synthesis to signoff for this instance of RISC-V design.

OpenLane is an automated RTL to GDSII pipeline that is built upon a number of bespoke scripts for design exploration and optimisation, OpenROAD, Yosys, Magic, Netgen, CVC, SPEF-Extractor, and KLayout. Additionally, it offers some unique scripts for optimisation and design research.

All ASIC implementation processes, from RTL to GDSII, are carried out by the flow. It now supports the C version of the gf180mcu PDK, the A and B versions of the sky130 PDK, and documentation detailing how to add support for additional PDKs, including proprietary ones, is available.

Links to Navigate each day:
1. [DAY-1](#DAY-1)
2. [DAY-2](#DAY-2)
3. [DAY-3](#DAY-3)
4. [DAY-4](#DAY-4)
5. [DAY-5](#DAY-5)
6. [CONCLUSION](#CONCLUSION)

## DAY-1

Sub-Task: Invoke OpenLANE in interactive mode and Setup the design for further synthesis of the RTL file

### **Theory:**

![image](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/435c1d7b-567d-47db-9e1d-d3be26d1d6e4)

This is the General ASIC backend flow with some special features of OpenLANE such as Synth Exploration and Design Exploration that help us obtain an optimized layout post parasitic extraction (alias RC extration). Each step is explained below:

1. *RTL Synthesis:*

   The process of translating a hardware description language (HDL), such Verilog or VHDL, into a gate-level representation is known as synthesis. This gate-level   
   representation is made up of flip-flops, various standard cells, and interconnected logic gates taken from a technology library. Yosys is a tool used for Synthesis and 
   abc is used for technology mapping post synthesis.

2. *Static Time Analysis:*

   STA involves analyzing the timing paths within a digital circuit statically, meaning without actually simulating the circuit's behavior over time. Instead, it uses 
   mathematical models and algorithms to predict the timing behavior based on the circuit's structure, delay models of individual components (such as logic gates, flip- 
   flops, etc.), and interconnect delays. OpenSTA uses the Constraints file(.sdc) and performs an initial STA.

3. *Design for Test:*

   Design for Test (DFT) refers to a set of techniques used in the design of digital integrated circuits (ICs) to facilitate testing and verification during the 
   manufacturing process. The primary goal of DFT is to ensure that chips can be efficiently and effectively tested to identify manufacturing defects and ensure their proper 
   functionality. Structures such as Scan Chains, ATPG, BIST are quite often used in the industry.

4. *Floorplanning:*

   The first arrangement and positioning of different functional blocks, components, and macros is known as floor planning. In order to meet design restrictions and maximise 
   chip performance, area utilisation, and connection, it arranges these components spatially. Floor planning's primary goals are to minimise wire lengths, minimise signal 
   delays, optimise power distribution, and guarantee effective chip utilisation. There are two types of floorplanning: Chip-level floorplan; Macro-level floorplan. The chip 
   floorplan establishes the overall architectural framework and organization of components on the semiconductor die and the macro floorplan focuses on the detailed layout 
   and interconnection of specific functional blocks within the chip. This is done using OpenROAD.

5. *Placement:*

   Placement refers to the process of determining the optimal locations for the various components and modules within the chip layout. It is one of the key steps in the 
   physical design flow and plays a significant role in achieving design goals such as performance, power efficiency, and area utilization. It is usually done in two steps:
   Global following Detailed Placement. OpenROAD tool is used for this step.
   
6. *Clock Tree Synthesis:*

    CTS ensures that the clock signal is distributed efficiently and with minimal skew, jitter, and power consumption. Prior to signal routing, Clock Tree Synthesis is 
    completed because the clock must be routed by building a clock distribution network that supplies the clock to each successive block. OpenROAD tool is used for this step.
    
7. *Optimization and Fake Antenna diode insertion script:*

    Post Optimizing the placement and Clock paths, we should address the concern of metal wire segments, that can lead to behave like an antenna (Charge Accumulation point) post fabrication. There are two solutions to this problem: Bridging the antennas, this process will ensure that no metal wire is unattached. But this solution requires antenna awareness on the entirity of the chip that can be gruesome. So, we can use Antenna diode cell to connect the wire to ground. As the charge accumulation happens, the voltage accross the wire exceeds the diode threshold(which is in forward bias) and the charge gets drained across the diode. Instead of adding an antenna diode wherever there is an open metal wire, we'll write a script where all open wires will consist of Fake antenna which we'll later prune the redundant antennas post detailed routing.
   
9. *Routing:*

    The act of figuring out the physical links between different components is called routing. According to PDK, the router makes advantage of the available metal layers. 
    The PDK specifies the thickness, pitch, and other parameters for every metal layer. Six routing tiers are defined by the Sky-130 PDK.
    
10. *Logic Equivalence Check:*

    LEC is used to verify that two different representations of a design (such as RTL description vs. gate-level netlist) are functionally equivalent, meaning they perform 
    the same logical operations and produce identical outputs for the same inputs.
    
11. Detailed Routing is done post LEC and the redundant Fake antennas will be pruned. The resultant routed layout will further undergo RC extraction where layout is converted to a specific schematic and the parasitic components of the schematic are modelled and extracted to create a circuit model.
    
12. *Sign Off:*

    Time verification such as STA and physical verification such as DRC and LVS(Done using MAGIC and NETGEN).

    DRC: Design Rule Check is the acronym for this. DRC's primary goal is to guarantee that the layout complies with manufacturing specifications and is appropriate for the 
    semiconductor fabrication process. Permissible geometries, dimensions, spacing, and other layout criteria are a few of the production guidelines. By identifying and 
    fixing layout problems that may result in fabrication defects or yield loss during semiconductor production, design for manufacture (DRC) is a crucial step in 
    guaranteeing the manufacturability and reliability of integrated circuit designs.

    LVS: Layout vs. Schematic is the acronym for this. To verify consistency and accuracy, the chip layout is compared to the matching schematic depiction. Netlists are 
    extracted by LVS tools from the design's schematic and layout representations.To find any discrepancies, the retrieved netlists from the schematic and layout are 
    compared. The layout is deemed confirmed once LVS clean, at which point the design can move on to the next phases of the physical design cycle.

    STA: Static Timing Analysis is what STA stands for. It assesses a digital circuit's timing behaviour without taking dynamic elements like clock skew and signal 
    transitions into account. It ascertains whether the design satisfies timing requirements for maximum clock frequency, setup and hold times, and other factors.

### **Labs:**

##### OpenLANE Directory Structure:

![day_1_1](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/4de0b271-8f53-4551-bef8-c94217d329d0)

#### PDK Directory:

![day_1_1](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/aec4b11c-9ad3-46f5-8866-a5fdc6f53ab4)
* The sky130A pdk version is specifically designed to work with open-source tools. It includes libs.tech and libs.ref. Libs.ref holds all the files particular to a process or technology, such as sky130_fd_sc_lp, which stands for Sky130nm Foundry Standard Cell Low Power. Files unique to the tools can be found on libs.tech.

#### Design Preparation:

* `docker` command invokes OpenLANE and `./flow.tcl -interactive` allows the openlane to enter interactive mode.
* `% package require openlane 0.9` retrives all packages and dependencies for running v0.9 of OpenLANE

![day_1_3](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/3e2f7caf-7410-481b-a04a-7ea908991902)

Establish the design stage for the flow and start the design synthesis process.

   ```
   prep -design picorv32a
   ```
This command ensures that the design files and configurations of picorv32a are loaded, this is the reason why prep must be run before any command in OpenLANE.

![day_1_4](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/1b90fb93-06d5-4a3a-a1b7-2c1a6fe61ef7)

After design preparation, review the files, perform synthesis, and describe the findings.

   ```
   run_synthesis
   ```
This command performs yosys RTL synthesis, ABC scripts and openSTA.

![day_1_6](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/5c1baaa2-ba2e-4595-9ee8-bf34f8677e74)

RTL(.v)-to-Netlist(.synth):

![day_1v2s](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/99ffe7b6-03c8-46a0-86d3-fe04fadc1acd)

Flop Ratio can be caliberated from runs/<date>/reports/synthesis/yosys_4.stat.rpt:

![day_1_7](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/1e72f1bd-f027-4731-bd16-5efe053ea34d)

the FF count: sky130_fd_sc_hd__dfxtp_2 = 1613
Total number of cells = 14876

Flip-flop ratio = count of DFFs / total number of cells = 1613 / 14876 = 10.8429 %

Further reports and log files can be evaluated in the runs directory:

![day_1_5](https://github.com/binocroc/VSDSoCDesignProgram/assets/59701387/72779415-645a-41be-8d53-393ddf989966)


## DAY-2

