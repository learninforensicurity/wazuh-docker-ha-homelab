# Wazuh Docker HA Homelab - Deployment Guide

## Prerequisites

- Windows host with WSL2 enabled
- Docker Desktop using the WSL2 backend
- Docker Compose
- Oracle VirtualBox
- Git
- Sufficient CPU, RAM, and disk space for the Wazuh stack
- A Debian-based VM for endpoint testing

## Project Preparation

Clone or copy this repository to the lab host and open a PowerShell terminal in the project directory.

    cd C:\CyberLab\wazuh-docker-ha-homelab

## Local Secrets

Runtime credentials must be kept outside the public repository.

Create a local .env file containing the credentials required by the Compose deployment. The .env file is intentionally excluded from Git.

Do not publish the contents of .env.

## Validate the Compose Configuration

Before starting the stack, validate the Compose configuration:

    docker compose config --quiet

A successful validation returns to the PowerShell prompt without an error.

## Start the Wazuh Stack

Start the services in detached mode:

    docker compose up -d

Check the services:

    docker compose ps

The expected deployment contains two Wazuh managers, three Wazuh indexers, one Wazuh Dashboard, and Nginx.

## Verify the Manager Cluster

Run:

    docker compose exec wazuh.master bash -c "/var/ossec/bin/cluster_control -l"

The output should identify the master and worker nodes.

## Verify the Indexer Cluster

Use the indexer credentials stored in the local environment to query the cluster node list. Do not place the password directly in documentation or source control.

The OpenSearch-compatible node endpoint is:

    https://wazuh1.indexer:9200/_cat/nodes?v

All three indexer nodes should be visible when the cluster is healthy.

## Dashboard

The Wazuh Dashboard is exposed through the configured HTTPS reverse-proxy path. The exact URL and certificate behavior depend on the local Docker and browser configuration.

## Shutdown

To stop the stack:

    docker compose down

## Important Notes

- Do not commit .env.
- Do not commit private TLS keys.
- Do not publish credential-bearing local configuration files.
- Keep a backup of working configuration before making major changes.
- Verify cluster health after configuration changes.
