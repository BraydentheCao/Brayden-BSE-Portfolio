# Ball Tracking Robot
Robot that tracks a ball using a raspberry pi camera and two ultrasonic sensors

| Brayden C | Naperville North High School | Computer Engineering | Incoming Senior |


![Headstone Image](logo.svg)
  
# Final Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone: PID, camera web streaming, ball tracking, camera distance tracking, and finished project

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 

## Technical summary 

I've completed the base project! To get there, I've accomplished:
- Streaming the camera video in a web browser
- Enabling the camera to track the ball using hsv/color detection and contours (outlining the ball) to find the center of the ball
- Creating a PID algorithm that allows the robot to properly rotate itself to the ball using the its center
- Finish a complete ball tracking algorithm that integrates this new PID angle alignment algorithm with the previously finished distance alignment algorithm 

I've also accomplished:
- An in progress addition of tracking the ball's distance with the camera using the pinhole camera model
- Redoing the wiring and layout of the electronics to be more compact
- Switching around the electrical componenets for better optimization

## Technical details

### Streaming with the camera

What is it? 
- A live video from my robot's raspberry pi camera 

How does it work? 
- A function called generate_frames() captures camera frames, processes them, encodes them as JPEG, and streams them using an HTTP format (multipart/x-mixed-replace).
- /route serves an HTML page (basically html embedded into my python code), which shows the live video.
- /video_feed route streams the processed video as a continuous sequence of JPEG images, simulating a video feed in the browser.
- The app.run() function starts a local web server on port 5000, so any device on the network can view the stream at http://<raspberry-pi-ip>:5000/
- Methods such as cv2.putText(...) allow information and text to be displayed and updated within the video


### Enabling the camera to track the ball

What is it? 
- Converts every frame from the robot's camera with many different computer vision tools such as hsv, contouring, and noise reudction (erosion and dilation) to accurately find the red ball
- Goal: Track the center of the ball and give data about how offset it is from the center of the camera's frame

How does it work?
- It starts by creating a duplicate of every original, uneditted frame and converting it into hsv
- HSV stands for Hue, Saturation, and Value. Hue represents every color from 1-179, Saturation is the intensity from 0-255, and Value is the brightness from 0-255. HSV is used here to more accurately detect color without too much interference from light level
- Once created, colors ranges to detect only red, a binary masks, and a bitwiseOr function turn red pixels into white, and then everything else into black
- Morphological operations (dilate and erode) are used to reduce noise in the image, or in other words remvoe random white spots in the hsv frame (which techanically is red in the original frame). In doing so, there is a clear shape, aka the ball
- Now that there is an hsv, contouring is used to outline the white pixels in the hsv frame (aka the ball), and this outline is then displayed on the web streaming of the camera
- To find the center, the average x and y coordinate of every contour is used. This center point of the ballcan then be compared to the actual center of the camera to find the x and y offset of the ball (we care about the x offset only because we can control the left and right position of the ball by turning)
- This offset information then becomes useful input data for the rotation PID 

Code (reduced)

```python
# ...Initialize camera and web streaming...

def track_red_ball(frame):
  hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

  # Color ranges
  lower_red1 = np.array([0, 100, 100])
  # ...Other 3 np arrays for color ranges... 

  # Binary mask
  mask1 = cv2.inRange(hsv, lower_red1, upper_red1)
  mask2 = cv2.inRange(hsv, lower_red2, upper_red2)
  mask = cv2.bitwise_or(mask1, mask2)

  # Morphological opporations  
  mask = cv2.erode(mask, None, iterations=2)
  mask = cv2.dilate(mask, None, iterations=2)

  # Create contours
  countours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
  if countours:
      largest = max(countours, key=cv2.contourArea)
      M = cv2.moments(largest)
      ((x, y), radius) = cv2.minEnclosingCircle(largest)
      
      if M["m00"] != 0 and radius > 50:
          cX = int(M["m10"] / M["m00"])
          cY = int(M["m01"] / M["m00"])
          offset = cX - CENTER_X # Useful PID input data

          # Draw the contours and center of the circle
          cv2.drawContours(frame, [largest], -1, (0, 255, 0), 2)
          cv2.circle(frame, (cX, cY), 5, (255, 0, 0), -1)
          cv2.line(frame, (cX, 0), (cX, frame.shape[0]), (255, 0, 0), 1)
          cv2.line(frame, (0, cY), (frame.shape[1], cY), (255, 0, 0), 1)

def generate_frames(): # Runs the track_red_ball() function
    while True:
        frame = picam2.capture_array()
        # frame = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)
        frame = track_red_ball(frame)
        ret, buffer = cv2.imencode('.jpg', frame)
        jpg_frame = buffer.tobytes()
```

### PID rotation algorithm

What is it? 
- Takes the pixel offset of the ball and calculates the speed needed to rotate to the ball
- Goal: Rotate the robot such that the ball is in the center

How does it work?
- The algorithm is seperated into two parts: A PID function that takes in the current and target position and returns speed & a main code that handles what to do with this speed given a certain offset
- The PID function, called ballAnglePID, takes in two parameters: Current x-coordinate of the ball's center and target x-coordinate. Using these two parameters, it calculates the current error. PID works by adding together three different variables, P (proportional), I (integral), and D (derivative). P uses the current 

Suprises about the project so far

Challanges (there were many!)

Final milestone plans



# First Milestone - Assemble the robot and add working ultrasonic sensors

<iframe width="560" height="315" src="https://www.youtube.com/embed/r7C_AkfogU0?si=wncgSTxgScqFEAFU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

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
