# Space Bot — API Investigation  
**Module:** Web Technology (5FTC1341)  
**Author:** Shreyas Jaiswal  

## 1. Introduction
This project demonstrates how a Python script can interact with multiple web APIs to collect real-time data and send it automatically to a Webex space.  
The bot monitors a Webex room, waits for commands like `/5` or `/10`, fetches the current ISS position, converts the coordinates to a human-readable location, and posts the response back into the same room.

The system uses:
- Webex Messaging API (REST)
- ISS Current Location API
- Mapbox Reverse Geocoding API
- Python’s `requests` library and JSON parsing

---

## 2. Webex Messaging API
The Webex Messaging API allows communication with Webex spaces using HTTP methods.  
The bot uses three important endpoints:

### Key Endpoints Used
| Purpose | Method | Endpoint | Description |
|--------|--------|----------|-------------|
| List rooms | GET | `https://webexapis.com/v1/rooms` | Retrieve all rooms the user has access to |
| Read latest message | GET | `https://webexapis.com/v1/messages` | Fetch the most recent message from a room |
| Post a message | POST | `https://webexapis.com/v1/messages` | Send text to a Webex room |

### Authentication
All Webex API requests require a valid Bearer token:
```
Authorization: Bearer <token>
```

---

## 3. ISS Current Location API
The ISS API provides the real-time latitude, longitude, altitude, velocity, visibility, and timestamp of the ISS.

Endpoint:
```
https://api.wheretheiss.at/v1/satellites/25544
```

### Example Response
```json
{
  "name": "iss",
  "id": 25544,
  "latitude": -51.5668,
  "longitude": 22.2114,
  "altitude": 423.88,
  "velocity": 27620.49,
  "visibility": "daylight",
  "timestamp": 1762614045
}
```

---

## 4. Geocoding API (Mapbox)
Reverse geocoding converts coordinates into a readable location name.

Example request:
```
https://api.mapbox.com/geocoding/v5/mapbox.places/<lon>,<lat>.json?access_token=<MAPBOX_TOKEN>
```

Example output fields used by the bot:
- `place`
- `region`
- `country`
- `place_name`

---

## 5. Example Output
When the bot receives a command like `/5`, it waits 5 seconds and responds with something like:

```
At Sat Nov 08 09:40:45 2025, the ISS was above Over the ocean near (-51.57°, 22.21°) at coordinates (-51.5668°, 22.2114°).
```

---

## 6. Web Architecture (Full System Explanation)

### Components
- **Webex UI (Client):** Where the user types commands.
- **Python Script (Bot Controller):** Handles polling, API calls, and message formatting.
- **External APIs:** Webex REST API, ISS API, Mapbox Reverse Geocoding.

### End-to-End Process
1. User types `/N` in a Webex room.  
2. Bot polls the room using Webex API.  
3. Bot detects a new message beginning with `/`.  
4. Bot extracts `N` and waits N seconds.  
5. Bot calls ISS API → gets latitude, longitude, timestamp.  
6. Bot calls Mapbox API → converts coordinates to a readable location.  
7. Bot formats a simple response string.  
8. Bot posts the final message back to the Webex room.

### Data Handling
- JSON parsing from APIs  
- Epoch timestamp → human-readable time  
- Coordinates → reverse geocoded location  

---

## 7. MVC Pattern (Applied to This Script)

### Model  
Handles core data operations:
- Fetch ISS position  
- Reverse geocode coordinates  
- Store fields like latitude, longitude, timestamp, location  

### View  
Responsible for building the final message:
- Formats:  
```
At <time>, the ISS was above <location> at coordinates (<lat>, <lon>).
```

### Controller  
Coordinates everything:
- Reads Webex messages  
- Detects `/N` commands  
- Calls APIs  
- Sends back the final message  

This design keeps the logic clean and easier to extend later.

---

## 8. Security and Token Management
Good security practice requires:

- Never hard-coding real tokens in public repositories  
- Storing tokens in environment variables (`WEBEX_TOKEN`, `MAPBOX_TOKEN`)  
- Never printing tokens in logs  
- Rotating tokens regularly  
- Respecting API rate limits  
- Using HTTPS + timeouts for all requests  

### Security Justification
```
Tokens are sensitive credentials.  
To avoid leaking them, they must be stored outside the script and never pushed to GitHub.  
Using HTTPS, short timeouts, and simple retry logic helps keep the bot safe and reliable.
```

---

## 9. Python Source Code
The full Python script used in this investigation is located in `space_bot.py`.

---

## 10. Advantages of This Integration
```
- Demonstrates real-time API communication  
- Shows how to combine multiple external APIs  
- Builds a simple automated bot workflow  
- Uses JSON parsing, timestamps, error handling  
- Clean architecture following MVC principles  
```

---

## 11. Summary Table

| Section | Purpose | Outcome |
|--------|---------|----------|
| Webex API | Room messaging | Script can read & post messages |
| ISS API | Get current ISS data | Provides real-time coordinates |
| Geocoding | Translate coordinates | Provides readable locations |
| MVC | Structure | Cleaner, easier to maintain |
| Security | Protect credentials | Prevents leakage of sensitive data |

---

## 12. What I Learned
```
- How to call REST APIs from Python using the `requests` library  
- How to parse JSON and extract only necessary fields  
- How epoch timestamps work and how to convert them  
- How to design a small script using MVC principles  
- Why API keys and tokens must be protected and not hard-coded  
```

---

## 13. Screenshots
Screenshots of running the bot and message output are included in the repository.

---

## 14. References
```
https://developer.webex.com  
https://api.wheretheiss.at/v1/satellites  
https://docs.mapbox.com/api/search/  
https://requests.readthedocs.io  
```


