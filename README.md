# REST Integration Lite – ServiceNow

## Project Overview

REST Integration Lite is a lightweight integration-focused ServiceNow project developed to demonstrate inbound and outbound REST API communication using Scripted REST APIs, REST Messages, Business Rules, and JSON payload processing.

The project simulates real-world enterprise integration patterns where ServiceNow both receives data from external systems and sends incident data to third-party platforms. The implementation demonstrates API design, request validation, outbound integrations, logging, and event-driven automation.

This project was developed using a scoped application integrated with GitHub source control to simulate enterprise API integration workflows.

---

# Business Requirement

The organization required a lightweight integration framework capable of:

* Receiving incident data from external systems
* Automatically creating incidents through REST APIs
* Sending high-priority incidents to external platforms
* Processing JSON payloads securely
* Implementing event-driven outbound integrations
* Logging integration activity and failures

---

# Technologies Used

| Technology              | Purpose                     |
| ----------------------- | --------------------------- |
| Scripted REST APIs      | Inbound REST integration    |
| REST Messages           | Outbound REST communication |
| Business Rules          | Event-driven automation     |
| JSON Payload Processing | API request handling        |
| GlideRecord             | Incident creation           |
| REST API Explorer       | API testing                 |
| GitHub Source Control   | Version management          |
| Scoped Applications     | Modular development         |

---

# Application Details

| Item              | Value                      |
| ----------------- | -------------------------- |
| Application Name  | REST Integration Lite      |
| Scope             | x_ns_rest_integration_lite |
| Version           | 1.0.0                      |
| Development Style | Scoped Application         |
| Source Control    | GitHub Integrated          |

---

# Project Architecture

The project demonstrates both inbound and outbound API communication patterns.

---

# Integration Flow

```text id="m82wsh"
External System
        ↓
Inbound REST API
        ↓
Scripted REST Resource
        ↓
Incident Creation
        ↓
High Priority Incident
        ↓
Business Rule Trigger
        ↓
Outbound REST Message
        ↓
External Endpoint
```

---

# Core Components Implemented

# 1. Inbound REST API

A Scripted REST API named `Incident API` was developed to allow external systems to create incidents in ServiceNow.

---

# API Details

| Property      | Value        |
| ------------- | ------------ |
| API Name      | Incident API |
| API ID        | incident_api |
| Resource Path | /create      |
| HTTP Method   | POST         |

---

# API Endpoint

```text id="h8dg9n"
POST /api/x_ns_rest_integration_lite/incident_api/create
```

---

# Functionalities

| Feature           | Behavior                      |
| ----------------- | ----------------------------- |
| JSON Validation   | Validates mandatory fields    |
| Incident Creation | Creates Incident dynamically  |
| Error Handling    | Returns API errors gracefully |
| HTTP Status Codes | Returns proper response codes |

---

# Scripted REST API Resource Script

```javascript id="6k9p1s"
(function process(
    /*RESTAPIRequest*/ request,
    /*RESTAPIResponse*/ response
) {

    try {

        var body = request.body.data;

        if (!body.short_description) {

            response.setStatus(400);

            response.setBody({
                error:
                'short_description is required'
            });

            return;
        }

        var inc = new GlideRecord('incident');

        inc.initialize();

        inc.short_description =
            body.short_description;

        inc.description =
            body.description || '';

        inc.impact =
            body.impact || 3;

        inc.urgency =
            body.urgency || 3;

        inc.caller_id =
            gs.getUserID();

        var incSysId = inc.insert();

        response.setStatus(201);

        response.setBody({
            message: 'Incident created',
            number: inc.number,
            sys_id: incSysId
        });

    } catch (ex) {

        response.setStatus(500);

        response.setBody({
            error: ex.message
        });
    }

})(request, response);
```

---

# Sample JSON Payload

```json id="p0hzzt"
{
  "short_description": "Email service down",
  "description": "Users cannot access email",
  "impact": 1,
  "urgency": 1
}
```

---

# API Response Example

```json id="2f4jzc"
{
  "message": "Incident created",
  "number": "INC0010001",
  "sys_id": "xxxxxxxxxxxxxxxx"
}
```

---

# 2. Outbound REST Integration

Implemented outbound REST communication using REST Messages and Business Rules.

---

# REST Message Configuration

| Property | Value                              |
| -------- | ---------------------------------- |
| Name     | Send Incident Out                  |
| Method   | POST                               |
| Endpoint | jsonplaceholder.typicode.com/posts |

---

# Business Rule – Outbound Incident Trigger

A Business Rule was implemented to automatically send high-priority incidents to external systems.

