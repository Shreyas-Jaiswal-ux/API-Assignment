# 🛰️ Space Bot — API Investigation

### Module: Web Technology (5FTC2167)  
### Assignment 1 – Space Bot  
### Author: Shreyas Jaiswal  

---

## 1️⃣ Webex Messaging API (7%)

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
