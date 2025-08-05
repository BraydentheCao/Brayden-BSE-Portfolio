# Ball Tracking Robot
Robot that tracks a ball using a raspberry pi camera and two ultrasonic sensors

| Name | School | Major | Grade | 
| -------- | -------- | -------- | -------- |
| Brayden C | Naperville North High School | Computer Engineering | Incoming Senior |


![Description of image](logo.svg)
  
# Final Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/xLRqnBRs9Pk?si=PUBBMHvU97V0aWAF" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Links

Code: <a href="https://github.com/BraydentheCao/BSE_Code"> Link </a><br>
Custom mount: <a href="https://cad.onshape.com/documents/69cba9d53024b1578fc0d810/w/c1cbd505540fad05171fcd8c/e/e5313dda26885e11e0ae25f9"> Link </a>


## What you've accomplished since your previous milestone

The first thing I accomplished was creating a custom mount for all the differnt electrical components. This mount has organic curves to branch between the screw holes and the minimalist mounting structure. It weighs just 25 g, holding a raspberry pi, pi camera, pi fan, ultrasonic sensor, motor driver, and breadboard

Another thing I accomplished was adding gesture control to my robot. Using my computer's web camera, the gesture control works by running mediapipe on my computer to recognize 21 different points on my hand, computing the angle and length of my index finger. It then uses web sockets to transmit JSON objects encoded with the hand data from my computer to the raspberry pi. The raspberry pi interprets this data with a motor control function, converting the angle reading and the length into motor speeds for the left and right motor. (This is run seperately from the ball tracking algorithm)

I added all of my code for the this project into its own github repository. When I was doing the gesture control extension, I had two windows of vscode open, one hosted on my computer, the other ssh ing into my raspberry pi. 

## What your biggest challenges and triumphs were at BSE

### Challenges:

One challenge early on was getting the PID turning controls to be more smooth. I kept running into the issue of the robot overshooting, and I fixed this by changing the turn to rotate only one wheel if the ball's offset from the center of the was within a certain range, where before it was always both wheels turning

I ran into several challenges with GitHub. After finishing my base project, I created a repository to code from both my Raspberry Pi and computer. I accidentally committed my entire env folder—nearly 6000 files—which almost crashed my system, since env folders should stay local. I was also editing the same repo in two VS Code windows (one on the Pi, one on my computer), which led to commit conflicts when I wasn't careful about editing different files on each.

As I was coding the web socket, I faced another error, which was where the flask (which is a way to stream my web cam on chrome using http requests) wouldn't run due to the web socket. This was because I had nested the two in a single thread of code, and the solution was to add threading to my code to run the web socket and flask in parallel and independently of each other.

While making the custom mount, I faced quite a few design challenges, but the biggest one by far was at the beginning, where I didn't actually have any dimensions for the acrylic base plate of my robot. More specifically, I didn't have any dimensions for the distances between any of the holes on the plate, meaning I had to test extensively to get all of the distances correct through trial and error. As you might expect, this took a lengthy amount of time, as I dimensions 10 different distances. I also early on ran into the issue of certain Onshape features (loft in this case) running into errors trying to generate the branches I wanted. As a result, I decided to look up how to create more customizable curves, and it involved bridging curves and surface modeling. The process took nearly 10 times as many features, but the end result look incredible

### Triumphs: 

The gesture control works! The gesture control works by first recording my hand from my computer and tracking 21 different points on my hand (using mediapipe). It then calculates the relative length of my index finger using the base and tip of it. This value is divided its pixel length by my palm length to calculate the relative length. The algorithm also calculates the angle of my index finger. All of these calculations run on my computer, and data is then transmitted to my pi, where then the pi calculates a speed for each motor to match the angle of my finger. The reason the relative length is used is because the this variable is magnitude constant for how fast the motors should spin. **In creating a relative length, moving my index finger forward and backward relative to my camera will actually change the speed of the robot, while moving my index finger left and right will change the direction**

The custom mount looks amazing! Initially I had this general idea for a custom mount around the end of milestone 2, since during that time I was using excessive tape and a hastily build mount to hold everything together. In fact during weeks 1 and 2, I had rebuilt this mount multiple times since it was just too messy initially. 

The branching nature of this mount was thought of somewhat beforehand, as I wanted the most minimalistic design possible using the least amount of material. During that time, I thought about how I could make organic branches that go directly from the mounting holes directly to the mounting pieces. After 7 versions, this is what I made (link is also at the top):

<img width="1229" height="892" alt="image" src="https://github.com/user-attachments/assets/637ec226-a0ce-446f-9b65-81f2d325220b" />

With all of the electronic components, here is what it looks like

