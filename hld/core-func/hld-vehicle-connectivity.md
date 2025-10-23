# HLD - Vehicle Connectivity

## Overview 
To enable AI-driven fleet operations and address project objectives we need to establish robust vehicle connectivity, data and command flow.

Vehicles use lightweight protocols (MQTT, HTTPS) to connect through a gateway layer that provides authentication, buffering, protocol translation and local decisioning. The gateway forwards normalized messages into a durable event backbone (Pub/Sub), which fans out to stream processing, storage and control paths. Device management (provisioning, registry, credentials, OTA, config, rules) sits alongside the gateway and exposes unified APIs for ops.

> Note: we adopt the [GCP reference architecture](https://cloud.google.com/architecture/connected-devices/iot-platform-product-architecture) as the baseline design. Where GCP services are implied, comments show how to replace them with vendor-agnostic equivalents (e.g., Kafka instead of Pub/Sub, managed MQTT broker instead of Cloud IoT Core).

## Major functional areas
| Area                  | Responsibilities                                                                                      |
|-------------------------|-----------------------------------------------------------------------------------------------------|
| Ingestion endpoints     | MQTT, HTTPS                                                                     |
| Gateway & LB            | Clustered broker, buffering, translation, scaling via external load balancer                         |
| Event backbone          | Pub/Sub / Kafka topics for telemetry and commands                                                   |
| Stream processing       | Real-time enrichment, windowing, deduplication                                                       |
| Device management       | Registry, credential store, config mgmt, OTA, rules engine                                          |
| Storage                 | Time-series, analytics, media archives                                                               |
| Commands                | Pub/Sub command topics, priority lanes, ACKs                                                         |
| Security & IAM          | TLS/mTLS, rotation, revocation, IAM least privilege                                                 |
| Observability           | Metrics, logs, alerting, SLOs for telemetry freshness and command latency                            |

## Commands and Data Flow
![Vehicle Connectivity](Vehicle%20Connectivity.png)

## Gateway & Load Balancing
- Clustered MQTT broker on K8s (e.g., EMQX / HiveMQ), fronted by TCP/SSL load balancer.  
- Horizontal autoscaling per region/vehicle class.  
- Local buffering for short outages.  
- Vendor-agnostic: GCP LB + GKE, AWS NLB + EKS, Azure LB + AKS.

## Device Identity & Management
- X.509 (mTLS), JWT or username/password depending on device class.  
- Automated provisioning & enrollment with revocation/rotation.  
- Registry for metadata & state, OTA service for firmware/config updates.  
- Rules engine or lightweight stream triggers for automation.

## Event & Command Flows
- **Telemetry:** Device → MQTT → Gateway → Event Backbone → Processing → Storage  
- **Command:** Backend → Command Topic → Gateway → Device (ACK required for critical ops)  
- Buffering & late data handling built in.

## Storage & Processing
- Hot: time-series DB (e.g., Bigtable).  
- Analytics: warehouse (e.g., BigQuery).  
- Media & firmware: object storage.  
- Stream processors for enrichment and geo-tagging.

## Security & Observability
- TLS/mTLS everywhere, per-device credentials, strict IAM.  
- Regional deployments for compliance.  
- Dashboards for connection count, message rate, latency, backlogs.  
- Alerts for gateway overload, command lag, OTA failures.

## What We Build
- MQTT gateway adapters (MQTT → backbone), OTA coordinator, provisioning service, rules engine (if not part of platform), dashboards & alerting.

## Key Recommendations
- Start with a clustered MQTT gateway + streaming backbone.  
- Automate provisioning and certificate lifecycle.  
- Define SLOs early for telemetry & commands.  
- Keep storage and processing regional for compliance.  
- Choose managed IoT platform vs. broker + custom mgmt based on required features.

## Availability Enhancements (Target: 99.9%)
Following measures proposed to avoid a single point of failure and ensure graceful degradation on outages, fast recovery, and controlled SLO breaches

| Layer / Area               | Availability Measures                                                                                      |
|----------------------------|------------------------------------------------------------------------------------------------------------|
| **Gateway & LB**           | Multi-zone clusters (N+1), external TCP/SSL LB with health checks, autoscaling, local buffering             |
| **Event Backbone**         | Pub/Sub / Kafka replication (≥3), message retention, idempotent replay                                      |
| **Processing**             | Parallel zone deployment, checkpointing, fast recovery                                                      |
| **Storage**                | Regional / multi-zone replication, automatic failover, low-latency cache                                   |
| **Device Connectivity**    | Graceful reconnect, retry with backoff, priority command lanes, ACK & retry                                 |
| **Operations**             | Health checks, autoscaling, runbooks, failover playbooks, monitoring of latency/backlog                     |
| **Disaster Recovery**      | RPO ≤ 5 min, RTO ≤ 15 min, secondary region backups, automated failover                                    |
| **Observability & SLOs**   | Dashboards for uptime/backlog/latency, alerting on SLO breaches, error rate monitoring                      |
