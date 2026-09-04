# Phase 0 – Foundation

## Purpose

Establish a stable, predictable, and well-understood base environment across the available hardware.

This phase focuses on clarity and consistency. Automation is introduced only where it directly supports that consistency, and no services beyond what is needed to manage the machines are deployed at this stage.

---

## Goals

- Define and document the current hardware
- Establish consistent naming
- Ensure reliable SSH access to every host
- Ensure systems are reachable and predictable on the network
- Introduce Ansible as the single management path, with a clear bootstrap-to-managed handoff
- Create a platform topology that matches reality

---

## Platform Topology

The platform has been reduced to the hardware that is actually in use. Devices that are switched off or retired are not listed here and are not managed.

### Monitoring Node

- Hostname: `prometheus`
- Device: Raspberry Pi 4 Model B (2 GB)
- Architecture: ARM (aarch64)
- Role: Dedicated monitoring host. Runs Prometheus and its exporters, probing the rest of the platform.
- Connectivity: Ethernet, direct to a router LAN port. Not behind the office powerline segment, so it retains network access when that segment fails. Powered from the modem UPS so it also survives mains interruptions.

### Server

- Hostname: `hermes`
- Device: x86_64 server
- Architecture: x86_64
- Role: Hosts the Hermes agent (in Docker) and other containerised services. Sits in the office behind the powerline segment.
- Connectivity: Ethernet via the office switch.
- `hermes-server` is the Tailscale hostname.

### Workstation

- Hostname: `localhost` (the development machine)
- Device: laptop
- Role: Control node for Ansible. The machine from which playbooks are run.

---

## Hardware Characteristics

The platform now contains a small number of heterogeneous hosts.

### Summary

- `prometheus`: constrained, ARM (Pi 4, 2 GB)
- `hermes`: higher capacity, x86_64
- `localhost`: the laptop, used as the control node

### Implications

- Not all workloads can run on all hosts.
- The 2 GB Pi 4 cannot comfortably run both Prometheus and Grafana together. The monitoring design splits these: Prometheus runs on the Pi; Grafana runs elsewhere (on `hermes`).
- The office hosts (`hermes`, and any ethernet-attached laptop or Pi) share a single failure point: the powerline segment to the router. `prometheus` is deliberately placed on the router side so it can observe that segment rather than share its failure.

---

## Naming Convention

Hosts use a descriptive name rather than a positional one:

- `prometheus` — the monitoring node
- `hermes` — the server
- `localhost` — the control node

Naming is stable, descriptive, and consistent across documentation and access methods.

---

## Access

### SSH

All hosts are reachable from the development machine over SSH.

There are two distinct access stages:

1. **Bootstrap stage** — the initial connection to a freshly installed machine uses the local `keith` account and the personal key. This is configured with `ansible-bootstrap.cfg` (`remote_user = keith`, `private_key_file = ~/.ssh/id_rsa`) and requires `--ask-become-pass`.
2. **Managed stage** — once the `ansible` user has been created with passwordless sudo and an authorised key, day-to-day management uses `ansible.cfg` (`remote_user = ansible`, `private_key_file = ~/.ssh/id_ansible`).

Example:

```
ssh ansible@prometheus
ssh ansible@hermes
```

---

## Ansible Management

Playbooks live in `ansible/`. Two configuration files select the access stage:

- `ansible-bootstrap.cfg` — for the initial bootstrap of a fresh host.
- `ansible.cfg` — for normal management once the `ansible` user exists.

The inventory defines three groups:

- `[monitor]` — `prometheus`
- `[workstation]` — `localhost`
- `[server]` — `prometheus` and `hermes`

### Bootstrap

`bootstrap.yaml` runs against all hosts and does the following:

1. Upgrades the base system.
2. Creates the `ansible` user.
3. Adds the Ansible SSH key to that user.
4. Installs passwordless sudo for the `ansible` user.

Run it against a freshly installed machine:

```
ANSIBLE_CONFIG=ansible-bootstrap.cfg ansible-playbook --ask-become-pass bootstrap.yaml
```

### Managed state

`packages.yaml` is the day-to-day playbook: it upgrades packages and applies the roles to the appropriate groups.

---

## Network

### Requirements

- All hosts have stable and predictable identity.
- Hosts are reachable by hostname or reserved IP.

### Approach

- DHCP reservations are preferred.
- Alternatively, local hostname resolution may be used.

Per-host network details (management IP, service IP, Tailscale hostname) live in `host_vars/` so they are kept out of the playbooks themselves.

---

## System State

Each host should:

- Be updated to the latest available packages.
- Use a consistent base operating system where possible (Debian).
- Have a consistent `ansible` user and access configuration.

---

## Validation

Phase 0 is considered complete when the following succeed reliably from the development machine:

```
ssh ansible@prometheus uptime
ssh ansible@hermes uptime
ANSIBLE_CONFIG=ansible.cfg ansible-playbook --check packages.yaml
```

---

## Scope Boundary

Phase 0 explicitly does not include:

- Service deployment (Prometheus, Grafana, exporters)
- Monitoring configuration
- Container orchestration
- CI/CD pipelines

These are introduced in later phases. The monitoring design that builds on this foundation is documented separately in the project notes.

---

## Outcome

At the end of Phase 0:

- The platform topology is documented accurately and matches the hardware in use.
- Every host is reachable and predictable.
- Ansible is the single management path, with a clean bootstrap-to-managed handoff.
- The foundation exists for the monitoring work that follows.

---

## Notes

This phase prioritises:

- simplicity
- clarity
- reproducibility

The goal is not to build the system yet, but to ensure the system can be understood and controlled before further complexity is introduced.