<img width="1299" height="708" alt="image" src="https://github.com/user-attachments/assets/772e587c-1c7a-4ea6-a646-1523de9cd751" />


## A summary of key topics you learned about

I learned a vast amount of skills, from computer vision, web sockets, hand gesture recognition, JSON, PID, and surface modeling in Onshape. To begin, I learned extensively about computer vision from the base project, where I used techniques such as HSV conversion, contouring, centriods, and morphological operations (such as dilate and erode). I added PID controls afterward, bridging my understanding of calculus with a real world application. My first modification was very CAD focused, where I learned how to make more organic curves using bridging curves and surface modeling. My second modification was very software focused, were I learned how to use hand gesture recognition software to determine the angle and length of my index finger. In this modification, I also learned how to use web sockets to communicate the information (using JSON objects) about my hand from my computer to my raspberry pi.   

## What you hope to learn in the future after everything you've learned at BSE

## Technical summary

# Second Milestone: PID, camera web streaming, ball tracking, camera distance tracking, and finished project

<iframe width="560" height="315" src="https://www.youtube.com/embed/u8KgakuqSV4?si=qzD20MOVRrHcwcu-" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<hr style="height:1px;border:none;background-color:#ccc;">


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

<hr style="height:1px;border:none;background-color:#ccc;">


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
<hr style="height:1px;border:none;background-color:#ccc;">


### PID rotation algorithm

What is it? 
- Takes the pixel offset of the ball and calculates the speed needed to rotate to the ball
- Goal: Rotate the robot such that the ball is in the center


**The algorithm is seperated into two parts: A PID function that takes in the current and target position and returns speed between -1 and 1 & a main code that handles what to do with this speed given a certain offset. Keep in mind the motors can have a speed between 0 and 1**


How does the PID function work?

- The PID function, called ballAnglePID, takes in two parameters: Current x-coordinate of the ball's center and target x-coordinate. Using these two parameters, it calculates the current error. PID works by adding together three different variables, P (proportional), I (integral), and D (derivative) to calculate speed (speed = P + I + D).
- Do note that PID is sign sensitive, meaning negative and postiive are treated differently, and the output can be positive or negative 
- P uses the current error mulitplied by some constant (kd). If the ball is say 100 pixels away from the center, then P will be kd*100
- I accumulates the total error over time mulitplied by some constant (ki). It essential checks to make sure the error over time cancels itself out, or in other words, it makes sure that the actual value is approaching the target value over time. For the purposes of my function, it is not too important
- D finds the difference between the current and previous error, then multiplies it by some constant (kd)


Here is the def ballAnglePID function code:


```python
def ballAnglePID(current, target): 
    global prevErrorA, curErrorA, iErrorA # A lot of the variables have the letter "a" at the end because originally I intended to make another PID function, but decided not to

    if abs(current - target) < 50: # 
       return 0

    curErrorA = 0.0015625*2*(target - current)

    '''
    The curError is multipled by 0.0015625*2 since the parameter "current" ranges
    from 0 to 640 (each frame is 640 pixels wide) and the possible return values
    can only be between -1 and 1. Basically we are translating an error between
    -320 to 320 all the way down to -1 to 1
    '''

    kp = 0.5 # Proportional gain
    kd = 0.1  # Derivative gain
    ki = 0.0000000001  # Integral gain. Generally has a very small value

    dError = curErrorA - prevErrorA # This is "current - previous error" calculation
    iErrorA += curErrorA  # Integral error can be implemented if needed

    P = kp * curErrorA
    I = ki * iErrorA
    D = kd * dError 
    
    prevErrorA = curErrorA
    speed = P + D # I decided not to include the "I" variable

    '''
    This if statement does two things:
    1: If speed is outside of the -1 to 1 range, return a speed of 0
    2: If the speed is below 0.4, return 0.4. This is because below 0.4, the robot won't move.
       Same applies for if the speed is negative
    '''
  
    if speed > -1 and speed < 1:
        if speed < 0.4 and speed > 0:   
            return 0.4
        elif speed > -0.4 and speed < 0:
            
            return -0.4
        else:
            return speed
    else:
        print("out of bounds")
        return 0
```
<hr style="height:1px;border:none;background-color:#ccc;">


### Distance tracking algorithms

What is it?
- Calulates the ball's distance from the robot
- There are two functions that calculate distance: One that uses the camera, the other than uses the ultrasonic sensor

