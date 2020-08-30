# Rover-Follower-Pair

This project was done as an assignment for Virginia Tech ECE 4534 Embedded System Design. In order to avoid violating the honor code in case this assignment is reused, the full project will not be uploaded.

This is my Senior Design project for my Embedded System Design course. The problem statement required that the team design a rover that would find an object on a field with obstacles. With a four man team, each person must be responsible for a single autonomous part.

The final design consisted of a two rovers, a leader and follower pair. The leader rover would search for the object and signal the follower rover when it should continue following. When the leader rover finds the object, a robotic arm mounted on the leader rover would pick up the object and place it onto the follower rover. The design was broken up into four autonomous parts: navigation, driving the rover, movement of the robotic arm, and the follower rover. I was responsible for the follower rover.

Hardware:
- TI CC3220SF
- Pixy2
- Force sensor
- Sharp IR sensor
- Zumo Rover
- ChipKit Motorshield

![](https://github.com/hanipman/Rover-Follower-Pair/blob/master/images/still.png)

The rover uses FreeRTOS to meet real time constraints. MessageQueues are used to communicate between other threads and interrupts.

The Pixy2 is used to detect the signal on the leader rover by setting it to look for a specific color. In this case the color to look for is red. The leader rover would have an array of red leds on its rear as a signal. The Pixy2 would be queried via SPI using a software timer interrupt and the x coordinate parsed out. This value is sent to the thread consisting of a PID algorithm to determine the direction of the rover.

The Sharp IR sensor is used for object detection. The rover would stop if it comes within 10-15 cm of the target. The IR sensor is connected via an analog pin. A custom distance equation was created by measuring out the distance to voltage data. A software timer would send the readings to the thread controlling thread movement to tell it when to stop.

The Zumo rover consisted of two motors connected via PWM and and the direction of the motors via GPIO. The motor encoders are connected via GPIO and uses the Capture driver from TIs SimpleLink API, which essentially acts like a GPIO interrupt. The motors are controlled via a PID control thread.

The force sensor is used to detect if an object is caught by the follower rover. It is connected via analog pin and its voltage measured through a software timer interrupt.

The follower consists of three states: following, searching, and waiting.

In the following state the rover waits for the signal and only moves forward when the signal is detected.

![](https://github.com/hanipman/Rover-Follower-Pair/blob/master/images/following_resize.gif)

In the searching state the rover spins in place until it finds an object of the set color. The rover will enter this state from the following state if no signal is found within a duration.

![](https://github.com/hanipman/Rover-Follower-Pair/blob/master/images/following_resize.gif)

The waiting state is exclusive for when the object is found. The rover can break this state only by successfully catching the object. At this point the follower rover goes into the following state.

![](https://github.com/hanipman/Rover-Follower-Pair/blob/master/images/following_resize.gif)
