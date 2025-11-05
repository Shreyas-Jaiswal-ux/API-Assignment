#  Space Bot — API Investigation

### Module: Web Technology (5FTC2167)  
### Assignment 1 – Space Bot  
### Author: Shreyas Jaiswal  

---

##  Webex Messaging API 

The **Webex Messaging API** allows developers to build bots or applications that interact with Webex rooms through RESTful calls. In the Space Bot project, it is used to:
- Retrieve a list of Webex rooms a user or bot belongs to.
- Continuously check new messages in a selected room.
- Post formatted ISS location information back to that room.

**Key Endpoints**
| Purpose | Method | Endpoint | Description |
|----------|---------|-----------|--------------|
| List Rooms | `GET` | `https://webexapis.com/v1/rooms` | Returns all rooms accessible by the user or bot. |
| Get Latest Message | `GET` | `https://webexapis.com/v1/messages?roomId={roomId}&max=1` | Fetches the latest message posted in a room. |
| Post Message | `POST` | `https://webexapis.com/v1/messages` | Sends the Space Bot’s formatted response message. |

**Authentication Header**



Authorization: Bearer <Access_Token>
Content-Type: application/json

Each request requires an **access token** to verify identity and ensure only authorized users can interact with Webex services. Tokens must be kept private to avoid misuse.

---

##  ISS Current Location API 

The **ISS Current Location API** provides real-time tracking information about the International Space Station’s position in orbit.  
The most commonly used and stable endpoint is:
https://api.wheretheiss.at/v1/satellites/25544

**Example Response**
```json
{
  "name": "iss",
  "id": 25544,
  "latitude": 51.6041,
  "longitude": -1.0328,
  "altitude": 414.6,
  "velocity": 27600,
  "visibility": "daylight",
  "timestamp": 1730655056
}


Purpose in the Space Bot :

The bot extracts the latitude, longitude, and timestamp values.

These coordinates are then sent to a geocoding API to determine what location the ISS is currently flying over.

```
3️ )Geocoding API 

The Geocoding API translates GPS coordinates into a readable physical location such as a city, region, or country.
In this project, Mapbox Geocoding API is used for its free developer tier and structured JSON responses.

Endpoint Format

https://api.mapbox.com/geocoding/v5/mapbox.places/{longitude},{latitude}.json?access_token=<YOUR_KEY>

Example JSON Output

{
  "type": "FeatureCollection",
  "features": [
    {
      "place_name": "Bolívar, Venezuela, Bolivarian Republic of"
    }
  ]
}

Data Extracted

place_name → readable location

context → nested elements like city, state, and country

Alternative APIs

OpenCage Geocoder (https://api.opencagedata.com/geocode/v1/json)

OpenWeatherMap Reverse Geocoding

Mapbox is preferred here because it is simple, quick, and returns detailed geographical context for coordinates.


4 ) Epoch to Human Time Conversion 
The ISS API provides timestamps in epoch format, which counts seconds since 1 January 1970 UTC.
For a human-friendly time format, Python’s standard libraries can convert it easily.

Example Conversion

import time
epoch = 1730655056
human_time = time.ctime(epoch)
print(human_time)
# Output: Tue Nov 01 14:10:56 2025




