# ADR-0002 — Use of MQTT Gateway + Pub/Sub for Vehicle Connectivity

## Context
Vehicles need a reliable, secure, low-latency channel for telemetry and command exchange. The platform must support thousands to tens of thousands of devices, tolerate intermittent connectivity, and scale horizontally.

## Decision
Adopt a clustered MQTT gateway (on Kubernetes) fronted by a load balancer for device connectivity and use Pub/Sub as the streaming backbone.

## Key Differentiators
- MQTT is lightweight and optimized for constrained IoT devices.  
- Pub/Sub offers managed horizontal scale, durable messaging, and native integration with Dataflow for streaming.  
- Clear separation between the connectivity layer and data processing.

## Consideration_1: Availability & Reliability
Clustering MQTT brokers and using Pub/Sub provides buffering, failover, and replay without custom broker logic. Enables 99.9% availability targets.

## Alternatives Considered
- **Direct HTTPS ingestion:** simpler but less efficient for low-power devices, no QoS.  
- **Standalone MQTT broker:** more control but higher operational complexity and limited streaming integration.

## Risks & Trade-offs
| Risk Area | Description | Mitigation |
|--|--|--|
| Broker complexity                | Managing a clustered MQTT gateway requires operational maturity and observability.                       | Use managed Kubernetes patterns, health probes, autoscaling, and robust monitoring/alerting.          |
| Load balancing & scaling         | High connection churn or uneven traffic may overload certain brokers.                                   | Apply TCP/SSL load balancing with sticky sessions and horizontal autoscaling.                        |
| Message delivery guarantees      | MQTT QoS handling and Pub/Sub acknowledgment semantics must be carefully aligned.                       | Enforce QoS 1 or 2 where needed, ensure idempotent message handling, and define retry strategies.     |
| Offline tolerance & buffering    | Prolonged connectivity gaps may cause telemetry backlog or command delivery delays.                      | Implement local buffering on devices and gateways; enforce retention policies and replay strategies.  |
| Vendor lock-in                   | Tying streaming to Pub/Sub may limit portability to other clouds.                                       | Abstract message routing layer; document migration paths (e.g., Kafka).                              |
| Security & auth                  | Per-device credentials and mTLS introduce operational overhead and potential configuration errors.       | Automate provisioning, rotation, and validation; use a centralized credential store.                 |
| Cost visibility                  | High Pub/Sub message volumes can create unpredictable costs.                                            | Implement cost alerts, batching, compression, and topic-level monitoring.                             |
| Latency & QoS conflicts          | Mixing low- and high-priority traffic on the same topics may affect delivery latency.                    | Separate topics by priority, enforce routing policies, and monitor latency SLOs.                      |
| Skills gap                       | MQTT and Pub/Sub integration may be less familiar to some teams.                                        | Provide documentation, reference implementations, and internal training.                             |

## Conclusion
MQTT + Pub/Sub provides the **best balance of efficiency, scalability, and operational simplicity** for the expected fleet size and SLA.
