# Monitoring Role

This role sets up monitoring for the query_gateway service using Prometheus and Grafana. It includes:

- Prometheus for metrics collection
- Grafana for metrics visualization
- Nginx as a reverse proxy for Grafana
- Pre-configured dashboard for the query_gateway service metrics

## Dashboard Details

The dashboard includes:
- HTTP request rate 
- HTTP request duration (p95)
- Error counts (4xx and 5xx)
- Memory usage
- CPU usage

## Usage

Simply include the monitoring role in your playbook after the portal role:

```yaml
- hosts: your_server
  roles:
    - portal
    - monitoring
```

## Accessing the Dashboard

After deployment, access the Grafana dashboard at:
- URL: http://your_server_ip/
- Default login: admin/admin (change this in production)

## Customizing the Dashboard

You can customize the dashboard through the Grafana UI. Changes will be persisted.

## Metrics Endpoint

The setup assumes your query_gateway service exposes a `/metrics` endpoint on port 8000 in Prometheus format. If your metrics endpoint is different, update the Prometheus configuration at `roles/monitoring/templates/prometheus/prometheus.yml`.

## Security Considerations

For production:
1. Change the default Grafana admin password
2. Consider adding HTTPS support for Grafana
3. Restrict access to Prometheus (default: exposed on port 9090)
4. Add authentication to Prometheus if exposed publicly 