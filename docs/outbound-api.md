# Outbound REST API

Trigger:
After Insert Business Rule on Incident
Condition: Priority = 1

Purpose:
Notify external system when high-priority incident is created.

Implementation:
- RESTMessageV2
- JSON payload construction
- Response status logging
