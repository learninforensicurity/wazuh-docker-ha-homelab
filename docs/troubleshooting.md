## Wazuh Docker HA Homelab - Troubleshooting

## Docker Containers

Check the status of all services:

    docker compose ps

View recent logs for a service:

    docker compose logs --tail 100 <service-name>

## Wazuh Manager Cluster

Check manager cluster membership:

    docker compose exec wazuh.master bash -c "/var/ossec/bin/cluster_control -l"

Expected result: one master node and one worker node, both running the same Wazuh version.

## Wazuh Indexer Cluster

Check the indexer cluster nodes:

    docker compose exec wazuh1.indexer bash -c "curl -sk -u admin:YOUR_INDEXER_PASSWORD https://wazuh1.indexer:9200/_cat/nodes?v"

All three indexer nodes should appear in the output. Do not commit the password or other credentials to Git.

## Wazuh Dashboard

Check that the dashboard container is running:

    docker compose ps wazuh.dashboard

Test the dashboard locally:

    curl.exe -k -I https://localhost/

A HTTP 302 response redirecting to the login page indicates that the dashboard is responding.

## Debian Wazuh Agent

Check the agent service on Debian:

    sudo systemctl status wazuh-agent

Check the Debian VM network configuration:

    ip addr
    ip route

The lab currently uses the 192.168.100.0/24 VirtualBox NAT Network.

## Docker Desktop and WSL2

If Docker commands fail unexpectedly, verify Docker Desktop is running and the WSL2 backend is enabled.

Check WSL status:

    wsl --status

Check Docker connectivity:

    docker info

If containers stop unexpectedly, check their status and recent logs before restarting the stack.

## Git and Security

Do not commit .env files, local Wazuh configuration containing credentials, TLS private keys, or other secrets.

Before pushing changes, review the files that Git will track:

    git status

Review the staged changes before committing:

    git diff --cached

## Recovery Approach

When troubleshooting, first identify the affected service with docker compose ps, then inspect its logs with docker compose logs --tail 100 <service-name>.

Avoid deleting volumes or rebuilding the entire environment unless the problem requires it. Preserve configuration and backups before destructive changes.

