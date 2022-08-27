# IOT-week-4-tasks

**Task 3:POST and GET from database (Temperature and Humidity)**

**1. you need the following parts:**
   
   i. ESP32 development board
   
   ii. DHT22 or DHT11 Temperature and Humidity Sensor
   
   iii. 4.7k Ohm Resistor
   
   iv. Breadboard
   
   v. Jumper wires
   
   ![image](https://user-images.githubusercontent.com/108147030/187028334-dcf0e428-e0cb-4d53-9789-9b32502c2a5f.png)
   
**2. For the circuit diagram you need to wire it as shown below:**
   
   ![image](https://user-images.githubusercontent.com/108147030/187028372-953253ac-4908-4378-a874-384ff60a6226.png)
   
**3. Installing Libraries**

  1) Installing the DHT sensor library:
  
  To read from the DHT sensor using Arduino IDE, you need to install the DHT sensor library(https://github.com/adafruit/DHT-sensor-library). Follow the next steps to install the library.

i. download the DHT Sensor library. You should have a .zip folder in your Downloads folder

ii. Unzip the .zip folder and you should get DHT-sensor-library-master folder

iii. Rename your folder from DHT-sensor-library-master to DHT_sensor

iv. Move the DHT_sensor folder to your Arduino IDE installation libraries folder

v. Finally, re-open your Arduino IDE
  
  2) Installing the Adafruit Unified Sensor Driver
  
   You also need to install the Adafruit Unified Sensor Driver library to work with the DHT sensor (https://github.com/adafruit/Adafruit_Sensor). Follow the next steps to install the library:
   
i. download the Adafruit Unified Sensor library. You should have a .zip folder in your Downloads folder

ii. Unzip the .zip folder and you should get Adafruit_sensor-master folder

iii. Rename your folder from Adafruit_sensor-master to Adafruit_sensor

iv. Move the Adafruit_sensor folder to your Arduino IDE installation libraries folder

v. Finally, re-open your Arduino IDE

  3) Installing the ESPAsyncWebServer library
  
Follow the next steps to install the ESPAsyncWebServer (https://github.com/me-no-dev/ESPAsyncWebServer) library:
   
i. download the ESPAsyncWebServer library. You should have a .zip folder in your Downloads folder

ii. Unzip the .zip folder and you should get ESPAsyncWebServer-master folder

iii. Rename your folder from ESPAsyncWebServer-master to ESPAsyncWebServer

iv. Move the ESPAsyncWebServer folder to your Arduino IDE installation libraries folder

v. Finally, re-open your Arduino IDE

  4) Installing the Async TCP Library for ESP32
  
The ESPAsyncWebServer (https://github.com/me-no-dev/ESPAsyncWebServer) library requires the AsyncTCP (https://github.com/me-no-dev/AsyncTCP) library to work. Follow the next steps to install that library:
   
i. download the AsyncTCP library. You should have a .zip folder in your Downloads folder

ii. Unzip the .zip folder and you should get AsyncTCP-master folder

iii. Rename your folder from AsyncTCP-master to AsyncTCP

iv. Move the AsyncTCP folder to your Arduino IDE installation libraries folder

v. Finally, re-open your Arduino IDE

**4. Open your Arduino IDE and copy the following code**

```ruby
// Import required libraries
#include "WiFi.h"
#include "ESPAsyncWebServer.h"
#include <Adafruit_Sensor.h>
#include <DHT.h>

// Replace with your network credentials
const char* ssid = "REPLACE_WITH_YOUR_SSID";
const char* password = "REPLACE_WITH_YOUR_PASSWORD";

#define DHTPIN 27     // Digital pin connected to the DHT sensor

// Uncomment the type of sensor in use:
//#define DHTTYPE    DHT11     // DHT 11
#define DHTTYPE    DHT22     // DHT 22 (AM2302)
//#define DHTTYPE    DHT21     // DHT 21 (AM2301)

DHT dht(DHTPIN, DHTTYPE);

// Create AsyncWebServer object on port 80
AsyncWebServer server(80);

String readDHTTemperature() {
  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
  // Read temperature as Celsius (the default)
  float t = dht.readTemperature();
  // Read temperature as Fahrenheit (isFahrenheit = true)
  //float t = dht.readTemperature(true);
  // Check if any reads failed and exit early (to try again).
  if (isnan(t)) {    
    Serial.println("Failed to read from DHT sensor!");
    return "--";
  }
  else {
    Serial.println(t);
    return String(t);
  }
}

String readDHTHumidity() {
  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
  float h = dht.readHumidity();
  if (isnan(h)) {
    Serial.println("Failed to read from DHT sensor!");
    return "--";
  }
  else {
    Serial.println(h);
    return String(h);
  }
}

const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE HTML><html>
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.7.2/css/all.css" integrity="sha384-fnmOCqbTlWIlj8LyTjo7mOUStjsKC4pOpQbqyi7RrhN7udi9RwhKkMHpvLbHG9Sr" crossorigin="anonymous">
  <style>
    html {
     font-family: Arial;
     display: inline-block;
     margin: 0px auto;
     text-align: center;
    }
    h2 { font-size: 3.0rem; }
    p { font-size: 3.0rem; }
    .units { font-size: 1.2rem; }
    .dht-labels{
      font-size: 1.5rem;
      vertical-align:middle;
      padding-bottom: 15px;
    }
  </style>
</head>
<body>
  <h2>ESP32 DHT Server</h2>
  <p>
    <i class="fas fa-thermometer-half" style="color:#059e8a;"></i> 
    <span class="dht-labels">Temperature</span> 
    <span id="temperature">%TEMPERATURE%</span>
    <sup class="units">&deg;C</sup>
  </p>
  <p>
    <i class="fas fa-tint" style="color:#00add6;"></i> 
    <span class="dht-labels">Humidity</span>
    <span id="humidity">%HUMIDITY%</span>
    <sup class="units">&percnt;</sup>
  </p>
</body>
<script>
setInterval(function ( ) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("temperature").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "/temperature", true);
  xhttp.send();
}, 10000 ) ;

setInterval(function ( ) {
  var xhttp = new XMLHttpRequest();
  xhttp.onreadystatechange = function() {
    if (this.readyState == 4 && this.status == 200) {
      document.getElementById("humidity").innerHTML = this.responseText;
    }
  };
  xhttp.open("GET", "/humidity", true);
  xhttp.send();
}, 10000 ) ;
</script>
</html>)rawliteral";

// Replaces placeholder with DHT values
String processor(const String& var){
  //Serial.println(var);
  if(var == "TEMPERATURE"){
    return readDHTTemperature();
  }
  else if(var == "HUMIDITY"){
    return readDHTHumidity();
  }
  return String();
}

void setup(){
  // Serial port for debugging purposes
  Serial.begin(115200);

  dht.begin();
  
  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi..");
  }

  // Print ESP32 Local IP Address
  Serial.println(WiFi.localIP());

  // Route for root / web page
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send_P(200, "text/html", index_html, processor);
  });
  server.on("/temperature", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send_P(200, "text/plain", readDHTTemperature().c_str());
  });
  server.on("/humidity", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send_P(200, "text/plain", readDHTHumidity().c_str());
  });

  // Start server
  server.begin();
}
 
void loop(){
  
}
```
Insert your network credentials in the following variables and the code will work straight away.

![image](https://user-images.githubusercontent.com/108147030/187028884-2800b66d-dc1c-4961-8d2e-6f30f81e282a.png)

**5.The web page should look like this**

![image](https://user-images.githubusercontent.com/108147030/187028915-6563a8e7-29ed-4ca9-92d1-fafccf5a0690.png)

**6. upload the code**

upload the code to your ESP32. Make sure you have the right board and COM port selected. After uploading, open the Serial Monitor at a baud rate of 115200. Press the ESP32 reset button. The ESP32 IP address should be printed in the serial monitor.

![image](https://user-images.githubusercontent.com/108147030/187029025-f61fa3c0-17b9-4035-8991-4a5bc9d71723.png)

**7. Web Server Demonstration**

Open a browser and type the ESP32 IP address. Your web server should display the latest sensor readings. Notice that the temperature and humidity readings are updated automatically without the need to refresh the web page.

![image](https://user-images.githubusercontent.com/108147030/187029091-fda730b1-b8cf-4050-b1d2-591df3aab593.png)
