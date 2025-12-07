# IoT-Based Water Management System

## 📋 Project Overview

This is an end-to-end IoT solution for water management that combines hardware sensors, cloud connectivity, machine learning anomaly detection, and a web dashboard. The system monitors water usage in real-time, detects anomalies, and visualizes data geographically.

## 🏗️ Architecture

The project consists of three main components:

1. **Microcontroller Code** - ESP32 with ultrasonic sensor
2. **Machine Learning Backend** - Isolation Forest-based anomaly detection
3. **Web Dashboard** - Real-time data visualization with GPS mapping

## 📁 Project Structure

```
lstm/
├── app.py                 # Streamlit anomaly detection app
├── cleaned_output.csv     # Processed sensor data
├── lstm_model.h5          # Trained LSTM model
├── model.joblib           # Isolation Forest model
└── requirements.txt       # Python dependencies

MicroControllerCode/
└── WaterManagement.ino    # ESP32 firmware

WebPage/
└── sesnor_data.html       # Real-time dashboard
```

## 🔧 Components

### 1. Hardware: ESP32 with Ultrasonic Sensor

**File:** [`MicroControllerCode/WaterManagement.ino`](MicroControllerCode/WaterManagement.ino)

**Features:**
- Reads distance from ultrasonic sensor (water level proxy)
- Connects to WiFi and AWS IoT Core via MQTT
- Sends sensor data with GPS coordinates every 30 seconds
- Publishes JSON formatted data to AWS IoT

### 2. Machine Learning: Anomaly Detection

**File:** [`lstm/app.py`](lstm/app.py)

**Features:**
- Loads pre-trained Isolation Forest model
- Preprocesses water usage data (date parsing, normalization)
- Detects anomalies using z-score normalized water usage
- Visualizes anomalies on time-series plots
- Provides statistical insights via Streamlit UI

### 3. Web Dashboard

**File:** [`WebPage/sesnor_data.html`](WebPage/sesnor_data.html)

**Features:**
- Displays real-time water level data in a table
- Interactive GPS map with device markers
- Auto-refreshes every 30 seconds
- Uses Leaflet.js for mapping
- Parses CSV files for data and location

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- ESP32 development board
- Arduino IDE
- AWS IoT Core account with certificates
- Git

### Installation

#### Backend Setup (Anomaly Detection)

```bash
cd lstm
pip install -r requirements.txt
streamlit run app.py
```

The app will start at `http://localhost:8501`

#### Microcontroller Setup

1. Install Arduino IDE
2. Add ESP32 board support
3. Update [`MicroControllerCode/WaterManagement.ino`](MicroControllerCode/WaterManagement.ino) with:
   - WiFi credentials
   - AWS IoT certificates
   - Device latitude/longitude
4. Upload to ESP32

#### Web Dashboard

1. Serve [`WebPage/sesnor_data.html`](WebPage/sesnor_data.html) on a web server:
```bash
cd WebPage
python -m http.server 8000
```

2. Open at `http://localhost:8000/sesnor_data.html`

3. Ensure `data.csv` and `location.csv` are in the WebPage directory

## 📊 Data Formats

### data.csv (Water Level)
```
Device ID,Distance (cm),Timestamp
ESP32-Ultrasonic,45.2,2024-01-15 10:30:45
ESP32-Ultrasonic,44.8,2024-01-15 11:00:45
```

### location.csv (GPS Data)
```
Device,Latitude,Longitude,Timestamp
ESP32-Ultrasonic,10.9038,76.8984,2024-01-15 10:30:45
```

### AWS IoT Message Format
```json
{
  "device_id": "ESP32-Ultrasonic",
  "distance_cm": 45.2,
  "latitude": 10.9038,
  "longitude": 76.8984,
  "timestamp": 1234567890
}
```

## 📈 Model Details

**Anomaly Detection Model:** Isolation Forest

- **Algorithm:** Ensemble of isolation trees
- **Input:** Single feature (normalized water usage)
- **Output:** Anomaly classification (-1 for anomaly, 1 for normal)
- **Loaded from:** `model.joblib`

## 📦 Dependencies

### Python Backend
See [`lstm/requirements.txt`](lstm/requirements.txt):
```
streamlit
pandas
joblib
matplotlib
seaborn
scikit-learn
openpyxl
```

### Microcontroller (ESP32)
- WiFi (built-in)
- WiFiClientSecure (built-in)
- PubSubClient
- ArduinoJson

### Web Frontend
- Leaflet.js (CDN)
- OpenStreetMap (tiles)
- Vanilla JavaScript

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| ESP32 won't connect to WiFi | Verify SSID/password, check WiFi range |
| AWS connection fails | Validate certificates, check endpoint, verify permissions |
| Dashboard shows no data | Ensure CSV files exist, check fetch paths |
| Anomalies not detected | Check data normalization, verify model file exists |
| Streamlit won't start | Run `pip install -r requirements.txt` |
| CORS error on dashboard | Serve via web server instead of file |

## 🔐 Security Notes

- ⚠️ Never commit AWS certificates to git
- Store credentials in environment variables for production
- Use strong WiFi passwords
- Enable MQTT authentication
- Validate all sensor data inputs



## 📞 Support

For issues or questions, please create an issue in the repository.

---

**Last Updated:** 2024