How does it work?
- The function that uses the ultrasonic sensor is pretty straightforward, while the one that uses the camera is slightly more complicated. I'm only going to cover the camera because of this
- The camera can track distance using proportions due to the nature of how the pi camera works and how it has similar triangles. Essentially, we can have the variables f, R, D, and r. Here is what they stand for:
  - R --> Radius of the red ball in real life (physically measured in cm)
  - D --> Distance of the ball from the camera (we are trying to find this value)
  - f --> Focal length of the camera (I found this experimentally for this project)
  - r --> Radius of the red ball in pixels from the camera frame


<img width="673" height="450" alt="Screenshot 2025-07-21 000902" src="https://github.com/user-attachments/assets/44a4be94-9106-47f1-82c0-ce334b23c415" />


Based on this image, it becomes clear where the similar triangles are, and as such, we can create this proportion:
  - D/R = f/r
  - To isolate D, we get: D = R*f/r


The code is really simple for this algorithm

```python

def measureBallDistance(r, offsetP):
    f = 290.562
    R = 17.78 # Radius of the ball in cm
    D = R*f/r
    offsetR = offsetP*D/f
    return D 
```

### Entire tracking algorithm  

  The code structure looks something like this:
  
  PID turn function
  ball distance function using camera
  ball distance function using ultrasonic sensor

  Looping code:
    Calculate current X position of the ball's center
    Calculate turnSpeed using PID turn function
    if ball is detected:
      Do initial alignment (I don't think this runs as intended right now, although I think that works in my favor)
      If ball distance is over 35: drive forward quickly
      else: 
        Rotate robot until algined, stop when aligned
        If robot has been stopped for long enough:
          Align the robot's distance with the ball
          Once done, stop the robot completely
    else: spin in place


Here's how the whole algorithm works:


First, it calculates the current X position of the ball's center and the radius of the ball. (This code has already been shown, but this helps to clarity where variables are coming from)

```python
countours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    if1 countours: # The if statements are numbered to make them more clear
        largest = max(countours, key=cv2.contourArea) # Finds the largest shape made by the many contours
        M = cv2.moments(largest) # Creates a dictionary that contains different values assosiated this this largest shape  
        ((x, y), radius) = cv2.minEnclosingCircle(largest) # Here is where the center and smallest radius of the circle
        
        if2 M["m00"] != 0 and radius > 50: # Only runs if the radius of the shape detected is greater than 50 pixels
            cX = int(M["m10"] / M["m00"])
            cY = int(M["m01"] / M["m00"])
            offset = cX - CENTER_X # The offset is precalculated for some display information 

            # Draws the contours, center, and lines for the x and y values of the cetner
            cv2.drawContours(frame, [largest], -1, (0, 255, 0), 2) 
            cv2.circle(frame, (cX, cY), 5, (255, 0, 0), -1)
            cv2.line(frame, (cX, 0), (cX, frame.shape[0]), (255, 0, 0), 1)
            cv2.line(frame, (0, cY), (frame.shape[1], cY), (255, 0, 0), 1)

            # Next code is here
      
```


After that, we move onto the actual algorithm. We begin with the initial alignment (which as of now doesn't actually work, and I'll explain in the code why that actually works in my favor). 

```python
           
            # Calculates offset for turning and display info (the PID function does its own calculation)
            if3 abs(offset) < 50:
                position = "Centered"
            elif3 offset < 0:
                position = "Left"
            else3:
                position = "Right"
            
            turnSpeed = ballAnglePID(cX,CENTER_X) #Find the turn speed using the PID function

            '''
            Alignment algorithm:

            Negative turn speed = right turn
            Positive turn speed = left turn
            if the turn speed is > 0, then turn left. If the offset is less than 100, then do a
            pivot turn, where only one wheel rotates. If the offset is greater than 100, then do a
            tank turn, where one wheel spins forward, the other spins backward (rotating in place)
            Do then same for if the speed is < 0, except flip the turn direction
            If speed is zero (the else statement), then stop the motors

            '''
            if4 turnSpeed > 0: # Left turn
                if5 abs(offset) < 100:  # Pivot turn
                    print(f"Turn speed: {turnSpeed}")
                    rightMotor.forward(turnSpeed)
                    leftMotor.stop()
                    
                else5: # Tank turn
                    print(f"Turn speed: {turnSpeed}")
                    rightMotor.forward(turnSpeed)
                    leftMotor.backward(turnSpeed)
            
            elif4 turnSpeed < 0: # Right turn
                if6 abs(offset) < 100:
                    print(f"Turn speed: {turnSpeed}")
                    leftMotor.forward(-turnSpeed) # The forward and backward methods only take in from 0 to 1, so the sign is flipped
                    leftMotor.stop()
                else6:
                    print(f"Turn speed: {turnSpeed}")
                    leftMotor.forward(-turnSpeed)
                    rightMotor.backward(-turnSpeed)
            
            else4:
                leftMotor.stop()
                rightMotor.stop()
            # Next code is here
```

The reason this code doesn't run is because it's immediately overriden by the "drive forward quickly" code. This is good actually a good thing because otherwise the robot would constantly correct its orientation every frame, and if the robot is extremely far away, any slight unintended turn will cause the robot to realign over and over again. This repitition causes very little forward movement and forces the robot to shake in place

The "drive forward quickly" is really simple and runs immediately after the initial turn 

```python
            '''
            Basically just speed forward if distance (using the camera is greater than 35
            When the ball is far, the camera is used. When it is close, the ultrasonic sensor is used.
            If it is too close, the camera is used again
            '''
            if7 measureBallDistance(radius,offset) > 35:
                rightMotor.forward(.7)
                leftMotor.forward(.7)
            else7:
                # Next code is here
```

Once the robot is close enough to the ball, it performs another alignment (this time it actually works)

```python
                '''
                What you’ll notice is that this code is basically the same as before, with the only difference being
                the addition of a counter variable called centerCountFrames. This variable just makes sure the robot
                has been centered (offset between -50 and 50) for enough frames (essentially enough time). That’s
                important because the next block of code handles adjusting the robot’s distance to the ball. Just like
                before, if there’s no time delay between one movement and the next (like stopping and then adjusting
                distance), the motors don’t actually stop. That causes overshooting and brings back the same shaking issue.
                '''

                if8 turnSpeed > 0:
                    centerCountFrames = 0 # If the robot has to align again, the counter resets
                    if9 abs(offset) < 100:
                        print(f"Turn speed: {turnSpeed}")
                        rightMotor.forward(turnSpeed)
                        leftMotor.stop()
                        
                    else9:
                        print(f"Turn speed: {turnSpeed}")
                        rightMotor.forward(turnSpeed)
                        leftMotor.backward(turnSpeed)

                elif8 turnSpeed < 0:
                    centerCountFrames = 0
                    if10 abs(offset) < 100:
                        print(f"Turn speed: {turnSpeed}")
                        leftMotor.forward(-turnSpeed)
                        leftMotor.stop()

                    else10:
                        print(f"Turn speed: {turnSpeed}")
                        leftMotor.forward(-turnSpeed)
                        rightMotor.backward(-turnSpeed)

                else8:
                    leftMotor.stop()
                    rightMotor.stop()
                    centerCountFrames += 1 # The "time delay" uses the number frames instaed of an actual time.sleep 
                # Next code is here
```

Once the robot has been centered for long enough, then the distance alignment begins 

```python

                '''
                Once the frame has been centered for 6 frames, distance from the ball is measured
                both using the camera and ultrasonic sensor. After that, the robot alignemnet is
                relatively straight forward 
                '''  
                if11 centerCountFrames > 5:
                    distanceFromBallCamera = measureBallDistance(radius,offset) - 17.78/2

                    '''
                    The camera uses the distance to the 3D center of the ball, while the ultrasonic sensor measures
                    the distance from the point on the ball closest to the robot. This means the distance using the
                    camera is higher by the radius of the ball, thus "-17.78/2"
                    '''

                    distanceFromBallUltrasonic = runUltrasonicDistance(TRIGM, ECHOM)
                    
                    if12 distanceFromBallCamera < 20 and distanceFromBallCamera > 8:  
                        if13 distanceFromBallUltrasonic < 12:                        
                            leftMotor.stop()
                            rightMotor.stop()  
                            print("Success!")
                        
                        else13: 
                            leftMotor.forward(0.4)
                            rightMotor.forward(0.4)
                            print("Distance speed: 0.4, too far")
                    
                    elif12 distanceFromBallCamera < 8: # If the ball is too close, then the ultrasonic sensor gives a huge number
                        leftMotor.backward(0.5)
                        rightMotor.backward(0.5)
                        print("Distance speed: -0.4, too close")


                    else12: #If the ball is out of range for some reason
                        leftMotor.forward(0.35)
                        rightMotor.forward(0.35)
                        print("Distance speed: 0.4")
            # Next code is here

           
```

The very end of the algorithm is the display info
    
```python
            # Display text
            cv2.putText(frame, f"Offset: {offset} | Position: {position}", (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (100, 100, 100), 2)
            cv2.putText(frame, f"Ball distance (camera): {distanceFromBallCamera:.2f} | Ball distance (US): {distanceFromBallUltrasonic:.2f}", (10, 60), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (100, 100, 100), 2)

        else1: # The turn in place
            rightMotor.forward(.5)
            leftMotor.backward(.5)
            print(f"Nothing detected: Turning at 0.5")    
```

add display text here

Suprises about the project so far


Honestly I used to think I didn't like coding nearly as much, but I have been mistaken. I took a class last school year that made me assume this, however, this project has made me love coding again, especially working so much with the terminal. For some reason, I really like interacting with the terminal a lot, maybe because it feels almost like being a hacker


Challanges (there were many!)

1: PID Offset Handling

Originally, the PID function corrected for an offset of exactly 0, which caused the robot to jitter in place. I modified it so that if the offset is between -50 and 50, it returns a speed of 0—making it more stable when centered.

2: Minimum Motor Speed (Floor Speed)

The motors wouldn't move at low speeds due to friction, so I added a minimum speed: 0.3 or -0.3 based on offset direction. However, this “floor speed” of 0.5 for turning was too high and caused the robot to overshoot the target window.

To fix this, I:

- Switched from carpet to a slippery surface (poster board), lowering floor speed to ~0.4.
- Swapped wheel motors (no effect).
- Powered the L298n motor driver directly from the battery pack (no effect).
- Switched to an L9110s motor driver, but overshooting persisted.

Eventually, I discovered the real issue: the added battery pack weight increased friction, requiring more torque. Removing it brought the floor speed down to 0.3, which was better—but not perfect.

Before/After Summary:

Before: L298n + battery pack, carpet → floor speed = 0.5
After: L9110s + powered from Pi, poster board → floor speed = 0.3

Also learned: motor drivers need a common ground with the Pi to function properly.

3: Slower Turning When Near Center

To reduce overshooting, I made the robot turn more slowly when the offset is between -100 and 100. Instead of turning both wheels, it now spins just one motor—this reduced heading speed and helped stay within bounds. However, the robot sometimes moved even when within the -50 to 50 “stop zone.”

4: Fixing the Final Overshoot

The final issue was that the code never explicitly handled a speed of 0 in the track_red_ball function. So I added a clause: if the PID returns 0, the robot now stops both motors. This fixed the last overshoot bug.
'''

## Final milestone plans

I plan to make two different extensions: A 3D printed mount for all my parts and gesture control

The 3D print I would like 


# First Milestone - Assemble the robot and add working ultrasonic sensors

<iframe width="560" height="315" src="https://www.youtube.com/embed/r7C_AkfogU0?si=wncgSTxgScqFEAFU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

How everything works together
- Currently, the robot is comprised of two motors, a raspberry pi, a L298n motor driver, a small breadboard, and two ultrasonic sensors. The image below contains a picture of the schematics with all of the different componenets and how they are powered and controlled electrically
_____
Technical summary: 
- So far, I have assembled the base of the robot with motors and wheels, as well as all of the components previously mentioned, all of which are fully wired and fully functional. For example, I am able to pick up distance readings from both ultrasonic sensors. In addition, I also coded the robot to where it can continuously adjust it's position to be 8-10 cm away from an object by using the ultrasonic sensors and adjusting the speed of the motors accordingly. Currently this feature is only being tested on the left ultrasonic sensor and being applied to both motors for ease of testing 
_____
Challenges:
- So far, I have faced a multitude of challenges, most of which have been due to wiring issues in some capacity. The first one I encountered was using an L298n instead of the motor drivers we were given, as I unfortunately did not recieve the motor drivers, and so instead I decided to improvise by using my own ones. It took a little bit more wiring and slightly different coding, and after some retries with wiring, both motors worked as intended with proper speed control
- The second challenge I faced was with wiring the ultrasonic sensors. The ultrasonic sensors' echo wire needs to have a voltage cap of 3.3V before it can be connected to the raspberry pi (it outputs a signal with 5V), therefore 1K and 2K resistor must be use as not to damage the raspberry pi. At first, I wasn't able to figure out why exactly my wires had an issue, but some time, I realized it was because I connected the resistor and echo wire on different rails and so they weren't actually connected
- The third challenge I faced was with controling the robot with the ultrasonic sensors. I was able to get the robot to move forward above 10 cm of distance (using the formula speed = 0.05*distance+0.4) and get the robot the move backward with a distance under 8 cm, however the issue was that robot would start ocillating between the forward and backwards motion likely due to the robot overshooting the intended distance. Another issue was below around a speed of 0.3-0.4, the robot wasn't able to spin its motors likely due to too low of a voltage and a lack of torque. Very likely I will have to use PID controls to assist with this
_____
Plan to complete your project:
- Attach the camera, code it, and get it to work with the ultrasonic sensors
- After the base project is done, I would like to add a control aspect to the robot, such as using my hand in some way to control it.
_____
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
