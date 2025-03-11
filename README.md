# LGP Monitoring Stack

A production-grade monitoring solution for Linux servers using Prometheus, Grafana, Loki, and related components.

## Architecture

This stack implements a comprehensive monitoring solution with the following components:

- **Prometheus**: Time series database and metrics collector with alerting capabilities
- **Grafana**: Visualization platform for metrics, logs, and alerting
- **Loki**: Log aggregation system optimized for Prometheus and Grafana
- **Alloy**: Grafana's observability correlation engine
- **cAdvisor**: Container resource usage and performance monitoring
- **Node Exporter**: Physical host metrics collector (CPU, memory, disk, network)

## Component Details

| Component | Purpose | Default Port | Data Retention |
|-----------|---------|--------------|----------------|
| Prometheus | Metrics collection and storage | 3004 | 15d / 10GB |
| Grafana | Visualization and dashboarding | 3003 | Persistent |
| Loki | Log aggregation | 9020 | Configured via volume |
| Alloy | Correlation engine | 9030 | Persistent |
| cAdvisor | Container metrics | 9050 | N/A (scrape only) |
| Node Exporter | Host metrics | 9060 | N/A (scrape only) |

## Quick Start

```shell
# Optional: Customize configuration 
# edit config/prometheus.yml, docker-compose.yml, etc.

# Start the monitoring stack
docker-compose up -d

# Verify all components are running
docker-compose ps

# Stop the stack
docker-compose down
```

## Detailed Configuration

### Prometheus

The Prometheus configuration (`config/prometheus.yml`) includes:

- 60s scrape interval for most targets
- Sample limits to prevent overload (1000 global, 50000 per job)
- Alert rules in `alerts.yml`
- Job configurations for all monitoring components

To customize:
- Adjust `scrape_interval` for different reporting frequencies
- Modify `sample_limit` based on your server capacity
- Configure alerting rules in `alerts.yml`

### Multi-Server Monitoring

For monitoring multiple servers:

1. **Static Configuration**:
   ```yaml
   # In prometheus.yml
   - job_name: 'remote-node'
     static_configs:
       - targets: ['server1:9100', 'server2:9100', 'server3:9100']
         labels:
           env: production
   ```

2. **Service Discovery**:
   - Consider using DNS or file-based discovery for dynamic environments

### Resource Allocation

The stack is configured with reasonable defaults:
- Prometheus: 1-2GB memory reservation
- Individual exporters: minimal overhead (~50-100MB each)

For high-traffic environments, adjust the `deploy.resources` section in `docker-compose.yml`.

## Security Considerations

This setup includes basic security measures:

1. Container isolation via Docker networks
2. Resource limits to prevent DoS
3. Basic authentication for Grafana

**Production Hardening Steps:**
- Enable TLS for all components
- Use secrets management for credentials
- Implement network policies/firewall rules
- Set up proper authentication for Prometheus

## Alerting Setup

1. **Define alerts** in `config/prometheus-alerts.yml`:
   ```yaml
   groups:
   - name: host_alerts
     rules:
     - alert: HighCPULoad
       expr: 100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
       for: 5m
       labels:
         severity: warning
       annotations:
         summary: "High CPU load on {{ $labels.instance }}"
         description: "CPU load is > 80% for 5 minutes"
   ```

2. **Configure Alertmanager** to handle notifications (email, Slack, PagerDuty, etc.)

## Performance Tuning

- **Prometheus retention**: Adjust `--storage.tsdb.retention.time` and `--storage.tsdb.retention.size`
- **Scrape interval**: Balance between granularity and performance
- **Query optimization**: Use recording rules for complex queries
- **Resource allocation**: Adjust container memory limits based on load

## Troubleshooting

Common issues and solutions:

1. **Prometheus not starting**: Check volume permissions and storage capacity
2. **Missing metrics**: Verify scrape configurations and target accessibility
3. **High memory usage**: Adjust retention settings and sample limits
4. **Dashboard errors**: Check data source configuration in Grafana

Debug commands:
```shell
# Check Prometheus targets
curl -s http://localhost:3004/api/v1/targets | jq .

# Verify scrape metrics
curl -s http://localhost:9060/metrics | grep node_

# Check Loki logs
docker-compose logs loki
```

## Dashboard Examples

The stack comes with pre-configured dashboards for:
- Node Exporter metrics (CPU, memory, disk, network)
- Container performance monitoring
- System overview metrics
- Log visualization and analysis
