FlexRIC and OSC O-DU L2
===


<a href="https://releases.ubuntu.com/20.04/"><img src="https://img.shields.io/badge/OS-Ubuntu20.04-purple" alt="Supported Platform BubbleRAN"></a>

<a href="https://bubbleran.com/docs/category/open-ran-studio"><img src="https://img.shields.io/badge/Platform-BubbleRAN ORS-blue" alt="Supported OS Ubuntu 20"></a>
<a href="https://drive.google.com/file/d/1RCkfhLVp8H9zS2chtVkXXUAsEpzCA_xm/view?usp=sharing"><img src="https://img.shields.io/badge/Comp.-FlexRIC-blue" alt="Supported OS Ubuntu 20"></a>
<a href="https://gitlab.eurecom.fr/br/flexric/-/blob/odu/examples/xApp/c/monitor/xapp_oran_moni.c?ref_type=heads"><img src="https://img.shields.io/badge/Comp.-xAPP-blue" alt="Supported OS Ubuntu 20"></a>

<a href="https://github.com/NTUST-BMW-Lab/odu-e2sm/tree/multi-UE"><img src="https://img.shields.io/badge/Comp.-OSC ODU L2-red" ></a>

- [FlexRIC BubbleRAN + OSC O-DU L2](#flexric-bubbleran--osc-o-du-l2)
- [1. Introduction](#1-introduction)
  - [1.1 System Architecture](#11-system-architecture)
  - [1.2 IP Tables](#12-ip-tables)
- [2. Installations](#2-installations)
- [3. IP Address Configurations](#3-ip-address-configurations)
- [Error](#error)

# 1. Introduction
This tutorial shows how to install and integrate the FlexRIC on BubbleRAN with OSC O-DU and O-CU Stub on a bare-metal server. The detail of the architecture can be seen on [Sect. 1.1](#11-system-architecture).


## 1.1 System Architecture
![](../assets/System%20Architecture.jpg)

## 1.2 IP Tables

| <center>Instance</center> | <center>IP Address</center> |
|---------------------------|-----------------------------|
| OSC O-DU                  | 192.168.8.22                |
| OSC O-CU                  | 192.168.8.30                |
| FlexRIC Near-RT RIC       | 192.168.8.48                |
| FlexRIC xAPP              | 192.168.8.118               |

# 2. Installations

1. [OSC O-DU L2](https://github.com/NTUST-BMW-Lab/docs/blob/706b9ff9f32c2fc0a20bfdcf91d0d7f2bc960057/O-RAN/OSC/installations/O-DU/G%20Release/osc-odu-l2.md#1-installation) (Step 1)
2. [BubbleRAN FlexRIC](../installations/flexric-bubbleran.md#2-installation) (Step 2)
3. [xAPP](../installations/KPM%20xAPP.md#1-installations) (Step 1)

# 3. IP Address Configurations

1. [OSC O-DU L2](https://github.com/NTUST-BMW-Lab/docs/blob/706b9ff9f32c2fc0a20bfdcf91d0d7f2bc960057/O-RAN/OSC/installations/O-DU/G%20Release/osc-odu-l2.md#2-configuration)
2. [BubbleRAN FlexRIC](../installations/flexric-bubbleran.md#3-configure-the-ric-ip-address)
3. [xAPP](../installations/KPM%20xAPP.md)

# Error
The FlexRIC has errorno = 99 when running:

```console
Thu, 09 Nov 2023 07:46:58 +0800: [INF] Running ric as /snap/flexric/x2/ric/ric -c /var/snap/flexric/common/conf/ric.conf 
Setting the config -c file to /var/snap/flexric/common/conf/ric.conf
[LibConf]: loading service models from SM_DIR: /snap/flexric/current/lib/flexric/
[LibConf]: reading configuration for NearRT_RIC
[LibConf]: NearRT_RIC IP: 192.168.8.48
[LibConf]: E2_Port Port: 36421
[LibConf]: E42_Port Port: 36422
[NEAR-RIC]: nearRT-RIC IP Address = 192.168.8.48, PORT = 36421
errno = 99
[NEAR-RIC]: Initializing 
[NEAR-RIC]: Loading SM ID = 148 with def = GTP_STATS_V0 
[NEAR-RIC]: Loading SM ID = 2 with def = ORAN-E2SM-KPM 
[NEAR-RIC]: Loading SM ID = 142 with def = MAC_STATS_V0 
[NEAR-RIC]: Loading SM ID = 144 with def = PDCP_STATS_V0 
[NEAR-RIC]: Loading SM ID = 3 with def = ORAN-E2SM-RC 
[NEAR-RIC]: Loading SM ID = 143 with def = RLC_STATS_V0 
[NEAR-RIC]: Loading SM ID = 145 with def = SLICE_STATS_V0 
[NEAR-RIC]: Loading SM ID = 146 with def = TC_STATS_V0 
[iApp]: Initializing ... 
[iApp]: nearRT-RIC IP Address = 192.168.8.48, PORT = 36422
errno = 99
fd created with 6 
[NEAR-RIC]: Initializing Task Manager with 2 threads 
```
