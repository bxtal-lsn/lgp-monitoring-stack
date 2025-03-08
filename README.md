# Monitoring Stack with Grafana, Prometheus, Loki & Alloy

This repository contains a complete monitoring stack for visualizing metrics and logs from containers and host systems using Docker Compose.

## Components

- **Grafana**: Visualization and dashboarding (port 3000)
- **Prometheus**: Metrics collection and storage (port 9090)
- **Loki**: Log aggregation system (port 3100)
- **Alloy**: Grafana complementary observability system (port 12345)
- **Node Exporter**: Host metrics exporter (port 9100)
- **cAdvisor**: Container metrics exporter (port 8080)

## Quick Start

```bash
# Start the monitoring stack
docker-compose up -d

# View logs
docker-compose logs -f

# Stop the stack
docker-compose down
```

## Services Access

- **Grafana**: http://localhost:9010 (user: admin, password: password)
- **Prometheus**: http://localhost:9040
- **Loki**: http://localhost:9020
- **cAdvisor**: http://localhost:9050
- **Node Exporter**: http://localhost:9060
- **Alloy**: http://localhost:9030

## Directory Structure

```
.
├── config/
│   ├── alloy.conf                     # Alloy configuration
│   ├── loki.yml                       # Loki configuration
│   ├── prometheus.yml                 # Prometheus configuration
│   ├── prometheus-alerts.yml          # Prometheus alerting rules
│   └── provisioning/
│       ├── dashboards/                # Grafana dashboard provisioning
│       │   ├── dashboard-provider.yml # Dashboard provider config
│       │   └── node-exporter-full.json # Node Exporter dashboard
│       └── datasources/               # Grafana datasource provisioning
│           └── grafana-datasources.yml # Datasource config
├── docker-compose.yml                 # Docker Compose configuration
└── README.md                          # This file
```

## Preconfigured Features

- **Dashboards**: Node Exporter Full dashboard pre-installed
- **Datasources**: Prometheus and Loki automatically configured
- **Alerts**: Basic host and container alerts configured in Prometheus
- **Metrics**: Host and container metrics automatically collected
- **Persistent Storage**: All data stored in Docker volumes

## Configuration

### Modifying Prometheus Targets

Edit `config/prometheus.yml` to add additional scrape targets.

### Adding Grafana Dashboards

Place JSON dashboard files in `config/provisioning/dashboards/` and restart the stack.

### Alerting Rules

Edit `config/prometheus-alerts.yml` to modify alerting rules.

## Troubleshooting

**Dashboards not showing up:**
- Check Grafana logs: `docker-compose logs grafana`
- Verify directory structure matches documentation
- Ensure dashboard JSON files are valid

**Service won't start:**
- Check for port conflicts
- Verify all required files exist in the correct locations
- Check individual service logs: `docker-compose logs [service_name]`

## Security Notes

This setup uses default credentials and is intended for local development or testing. For production use:
- Change default Grafana password
- Set up proper authentication for all services
- Use secrets management for credentials
- Configure TLS for all exposed endpoints
