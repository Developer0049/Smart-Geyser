
// Define Blynk template ID, name, and auth token
#define BLYNK_TEMPLATE_ID "TMPL3LkQVk-qu"
#define BLYNK_TEMPLATE_NAME "ENERGY METER"
#define BLYNK_AUTH_TOKEN "r_1eYfdNnLRcim3rhPvXw9LMHZS9drXb"

// Enable serial prints (comment out to disable)
#define BLYNK_PRINT Serial

#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// Set LCD address to 0x27 or 0x3F for a 16x2 display
LiquidCrystal_I2C lcd(0x27, 16, 2);

#include <ESP8266WiFi.h>
#include <WiFiClient.h>
#include <BlynkSimpleEsp8266.h>
#include <OneWire.h>
#include <DallasTemperature.h>

// Define pin connections
#define ONE_WIRE_BUS 2 // DS18B20 on Arduino pin 2 (D4 on NodeMCU module)
#define relay D5
#define temp_buttonup D6
#define temp_buttondown D3

// Initialize OneWire and DallasTemperature libraries
OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature DS18B20(&oneWire);

// Global variables
float temp;
int temp_button_upstate = 0;
int temp_button_downstate = 0;
int last_temp_button_upstate = HIGH;
int last_temp_button_downstate = HIGH;

// WiFi credentials
char ssid[] = "Nooh";
char pass[] = "a2s9cthn";

int Val;
int newval;
BlynkTimer timer;
char auth[] = BLYNK_AUTH_TOKEN;
int gassensor = A0;

void setup() {
  // Initialize LCD and turn on backlight
  lcd.init();
  lcd.backlight();

  // Set pin modes
  pinMode(gassensor, INPUT);
  pinMode(relay, OUTPUT);
  digitalWrite(relay, LOW);
  pinMode(temp_buttonup, INPUT_PULLUP);
  pinMode(temp_buttondown, INPUT_PULLUP);

  // Initialize serial console
  Serial.begin(115200);

  // Initialize DS18B20 temperature sensor
  DS18B20.begin();

  // Initialize Blynk
  Blynk.begin(auth, ssid, pass);
}

// Blynk virtual write function for V2
BLYNK_WRITE(V2) {
  Val = param.asInt(); 
  Serial.print("The Threshold value is: ");
  Serial.println(Val);
  Serial.println();
}

void loop() {
  Blynk.run();

  // Read gas sensor value
  int gasvalue = analogRead(gassensor);

  // Read temperature from DS18B20
  DS18B20.requestTemperatures(); 
  temp = DS18B20.getTempCByIndex(0); // Celsius

  // Send temperature and gas values to Blynk
  Blynk.virtualWrite(V0, temp);
  Serial.println(temp);
  Blynk.virtualWrite(V1, gasvalue);

  // Update newval and Val based on button states
  newval = Val;
  temp_button_upstate = digitalRead(temp_buttonup);
  temp_button_downstate = digitalRead(temp_buttondown);
  if (temp_button_upstate == LOW) {
    newval = newval + 1;
    Val = newval;
  }
  if (temp_button_downstate == LOW) {
    newval = newval - 1;
    Val = newval;
  }
  Blynk.virtualWrite(V2, newval);

  // Control relay based on temperature threshold
  if (Val > temp) {
    digitalWrite(relay, HIGH);
  } else {
    digitalWrite(relay, LOW);
  }

  // Update LCD display
  lcd.setCursor(0, 0);
  lcd.print("Temp:");
  lcd.print(temp);
  lcd.setCursor(0, 1);
  lcd.print("Set Temp:");
  lcd.print(float(newval));
}
