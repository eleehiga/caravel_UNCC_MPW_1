# Gonzaga University EENG406 Analog Neural Network project 
## Project information
* by Ethan Higa, Chris Chock, Matthew Gauss, Carter Roth, Elwyn Frailey
* Teacher: Claudio Talarico
## Important Links
1. https://github.com/iic-jku/iic-osic-tools: GitHub for the docker installation  
2. https://github.com/Bryce-Readyhough/caravel_UNCC_MPW_1: GitHub for the neural network ngspice simulation 
3. https://ieeexplore.ieee.org/document/9184447: IEEE paper code with theory and results we are trying to replicate

## Installing Docker and Using docker:
1. Download and install GitHub desktop. 
   * https://docs.github.com/en/desktop/installing-and-configuring-github-desktop/installing-and-authenticating-to-github-desktop/installing-github-desktop 
   * Follow instructions on the website. 
   * A GitHub account is necessary to download the GitHub desktop. 
2. Download and install docker desktop: 
   * https://www.docker.com/products/docker-desktop/ 
3. Clone the GitHub repository to your host computer from: 
   * https://github.com/iic-jku/iic-osic-tools 
   * This will have ngspice and other useful programs. 
   
      a. Open GitHub Desktop 
  
      b. To clone from the internet, click on current repository box in the top left. 
      
      c. Hit add and select clone repository 
      
      d. Hit URL and past the GitHub link under Repository URL 
      
      e. Before you hit the blue cone button, select the folder you want the repository saved to (know where you saved this because we will grab it later) 
  
4. Installing VcXsrc 
   * https://github.com/iic-jku/iic-osic-tools#installing-x11-server

      a. Click on the blue link VcXsrc next to For Windows (There is also a link for Mac) 
  
      b. Download Vcxsrv which will install Xlaunch (for more details and instructions read the GitHub) 
  
5. Open XLaunch 

   a. Select `Multiple Windows` and `Display Number = 0` and then `Next` 
  
   b. `Start no Client` and then `Next` 
  
   c. Check all boxes and then `Next` 
  
   d. `Finish`
  
   * XLaunch should be running now (click the ^ in the bottom right of your desktop to see that the XLaunch icon is present. If so, it is running) 
6. Enter the downloaded docker tools Git repository from the command prompt 

   a. Open your computers Command Prompt (type Command Prompt in computer’s search bar to find it) 
  
   b. Cd to the iic-osic-tools folder (use dir command to see what directories you can go in) 
  
   c. Enter the command `.\start_x.bat` 
  
   d. After it installs, enter command `docker start iic-osic-tools_xserver` (enter in the command prompt window not the new terminal that pops up). 
  
