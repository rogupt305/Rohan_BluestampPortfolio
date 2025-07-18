# Hexapod Robot 🤖
The Hexapod Robot is a six-legged creature similar in dexterity to that of an insect. Modeled after the unusual nature of a crab's design, this hexapod is capable of ambulation, rotation, physical interaction with it's environment, and expressive guestures, while simultaneously performing other varying functions. Controlled by an Arduino MEGA board, this creation is highly versatile, having the potential to be programmed and utilized by the user's free will.  
In the following lines, I have provided an in-depth explanation on how each and every component of this robot was manufactured, designed, and coded. 

| **Engineer** | **School** | **Project Type** | **Grade** |
|:--:|:--:|:--:|:--:|
| Rohan G | Leland High School | Mechanical Engineering | Rising Sophomore

## PROJECT SPECIFICATIONS 📄
1. Control Board: Freenove Crawling Robot Controller (recognized as an Arduino MEGA). 
2. Controller: Recognized as an Arduino Uno. 
3. Servos (motors): 18 count, MG90S 9g servos, rotating 180° DC motor
4. Power Source (Robot): Tenergy NiMH 7.2V 3000mAh Battery Pack.
5. Power Source (Controller): 9V Alkaline Battery
6. Servo Driver: Arduino Nano
7. Bluetooth Reciever (One on the controller and one on the robot control board)
8. WLAN module: Controls the robot via Wi-Fi, usually from a computer or phone
9. LED Lights: W2812 lights
10. Ultrasonic Sensor: HC-SR04
11. Breadboard: Adafruit PermaProto Pi Small-size Breadboard, ~46mm x ~57mm

ARDUINO LIBRARIES:  
LED Lights: FastLED
Servos: Wire, Servo

# HOW PARTS WORK:
Servos: The servos I mentioned are micro servo motors, weighing around 9 grams each. They rotate up to 180 degrees, in a discontinuous motion. To control them, PWM (pulse-width modulation) signals are sent from the Arduino. 
A 1 millisecond pulse makes the servos turn to around 0 degrees. 
A 1.5 millisecond pulse makes them move to 90 degrees.
A 2 millisecond pulse turns it to around 180 degrees. 
These signals are usually sent around every 20 milliseconds, to keep the servo at the desired angle. 
Inside each MSG90S servo is a small DC motor for rotation, a set of gears to reduce speed and increase torque, a potentiometer to detect the current angle, and a control circuit to move the motor to achieve the desired angle. 

Bluetooth Modules: These are small, wireless devices that enable the robot and controller talk to each other. These modules send a stream of numbers, each linked to an order (See Milestone 3: How the code works) every time an input is detected on the controller. These modules work using short-range radio waves, of up to 10 meters, and are relatively easy to use with Arduino. 

WLAN Module (Wi-Fi Control): Similar to the bluetooth module. This module enables a Wi-Fi network called "Hexapod Robot", which is then available for connection to a device. Being more versatile, this module can be programmed to send data to and from the robot and the device (for example, the robot can be programmed to send sensor readings or status updates back). The Wi-Fi is more helpful for longer-range control than Bluetooth, especially if they are on the same home network. 

# A Pinch of Salt
While I’m proud of the outcome of my project, there are a few areas where I believe I could have improved or taken better advantage of the opportunities available.

First, the claw design was not as efficient as I initially envisioned. I originally planned a horizontally-oriented claw, but the configuration required too much space and interfered with the robot’s front legs. As a result, I had to recalibrate the leg positions, which negatively affected the robot’s walking ability and  speed. Ultimately, I opted for a vertically-opening, scooping claw instead. While this design improved compatibility with the robot’s movement, it lacked the gripping ability of a horizontal mechanism and limited the claw’s functionality.

Second, I realized that the project offered far more potential on the coding side than I fully explored. In total, I only wrote or modified approximately 850 lines of code, with most of my focus directed toward hardware. In hindsight, I could have invested more time in software enhancements. For example, the ultrasonic sensor was only used to trigger an LED, but it could be leveraged for far more sophisticated applications, such as object detection or navigation.

Based on my experience, I would encourage others looking to modify this project to consider the following:

