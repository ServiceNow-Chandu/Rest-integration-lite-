# REST Integration Lite – ServiceNow

A scoped ServiceNow application demonstrating inbound and outbound
REST integrations using Scripted REST APIs and REST Messages.

This project simulates real-world integration scenarios including
API validation, JSON parsing, outbound event triggers, and error handling.

---

## Project Objective
To design and implement secure and reliable REST integrations between
ServiceNow and external systems.

---

## Features Implemented

### Inbound REST API
- Scripted REST API endpoint
- POST method to create incidents
- JSON payload validation
- Structured success and error responses

### Outbound REST API
- REST Message configuration
- Automatic trigger on high-priority incidents
- JSON request body creation
- Logging of external responses

### Error Handling
- Try/catch blocks
- HTTP status validation
- Response body logging

---

## Technologies Used
- Scripted REST APIs
- RESTMessageV2
- Business Rules
- JSON payload handling
- GitHub source control

---

## Demo Scenarios
1. Send POST request → Incident created
2. Create Priority 1 Incident → External API call triggered
3. Verify logs for response handling

---

## Notes
- Uses public test API for outbound calls
- Designed with clean separation between inbound and outbound logic
- Built entirely in a scoped application
