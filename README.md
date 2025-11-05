# 🛰️ Space Bot — API Investigation

### Module: Web Technology (5FTC2167)
### Assignment 1 – Space Bot  
### Author: Shreyas Jaiswal  

---

## 1️⃣ Webex Messaging API

The **Webex Messaging API** allows developers to create bots and automation tools that can interact with Webex rooms using RESTful endpoints.  
In the Space Bot project, it is used to:
- Retrieve a list of Webex rooms a user or bot is part of.  
- Continuously monitor a selected room for new messages.  
- Post formatted ISS location updates back to that room.

### 🔗 Key Endpoints

| Purpose | Method | Endpoint | Description |
|----------|---------|-----------|--------------|
| List Rooms | `GET` | `https://webexapis.com/v1/rooms` | Returns all rooms accessible by the user or bot. |
| Get Latest Message | `GET` | `https://webexapis.com/v1/messages?roomId={roomId}&max=1` | Fetches the most recent message from a selected room. |
| Post Message | `POST` | `https://webexapis.com/v1/messages` | Sends the bot’s formatted ISS location message. |

**Authentication Header**

Authorization: Bearer <ACCESS_TOKEN>
Content-Type: application/json


All requests require a valid **access token**, which ensures that only authorized users or bots can access the Webex environment.  
Tokens should never be exposed publicly for security reasons.

---

## 2️⃣ ISS Current Location API

The **ISS Current Location API** provides real-time latitude and longitude coordinates of the International Space Station (ISS).

**Endpoint:**
https://api.wheretheiss.at/v1/satellites/25544

**Example Response:**
```json
{
  "name": "iss",
  "id": 25544,
  "latitude": 51.6041,
  "longitude": -1.0328,
  "altitude": 414.6,
  "velocity": 27600,
  "timestamp": 1730655056
}
```
The Space Bot extracts the following fields:
Latitude & Longitude: identify the ISS position on Earth.
Timestamp: represents the exact time the data was captured (in epoch format).
These values are then passed to the Geocoding API to find the corresponding location name on Earth.

3️⃣ Geocoding API

The Geocoding API converts GPS coordinates into human-readable addresses (e.g., city, region, or country).
In this project, Mapbox Geocoding API is used due to its free developer tier and precise location data.

Endpoint Format
https://api.mapbox.com/geocoding/v5/mapbox.places/{longitude},{latitude}.json?access_token=<YOUR_KEY>

{
  "features": [
    {
      "place_name": "Bolívar, Venezuela, Bolivarian Republic of"
    }
  ]
}

Extracted Fields

place_name: readable description of the location.

context: contains nested data such as city, region, and country.

Alternative Providers

OpenCage Geocoder

OpenWeatherMap Reverse Geocoding

Why Mapbox?
It is developer-friendly, stable, and returns consistent JSON responses that are easy to parse with Python’s requests library.

4️⃣ Epoch → Human Time Conversion

The ISS API provides timestamps in epoch (UNIX) format, which represents seconds since 1 January 1970 (UTC).
To convert it into a human-readable date and time:

import time
epoch = 1730655056
print(time.ctime(epoch))
# Output: Tue Nov 04 14:10:56 2025

Or using the datetime module:

from datetime import datetime
epoch = 1730655056
print(datetime.fromtimestamp(epoch).strftime("%a %b %d %H:%M:%S %Y"))

This makes the output easily understandable for users when displayed in the Webex room.

5️⃣ Web Architecture

The Space Bot follows a client–server architecture where the bot acts as an intermediary between multiple web services.
Communication occurs through REST APIs using HTTP requests and JSON responses.

🧭 Architecture Flow

                       [User] → [Webex Room] → [Webex API] → [Python Space Bot]
                                          ↓
                                 [ISS + Geocoding APIs]
                                          ↓
                              [Webex Room: Bot Response]
🔄 How It Works

The user sends a command like /5 in the Webex room.
The Space Bot detects this command and waits 5 seconds.
It retrieves the ISS coordinates via the ISS API.
It converts the coordinates to a readable address using the Geocoding API.
It converts the epoch timestamp to a human-readable format.
It posts a formatted response message back to the Webex room.

      Example Output
      On Tue Nov 04 14:10:56 2025, the ISS was flying over Bolívar, Venezuela (7.0680°, -61.2094°)

6️⃣ MVC Design Pattern - The Space Bot follows the Model–View–Controller (MVC) design pattern to ensure clean structure and maintainability.

| **Component** | **Role** |
|----------------|-----------|
| **Model** | Handles all data operations — fetching API responses, storing coordinates, timestamps, and locations. |
| **View** | Displays the output message sent to Webex (formatted with time and location). |
| **Controller** | Processes logic: detects `/seconds` commands, calls APIs, handles delays, and posts the final message. |

      
This architecture demonstrates smooth API integration, allowing real-time global tracking of the ISS.

Example Workflow :
User → Controller → Model → View → Webex Message

The Controller interprets /5 and initiates logic.
The Model retrieves and processes ISS + Geocoding data.
The View formats and displays the final message to the user.

Advantages :

Promotes modular, organized, and reusable code.
Makes future feature additions (like SpaceX launch info) easier.
Improves debugging and testing by separating data, logic, and display 

##  Summary

| **Section** | **Function** | **Key Outcome** |
|--------------|---------------|------------------|
| **Webex Messaging API** | Communication layer | Enables bot–user interaction in Webex. |
| **ISS Current Location API** | Data source | Provides live ISS coordinates and time. |
| **Geocoding API** | Data translation | Converts coordinates to a readable location. |
| **Epoch Conversion** | Readability | Displays clear, human-friendly timestamps. |
| **Web Architecture** | Structure | Integrates multiple services through REST APIs. |
| **MVC Pattern** | Code organization | Keeps the project modular and maintainable. |

🔗 References

Webex Developer API Docs
Where the ISS At API
Mapbox Geocoding API
Python Time Library
Python Datetime Library