1. Explore Autonomous Motion Through Code Enhancements:
The robot features a robust hardware and software foundation that lends itself well to autonomous movement. Its existing codebase can be easily adapted to incorporate sensors like the ultrasonic module, allowing for obstacle detection or semi-autonomous navigation. Thanks to the robot’s stable six-legged design, which offers balanced and reliable locomotion, there’s ample room to experiment without compromising movement quality.

2. Prioritize Function Over Form in Claw Design:
If a claw is part of your modification plan, I recommend starting with a horizontally-closing mechanism mounted farther from the robot’s body. Although it may seem less visually appealing, this configuration offers superior gripping capability and overall performance. With that said - it is mostly up to you - if you prefer functionality over visuals.


# Modification Milestone 💻🗜️
## CUSTOM CLAWS, CODE REFINEMENTS AND LED LIGHTS

### How the code works
A crucial modification that had to be done before I could design my first modification, a set of two claws, was with the code. The actual code was rather complex, but the idea was relatively simple - after establishing definitions, declarations (Example: defining the capabilities of certain functions) and orders (Example: Turn right, left, activate sleep mode, etc.), the Arduino Uno controller would send one byte per second to the robot control board based on user inputs. Each byte contains 256 different numbers from a range of 1-255. Being attached and correlated directly with an order, these numbers were the way that the controller relays information to the control board in a wireless manner. 
However, for a person with little experience in the domain of software prior to this, I found myself consulting an instructor numerous times.
Here's what I managed to do:

I went into the "FNHROrders.cpp" (FNHR simply stands for "Freenove Hexapod Robot") section of the MEGA's code, and established my own custom orders, setting them equal to distinct numbers: 
LINES: 75 - 77
```C++
  static const byte orderOpen = 67;
  static const byte orderClose = 72;
  static const byte orderStop = 79;
```

Following this, I had to incorporate an Arduino Nano board into my setup, due to having no extra space to add servos on the robot control board (Arduino MEGA). From this point onwards, the first main hurdle was getting the Arduino MEGA to communicate with the Arduino Nano. 
After wiring the Nano to the MEGA, a simple line in the Communications (FNHRComm.cpp) section of the MEGA code got it to work: 
LINE: 31
```C++
Wire.begin(); //set up background, introduce/ensure that wires are properly set up in SDA and SCL pins
```

Here's how the Arduino Nano was wired:

<img src="https://i.postimg.cc/dVGzpQsM/arduino-nano-connection.jpg" alt="My iPhone photo" width="400">

FIGURE 1: Arduino Nano Wiring

Following this, the Arduino Nano needed to be "informed" of the orders it was to execute, as well as how to efficiently execute them. I created a new Arduino.ide file, then I wrote and uploaded the following code to the Arduino NANO. (NOTE: I have included comments in the code detaling what each section does)
LINES: 1 - 52
```C++
#include <Servo.h>
#include <Wire.h>

Servo servoL{};
Servo servoR{};

//ORDERS - From robot controller to Nano
static const byte orderOpen = 67;
static const byte orderClose = 72;
static const byte orderStop = 79;
byte orderCheck = orderStop;

//SETUP: Essentially setting up the wire comms., a few test runs, and specific pins where servos are attached to. 
void setup()
{
  servoL.attach(10);
  servoR.attach(9);
  servoL.write(100);//test
  servoR.write(100);//test
  Wire.begin(92);
  Wire.onReceive(cmd_ON);
  Serial.begin(9600);
};

//CHECKS: The following lines are designed to pinpoint which command is being sent to the Arduino, and what to do for each command
void loop()
{
  delay(42);

  if(orderCheck == orderOpen)
  {
    servoL.write(servoL.read() + 1);
    servoR.write(servoR.read() + 1);
  }

  if(orderCheck == orderClose)
  {
    servoL.write(servoL.read() - 1);
    servoR.write(servoR.read() - 1);
  }

  if(orderCheck == orderStop)
  {
    servoL.write(servoL.read()); //technically no need to write, but this keeps servo in same pos.
    servoR.write(servoR.read()); //read prev. line
  }
};

void cmd_ON() //runs only when recieved cmd from board (custom function name)
{
  orderCheck = Wire.read(); 
  Serial.println("Recieved Data");
}
```

