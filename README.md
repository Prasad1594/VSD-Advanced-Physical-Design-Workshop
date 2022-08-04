# VSD-Advanced-Physical-Design-Workshop

This file contains all the labs and Exams that were undertaken for the Advanced Physical Design Workshop conducted by VSD using OpenLane/Sky130 from 03<sup>rd</sup> Aug 2022 to 07<sup>th</sup> Aug 2022.

## Day 1 - Getting Familiar with OpenLane

Day 1 lectures mainly focused on getting started with the basics like what exactly a chip is or what is the actual difference between a chip and a package and similar stuff. Then there was a brief introduction about OpenLane Platform and it's architecture.
The lab for Day 1 was getting familiar with OpenLane platform and getting to know the directory structure for the work folders provided for the workshop.

One important thing to note is the path of the folder that would be used by us is
`/Desktop/work/tools/openlane_working_dir/openlane$`

We start by running the docker daemon service
`/Desktop/work/tools/openlane_working_dir/openlane$ docker`

which should open the bash shell and look something like this.

![Getting Started](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/bash.png)

Once it is booted up, we need to run the **flow.tcl** file. Another important thing to note here is that the file needs to be run interactively which allows us to run the Openlane Tool in a step by step process rather than running all the commands at once as shown in the picture.

![OpenLane Tool](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/flowtcl.png)

Next step is importing all the packages required for running the flow using command

`package require openlane 0.9`

Now, setting up the design preparation stage. OpenLane has quite a few design which are loaded by default. For this workshop, we will be using the picorv32a RiscV Processor. The design can be prepared in OpenLane using the command

`prep -design picorv32a` 

This also merges cell and technology level lef files. Once finished preparation, the shell should look something like this
![Prepare the Design](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/PrepDesign.png)

After preparation is finished, next step is running the synthesis.

`run_synthesis`

It runs both YOSYS and ABC synthesis and may take up to 3-5 mins. It should return a synthesis was successful message. It returns loads of information regarding the processor which can be accessed for further assessment.

![Successful Synthesis](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/synthesis.png)

The task for Day 1 is to calculate the flop Ratio for D Flip-Flop which is an important factor in guaging a processor performance. It is nothing but ratio of number of D Flip-Flops to the number of total cells.

![Flop Ratio](https://github.com/Prasad1594/VSD-Advanced-Physical-Design-Workshop/blob/main/images/flopration.png)

As seen in the image, the number of D Flip-Flops are **1613** and the number of total cells are **14876** which gives us the Flop Ratio of **0.1084** or **10.84%**
