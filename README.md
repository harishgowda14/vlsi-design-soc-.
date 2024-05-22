# vlsi-design-soc-.
# Table of Contents
1. [Day 1:](#day1)
    - [Get Familiar with Open-Source EDA Tools](#get-familiar-with-open-source-eda-tools)
    - [Intro to OpenLane](#intro-to-openlane)
    - [Directory Structure of Openlane:](#directory-structure-of-openlane)
    - [Steps to characterize synthesis results](#steps-to-characterize-synthesis-results)
   
2. [Day 2:](#day2)
    - [Steps to run floorplan using OpenLANE](#steps-to-run-floorplan-using-openlane)
    - [Review floorplan layout in Magic](#review-floorplan-layout-in-magic)
    - [Congestion aware placement using RePlAce](#congestion-aware-placement)
3. [Day3:](#day3)
    - [Lab introduction to Sky130 basic layers layout and LEF using inverte](#lab-intro-to-sky130-basics)
    - [Lab steps to create std cell layout and extract spice netlist](#lab-setps-create-std-cell-layout-and-extract-spice-netlist)
    - [Lab introduction to Magic tool options and DRC rules](#lab-drc-rules)
    - [Lab introduction to Magic and steps to load Sky130 tech-rules](#lab-introduction-to-magic-and-steps-to-load-sky130)
    - [Lab exercise to fix poly.9 error in Sky130 tech-file](#lab-exercise-to-fix-poly.9-tech-file)

4. [Day4:](#day4)
    - [Pre-layout timing analysis and importance of good clock tree ](#pre-layout-timinig-analysis-and-importancs-of-good-clock-tree)
    - [ Lab steps to configure synthesis settings to fix slack and include vsdinv](#lab-steps-to-configure-synthesis-settings)
    - [Lab steps to configure OpenSTA for post-synth timing analysis](#lab-steps-to-configure-opensta-for-post-synth-timing-analysis)
    - [Lab steps to optimize synthesis to reduce setup violations](#lab-steps-to-optimize-synthesis-to-reduce-setup-violations)
    -  [Lab steps to do basic timing ECO](#lab-steps-to-do-basic-timing-eco)
    -  [Lab steps to run CTS using Triton](#lab-steps-to-run-cts-using-triton)
    -  [Lab steps to verify CTS runs](#lab-steps-to-verify-cts-runs)
    -  [Lab steps to execute OpenSTA with right timing libraries and CTS assignment](#lab-steps-to-execute-opensta-with-right-timing-libraries-and-cts-assignment)

5. [Day 5:](#day5)
    - [Final step for RTL2GDS using tritinRoute and openSTA](#Final-step-for-rtltogds2)

# Day1:<a name ="day1"></a>
## Get Familiar with Open-Source EDA Tools<a name="get-familiar-with-open-source-eda-tools"></a>

### Basic Bash Commands:
- `cd`   : Change directory.
- `ls`   : List all files.
- `mkdir`: Make a directory.
- `pwd`  : Show the current directory.
- `clear`: Clear the terminal.
- `tree` :This command is used to print the hierarchy of the file system from the present directory.

### Intro to OpenLane:<a name="intro-to-openlane"></a>
- OpenLane is an automated RTLtoGDSII flow. It consists of a variety of open-source tools including:
<div align="center">

|      Software           |            Description                          |
|:-----------------------:|:-----------------------------------------------:|
|         Yosys           |            Verilog synthesis tool               |
|         Magic           |                Layout editor                    |
|         Netgen          |        Digital netlist comparison tool          |
|         Fault           |            Digital fault simulator              |
| CVC SPEF-Extractor      |  Circuit verification and analysis tool         |
|          CU-GR          |              Global routing tool                |
|         Klayout         |      Mask layout editor and viewer              |

</div>


## Directory Structure of Openlane:<a name="directory-structure-of-openlane"></a>
- This the directory structure of the openlane

```

openlane
├── AUTHORS.md
├── clean_runs.tcl
├── configuration
├── conf.py
├── CONTRIBUTING.md
├── default.cvcrc
├── designs
├── docker_build
├── docs
├── flow.tcl
├── LICENSE
├── Makefile
├── README.md
├── regression_results
├── report_generation_wrapper.py
├── run_designs.py
└── scripts
```
- In the designs folder we have a couple of designs that comes witht the openlane.Thefollowing designs are included in the openlane.
```

designs
├── 151
├── aes
├── aes128
├── aes192
├── aes256
├── aes_cipher
├── aes_core
├── APU
├── blabla
├── BM64
├── chacha
├── cic_decimator
├── des
├── des3
├── digital_pll_sky130_fd_sc_hd
├── genericfir
├── inverter
├── jpeg_encoder
├── ldpc_decoder_802_3an
├── ldpcenc
├── manual_macro_placement_test
├── md5
├── ocs_blitter
├── picorv32a
├── point_add
├── point_scalar_mult
├── PPU
├── README.md
├── s44
├── salsa20
├── sha3
├── sha512
├── sound
├── spm
├── synth_ram
├── usb
├── usb_cdc_core
├── usbf_device
├── wbqspiflash
├── xtea
├── y_dct
├── y_huff
└── zipdiv
```
-  For Demonstration of tools, we have choosen the picorv32a project.Below are the contents of the picrorv32a.
```
picorv32a
.
├── config.tcl
├── runs
│   ├── 10-04_20-22
│   ├── 11-04_07-34
│   ├── 12-04_08-02
│   └── 12-04_14-17
├── sky130A_sky130_fd_sc_hd_config.tcl
├── sky130A_sky130_fd_sc_hdll_config.tcl
├── sky130A_sky130_fd_sc_hs_config.tcl
├── sky130A_sky130_fd_sc_ls_config.tcl
├── sky130A_sky130_fd_sc_ms_config.tcl
└── src
    ├── picorv32a.sdc
    └── picorv32a.v
```
- This config.tlc file contains every details about the design. for example, details about enrollment, clock period, clock period port etc.The setting in the config.tcl has higher precedence compared to the default canfigurations. These settings will override the default settings of the openlane.

## Design Preparation Step:
- To access the openlane tool first we have to change the directory to the 
`/<path to openlane_working_dir>/openlane`, we can access the files .
In this folder we have to type
```
$  docker
$ ./flow.tcl --interactive
```
- Here we are using `--interactive` , because we are manually running every tool by overselves.If we just execute `./flow.tcl` The entire process from RTL to GDS will we completed.

![image](https://i.imgur.com/OzGRR6A.jpeg)

- we need add the required  packages , the below command does that.
```
$package require openlane 0.9
```
- before running the synthesis , we have to prep the design. 
```
$ prep -design picorv32a
```

![image](https://i.imgur.com/00Mqqo7.png)

- Result for the above command.

![image](https://i.imgur.com/D3yTMWZ.png)
- Preparation state  is done ,Now we can processed with the synthesis.Now we executing the  command.
  ```
    $ run_synthesis
  ```

![image](https://i.imgur.com/GrLreJt.png)

- After synthesis , If every thing goes correctly, we can see the terminal result as,

![image](https://i.imgur.com/9dYgzPi.png)

## Steps to characterize synthesis results:<a name="steps-to-characterize-synthesis-results"><\a>
-From the sysnthesis results, we can observe a report printed on the console.

![image](https://i.imgur.com/ZoNDdPU.png)

```
flop ratio = (number of flip flops) / (number of total cells)
```
- From the above data we can absorve that,flip flop ratio is 10.84%.
- After completing the synthesis process, we can now confirm that all the mappings have been performed by ABC.

![image](https://i.imgur.com/oQIMLsn.png)
-In the report, we can identify the completion time of the actual synthesis. The synthesis statistics report displayed below is consistent with the previously observed information.

## Day2: <a name="day2"></a>
# Steps to run floorplan using OpenLANE:<a name="steps-to-run-floorplan-using-openlane"></a>
- To create a floorplanning, we need to execute the command.
```
$ run_floorplan
```

![image](https://i.imgur.com/SD6Bs9e.png)

- If floorplan execute without any error's we should get the terminal output like this.

![image](https://i.imgur.com/mwCxfzn.png)
- Floorplan results are created in the below locations.
```
vsduser@vsdsquadron:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/14-04_05-57/results/floorplan$ ls
merged_unpadded.lef  picorv32a.floorplan.def  picorv32a.floorplan.def.png
```
- picorv32a.floorplan.def contains the floorplaning information and picorv32a.floorplan.def.png is a image of teh floorplan., you can see the image below.

![picorv32a.floorplan.def.png](https://i.imgur.com/QKvhOp2.png)

# Review floorplan layout in Magic : <a name="review-floorplan-layout-in-magic"></a>
- To view  and edit the floor plan we ca use magic, we need to execute the command
```
magic -T /home/<user name>/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def
```

![magic command](https://i.imgur.com/7H2BDcB.png)
- After executing the above command you will find a pop window of magic which should look like,

![magic window](https://i.imgur.com/D7yu9ft.png)
- Then point eh mouse on the design , and press `s`, which will select the design and then press `v` which will make the design center.

![magic s+v](https://i.imgur.com/H6kHeal.png)
- on the design press left click on a corner and right click on other place to select the a portion of the design, then we have to press `z` to zoom that section. In magic we have to press `s` to select any sections, so we randomly choose a component and press 's' ,and shift magic command window and execute 
```
% what
```
- This will show the deatils of the selected component.

![image](https://i.imgur.com/GHUNyvY.png)
- you can observer the the Decap cells are arranged at the border of the side rows.

![image](https://i.imgur.com/miUYCnj.png)
# Congestion aware placement using RePlAce<a name ="congestion-aware-placement"></a>
- After floorplanning , we should go to do placement. As we know we have two type of placements.Global placement and deatiled placement, When we run the placement, first Global placement is happens. main objective of glibal placement is to reducing the length of wires.
- we make the placement by executing the command
```
run_placement
```
- The results of this command are stored the in placement folder in the results.The below shows the results,
```
vsduser@vsdsquadron:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/14-04_05-57/results/placement$ l -ltr
total 3892
lrwxrwxrwx 1 vsduser vsduser      29 Apr 14 11:27 merged_unpadded.lef -> ../../tmp/merged_unpadded.lef
-rw-r--r-- 1 vsduser vsduser 3164924 Apr 14 12:14 picorv32a.placement.def
-rw-r--r-- 1 vsduser vsduser  816328 Apr 14 12:14 picorv32a.placement.def.png
```
- The image `picorv32a.placement.def.png` is the snapshot of the placemnt that is automatilcally created.

![image](https://i.imgur.com/Ge9q8iE.png)

- we view the plcament in magic, execute the command, from the `/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/14-04_05-57/results/placement` folder.
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```
- After executing the command you the magic window will pop and up and you can observe the floorplan.In zoom image you can see the global palcement.

![image](https://i.imgur.com/0nc8OdA.png)
![image](https://i.imgur.com/CCjnruN.png)
# Day3 <a name="day3"></a>
## Lab introduction to Sky130 basic layers layout and LEF using inverter:<a name="lab-intro-to-sky130-basics">
- In the sky130 semiconductor process, different colors represent distinct layers or regions on the integrated circuit. For instance, the blue-purple shade indicates the local interconnect layer, while light purple signifies metal 1 and pink denotes metal 2. N-well regions are depicted by solid dashed lines, while green is used for N-diffusion areas. Polysilicon gates are represented in red, and P-diffusion regions are shown in brown. 
![image](https://i.imgur.com/6eTJ5ln.png)
- In magic tool , we can use `s` for selecting a single item while , we can press `s+s+s` to select all teh connected elements.Then we can use `what` command in magic terminal, to know details about the element.`box` command can be used to get ehe selected area dimention's.
![image](https://i.imgur.com/AeaypkX.png)

## Lab steps to create std cell layout and extract spice netlist: <a name="lab-setps-create-std-cell-layout-and-extract-spice-netlist"></a>
- We have to execute 'extract all` to get the files extracted, execute this command in the magic command window.

![image](https://i.imgur.com/OzIt6AG.png)
- The we have to convert the model to spice model. We can do it  by executing the commands. After executing the `extract all` the `.ext` .
 ```
ext2spice cthresh 0 rthresh 0
ext2spice
```
![image](https://i.imgur.com/AatNHyY.png)
- Then after executing the above commands , the `.spice` will be generated .
- ![image](https://i.imgur.com/KcLTdUa.png)
- The generated spice file contains the following code.
- ![image](https://i.imgur.com/RLQJMOo.png)
- We need to make some changes in the code so that we can make the a transiant simualtion for the given circuit.
![image](https://i.imgur.com/vDycq8d.png)
- Then using ngpsice, we can simulate the inverter.
```
    $ ngspice sky130_inv.spice
```
- After executing the command you will see the output as below, If there are no error.
![image](https://i.imgur.com/H7VfThE.png)
- To plot the graphs we need to enter the below command.And you will the the waveform .
```
ngspice 1-> plot y vs time a
```
![image](https://i.imgur.com/agvq5Vq.png)

- We can calculate the rise time,fall time, propagation delay, cell fall delay from the above graph

### rise time:
- It is the time taken by the signal to rise from 20% to 80% value .rise time=(2.2489-2.1819)=66.92psec

### fall time
- it is the time take by output for transition from 80% to 20%. rise time= (4.09512 - 4.05264)e-09 = 42.51 psec.

### propagation delay
- it is the time difference between the 50% of input and 50% of the output
.propogation delay =(2.2106 - 2.15012)e-09 = 60.48 psec.


### cell fall delay
- it is time for output falling to 50% and input is rising to 50%. so, cell fall delay =(4.07735 - 4.04988)e-09 = 27.47 psec.

## Lab introduction to Magic tool options and DRC rules <a name="lab-drc-rules"></a>
- To download and extract the DRC corrections run the following commands.
```
$ wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
$ tar xfz drc_tests.tgz
$ cd drc_tests
```
- In the folder created you will find the all the files as listed below
```
drc_Tests
├── capm.mag
├── difftap.mag
├── dnwell.mag
├── hvtp.mag
├── hvtr.mag
├── licon.mag
├── li.mag
├── lvtn.mag
├── mcon.mag
├── met1.mag
├── met2.mag
├── met3.mag
├── met4.mag
├── met5.mag
├── npc.mag
├── nsd.mag
├── nwell.mag
├── pad.mag
├── poly.mag
├── psd.mag
├── rpm.mag
├── sky130A.tech
├── tunm.mag
├── varac.mag
├── via2.mag
├── via3.mag
├── via4.mag
└── via.mag

```
## Lab introduction to Magic and steps to load Sky130 tech-rules <a name="lab-introduction-to-magic-and-steps-to-load-sky130"></a>
- To open the magic use the below command
```
$ magic -d XR &
```

![image](https://i.imgur.com/bQ7y8jL.png)
- `drc why` will give the resons for the failure of the system

![image](https://i.imgur.com/nM2bSPq.png)
- Next, select a blank area and hover the mouse pointer over the metal3 contact icon. Press the `p` button and type `pek` in the tkcon. Then execute the command `cif see VIA2` in the tkcon tab.This will show the via information.
- 
![image](https://i.imgur.com/OUb4t8g.png)

- To remove the information , by executing the command `clear feed` . 

![image](https://i.imgur.com/iYUY26C.png)

## Lab exercise to fix poly.9 error in Sky130 tech-file<a name="lab-exercise-to-fix-poly.9-tech-file">
- to load the poly ,execute the command
```
load poly
```

![image](https://i.imgur.com/DOyqf42.png)

- The wrong rules are

![image](https://i.imgur.com/tGv3bAx.png)
![image](https://i.imgur.com/27JbDhS.png)

- The newly added rules are 

![image](https://i.imgur.com/LQOH5rW.png)
![image](https://i.imgur.com/sctP6Dt.png)

- changing the drc rules we have to reload the tech file and and check the drc again by running the commands.

```
tech load sky130A.tech
check drc
```

![image](https://i.imgur.com/ZHjFWwA.png)
![image](https://i.imgur.com/MLYWdDn.png)

# Day 4:<a name="day4"></a>
## Pre-layout timing analysis and importance of good clock tree <a name="pre-layout-timinig-analysis-and-importancs-of-good-clock-tree"></a>

- To integrate the '.lef' file from the '.mag' file into the picorv32a flow, we must adhere to specific guidelines for creating standard cells:

1. Input and output ports must align with the intersection of vertical and horizontal tracks.

2. The standard cell's width should be an odd multiple of the track pitch, and its height should be an odd multiple of the track's vertical pitch.

For further details, we can refer to the track file located at `pdk/sky130/libs.tech/openlane/sky130_fd_sc_hd/track.info.`
![image](https://i.imgur.com/JKgbrBG.png)
- During the routing stage, the track serves as a guide for the routing of metal layers like metal 1, metal 2, etc.
- Automated Place and Route (PNR) requires specifying the routing paths, which are dictated by tracks. Each track is positioned at coordinates (0.23, 0.46)um horizontally and (0.17, 0.34)um vertically for li1, metal 1, and metal 2 layers.
- In the layout, ports are situated on the li1 layer. To ensure that ports align with the intersection of tracks, we need to convert the grid into tracks.
- To accomplish this, we'll first open the tracks file and then access the tkcon window and execute the help grid command

![image](https://i.imgur.com/1r5Rsst.png)
![image](https://i.imgur.com/EFhSGwW.png)

- we have to execute the command, to extrac the `sky130_inv.lef` and `sky130_vsdinv.mag` file
`
lef write 
`
.

![image](https://i.imgur.com/jcuvAYI.png)

- Then we have to copy the file `sky130_vsdinv.mag`  to `src` folder of `picorv32a`, then we can verify the files using the command `ls -ltr` .

![image](https://i.imgur.com/JyEWFJp.png)

- Make these edits to the `congig.tcl` file.

![image](https://i.imgur.com/1TA78BI.png)

- Now we will go to the open lane directory and execute the docker command.Will Execute the following commands in a line

```
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]      
add_lefs -src $lefs
run_synthesis
```

![image](https://i.imgur.com/Cl4ObGh.png)

![image](https://i.imgur.com/Uki8d7j.png)

## Lab steps to configure synthesis settings to fix slack and include vsdinv <a name="lab-steps-to-configure-synthesis-settings"></a>
- We refer to README file and try change some some settings, of the openlane.

![image](https://i.imgur.com/XDFsYWt.png)
- We can set som environment parameters but running the below command.

```
prep -design picorv32a -tag 01-04_12-54 -overwrite

set lefs [glob $::env(DESIGN_DIR)/src/*.lef]

add_lefs -src $lefs

echo $::env(SYNTH_STRATEGY)

set ::env(SYNTH_STRATEGY) "DELAY 3"

echo $::env(SYNTH_BUFFERING)

echo $::env(SYNTH_SIZING)

set ::env(SYNTH_SIZING) 1

echo $::env(SYNTH_DRIVING_CELL)

run_synthesis

prep -design picorv32a -tag 01-04_12-54 -overwrite
```

- In the previous run we have observed the negative slack value
```
wns(worst negative slack)= -23.89
tns(total negative slack)= -711.59.
```
- after changing the settings we will execute the synthesis again by executing the command `run_synthesis`.

![image](https://i.imgur.com/2MgLoOR.png)

![image](https://i.imgur.com/2MgLoOR.png)

- Now we go a step ahead and execute the `run_floor` plan.

![image](https://i.imgur.com/L8uUpnH.png)

![image](https://i.imgur.com/DUbqbq8.png)

- As we encountered the errors we need to execute the below command sto make make it for floorplan.
```
init_floorplan
place_io
tap_decap_or
```

![image](https://i.imgur.com/Isp1Y7P.png)

![image](https://i.imgur.com/6843cmt.png)

![image](https://i.imgur.com/ojH16gV.png)

- Now we can procees with the floorplan. we execute `run_floorplan`

![image](https://i.imgur.com/R3CcfWK.png)

- we can view the floor plan by executing the command `magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def` from the folder `/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/01-04_12-54/results/placement`.

![image](https://i.imgur.com/20Gldfi.png)

- Then we can see inverter ,select the inverter by placing the cursor on the inverter and press `s` then type `expand in the magic terminal.


## Lab steps to configure OpenSTA for post-synth timing analysis <a name ="lab-steps-to-configure-opensta-for-post-synth-timing-analysis"></a>
- we can so STA analysis to find the timining issues.First we will run the synthesis using the following commands in openlane directory

```
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
run_synthesis
```
![image](https://i.imgur.com/W7jaRzi.png)

![image](https://i.imgur.com/fsnXFcL.png)

- Now we have to make a new `pre_sta.conf file`. We can do this by vim editor or in simple text editor also. We store this  `pre_sta.conf` file in `openlane` folder .

![image](https://i.imgur.com/fdG6iDD.png)

![image](https://i.imgur.com/ASfNLxU.png)

- Now we will create a new `my_base.sdc` file in the , and we will copy the content from the `base.sdc` and edit the content as shown below. we store this file in `openlane/designs/picorv32a/src` .

![image](https://i.imgur.com/fdG6iDD.png)

![image](https://i.imgur.com/lTVlFtH.png)

- Then we execute the command `sta pre_sta.conf` , then we get the result as below

![image](https://i.imgur.com/fSIByBb.png)

![image](https://i.imgur.com/VXHQX9N.png)

![image](https://i.imgur.com/4xVpFvP.png)

## Lab steps to optimize synthesis to reduce setup violations:<a name="lab-steps-to-optimize-synthesis-to-reduce-setup-violations"></a>

- Now we will increase the fanout, and again synthesis the circuit ,by running the below commands.
```
prep -design picorv32a -tag 02-04_05-27 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
set ::env(SYNTH_MAX_FANOUT) 4
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```
![image](https://i.imgur.com/EJtFzDt.png)

- Now run the command `sta pre_sta.conf` .

![image](https://i.imgur.com/te6xJyo.png)ab steps to do basic timing ECO

![image](https://i.imgur.com/JhprkAp.png)

![image](https://i.imgur.com/4xVpFvP.png)ab steps to do basic timing ECO

## Lab steps to do basic timing ECO <a name ="lab-steps-to-do-basic-timing-eco"></a>

- To Reports all the connections to a net 
`report_net -connections _11672_`

- To Check the command syntax 
`help replace_cell`

- To Replace the cell 
`replace_cell _14510_ sky130_fd_sc_hd__or3_4`

- To Generate the custom timing report 
`report_checks -fields {net cap slew input_pins} -digits 4`

![image](https://i.imgur.com/604mbSJ.png)

![image](https://i.imgur.com/7cPobas.png)


![image](https://i.imgur.com/JlBPZIA.png)
![image](https://i.imgur.com/5DtmBKt.png)

## Lab steps to run CTS using Triton<a name="lab-steps-to-run-cts-using-triton"></a>
- Now as after we make changes , we the verilog file using the command `write_verilog` and the file is located in  `/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/01-04_12-54/results/synthesis/picorv32a.synthesis.v`

![image](https://i.imgur.com/S5HYKST.png)

![image](https://i.imgur.com/QVrf73V.png)

- Now we don't do the synthesis again , If we did all our modifications will be cleared. instead we continue from floor plan.To run the floor plan , we continue to execute the following commands.

```
   prep -design picorv32a -tag 02-04_05-27 
   set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
   add_lefs -src $lefs
   set ::env(SYNTH_STRATEGY) "DELAY 3"
   set ::env(SYNTH_SIZING) 1
   init_floorplan
   place_io
   tap_decap_or
   run_placement

   # Incase getting error will use this command
   unset ::env(LIB_CTS)

```
![image](https://i.imgur.com/pbWDGXC.png)

![image](https://i.imgur.com/PjbIpzy.png)

![image](https://i.imgur.com/shMF6Um.png)

![image](https://i.imgur.com/P88rKwW.png)

![image](https://i.imgur.com/PHL4v5f.png)

- Finally we run the command `run_cts`

![image](https://i.imgur.com/D2Jent1.png)

-After successfull execution of the command it will genrate the ` picorv32a.cts.def`, which will be used for power planning and furthur steps. 

![image](https://i.imgur.com/yjSoc8c.png)

 - ![image](https://i.imgur.com/2406XPs.png)

## Lab steps to verify CTS runs<a name="lab-steps-to-verify-cts-runs"></a>
- we are using all these atomic commands like `run_synthesis` ,`run_floorplan` and etc.., are these are stores in `/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands
`. This folder contains.

![image](https://i.imgur.com/3JIuJ5w.png)

- 
Let us look in to the `cts.tcl`.

![image](https://i.imgur.com/pRrB75X.png)
![image](https://i.imgur.com/WTDfzFl.png)

- The openroad contain these files, observe that it does not contain a synthesis file.
![image](https://i.imgur.com/vehKxRz.png)
- To create a database in OPENROAD using LEF and TMP files, follow these steps:

1.Ensure that you are in the directory containing the LEF and TMP files.

2.Launch the OPENROAD tool by entering the command:
    `openroad` inside the openlane flor
    
![image](https://i.imgur.com/x77PRkq.png)

- 1.Once inside the OPENROAD tool, execute the following commands to read the LEF and DEF files:

``` 
read_lef /openLANE_flow/designs/picorv32a/runs/02-04_05-27/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/02-04_05-27/results/cts/picorv32a.cts.def
```
 
- To create the OpenROAD database file named pico_cts.db, use the command:
`write_db pico_cts.db`
 
 ![image](https://i.imgur.com/3AFo9zb.png)
 
 - we have canr read  the `db` file using the command `read_db pico_cts.db`
 
![image](https://i.imgur.com/qnGYJqQ.png)

### To read the netlist post CTS

read_verilog /openLANE_flow/designs/picorv32a/runs/02-04_05-27/results/synthesis/picorv32a.synthesis_cts.v

- To read the library for design `read_liberty $::env(LIB_SYNTH_COMPLETE)`.
- To link the design and library `link_design picorv32a` .
- To read the custom sdc we have created `read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc` .

- To setting all clocks as propagated clocks `set_propagated_clock [all_clocks]` .

- To Generate the custom timing report  `report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4` .
- To exit from the openroad flow to openlane flow `exit` .

![image](https://i.imgur.com/19gNWhW.png)

![image](https://i.imgur.com/f2pRlLc.png)

![image](https://i.imgur.com/Y8P4v4e.png)

- The results of the report

![image](https://i.imgur.com/Ka9jOr2.png)

![image](https://i.imgur.com/TejPGZ6.png)

## Lab steps to execute OpenSTA with right timing libraries and CTS assignment<a name="lab-steps-to-execute-opensta-with-right-timing-libraries-and-cts-assignment"></a>
- To remove sky130_fd_sc_hd__clkbuf_1 from the list 
`set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]`

- To check the current value of CTS_CLK_BUFFER_LIST 
`echo $::env(CTS_CLK_BUFFER_LIST)`

- To check the current value of CURRENT_DEF
`echo $::env(CURRENT_DEF)`
- To set def as placement def
`set ::env(CURRENT_DEF) /openLANE_flow/designs/picorv32a/runs/02-04_05-27/results/placement/picorv32a.placement.def`

- To run cts
`run_cts`

To check the current value of CTS_CLK_BUFFER_LIST 
`echo $::env(CTS_CLK_BUFFER_LIST)`
## Lab steps to observe impact of bigger CTS buffers on setup and hold timing

- Now we will follow the same commands we have used earlier to run OPENROAD,
```
openroad
read_lef /openLANE_flow/designs/picorv32a/runs/02-04_05-27/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/02-04_05-27/results/cts/picorv32a.cts.def
write_db pico_cts1.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/02-04_05-27/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4
report_clock_skew -hold
report_clock_skew -setup
exit
```
![image](https://i.imgur.com/eIFOV6r.png)

![image](https://i.imgur.com/F5Ayrob.png)

![image](https://i.imgur.com/9DHZhLI.png)

![image](https://i.imgur.com/Le15dj4.png)

![image](https://i.imgur.com/2JbLDW3.png)

![image](https://i.imgur.com/mXUop7a.png)

![image](https://i.imgur.com/VMgzImq.png)

![image](https://i.imgur.com/gvzYZsX.png)

![image](https://i.imgur.com/OfUSiji.png)


# Day 5 <a name="day5"></a>
## Final step for RTL2GDS using tritinRoute and openSTA<a name="Final-step-for-rtltogds2"></a>
 - To contiue we need to open openlane, and prep the previous run without overwriting the config file
 
```
docker

./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a -tag 01-04_12-54
echo $::env(CURRENT_DEF)

```

![image](https://i.imgur.com/zlsIN0C.png)
- Before procceding to the routing , we need to generate the power distribution network by executing the command `gen_pdn`

![image](https://i.imgur.com/USPawkm.png)

![image](https://i.imgur.com/wFTnB9Q.png)

- After generating the power distribution network, we can procced to routing , by executing the command `run_routing`. This process takes some time.

![image](https://i.imgur.com/PZHP3u0.png)

![image](https://i.imgur.com/zPNmFMs.png)

- You observe that routing takes aroung 19 minutes to complete, the result's of the routing are stored in the directory 

`/home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/01-04_12-54/results/routing
`
![image](https://i.imgur.com/iC53vkK.png)

- Sceern shot after the routing.

![image](https://i.imgur.com/Ny9aZwc.png)