With that successfully out of the way, the next step was to calibrate the Arduino MEGA to be able to talk to the Arduino Nano, by writing a few extra lines of code in the Communications (FNHRComm.cpp) tab on the MEGA. (Comments are added in necessary locations). 
LINES: 534 - 560
```C++
//From a chain of previous "if" statements, three else ifs for each command

else if (blockedOrder == Orders::orderOpen)
  {
    Wire.beginTransmission(92); //begin transmission to arduino nano
    Wire.write(Orders::orderOpen); //carry out the process
    Wire.endTransmission();
    //TESTING
    Serial.println("Send Open Message"); //This line of code sends a message in the Serial Monitor on the Arduino App. Now unused, this helped ensure that the commands were running, to troubleshoot when the servos did not move as expected
  }

//The following lines operate in the same way

  else if(blockedOrder == Orders::orderClose)
  {
    Wire.beginTransmission(92); 
    Wire.write(Orders::orderClose); 
    int code = Wire.endTransmission();
    if((code) != 0)
    {
      Serial.println(code);
    }
    Serial.println("Send Closed Message");
  }

  else if(blockedOrder == Orders::orderStop)
  {
    Wire.beginTransmission(92); //begin transmission to arduino nano
    Wire.write(Orders::orderStop); //carry out the process
    Wire.endTransmission();
    Serial.println("Send Stopped Message");
  }
```

From here, the final step was to figure out which input terminal on the controller would be used to control the servos, and code for that. I decided to go with the two potentiometers, labeled as "pot1" and "pot2" in the code. I chose them because their original function, to raise the height of the body, didn't seem very relevant to me. Furthermore, turning them on didn't even lift the body, indicating an error in the code.
I deleted the previous code, then wrote the following in the Remote ("FNHRremote.cpp") tab of the Arduino UNO Controller: 
LINES: 138 - 152
```C++
/* The potentiometer goes from 0-1024, depending on how far open it is.
   I decided to go with 1/3 of this amount, meaning that the potentiometer
   needs to be intentionally moved a distance to work. This accounts for accidental movements.*/

 if(pot1Value >= 341) 
  {
    rf24OutData[rf24OutDataCounter++] = Orders::orderOpen;
  }
  else if(pot2Value >= 341) //else if makes it either/or, so that only one command is sent
  {
    rf24OutData[rf24OutDataCounter++] = Orders::orderClose;
  }
  else //if the potentiators are "off", stop sending orders
  {
    rf24OutData[rf24OutDataCounter++] = Orders::orderStop;
  }
```
With that, the software for my first modification was working. 

### Using Fusion 360 (CAD) to digitally model my project
I split up the claw design into three main sections - a mount, two supporting arms, and two "scoopers". Below is an image of the final prints of each. NOTE: for the scoopers, I decided to use the pre-made acrylic leg parts, because I found that their design was ideal not only for walking, but for scooping up items as well. 
The main idea was that the mount would be attached to the acrylic plate. Directly attached to the mount, the supporting arms would also be attached to a servo each, which would vertically move the scoopers. I had to split up the entire project into three parts, since the angular shapes and attention to detail would have been extremely difficult to reproduce on a 3-D printer if the parts were not printed individually.

MOUNT:
This is the piece that holds both claws, and connects them to the robot's body. As a result, half of the mount is specially styled to fit into the gaps in the acrylic plate, while the other half has a slot-in slider where the supporting arms will slide into. This makes it easier for the printer to focus on printing the mount correctly. 
To start, I took necessary measurements of the acrylic plate on which the mount would attach to. I then found the exact CAD replica for the base plate from online searches. Then, I constructed and printed two iterations of the mount, as shown below:

<img src="https://i.postimg.cc/QxfbMRfB/mount-iterations.jpg" alt="My iPhone photo" width="400">

FIGURE 2: Mount Iterations

As depicted, I incorporated a clip-like structure to make the mount easy to take off and reattach. This was because the USB port of the Arduino MEGA was directly in the way of the mount. 
Creating an efficient clip was a struggle, and was why I needed multiple iterations. It's important to ensure that the clip is thin enough for it's structure to bend, but not so thin that it breaks off. I went with ~0.75mm.
Furthermore, since this was a prototype, I made sure to create gaps in the design, removing filament where it was not needed in order to conserve it.

The next step was to create the claw arm. This step was relatively easy, and didn't involve many iterations due to a straightforward design. Depicted below is my final design:
<img src="https://i.postimg.cc/tJN7hvWs/Whats-App-Image-2025-07-16-at-09-25-35-5c1b6203.jpg" alt="My iPhone photo" width="400">

