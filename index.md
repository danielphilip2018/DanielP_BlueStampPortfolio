# Floor Cleaning Robot
The Floor Cleaning Robot is a automated robot that cleans various areas without the need of supervision. Instead of setting aside time to cleaning your home, you can let the robot clean it while spending the free time on more important things. While it won't do a deep clean, it will do more than enough to maintain clean walkways and rooms with virtually no time or physical commitment.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Daniel P. | Fremont High School | Mechanical Engineering | Incoming Junior

<!--Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.-->

![Headstone Image](logo.svg)
   
# Final Milestone

<iframe width="820" height="462" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For my final milestone, I made the robot intelligently turn towards areas that had more space. To do this, I added 3 ultrasonic sensors in an array, placed on top to get distance readings of the front, left and right. I then coded in a script that would make the robot used these 4 sensors to find the place with the place with the most space and go there. The robot also follows walls on its left side and switches between just searching for longest distance and following the wall by itself. I also made the robot have basic mapping capabilities, using a Python script to link the computer Terminal and the Arduino code and make a live radar/map of its surroundings. I expected this milestone to be really hard and it was. The code was by far the hardest part of this milestone, but the sensor array was also challenging. The mounting was through cardboard that liked to push the sensor pins and the wires away from each other. The sensors tended to give weird readings, and I had to look at each individual to find the issues with the code or wiring. That being said, the sensor array has helped expand the scope of this project, as even though my time is done in BSE, it opens the door to lots of much more complex ideas to be implemented. 

My biggest challenges at BSE were learning to CAD and the coding. CAD was something I'd tried before this course with barely any success, but this course helped me learn the basics and start creating useful and properly-scaled shapes and structures. My biggest triumphs were the building of the robot frame and the wiring, as I've always enjoyed doing that and the wiring felt more like a complex puzzle than a issue. Seeing all the wiring but knowing what each individual one did felt very satisfying to me. 

# *Schematics*
<img width="2000" height="auto" alt="FCRSideView" src="https://github.com/user-attachments/assets/a6ce5e29-b54d-4ff4-83de-74a2005e7450" />
<img width="2000" height="auto" alt="FCR3DView" src="https://github.com/user-attachments/assets/1c799f16-8252-4a95-a600-c57397a6cd69" />


# *Code*
<!--**Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs.**-->