7. Cloning repository into the docker 

   a. In the container enter the command `git clone  https://github.com/Bryce-Readyhough/caravel_UNCC_MPW_1` 
  
   * The screenshot below is the correct terminal. 
   * When the terminal opens, the current directory should be /foss/designs. 
   ![](https://github.com/eleehiga/nn_for_iic_tools/blob/mpw-one-b-merged/pictures/foss_designs.png)
8. Uncompressing data and displaying circuit in magic 

   a. Cd to caravel_UNCC_MPW_1 and run `make uncompress -j$nproc` 
  
   b. Cd mag 
  
   c. Ls 
  
   d. Use command ‘magic user_project_wrapper.mag’ 
  
   e. Magic should pop up 
  
##  Running Spice code
1. Pathing to the directory  

   a. Be in the /foss/designs directory in the docker.
  
   b. cd to ` caravel_UNCC_MPW_1/mag` folder 
  
2. Editing the spice file 

   a. Before running the spice code enter command `nano user_project_wrapper.spice 
  ` 
   b. Add the following lines of code under the .include lines:  
  
     ```
     .param mc_mm_switch=0 
     .param mc_pr_switch=1  
     ```
   c. Comment out all the includes with `*`
  
   d. Then enter in these lines (which are the correct includes for the docker) 
  
     ```
     .include"/foss/pdks/sky130A/libs.tech/ngspice/r+c/res_typical__cap_typical__lin.spice" 
     .include "/foss/pdks/sky130A/libs.tech/ngspice/r+c/res_typical__cap_typical.spice" 
     .include "/foss/pdks/sky130A/libs.tech/ngspice/corners/tt.spice"  
     ```
     
   e. Remove `v("analog_io[15]")` in the plot command 
  
   * Just unnecessary code
   * (if using nano, you can ctrl W to search code) 
  
   f. To leave nano use `ctrl X` the hit `y` and then enter
  
   * `y` is yes to save 
  
   g. Enter the command `ngspice user_project_wrapper.spice`
  ` 
   h. The graph should look like fig 6. (a) in the IEEE paper https://ieeexplore.ieee.org/document/9184447 
  
## Running the Docker post setup

a. Launch XLaunch (might auto open on computer start up) 

   * Reference step 6. under “Installing Docker and Using docker” to fix settings if they did not save 
  
b. Launch Docker Desktop 

c. Press the play button as shown  

   ![](https://github.com/eleehiga/nn_for_iic_tools/blob/mpw-one-b-merged/pictures/docker.png)
   * a terminal should appear 
   * the current directory should be /foss/designs
## Outputs
![](https://github.com/eleehiga/nn_for_iic_tools/blob/mpw-one-b-merged/pictures/RS_inv.png)
To have the above output in the `mag` directory and enter `ngspice user_project_wrapper.spice`. This regular spiking pattern is created as the capacitor `Ca` is charged and then discharged. The control voltages are set as: 
```
Vdd vdda1 gnd DC 0.7V 
Vgnd vssa1 gnd DC 0.0V 
Vth analog_io[10] gnd DC 0.1V 
Vk analog_io[9] gnd DC 0.15V 
Vw analog_io[11] gnd DC 0.18V 
Vr analog_io[8] gnd DC 0.25V 
Vau analog_io[12] gnd DC 0.7V 
Vad analog_io[7] gnd DC 0.0V 
```
![](https://github.com/eleehiga/nn_for_iic_tools/blob/mpw-one-b-merged/pictures/CH_inv.png)
To have the above output in the `mag` directory enter `ngspice neuron-test.spice`. This chattering pattern is created as the IEEE document said by “manipulating the control voltages” in which there will be periodic bursts of the neuron. The control voltages are set as: 
```
Vdd vpwr gnd DC 0.7V 
Vgnd vgnd gnd DC 0.0V 
Vth vth gnd DC 0.1V 
Vk vk gnd DC 0.15V 
Vw vw gnd DC 0.01V 
Vr vr gnd DC 0.37V 
Vau vau gnd DC 0.08V 
Vad vad gnd DC 0.25V 
```
![](https://github.com/eleehiga/nn_for_iic_tools/blob/mpw-one-b-merged/pictures/RS_sub_inv.png)
Above is the output of entering `ngspice user_project_wrapper.spice` but with changing `Vdd` as `0.635V` below the original `0.7V`. As shown, there is not enough voltage to support the spiking as shown by the flat spikes. 

# Based off of: Analog Spiking Neuron Circuit and 10-bit Digital to Analog Converter - Caravel Submission 

## Analog Spiking Neuron Circuit

This is the Google/EFabless/Skywater Caravel submission of an [Analog Spiking Neuron Circuit.](https://ieeexplore.ieee.org/document/9184447) The submission also includes a SONOS transistor array. 

### Neuron circuit
The circuit in the original paper is in 130nm technology and has a vdd of 300mV. Skywater pdk is hybrid 180nm/130nm node where the minimum transistor length is 150nm.
As a result vdd needs to be higher in order to get the circuit to work properly. In simulation 700mV seems to work well. 

<p align=”center”>
<img src="/doc/neuron.png" width="75%"> 
</p>

#### Pinout
| node   | pad         | node      | pad         | node        | pad         |
|--------|-------------|-----------|-------------|-------------|-------------|
| i_bias | mprj_io[13]  | v_buff    | mprj_io[22] | vdd        |   vdda1     |
| vad    | mprj_io[14]  | u_buff    | mprj_io[23] | vss        |   vssa1     |
| vr     | mprj_io[15]  | a_buff    | mprj_io[24] | vdd_aux    |   vdda2     |
| vk     | mprj_io[16] | axon_buff | mprj_io[25] |
| vth    | mprj_io[17] | sel       | mprj_io[26] |
| vw     | mprj_io[18] | v_syn     | mprj_io[27] |
| vau    | mprj_io[19] | u_syn     | mprj_io[28] |
| vsyn0  | mprj_io[20] | a_syn     | mprj_io[29] |
| vsyn1  | mprj_io[21] | axon_syn  | mprj_io[30] |
|        |             | i_in      | mprj_io[31] |

#### Extracted Netlist Simulation 
Spiking pattern at v_buff for i_in = 10pA DC 
<p align=”center”>
<img src="/doc/sample_sim.png" width="75%"> 
</p>

### SONOS array
A 2x2 array of nfet sonos cells with transistor sizing 420nmx150nm.
<p align=”center”>
<img src="/doc/sonos.png" width="75%"> 
</p>

#### Pinout
| node | pad         |
|------|-------------|
| WL0  | mprj_io[7]  |
| WL1  | mprj_io[8]  |
| BL0  | mprj_io[9]  |
| SL0  | mprj_io[10] |
| BL1  | mprj_io[11] |
| SL1  | mprj_io[12] |



## 10-bit Digital to Analog Converter

The mega project area also contains a 10-bit Potentiometric Digital to Analog Converter built off of [Ashutosh Sharma's design.](https://github.com/xzlashutosh/avsddac_3v3) The design uses a 3.3V rail voltage, and 1.8V digital input voltage. The DAC's inputs are controlled by the Caravel harness' built in RISC-V core. [Ashutosh Sharma's design](https://github.com/xzlashutosh/avsddac_3v3) used the OSU180 process, for the caravel submission it was ported to the Sky130 process. 

The design uses a string of polysilicon resistors in series to create a string DAC. The resistors are connected to digital switches in order to achieve an exact voltage at the output. The device was built through hierarchical sub-circuits and sub-layouts starting off at 2-bit, then 3-bit and so forth, incrementally reaching 10-bit. With a full scale of 3.3 V and a 10 bit resolution. 

## Pinout

| Node          | Pad           |
| :------------ | ------------- |
| Analog_Out    | analog_io[25] |
| Digital_In[0] | io_in[0]      |
| Digital_In[1] | io_in[1]      |
| Digital_In[2] | io_in[2]      |
| Digital_In[3] | io_in[3]      |
| Digital_In[4] | io_in[4]      |
| Digital_In[5] | io_in[5]      |
| Digital_In[6] | io_in[6]      |
| Digital_In[7] | io_in[7]      |
| Digital_In[8] | io_in[8]      |
| Digital_In[9] | io_in[9]      |
| VDD           | vdda1         |
| Gnd           | vssa1         |

#### Simplified DAC Architecture 
String of resistors and switches that make up potentiometric DAC.
<p align=”center”>
<img src="/doc/circuit_DAC.png" width="45%"> 
</p>

#### Abstracted DAC Block Diagram 
Simple block representing all inputs and outputs of DAC.
<p align=”center”>
<img src="/doc/abstract_block_diagram.png" width="45%"> 
</p>
