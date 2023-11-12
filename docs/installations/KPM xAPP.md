KPM xAPP
===

<a href="https://releases.ubuntu.com/20.04/"><img src="https://img.shields.io/badge/OS-Ubuntu20.04-purple" alt="Supported Platform BubbleRAN"></a>

<a href="https://bubbleran.com/docs/category/open-ran-studio"><img src="https://img.shields.io/badge/Platform-BubbleRAN ORS-blue" alt="Supported OS Ubuntu 20"></a>
<a href="https://gitlab.eurecom.fr/br/flexric/-/blob/odu/examples/xApp/c/monitor/xapp_oran_moni.c?ref_type=heads"><img src="https://img.shields.io/badge/Comp.-xAPP-blue" alt="Supported OS Ubuntu 20"></a>


TOC:
- [KPM xAPP](#kpm-xapp)
- [1. Installations](#1-installations)
  - [1.1 Install Prerequisites](#11-install-prerequisites)
  - [1.2 Install FlexRIC](#12-install-flexric)
- [2. Configure FlexRIC](#2-configure-flexric)
- [3. Run Near-RT RIC](#3-run-near-rt-ric)

# 1. Installations

## 1.1 Install Prerequisites
> **Note**: please run it as "root" superuser

1. Run it "root" superuser mode:
    ```bash
    sudo -i
    ```

    This command will make the terminal symbol from "$" into "#" like below
    ```console
    root@<server>~#
    ```

2. Check the Ubuntu Version:
    
    **Note**: In this guide, we're using Ubuntu 20.04

    ```bash
    lsb_release -a
    ```

    **Output**
    ```console
    No LSB modules are available.
    Distributor ID:	Ubuntu
    Description:	Ubuntu 20.04.6 LTS
    Release:	20.04
    Codename:	focal
    ```

3. Use GCC v10 above
    > [!WARNING]
    > Don't use GCC v11

    > [!NOTE]
    > In this tutorial, we use GCC v10

    ```bash
    sudo apt update
    sudo apt install build-essential
    sudo apt-get install manpages-dev
    
    # Install GCC v10
    sudo apt install g++-10 gcc-10
    sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 60 --slave /usr/bin/g++ g++ /usr/bin/g++-10 --slave /usr/bin/gcov gcov /usr/bin/gcov-10

    # Verify version
    gcc --version
    ```

    **Output**:
    ```bash
    gcc (Ubuntu 10.5.0-1ubuntu1~20.04) 10.5.0
    Copyright (C) 2020 Free Software Foundation, Inc.
    This is free software; see the source for copying conditions.  There is NO
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
    ```

4. CMake (v3.22 above). 

    On Ubuntu, you might want to use [this PPA](./dependencies/Kitware%20APT.md) to install an up-to-date version.

    ```bash
    wget http://www.cmake.org/files/v3.24/cmake-3.24.2.tar.gz
    tar xf cmake-3.24.2.tar.gz
    cd cmake-3.24.2
    ./configure
    make
    sudo make install

    # Verify version
    cmake -version
    ```

    **Output**:

    ```bash
    cmake version 3.28.0-rc1

    CMake suite maintained and supported by Kitware (kitware.com/cmake).
    ```

5. SWIG (at least  v.4.1). 

    We use SWIG as an interface generator to enable the multi-language feature (i.e., C/C++ and Python) for the xApps. Please, check your SWIG version (i.e, `swig -version`) and install it from scratch if necessary as described here: https://swig.org/svn.html or via the code below: 

    - Install dependencies:
        ```bash
        sudo apt update
        sudo apt install autotools-dev automake pcre2-utils libpcre2-dev build-essential

        # Verify installation
        sudo apt list autotools-dev automake pcre2-utils libpcre2-dev build-essential
        ```

        **Output**:

        ```console
        automake/focal,focal,now 1:1.16.1-4ubuntu6 all [installed]
        autotools-dev/focal,focal,now 20180224.1 all [installed]
        build-essential/focal-updates,now 12.8ubuntu1.1 amd64 [installed]
        build-essential/focal-updates 12.8ubuntu1.1 i386
        libpcre2-dev/focal-updates,focal-security,now 10.34-7ubuntu0.1 amd64 [installed]
        libpcre2-dev/focal-updates,focal-security 10.34-7ubuntu0.1 i386
        pcre2-utils/focal-updates,focal-security,now 10.34-7ubuntu0.1 amd64 [installed]
        pcre2-utils/focal-updates,focal-security 10.34-7ubuntu0.1 i386
        ```

    - Install SWIG
        ```bash
        git clone https://github.com/swig/swig.git
        cd swig
        git checkout release-4.1
        ./autogen.sh
        ./configure --prefix=/usr/
        make -j8
        make install
        ```

        Check your Swig version
        ```bash
        swig -version
        ```

        **Output**:
        ```bash
        SWIG Version 4.1.1

        Compiled with g++ [x86_64-pc-linux-gnu]

        Configured options: +pcre

        Please see https://www.swig.org for reporting bugs and further information
        ```

6. Flatbuffer encoding (optional). 
  
    We also provide a flatbuffers encoding/decoding scheme as alternative to ASN.1. In case that you want to use it  follow the instructions at https://github.com/dvidelabs/flatcc and provide the path for the lib and include when selecting it at `ccmake ..` from the build directory

7. Install other dependencies

   - **Install common dependencies in Ubuntu:  (at least python3.8)**
        ```bash
        # Python 3.8 is old version, add the "deadsnakes" repository to install.
        sudo add-apt-repository ppa:deadsnakes/ppa

        sudo apt update
        sudo apt install asn1c libsctp-dev python3 cmake-curses-gui python3-dev pkg-config libconfig-dev libconfig++-dev libpython2-dev libssl-dev -y poppler-utils 

        # Verify installation
        sudo apt list asn1c libsctp-dev python3 cmake-curses-gui python3-dev pkg-config libconfig-dev libconfig++-dev libpython2-dev libssl-dev poppler-utils 
        ```

        **Output**
        ```console
        Listing... Done
        asn1c/focal,now 0.9.28+dfsg-3 amd64 [installed]
        cmake-curses-gui/focal-rc,now 3.28.0~rc1-0kitware1ubuntu20.04.1 amd64 [installed]
        cmake-curses-gui/focal 3.24.1-0kitware1ubuntu20.04.1 i386
        libconfig++-dev/focal,now 1.5-0.4build1 amd64 [installed]
        libconfig++-dev/focal 1.5-0.4build1 i386
        libconfig-dev/focal,now 1.5-0.4build1 amd64 [installed]
        libconfig-dev/focal 1.5-0.4build1 i386
        libpython2-dev/focal,now 2.7.17-2ubuntu4 amd64 [installed]
        libpython2-dev/focal 2.7.17-2ubuntu4 i386
        libsctp-dev/focal,now 1.0.18+dfsg-1 amd64 [installed]
        libsctp-dev/focal 1.0.18+dfsg-1 i386
        libssl-dev/focal-updates,focal-security,now 1.1.1f-1ubuntu2.19 amd64 [installed]
        libssl-dev/focal-updates,focal-security 1.1.1f-1ubuntu2.19 i386
        pkg-config/focal,now 0.29.1-0ubuntu4 amd64 [installed]
        pkg-config/focal 0.29.1-0ubuntu4 i386
        poppler-utils/focal-updates,focal-security,now 0.86.1-0ubuntu1.3 amd64 [installed]
        poppler-utils/focal-updates,focal-security 0.86.1-0ubuntu1.3 i386
        python3-dev/focal,now 3.8.2-0ubuntu2 amd64 [installed]
        python3-dev/focal 3.8.2-0ubuntu2 i386
        python3/focal,now 3.8.2-0ubuntu2 amd64 [installed]
        python3/focal 3.8.2-0ubuntu2 i386
        ```

   - **Install MySQL as a storage for xApps:**
        ```bash
        sudo apt update
        sudo apt install libmysqlclient-dev mysql-server

        # Verify installation
        sudo apt list libmysqlclient-dev mysql-server
        ```

        **Output**
        ```console
        Listing... Done
        libmysqlclient-dev/focal-updates,focal-security,now 8.0.34-0ubuntu0.20.04.1 amd64 [installed]
        libmysqlclient-dev/focal-updates,focal-security 8.0.34-0ubuntu0.20.04.1 i386
        mysql-server/focal-updates,focal-updates,focal-security,focal-security,now 8.0.34-0ubuntu0.20.04.1 all [installed]
        ```

   - (Optional) Install websocket for proxy agent:
        ```bash
        sudo apt update
        sudo apt install libwebsockets-dev libjson-c-dev

        # Verify installation
        sudo apt list libwebsockets-dev libjson-c-dev
        ```

        **Output**:
        ```console
        Listing... Done
        libjson-c-dev/focal-updates,focal-security,now 0.13.1+dfsg-7ubuntu0.3 amd64 [installed]
        libjson-c-dev/focal-updates,focal-security 0.13.1+dfsg-7ubuntu0.3 i386
        libwebsockets-dev/focal,now 3.2.1-3 amd64 [installed]
        ```
        
        > Proxy agent has been tested with specific version of `libwebsockets-dev` and `libjson-c-dev`;
        cmake will check for that and promt an error in case the version you are trying to install is not compatible.


8. Install ASN1 from OAI repository:
    - Clone Repository:
        
        ```bash
        git clone https://gitlab.eurecom.fr/oai/openairinterface5g oai && cd oai/cmake_targets/
        git checkout 2023.w16
        ./build_oai -I
        ```

        **Output**:
        ```console
        ...
        Setting up cmake-data (3.28.0~rc2-0kitware1ubuntu20.04.1) ...
        Setting up cmake (3.28.0~rc2-0kitware1ubuntu20.04.1) ...
        Setting up cmake-curses-gui (3.28.0~rc2-0kitware1ubuntu20.04.1) ...

        Installing ASN1. The log file for ASN1 installation is here: /root/flexric-osc/build/oai/cmake_targets/log/asn1c_install_log.txt 

        Installing SIMDE from source without test cases (header files only)
        Cloning into '/tmp/simde'...
        remote: Enumerating objects: 10769, done.
        
        ...
        -- Configuring done (1.3s)
        -- Generating done (2.2s)
        -- Build files have been written to: /root/flexric-osc/build/oai/cmake_targets/ran_build/build
        BUILD SHOULD BE SUCCESSFUL
        ```


9. (Optional) Install Go for xApp:
    
    ```bash
    sudo snap install go --classic --channel=1.19/stable

    # Verify installation
    go version
    ```

    **Output**:
    ```console
    go version go1.19.13 linux/amd64
    ```

## 1.2 Install FlexRIC

1. Run superuser mode:
    ```bash
    sudo -i
    ```

2. Clone the FlexRIC to root folder:
    > [!NOTE]
    > Setup the [GitLab SSH communication](https://docs.gitlab.com/ee/user/ssh.html) to simplify the authentication process.
    
    ```bash
    git clone git@gitlab.eurecom.fr:br/flexric.git flexric-br
    ```

3. Switch to designated branch
    > [!NOTE]
    > In this tutorial, we're using `odu` branch

    ```bash
    cd flexric-br

    git switch odu
    ```
    
    **Output**:
    ```console
    Branch 'odu' set up to track remote branch 'odu' from 'origin'.
    Switched to a new branch 'odu'
    ```

4. Build the NearRT-RIC and xApps components:
    ```bash
    mkdir build && cd build
    cmake ..
    make -j
    ```

    **Output**:
    ```console
    ...
    [100%] Linking C executable test_ag_ric_xapp
    [100%] Built target xapp_kpm_rc
    [100%] Built target test_ag_ric_xapp
    [100%] Linking CXX shared module _xapp_sdk.so
    [100%] Built target xapp_sdk
    ```

    After the build process, `./build` directory will have these components:
    ```bash
    ls
    ```

    **Output**:
    ```console
    CMakeCache.txt  CMakeFiles  CTestTestfile.cmake  Makefile  cmake_install.cmake  examples  src  test
    ```

5. Install Near-RT RIC :
    ```bash
    sudo make install
    ```
    
    **Output**:
    ```console
    ...
    -- Installing: /usr/local/lib/python3/dist-packages/monitor/xapp_oran_moni.py
    -- Installing: /usr/local/lib/python3/dist-packages/monitor/xapp_cust_moni.py
    -- Installing: /usr/local/lib/python3/dist-packages/control/xapp_slice_moni_ctrl.py
    -- Installing: /usr/local/lib/python3/dist-packages/interactive/xapp.py
    -- Installing: /usr/local/lib/libe42_xapp_shared.so
    ```

6. Test Installation:
    ```bash
    cd test/agent-ric
    ./test_near_ric
    ```

    **Output**:
    ```console
    ...
    Encoding no label
    [E2-AGENT]: RIC_SUBSCRIPTION_DELETE_REQUEST rx
    [E2-AGENT]: RIC_SUBSCRIPTION_DELETE_REQUEST rx
    ag->agent_stopped = true 
    Test communicating E2-Agent and Near-RIC run SUCCESSFULLY
    ```

6. Unit Test (Optional steps)
   1. Ctest

        Execution Path: `~/<FlexRIC PATH>`:
        ```bash
        ./build/ctest
        ```

        **Output**:
        ```console
        Test project /root/flexric-osc/build
            Start 1: Unit_test_MAC
        1/8 Test #1: Unit_test_MAC ....................   Passed    0.00 sec
            Start 2: Unit_test_RLC
        2/8 Test #2: Unit_test_RLC ....................   Passed    0.00 sec
            Start 3: Unit_test_PDCP
        3/8 Test #3: Unit_test_PDCP ...................   Passed    1.15 sec
            Start 4: Unit_test_SLICE
        4/8 Test #4: Unit_test_SLICE ..................   Passed    0.48 sec
            Start 5: Unit_test_TC
        5/8 Test #5: Unit_test_TC .....................   Passed    8.44 sec
            Start 6: Unit_test_GTP
        6/8 Test #6: Unit_test_GTP ....................   Passed    0.00 sec
            Start 7: Unit_test_RC
        7/8 Test #7: Unit_test_RC .....................   Passed    5.45 sec
            Start 8: Unit_test_KPM
        8/8 Test #8: Unit_test_KPM ....................   Passed    4.59 sec

        100% tests passed, 0 tests failed out of 8

        Total Test time (real) =  20.15 sec
        ```

   2. Service Model unit test

        Execution Path: `~/<FlexRIC PATH>`:
        ```bash
        cd ./build/test/sm
        ls

        # example: KPM
        ./kpm_sm/test_kpm_sm
        ```

        **Output**:
        ```console
        ...
                                        </DistMeasurementBinRangeItem>
                        </distMeasBinRangeInfo>
                    </subscriptInfo>
                </actionDefinition-Format2>
            </actionDefinition-formats>
        </E2SM-KPM-ActionDefinition>
        Key Performance Metric (KPM-SM) version 3.0 Release 3 run with success
        ```
   
# 2. Configure FlexRIC
1. Configure FlexRIC IP Address:
    1. Open file flexric.conf directory:
        ```bash
        sudo nano /usr/local/etc/flexric/flexric.conf
        ```

    2. Set the IP address:
        > [!NOTE]
        > In this tutorial, the Near-RT RIC IP address is 192.168.8.48 from BubbleRAN.
        ```bash
        [NEAR-RIC]
        NEAR_RIC_IP = 192.168.8.48

        [XAPP]
        DB_DIR = /tmp/
        ```

# 3. Run Near-RT RIC
> **Note**: Near-RT RIC have to be run first before DU & CU.

1. Open the file below to run:
   
    Execution Path: `~/<FlexRIC PATH>`
    ```bash
    ./build/examples/ric/nearRT-RIC
    ```

    **Output**:
    ```console
    Setting the config -c file to /usr/local/etc/flexric/flexric.conf
    Setting path -p for the shared libraries to /usr/local/lib/flexric/
    [NEAR-RIC]: nearRT-RIC IP Address = 192.168.8.118, PORT = 36421
    [NEAR-RIC]: Initializing 
    [NEAR-RIC]: Loading SM ID = 2 with def = ORAN-E2SM-KPM 
    [NEAR-RIC]: Loading SM ID = 143 with def = RLC_STATS_V0 
    [NEAR-RIC]: Loading SM ID = 148 with def = GTP_STATS_V0 
    [NEAR-RIC]: Loading SM ID = 146 with def = TC_STATS_V0 
    [NEAR-RIC]: Loading SM ID = 144 with def = PDCP_STATS_V0 
    [NEAR-RIC]: Loading SM ID = 142 with def = MAC_STATS_V0 
    [NEAR-RIC]: Loading SM ID = 145 with def = SLICE_STATS_V0 
    [NEAR-RIC]: Loading SM ID = 3 with def = ORAN-E2SM-RC 
    [iApp]: Initializing ... 
    [iApp]: nearRT-RIC IP Address = 192.168.8.118, PORT = 36422
    fd created with 6 

    ```

2. Run the DU & CU.

3. Run xApp:
    > xAPP can be run after E2 node connection between RIC & DU is established.

    1. Open a new terminal (1 terminal for FlexRIC, 1 terminal for xApp).
    2. Open the file below to run an xApp
        > Below we run the KPM xApp.

        Open the `xapp_kpm_rc` file with root path: `~/<FlexRIC PATH>`
        ```
        ./build/examples/xApp/c/kpm_rc/xapp_kpm_rc
        ```
        - FlexRIC output:
            ```console
            Received message with id = 0, port = 17806 
            [E2AP] Received SETUP-REQUEST from PLMN 311.48 Node ID 0 RAN type ngran_gNB
            [NEAR-RIC]: Accepting RAN function ID 2 with def = h0ORAN-E2SM-KPM 
            [NEAR-RIC]: Accepting RAN function ID 3 with def =?ORAN-E2SM-RC 
            Unknown RAN function ID, thus rejecting 168 
            [NEAR-RIC]: Accepting interfaceType 3
            [iApp]: E42 SETUP-REQUEST received
            [iApp]: E42 SETUP-RESPONSE sent
            [iApp]: SUBSCRIPTION-REQUEST xapp_ric_id->ric_id.ran_func_id 2  
            [E2AP] SUBSCRIPTION REQUEST generated
            [NEAR-RIC]: nb_id 0 port = 17806  
            [NEAR-RIC]: nb_id 0 port = 17806  
            [NEAR-RIC]: CONTROL SERVICE sent

            [iApp]: E42_RIC_CONTROL_REQUEST rx
            [NEAR-RIC]: CONTROL ACKNOWLEDGE received
            [iApp]: RIC_CONTROL_ACKNOWLEDGE tx
            [NEAR-RIC]: nb_id 0 port = 17806  
            [NEAR-RIC]: CONTROL SERVICE sent

            [iApp]: E42_RIC_CONTROL_REQUEST rx
            [NEAR-RIC]: CONTROL ACKNOWLEDGE received
            [iApp]: RIC_CONTROL_ACKNOWLEDGE tx
            
            ```
        - KPM xAPP output:
            ```console:
            ...
            ----------Indication # 257 -------------
            Slice {1-234} measurment "DRB.UEThpDl.SNSSAI" = 2092240
            Slice {1-234} measurment "RRU.PrbUsedDl.SNSSAI" = 61
            Slice {2-334} measurment "DRB.UEThpDl.SNSSAI" = 609488
            Slice {2-334} measurment "RRU.PrbUsedDl.SNSSAI" = 19
            Slice {3-434} measurment "DRB.UEThpDl.SNSSAI" = 586768
            Slice {3-434} measurment "RRU.PrbUsedDl.SNSSAI" = 18
            ----------Indication # 258 -------------
            Slice {1-234} measurment "DRB.UEThpDl.SNSSAI" = 2059424
            Slice {1-234} measurment "RRU.PrbUsedDl.SNSSAI" = 60
            Slice {2-334} measurment "DRB.UEThpDl.SNSSAI" = 586864
            Slice {2-334} measurment "RRU.PrbUsedDl.SNSSAI" = 18
            Slice {3-434} measurment "DRB.UEThpDl.SNSSAI" = 572544
            Slice {3-434} measurment "RRU.PrbUsedDl.SNSSAI" = 18
            ...
            ```