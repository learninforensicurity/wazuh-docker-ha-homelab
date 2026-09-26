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

The lab includes Debian-based virtual machines running Wazuh agents. Additional authorized endpoints can be added as the lab expands.

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
