# Intermediate_CAD
This README contains documentation of my experiences through the new and foreign world of circuit python. If you are going to borrow some of it, please give credit or else you have plagiarised. See other people in my class at: (https://github.com/chssigma/Class_Accounts).
## Table of Contents
* [Hello_CircuitPython](#Hello_CircuitPython)
* [CircuitPython_Servo](#CircuitPython_Servo)
* [CircuitPython_LCD](#CircuitPython_LCD)
* [CircuitPython Ultrasonic Sensor](#CircuitPython_Ultrasonic_Sensor)
---
## Hello_CircuitPython

### Description & Code
```
#Grant Gastinger
#firstAssignment
#Turns the neopixel red and makes it blink
#Code Based of Example code from Engineering 3 Canvas

from time import time
import board
import neopixel
import time

dot = neopixel.NeoPixel(board.NEOPIXEL, 1)
dot.brightness = 0.5 

print("Make it red!")

while True:
dot.fill((255, 0, 0))
time.sleep(0.5)
dot.fill((0,0,0))
time.sleep(0.5)
```
### Evidence
https://user-images.githubusercontent.com/91094422/192555526-bd92a678-54f8-4581-986d-d5b9983d4272.mp4

### Wiring
There is no wiring needed because the neopixel is part of the board. 

### Reflection
 Importing things is very important. You can only use functions that are first imported and the files are put in the lib folder. 
 Unlike Arduino, the loop for circuit python is the "while true:" instead of void loop. 
 You need to check the serial monitor of the board to see if anything is wrong. The serial monitor on the computer is not as useful.
 
 ---

## CircuitPython_Servo

### Description & Code

```
#Grant Gastinger
#servoAssignment
#Makes a 180 servo turn back and forth 
#Based off of code from: 2018 Kattni Rembor for Adafruit Industries

import time
import board
import pwmio
from adafruit_motor import servo

# create a PWMOut object on Pin A2.
pwm = pwmio.PWMOut(board.D2, duty_cycle=2 ** 15, frequency=250)

# Create a servo object, my_servo.
my_servo = servo.Servo(pwm)

while True:
    for angle in range(0, 180, 5):  # 0 - 180 degrees, 5 degrees at a time.
        my_servo.angle = angle
        time.sleep(0.01)
    for angle in range(180, 0, -5): # 180 - 0 degrees, 5 degrees at a time.
        my_servo.angle = angle
        time.sleep(0.01)

```

### Evidence
https://user-images.githubusercontent.com/91094422/193044111-5d46a9d9-b366-4e6e-9d3c-922e085cacc6.mp4

### Wiring
![EliasWiring](https://user-images.githubusercontent.com/91094422/193046672-c67778a8-b683-4d67-8af1-8846cccd1db1.png)

Image credit: Elias Garcia

### Reflection
[Here's where I found the basis for my code](https://learn.adafruit.com/circuitpython-essentials/circuitpython-servo)  There was one issue, you need to use the D pins(digital pins) for the servo, not the A pins.

---

## CircuitPython_DistanceSensor

### Description & Code

```
#Grant Gastinger
#ultraSensor
#Have a neopixel fade colors from green to blue to red when at <5cm, 5-20cm, >20cm
#Based off code from Graham Gilbert-Schroeer
import time
import board
import adafruit_hcsr04
import neopixel
import simpleio

sonar = adafruit_hcsr04.HCSR04(trigger_pin=board.D3, echo_pin=board.D2)
Mason = neopixel.NeoPixel(board.NEOPIXEL, 1)#connecting the neopixel on the board to the code
Mason.brightness = .5  #setting the brightness of the light, from 0-1 brightness
red = 0
blue = 0
green = 0

while True:
    try:
        cm = sonar.distance
        print((sonar.distance))
        time.sleep(0.01)
        if cm < 5:
            Mason.fill((255, 0, 0))#red
        if cm > 5 and cm < 12.5:
            red = simpleio.map_range(cm,5,12.5,255,0)
            blue = simpleio.map_range(cm,5,12.5,0,255)
            Mason.fill((red, 0, blue))
        if cm > 12.5 and cm < 20:   
            blue = simpleio.map_range(cm,12.5,20,255,0)
            green = simpleio.map_range(cm,12.5,20,0,255)
            Mason.fill((0, green, blue))
        if cm > 20:
            Mason.fill((0, 255, 0))#green
    except RuntimeError:
        print("Retrying!")
    time.sleep(0.1)
```

### Evidence
https://user-images.githubusercontent.com/91094422/193284908-be352a88-619a-47df-9850-289edfa8f144.mp4

### Wiring
![eLIAS3](https://user-images.githubusercontent.com/91094422/193285420-b9590fd5-7f85-4ac5-8543-de44b9a417bd.png)

### Reflection
When dealing with if statements, instead of using brackets like in arduino, you need to make sure the code is indented correctly. If a line of code is not indented enough, it will not be run in the loop. The syntax for the map function is "variable = simpleio.map_range(measured variable, input min, input max, output min, output max). "try" is a loop that checks a bunch of if statements and if none of them work, it runs the "except RuntimeError:" loop. 

## CircuitPython_LCD

### Description & Code

```
#Grant Gastinger
#lcdAssignment
#Uses an LCD to display the amount of times a button is clicked. Reverses if switch is flipped.
#Based off code from Engineering 3 canvas

import board
from lcd.lcd import LCD
from lcd.i2c_pcf8574_interface import I2CPCF8574Interface
import time
from digitalio import DigitalInOut, Direction, Pull

# get and i2c object
i2c = board.I2C()
btn = DigitalInOut(board.D2)
btn.direction = Direction.INPUT
btn.pull = Pull.UP
clickCount = 0

switch = DigitalInOut(board.D7)
switch.direction = Direction.INPUT
switch.pull = Pull.UP
# some LCDs are 0x3f... some are 0x27...
lcd = LCD(I2CPCF8574Interface(i2c, 0x3f), num_rows=2, num_cols=16)

lcd.print("Grant")

while True:
    if not switch.value:
        if not btn.value:
            lcd.clear()
            lcd.set_cursor_pos(0, 0)
            lcd.print("Click Count:")
            lcd.set_cursor_pos(0,13)
            clickCount = clickCount + 1
            lcd.print(str(clickCount))
        else:
            pass
    else:
        if not btn.value:
            lcd.clear()
            lcd.set_cursor_pos(0, 0)
            lcd.print("Click Count:")
            lcd.set_cursor_pos(0,13)
            clickCount = clickCount - 1
            lcd.print(str(clickCount))
        else:
            pass
    time.sleep(0.1) # sleep for debounce
```

### Evidence
![image](https://user-images.githubusercontent.com/91094422/197212627-0e835803-2ad7-4bbc-863a-95a5b803c1dd.png)
Video Credit: Elias Garcia

### Wiring
![image](https://user-images.githubusercontent.com/91094422/197210798-31bc93db-ff26-4a1f-b279-9e3f9c75e703.png)
Image Credit: Elias Garcia

### Reflection
If you use a pull up on one of your pins it makes it much easier to use a button because all you need to do is wire it from the pin to gnd and it is a fully functional button. Also make sure that when using a push button you put the wires on diagonal sides, right across from each other won't do anything. Make sure to set the cursor and also clear the lcd before you try to print anything. While true: is pretty much just the void loop in arduino.
