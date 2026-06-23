---
title: Release - Emio Labs v26.06
description: We are happy to announce **Emio Labs v26.06**
authors:
  - hanaerateau
  - eulaliecoevoet
tags: [v26.06, release, Emio Labs]
hide_table_of_contents: false
date: 2026-06-31T10:00
---


import emio_gamepad from './2026-06-release/img/emio-gamepad.gif'
import emiolabs_solutions from './2026-06-release/img/emio-labs-solutions.png'
import emio_matlab from './2026-06-release/img/emio-matlab-simulink.png'

We are happy to announce **Emio Labs v26.06**!

With this release Emio Labs has now a solution mode. 
The release also contains many new exciting features and improvements.
To upgrade, download the new version !

<!--truncate--> 

## Highlights

### ✨ Solution Mode

You can now enable a solution mode in Emio Labs to see the solutions of the labs. You can show or hide these solutions by toggling the Labs>Enable/Disable Solutions in the top menu bar. Have a look at [the documentation](https://docs-support.compliance-robotics.com/docs/v26.06/Users/EmioLabs/create-your-lab/#solutions).

<img className="centered" src={emiolabs_solutions} width="80%" style={{boxShadow:"rgba(149, 157, 165, 0.2) 0px 8px 24px"}}/>  

### ✨ MATLAB & Emio

Pilot Emio from MATLAB. This [GitHub repository](https://github.com/SofaComplianceRobotics/MATLAB.Emio) shows an example of how to use the Emio Communication UDP block in SimuLink. Follow the instructions of the README.md and create your own lab on MATLAB.

<img className="centered" src={emio_matlab} width="80%" style={{boxShadow:"rgba(149, 157, 165, 0.2) 0px 8px 24px"}}/>  

### ✨ New Content

We are excited to introduce new content to Emio Labs. These additions will help you explore the capabilities of Emio:

- [Emio.lab_optimization_firstorder](https://github.com/SofaComplianceRobotics/Emio.lab_optimization_firstorder): This lab aims at, in a first part, introducing the inverse kinematics of Emio using a multilayer perceptron (MLP) to model the mapping from end-effector position to motor angles. In a second part, the concept of parametric model is introduced to calibrate the youg modulus.
- [MATLAB.Emio.demo_pendulum](https://github.com/SofaComplianceRobotics/MATLAB.Emio.demo_pendulum): This demos shows a MATLAB project to identify the transfer function to stabilize a weighted leg of Emio.
- [Emio.demo_gamepad](https://github.com/SofaComplianceRobotics/Emio.demo_gamepad): This demo shows how to move the gripper of Emio  using a gamepad. It uses the inputs Python package to handle gamepad events. 

<img className="centered" src={emio_gamepad} width="80%" style={{boxShadow:"rgba(149, 157, 165, 0.2) 0px 8px 24px"}}/>  

## Other Changes

### New Features
- You can now copy the logs of SOFA to the clipboard using the new button in the logs window.

### Enhancements
- The logs of SOFA are now colorized
- The text is wrapped
- Labs now have a table of content

### Bug Fixes
- Fixes crash when logs are too large
- Fixes extrapython path not taken into account
