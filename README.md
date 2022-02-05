# Soil-MS-gauge-with-Blynk
I have been working on Blynk with Moisture and DHT11 sensors with NodeMCU esp8266 and the problem is with moisture sensor. Whenever the oisture sensor is placed under dry conditions it gives a value of 1024 or something around that and when it is in under wet conditions it shows a reading of below 600 which are the default readings of moisture sensor. What I'm trying to do is get the values for wet conditions to be near 1024 and for dry conditions to be below 600 whoch is kind of intuitive and basic sense since more the moisture more the reading. The problem is I don't know how to do that. Here is the code -
```
#define BLYNK_PRINT Serial
#include <SPI.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h>
#define BLYNK_PRINT Serial
#include <OneWire.h>
#include <DallasTemperature.h>
#define ONE_WIRE_BUS D2
OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);

char auth[] = "4pd1tQr1dAilNauRpvQQR0--wfNMaoDW";
char ssid[] = "KUMAR";
char pass[] = "SRAVENKY@861614";

#define sensorPin D3
int sensorState = 0;
int lastState = 0;
#define DHTPIN 2
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);
BlynkTimer timer;
void sendSensor()
{
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  if (isnan(h) || isnan(t)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

  Blynk.virtualWrite(V5, h);  //V5 is for Humidity
  Blynk.virtualWrite(V6, t);  //V6 is for Temperature
  
}
void setup()
{
  Blynk.begin(auth, ssid, pass);
  pinMode(sensorPin, INPUT);
  dht.begin();

  timer.setInterval(10000L, sendSensor);
  Serial.begin(115200);
  sensors.begin();
}
int sensor = A0;
void sendTemps()
{
  sensor = analogRead(A0);
  sensors.requestTemperatures();
  float temp = sensors.getTempCByIndex(0);
  Serial.println(temp);
  Serial.println(sensor);
  Blynk.virtualWrite(V1, temp);
  Blynk.virtualWrite(V2, sensor);
  timer.setInterval(10000L, sendSensor);
}
void loop()
{
  Blynk.run();
  timer.run();
}
```
I'm using ARDUINO IDE.
