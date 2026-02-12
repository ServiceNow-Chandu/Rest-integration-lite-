# Inbound REST API

Endpoint:
POST /api/x_<scope>_rest_integration_lite/incident_api/create

Purpose:
Allows external systems to create incidents in ServiceNow.

Key Features:
- Validates required fields
- Returns HTTP 400 for bad requests
- Returns HTTP 201 for successful creation
- Handles unexpected errors with HTTP 500

Design Considerations:
- Input validation before database insert
- Controlled field mapping
- Secure execution context