FIGURE 3: Claw Arm Mount

After printing one claw mount and two claw arms, I centered my servos and hot-glued the entire design:
<img src="https://i.postimg.cc/L4LP9NYK/Whats-App-Image-2025-07-16-at-09-28-10-b26f02ce.jpg" alt="My iPhone photo" width="400">

FIGURE 4: Final Design of the Claw

Following this, I soldered all of my connections to the Adafruit PermaPro Pi Small-size Breadboard. This breadboard is perfect for this project due to it's small size, and power rails for ground and power. 

Then, I used Fusion to CAD a top "shell" that wraps around the structure of the acrylic plate. To do this, I used the base plate CAD file, then created a design on around  it. This was also fairly straightforward, only requiring one iteration before the final design. 

### Adding and Coding LED lights and the Ultrasonic Sensor
After the claw was fully functional, the next step was to add LED lights to my project. This involved a lot of soldering, wire measurement and accurate placement of the lights. The main idea for the LED lights was to code different sequences for different states of being for the robot. For example, when "idle" (not moving at all), the robot would simply emit a blue light from underneath. When moving, however, the robot would pulse a blue light to indicate motion. When the claw is moving, the lights would flash yellow and blue, and when the robot is close to an object, it would flash red (this is where the ultrasonic sensor comes in). Adding the LED lights themselves was fairly straightforward, however the code was a bit challenging. 

