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