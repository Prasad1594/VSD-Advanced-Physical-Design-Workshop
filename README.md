# VSD-Advanced-Physical-Design-Workshop

This file contains all the labs and Exams that were undertaken for the Advanced Physical Design Workshop conducted by VSD using OpenLane/Sky130 from 03<sup>rd</sup> Aug 2022 to 07<sup>th</sup> Aug 2022.

## Day 1 - Getting Familiar with OpenLane

Day 1 lectures mainly focused on getting started with the basics like what exactly a chip is or what is the actual difference between a chip and a package and similar stuff. Then there was a brief introduction about OpenLane Platform and it's architecture.
The lab for Day 1 was getting familiar with OpenLane platform and getting to know the directory structure for the work folders provided for the workshop.

One important thing to note is the path of the folder that would be used by us is
```
/Desktop/work/tools/openlane_working_dir/openlane$
```

We start by running the docker daemon service
```
/Desktop/work/tools/openlane_working_dir/openlane$ docker
```

which should open the bash shell and look something like this.

![Getting Started](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/bash.png)

Once it is booted up, we need to run the **flow.tcl** file. Another important thing to note here is that the file needs to be run interactively which allows us to run the Openlane Tool in a step by step process rather than running all the commands at once as shown in the picture.

![OpenLane Tool](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/flowtcl.png)

Next step is importing all the packages required for running the flow using command

```
package require openlane 0.9
```

Now, setting up the design preparation stage. OpenLane has quite a few design which are loaded by default. For this workshop, we will be using the picorv32a RiscV Processor. The design can be prepared in OpenLane using the command

```
prep -design picorv32a
```

This also merges cell and technology level lef files. Once finished preparation, the shell should look something like this
![Prepare the Design](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/PrepDesign.png)

After preparation is finished, next step is running the synthesis.

```
run_synthesis
```

It runs both YOSYS and ABC synthesis and may take up to 3-5 mins. It should return a synthesis was successful message. It returns loads of information regarding the processor which can be accessed for further assessment.

![Successful Synthesis](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/synthesis.png)

The task for Day 1 is to calculate the flop Ratio for D Flip-Flop which is an important factor in guaging a processor performance. It is nothing but ratio of number of D Flip-Flops to the number of total cells.

![Flop Ratio](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/flopration.png)

As seen in the image, the number of D Flip-Flops are **1613** and the number of total cells are **14876** which gives us the Flop Ratio of **0.1084** or **10.84%**

## Day 2 - Floorplanning

A netlist defines connectivity between all the components in the circuit. The chip consists of two main components viz. Core and Die. Core is encapsulated in the Die and holds all the logic components of the chip.

Utilization factor of a chip is defined as the ratio of area occupied by all the blocks to the total available area.

An Aspect Ratio of a chip is defined as ratio of height to the width of the chip. An aspect ratio of 1 indicates a square chip.

The arrangement of a set of block which is better known as an IP is called as **Floorplanning**

Day 2 of the lab focusses on Floorplanning which is the next step after running the synthesis.

Floorplan can be run using the command

```
run_floorplan
```

It is important to have a less dense design during initial phase to meet the timing constraints.
A successful floorplan run should return something like this.
![Floorplan](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/floorplansuccess.png)

Bash shell can only do as much as return if the floorplan was successful. However, wouldn't it be awesome to see the actual layout after the floorplan? This is where another tool called as **Magic** comes in. To see the layout in Magic, we need to change the active directory where our floorplan results are located. In this case, let's change the directory to

```
Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/05-08_02-14/results/floorplan/
```

Viewing the layout consists of two steps:

- Run Magic with Tec file and merged lef file

```
magic -T /home/patil.prasad19/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech
```

- Read the already merged def file

```
read ../../tmp/merged.lef def read picorv32a.floorplan.def
```

This is the completely loaded layout in Magic.
![Floorplan Layout](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/floorplan.png)

The next stage in design after Floorplan is the Placement stage. Main focus of this stage is to reduce the Half Parameter Wire Length which can be run using command,

```
run_placement
```

A successful placement run should return something like this. A change in HPWL can be observed here after successful run placement.

![Placement](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/successfulplacement.png)

Similar to floorplan, to view the placement we need to navigate one folder up and again run the **magic** tool.

```
magic -T /home/patil.prasad19/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech read ../../tmp/merged.lef def read picorv32a.placement.def
```

It can be observed from the Magic Tool that all the standard cells have now been populated on the floorplan.

![Standard Cells](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/placementsdc.png)

## Day 3 - Designing Library Cell and NGSpice Characterization

A Spice Deck is connectivity information of the cells using Netlist.

For the Spice simulation, we will be using a pre-built Standard Cell Design library built by @Nickson-jose.

We start by copying our sky130A .tech file to the cloned standard cell repository.

We can see our recently copied CMOS inverter in Magic Tool by loading the tech file.

![Inverter](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/inverter.png)

We will try to extract the Spice netlist from the Inverter using **tkcon** 

```
extract all
```

![Extract Spice Netlist](extractspice.png)

As seen in the image, it will extract the design into ext file. This ext file can be used to create the spice file.

```
ext2spice cthresh 0 rthresh 0
```

![Extract to Spice](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/ext2spice.png)

This is the extracted Spice File
![Spice File](spicefile.png)

It can be observed from the Spice file that the actual dimensions do not correspond to dimensions in Spice file which are 10000u. Hence, they need to be changed to the dimensions corresponding to 1 box which is 0.01u.

We also need to include the pmos and nmos library files in the Spice file

```
.include ./libs/pshort.lib
.include ./libs/nshort.lib
```

We need to specify the Source and Drain voltages as well as the Pulsating voltage. Here the **VDD** is 3.3V which is connected between **VPWR** and **VSS** is 0V which is connected to **VGND**
```
VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
```
To run the Transient analysis, we can define the parameters,
```
.tran 1n 20n
.control
run
.endc

```

Before running the spice tool, we need to ensure the correct definition of PMOS and NMOS is specified. In our case, the names of models being used are `pshort_model.0` for **PMOS** and `nshort_model.0` for **NMOS**.
The Spice Simulation can be run using command,
```
ngspice sky130_inv.spice
```

It should return a window with successful Simulation and the values used which can be verified from our initial file
![Spice](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/inv_ngspice.png)

We can plot the output vs time graph using
```
plot y vs time a
```
![Graph](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/outgraph.png)

The cell rise delay can be calculated using this output graph.
![Cell Rise Delay](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/cellrise.png)

Calculating the difference in rise times, we can get the Rise delay $ 2.206 - 2.149 = 0.057ns $. Using similar method we can calculate the falling delay from the falling edge, $ 4.074 - 4.050 = 0.024ns $.
