# Resource Consumption Analysis - Grafana Dashboards

A comprehensive suite of Grafana dashboards for analyzing Kubernetes workload resource consumption, with simulation profiles for rightsizing recommendations.

## Dashboards

### 📊 Namespace Overview Dashboard (`grafana-dashboard.json`)
- **UID**: `workload-resource-analysis`
- Provides namespace-level overview of all workloads
- CPU and Memory resource usage tables with min/max/avg statistics
- Resource utilization and risk analysis
- Limit/Request ratio analysis
- Time distribution by resource zone
- **Simulation rows** for Normal and Aggressive optimization profiles

### 🔍 Workload Details Dashboard (`grafana-dashboard-workload.json`)
- **UID**: `workload-resource-workload`
- Deep-dive analysis for individual workloads
- Real-time CPU/Memory usage time series with request/limit lines
- Gauge panels for utilization percentages and limit/request ratios
- Per-pod breakdown for troubleshooting
- **Simulation rows** with Normal and Aggressive profile recommendations

## Documentation Dashboards

### 📖 Namespace Overview Documentation (`grafana-dashboard-docs.json`)
- **UID**: `workload-resource-analysis-docs`
- Detailed explanations for all panels in the Namespace Overview Dashboard
- Threshold definitions and color interpretations
- Formula descriptions and usage guidance

### 📖 Workload Details Documentation (`grafana-dashboard-workload-docs.json`)
- **UID**: `workload-resource-workload-docs`
- Detailed explanations for all panels in the Workload Details Dashboard
- Threshold definitions for gauges and simulated values

## Simulation Profiles

| Profile | CPU Request | CPU Limit | Memory Request | Memory Limit |
|---------|-------------|-----------|----------------|--------------|
| **Normal** | P90 | P99 × 1.5 | P95 | Max × 1.2 |
| **Aggressive** | P95 × 1.1 | Max × 1.25 | Max | Max × 1.25 |

## Management Script

### `manage-dashboards.sh`

Bash script for managing dashboards on a Grafana server.

#### Commands

```bash
# Import all dashboards to a folder
./manage-dashboards.sh import --url https://grafana.example.com --folder "My Folder" --user admin --password secret

# Delete dashboards from a folder
./manage-dashboards.sh delete --url https://grafana.example.com --folder "My Folder" --user admin --password secret

# List dashboards in a folder
./manage-dashboards.sh list --url https://grafana.example.com --folder "My Folder" --user admin --password secret

# Check connection status
./manage-dashboards.sh status --url https://grafana.example.com --user admin --password secret
```

#### Options

| Option | Description |
|--------|-------------|
| `--url URL` | Grafana server URL (or `GRAFANA_URL` env var) |
| `--folder NAME` | Target folder name |
| `--user USER` | Username for basic auth (or `GRAFANA_USER` env var) |
| `--password PASS` | Password for basic auth (or `GRAFANA_PASSWORD` env var) |
| `--api-key KEY` | API key/Service Account token (or `GRAFANA_API_KEY` env var) |
| `--insecure` | Skip TLS certificate verification |
| `--namespace-filter REGEX` | Filter namespaces by regex (default: `.*` = all) |
| `--workload-kinds KINDS` | Pod owner kinds to include (default: `ReplicaSet\|ReplicationController\|StatefulSet`) |

#### Examples

```bash
# Import with namespace filtering (only prod namespaces)
./manage-dashboards.sh import --url https://grafana.example.com \
  --folder "Production" --user admin --password secret \
  --namespace-filter "^prod-.*"

# Import with custom workload kinds
./manage-dashboards.sh import --url https://grafana.example.com \
  --folder "Custom" --user admin --password secret \
  --workload-kinds "Deployment|StatefulSet|DaemonSet"

# Use environment variables
export GRAFANA_URL="https://grafana.example.com"
export GRAFANA_USER="admin"
export GRAFANA_PASSWORD="secret"
./manage-dashboards.sh import --folder "My Folder"
```

## Requirements

- Grafana 10.x+
- Prometheus datasource with Kubernetes metrics:
  - `container_cpu_usage_seconds_total`
  - `container_memory_working_set_bytes`
  - `kube_pod_info`
  - `kube_pod_container_resource_requests`
  - `kube_pod_container_resource_limits`
