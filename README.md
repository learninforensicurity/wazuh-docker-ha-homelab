# Wazuh Docker HA Homelab

A cybersecurity homelab built around a highly available Wazuh deployment using Docker, Docker Compose, VirtualBox, and Debian-based endpoints.

## Project Overview

This project is designed as a practical cybersecurity learning environment for:

- Security monitoring and SIEM practice
- Wazuh manager clustering
- Wazuh indexer clustering
- Endpoint monitoring
- Log collection and analysis
- Docker and container administration
- Network monitoring and troubleshooting
- Git/GitLab project management
- Cybersecurity lab experimentation

The environment is intentionally designed to reduce the number of virtual machines required by running the Wazuh server infrastructure in Docker.

## Architecture

The lab uses a Docker-based Wazuh high-availability architecture:

    Wazuh Dashboard (HTTPS/443)

             |

            Nginx

             |

      +------+------+
      |             |
Wazuh Manager   Wazuh Manager
   Master          Worker
      |             |
      +------+------+
             |

     Wazuh Indexer
      3-node cluster

Endpoints connect to the Wazuh manager cluster and send security telemetry for monitoring and analysis.

### Main Components

| Component | Quantity | Purpose |
|---|---:|---|
| Wazuh Manager | 2 | Master/worker manager cluster |
| Wazuh Indexer | 3 | Search and storage cluster |
| Wazuh Dashboard | 1 | Web-based monitoring interface |
| Nginx | 1 | Front-end reverse proxy |
| Debian Wazuh Agent | 1+ | Endpoint monitoring |
| Docker Compose | 1 | Container orchestration |

## Deployment

- Windows host with WSL2
- Docker Desktop with WSL2 backend
- Docker Compose
- Oracle VirtualBox
- Git
- Debian-based Wazuh agent VM
### Start the Wazuh stack

From the project directory:

    docker compose up -d

### Check container status

    docker compose ps

All expected Wazuh services should be running before proceeding with agent enrollment.
### Stop the stack

    docker compose down
> Keep local secrets in .env. Do not commit .env, private TLS keys, or local credential-bearing configuration files to the repository.
## Verification

### Wazuh manager cluster

    docker compose exec wazuh.master bash -c "/var/ossec/bin/cluster_control -l"

The command should show the master and worker nodes.

### Wazuh indexer cluster

    Use the Wazuh indexer credentials stored in your local environment to query `https://wazuh1.indexer:9200/_cat/nodes?v`. Do not place the password in this README.

Use the command to verify that all three indexer nodes are participating in the cluster.
## Debian Wazuh Agent

A Debian VM is used as an endpoint for Wazuh monitoring.

The agent communicates with the Wazuh manager over the configured Wazuh agent ports. The agent configuration should be generated from the Wazuh Dashboard and installed using the appropriate Debian package.

After installation, enable and start the agent service:

    sudo systemctl enable wazuh-agent
    sudo systemctl start wazuh-agent

Verify the service:

    sudo systemctl status wazuh-agent
## Network

The lab uses a VirtualBox NAT Network for communication between the host environment and virtual machines.

Example lab network:

    192.168.100.0/24

The exact IP addresses may vary depending on the local VirtualBox configuration. Avoid publishing personal or sensitive network information in the public repository.
## Security Notes

- Never commit .env files containing credentials.
- Never commit private TLS keys or certificates that contain private key material.
- Keep credential-bearing local Wazuh configuration files outside Git when they contain secrets.
- Use strong, unique credentials for deployments outside a disposable lab environment.
- Review Git history before making a repository public.
- If a secret is ever accidentally committed, rotate the affected credential and remove the secret from repository history before publishing.
## Project Status

The lab currently includes:

- Wazuh manager master/worker cluster
- Three-node Wazuh indexer cluster
- Wazuh Dashboard
- Nginx reverse proxy
- Debian Wazuh agent
- Docker Compose deployment
- Git/GitLab version control

The configuration is intended for cybersecurity learning, experimentation, and homelab practice.
## Git and GitLab

The project is maintained using Git and hosted on GitLab.

Repository:

https://gitlab.com/learninforensicurity/wazuh-docker-ha-homelab

Before pushing changes, verify that no credentials, private keys, or other sensitive information are included in the commit.
## Troubleshooting

### Check running containers

    docker compose ps

### View container logs

    docker compose logs --tail 100 <service-name>

### Restart a specific service

    docker compose restart <service-name>

### Check the Debian agent

    sudo systemctl status wazuh-agent
    sudo journalctl -u wazuh-agent --no-pager -n 100

For cluster problems, verify Docker networking, service names, certificates, and the manager/indexer cluster status before changing configuration.
## Disclaimer

This project is intended for authorized cybersecurity education, testing, and homelab use. Only monitor systems and networks that you own or have explicit permission to assess.
