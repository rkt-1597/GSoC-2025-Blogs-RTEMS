---
layout: post
title: GSoC 2025 - Second Half @RTEMS
categories: ["Weekly Progress"]
description: GSoC 2025 Midterm Evaluation
---

## Project overview
------------------------------------
GRETH is an Ethernet Media Access Controller from Frontgrade Gaisler. Currently, support for this driver in RTEMS exists only for the Legacy Networking Stack. However, lwIP, which stands for `lightweight Internet Protocol` is a newly emerging network stack, which is getting popular, owing to its compatibility with embedded systems, its lower memory footprint and simpler logic, among its other benefits. This project aims to provide GRETH Network driver for RTEMS lwIP Network Stack, for the LEON3 processor, which is based on SPARC Architecture 

## Way ahead...
----------------------------------------
1. Packet Reception :   
    + After implementing Driver initialization and packet transmission mechanism, another important aspect of the new GRETH lwIP driver that remains yet is its packet reception mechanism.  
    + Check for various types of errors in received packets.  
    + Once packets have been received, make the buffer descriptors again ready for packet reception.  
    + Pass the received packet up the network stack for further processing.  
    + Write tests/RTEMS applications for testing reception functionality of GRETH lwIP driver.  
    + Implement interrupt handling for reception of packets.  

2. Complete Interrupt Handling Mechanism :  
    + Audit the interrupt handling  mechanism generated till this point  
    + Complete any remaining portion of the interrupt handling mechanism  

