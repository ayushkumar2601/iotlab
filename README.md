# iotlab
# Arduino UNO & Raspberry Pi Sensor Experiments

---

# 1. Turn ON LED When Button is Pressed

## Components
- Arduino UNO / Raspberry Pi
- LED
- Push Button
- 220Ω Resistor
- Breadboard
- Jumper Wires

---

# Arduino UNO

## Pin Connections
- LED + → Pin 13
- LED - → GND
- Button One Side → Pin 2
- Button Other Side → GND

---

## Arduino Code

```cpp
int buttonPin = 2;
int ledPin = 13;

void setup() {
  pinMode(buttonPin, INPUT_PULLUP);
  pinMode(ledPin, OUTPUT);
}

void loop() {
  int buttonState = digitalRead(buttonPin);

  if(buttonState == LOW) {
    digitalWrite(ledPin, HIGH);
  }
  else {
    digitalWrite(ledPin, LOW);
  }
}
```

---

# Raspberry Pi

## Dependencies Install

```bash
sudo apt update
sudo apt install python3-rpi.gpio
```

---

## Pin Connections
- LED + → GPIO17
- LED - → GND
- Button One Side → GPIO27
- Button Other Side → GND

---

## Python Code

```python
import RPi.GPIO as GPIO
import time

LED = 17
BUTTON = 27

GPIO.setmode(GPIO.BCM)
GPIO.setup(LED, GPIO.OUT)
GPIO.setup(BUTTON, GPIO.IN, pull_up_down=GPIO.PUD_UP)

try:
    while True:
        if GPIO.input(BUTTON) == 0:
            GPIO.output(LED, GPIO.HIGH)
        else:
            GPIO.output(LED, GPIO.LOW)

        time.sleep(0.1)

except KeyboardInterrupt:
    GPIO.cleanup()
```

---

# 2. IR Sensor Object Detection with LED and Buzzer

## Components
- IR Sensor
- LED
- Buzzer
- Arduino UNO / Raspberry Pi

---

# Arduino UNO

## Pin Connections
- IR OUT → Pin 2
- LED + → Pin 8
- Buzzer + → Pin 9
- All GND → GND

---

## Arduino Code

```cpp
int irSensor = 2;
int led = 8;
int buzzer = 9;

void setup() {
  pinMode(irSensor, INPUT);
  pinMode(led, OUTPUT);
  pinMode(buzzer, OUTPUT);
}

void loop() {
  int state = digitalRead(irSensor);

  if(state == LOW) {
    digitalWrite(led, HIGH);
    digitalWrite(buzzer, HIGH);
  }
  else {
    digitalWrite(led, LOW);
    digitalWrite(buzzer, LOW);
  }
}
```

---

# Raspberry Pi

## Dependencies Install

```bash
sudo apt install python3-rpi.gpio
```

---

## Pin Connections
- IR OUT → GPIO18
- LED → GPIO23
- Buzzer → GPIO24
- GND → GND

---

## Python Code

```python
import RPi.GPIO as GPIO
import time

IR = 18
LED = 23
BUZZER = 24

GPIO.setmode(GPIO.BCM)

GPIO.setup(IR, GPIO.IN)
GPIO.setup(LED, GPIO.OUT)
GPIO.setup(BUZZER, GPIO.OUT)

try:
    while True:
        if GPIO.input(IR) == 0:
            GPIO.output(LED, 1)
            GPIO.output(BUZZER, 1)
        else:
            GPIO.output(LED, 0)
            GPIO.output(BUZZER, 0)

        time.sleep(0.1)

except KeyboardInterrupt:
    GPIO.cleanup()
```

---

# 3. Turn ON LED Using LDR Sensor

## Components
- LDR
- LED
- 10KΩ Resistor
- Arduino UNO / Raspberry Pi

---

# Arduino UNO

## Pin Connections
- LDR → A0
- LED → Pin 13
- Resistor → Voltage Divider with LDR

---

## Arduino Code

```cpp
int ldr = A0;
int led = 13;

void setup() {
  pinMode(led, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  int value = analogRead(ldr);

  Serial.println(value);

  if(value < 500) {
    digitalWrite(led, HIGH);
  }
  else {
    digitalWrite(led, LOW);
  }

  delay(100);
}
```

---

# Raspberry Pi

NOTE: Raspberry Pi has no analog pin. Use an MCP3008 ADC converter.

---

## Dependencies Install

```bash
sudo apt update
sudo apt install python3-pip
pip3 install gpiozero spidev
```

---

## Pin Connections
- LDR → MCP3008 CH0
- LED → GPIO17
- MCP3008 connected to SPI pins

---

## Python Code

```python
from gpiozero import MCP3008, LED
from time import sleep

ldr = MCP3008(channel=0)
led = LED(17)

while True:
    value = ldr.value
    print(value)

    if value < 0.5:
        led.on()
    else:
        led.off()

    sleep(0.1)
```

---

# 4. Measure Temperature and Humidity Using DHT22

## Components
- DHT22 Sensor
- Arduino UNO / Raspberry Pi

---

# Arduino UNO

## Library Install
Install:
- DHT sensor library
- Adafruit Unified Sensor

From Arduino IDE:
Sketch → Include Library → Manage Libraries

Search:
- DHT sensor library by Adafruit

---

## Pin Connections
- DHT22 DATA → Pin 2
- VCC → 5V
- GND → GND

---

## Arduino Code

```cpp
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT22

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
}

void loop() {
  float temp = dht.readTemperature();
  float hum = dht.readHumidity();

  Serial.print("Temperature: ");
  Serial.print(temp);
  Serial.println(" °C");

  Serial.print("Humidity: ");
  Serial.print(hum);
  Serial.println(" %");

  delay(2000);
}
```

---

# Raspberry Pi

## Dependencies Install

```bash
sudo apt update
sudo apt install python3-pip
pip3 install Adafruit_DHT
```

---

## Pin Connections
- DATA → GPIO4
- VCC → 3.3V
- GND → GND

---

## Python Code

```python
import Adafruit_DHT
import time

sensor = Adafruit_DHT.DHT22
pin = 4

while True:
    humidity, temperature = Adafruit_DHT.read_retry(sensor, pin)

    if humidity is not None and temperature is not None:
        print(f"Temperature: {temperature:.1f}°C")
        print(f"Humidity: {humidity:.1f}%")
    else:
        print("Sensor failure")

    time.sleep(2)
```

---

# Common Arduino IDE Setup

## Install Arduino IDE
Download from:

- :contentReference[oaicite:0]{index=0}

---

# Common Raspberry Pi Setup

## Enable GPIO & SPI

```bash
sudo raspi-config
```

Enable:
- SPI
- GPIO

Reboot Raspberry Pi after enabling.

---

# Required Raspberry Pi Libraries Summary

```bash
sudo apt update
sudo apt install python3-rpi.gpio python3-pip
pip3 install gpiozero spidev Adafruit_DHT
```

---