I added code to the servo driver (Arduino Nano), and soldered the connections to this board as well. Here are the new lines of code that I added:
``` C++
/* #include <Servo.h>
#include <Wire.h> */
#include <FastLED.h>

CRGB leds [3]; //Array of three LED lights

const int trigPin = 8; //location of the "trig" pin from the ultrasonic sensor
const int echoPin = 7; //location of the "echo" pin fron the ultrasonic sensor

/* Servo servoL{};
Servo servoR{}; */

//ULTRASONIC
 int distance;

//LEDS
struct color
{
  byte red;
  byte green;
  byte blue;
};

color blink1;
color blink2;
color blink3;

int pulseDirection1 = 3;
int pulseDirection2 = 40;
int brightness = 0; //This is for pulsing blue - this variable is assigned to the "blue" section of RGB,
// and will change actively, altering the brightness

int timer = 0;
int TbetweenBlnk = 1;
int currentColor = 2;

//ORDERS - From robot controller to Nano
/* static const byte orderOpen = 67;
static const byte orderClose = 72;
static const byte orderStop = 79;
*/
//Two new orders - help distinguish if the robot is moving or stationary (idle)
static const byte orderMoving = 59;
static const byte orderStation = 60;

byte orderCheck = orderStop;
bool moving = false;

void cmd_ON();

void setup()
{
/*  //SERVOS
  servoL.attach(10);
  servoR.attach(9);
  servoL.write(100);//test
  servoR.write(100);//test
  Wire.begin(92);
  Wire.onReceive(cmd_ON);
  Serial.begin(9600);
*/
//LEDs
  FastLED.addLeds<WS2812,12,GRB>(leds,3);
  /*leds[0] = CRGB(0,0,100);//(0-255,0-255,0-255)
  leds[1] = CRGB(0,0,100);//(0-255,0-255,0-255)
  leds[2] = CRGB(0,0,100);//(0-255,0-255,0-255)
  */

  blink1.red = 255;
  blink2.blue = 255;
  blink1.green = 255;
  blink2.green = 0;
  blink1.blue = 0;
  blink2.red = 0;

  //ULTRASONIC SENSOR
  Serial.begin(9600);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
};

//ULTRASONIC
long microsecondsToCentimeters(long microseconds) 
{
  // The speed of sound is 343 m/s or 0.034 cm/µs
  return microseconds / 29 / 2;
}

void pulseBlue()
{  //runs every 40 milliseconds
  leds[0] = CRGB(0,0,brightness);
  leds[1] = CRGB(0,0,brightness);
  leds[2] = CRGB(0,0,brightness);

//Increase or decrease the brightness
  if(brightness > 254 && pulseDirection1 > 0)
  {
    pulseDirection1 = -pulseDirection1;
  }
  if(brightness < 1 && pulseDirection1 < 0)
  {
    pulseDirection1 = -pulseDirection1;
  }
  brightness = brightness + pulseDirection1;
}

void blue()
{
  leds[0] = CRGB(0,0,255);
  leds[1] = CRGB(0,0,255);
  leds[2] = CRGB(0,0,255);
  
}

void yellowBlue()
{
  timer = timer + 1;
  if(timer > TbetweenBlnk)
  {
    timer = 0;
    if(currentColor == 1)
    {
      currentColor = 2;
      leds[0] = CRGB(blink2.red,blink2.green,blink2.blue);
      leds[1] = CRGB(blink2.red,blink2.green,blink2.blue);
      leds[2] = CRGB(blink2.red,blink2.green,blink2.blue);
    }
    else
    {
      currentColor = 1;
      leds[0] = CRGB(blink1.red,blink1.green,blink1.blue);
      leds[1] = CRGB(blink1.red,blink1.green,blink1.blue);
      leds[2] = CRGB(blink1.red,blink1.green,blink1.blue);
    }
    
   
  }
}

void blinkRed()
{
  leds[0] = CRGB(brightness,0,0);

  if(brightness > 254 && pulseDirection2 > 0)
  {
    pulseDirection2 = -pulseDirection2;
  }

  if(brightness < 1 && pulseDirection2 )
  {
    pulseDirection2 = -pulseDirection2;
  }

  brightness = brightness + pulseDirection2;
}

void blinktask() //blinktask is the function which defines when to activate a specific sequence of lights.
{
  if(10*distance < 68) //distance from ultrasonic sensor, multiplied by 10 to convert from centimeters to millimeters.
  {
    blinkRed();
  }
  else if(orderCheck == orderOpen || orderCheck == orderClose)
  {
    redBlue();
  }
  else if(moving == true)
  {
    pulseBlue();
  }
  else if(moving == false)
  {
    blue();
  }
  FastLED.show();
}

void loop()
{
  // Clear the trigPin
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  // Sets the trigPin on HIGH state for 10 micro seconds
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  // Reads the echoPin, returns the sound wave travel time in microseconds
  long duration = pulseIn(echoPin, HIGH);
  // Calculating the distance
  distance = microsecondsToCentimeters(duration);
  // Prints the distance on the Serial Monitor
  Serial.print("Distance: ");
  Serial.print(10*distance);
  Serial.println(" mm");
  delay(40);

  blinktask();
  Serial.println(moving); //testing if moving detected, in the serial monitor

 /* if(orderCheck == orderOpen)
  {   
    servoL.write(servoL.read() + 1);
    servoR.write(servoR.read() - 1);
  }

  if(orderCheck == orderClose)
  {
    servoL.write(servoL.read() - 1);
    servoR.write(servoR.read() + 1);
  }
*/
  if(orderCheck == orderStop)
  {
    FastLED.show();
   /* servoL.write(servoL.read()); //technically no need to write, but this keeps servo in same pos.
    servoR.write(servoR.read()); //read prev. line
  }

  if(servoL.read() < 25)
  {
    servoL.write(servoL.read() + 1);
  }

  if(servoR.read() < 30)
  {
    servoR.write(servoR.read() + 1);
  }
};

void cmd_ON() //runs only when recieved cmd from board (custom function name)
{
  byte command = Wire.read(); //wire.read tells which number robot board sends, set that equal to orderCheck so knows which order */
  if(command == orderMoving)
  {
    moving = true;
  }
  else if(command == orderStation)
  {
    moving = false;
  }
  else
  {
    orderCheck = command;
  }
  Serial.println("Recieved Data");
}

```
I also added two new orders (orderMoving and orderStation) to the MEGA's code in the Orders.h section (see above for how to add orders).

### General Tweaks to Improve Stability
When I constructed the base model for the robot, I noticed a few things about it - firstly, the robot legs continuously slipped while walking on slick floors, such as the table or the ground. To counteract this, I applied a few layers of hot glue to the tip of each leg, to cushion the legs, reduce servo burnout, raise the body slightly, reduce noise while walking, and improve grip. Furthermore, I ensured that all additional wires that I added did not go through the mess of wires associated with the 18 servos. This makes the robot have a clean look, while being organized with cable management. Lastly, I mounted the battery on the bottom, and used zip ties to make it more secure. 