```c++
// Throttle Control
const int A_1B = 5;
const int A_1A = 6;
const int B_1B = 9;
const int B_1A = 10;

// Red Gold FRONT
const int echoPinFront = 4;
const int trigPinFront = 3;

// Purple White BACK NOT USED OPEN
const int echoPinBack = A5;
const int trigPinBack = A4;

// Black White RIGHT
const int trigPinRight = A3;
const int echoPinRight = A2;

// Orange Yellow LEFT
const int echoPinLeft = A0;
const int trigPinLeft = A1;

// IR Sensors
const int rightIR = 7;
const int leftIR = 8;

const float mult = 0.92; 

// Navigation Parameters
const float FRONT_THRESHOLD   = 18.0;
const float MIN_BRACKET      = 12.0;
const float MAX_BRACKET      = 18.0; 
const float MAX_HUG_DISTANCE = 26.0;

bool hasFoundWall = false;

float readSensorDataFront() {
  digitalWrite(trigPinFront, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPinFront, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPinFront, LOW);
  float dist = pulseIn(echoPinFront, HIGH, 5800) / 58.00; 
  return (dist <= 2.0) ? 999.0 : dist; 
}

// UNUSED below
float readSensorDataBack() {
  digitalWrite(trigPinBack, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPinBack, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPinBack, LOW);
  float dist = pulseIn(echoPinBack, HIGH, 5800) / 58.00; 
  return (dist <= 2.0) ? 999.0 : dist;
}

float readSensorDataRight() {
  digitalWrite(trigPinRight, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPinRight, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPinRight, LOW);
  float dist = pulseIn(echoPinRight, HIGH, 5800) / 58.00; 
  return (dist <= 2.0) ? 999.0 : dist;
}

float readSensorDataLeft() {
  digitalWrite(trigPinLeft, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPinLeft, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPinLeft, LOW);
  float dist = pulseIn(echoPinLeft, HIGH, 5800) / 58.00; 
  return (dist <= 2.0) ? 999.0 : dist;
}

void moveForward(int speed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, speed);
  analogWrite(B_1B, speed * mult);
  analogWrite(B_1A, 0);
}

void veerRight(int baseSpeed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, baseSpeed);             
  analogWrite(B_1B, (baseSpeed * 0.5) * mult); 
  analogWrite(B_1A, 0);
}

void veerLeft(int baseSpeed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, baseSpeed * 0.5);      
  analogWrite(B_1B, baseSpeed * mult);
  analogWrite(B_1A, 0);
}

void moveBackward(int speed) {
  analogWrite(A_1B, speed);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed * mult);
}

void pivotLeft(int speed) {
  analogWrite(A_1B, speed);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, speed * mult);
  analogWrite(B_1A, 0);
}

void pivotRight(int speed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, speed);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed * mult);
}

void stopMove() {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);
}

void sendTelemetry(float front, float left, float right) {
  Serial.print(front);
  Serial.print(",");
  Serial.print(left);
  Serial.print(",");
  Serial.print(right);
  Serial.print(",");
  Serial.println(hasFoundWall ? "WALL_HUG" : "SEARCH");
}

void setup() {
  Serial.begin(9600);

  pinMode(A_1B, OUTPUT);
  pinMode(A_1A, OUTPUT);
  pinMode(B_1B, OUTPUT);
  pinMode(B_1A, OUTPUT);

  pinMode(echoPinFront, INPUT);
  pinMode(trigPinFront, OUTPUT);
  pinMode(echoPinBack, INPUT);
  pinMode(trigPinBack, OUTPUT);
  pinMode(echoPinRight, INPUT);
  pinMode(trigPinRight, OUTPUT);
  pinMode(echoPinLeft, INPUT);
  pinMode(trigPinLeft, OUTPUT);

  pinMode(leftIR, INPUT);
  pinMode(rightIR, INPUT);
}

void loop() {
  int leftIR_val = digitalRead(leftIR);
  int rightIR_val = digitalRead(rightIR);

  float distanceFront = readSensorDataFront();
  delay(10);
  float distanceLeft = readSensorDataLeft();
  delay(10);
  float distanceRight = readSensorDataRight();

  sendTelemetry(distanceFront, distanceLeft, distanceRight);

  if (leftIR_val == LOW && rightIR_val == HIGH) {
    veerRight(180);
    delay(150); 
    stopMove();
  } 
  else if (leftIR_val == HIGH && rightIR_val == LOW) {
    veerLeft(180);
    delay(150);
    stopMove();
  } 
  else if (leftIR_val == LOW && rightIR_val == LOW) {
    moveBackward(150);
    delay(250);
    stopMove();
  } 

  else {
    if (distanceLeft < MAX_HUG_DISTANCE && !hasFoundWall) {
      hasFoundWall = true;
    }

    if (distanceFront <= FRONT_THRESHOLD) {
      stopMove();
      delay(300);

      distanceRight = readSensorDataRight();
      sendTelemetry(distanceFront, distanceLeft, distanceRight);

      if (distanceLeft > distanceRight) {
        pivotRight(180);
        delay(670);
      } else {
        pivotLeft(180);
        delay(670);
      } 
      stopMove();
      delay(150);
    } 

    else {
      if (hasFoundWall) {
        if (distanceLeft < MIN_BRACKET) {
          veerLeft(160);
          delay(30);
        } 
        else if (distanceLeft > MAX_BRACKET && distanceLeft <= MAX_HUG_DISTANCE) {
          veerRight(160);
          delay(40);
        } 
        else if (distanceLeft > MAX_HUG_DISTANCE) {
          if (distanceFront <= (FRONT_THRESHOLD + 10.0)) {
            moveForward(120);
          } else {
            moveForward(200);
          }
        } 
        else {
          if (distanceFront <= (FRONT_THRESHOLD + 10.0)) {
            moveForward(120);
          } else {
            moveForward(200);
          }
        }
      } 
      else {
        if (distanceFront <= (FRONT_THRESHOLD + 10.0)) {
          moveForward(120);
        } else {
          moveForward(200);
        }
      }
    }  
  }
}
```


# Second Milestone

<iframe width="820" height="462" src="https://www.youtube.com/embed/f4Klb_VM6o0?si=FL4mymssh1P_Weu8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

For my second milestone, I essentially finished the base floor cleaning robot project. I used an ultrasonic sensor for obstacle avoidance, as well as two close range IR sensors in the robot's blindspot to prevent it from getting stuck. I also used Onshape, a browser-based CAD software, to create a clean cover that hides the robot's wiring. The wiring was simpler than I expected it to be, as moving stuff around the breadboard made it quite easy to connect all the newmodules. The main challenge of this module was learning how to use CAD software, which I had never touched before. I found it incredibly confusing at the beginning, but I pretty quickly learnt how to make more complicated shapes and things, like I did for the cover. My final milestone is my most ambitious milestone, as it requires good knowledge of compiling and making the robot act on very large data sets. It will most likely require an extra Raspberry Pi. 

# *Schematics*
<img width="2000" height="auto" alt="20260701_090928" src="https://github.com/user-attachments/assets/5d3fddb8-d7c2-4b8d-ace4-14d7b491ee9c" />
<img width="2000" height="auto" alt="20260701_090934" src="https://github.com/user-attachments/assets/a4f1e230-1dc2-44db-afb9-8e8ed15b90c7" />

# *Code*
<!--**Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs.**-->

