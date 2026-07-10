# Floor Cleaning Robot
The Floor Cleaning Robot is a automated robot that cleans various areas without the need of supervision. Instead of setting aside time to cleaning your home, you can let the robot clean it while spending the free time on more important things. While it won't do a deep clean, it will do more than enough to maintain a clean home with virtually no time or physical commitment.

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Daniel P. | Fremont High School | Mechanical Engineering | Incoming Junior

<!--Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.-->

![Headstone Image](logo.svg)
   
# Final Milestone

<iframe width="576" height="486" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

<iframe width="576" height="486" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone

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

<iframe width="576" height="486" src="https://www.youtube.com/embed/t34ZnVvarjQ" title="Daniel P. Milestone 1" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My main project for these 6 weeks is a Floor Cleaning Robot. The idea is that the robot can clean a room by towing a vacuum while avoiding obstacles, ultimately cleaning the dust near corners and around the room while not moving or getting stuck with big objects. I also plan to make an extension of this, using data multiple ultrasonic sensors to create a map of the room which the robot can then intelligently clean. For my first milstone, I've created the physical robot itself and wired the numerous modules. For my first movements of the robot, I connected a Line Tracking Sensor that uses infrared and photoresistive sensors to keep the robot following a black line, which it does perfectly, except for a few scenarios. One of the main problems I faced was the wiring, as there are numerous modules and wires that must be connected on a small breadboard. I expect it to be even more complex as I connect more modules, so I will have the rewire many parts to make it more efficient. I plan to finish the base project itself (line-tracking with obstacle avoidance) in the next 2-3 weeks, and then spend the rest of my time working on mapping out areas intelligently.

# *Schematics*


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

<!--# Other Resources/Examples
**One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.**
**- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)**
**- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)**
**- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)**

**To watch the BSE tutorial on how to create a portfolio, click here.**
