# FlexRIC BubbleRAN

<a href="https://releases.ubuntu.com/20.04/"><img src="https://img.shields.io/badge/Platform-BubbleRAN-blue" alt="Supported OS Ubuntu 20"></a>
<a href="https://releases.ubuntu.com/20.04/"><img src="https://img.shields.io/badge/OS-Ubuntu20.04-Green" alt="Supported Platform BubbleRAN"></a>




- [FlexRIC BubbleRAN](#flexric-bubbleran)
- [1. Introduction](#1-introduction)
- [2. Installation](#2-installation)
- [3. Configure the RIC IP address](#3-configure-the-ric-ip-address)
- [4. Run the FlexRIC](#4-run-the-flexric)

# 1. Introduction
In this tutorial, the FlexRIC was installed through a snap package provided by BubbleRAN team.

# 2. Installation

1. Download the [snap package](https://drive.google.com/file/d/1RCkfhLVp8H9zS2chtVkXXUAsEpzCA_xm/view?usp=sharing).

2. Install FlexRIC on BubbleRAN:
   1. Install the snap package on the `PATH: {file path}` location
        ```bash
        sudo snap install --dangerous ./flexric.snap
        ```

        **Output**:
        ```console
        flexric v4.1.0-master installed
        ```

    2. Connect the snap package:
        ```bash
        readarray -t list <<<$(snap connections flexric | awk '{print$2}' | tail -n+2)
        for plug in "${list[@]}"; do
            sudo snap connect $plug
            echo "Plug $plug has been connected."
        done
        ```

        **Output**:
        ```console
        Plug flexric:cpu-control has been connected.
        Plug flexric:netlink-connector has been connected.
        Plug flexric:network-bind has been connected.
        Plug flexric:network-control has been connected.
        Plug flexric:process-control has been connected.
        ```

    3. Run initial configuration:
        ```bash
        sudo flexric.init ric
        ```

        **Output**:
        ```console
        Tue, 07 Nov 2023 22:47:20 +0800: [INF] Initializing the environment, as well as RT and Near-RT RIC.
        Tue, 07 Nov 2023 22:47:20 +0800: [INF] Config File:                 /var/snap/flexric/common/conf/ric.conf
        Tue, 07 Nov 2023 22:47:20 +0800: [INF] Near RIC IP Address:         127.3.1.62
        Tue, 07 Nov 2023 22:47:20 +0800: [SUC] flexric.ric init is done.
        ```

# 3. Configure the RIC IP address
> [!NOTE]
> Highlights information that users should take into account, even when skimming.

**Command**:
```bash
sudo flexric.conf edit
```

**File Content**:
```shell
1 SM_DIR = "/snap/flexric/current/lib/flexric/"
2 
3 Name = "NearRT_RIC"
4 NearRT_RIC_IP = "192.168.8.48" # Update this IP address.
5 E2_Port = 36421
6 E42_Port = 36422
~
~
```

Press `:x` + Enter to save from Vim editor.

# 4. Run the FlexRIC
```bash
sudo flexric.ric
```
    
**Output**:
```console
Tue, 07 Nov 2023 22:56:20 +0800: [SUC] Plug network-control is connected.
Tue, 07 Nov 2023 22:56:20 +0800: [SUC] Plug process-control is connected.
Tue, 07 Nov 2023 22:56:20 +0800: [INF] Running ric as /snap/flexric/x1/ric/ric -c /var/snap/flexric/common/conf/ric.conf 
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
