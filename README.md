# Smart Greenhouse Monitoring System 🌱

A modern, IoT-based web dashboard for real-time monitoring of greenhouse environmental conditions using ESP8266, Node.js, MySQL, and beautiful data visualization.

## 🎯 Features

- **Real-time Monitoring**: Live sensor data updates from local Node.js backend
- **Interactive Charts**: Beautiful line charts showing historical trends
- **Multiple Sensors**: 
  - Temperature (DHT11/DHT22)
  - Humidity (DHT11/DHT22)
  - Soil Moisture
  - Light Intensity (LDR)
- **Responsive Design**: Works perfectly on desktop, tablet, and mobile devices
- **Modern UI**: Dark theme with glassmorphism effects and smooth animations
- **AI Predictions**: Forecast future sensor values based on historical data
- **Statistics**: Average values calculation and display

## 🚀 Quick Start

### 1. Backend Setup (Node.js & MySQL)

1.  **Install Node.js**: Download and install from [nodejs.org](https://nodejs.org/).
2.  **Install MySQL**: Download and install MySQL Server.
3.  **Create Database**:
    Run the SQL commands in `schema.sql` to create the database and table.
    ```sql
    CREATE DATABASE stockvision;
    USE stockvision;
    CREATE TABLE sensor_readings (...);
    ```
4.  **Configure Environment**:
    Update `.env` file with your MySQL credentials.
    ```
    DB_HOST=localhost
    DB_USER=root
    DB_PASSWORD=your_password
    DB_NAME=stockvision
    PORT=3000
    ```
5.  **Install Dependencies**:
    ```bash
    npm install
    ```
6.  **Start Server**:
    ```bash
    npm start
    ```
    The server will run on `http://localhost:3000`.

### 2. Configure ESP8266

Upload the following code to your ESP8266 (NodeMCU). Make sure to update `YOUR_WIFI_SSID`, `YOUR_WIFI_PASSWORD`, and `YOUR_SERVER_IP`.

```cpp
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>
#include <DHT.h>

// WiFi credentials
const char* ssid = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";

// Server settings
const char* serverUrl = "http://YOUR_SERVER_IP:3000/api/data";

// Sensor pins
#define DHTPIN D4
#define DHTTYPE DHT22
#define SOIL_PIN A0
#define LDR_PIN D5

DHT dht(DHTPIN, DHTTYPE);
WiFiClient client;
HTTPClient http;

void setup() {
  Serial.begin(115200);
  pinMode(LDR_PIN, INPUT);
  
  dht.begin();
  WiFi.begin(ssid, password);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  
  Serial.println("\nWiFi Connected!");
}

void loop() {
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();
  int soilMoisture = map(analogRead(SOIL_PIN), 0, 1023, 0, 100);
  int lightIntensity = map(digitalRead(LDR_PIN), 0, 1, 0, 100);
  
  if (WiFi.status() == WL_CONNECTED) {
    http.begin(client, serverUrl);
    http.addHeader("Content-Type", "application/json");
    
    String payload = "{\"temperature\":" + String(temperature) + 
                     ",\"humidity\":" + String(humidity) + 
                     ",\"soil_moisture\":" + String(soilMoisture) + 
                     ",\"light_intensity\":" + String(lightIntensity) + "}";
                     
    int httpResponseCode = http.POST(payload);
    
    if (httpResponseCode > 0) {
      Serial.print("HTTP Response code: ");
      Serial.println(httpResponseCode);
    } else {
      Serial.print("Error code: ");
      Serial.println(httpResponseCode);
    }
    http.end();
  }
  
  delay(15000); // Update every 15 seconds
}
```

### 3. Launch Dashboard

1.  Ensure the Node.js server is running (`npm start`).
2.  Open `http://localhost:3000` in your browser.
    (The server serves the static files as well).

## 📊 Dashboard Components

### Sensor Cards
- **Temperature**: Displays current temperature in °C with range indicator
- **Humidity**: Shows humidity percentage with visual bar
- **Soil Moisture**: Moisture level percentage for irrigation decisions
- **Light Intensity**: Light availability for plant growth

### Charts
- **Temperature & Humidity Trends**: Historical line chart
- **Soil Moisture & Light Trends**: Historical line chart
- Adjustable data points (20, 50, or 100)

### Statistics
- Average values for all sensors
- Calculated from historical data

## 🛠️ Hardware Requirements

- **ESP8266 (NodeMCU)**: Wi-Fi enabled microcontroller
- **DHT11/DHT22**: Temperature and humidity sensor
- **Soil Moisture Sensor**: Measures soil water content
- **LDR (Light Dependent Resistor)**: Measures light intensity
- **Breadboard & Jumper Wires**: For connections
- **USB Cable**: For power and programming

## 💻 Software Requirements

- **Arduino IDE**: For programming ESP8266
- **Node.js**: Backend runtime
- **MySQL**: Database
- **Modern Web Browser**: Chrome, Firefox, Edge, or Safari

## 🎨 Technologies Used

### Frontend
- **HTML5**: Semantic markup
- **CSS3**: Modern styling with custom properties
- **JavaScript (ES6+)**: Application logic
- **Chart.js**: Data visualization

### Backend
- **Node.js & Express**: API Server
- **MySQL**: Database storage

### Design
- **Glassmorphism**: Modern UI aesthetic
- **Responsive Grid**: Mobile-first design
- **Custom Animations**: Smooth transitions
- **Dark Theme**: Reduced eye strain

## 👥 Team

**Acropolis Institute of Technology and Research**  
Department of Information Technology

### Group Members
- **Adarsh Patel** (0827IT231007)
- **Atharva Rathore** (0827IT231035)
- **Gourav Mahajan** (0827IT231047)
- **Kunal Sonare** (0827IT231078)

### Guides
- **Internal Guide**: Prof. Mahendra Verma
- **External Guide**: Ms. Neha Sonare

## 📄 License

This project is created for educational purposes as part of the IT department curriculum.