---

# Trigger Logic

| Condition    | Action                     |
| ------------ | -------------------------- |
| Priority = 1 | Send outbound REST payload |

---

# Business Rule Script

```javascript id="8x9ylw"
(function executeRule(current, previous) {

    var r = new sn_ws.RESTMessageV2(
        'Send Incident Out',
        'post'
    );

    var payload = {

        incident: current.number,

        description:
            current.short_description,

        priority: current.priority
    };

    r.setRequestBody(
        JSON.stringify(payload)
    );

    r.setRequestHeader(
        'Content-Type',
        'application/json'
    );

    var response = r.execute();

    var status =
        response.getStatusCode();

    var body =
        response.getBody();

    gs.info(
        'Outbound incident sent: ' +
        status +
        ' | ' +
        body
    );

})();
```

---

# Outbound Payload Example

```json id="lmn5hp"
{
  "incident": "INC0010002",
  "description": "Production outage",
  "priority": 1
}
```

---

# Error Handling & Logging

Implemented integration logging and API validation to improve operational reliability.

---

# Error Handling Features

| Feature           | Purpose                 |
| ----------------- | ----------------------- |
| Try/Catch Blocks  | Prevent API failures    |
| HTTP Status Codes | Proper API responses    |
| gs.info Logging   | Integration monitoring  |
| Validation Checks | Secure payload handling |

---

# Testing Scenarios

# Scenario 1 – Create Incident via API

### Test

Send POST request with valid JSON payload.

### Expected Result

* Incident created successfully
* HTTP 201 returned

---

# Scenario 2 – Missing Required Field

### Test

Send payload without short_description.

### Expected Result

* HTTP 400 returned
* Validation error displayed

---

# Scenario 3 – Outbound REST Trigger

### Test

Create Priority 1 Incident.

### Expected Result

* Outbound REST call executed
* Payload sent externally
* Logs generated successfully

---

# End-to-End Workflow

```text id="vbfljl"
External System Sends JSON
        ↓
Inbound REST API Receives Request
        ↓
Request Validation Executes
        ↓
Incident Created in ServiceNow
        ↓
Priority 1 Incident Detected
        ↓
Business Rule Triggers
        ↓
Outbound REST Message Sent
        ↓
Integration Logged Successfully
```

---

# Enterprise Concepts Demonstrated

| Concept                   | Implemented |
| ------------------------- | ----------- |
| Inbound REST APIs         | Yes         |
| Outbound REST Integration | Yes         |
| Scripted REST APIs        | Yes         |
| REST Messages             | Yes         |
| JSON Processing           | Yes         |
| Event-driven Automation   | Yes         |
| Error Handling            | Yes         |
| Logging & Monitoring      | Yes         |
| API Validation            | Yes         |

---

# Technical Challenges Solved

## Challenge 1

Handling external API requests securely.

### Solution

Implemented request validation and HTTP status handling.

---

## Challenge 2

Automating outbound integration triggers.

### Solution

Used Business Rules to initiate REST communication dynamically.

---

## Challenge 3

Preventing API failures from breaking workflows.

### Solution

Implemented try/catch error handling.

---

## Challenge 4

Maintaining integration visibility.

### Solution

Added logging and API response tracking.

---

# GitHub Repository Structure

```text id="g41m2m"
08-rest-integration-lite/
│
├── README.md
│
├── docs/
│   ├── architecture.md
│   ├── inbound-api.md
│   ├── outbound-api.md
│   ├── payload-examples.md
│   ├── error-handling.md
│   └── testing.md
│
├── sample-data/
│   └── incident_payload.json
│
├── media/
│
└── update_sets/
```

---

# Sample Payload File

```json id="rnww3q"
{
  "short_description": "VPN outage",
  "description": "Users unable to connect",
  "impact": 1,
  "urgency": 1
}
```

---

# Real-World Enterprise Benefits

| Benefit                    | Impact                            |
| -------------------------- | --------------------------------- |
| Automated Incident Intake  | Reduced manual entry              |
| System Integration         | Improved operational connectivity |
| Event-driven Notifications | Faster escalation                 |
| API-based Automation       | Increased efficiency              |
| Integration Monitoring     | Improved troubleshooting          |

---

# Project Outcome

The REST Integration Lite implementation successfully demonstrated inbound and outbound API integration patterns in ServiceNow using Scripted REST APIs, REST Messages, JSON processing, and automated outbound triggers.

The project improved:

* Integration automation
* API handling capability
* Incident intake efficiency
* External system connectivity
* Operational monitoring
