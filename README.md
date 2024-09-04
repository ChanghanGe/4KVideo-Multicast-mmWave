# ICDCS24 4K Video Multicast via  mmWave Souce Code
Source Code of "Optimized Live 4K Video Multicast Streaming on Commodity WiGig Devices" (IEEE ICDCS 2024)

#### PI: [Dr. Lili Qiu](https://www.cs.utexas.edu/~lili/) @ The University of Texas at Austin & Microsoft Research Asia Shanghai <br>
#### Developer: [Zhaoyuan He](https://gavinsyw.github.io/), [Changhan Ge](https://changhange.github.io/), Wangyang Li, Ghufran Baig @ The University of Texas at Austin, Peijie Li @ Google

Abstract: The popularity of 4K videos is on the rise. However, streaming such high-quality videos over mm Wave to several users presents significant challenges due to directional communication, fluctuating channels, and high bandwidth demands. To address these challenges, this paper introduces an innovative 4K layered video multicast streaming system. We (i) develop a video quality model tailored for layered video coding, (ii) optimize resource allocation, scheduling, and beamforming based on the channel conditions of different users, and (iii) design a streaming strategy that integrates fountain code to eliminate redundancy in multicast groups, coupled with a Leaky-Bucket approach for congestion control. We implement our system on Commodity-Off- The-Shelf (COTS) WiGig devices and demonstrate its effectiveness through comprehensive testbed and emulation experiments. <br>

Read our paper [here](https://ieeexplore.ieee.org/abstract/document/10631023)

# Warning & Disclaimer
Hardware modifications in this design will void your devices' warranty. The code requires root access to the operating system and will hijack the network interface, which may cause data leakage and irreversible damage to your OS and hardware. Users try our code at their own risk and responsibility. Meanwhile, we are not responsible for problems caused by third party software involved in our code.

# Emulation

### Generate mmWave CSI trace using Wireless Insite
* Wireless Insite version tested: v3.4.4.14
* A valid Wireless Insite license (with X3D feature) is required. Please contact [Remcom](https://www.remcom.com/wireless-insite-em-propagation-software) for details.

# Testbed 
### Equipments
* Devices: 4 Acer TravelMate P658-m, one serving as AP (TX) and the other 3 serving as STAs (RX).
* Network Card: [NGFF595A-L](https://lian-mueller.com/qualcomm-atheros-communications-distributor/ngff595a-l.html) Qualcomm Atheros QCA6320-based 802.11ad WiGig Baseband M.2 Module (Sparrrow).
  + Qualcomm Atheros QCA6335 based [NGFF695A-L](https://lian-mueller.com/qualcomm-atheros-communications-distributor/ngff695a-l.html) (Sparrow Plus) in [M3](http://m3.ucsd.edu/sdr/) should also work but we never tried.
* Phased Array: Qualcomm Atheros QCA6310-based 802.11ad WiGig RF Antenna Module (32-antenna URA, the same one as the single antenna array module used in [M3](http://m3.ucsd.edu/sdr/) and [MultiLoc](https://dl.acm.org/doi/pdf/10.1145/3498361.3538920)).
  + Acer TravelMate P658-m orginally comes with [NGFF595A-L-Ant](https://lian-mueller.com/qualcomm-atheros-communications-distributor/ngff595a-l-ant.html) (32-antenna irregular array, the same one used in [ACO](https://dl.acm.org/doi/abs/10.1145/3241539.3241576)). We replaced the phased array with a 32-ant URA for a better evaulation. 
  + Our approach should be able to work with any phased array, including NGFF595A-L-Ant, since it does not consider any antenna structure.

### Dependencies
* OS tested: Ubuntu 16.04/18.04/20.04 (Ubuntu 20.04 will need to install python2 to support the legacy features).
* ```OpenSSH``` server/client and ```sshpass``` required on both machines.
* Python 3.8+ environment required, need to install numpy and scipy packages.
* Software required: Matlab (tested with 2021a, 2021b, and 2022a)
  + A valid matlab license is required.
  + require signal processing and phased array toolboxes
  + require matlab engine for python, detailed installation instruction can be found [here](https://www.mathworks.com/help/matlab/matlab_external/install-the-matlab-engine-for-python.html)
* A custom patched wil6210 firmware (driver of 802.11ad card) for ```wil6210.fw``` version 6.2.0.40 and a phased-array controller ```wil6210_server-2.2.0``` server are required, which are the same ones used in [X-array](https://dl.acm.org/doi/10.1145/3372224.3380882). These two modules togehter enable dumping received signal strength (RSS) from network interface. Meanwhile, a codebook module ```wil6210_brd_mod``` is needed to generate beamforming codebook. We are not the author of these third-party software and do not have the permission to release them. User have to request them from X-array.
  + Note that the "wil6210-server" used in [M3](https://github.com/RenjieZhao/M-Cube-Hostcmds/tree/main) does not work for our code since it does not support RSS dumping. The server should include ```per_beam_snr``` module.
  + Note that the patched firmware "wil6210.fw" used in [ACO](https://dl.acm.org/doi/abs/10.1145/3241539.3241576) does not work for our code.
* Your AP and STA should be connected via either ethernet port or sub-6 WiFi link, and the corresponding network interfaces should have IPv4 addresses under the same subnet, say ```AP: 192.168.0.10/24, STA: 192.168.0.66/24```.

#### Run with our codebook
* Make sure you already calibrate your experiment settings following the preliminary.
* Install all the dependencies on your AP and STA machine.
* Clone this repo to your STA machine by
  ```
  git clone git@github.com:ChanghanGe/4KVideo-Multicast-mmWave.git
  cd 4KVideo-Multicast-mmWave
  ```
* You have to figure out the name of your WiGig interface first. You can check all your network interface by hit ```ifconfig -a```. In our experiment, the name of the WiGig interface is ```wlp3s0```. It varies by machine. If your WiGig interface is NOT ```wlp3s0```, you have to change it in ```./firmware/interfaces_mmwave_AP```,  ```./main/main.py``` and ```./firmware/load_csi_firmware.sh```
* One of your machine have to be on AP mode. Normally wireless interfaces of a linux machine are set as STA mode by default. To make it as an AP, run
  ```
  sudo cp ./firmware/ap_mmwave.conf /etc/wpa_supplicant/
  sudo cp ./firmware/interfaces_mmwave_AP /etc/network/interfaces
  ```
  Then reboot your AP. It should then work at channel 2 of 802.11ad (60.48GHz). This code works for linux kernel version 4.15.0-142.
  If the rebooting does not make your laptop an AP, please ask ChatGPT or Stackoverflow.
* Put the ```wil6210_brd_mod``` under the ```./codebook/``` folder, put the patched ```wil6210.fw``` under the ```./firmware/patched_fw/``` folder and put the patched ```wil6210_server-2.2.0``` under the ```./firmware/``` folder. Then run the following commands.
  ```
  chmod a+x ./codebook/wil6210_brd_mod
  chmod a+x ./firmware/patched_fw/wil6210.fw
  chmod a+x ./firmware/wil6210_server-2.2.0

