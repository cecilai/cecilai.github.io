Arduino Traffic Light
Arduino Traffic Light
My Process

I made a traffic light using an Arduino, a breadboard, a red LED, a yellow LED, a green LED, and a button. When the button is pressed, the lights turn on in order like a real traffic light. We worked on the wiring and code together to get the different parts to work with each other.

I wanted to use multiple LEDs and a button together because I wanted to see how different parts could work together in one circuit. Traffic lights are also something I see in everyday life, so I thought it would be interesting to make a simple version of one. It also gave us a chance to practice using an input to control different outputs.

The new component I worked with was the button. The button is an input because it tells the Arduino when it is pressed. I searched online to figure out how to set up the button and connect it to the circuit. I also used ChatGPT when I did not know how to write certain parts of the Arduino code. It helped me understand the parts of the code I was stuck on.

At first, some of our wires were in the wrong places, so the lights were not working correctly. Elaine told me to check if the wires were in the wrong places. I checked them and realized that some of the wires were in the wrong places. I moved them and tested the circuit again. We also had an error in our code, so we fixed it and tested it again. After making these changes, the lights worked in the correct order.

Final Circuit and Code

We started by setting up the breadboard and connecting the red, yellow, and green LEDs to the Arduino.

[ADD PHOTO 1 HERE]

We then added the button and connected the wires so it could work as the input for the traffic light.

[ADD PHOTO 2 HERE]

We checked the wiring and found that some of the wires were in the wrong places. We moved them to the correct places and tested the circuit again.

[ADD PHOTO 3 HERE]

We also worked on the code and fixed an error that was stopping the circuit from working correctly.

[ADD PHOTO 4 HERE]

After making the changes, we tested the traffic light again. The button worked and the red, yellow, and green LEDs turned on in the correct order.

[ADD PHOTO 5 HERE]

Our final circuit has a red LED, yellow LED, green LED, and a button on a breadboard. The button is the input, and the three LEDs are the outputs. When the button is pressed, the Arduino starts the traffic light sequence.

Our final code:

Technical Tidbit: How the Button Works

The button is an input because it gives the Arduino information when it is pressed. The Arduino reads the button and uses that information to start the traffic light sequence. The LEDs are outputs because the Arduino controls when they turn on and off.

We also learned about debouncing. Sometimes a button can register a press more than once because of the way the button makes contact. This is called bouncing. Debouncing helps the Arduino recognize the action as one button press instead of multiple presses. This is important because we want one press of the button to start the sequence correctly.

Peer Support

When we were working on our circuit, some of the lights were not working correctly. Elaine told me to check if the wires were in the wrong place. I checked them and realized that some of the wires were in the wrong places. I moved them to the right places, and then the circuit started working correctly.

Her suggestion helped me figure out what to check instead of just changing random things. It also helped me understand that checking the wiring is an important part of fixing a circuit. After fixing the wires, I was able to test the circuit again and see that the lights were working.

Use-Case Reflection

A traffic light can help control the movement of cars and people. Our project is a simple version of this idea because it uses lights to show a sequence. To make our project more like a real traffic light, we would need to make the timing more realistic and add something that could detect cars or people.

If we continued working on it, we would use debugging because we would need to find and fix problems with the wiring or code. We could also add a sensor so the traffic light could respond to cars or people instead of only starting when the button is pressed.

This project helped me practice using an input and multiple outputs together. It also helped me learn that testing the circuit and fixing mistakes are important parts of making an Arduino project work.
