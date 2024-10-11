Smart Geyser:

This code is for a temperature control system using a NodeMCU ESP8266 board, a DS18B20 temperature sensor, a gas sensor, and a relay module. The system is controlled using two push buttons and a Blynk app.

Key Features:

Reads temperature from the DS18B20 sensor and sends it to the Blynk app.
Reads gas sensor value and sends it to the Blynk app.
Allows users to set a temperature threshold using the Blynk app.
Updates the temperature threshold value based on the button states (up and down).
Controls the relay module based on the temperature threshold value.
Displays the current temperature and set temperature on an LCD display.


Libraries Used:
->Wire.h
->LiquidCrystal_I2C.h
->ESP8266WiFi.h
->WiFiClient.h
->BlynkSimpleEsp8266.h
->OneWire.h
->DallasTemperature.h


Hardware Components:
->NodeMCU ESP8266 board
->DS18B20 temperature sensor
->Relay module
->LCD display
->Push buttons (2)
->Blynk app


Functionality:
->The system reads the temperature from the DS18B20 sensor and sends it to the Blynk app.
->The system reads the gas sensor value and sends it to the Blynk app.
->The user can set a temperature threshold using the Blynk app.
->The system updates the temperature threshold value based on the button states (up and down).
->The system controls the relay module based on the temperature threshold value.
->The system displays the current temperature and set temperature on an LCD display.