This concludes my modification milestone. To recap, I:
1. Added claws
2. Used CAD to design and 3-D print mounts and a shell for the claws
3. Used a new board (Arduino Nano) for driving servos, LEDs, and ultrasonic sensor
4. Modified the Hexapod and the Remote code
5. Made my own code for the Arduino Nano
6. Mounted and coded an Ultrasonic Sensor
7. Added and coded LED lights
8. Added hot glue to the tip of the legs
9. Soldered all of my connections permanently to a board


# Second Milestone 🖥️
## CODING & MOVEMENT CALIBRATION

<iframe width="560" height="315" src="https://www.youtube.com/embed/D_x8IfXiv48" title="Rohan G. Milestone 2" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Background Information
The hexapod robot's main code was prewritten, and simply needed to be uploaded to the computer. In addition to the code, the developers of the robot designed an additional software that could be run on the computer. This software was fully equipped to connect to the robot, control it, and calibrate the standard position of it's six legs. However, the software's UI was poorly designed, and it took a lot of troubleshooting to make the software perform it's intended functions. 

### Accomplishments
In this milestone, I accomplished the following:
1. Downloaded the Processing and Arduino app
2. Installed the necessary drivers (some of them were pre-installed, some of them were available to install on the Arduino IDE app and some were given to me by the robot developers)
3. Uploaded the code to the remote and to the robot control board
4. Fixed a significant issue with the Wireless Modules on the controller and the robot control board (this is how they communicate with each other) - the Modules were not connecting with each other, and I realized that it was due to interference between a different person's Module.
5. Calibrated the robot's legs to a standard position (the position of the servos deviated from their standard position during assembly, this is to recorrect that)
6. Mounted the battery on the bottom of the robot, recalibrated the legs to accomodate

### Challenges
Before calibrating the robot, I realized that I had completely miswired the servo motors. This proved to be a huge setback, because I had to manually go back and re-wire every servo solely by trial and error. 
The reason for this is because when calibrating the robot using the UI App, the app has pre-set locations for each servo on the pinout map. If the servo is not plugged into the correct pin, the robot will wrongly move the servo. 
As mentioned earlier, utilizing the poorly designed UI proved to be an existential challenge to overcome when calibrating the robot's legs. When altering their position in the three dimensional (x, y, z) plane, altering one of these variables moved the robot's leg through a 2-d plane, instead of the ideal one dimensional line. Furthermore, different controls did this unpredictably, making it extremely time-consuming to get the robot's leg in the correct place. In the end, I had to resort to manually removing the servos and reattaching them at a different angle, which slightly displaced their range of motion. Luckily, this did not cause significant change to the robot's final movement. 

The robot sitting on the calibration mat:

<img src="https://i.postimg.cc/fbG5NmBB/calibrate.jpg" alt="My iPhone photo" width="400">

FIGURE 5: Robot Calibration

### Next Steps:
After this milestone, I worked on the software and hardware part for my first modification - adding claws.
<!---**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**


For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone -->

# First Milestone 🔧
## HARDWARE & CONSTRUCTION

<iframe width="560" height="315" src="https://www.youtube.com/embed/L9QgHenpvF0" title="Rohan G. Milestone 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Accomplishments
My primary project milestone was the hardware and construction of the robot. With 18 small servos, each operating one of three joints on one of the six legs, the assembly proved to be an extremely tedious process, involving a plethora of screws and accurate attachment of servos. During this process, I accomplished the following:
1. Assembled the joints for each leg
2. Connected each leg to the control board's encasement plate (which served as the main chassis)
3. Wired servos to the underside of the board (containing the pins for ground, power and servo)
4. Cable management (18 servos result in a lot of loose cables!)
5. Fixed the servos into their respective brackets
6. Installed the WNIC (Wireless Network Interface Card) to Arduino MEGA to potentially control the robot via Wi-Fi hotspot
7. Installed the Wireless Module to Arduino MEGA (Another will be connected to a programmed controller) to potentially control the robot directly via the controller

