---
layout:      project  # Must be set to project
title:       Toyon Research Corporation
date:        8 Feb 2024
image:
  path:      /assets/thumb/toyon@6000w.webp
  srcset:
    1920w:   /assets/thumb/toyon@1920w.webp
    960w:    /assets/thumb/toyon@960w.webp
    480w:    /assets/thumb/toyon@480w.webp
    240w:    /assets/thumb/toyon@240w.webp
caption:     >
  A summary of Allen's experience at Toyon Research Corporation.
description: >
  A summary of Allen's experience at Toyon Research Corporation.
hide_description: true
featured:    false
sitemap:     false
---


#### Since joining Toyon, Allen has worked on a variety of projects and has gained extensive experience with AI and machine learning (ML), deep learning, image signal processing, 3D geometric computer vision, and more. Some project highlights are featured below:

<style>
  h3, h3+p {display: inline;}
</style>

---

<details>
<summary> <h3> LIDAR Point Cloud Processing, BIM Generation, and Segment Anything in 3D </h3> </summary>
<div style="text-align: justify">
In work for a counter-WMD client requiring 3D mapping capabilities, Allen has been exploring the use of the Segment Anything Model (SAM) for automated data labeling of 2D and 3D data, comprising of color images, depth images, and LIDAR point clouds. This automated data labeling method greatly enhances the productivity of human labelers, as they only need to verify the accuracy of the labels and make corrections as needed, rather than creating them from scratch. The labeled data can subsequently be used to train a more specialized AI model that can focus on specific objects of interest, and run in real-time on edge computing devices.
<br><br>
Furthermore, Allen has developed methods of plane segmentation and the extraction of infrastructure components, such as walls, floors, and ceilings, for the generation of Building Information Models (BIM) from LIDAR scan data. This modeling software is actively being extended to support representation of room-scale objects, such as desks and other furniture, to increase the detail of the BIM models.
</div>
</details>

---

<details>
<summary> <h3> 3D Reconstruction, Automatic Target Recognition, and CNNs for Satellite Imagery </h3> </summary>
<div style="text-align: justify">
Allen has also been active in the development and testing of a C++ implementation of 3D reconstruction using airborne and satellite images in a novel Bayesian 3D reconstruction algorithm framework. As part of that effort he has architected, trained, and tested U-Net-based convolutional neural networks (CNNs) for the semantic segmentation of buildings from terrain, and the above-ground-level height regression of both buildings and terrain. To enable single-image semantic segmentation and height regression, he developed new ways to incorporate perspective metadata, such as sun and satellite azimuth and elevation, and shadow appearance.
<br><br>
In work for clients in the aerospace and geospatial analytics industries, Allen has implemented and tested convolutional neural networks (CNNs) for automatic target recognition (ATR) of aircraft and ships in satellite and aerial imagery. These CNNs detect and classify objects using oriented bounding boxes (OBBs), as opposed to the more common axis-aligned bounding boxes. In addition to showing objects’ orientations, these OBBs enable much tighter detection of long diagonal objects, e.g. ships, thereby significantly reducing the number of background pixels within the bounding box and preventing bounding box overlap in cases of tightly packed diagonal objects. To support this effort, Allen has also implemented a polygon and class ID labeling program, for efficiently labeling gigapixel (\(10^9 \sim 30k \times 30k\) pixels) size satellite images, and for reviewing and augmenting existing labels.
</div>
</details>

---

<details>
<summary> <h3> AR, VR, and Single-view 3D Reconstruction </h3> </summary>
<div style="text-align: justify">
In work for a special operations client, Allen has investigated novel methods for the single-view 3D reconstruction of building interiors, exploiting the line-features and orthogonal axes inherent in indoor scenes. The polygon mesh reconstructed 3D models are first individually generated, and then subsequently fused together into a common coordinate system for use in virtual reality (VR) walkthroughs. The models’ relative poses were estimated through the detection of ArUco fiducial markers. Allen’s work was presented at an industry 3D reconstruction workshop at SOFWERX, where his results were enthusiastically received.
<br><br>
On a project for a maritime client, Allen developed an algorithm for joint 360-degree stereo-camera calibration and panoramic stereo-image stitching, supporting 360-VR viewing of underwater environments for mine defusal.
<br><br>
On another project for a maritime client, in order to enable the creation of Augmented Reality (AR) tutorials of engineering and maintenance efforts in complex and dynamic workspaces, Allen has focused his efforts on developing software libraries which enable real-time object 6-DOF pose estimation. The pose estimation deep neural networks enable AR headsets to detect small tools in real-time that may be picked up, moved around, and used to interact with other objects in the scene, whereas by default most AR headsets only map static environments.
</div>
</details>

---

<details>
<summary> <h3> Deep Reinforcement Learning </h3> </summary>
<div style="text-align: justify">
In the domain of reinforcement learning, Allen has contributed to the development of the AFRL Core Reinforcement Learning (CoRL) library and the DARPA Artificial Intelligence Reinforcements (AIR) simulator. He has trained deep reinforcement learning agents to solve asymmetrical tasks in 2D and 3D environments, and created scripted agents and scenarios to support curriculum learning for trainable agents. Agents are trained on simple tasks at first, followed by increasingly difficult tasks, in order to learn in an incremental manner. This allows the agents to converge on solving hard tasks that are otherwise too difficult to learn from scratch.
</div>
</details>

---

