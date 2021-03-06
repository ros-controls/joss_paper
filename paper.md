---
title: 'ros_control: A generic and simple control framework for ROS'
tags:
  - ros
  - control
  - framework
  - robotics
  - robot control
  - manipulation
  - navigation
  - mobile robotics
  - hardware abstraction layer  
authors:
 - name: Sachin Chitta
   orcid: 0000-0003-4859-6096
   affiliation: 9, 11
 - name: Eitan Marder-Eppstein
   orcid: 0000-0003-0940-7117
   affiliation: 1
 - name: Wim Meeussen
   orcid: 0000-0003-1905-4001
   affiliation: 1
 - name: Vijay Pradeep
   orcid: 0000-0002-8280-7275
   affiliation: 1
 - name: Adolfo Rodríguez Tsouroukdissian
   orcid: 0000-0002-3075-1329
   affiliation: 12, 2
 - name: Jonathan Bohren
   orcid: 0000-0003-4568-6352
   affiliation: 8, 10
 - name: David Coleman
   orcid: 0000-0001-6112-1795
   affiliation: 3, 11
 - name: Bence Magyar
   orcid: 0000-0001-8455-8674
   affiliation: 4, 2
 - name: Gennaro Raiola
   orcid: 0000-0003-1481-1106
   affiliation: 5, 2
 - name: Mathias Lüdtke
   orcid: 0000-0002-1835-3854
   affiliation: 6
 - name: Enrique Fernandez Perdomo
   orcid: 0000-0002-4881-0497
   affiliation: 7, 2
affiliations:
 - name: hiDOF, Inc. (at the time of this work)
   index: 1
 - name: PAL Robotics (at the time of this work)
   index: 2
 - name: PickNik Consulting
   index: 3
 - name: Heriot-Watt University, Edinburgh Centre for Robotics
   index: 4
 - name: Department of Advanced Robotics, Istituto Italiano di Tecnologia (IIT)
   index: 5
 - name: Fraunhofer IPA
   index: 6
 - name: Clearpath Robotics
   index: 7
 - name: Honeybee Robotics
   index: 8
 - name: Kinema Systems Inc.
   index: 9
 - name: John Hopkins University
   index: 10
 - name: Willow Garage Inc. (at the time of this work)
   index: 11
 - name: Pick-it NV
   index: 12
date: 31 October 2017
bibliography: paper.bib
---
# Summary

In recent years the Robot Operating System [@quigley2009ros] (ROS) has become the 'de facto' standard framework for robotics software development. The `ros_control` framework provides the capability to implement and manage robot controllers with a focus on both _real-time performance_ and _sharing of controllers_ in a robot-agnostic way. The primary motivation for a sepate robot-control framework is the lack of realtime-safe communication layer in ROS. Furthermore, the framework implements solutions for controller-lifecycle and hardware resource management as well as abstractions on hardware interfaces with minimal assumptions on hardware or operating system. The clear, modular design of `ros_control` makes it ideal for both research and industrial use and has indeed seen many such applications to date. The idea of `ros_control` originates from the `pr2_controller_manager` framework specific to the PR2 robot but `ros_control` is fully robot-agnostic. Controllers expose standard ROS interfaces for out-of-the box 3rd party solutions to robotics problems like manipulation path planning (`MoveIt!` [@chitta2012moveit]) and autonomous navigation (the `ROS navigation stack`). Hence, a robot made up of a mobile base and an arm that support `ros_control` doesn't need any additional code to be written, only a few controller configuration files and it is ready to navigate autonomously and do path planning for the arm. `ros_control` also provides several libraries to support writing custom controllers.
<!-- with some ideas borrowed from OROCOS [@bruyninckx2001open].  -->

![Overview](images/overview.png)

# Packages and functionalities

The backbone of the framework is the Hardware Abstraction Layer, which serves as a bridge to different simulated and real robots. This abstraction is provided by the `hardware_interface::RobotHW` class; specific robot implementations have to inherit from this class. Instances of this class model hardware resources provided by the robot such as electric and hydraulic actuators and low-level sensors such as encoders and force/torque sensors. It also allows for integrating heterogeneous hardware or swapping out components transparently whether it is a real or simulated robot.

