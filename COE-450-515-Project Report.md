# AI-Guided Maze-Navigating Car - Original report text

COE 450/515 Course Project Report – Term 221 1 
 
AI-Guided Autonomous Maze-Navigating Car 
Khalid Alsaif, Saud Alsaleem 
King Fahd University of Petroleum and Minerals 
Computer Engineering Department 
COE 450/515: Introduction to Smart Systems 
 
  

COE 450/515 Course Project Report – Term 221       
 
 
Introduction 
Recent advances in embedded machine learning have enabled small, low-power devices 
to perform increasingly intelligent tasks such as image recognition, real-time control, and 
autonomous navigation. This project explores these capabilities through the development of an 
AI-Guided Autonomous Maze-Navigating Car, a system that combines computer vision, 
embedded artificial intelligence, and real-time actuation to navigate a maze without human 
intervention. The goal is to design a complete smart system capable of perceiving its 
environment using an onboard camera, interpreting directional signs through an Edge Impulse-
trained neural network, and controlling motors and sensors to move safely and correctly through 
the maze. 
The project integrates multiple components: an ESP32-based robotic car platform, an ultrasonic 
distance sensor for obstacle detection, a camera module for visual input, and an on-device AI 
model for sign classification. One key challenge was in the capture phase. We first took pictures 
using a laptop camera. However, it gave us bad classification using the car cam. Thus, we 
retrained it by taking the pictures using the car’s cam. Another issue we faced was with the car 
kit itself. One issue was with the servo motor that holds the cam. It had a problem that caused it 
to aggressively oscillate, making classification much harder. Furthermore, the floor had a great 
impact on the classification. Each floor material affects the turning angle, and thus makes the 
car’s turning inside the maze inconsistent.  
System Description 
The proposed system is an autonomous robotic car capable of navigating a maze using 
visual cues interpreted by an embedded AI model. The system relies on three core subsystems: 

COE 450/515 Course Project Report – Term 221       
 
perception, decision-making, and actuation. The perception subsystem uses the ESP32 camera 
module to continuously capture images from the front-facing perspective of the robot. These 
images are then processed by a machine-learning classifier deployed through Edge Impulse, 
providing the system with the ability to recognize directional signs such as Left, Right, and U-
turn indicators. 
The decision-making subsystem integrates the neural-network output with sensor data from the 
ultrasonic module. The AI inference determines the intended navigation direction, while the 
ultrasonic sensor ensures safe operation by preventing collisions and allowing the robot to stop 
or search for the next direction when obstacles are detected. Once a direction is identified with 
sufficient confidence, the actuation subsystem commands the Freenove motor driver to perform 
the appropriate maneuver. These maneuvers include forward motion, turning left or right, or 
executing a U-turn sequence depending on the detected sign. 
The figure below shows the logic of our project. 
 


COE 450/515 Course Project Report – Term 221       
 
 
Hardware Platform 
The hardware platform consists of the Freenove 4WD Smart Car for ESP32, chosen for its 
integrated motor driver, compatibility with camera modules, and support for ultrasonic sensing. 
The system includes: 
• ESP32-WROVER microcontroller: A dual-core MCU with integrated Wi-Fi and 
sufficient memory to execute Edge Impulse models efficiently. 
• OV2640 camera module: Captures forward-facing images at QVGA resolution 
(320×240), which are resized and processed for AI inference. 
• Ultrasonic distance sensor: Provides obstacle detection by measuring forward distance. 
Its readings allow safe movement by stopping the robot before collision. 
• Motor driver (PCA9685 + L298N-style control): Supports 4-wheel steering and 
independent speed control for differential turning. 
• 4 geared DC motors: Enable omnidirectional movement, including pivot turns, forward 
motion, and reversing. 
• Battery pack and power distribution board: Supply stable voltage to both logic 
components and motors. 
 
Software Platform 
The software platform integrates multiple components running on the ESP32: 
• Arduino framework: Used for programming the microcontroller due to its simplicity, 
large library support, and compatibility with Freenove hardware. 
• ESP32 Camera driver: Handles sensor initialization, JPEG frame capture, RGB 
conversion, and resizing needed by the Edge Impulse pipeline. 

COE 450/515 Course Project Report – Term 221       
 
• Edge Impulse inference SDK: Provides the machine-learning runtime for performing real-
time classification directly on the ESP32. The model is trained on directional sign images 
and exported as a C++ library. 
The software follows an event-driven loop structure. Each iteration captures an image, 
runs the classifier, interprets the result, checks for obstacles, and issues motor commands 
accordingly. Memory management, especially for camera buffers, is carefully handled to 
ensure real-time performance within ESP32’s resource limits. 
 
Implementation 
The implementation began with configuring the ESP32 camera module and ensuring 
correct pin mappings for the Freenove hardware. Once the camera was operational, a custom 
dataset of directional signs (Left, Right, U-turn) was collected manually and uploaded to Edge 
Impulse. The model was trained as an object-detection model, enabling the robot to detect the 
location and label of signs within its field of view. 
 
We have also designed the maze using SketchUp. 
 
The figure below shows the training result from Edge Impulse 


COE 450/515 Course Project Report – Term 221       
 
 
On the ESP32 side, the inference loop was integrated with the motor control logic. Each cycle 
performs the following steps: 
1. Capture frame using esp_camera_fb_get(). 
2. Convert and resize the image to fit the model input dimensions. 
3. Run inference using Edge Impulse’s run_classifier() function. 
4. Parse bounding boxes to determine whether an “R”, “L”, or “U” sign is detected. 
5. Read ultrasonic sensor to ensure no forward obstacle is present. 
6. Execute movement: 
o Forward movement if the path is clear. 
o Turn right if an "R" sign is detected. 
o Turn left if an "L" sign is detected. 
o Execute U-turn if a "U" sign is detected. 
7. Stop motors and reset flags before the next frame. 
The system successfully integrates perception and navigation, enabling real-time autonomous 
operation. 
 
Performance Evaluation 
Discuss and provide results for the performance of the system including, memory 
footprint, power consumption, latency, etc. 


COE 450/515 Course Project Report – Term 221       
 
The system was evaluated inside a controlled maze environment with printed directional signs 
placed at intersections. Key performance factors considered include: 
• Inference speed: The ESP32 processed each frame in real time, with average 
classification latency of 129 ms. 
• Detection accuracy: The model reliably identified signs when placed at reasonable 
distances with accuracy of 0.8 to 0.95. 
• Average power consumption: 8.5 to 9.5 W. 
 
Conclusion 
This project demonstrates the feasibility and potential of deploying artificial intelligence 
on resource-constrained embedded platforms for real-time autonomous navigation. By 
combining the ESP32 microcontroller, onboard camera, machine-learning inference from Edge 
Impulse, and precise motor control via the Freenove platform, we built a fully autonomous maze-
solving robot capable of interpreting visual signs and making intelligent decisions. 
The work highlights the strengths of edge AI, including low latency, independence from external 
servers, and the ability to operate in environments without connectivity. Challenges encountered, 
such as , provide insight into the practical considerations of designing embedded vision systems. 
Overall, the project successfully integrates machine learning, perception, decision-making, and 
control into a cohesive autonomous robotics system, providing a strong foundation for future 
exploration in embedded AI and smart robotic navigation. 

COE 450/515 Course Project Report – Term 221       
 
References 
Freenove, 
https://docs.freenove.com/projects/fnk0053/en/latest/fnk0053/codes/tutorial/Welcome.ht
ml  
 