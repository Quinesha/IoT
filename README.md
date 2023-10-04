# IoT
Documentation for IoT class

# Assignment
Stel de LEDstrip kleur via een colorpicker in via Adafruit IO. adafruit IO . 
Dit is een andere library dan Fastled, er bestaan meerdere libraries (software) voor dezelfde hardware. 
Soms is de ene meer geschikt voor wat jij wil, soms de andere.

## Stap 1 
Collect the required components.
In this case we'll require

- 1x Arduino Board
- 1x LEDstip
- Arduino IDE
- Adafruit library

## Step 2
Install the right library in the Arduino IDE

Select the Library manager of the Arduino IDE search for "Adafruit IO Arduino"

<img width="198" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/5c335852-e8dc-4bea-8652-00b51778b4b6">

Scroll trhough the serach results until you find: "Adafruit AW9523 by Adafruit"
When you've succesfully found the right library, click on install -> install all.

Now wait until it's been installed.

## Step 3
Next, connect the cables from the LEDstrip to the Arduino board.

- connect a wire from 5V to 3V3
- connect the middle wire (Din) to D5
- connect GND wire to GND

Now you're all set :)

## Step 4
Write your code and if it doesn't go well, ask ChatGPT for help.

''' 
#include <Adafruit_NeoPixel.h>

#define PIN        D5  // Define the pin where the data line is connected to
#define NUMPIXELS  15  // Define the number of NeoPixels

Adafruit_NeoPixel strip = Adafruit_NeoPixel(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);

void setup() {
  strip.begin();  // Initialize the strip
  strip.show();   // Initialize all pixels to 'off'
}

void loop() {
  for (int i = 0; i < strip.numPixels(); i++) {
    marqueeRainbow(i);
    delay(100); // Adjust for desired speed of marquee movement
  }

  for (int j = 0; j < 2; j++) {
    blinkPattern1();
    delay(500); // Pause for 0.5 seconds
    blinkPattern2();
    delay(500); // Pause for 0.5 seconds
  }
}

void marqueeRainbow(int position) {
  strip.clear();
  uint32_t color = Wheel((position * 256 / strip.numPixels()) & 255);
  strip.setPixelColor(position, color);
  strip.show();
}

void blinkPattern1() {
  for (int i = 0; i < strip.numPixels(); i++) {
    if (i % 3 == 0) {
      strip.setPixelColor(i, strip.Color(255, 0, 0));  // Red
    } else if (i % 3 == 1) {
      strip.setPixelColor(i, strip.Color(0, 255, 0));  // Green
    } else {
      strip.setPixelColor(i, strip.Color(0, 0, 255));  // Blue
    }
  }
  strip.show();
}

void blinkPattern2() {
  for (int i = 0; i < strip.numPixels(); i++) {
    if (i % 3 == 0) {
      strip.setPixelColor(i, strip.Color(0, 255, 0));  // Green
    } else if (i % 3 == 1) {
      strip.setPixelColor(i, strip.Color(0, 0, 255));  // Blue
    } else {
      strip.setPixelColor(i, strip.Color(255, 0, 0));  // Red
    }
  }
  strip.show();
}

uint32_t Wheel(byte WheelPos) {
  WheelPos = 255 - WheelPos;
  if(WheelPos < 85) {
    return strip.Color(255 - WheelPos * 3, 0, WheelPos * 3);
  }
  if(WheelPos < 170) {
    WheelPos -= 85;
    return strip.Color(0, WheelPos * 3, 255 - WheelPos * 3);
  }
  WheelPos -= 170;
  return strip.Color(WheelPos * 3, 255 - WheelPos * 3, 0);
}

'''

## Step 5
Upload your code by clicking on the right facing arrow.

<img width="30" alt="image" src="https://github.com/Quinesha/IoT/assets/113134070/4bb33fb2-552e-4fbd-a822-4400ce27919a">

Wait until the code has uploaded anddd watch the magic happen!


## My mistakes
None, everything went exactly the way I needed it to


## Sources
- My own imagination
- ChatGPT
- https://docs.google.com/document/d/1l_9xpU-n7Rhxj-L3wI72Kvc3GQ9q6tFl955J2NTYF9k/edit
- https://docs.google.com/document/d/14jhaxaJUhuu0p6_u_oNj8_50Y6PAmtrvcePtKc0HDGs/edit