There is a possibility for composing already implemented `RobotHW` instances which is ideal for constructing control systems for robots where parts come from different suppliers, each supplying their own specific `RobotHW` instance. The rest of the `hardware_interface` package defines read-only or read-write typed joint and actuator interfaces for abstracting hardware away, e.g. state, position, velocity and effort interfaces. Through these typed interfaces this abstraction enables easy introspection, increased maintainability and controllers to be hardware-agnostic.

The `controller_manager` is responsible for managing the lifecycle of controllers, and hardware resources through the interfaces and handling resource conflicts between controllers. The lifecycle of controllers is not static. It can be queried and modified at runtime through standard `ROS services` provided by the `controller_manager`. Such services allow to start, stop and configure controllers at runtime.

![ROS Control overview](images/ros_control_overview.png)

Furthermore, `ros_control` ships software libraries addressing real-time ROS communication, transmissions and joint limits. The `realtime_tools` library adds utility classes handling ROS communications in a realtime-safe way. The `transmission_interface` package supplies classes implementing joint- and actuator-space conversions such as: simple reducer, four-bar linkage and differential transmissions. A declarative definition of transmissions is supported directly with the kinematics and dynamics description in the robot's Universal Robot Description Format (URDF) [@garage2009universal] file. The `joint_limits_interface` package contains data structures for representing joint limits, methods to populate them through URDF or yaml files and methods to enforce these limits. `control_toolbox` offers components useful when writing controllers: a PID controller class, smoothers, sine-wave and noise generators.

The repository `ros_controllers` holds several ready-made controllers supporting the most common use-cases for manipulators, mobile and humanoid robots, e.g. the `joint_trajectory_controller` is heavily used with position-controlled robots to interface with MoveIt!. Finally, `control_msgs` provides ROS messages used in most controllers offered in `ros_controllers`.

`ros_control` was conceptualized by Sachin Chitta at Willow Garage Inc. and initial design and implementation was done by Sachin Chitta (then at Willow Garage), Wim Meussen, Vijay Pradeep and Eitan Marder-Epstein (then at HiDOF) before being released open-source.

`ros_control` is released as binary packages with each new version of ROS, source code is hosted at the [ros-controls](https://github.com/ros-controls) Github organization. Documentation on behaviour, interfaces, doxygen-generated pages and tutorials can be found at [ros_control](http://wiki.ros.org/ros_control) and [ros_controllers](http://wiki.ros.org/ros_controllers). For a thorough presentation we invite the interested reader to watch the talk given at ROSCon2014 [@rodriguez2014roscon].

# Robots using `ros_control`

Being a mature framework, `ros_control` is widely applied to both production and research platform robots. A few examples where the control system is implemented with `ros_control` are:

- Clearpath Robotics' outdoor mobile robots: Grizzly, Husky, Jackal [@cpr2017roscontrol], and OTTO Motors' industrial indoor mobile robots: OTTO 1500, OTTO 100
- The "Twil" robot at Federal University of Rio Grande do Sul [@lages2017parametric]
- The quadruped robots HyQ and HyQ2Max [@semini11hyqdesign, @semini2017design] at Istituto Italiano di Tecnologia
- NASA's humanoid and biped robots: Valkyrie & Robonaut [@ROB:ROB21560, @hart2014robot, @badger2016ros]
- PAL Robotics' humanoid, biped and mobile robots: REEM, REEM-C, PMB2, Tiago and Talos [@stasse2017talos]
- Shadow Robot's anthropomorphic, highly sensorized and precise Shadow Hand [@meier2016distinguishing]
- Universal Robots' industrial arms: UR3, UR5 [@andersen2015optimizing]

![Robots montage](images/ros_control_montage.jpg)
Robot names and credits in order of appearance: Valkyrie (Photo by NASA / Bill Stafford), Husky (Photo by Clearpath Robotics), OTTO (Photo by Otto Motors), TIAGo (Photo by PAL Robotics), Dexterous Hand (Photo by Shadow Robot), HyQ2Max (Photo by Istituto Italiano di Tecnologia), TALOS (Photo by PAL Robotics)

# References