3. Code Clean-Up and Documentation :  
    + Clean up the code generated so far, according to [RTEMS Coding Standards](https://docs.rtems.org/docs/6.1/eng/coding.html)  
    + Create documentation using fraeworks like Doxygen, for future references.  


## Methods and Steps used for simulation 
--------------------------------------

The testing as tested on `Ubuntu 22.04.5 LTS (jammy)` is done using these steps 

1. Installing RTEMS : Follow these steps to install RTEMS. Here, the installation prefix chosen is `$HOME/quick-start/rtems/7`.
    + Clone the repositories to obtain the sources
    `>>> mkdir -p $HOME/quick-start/src`
    `>>> cd $HOME/quick-start/src `
    `>>> git clone https://gitlab.rtems.org/rtems/tools/rtems-source-builder.git rsb  `
    `>>> git clone https://gitlab.rtems.org/rtems/rtos/rtems.git`  
    +  Installing the Tool Suite :  
    `>>> cd $HOME/quick-start/src/rsb/rtems` 
    `>>> ../source-builder/sb-set-builder --prefix=$HOME/quick-start/rtems/7 7/rtems-sparc`  
    + Build the Board Support Package, for this GSoC project, BSP is `sparc/leon3`  
    `>>> cd $HOME/quick-start/src/rsb/rtems`  
    `>>> ../source-builder/sb-set-builder --prefix=$HOME/quick-start/rtems/7 \`  
    `--target=sparc-rtems7 --with-rtems-bsp=sparc/erc32 --with-rtems-tests=yes 7/rtems-kernel`  
    + With this, we have built RTEMS and also our required BSP!

2. To create virtual bridge `lxcbr0` :   
`lxcbr0` is a virtual bridge. When an application, which uses GRETH, is runnning in SIS, a tap device is automatically created when the application enables the GRETH core. The tap can optionally be connected to a host bridge using -bridge br0 or similar at invocation. Networking requires SIS to be run as root or with `sudo`. To create this bridge : 

    + Install `lxc` package :   
    `>>> sudo apt install lxc`  
    + Set the following configurations in file `/etc/default/lxc-net` :   
    `USE_LXC_BRIDGE="true"`   
    `LXC_BRIDGE="lxcbr0"`  
    `LXC_ADDR="10.0.3.1"`  
    `LXC_NETMASK="255.255.255.0"`  
    `LXC_NETWORK="10.0.3.0/24"`  
    `LXC_DHCP_RANGE="10.0.3.2,10.0.3.254"`  
    `LXC_DHCP_MAX="253"`  
    `LXC_DHCP_CONFILE=""`  
    `LXC_DOMAIN="lxc"`
    using the command :  
    `>>> sudo nano /etc/default/lxc-net`
    + Now run the following command to start the virtual bridge `lxcbr0` :  
    `>>> sudo systemctl start lxc-net`  
    + To stop the bridge `lxcbr0` use the following command :  
    `>>> sudo systemctl stop lxc-net`

3. Starting the SIS terminal : 

    + Clone the GitLab repository for RTEMS-SIS :  
    `>>> git clone https://gitlab.rtems.org/rtems/tools/rtems-sis`
    + Move to `rtems-sis` directory  
    `>>> cd rtems-sis`  
    + SIS uses `make` build system. Run the following command to build SIS  
    `>>> ./configure`  
    `>>> make`
    + (optional) Further, to generate doccumentation for SIS, to generate a file `sis.pdf`, run :  
    `>>> make sis.pdf`  
    + Now you will be having an executable file : `sis`. To start it, run (`-v` option enables higher verbosity; it is an optional flag) :  
    `>>> sudo ./sis -v -leon3 -bridge lxcbr0 ./<application-file-name>`
    + To run your application, use the command in SIS terminal:  
    `>>> run`  
    + The application should start running

4. To build RTEMS packages like : [Legacy Networking](https://gitlab.rtems.org/rtems/pkg/rtems-net-legacy), [lwIP](https://gitlab.rtems.org/rtems/pkg/rtems-lwip) or [Net Services](https://gitlab.rtems.org/rtems/pkg/rtems-net-services), follow these steps : 

    + Clone the GitLab repository :  
    `>>> git clone <repository-link-http-type>`  
    + Move inside the cloned repository :  
    `>>> cd <cloned-repository>`  
    + First, populate the git submodules :  
    `>>> git submodule init`  
    `>>> git submodule update` 
    + All these packages use `waf` build system. So, toi use this build system, follow these steps :  
    1. Confgure the workspace. Here, `INSTALL_PREFIX` is the [path where RTEMS is installed](https://docs.rtems.org/docs/main/user/start/prefixes.html#quickstartprefixes) :  
    `>>>./waf configure --prefix=INSTALL_PREFIX --rtems-bsps sparc/leon3`  
    2. Build the package :  
    `>>> ./waf build`  
    3. Install the package :  
    `>>> ./waf install`  

    For [Legacy Networking Demos](https://gitlab.rtems.org/rtems/pkg/rtems-net-legacy-demos), this process is a bit different since it uses `make` build system. To build it, follow these steps :    
    + Clone the GitLab repository :  
    `>>> git clone <repository-link-http-type>`  
    + Move inside the cloned repository :  
    `>>> cd <cloned-repository>`
    + Set variables (for `rtems-installation-prefix` check previous part) :  
    `>>> export RTEMS_MAKEFILE_PATH=<rtems-install-prefix>/sparc-rtems7/leon3`  
    `>>> export PROJECT_ROOT=<rtems-install-prefix>`  
    + Then build the package :  
    `>>> make`

5. Using tests from RTEMS Packages in SIS : Once RTEMS Packages are built, tests can be found at : 
    + In Legacy Networking :  
    `rtems-net-legacy/build/sparc-rtems7-leon3/testsuites/<test-name>/<test>.exe`  
    + In Legacy Networking Demos :  
    `rtems-net-legacy-demos/<test-name>/o-optimize/<test>.exe`  
    [e.g. tests like ttcp, telnetd, etc.]  
    + In Net Services :  
    `rtems-net-services/buils/sparc-rtems7-leon3/<test>.exe`  
    + In lwIP :  
    `rtems-lwip/build/sparc-rtems7-leon3/<test>.exe`
