# Maritime Object Detection & Tracking via ROS 2
> **An Autonomous Perception Pipeline for Real-Time Harbor Monitoring**

## 📖 Overview
This repository contains a robust, ROS 2-based computer vision pipeline designed for the detection, classification, and continuous tracking of maritime micro-targets (e.g., swimmers, surfers, paddleboards, and small watercraft). Originally developed as a thesis project, this system tackles real-world environmental challenges such as severe sun glare, heavy occlusion, and target label flickering by coupling a high-precision neural network with advanced temporal tracking logic.

## 🏗️ System Architecture
The software is fully containerized via Docker and decoupled into a modular ROS 2 topic graph to ensure fault tolerance and scalability for edge deployment.

### 1. Perception Layer (Cascade R-CNN + FPN)
* **Multi-Scale Feature Extraction:** Utilizes a Feature Pyramid Network (FPN) to process semantic context and high-resolution spatial details simultaneously, preventing the loss of micro-targets.
* **Micro-Anchor Tuning:** Re-engineered Region Proposal Network (RPN) incorporating 32-pixel micro-anchors and custom aspect ratios specifically tailored for the geometry of swimmers and SUPs.
* **Iterative Bounding Box Refinement:** Employs cascading network heads with progressively stricter Intersection over Union (IoU) thresholds (0.5 &rarr; 0.6 &rarr; 0.7) to achieve extreme localization accuracy.

### 2. Tracking & Association Layer (ByteTrack)
* **Spatial Tracking:** Utilizes a Kalman Filter to predict bounding box trajectories during temporary occlusions (e.g., a swimmer diving beneath a wave).
* **Global Optimum Matching:** Implements the Hungarian Algorithm to mathematically minimize assignment costs across the scene, ensuring consistent ID retention.
* **Label Stability Mitigation:** Custom 15-frame temporal buffer and majority-voting mechanism to completely eliminate classification flickering on ambiguous targets. 

### 3. Middleware Integration (ROS 2)
* `detector_node`: Handles tensor ingestion and neural network inference.
* `tracker_node`: Manages spatial memory, ID assignment, and temporal label smoothing.
* `health_node`: Autonomous system monitor that broadcasts `WARN` states if GPU memory utilization exceeds **85%**, allowing for graceful fallback before Out-Of-Memory (OOM) failures.

## 🚀 Advanced Engineering Features
* **Test-Time Augmentation (TTA):** Wraps the detection phase in a geometric permutation loop, applying a secondary Non-Maximum Suppression (NMS) consensus filter to boost mAP on shape-shifting targets.
* **Degenerate Box Sanitization:** Custom data pre-processing pipeline that actively filters 0-pixel and 1-pixel annotations, preventing divide-by-zero singularities and fatal gradient loss spikes during training.
* **I/O Optimization Strategy:** Pipeline architecture designed to accommodate future hardware-accelerated video decoding (NVDEC) to prevent CPU starvation during heavy ROS 2 matrix serialization.

## 💻 Technology Stack

| Category | Technologies |
| :--- | :--- |
| **Deep Learning** | PyTorch, MMDetection |
| **Middleware** | ROS 2 (Humble/Foxy), Custom Node Graph |
| **Computer Vision** | OpenCV, ByteTrack, Cascade R-CNN |
| **Deployment** | Docker, Linux |

## 🔮 Future Roadmap
This architecture is structured for transition from lab to production environments. Planned expansions include:
* **Edge Compute Integration:** Deployment on NVIDIA Jetson AGX platforms for localized harbor processing at 25-30 FPS.
* **Long-Term Re-Identification (ReID):** Appearance-based visual embedding extractors to recover target IDs across extended timeframes and complete camera exits.
* **Thermal Sensor Fusion:** Integration of Long-Wave Infrared (LWIR) topics to eliminate false negatives caused by sun glare and enable zero-light night operations.

---
*Developed as part of a Bachelor's Thesis in Maritime Object Categorization and Tracking.*
