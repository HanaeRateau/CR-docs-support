---
title: MATLAB
sidebar_position: 3
---


import emio_matlab from './img/emio-matlab-simulink.png';

Pilot Emio from MATLAB, and create your own teaching material. Thanks to the [UDP bridge](https://docs-support.compliance-robotics.com/docs/v26.06/Developers/emio-api/#startudp) of the [Emio API](https://docs-support.compliance-robotics.com/docs/v26.06/Developers/emio-api/) and the [MATLAB.Emio](https://github.com/SofaComplianceRobotics/MATLAB.Emio) GitHub repository you can use the _Emio Communication UDP block_ in SimuLink and send commands to Emio, and also retrieve markers position, directly from MATLAB. 

<img className="centered" src={emio_matlab} width="80%" alt="Emio Communication UDP block in Simulink"/>
<figcaption>Emio Communication UDP block in Simulink.</figcaption>

GitHub repositories:

- [MATLAB.Emio](https://github.com/SofaComplianceRobotics/MATLAB.Emio): shows an example of how to use the _Emio Communication UDP block_ in SimuLink. Follow the instructions of the `README.md` and create your own teaching material on MATLAB.
- [MATLAB.Emio.demo_pendulum](https://github.com/SofaComplianceRobotics/MATLAB.Emio.demo_pendulum): shows a MATLAB project to identify the transfer function to stabilize a weighted leg of Emio.