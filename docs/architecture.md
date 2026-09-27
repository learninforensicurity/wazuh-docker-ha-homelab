# Wazuh Docker HA Homelab - Architecture

## Overview

This document describes the architecture of the Docker-based Wazuh high-availability homelab.

## Wazuh Server Layer

The server infrastructure consists of:

- 2 Wazuh Manager nodes: one master and one worker
- 3 Wazuh Indexer nodes forming an indexer cluster
- 1 Wazuh Dashboard
- 1 Nginx reverse proxy

## Endpoint Layer

The lab currently includes three authorized Wazuh endpoints:

- Debian Linux VM - Wazuh agent 001
- Physical Windows 10 Pro endpoint - Wazuh agent 002
- Windows 7 Enterprise VM - Wazuh agent 003

The Windows 7 VM is retained as a controlled legacy Windows endpoint for cybersecurity training and testing.

The Debian and Windows 7 virtual machines are connected to the VirtualBox NAT Network used by the lab. The physical Windows 10 endpoint communicates with the Docker-based Wazuh infrastructure over the host LAN.

## Endpoint Network Layout

The current lab endpoint networks are:

    VirtualBox NAT Network
    192.168.100.0/24

    Physical LAN
    192.168.29.0/24

Wazuh agent traffic uses TCP 1514. The Nginx layer load-balances agent traffic between the Wazuh master and worker managers.

Agent enrollment uses TCP 1515 and is handled directly by the Wazuh manager service.

Virtual machine snapshots are maintained as recovery points before cybersecurity exercises that may intentionally modify the endpoint state.

## Load Balancing

Nginx provides the agent traffic load-balancing layer for the Wazuh manager cluster.

Wazuh agents connect to the Nginx endpoint for ongoing agent communication rather than directly to an individual manager:

    Wazuh Agent
         |
         | TCP 1514
         v
    Nginx Load Balancer
         |
         +----> Wazuh Master :1514
         |
         +----> Wazuh Worker :1514

The Nginx configuration uses the stream module to load-balance TCP/1514
agent connections between the Wazuh master and worker nodes.

The lab uses consistent hashing based on the client address so that an
individual endpoint is consistently directed to a backend manager while
connections can be distributed across the manager nodes.

Agent enrollment uses TCP/1515 and is handled separately by the Wazuh
manager service.

This design provides a single agent-facing endpoint and avoids configuring
each endpoint with individual manager-node addresses.

## Network Layer

VirtualBox provides the virtual-machine networking. The lab uses a private NAT Network for communication between the endpoint VMs and the Docker-based Wazuh infrastructure.

Example lab network:

    192.168.100.0/24

Individual addresses should be documented only when they are useful for reproducing the lab and do not expose sensitive local information.

## Data Flow

1. Wazuh agents collect security telemetry from endpoints.
2. Agents communicate with the Wazuh manager layer.
3. The manager layer processes and forwards events to the Wazuh indexer cluster.
4. The indexers store and make event data searchable.
5. The Wazuh Dashboard provides the web interface for security monitoring and analysis.

## Design Goals

- Reduce VM resource consumption by containerizing the Wazuh server layer.
- Practice manager and indexer clustering.
- Practice endpoint onboarding and monitoring.
- Provide a reproducible cybersecurity learning environment.
- Keep secrets and private TLS material outside the public Git repository.