### Challenges:
As mentioned earlier, the assembly, specifically the screws, was a time-consuming process. This was partly due to the screws' small size and extremely tight fit, which made them difficult to properly screw into the acrylic plates. However, this resulted in a far more sturdy and rigid frame, and one that was able to efficiently encase the control board and other electronic parts. 
With that being said, however, it was extremely challenging to manage the mess of cables from the servos, due to the robot's compact nature. Furthermore, the sharp, angular shape of the plate encasing the board resulted in a few exposed copper wires, as the servo cables scraped against it frequently. In the end, I resorted to smoothening out the edges by applying layers of electric tape, as well as lightly zip-tying around the mass of cables. 

Below is an image of the cables. Applying the yellow electrical tape on the edges of the acrylic plate smoothened them out, reducing the risk of exposed wires:

<img src="https://i.postimg.cc/mgrmNbPN/cables-more2.jpg" alt="My iPhone photo" width="400">

FIGURE 6: Cable Management

### Next Steps:
After this milestone, I worked on the code for the robot, and its calibration. 

<!--- **Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/CaCazFBhYKs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project -->

# Starter Milestone
<iframe width="560" height="315" src="https://www.youtube.com/embed/FzQ8njRQAnI?si=v1wpNMm2m4P6HU-q" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Description:
Before diving into the design, construction and code for my main project, I assembled a guided "starter" project through BlueStamp to gauge my soldering and circuitry skills. Inspired by the erratic, insect-like movement that it emulates, I assembled and tested the "Jitterbug" - a small robot that scuttles along flat surfaces with jittery unpredictability. 
The main idea behind the creation follows a simple series circuit, with no programming involved. Powered by a button battery, the flow of electricity proceeds to a switch, before passing through two LEDs and a vibration motor, which drives the robot's jittery nature. Each component is meticulously soldered to the insect-shaped board to ensure that the circuit stays securely in place for the longest period of time. 

### Challenges:
While constructing this project, I ran into one main challenge - due to the board's highly compact design, it was difficult to solder the wires without accidentally creating a connection between two of them, which could cause a short circuit. I ran into this issue a few times. To resolve the problem, I simply re-heated the solder with a hot iron, and then meticulously guided the solder back into place. 

### Next Steps:
After this milestone, I got ready to work on my main project. 


# Schematics 
<!---Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. -->
Milestone 1 & 2: Servos to Arduino Mega. The servos are connected from pins 22-39. 
NOTE: The actual Freenove board is merely "recognized" as Arduino Mega. In reality, the board does not look like this. Ensure that the VCC and GND wires are properly connected in their color-coded pins. 

<img src="https://i.postimg.cc/76VRjKxt/Screenshot-2025-07-03-141546.png" alt="My iPhone photo" width="400">

Milestone 3: Connection between Arduino MEGA and Arduino Nano. 
Once again, note that the Arduino MEGA board is not the same as the actual control board, but the Arduino Nano is. However, pay close attention to the numbers and characters next to each used pin on the Arduino MEGA, as they are identical to that on the control board.

<img src="https://i.postimg.cc/pTsBv737/Screenshot-2025-07-03-143919.png" alt="My iPhone photo" width="400">

# Bill of Materials
<!--Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. -->

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Freenove Hexapod Robot Kit (FNK0031) | Kit containing parts, servos, robot & controller | $126.99 | <a href="[https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/](https://store.freenove.com/products/fnk0031)"> Link </a> |
| Arduino Nano | Used as a servo driver | $24.99 | <a href="[https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/](https://www.amazon.com/Arduino-A000005-ARDUINO-Nano/dp/B0097AU5OU/ref=sr_1_2?dib=eyJ2IjoiMSJ9.UR9t6Z2D5rIVJlr8NPSrk8lsooCrlbXp6PW8NiTHZI1w37ejl4nxF76g50XESv8CvSdeUNV8zFUxEFJnO0XoENPKswfnoc1nYWOzeyCu6o8iyy5vysywuz_CieTA3pa8_88EENz_kXVRjiFUxu2d7ch2FQM_N7aJ38K1Zolt7wA9tvVmW8FOsUWHl6bmxlTPdYmCZjAOrZe_qcgNd0gOxHIP-F1m5sVU9OxnuI5BzQ8.d1XvuVapqP2lwlKgqOHKYPuD-qcKNjp3wiklwwTU32U&dib_tag=se&keywords=arduino+nano&qid=1751580964&sr=8-2)"> Link </a> |

NOTE: The two extra servos that I used for the claw were included in the kit. 

<!--# Other Resources/Examples -->

<!--One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.-->