```c++
const int A_1B = 5;
const int A_1A = 6;
const int B_1B = 9;
const int B_1A = 10;

const int echoPin = 4;
const int trigPin = 3;

const int rightIR = 7;
const int leftIR = 8;

const float mult = 1;

float readSensorData() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  float distance = pulseIn(echoPin, HIGH) / 58.00; 
  return distance;
}

void moveForward(int speed) {

  analogWrite(A_1B, 0);
  analogWrite(A_1A, 255);
  analogWrite(B_1B, 255*mult);
  analogWrite(B_1A, 0);
  delay(15);

  int adjustedRightSpeed = speed; 

  analogWrite(A_1B, 0);
  analogWrite(A_1A, speed);
  analogWrite(B_1B, adjustedRightSpeed*mult);
  analogWrite(B_1A, 0);
}

void moveBackward(int speed) {
  analogWrite(A_1B, speed);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed);
}

void backLeft(int speed) {
  analogWrite(A_1B, speed);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);
}

void backRight(int speed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, speed);
}

void stopMove() {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);
}

void setup() {
  Serial.begin(9600);

  pinMode(A_1B, OUTPUT);
  pinMode(A_1A, OUTPUT);
  pinMode(B_1B, OUTPUT);
  pinMode(B_1A, OUTPUT);

  pinMode(echoPin, INPUT);
  pinMode(trigPin, OUTPUT);

  pinMode(leftIR, INPUT);
  pinMode(rightIR, INPUT);
}

void loop() {
  int left = digitalRead(leftIR);
  int right = digitalRead(rightIR);


  if (!left && right) {
    backRight(180); 
    delay(200);
  } else if (left && !right) {
    backLeft(180);
    delay(200);
  } else if (!left && !right) {
    moveBackward(180);
    delay(300);
  } else {
    float distance = readSensorData();
    Serial.println(distance);

    if (distance > 30) { 
      moveForward(255);
    } else if (distance <= 30 && distance >= 12) {
      Serial.println("Slow down");
      moveForward(120);
    } else if (distance < 12) {
      Serial.println("Change direction");
      stopMove();
      delay(150);
      moveBackward(180);
      delay(520);
      backLeft(180);
      delay(600);
      stopMove();
      delay(250);
    }  
    }
  }
```

# First Milestone

<iframe width="820" height="462" src="https://www.youtube.com/embed/t34ZnVvarjQ" title="Daniel P. Milestone 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My main project for these 6 weeks is a Floor Cleaning Robot. The idea is that the robot can clean a room by towing a vacuum while avoiding obstacles, ultimately cleaning the dust near corners and around the room while not moving or getting stuck with big objects. I also plan to make an extension of this, using data multiple ultrasonic sensors to create a map of the room which the robot can then intelligently clean. For my first milstone, I've created the physical robot itself and wired the numerous modules. For my first movements of the robot, I connected a Line Tracking Sensor that uses infrared and photoresistive sensors to keep the robot following a black line, which it does perfectly, except for a few scenarios. One of the main problems I faced was the wiring, as there are numerous modules and wires that must be connected on a small breadboard. I also found the logic of how it would know which way to turn a bit confusing at first. I expect it to be even more complex as I connect more modules, so I will have the rewire many parts to make it more efficient. I plan to finish the base project itself (line-tracking with obstacle avoidance) in the next 2-3 weeks, and then spend the rest of my time working on mapping out areas intelligently.

# *Schematics*
<img width="644" height="522" alt="Screenshot 2026-07-13 at 08 44 36" src="https://github.com/user-attachments/assets/c6464b0a-c12c-4781-b0c8-e26ff8d31f4d" />
<img width="634" height="491" alt="Screenshot 2026-07-13 at 08 44 09" src="https://github.com/user-attachments/assets/d19f83a9-ac96-4b81-becd-00d88396bee9" />


# *Code*
<!--**Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs.**-->

```c++
const int A_1B = 5;
const int A_1A = 6;
const int B_1B = 9;
const int B_1A = 10;

const int lineTrack = 2;

void setup() {
  Serial.begin(9600);

  //motor
  pinMode(A_1B, OUTPUT);
  pinMode(A_1A, OUTPUT);
  pinMode(B_1B, OUTPUT);
  pinMode(B_1A, OUTPUT);
  //line track
  pinMode(lineTrack, INPUT);
}

void loop() {

  int speed = 150;

  int lineColor = digitalRead(lineTrack); // 0:white  1:black
  Serial.println(lineColor);
  if (lineColor) {
    moveLeft(speed);
  } else {
    moveRight(speed);
  }
}
void moveLeft(int speed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, speed);
  analogWrite(B_1B, 0);
  analogWrite(B_1A, 0);
}

void moveRight(int speed) {
  analogWrite(A_1B, 0);
  analogWrite(A_1A, 0);
  analogWrite(B_1B, speed);
  analogWrite(B_1A, 0);
}

```

# Bill of Materials
<!--**Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs.**-->

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| SunFounder 3in1 Kit | Main robot structure and basic sensors | $69.99 | <a href="https://www.sunfounder.com/products/sunfounder-3-in-1-iot-smart-car-learning-ultimate-starter-kit"> Link </a> |
| Odistar Desktop Vacuum | Vacuum for robot | $12.98 | <a href="https://www.amazon.com/ODISTAR-Endurance-Cordless-Rotatable-Keyboard/dp/B07Q128V6W"> Link </a> |
| amazonbasics 9V and 1.5V Batteries | Robot power without desktop | $5-$10 | <a href="https://www.amazon.com/stores/AmazonBasics/page/947C6949-CF8E-4BD3-914A-B411DD3E4433"> Link </a> |
