# Hexapod Robot
The Hexapod Robot is a six-legged creature similar in dexterity to that of an insect. Modeled after the unusual nature of a crab's design, this hexapod is capable of ambulation, rotation, physical interaction with it's environment, and expressive guestures, while simultaneously performing other varying functions. Controlled by an Arduino MEGA board, this creation is highly versatile, having the potential to be programmed and utilized by the user's free will.  
In the following lines, I have provided an in-depth explanation on how each and every component of this robot was manufactured, designed, and coded. 

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Rohan G | Leland High School | Mechanical Engineering | Incoming Sophomore

## PROJECT SPECIFICATIONS
1. Control Board: Freenove Crawling Robot Controller (recognized as an Arduino MEGA). 
2. Controller: Recognized as an Arduino Uno. 
3. Servos: 18 count, MG90S 9g servos
4. Power Source (Robot): Tenergy NiMH 7.2V 3000mAh Battery Pack.
5. Power Source (Controller): 9V Alkaline Battery
6. Servo Driver: Arduino Nano


<!---**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg) -->
  
# Final Milestone

*** WORK IN PROGRESS ***

# Third Milestone
## CUSTOM CLAWS AND CODE REFINEMENTS

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

***** ADD IMAGE OF FINAL DESIGN *****

MOUNT:
This is the piece that holds both claws, and connects them to the robot's body. As a result, half of the mount is specially styled to fit into the gaps in the acrylic plate, while the other half has a slot-in slider where the supporting arms will slide into. This makes it easier for the printer to focus on printing the mount correctly. 
To start, I took necessary measurements of the acrylic plate on which the mount would attach to. Then, I constructed and printed two iterations of the mount, as shown below:
<img src="https://i.postimg.cc/QxfbMRfB/mount-iterations.jpg" alt="My iPhone photo" width="400">

As depicted, I incorporated a clip-like structure to make the mount easy to take off and reattach. This was because the USB port of the Arduino MEGA was directly in the way of the mount. 
Creating an efficient clip was a struggle, and was why I needed multiple iterations. It's important to ensure that the clip is thin enough for it's structure to bend, but not so thin that it breaks off. I went with ~0.75mm.
Furthermore, since this was a prototype, I made sure to create gaps in the design, removing filament where it was not needed in order to conserve it.

*** WORK IN PROGRESS ***


<!--- **Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**
For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE
-->


# Second Milestone
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

### Next Steps:
After this milestone, I worked on the software and hardware part for my first modification - adding claws.
<!---**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**


For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone -->

# First Milestone
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
