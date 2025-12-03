```markdown
# 🚀 Space Bot – API Investigation & System Documentation  
**Author:** Shreyas Jaiswal  
**Module:** Web Technology (5FTC2167)  
**Assignment 1 – Space Bot (50%)**

---

# 1. Introduction

This project implements a Python-based **Space Bot** that integrates multiple REST APIs to monitor a Webex messaging room. When a user sends a command such as:

```
/5
```

…the bot waits 5 seconds, fetches the **current ISS latitude and longitude**, reverse-geocodes the location using Mapbox, converts the timestamp from epoch into readable time, and posts the formatted message back into the Webex room.

This README documents all APIs used, explains web architecture, and describes how MVC applies to this project.

---

# 2. Webex Messaging API

### Base URL
```bash
https://webexapis.com/v1
```

### Endpoints Used
| Purpose | Method | Endpoint |
|--------|--------|----------|
| List rooms | GET | `/rooms` |
| Get latest message | GET | `/messages?roomId=<id>&max=1` |
| Post a message | POST | `/messages` |

### Example JSON – GET /rooms
```json
{
  "items": [
    {
      "id": "Y2lzY29zcGFyazovL3VzL1JPT00v123",
      "title": "ISS Room",
      "type": "group"
    }
  ]
}
```

### Required Headers
```bash
Authorization: Bearer <token>
Content-Type: application/json
```

---

# 3. ISS Current Location API

### Endpoint
```bash
https://api.wheretheiss.at/v1/satellites/25544
```

### Example Response
```json
{
  "latitude": 7.0680,
  "longitude": -61.2094,
  "timestamp": 1677172913
}
```

Returns:
- ISS latitude  
- ISS longitude  
- Epoch timestamp  

---

# 4. Mapbox Reverse Geocoding API

### Endpoint Format
```bash
https://api.mapbox.com/geocoding/v5/mapbox.places/<lon>,<lat>.json?access_token=<TOKEN>
```

### Example Response
```json
{
  "features": [
    { "text": "Bolívar", "place_type": ["region"] },
    { "text": "Venezuela", "place_type": ["country"] }
  ]
}
```

### Fallback Ocean Classifier (Bot Logic)
```python
if -70 <= lon <= 50:
    return "the Atlantic Ocean"
```

---

# 5. Epoch → Human Time Conversion

### Conversion Code
```python
from datetime import datetime
human_time = datetime.fromtimestamp(epoch).strftime("%a %b %d %H:%M:%S %Y")
```

### Example Output
```bash
Wed Feb 23 17:21:53 2022
```

---

# 6. Web Architecture Overview

```text
+------------------+        HTTPS         +------------------+
|   Python Bot     | <------------------> |  Webex Cloud     |
|  (Client App)    |       JSON           | Messaging API     |
+--------+---------+                       +--------+---------+
         |                                            |
         | REST request → ISS API                     | REST request → Mapbox API
         v                                            v
+--------+---------+                       +---------+--------+
|  ISS API (GPS)   |                       | Mapbox Geocoding |
+------------------+                       +------------------+
```

### Key Concepts
```text
• Client–Server Model
• Stateless REST operations
• JSON for data exchange
• Polling Webex every second
```

---

# 7. MVC Design Pattern (Applied to Space Bot)

### Model
```text
• ISS API data
• Mapbox geocoding data
• Epoch → human-time conversion
• Ocean classifier
```

### View
```text
The formatted Webex message showing:
- Human time
- Location (city/region/country or ocean)
- ISS coordinates
```

### Controller
```text
• Poll Webex room messages
• Detect /N commands
• Trigger ISS + Mapbox API calls
• Apply delays
• Send final output back to Webex
• Handle errors (network, parsing, etc.)
```

---

# 8. Security & Token Management

```text
• API tokens must never be committed to public repositories.
• HTTPS ensures encrypted communication with all APIs.
• Tokens remain only in memory during execution.
• Error-handling blocks prevent token leakage.
• Rate limits and unexpected failures are safely handled.
```

---

# 9. Python Techniques Used

```text
• requests – making REST API calls
• json() – parsing returned JSON data
• datetime – converting epoch timestamps
• while loops – continuous room monitoring
• try/except – robust error handling
• custom functions – modular, reusable structure
• string formatting – clean final output messages
```

---

# 10. Advantages of This Integration

```text
• Demonstrates real-time API orchestration
• Combines multiple data sources into a single workflow
• Uses clean MVC-like structure inside Python
• Easily extendable (e.g., SpaceX launch API)
• Functional, modular, scalable design
```

---

# 11. Summary Table of APIs

| API | Purpose | Example Output |
|------|---------|----------------|
| Webex Rooms API | List rooms | Title, roomId |
| Webex Messages API | Read + send messages | JSON text |
| ISS API | ISS GPS + timestamp | lat, lon, epoch |
| Mapbox API | Convert GPS → location | City/region/country |
| Python datetime | Epoch → human time | Thu Mar 21 14:22:11 |

---

# 12. What I Learned

```text
• How REST APIs communicate
• How to authenticate with Bearer tokens
• Using Webex developer APIs in Python
• Reverse geocoding + GPS coordinate understanding
• JSON parsing and data transformation
• MVC thinking applied to automation scripts
• Handling network errors and API limits
• Building modular and scalable Python programs
```

---

# 13. Screenshots

*(Add your screenshots here in the actual README — Webex output, terminal logs, etc.)*

---

# 14. References

```text
Webex Developer Docs – https://developer.webex.com
Mapbox Geocoding API – https://docs.mapbox.com/api/search/geocoding/
ISS API – https://wheretheiss.at/w/developer
Python datetime – https://docs.python.org/3/library/datetime.html
Python requests – https://requests.readthedocs.io
```
```
