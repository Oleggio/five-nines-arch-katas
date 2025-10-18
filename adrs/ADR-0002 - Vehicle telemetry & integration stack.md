# ADR-0002 — Use of MQTT Gateway + Pub/Sub for Vehicle Connectivity

## Context
Vehicles need a reliable, secure, low-latency channel for telemetry and command exchange. The platform must support thousands to tens of thousands of devices, tolerate intermittent connectivity, and scale horizontally.

## Decision
Adopt a clustered MQTT gateway (on Kubernetes) fronted by a load balancer for device connectivity, and use Pub/Sub as the streaming backbone.

## Key Differentiators
- MQTT is lightweight and optimized for constrained IoT devices.  
- Pub/Sub offers managed horizontal scale, durable messaging, and native integration with Dataflow for streaming.  
- Clear separation between connectivity layer and data processing.

## Consideration_1: Availability & Reliability
Clustering MQTT brokers and using Pub/Sub provides buffering, failover, and replay without custom broker logic. Enables 99.9% availability targets.

## Alternatives Considered
- **Direct HTTPS ingestion:** simpler but less efficient for low-power devices, no QoS.  
- **Standalone MQTT broker:** more control but higher operational complexity and limited streaming integration.

## Conclusion
MQTT + Pub/Sub provides the **best balance of efficiency, scalability, and operational simplicity** for the expected fleet size and SLA.
