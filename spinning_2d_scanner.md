Innovator Journal: Spinning 2D Scanner
Goal






Our goal is to create a simple spinning 2D scanner as a minimum testable prototype for the more complex 3D scanner we will eventually build. We started by building the basic electronics and figuring out how the different components would work together.









Elaine, Michelle, and I started assembling the electronics for our scanner. We connected an Arduino to a breadboard and wired the ultrasonic sensor and motor driver. We also organized the wires so the different components could connect to the Arduino.

 
 
 Our assembled circuit with the Arduino, breadboard, ultrasonic sensor, and motor driver
<img width="1440" height="1920" alt="image" src="https://github.com/user-attachments/assets/622fbfd3-e7f5-469e-9d99-2be2ba16375b" />


: A different view of our circuit showing how the components and wires are connected.
<img width="1440" height="1920" alt="image" src="https://github.com/user-attachments/assets/7180ba77-45c0-421f-85b8-122534bc36d3" />
<img width="1440" height="1920" alt="image" src="https://github.com/user-attachments/assets/1e082a8a-ef18-42e6-8b04-ab7c00705833" />












We learned that building the physical circuit is an important first step before the scanner can collect data. We also learned that the wiring has to be organized carefully because there are many connections between the Arduino, sensor, and motor components.









One challenge we had was using hot glue to secure the different parts of our prototype. It was difficult to position the components exactly where we wanted them while also making sure they stayed in place. We had to be careful about where we applied the glue so it would hold the parts without getting in the way of the other components.










Here is our code 





#include <Stepper.h>

//Input pins
#define OUTPUT1   7                
#define OUTPUT2   6                
#define OUTPUT3   5              
#define OUTPUT4   4              

// steps per rotation
const int stepsPerRotation = 1025;  // 28BYJ-48 has 2048 steps per rotation

Stepper myStepper(stepsPerRotation, OUTPUT1, OUTPUT3, OUTPUT2, OUTPUT4);  

const int trigPin = 9;
const int echoPin = 10;

float duration, distance;

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.begin(9600);
  // speed of the motor in RPM
  myStepper.setSpeed(5);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.begin(9600);
}

void loop() {
  // Turn halfway forward (180 degrees = half of full steps)
  myStepper.step(stepsPerRotation / 2);
  delay(500); //in milisecs

  // reverse direction by using a negative
  myStepper.step(-stepsPerRotation / 2);
  delay(500);

  digitalWrite(trigPin, LOW);
  delayMicroseconds(1);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(1);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = (duration*.0343)/2;
  Serial.print("Distance: ");
  Serial.println(distance);
}















Our next step is to get the motor to spin consistently and have the ultrasonic sensor measure while the object rotates. Once we get those parts working together, we can begin testing whether our prototype can create a simple 2D scan.
