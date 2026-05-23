# Phase 0 – Foundation

## Purpose

Establish a stable, predictable, and well-understood base environment across all available hardware.

This phase focuses on clarity and consistency rather than tooling. No automation or services are introduced at this stage.

---

## Goals

- Define and document all hardware
- Establish consistent naming
- Ensure reliable SSH access to all nodes
- Ensure systems are reachable and predictable on the network
- Create a clear platform topology that matches reality

---

## Platform Topology

### Control Node

- Hostname: control-node
- Device: Lenovo L580
- Specifications: 16GB RAM, 256GB SSD
- Architecture: x86_64
- Role: Platform control, management, and core services

---

### Worker Nodes

- pi-node-1 (Raspberry Pi 4)
- pi-node-2 (Raspberry Pi 4)
- pi-node-3 (Raspberry Pi 4)

- Architecture: ARM
- Role: Primary workload execution (services, containers, experiments)

---

### Legacy Nodes

- pi-legacy-a (Raspberry Pi 1 Model A)
- pi-legacy-b (Raspberry Pi 1 Model B+)

- Architecture: ARM (very low capability)
- Role:
  - Constraint testing
  - Lightweight services
  - Failure simulation
  - Edge scenarios

Legacy nodes are not part of the primary workload pool and must be explicitly targeted for use.

---

## Hardware Characteristics

The platform contains mixed hardware with significant differences in capacity and architecture.

### Summary

- control-node: High capacity, x86_64
- worker nodes: Moderate capacity, ARM (Pi 4)
- legacy nodes: Extremely constrained, ARM (Pi 1)

### Implications

- Not all workloads can run on all nodes
- Some software will not support older ARM architectures
- Resource constraints vary significantly
- Workload placement decisions will be required in later phases

---

## Naming Convention

All nodes use a consistent and descriptive naming scheme:

control-node
pi-node-1
pi-node-2
pi-node-3
pi-legacy-a
pi-legacy-b

Naming is:

- stable
- descriptive
- consistent across all documentation and access methods

---

## Access

### SSH

All nodes are accessible from the development machine via SSH.

Requirements:

- SSH key-based authentication is configured
- Password authentication is not required for normal access
- Consistent user account across nodes (e.g. pi)

Example:

ssh control-node
ssh pi-node-1
ssh pi-legacy-a

---

## Network

### Requirements

- All nodes have stable and predictable identity
- Nodes are accessible by hostname or reserved IP

Approach:

- DHCP reservations preferred
- Alternatively, local hostname resolution may be used

### Outcome

- No need to manually track IP addresses
- Nodes are consistently reachable

---

## System State

Each node should:

- Be updated to latest available packages
- Use a consistent base operating system where possible
- Have consistent user and access configuration

---

## Validation

Phase 0 is considered complete when the following commands succeed reliably from the development machine:

ssh control-node uptime
ssh pi-node-1 uptime
ssh pi-node-2 uptime
ssh pi-node-3 uptime
ssh pi-legacy-a uptime
ssh pi-legacy-b uptime

---

## Scope Boundary

Phase 0 explicitly does not include:

- Infrastructure as Code (Ansible)
- Docker or container runtime
- Kubernetes or orchestration
- Monitoring systems
- CI/CD pipelines
- Service deployment

These are introduced in later phases.

---

## Outcome

At the end of Phase 0:

- All hardware is clearly defined and understood
- All nodes are accessible and predictable
- The platform topology is documented accurately
- There is a stable foundation for future automation

---

## Notes

This phase prioritises:

- simplicity
- clarity
- reproducibility

The goal is not to build a system yet, but to ensure that the system can be understood and controlled before further complexity is introduced.

