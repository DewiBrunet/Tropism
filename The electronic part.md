
### Wiring

https://www.circuito.io/static/reply/index.html?solutionId=616c06a98243f9003016e97f&solutionPath=storage.circuito.io

The wiring for the capacitive touch is wrong in the circuito.io. Pin 2 to the start of the resistor stripe, then PIN 3, then wire to the capacitive touch.



### Test file

Test file for the composant made with circuito.io and tweaked to add the capacitive sensor.

[test_allcomposant.zip](https://github.com/DewiBrunet/Tropism/files/7360110/test_allcomposant.zip)


### Code

Basic servo sweep and changing HUE. No capacitive sensor yet or blending. 
to add : https://github.com/marmilicious/FastLED_examples/blob/master/breath_effect_v2.ino


```

// Include Libraries
#include <CapacitiveSensor.h>
#include <FastLED.h>
#include <Servo.h>

Servo myservo;
int pos = 0;

// pin 2 sends electrical energy - pin 3 senses senses a change
CapacitiveSensor   capSensor = CapacitiveSensor(2,3);
#define CS_RCEVPIN  3
#define CS_SENDPIN 2

#define LED_TYPE PL9823
#define COLOR_ORDER RGB
#define DATA_PIN 4
#define NUM_LEDS 1
#define BRIGHTNESS 255
CRGB leds[NUM_LEDS];



long sensorValue = capSensor.capacitiveSensor(30);

int speedServo=15; // waits the value entered in ms before reaching a new position
int minPos=0;     // minimum angle position of the servo
int maxPos=90;   // maximum angle position of the servo

static float pulseSpeed = 0.5;  // Larger value gives faster pulse.

uint8_t hueA = 15;  // Start hue at valueMin.
uint8_t satA = 230;  // Start saturation at valueMin.
float valueMin = 120.0;  // Pulse minimum value (Should be less then valueMax).

uint8_t hueB = 95;  // End hue at valueMax.
uint8_t satB = 255;  // End saturation at valueMax.
float valueMax = 255.0;  // Pulse maximum value (Should be larger then valueMin).

uint8_t hue = hueA;  // Do Not Edit
uint8_t sat = satA;  // Do Not Edit
float val = valueMin;  // Do Not Edit
uint8_t hueDelta = hueA - hueB;  // Do Not Edit
static float delta = (valueMax - valueMin) / 2.35040238;  // Do Not Edit

void setup()                    
{
  myservo.attach(5); 
    
  Serial.begin(9600);
  delay(2000);  // Startup delay
  FastLED.addLeds<LED_TYPE, DATA_PIN, COLOR_ORDER>(leds, NUM_LEDS);
  FastLED.setBrightness(BRIGHTNESS);
  FastLED.clear();
  FastLED.show();
}

void loop()                    
{
  
float dV = ((exp(sin(pulseSpeed * millis()/2000.0*PI)) -0.36787944) * delta);
  val = valueMin + dV;
  hue = map(val, valueMin, valueMax, hueA, hueB);  // Map hue based on current val
  sat = map(val, valueMin, valueMax, satA, satB);  // Map sat based on current val

  for (pos = minPos; pos <= maxPos; pos += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo.write(pos); 
    leds[0] = CRGB::Red;
    FastLED.show();
    delay(speedServo);
  }
    
  for (pos = maxPos; pos >= minPos; pos -= 1) { // goes from 180 degrees to 0 degrees
    myservo.write(pos);  
    leds[0] = CRGB::White;
    FastLED.show();
    delay(speedServo);    
  }

  
}
```
