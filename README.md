# SQD Portal Deployment Guide

This guide will walk you through deploying a SQD Portal along with its monitoring stack (Prometheus and Grafana) using Ansible.

## Minimum Hardware Requirements

### Portal
* **RAM**: Minimum 12GB RAM
* **CPU**: 2 vCPUs for light usage
* **Storage**: At least 20GB of free disk space
* **Operating System**: Ubuntu 20.04 LTS or newer

### Hotblocks Service
* **RAM**: Minimum 4GB RAM
* **CPU**: 1 vCPUs for light usage
* **Storage**: At least 40GB of free disk space
* **Recommended OS**: Ubuntu 20.04 LTS or newer

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/subsquid-labs/bare-metal-private-Portal.git
cd bare-metal-private-Portal
```

### 2. Install Ansible Dependencies

This project requires several Ansible collections. Install them using:

```bash
ansible-galaxy install -r requirements.yml
```

### 3. Configure Your Inventory

Edit the `inventory.yml` file to specify your target servers. Replace the example values with your own:

```yaml
portals:
  hosts:
    Portal-node-1:
      ansible_host: your-server-ip-or-domain.com
      ansible_user: your-user
      ansible_ssh_private_key_file: ~/path/to/your/private-key.pem
```

You can add multiple hosts under the `portals` group if you want to deploy to multiple servers.

### 4. Configure Environment Variables

The default configuration will deploy a SQD Portal connected to the Tethys testnet. If you need to customize settings, review the environment variables in `roles/portal/templates/.env`.

Key configurations you might want to adjust:
* `RPC_URL`: Your preferred Arbitrum RPC endpoint
* `L1_RPC_URL`: Your preferred Ethereum RPC endpoint

### 5. Add your Portal key (TODO: this doesn't look safe)

Add your key file to `roles/portal/templates/` named as portal-key

### 6. Run the Deployment

To deploy both the Portal and monitoring stack:

```bash
ansible-playbook -i inventory.yml deploy.yml
```

To deploy just the Portal without monitoring:

```bash
ansible-playbook -i inventory.yml portal.yml
```

To deploy just the monitoring stack without Portal:

```bash
ansible-playbook -i inventory.yml portal.yml
```

### 6. Accessing Your Services

After successful deployment, you can access your services at:

* **Portal**: `http://your-server-ip-or-domain.com/` 
* **Grafana Dashboard**: `http://your-server-ip-or-domain.com:3000`
  * Default login: username `admin`, password `admin`
  * **Important**: Change the default password after first login!
* **Prometheus**: `http://your-server-ip-or-domain.com:9090/`

## Security Considerations

### Firewalls

The deployment automatically configures UFW (Uncomplicated Firewall) to allow the necessary ports:
* 22 (SSH)
* 80 (HTTP)
* 443 (HTTPS)
* 8000 (Portal)
* 3000 (Grafana)
* 9090 (Prometheus)

If you're using cloud providers like AWS, Azure, or GCP, you'll need to configure their security groups/firewalls to allow these ports as well.

### Production Recommendations

For production deployments, consider:

1. Adding HTTPS support with Let's Encrypt
2. Changing default passwords for Grafana
3. Restricting access to Prometheus or adding authentication
4. Setting up regular backups

## Troubleshooting

### Common Issues

1. **Connection timeouts**: Ensure your SSH key has the correct permissions (`chmod 600 your-key.pem`) and your server's firewall allows SSH connections.

2. **Docker network issues**: If services can't communicate, you might need to restart Docker:
   ```bash
   ssh your-server "sudo systemctl restart docker"
   ```

3. **Metric data not showing in Grafana**: Ensure that:
   * The Prometheus data source is configured correctly in Grafana
   * Docker networks are properly connected
   * The Query Gateway service is exposing metrics at port `:3000`

### Logs

To check logs for troubleshooting:

```bash
# Portal logs
ssh your-server "docker logs \$(docker ps | grep query_gateway | awk '{print \$1}')"

# Prometheus logs
ssh your-server "docker logs \$(docker ps | grep prometheus | awk '{print \$1}')"

# Grafana logs
ssh your-server "docker logs \$(docker ps | grep grafana | awk '{print \$1}')"
```

## Maintenance and Updates

### Updating the Portal

To update to a newer version of the Portal:

1. Update the image tag in `roles/Portal/templates/docker-compose.yaml`
2. Run the deployment playbook again:
   ```bash
   ansible-playbook -i inventory.yml portal.yml
   ```