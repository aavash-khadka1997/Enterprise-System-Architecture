# DataHub Overall Architecture and CRM Integration

## Architectural Diagram
*(See attached `datahub_architecture.jpg` for the visual flow of the integration)*

![DataHub Architecture](datahub_architecture.jpg)

## System Overview
The DataHub enterprise architecture bridges core cloud computing infrastructure with client-facing Customer Relationship Management (CRM) and billing systems. This creates an automated, zero-touch provisioning environment where business logic dictates cloud resource allocation.

## Core Cloud Components & Objects
*   **API Gateway / Load Balancer:** Acts as the single entry point for all CRM requests to the backend server clusters, distributing traffic and enforcing rate limits.
*   **Compute Nodes (Hypervisors):** The primary server clusters responsible for spinning up virtualized environments based on CRM parameter requests.
*   **Object & Block Storage:** Scalable storage arrays allocated dynamically to client instances.
*   **Telemetry & Log Aggregator:** Collects system health and usage metrics (CPU, RAM, bandwidth) from the compute nodes to feed back into the CRM for client dashboard visibility.

## Parameter Systems & CRM Workflow
The CRM dictates the physical allocation of cloud resources through a strict parameter passing system. 

1.  **Resource Allocation Parameters:** When a client is onboarded via the CRM, the system generates a JSON payload containing specific constraints (e.g., `max_vcpu: 16`, `max_ram_gb: 64`). The API Gateway validates these parameters before the compute nodes provision the environment.
2.  **Billing Metrics System:** The CRM syncs the client's `Service_Tier` parameter with the automated billing engine. If the Telemetry node reports that a client has exceeded their bandwidth parameter, the billing system is automatically triggered to issue an overage invoice.
3.  **State Synchronization:** The DataHub compute nodes operate on a polling mechanism. If a server cluster goes offline for maintenance, the status parameter changes to `MAINT_MODE`. This state is pushed back to the CRM, which automatically updates the client-facing status page and pauses billing timers.

## Security Controls
All internal communication between the CRM, parameter validation engines, and the cloud objects is conducted over a private Virtual Private Cloud (VPC) subnet utilizing mTLS (Mutual TLS) authentication to ensure data integrity and prevent unauthorized provisioning.
