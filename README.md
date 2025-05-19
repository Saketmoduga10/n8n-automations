# n8n Automations

A collection of n8n workflows exported as GitHub-hosted JSON files.  
This repository makes it easy for anyone to import and run these automations in their own n8n instance.

---

## 📦 Project: Daily Weather Report

**Description**  
Automatically fetch today’s weather for your city from OpenWeatherMap and email yourself a neat summary every day at 9 AM.

**Features**  
- Runs on a daily schedule (configurable).  
- Retrieves live weather data via HTTP Request.  
- Extracts and formats key fields (city name, weather description, temperature, feels-like).  
- Sends a plain-text email via Gmail with the report.

---

## 🚀 Technical Details

1. **Schedule Trigger**  
   - Fires once per day at a configurable hour.  
   - Configured under `Schedule Trigger` → Interval: `Days` → Trigger at Hour: `9`.

2. **HTTP Request**  
   - Method: `GET`  
   - URL: `https://api.openweathermap.org/data/2.5/weather`  
   - Query Parameters:  
     - `q`: your city (e.g. `Dallas,US`)  
     - `units`: `metric`  
     - `appid`: **Your** OpenWeatherMap API key

3. **Set (Edit Fields)**  
   - Extracts from the JSON response:  
     - `city` → `{{$json.name}}`  
     - `description` → `{{$json.weather[0].description}}`  
     - `temp` → `{{$json.main.temp}}`  
     - `feels_like` → `{{$json.main.feels_like}}`  
   - Builds a single `emailBody` string:  
     ```
     Today's weather in {{$json.city}}: {{$json.description}}, temp {{$json.temp}}°C (feels like {{$json.feels_like}}°C).
     ```

4. **Gmail (Send a Message)**  
   - Resource: `Message`  
   - Operation: `Send`  
   - To: your email address  
   - Subject (expression):  
     ```
     Daily Weather in {{$node["Edit Fields"].json["city"]}}
     ```  
   - Message (expression):  
     ```
     {{$node["Edit Fields"].json["emailBody"]}}
     ```
   - Authentication: OAuth2 via a Google Cloud OAuth client (see Credentials).

---

## 🛠 Prerequisites

- **n8n** (self-hosted or cloud)  
- **Node.js** (if self-hosting)  
- An **OpenWeatherMap** account & API key (free tier)  
- A **Google Cloud** project with OAuth consent screen and a Gmail-enabled OAuth2 client ID/secret  
- Basic Git & GitHub account

---

## ⚙️ Installation & Usage

1. **Clone this repo**  
   ```bash
   git clone https://github.com/<your-username>/n8n-automations.git
   cd n8n-automations
