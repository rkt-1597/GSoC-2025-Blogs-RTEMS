---
layout: post
title: Project Introduction – Provide SPARC greth Network Drivers for lwIP
categories: ["Project Description"]
description: GSoC-2025 Project - Prithvi Tambewagh
---

## Overview
----------------------------------
The “Provide SPARC greth Network Drivers for lwIP” project is part of Google Summer of Code 2025 with the RTEMS Project. The goal is to modernize networking support for SPARC-based LEON3BSP by porting the GRETH Ethernet driver from the Legacy Network stack to the lwIP (Lightweight IP) stack.

## Background
----------------------------------
Currently, SPARC Board Support Packages (BSPs) in RTEMS only support the GRETH network driver using the legacy TCP/IP stack. This limits the ability to use the more modern, lightweight, and actively maintained lwIP stack for embedded networking. lwIP is widely used in embedded systems for its less memory requirment and efficient performance, making it ideal for real-time operating systems like RTEMS.

## Project Goals
----------------------------------
- **Port the GRETH driver** from the legacy RTEMS network stack to the lwIP network stack for SPARC BSPs.
- **Study and leverage the existing driver** as an example, and investigate any available older lwIP drivers (e.g. TMS570 Ethernet lwIP Driver), while accounting for updates in both GRETH hardware and lwIP since their original development.
- **Enable robust, modern networking** on SPARC hardware using lwIP, improving maintainability, performance, and feature availability for RTEMS users by means like tests

## Approach
----------------------------------
- Analyze the current GRETH driver implementation in the legacy stack.
- Research and evaluate any previously available lwIP-compatible GRETH drivers e.g TMS570 Ethernet lwIP Driver
- Adapt or rewrite the driver as needed to ensure compatibility with lwIP and GRETH hardware.
- Test and validate the new driver on simulator e.g. RTEMS-SIS.
- Collaborate with mentors and the RTEMS community for feedback and integration guidance.


## Important Links
----------------------------------
+ [GitLab Issue - GSoC 2025 Project](https://gitlab.rtems.org/rtems/programs/gsoc/-/issues/77)
+ [GitLab GSoC 2025 Project Epic Link](https://gitlab.rtems.org/groups/rtems/-/epics/28)

