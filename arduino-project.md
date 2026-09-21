Arduino Traffic Light
My Process

I made a traffic light using an Arduino, a breadboard, 3 LEDS,  and a button. When the button is pressed, the lights turn on in order like a real traffic light. I wanted to use multiple LEDs and a button together because I wanted to see how different parts could work together in one circuit. Traffic lights are also something I see in everyday life, so I thought it would be interesting to make a simple version of one. It also gave us a chance to practice using an input to control multiple outputs.

The new component I worked with was the button. The button is an input because it tells the Arduino when it is pressed. We searched online to figure out how to set up the button and connect it to the circuit. I also used ChatGPT when I did not know how to write certain parts of the Arduino code. It helped me understand the parts of the code I was stuck on. While building the circuit, we had some problems with the wiring because some of the wires were in the wrong places. Elaine suggested that I check the wires, and when I looked at them, I realized that some of them were in the wrong places. We moved them to the correct places and tested the circuit again. We also had an error in our code, so we fixed that and tested it again. After making these changes, the lights started working in the correct order.

Here are some photos of our project and a video that tested our circuit:

<img width="1080" height="1920" alt="arduino-day8-1" src="https://github.com/user-attachments/assets/cbd5c1c5-4928-4630-b4e4-ea0e64ba4d62" />



https://github.com/user-attachments/assets/688c62de-5134-4b22-93f5-a338fe8aff63














After fixing the wiring and code, our final led had a button connected to the Arduino on a breadboard. The button is the input, while the three LEDs are the outputs. When the button is pressed, the Arduino starts the traffic light sequence and turns the lights on in the correct order. We tested the circuit several times to make sure the button and LEDs were working together correctly.

Our final code:
const int carRed = 8;
const int carYellow = 9;
const int carGreen = 10;

const int walkGreen = 11;
const int walkRed = 12;

const int button = 2;
const int buzzer = 3;

void setup() {
  pinMode(carRed, OUTPUT);
  pinMode(carYellow, OUTPUT);
  pinMode(carGreen, OUTPUT);

  pinMode(walkGreen, OUTPUT);
  pinMode(walkRed, OUTPUT);

  pinMode(button, INPUT_PULLUP);
  pinMode(buzzer, OUTPUT);
}

void loop() {
  // normal state
  digitalWrite(carRed, LOW);
  digitalWrite(carYellow, LOW);
  digitalWrite(carGreen, HIGH);

  digitalWrite(walkRed, HIGH);
  digitalWrite(walkGreen, LOW);

  noTone(buzzer);

  // start crossing when the button is pressed
  if (digitalRead(button) == LOW) {
    delay(1000);

    // cars slow down
    digitalWrite(carGreen, LOW);
    digitalWrite(carYellow, HIGH);
    delay(2000);

    // cars stop
    digitalWrite(carYellow, LOW);
    digitalWrite(carRed, HIGH);
    delay(1000);

    // pedestrians can walk
    digitalWrite(walkRed, LOW);
    digitalWrite(walkGreen, HIGH);

    // slow chirps while walking
    for (int i = 0; i < 4; i++) {
      tone(buzzer, 700);
      delay(80);
      noTone(buzzer);
      delay(700);
    }

    // faster warning at the end
    for (int i = 0; i < 4; i++) {
      digitalWrite(walkGreen, LOW);
      tone(buzzer, 850);
      delay(70);
      noTone(buzzer);
      delay(250);

      digitalWrite(walkGreen, HIGH);
      tone(buzzer, 850);
      delay(70);
      noTone(buzzer);
      delay(250);
    }

    // pedestrians stop
    digitalWrite(walkGreen, LOW);
    digitalWrite(walkRed, HIGH);
    noTone(buzzer);
    delay(1000);

    // cars can go again
    digitalWrite(carRed, LOW);
    digitalWrite(carGreen, HIGH);

    // wait for the button to be released
    while (digitalRead(button) == LOW) {
      delay(10);
    }
  }
}



Technical Tidbit: How the Button Works

The button is an input because it gives the Arduino information when it is pressed. The Arduino reads the button and uses that information to start the traffic light sequence. The LEDs are outputs because the Arduino controls when they turn on and off. We also learned about debouncing. Sometimes a button can register a press more than once because of the way the button makes contact. This is called bouncing. Debouncing helps the Arduino recognize the action as one button press instead of multiple presses. This is important because we want one press of the button to start the sequence correctly.

Peer Support

When we were working on our circuit, some of the lights were not working correctly. Elaine told me to check if the wires were in the wrong place. I checked them and realized that some of the wires were in the wrong places. I moved them to the right places, and then the circuit started working correctly. Her suggestion helped me figure out what to check instead of just changing random things. It also helped me understand that checking the wiring is an important part of fixing a circuit.

Use-Case Reflection

A traffic light can help control the movement of cars and people. Our project is a simple version of this idea because it uses lights to show a sequence. To make our project more like a real traffic light, we would need to make the timing more realistic and add something that could detect cars or people. If we continued working on it, we would use debugging because we would need to find and fix problems with the wiring or code. We could also add a sensor so the traffic light could respond to cars or people instead of only starting when the button is pressed. This project helped me practice using an input and multiple outputs together and showed me that testing and fixing mistakes are important parts of making an Arduino project work.
