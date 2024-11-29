# crio_acquisition
PAMGuard data acquisition from National Instruments crio device

The project has three parts, all of which need to work together to acquire 
audio data from Natoinal Instruments CRio chassis such as
the [CRio 9063](https://www.ni.com/en-gb/shop/model/crio-9063.html?srsltid=AfmBOooRidjGu2tijWGxxd-VjpokSTyN8vtNZgq45rqwJvayhoVyYeN4)
[CRio 9067](https://www.ni.com/en-gb/shop/model/crio-9067.html?srsltid=AfmBOooDl_wena_5vqRzmayb_Y_s5iTPKoPyx8wyZfjPkojd_0mdm7Fr)
and the
[CRio 9068](https://www.ni.com/en-gb/shop/model/crio-9068.html?srsltid=AfmBOopb9mQXtb1UeGcc7YlXIJsJZhzPp5V88Z2py--iy7sWA6-_uZ-V)
as used in the following studies:


Gillespie, Douglas, Gordon Hastie, Jessica Montabaranom, Emma Longden, Katie Rapson, Anhelina Holoborodko, and Carol Sparling. "Automated detection and tracking of marine mammals in the vicinity of tidal turbines using multibeam sonar." Journal of Marine Science and Engineering 11, no. 11 (2023): 2095.

Gillespie, Douglas, Michael Oswald, Gordon Hastie, and Carol Sparling. "Marine mammal HiCUP: A high current underwater platform for the long-term monitoring of fine-scale marine mammal behavior around tidal turbines." Frontiers in Marine Science 9 (2022): 850446.

Macaulay, Jamie, Jonathan Gordon, Douglas Gillespie, Chloë Malinka, and Simon Northridge. "Passive acoustic methods for fine-scale tracking of harbour porpoises in tidal rapids." The Journal of the Acoustical Society of America 141, no. 2 (2017): 1120-1132.

The code falls into three parts. The Java and C can generally be built with free tools, but 
the FPGA code requires [Labview](https://www.ni.com/en/shop/labview.html) 
from NI, which can be quite expensive. FPGA bitcodes for the devices and numbers of
channels that we've used are available, but to build for other devices, you'll
need Labview.

## FPGA Code
In folder LabView8ch

Requires [Labview](https://www.ni.com/en/shop/labview.html)  to build.
FPGA model that can be built and uploaded to the CRio. The CCode on the CRio (see below) 
will attempt to select the correct model for the number of channels and device
you are using. 

The FPGA code acquired data from the CRio modules and places it in a ring buffer 
read by the CRio C Code. 


##  CRio C/C++ Code
In folder cRio_DAQ

Should be possible to buile this with free [C/C++ development tools](https://knowledge.ni.com/KnowledgeArticleDetails?id=kA03q000000YHR7CAO&l=en-GB) from NI

An embedded C program that runs on the CRio ARM processor. It starts to FPGA, then
reads data from the ring buffer
then compresses it using the [X3 algorithm](https://doi.org/10.1121/1.4776206) and 
sends it to PAMGuard over an Ethernet connection using TCP. The C is all controlled
from within PAMGuard using a shell library, mostly using UDP based commands.

An earlier version recorded data to wav files on the CRio drive, though that code would
need reinstating. 

## PAMGuard DAQ Plugin
In folder PAMGuard_DAQ_Plugin

Pure Java. 
Can be built with [Eclipse](https://www.eclipse.org/) free IDE. 

This is an acquisition plugin module for [PAMGuard](www.pamguard.org) which 
constrols the C program on the CRio, recieved data from the CRio on a TCP
socket, unpacks the compressed data, and inserts it into the PAMGuard
processing chain. 


