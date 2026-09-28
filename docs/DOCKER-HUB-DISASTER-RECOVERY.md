# Docker Hub & Disaster Recovery

## Docker Hub Images

The lab publishes the Wazuh 4.14.8 container images used by the homelab.

| Component | Docker Hub Image | Version |
|---|---|---|
| Wazuh Manager | `learninforensicurity123/wazuh-manager` | `4.14.8` |
| Wazuh Indexer | `learninforensicurity123/wazuh-indexer` | `4.14.8` |
| Wazuh Dashboard | `learninforensicurity123/wazuh-dashboard` | `4.14.8` |

These images provide a recoverable copy of the container images used by the lab.

## Backup Responsibilities

The homelab uses multiple layers of recovery.

### GitHub / GitLab

The Git repositories preserve the project configuration and documentation, including:

- Docker Compose configuration
- Wazuh configuration files
- Nginx configuration
- Architecture diagrams
- Documentation
- Deployment and troubleshooting procedures

### Docker Hub

Docker Hub preserves the published container images:

- Wazuh Manager 4.14.8
- Wazuh Indexer 4.14.8
- Wazuh Dashboard 4.14.8

### Separate Runtime Backup

Docker Hub and Git repositories do **not** preserve the following runtime state:

- Docker volumes
- Wazuh manager queues
- Wazuh indexer data
- Vulnerability detection feed data
- Enrolled-agent runtime state
- Docker Desktop / WSL2 state
- VirtualBox virtual machines
- VirtualBox snapshots

These require separate backup procedures if full stateful recovery is required.

## Disaster Recovery Strategy

If the `C:\CyberLab` project directory is lost, the high-level recovery process is:

1. Install Docker Desktop with WSL2 support.
2. Clone the project repository from GitHub or GitLab.
3. Recreate the local `.env` file from the documented secret/configuration requirements.
4. Pull the required Docker images from Docker Hub.
5. Deploy the Wazuh HA Docker Compose stack.
6. Verify the Wazuh manager cluster.
7. Verify the three-node Wazuh indexer cluster.
8. Verify the Wazuh Dashboard.
9. Verify Nginx agent load balancing.
10. Reconnect/re-enroll Wazuh agents where required.
11. Restore separate Docker volume backups if stateful recovery is required.
12. Verify endpoint visibility and security-event ingestion.

## Important Limitation

Rebuilding the Docker containers from Git and Docker Hub creates the application infrastructure again, but it does not automatically restore historical Wazuh data.

For full stateful recovery, Docker volume backups and endpoint/VM backups must be maintained separately.

## Current Lab Recovery Assets

| Asset | Recovery Source |
|---|---|
| Docker Compose configuration | GitHub / GitLab |
| Wazuh configuration | GitHub / GitLab |
| Nginx configuration | GitHub / GitLab |
| Architecture diagrams | GitHub / GitLab |
| Wazuh Manager image | Docker Hub |
| Wazuh Indexer image | Docker Hub |
| Wazuh Dashboard image | Docker Hub |
| Wazuh historical data | Separate volume backup required |
| Wazuh queues | Separate volume backup required |
| VirtualBox VMs | Separate VM backup required |
| VM snapshots | Separate VirtualBox backup required |
| `.env` secrets | Secure separate backup required |

## Versioning

The published Wazuh images are explicitly versioned as `4.14.8`.

Future Wazuh upgrades should use new version tags rather than replacing the existing `4.14.8` recovery point.

Example:

```text
wazuh-manager:4.14.8
wazuh-manager:<new-version>