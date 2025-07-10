# Ball Tracking Robot
Robot that tracks a ball using a raspberry pi camera and two ultrasonic sensors

| Brayden C | Naperville North High School | Computer Engineering | Incoming Senior |


![Headstone Image](logo.svg)
  
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 

# First Milestone - Assemble the robot and add working ultrasonic sensors

<iframe width="560" height="315" src="https://youtu.be/PMO_mazFVpQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your first milestone, describe what your project is and how you plan to build it. You can include:

How everything works together
- Currently, the robot is comprised of two motors, a raspberry pi, a L298n motor driver, a small breadboard, and two ultrasonic sensors. The image below contains a picture of the schematics with all of the different componenets and how they are powered and controlled electrically

Technical summary: 
- So far, I have assembled the base of the robot with motors and wheels, as well as all of the components previously mentioned, all of which are fully wired and fully functional. For example, I am able to pick up distance readings from both ultrasonic sensors. In addition, I also coded the robot to where it can continuously adjust it's position to be 8-10 cm away from an object by using the ultrasonic sensors and adjusting the speed of the motors accordingly. Currently this feature is only being tested on the left ultrasonic sensor and being applied to both motors for ease of testing 

Challenges:
- So far, I have faced a multitude of challenges, most of which have been due to wiring issues in some capacity. The first one I encountered was using an L298n instead of the motor drivers we were given, as I unfortunately did not recieve the motor drivers, and so instead I decided to improvise by using my own ones. It took a little bit more wiring and slightly different coding, and after some retries with wiring, both motors worked as intended with proper speed control
- The second challenge I faced was with wiring the ultrasonic sensors. The ultrasonic sensors' echo wire needs to have a voltage cap of 3.3V before it can be connected to the raspberry pi (it outputs a signal with 5V), therefore 1K and 2K resistor must be use as not to damage the raspberry pi. At first, I wasn't able to figure out why exactly my wires had an issue, but some time, I realized it was because I connected the resistor and echo wire on different rails and so they weren't actually connected
- The third challenge I faced was with controling the robot with the ultrasonic sensors. I was able to get the robot to move forward above 10 cm of distance (using the formula speed = 0.05*distance+0.4) and get the robot the move backward with a distance under 8 cm, however the issue was that robot would start ocillating between the forward and backwards motion likely due to the robot overshooting the intended distance. Another issue was below around a speed of 0.3-0.4, the robot wasn't able to spin its motors likely due to too low of a voltage and a lack of torque. Very likely I will have to use PID controls to assist with this

Plan to complete your project:
- Attach the camera, code it, and get it to work with the ultrasonic sensors
- After the base project is done, I would like to add a control aspect to the robot, such as using my hand in some way to control it.
  
# Schematics 
<img width="717" height="762" alt="image" src="https://github.com/user-attachments/assets/4e5b79de-aa66-4abd-90b7-6870576d4a89" />

# Code

```python
import RPi.GPIO as GPIO
from gpiozero import Motor
from time import sleep


rightMotor = Motor(forward=17, backward=27, enable=12, pwm=True)
leftMotor = Motor(forward=22, backward=23, enable=13, pwm=True)

GPIO.setmode(GPIO.BCM)
TRIGL = 6
ECHOL = 5
GPIO.setup(TRIGL, GPIO.OUT)
GPIO.setup(ECHOL, GPIO.IN)

TRIGR = 26
ECHOR = 16
GPIO.setup(TRIGR, GPIO.OUT)
GPIO.setup(ECHOR, GPIO.IN)


while True:
      GPIO.output(TRIGL, False)
      time.sleep(0.5)
      
      GPIO.output(TRIGL, True)
      time.sleep(0.00001)
      GPIO.output(TRIGL, False)
      
      pulse_startL = time.time()
      while GPIO.input(ECHOL) == 0:
          pulse_startL = time.time()
          
      pulse_endL = time.time()
      while GPIO.input(ECHOL) == 1:
          pulse_endL = time.time()
      pulse_durationL = pulse_endL - pulse_startL
      distanceL = round(pulse_durationL * 17150,3)
      
      print(f"Distance: {distanceL} cm")

      speedLeft = distanceL*0.005+0.5  # Adjust speed based on distance
      if distanceL > 10 and distanceL < 100:
          speedLeft = distanceL*0.005+0.35
          leftMotor.forward(speedLeft)
          rightMotor.forward(speedLeft)
      elif distanceL <= 10 and distanceL > 8:
          speedLeft = 0
          leftMotor.forward(speedLeft)
          rightMotor.forward(speedLeft)
      elif distanceL <= 8:
          speedLeft = .3
          leftMotor.backward(speedLeft)
          rightMotor.backward(speedLeft)
          sleep(.1)
      else:
          leftMotor.forward(0)
          rightMotor.forward(0)
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| Raspberry Pi Kit | What the item is used for | $95.19 | <a href="https://www.amazon.com/RasTech-Raspberry-Starter-Heatsink-Screwdriver/dp/B0C8LV6VNZ"> Link </a> |
| Robot Chassis | What the item is used for | $18.99 | <a href="https://www.amazon.com/Smart-Chassis-Motors-Encoder-Battery/dp/B01LXY7CM3"> Link </a> |
| Screwdriver Kit | What the item is used for | $5.94 | <a href="https://www.amazon.com/Small-Screwdriver-Set-Mini-Magnetic/dp/B08RYXKJW9"> Link </a> |
| Ultrasonic Sensor | What the item is used for | $9.99 | <a href="https://www.amazon.com/WWZMDiB-HC-SR04-Ultrasonic-Distance-Measuring/dp/B0CQCCGXCP"> Link </a> |
| Pi Cam | What the item is used for | $12.86 | <a href="https://www.amazon.com/gp/product/B07RWCGX5K"> Link </a> |
| Motors | What the item is used for | $11.98 | <a href="https://www.amazon.com/AEDIKO-Motor-Gearbox-200RPM-Ratio/dp/B09N6NXP4H"> Link </a> |
| Electronics Kit | What the item is used for | $11.98 | <a href="https://www.amazon.com/EL-CK-002-Electronic-Breadboard-Capacitor-Potentiometer/dp/B01ERP6WL4"> Link </a> |
| SD Card Adapter | What the item is used for | $9.99 | <a href="https://www.amazon.com/dp/B081VHSB2V?"> Link </a> |
| DMM | What the item is used for | $11.00 | <a href="https://www.amazon.com/AstroAI-Digital-Multimeter-Voltage-Tester/dp/B01ISAMUA6"> Link </a> |
| Champion sports ball | What the item is used for | $16.73 | <a href="https://www.amazon.com/Champion-Sports-Inch-Coated-Density/dp/B000KYTTYO"> Link </a> |
| AA batteries | What the item is used for | $18.74 | <a href="https://www.amazon.com/Duracell-Coppertop-AA-Ingredients-Long-lasting/dp/B0035LCFNQ"> Link </a> |
| USB power bank & cable | What the item is used for | $16.19 | <a href="https://www.amazon.com/SIXTHGU-Portable-Charger-Charging-Flashlight/dp/B0C7PHKKNK"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
