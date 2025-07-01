# Hexapod Robot
The Hexapod Robot is a six-legged creature similar in dexterity to that of an insect. Modeled after the elegance of a spider's design, this hexapod is capable of ambulation, rotation, and expressive gestures, while also performing various functions. Controlled by an Arduino MEGA board, this creation is highly versatile, having the potential to be programmed by the user's free will.  

<!---Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails!

You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:
 -->

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Rohan G | Leland High School | Electrical Engineering | Incoming Sophomore

## PROJECT SPECIFICATIONS
Control Board: Freenove Crawling Robot Controller (recognized as an Arduino MEGA). 
Controller: Recognized as an Arduino Uno. 
Servos: 18 count, MG90S 9g servos, 
Power Source (Robot): Tenergy NiMH 7.2V 3000mAh Battery Pack.
Power Source (Controller): 9V Alkaline Battery

<!---**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg) -->
  
# Final Milestone

# Third Milestone
## CUSTOM CLAWS AND CODE REFINEMENTS

### How the code works
A crucial modification that had to be executed before I could design the claw regarded the code. The actual code was rather complex, but the idea was relatively simple - after establishing definitions, declarations (Example: defining the capabilities of certain functions) and orders (Example: Turn right, left, activate sleep mode, etc.), the Arduino Uno controller would send one byte per second to the robot control board based on user inputs. Each byte contains 256 different numbers from a range of 1-255. Being attached and defined directly to an order, these numbers were the way that the controller relays information to the control board in a wireless manner. 
However, for a person with little experience in the domain of software prior to this, I found myself consulting an instructor numerous times.
Here's what I managed to do:
I went into the "Orders" section of the code, and established my own custom orders, setting them equal to distinct numbers:

### Accomplishments
In this milestone, I added fully-functioning claws to my project. To successfully accomplish this, I had to modify the code to include two additonal servos:
```C++
  static const byte orderOpen = 67;
  static const byte orderClose = 72;
  static const byte orderStop = 79;
```


<!--- **Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE
-->


# Second Milestone
## CODING & MOVEMENT CALIBRATION
### Background Information
The hexapod robot's main code was prewritten, and simply needed to be uploaded to the computer. In addition to the code, the developers of the robot designed an additional software that could be run on the computer. This software was fully equipped to connect to the robot, control it, and calibrate the standard position of it's six legs. However, the software's UI was poorly designed, and it took a lot of troubleshooting to make the software perform it's intended functions. 

### Accomplishments
In this milestone, I accomplished the following:
1. Downloaded the Processing and Arduino app
2. Installed the necessary drivers (some of them were pre-installed, some of them were available to install on the Arduino IDE app and some were given to me by the robot developers)
3. Uploaded the code to the remote and to the robot control board
4. Fixed a significant issue with the Wireless Modules on the controller and the robot control board (this is how they communicate with each other) - the Modules were not connecting with each other prior to this tweak
5. Calibrated the robot's legs to a standard position (the position of the servos deviated from their standard position during assembly, this is to recorrect that)
6. Mounted the battery on the bottom of the robot, recalibrated the legs to accomodate
7. Polished off the looks and movement of the robot

### Challenges
As mentioned earlier, utilizing the poorly designed UI proved to be an existential challenge to overcome when calibrating the robot's legs. When altering their position in the three dimensional (x, y, z) plane, altering one of these variables moved the robot's leg through a 2-d plane, in comparison to a one dimensional line. Furthermore, different controls did this unpredictably, making it extremely time-consuming to get the robot's leg in the correct place. In the end, I had to resort to manually removing the servos and reattaching them at a different angle, which slightly displaced their range of motion. Luckily, this did not cause significant change to the robot's final movement. 

<!---**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone -->

# First Milestone
## HARDWARE & CONSTRUCTION
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


# Schematics 
<!---Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. -->

# Code
<!--Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. -->

```c++
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  Serial.println("Hello World!");
}

void loop() {
  // put your main code here, to run repeatedly:

}
```

# Bill of Materials
<!--Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. -->

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# Other Resources/Examples
<!--One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.-->
