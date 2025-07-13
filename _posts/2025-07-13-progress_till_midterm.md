---
layout: post
title: Progress till Midterm Evaluation @RTEMS-GSoC-2025
categories: ["Weekly Progress"]
description: GSoC 2025 Midterm Evaluation
---

## Milestones Achieved
-------------------------------
The period in GSoC until now has been pretty thrilling and rewarding. During this time till Midterm Evaluation of GSoC 2025 @RTEMS Project, I accomplished the following milestones : 

+  GRETH lwIP Driver successfully being Initialized and this is tested
+  Code for GRETH Transmission Mechanism and handling of Transmission related Interrupts ready  
+  GRETH Legacy Driver successfully simulated and tested

## Project overview
------------------------------------
GRETH is an Ethernet Media Access Controller from Frontgrade Gaisler. Currently, support for this driver in RTEMS exists only for the Legacy Networking Stack. However, lwIP, which stands for `lightweight Internet Protocol` is a newly emerging network stack, which is getting popular, owing to its compatibility with embedded systems, its lower memory footprint and simpler logic, among its other benefits. This project aims to provide GRETH Network driver for RTEMS lwIP Network Stack, for the LEON3 processor, which is based on SPARC Architecture 

## Progress In Detail...
----------------------------------------
1. GRETH lwIP Driver successfully being Initialized and this is tested :  
    Before this project, RTEMS lwIP Package did not have support for GRETH as well as for `sparc/leon3` BSP. My first milestone for my GSoC project was to provide support for `sparc/leon3` BSP by starting to write GRETH Driver for RTEMS lwIP Networking Stack, thus building this package for the first time for GRETH as well as `sparc/leon3` BSP. I feel very happy to tell that with the new code for RTEMS lwIP GRETH Driver, and after days of debugging and fixing warnings and errors, the lwIP Networking Stack now builds successfully, without even any warnings!  
    ![lwIP_successful_build]({{site.baseurl}}/assets/posts/week7/lwip_success.png) 

    The commit ID at which the successful build was first noted is (clickable link):  
    [`0680fb98e91d2f418daedb54964782dfa6c8f6ee`
    on branch `Prithvi-GSoC-25`](https://gitlab.rtems.org/rtems/pkg/rtems-lwip/-/merge_requests/22/diffs?commit_id=0680fb98e91d2f418daedb54964782dfa6c8f6ee)  

    The initialization of the GRETH lwIP Driver has been tested using `printf()` statements at various phases in the driver code, and using `telnetd01.exe` application from RTEMS lwIP itself, being run in SIS.  
    ![lwIP_init_tyesting]({{site.baseurl}}/assets/posts/week7/greth_success_init.png)  

2. Code for GRETH Transmission Mechanism and handling of Transmission related Interrupts ready :  
    The code for GRETH lwIP driver transmission mechanism, along with required interrupt handling for transmission mechanism, is ready. The code is written keeing in mind the working as studied from GRETH Legacy Driver, in RTEMS Legacy Networking, and various learnings from TMS570 Ethernet Driver in RTEMS lwIP.    

    The commit ID at which the successful build with the whole transmission mechanism was first noted is (clickable link):  
    [`ca4c217081412523043b7a5991822a952ec0c06b`
    on branch `Prithvi-GSoC-25`](https://gitlab.rtems.org/rtems/pkg/rtems-lwip/-/merge_requests/22/diffs?commit_id=ca4c217081412523043b7a5991822a952ec0c06b)  

3. GRETH Legacy Driver successfully simulated and tested :  
    A key aspect of this project was to simulate GRETH Legacy Driver. For this milestone, I had encountered several problems, like memory alignment issues, connectivity issues, etc. due to which I wasn't able to simulatye the working of GRETH Legacy Driver. However, it was due to all these errors that I got the opportunity to dive deep into RTEMS codebase, so as to work on these issues in a better way. I learnt about the importance and method of configuration of struct `rtems_bsdnet_config` and `rtems_bsdnet_ifconfig`, working with `sparc-rtems7-gdb`, using tools like  `addr2line`, `sparc-rtems7-objdump`, etc. to name a few important learnings.  

    Finally, referring to [RTEMS Networking Tests](https://github.com/joelsherrill/rtems-networking-tests/blob/main/mcast_client/mcast_client.c) I tried making a unicast client, which sends a message to an IP address (GRETH LEgacy Driver IP Address)  
    This was verified by monitoring the UDP Packets received by Legacy GRETH Driver using `tcpdump` utility.
    ![tcpdump_ucast]({{site.baseurl}}/assets/posts/week7/udp_echo/ucast_client.png)  
    `telnetd02.exe` application from RTEMS LEgacy Networking, has a command `netstats` using which, dteailed statistics regading the recent communication session, could be obtained. The statistics for the above result shown proper statistics.
    ![tcpdump_netstats]({{site.baseurl}}/assets/posts/week7/udp_echo/ucast_netstats.png)
    Further, using `netcast` I was also able to transmit custom UDP packets to the Legacy GRETH Driver and it shown proper reception.
    ![netcat_netstats]({{site.baseurl}}/assets/posts/week7/udp_echo/nc_netstats.png)
    After checking that my unicast UDP client test works, I pushed it to [my branch of my fork of RTEMS Networking Tests](https://github.com/rkt-1597/rtems-networking-tests/blob/Prithvi-GSoC-2025/ucast_client/ucast_client.c)


## Methods and Steps used for simulation 
--------------------------------------

These tests have been tested on `Ubuntu 22.04.5 LTS (jammy)`

1. To create virtual bridge `lxcbr0` :   
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

2. Starting the SIS terminal : 

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

3. To build RTEMS packages like : [Legacy Networking](https://gitlab.rtems.org/rtems/pkg/rtems-net-legacy), [lwIP](https://gitlab.rtems.org/rtems/pkg/rtems-lwip) or [Net Services](https://gitlab.rtems.org/rtems/pkg/rtems-net-services), follow these steps : 

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

4. Using tests from RTEMS Packages in SIS : Once RTEMS Packages are built, tests can be found at : 
    + In Legacy Networking :  
    `rtems-net-legacy/build/sparc-rtems7-leon3/testsuites/<test-name>/<test>.exe`  
    + In Legacy Networking Demos :  
    `rtems-net-legacy-demos/<test-name>/o-optimize/<test>.exe`  
    [e.g. tests like ttcp, telnetd, etc.]  
    + In Net Services :  
    `rtems-net-services/buils/sparc-rtems7-leon3/<test>.exe`  
    + In lwIP :  
    `rtems-lwip/build/sparc-rtems7-leon3/<test>.exe`

