# Phase 0 - Foundation

## Why not dive in to automation?

Before building anything, I want a setup that is predictable and easy to work with.  
This is about getting a setup that is simple and can be build on, with the limited unused hardware I have around.

No automation, no containers, no tooling. Just a stable environment.

---

## What I want out of this

By the end of this phase:

- every device is clearly identified
- I can connect to everything easily
- nothing relies on remembering IP addresses
- the repo reflects what actually exists

If I have to stop and think "which machine is this?", then this phase is not done.

---

## Current setup

### Control node

- control-node
- Lenovo L580 (16GB RAM, 256GB SSD)
- x86-64

This will become the main control point for the platform. It is the most reliable and capable machine I have in this setup.

---

### Worker nodes

- pi-node-1
- pi-node-2
- pi-node-3

(All Raspberry Pi 4)

These will be used to actually run services and workloads.

---

### Legacy nodes

- pi-legacy-a (Pi 1 Model A)
- pi-legacy-b (Pi 1 Model B+)

These are very limited machines. They are not part of the normal workload pool.

I will use them for:
- testing constrained scenarios
- intentionally pushing limits
- understanding failure behaviour

---

## A note on hardware

This is deliberately mixed:

- laptop: strong, reliable
- Pi 4s: reasonable capacity
- Pi 1s: very limited

That is intentional.

It forces decisions about where things should run instead of assuming everything is the same.

---

## Naming

Everything has a fixed name:

control-node  
pi-node-1  
pi-node-2  
pi-node-3  
pi-legacy-a  
pi-legacy-b  

These names will not change later. If they do, something has gone wrong.

---

## Access

I should be able to SSH to anything without thinking about it.

From my dev machine:

ssh control-node  
ssh pi-node-1  
ssh pi-legacy-a  

No passwords, no trial and error.

If connecting to a node feels awkward, that is something to fix in this phase.

---

## Networking

No complicated networking setup.

Just:

- each node has a stable identity (hostname or reserved IP)
- I can reach each node by name

I should never need to remember an IP address.

---

## Baseline state

All nodes should:

- be updated
- have a consistent user account
- be in a usable, known-good state

Nothing special, just clean.

---

## How I know this phase is done

From my dev machine, these should all work without friction:

ssh control-node uptime  
ssh pi-node-1 uptime  
ssh pi-node-2 uptime  
ssh pi-node-3 uptime  
ssh pi-legacy-a uptime  
ssh pi-legacy-b uptime  

If any of these are unreliable or annoying, there is still work to do.

---

## What I am not doing yet

I am deliberately avoiding:

- Ansible
- Docker
- Kubernetes
- monitoring
- CI/CD
- running services

That comes later.

---

## End state

At this point:

- the system is simple and predictable
- I understand what each machine is for
- I can access everything quickly
- I have a clean base to build from

That is enough. Anything more at this stage is unnecessary.
