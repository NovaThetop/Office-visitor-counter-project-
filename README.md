# Office-visitor-counter-project-
Made a visitor counter for STUHI office from hardware to track visitors at different times from your phone. 

IoT Bi-Directional Visitor Counter (ESP32 + MicroPython)
IoT-enabled visitor counter built with an ESP32 and two HC-SR04 ultrasonic sensors. It accurately counts people entering and exiting a room and updates the total in real-time to a mobile phone using the Blynk IoT platform, which also collects data and makes analytics.

This build is designed to be minimalistic—no physical displays or noisy relays, just seamless cloud syncing via Wi-Fi.

Hardware Used :
ESP32 Development Board
2x HC-SR04 Ultrasonic Sensors (One for Entry, one for Exit)
2 Breadboards where esp32 straddles the center of the both breadboards & Jumper Wires
Micro-USB Cable (for programming and power)
0 resistors but highly recommend using them to avoid burning your esp32 
-

Simple Wiring Guide
The wiring is straightforward. The ESP32 is the brain, and we power the 5V sensors using the ESP32's Vin pin (which draws 5V directly from the USB connection).

Component	Pin	Connects to (ESP32 / Breadboard)
Power	VCC	Vin (Red 5V Rail)
Power	GND	GND (Blue Ground Rail)
Sensor 1 (Entry)	Trig	Pin D32
Sensor 1 (Entry)	Echo	Pin D33
Sensor 2 (Exit)	Trig	Pin D4
Sensor 2 (Exit)	Echo	Pin D2
Two jumper wires to connect the two breadboards used.
(Note: The ESP32 is a 3.3V logic device. While connecting the 5V Echo pin directly to the ESP32 works for prototyping, using a simple voltage divider on the Echo pins is recommended for long-term use).

The Algorithm: How It Knows Your Direction
Measuring distance is easy, but tracking direction and preventing false counts requires a specific sequence logic. If you just wave your hand in front of one sensor, or if two people cross paths, a simple counter will fail to count. 

Here is the "State Machine" algorithm used in this code:
-

The Trigger: The loop constantly pings both sensors. When an object breaks the threshold ( < 130 cm) on Sensor 1, the sequence initiates.

The Countdown Window: Once Sensor 1 is triggered, a timeout variable gives the person exactly 4 seconds to reach Sensor 2.

Confirmation: If Sensor 2 is triggered before the 4 seconds run out, it's considered a "Successful Entry" (+1). If the timer runs out, the event is discarded as a false alarm.

Debounce : After a successful count, the code intentionally sleeps for 1-2 seconds. This prevents a slow-walking person from being counted twice.

Reverse Logic: The exact same sequence runs in reverse. If Sensor 2 is triggered first, it waits for Sensor 1 to confirm a "Successful Exit" (-1).

_________________________________________________________________________________________________________________

How the Wi-Fi & Mobile App Work
In this project I used Blynk IoT to send data to your phone without needing complex web servers, it also can keep all of your other sensor projects in one window in the phone. 

The Connection: When the ESP32 powers on, it connects to your local Wi-Fi router.(through your input of your wifi name and password into the code)

Authentication: It then reaches out to the Blynk Cloud servers using a unique AUTH_TOKEN assigned to your project ( you need to find it in the app or website of your "device" on the blynk. ) 

Virtual Pins: Instead of sending physical voltages, the ESP32 pushes the visitor_count integer to a Virtual Pin (V1) in the cloud on the Blynk.

The App: The Blynk app on your phone subscribes to this Virtual Pin. The moment the ESP32 updates V1, the cloud pushes that new number to your phone screen instantly.
_________________________________________________________________________________________________________________

Setup & Installation
Flash MicroPython: Use Thonny IDE to download the MicroPython onto your ESP32.

Install Blynk: Go to Tools > Manage Packages in Thonny and install blynk-library-python to your esp32.

Blynk Dashboard: Create a template on Blynk.cloud, add a Datastream as a Virtual Pin (V1), and get your Auth Token.

Upload Code: Create a file named main.py on your ESP32, paste the code below, and update your Wi-Fi and Blynk credentials.
