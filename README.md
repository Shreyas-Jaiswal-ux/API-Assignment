# Space Bot — API Investigation  
Module: Web Technology (5FTC2167)  
Assignment 1 – Space Bot  
Author: Shreyas Jaiswal  

---

## 1. Introduction  
This project demonstrates how multiple web services can be integrated into one Python-based automation tool.  
The Space Bot listens to a Webex messaging room, waits for commands such as `/5`, retrieves the live location of the International Space Station (ISS), reverse-geocodes the coordinates into a readable place, and posts the final message back into the Webex room.  
This provides practical experience with REST APIs, JSON handling, API authentication, and simple bot automation.

---

## 2. Webex Messaging API  
The Webex Messaging API allows a script or application to interact with Webex rooms.  
This bot uses the following features:

• List all rooms the user or bot is part of  
• Read the latest incoming message in a selected room  
• Post a reply message containing ISS data  

### Key Endpoints Used

| Purpose | Method | Endpoint | Description |
|--------|--------|----------|-------------|
| List Rooms | GET | https://webexapis.com/v1/rooms | Retrieves all Webex rooms available to the user |
| Get Latest Message | GET | https://webexapis.com/v1/messages | Reads recent messages in a specific room |
| Post Message | POST | https://webexapis.com/v1/messages | Sends a formatted message into a Webex room |

### Authentication  
All Webex requests require this header:

```
Authorization: Bearer <ACCESS_TOKEN>
Content-Type: application/json
```

---

## 3. ISS Current Location API  
The ISS API returns the real-time latitude, longitude, and timestamp of the International Space Station.

Endpoint:  
https://api.wheretheiss.at/v1/satellites/25544

### Example Response
```json
{
  "name": "iss",
  "id": 25544,
  "latitude": -51.5668,
  "longitude": 22.2114,
  "timestamp": 1730893245
}
```

The bot extracts the following fields:  
• latitude  
• longitude  
• timestamp (UNIX epoch, later converted)

---

## 4. Geocoding API (Mapbox)  
Mapbox converts raw GPS coordinates into readable human locations such as a city, region, or country.

Endpoint:  
https://api.mapbox.com/geocoding/v5/mapbox.places/{lon},{lat}.json?access_token=YOUR_TOKEN

Typical returned values include:  
• place_name (full readable location string)  
• context fields (country, region, city)  

If no location can be identified, the script returns "Unknown location".

---

## 5. Example Output  
After collecting ISS coordinates and converting the timestamp and location, a final message looks like:

```
On Sat Nov 08 09:40:45 2025, the ISS was flying over Brazil (-51.5668°, 22.2114°)
```

If the location is not detected:

```
Unknown location
```

---

## 6. Web Architecture (Full System Explanation)  
The Space Bot follows a simple event-driven architecture where it continuously monitors a Webex room and reacts to user input.

### Components  
• Webex client (where the user types commands)  
• Python bot script (main logic controller)  
• External APIs (Webex REST API, ISS Location API, Mapbox Geocoding API)

### End-to-end Process  
1. The bot authenticates to Webex using a token.  
2. The bot lists all available rooms.  
3. The user selects a room to monitor.  
4. The bot polls the room for new messages.  
5. When a command such as `/5` appears, the bot waits 5 seconds.  
6. The bot fetches the ISS position using the ISS API.  
7. The bot reverse-geocodes the coordinates using Mapbox.  
8. The bot formats the final message.  
9. The bot posts the message into the Webex room.

### Data Handling  
• JSON is used for all incoming and outgoing API data.  
• HTTPS is used for all network communication.  
• Epoch timestamps are converted into human-readable strings using Python.

---

## 7. MVC Pattern (Applied to This Script)  
Although this is a small console program, its structure aligns with the Model-View-Controller pattern.

### Model  
Handles data retrieval and preparation:  
• ISS API (returns lat, lon, epoch)  
• Mapbox API (returns place_name)  
• Webex API message and room data  

### View  
Handles the formatting of human-readable output:  
• Constructs the text message that will be posted into the Webex room  
• Templates the final ISS message  

### Controller  
Controls program flow:  
• Authenticates the user  
• Selects the room  
• Detects `/seconds` commands  
• Calls Model functions (ISS + geocoding)  
• Sends the result using View formatting  
• Posts the final response to Webex  

This separation keeps data logic, formatting, and flow control organised and easier to extend.

---

## 8. Security and Token Management  
To ensure safe handling of tokens and API keys:

• Sensitive tokens should not be hard-coded or committed to GitHub.  
• Environment variables should be used (e.g., `WEBEX_TOKEN`, `MAPBOX_TOKEN`).  
• Tokens must never be printed in logs or error messages.  
• Always use HTTPS endpoints and include timeouts in API calls.  
• Rate limits should be respected, and backoff should be used if needed.  
• If a token is exposed accidentally, it must be rotated immediately.

### Security Justification  
For safety, the bot loads tokens securely and avoids printing or storing them in logs. All external requests use HTTPS with timeouts, and errors are handled without exposing any credentials. This prevents accidental credential leakage and keeps the bot compliant with good API security practices.

---

## 9. Python Source Code  
(Place the full Python script here exactly as used in the project.)

---

## 10. Advantages of This Integration  
• Demonstrates use of multiple REST APIs in one script  
• Shows how JSON parsing and time conversion works  
• Demonstrates a practical event-driven automation flow  
• Code is structured using ideas from MVC  
• Easy to extend with additional features in the future  

---

## 11. Summary Table  

| Section | Function | Outcome |
|--------|----------|---------|
| Webex Messaging API | Communication | Read and send messages |
| ISS API | Position tracking | Real-time ISS latitude/longitude |
| Geocoding API | Location conversion | Converts coordinates into human-readable places |
| MVC Pattern | Structure | Organises data, logic, and presentation |
| Automation | Bot behaviour | Responds to user commands in real time |

---

## 12. What I Learned  
• How to call REST APIs from Python using the `requests` library.  
• How to read JSON and extract only the fields required.  
• How UNIX epoch timestamps work and how to convert them to normal date-time format.  
• How to design a small script using ideas from MVC by separating data, logic, and formatting.  
• Why API keys and tokens must be treated as secrets and must not be hard-coded.

---

## 13. Screenshots  
(Insert screenshots of terminal output and Webex bot responses here.)

Example placeholders:

```
![Bot Running](images/terminal.png)
![Webex Response](images/response.png)
```

---

## 14. References  
• Webex Developer API Documentation  
• WhereTheISS.at API  
• Mapbox Geocoding API  
• Python Requests Library  

