# CostPilot

[![Release](https://img.shields.io/github/v/release/cost-pilot/public?label=latest)](https://github.com/cost-pilot/public/releases)
[![Helm](https://img.shields.io/badge/Helm-OCI-blue)](https://github.com/orgs/cost-pilot/packages)

Kubernetes cost visibility and optimisation for engineering teams. CostPilot breaks down your spend by namespace, workload, team, and label — with 50+ automated efficiency insights to help you act.

## How it works

A lightweight agent runs in your cluster, collecting pod, node, and resource metrics. Data is shipped to the CostPilot platform where it's analysed in real time. Your team gets a dashboard with cost breakdowns, efficiency insights, and budget alerts.

```
┌─────────────────┐     ┌──────────────────────┐     ┌──────────────────┐
│  Your Cluster    │     │  CostPilot Platform  │     │    Dashboard     │
│                  │     │                      │     │                  │
│  Operator (1)    │────▶│  Ingestion           │────▶│  Cost breakdown  │
│    └─ Agent (3)  │     │  Processing          │     │  50+ insights    │
│                  │     │  Analysis            │     │  Budget alerts   │
└─────────────────┘     └──────────────────────┘     └──────────────────┘
```

The **operator** manages the agent lifecycle. The **agent** replicas collect metrics and ship them via mTLS. The platform handles the rest.

## Prerequisites

- Kubernetes 1.24+
- Helm 3.8+ (for OCI registry support)
- A CostPilot account and cluster API key — [sign up at cost-pilot.com](https://cost-pilot.com)

## Quick start

### 1. Create the namespace and API key secret

```bash
kubectl create namespace costpilot

kubectl create secret generic cp-agent-secret \
  --namespace costpilot \
  --from-literal=cluster-api-key=YOUR_API_KEY
```

### 2. Install via Helm

```bash
helm upgrade --install costpilot \
  oci://ghcr.io/cost-pilot/helm/cost-pilot-agent \
  --namespace costpilot \
  --set backend.ingesterEndpoint=eu
```

### 3. Verify

```bash
kubectl get pods -n costpilot
```

You should see the operator pod running within ~30 seconds, followed by the agent pods.

### 4. View your dashboard

Log in at [app.cost-pilot.com](https://app.cost-pilot.com) — cost data typically appears within 5 minutes of agent startup.

## Configuration

| Parameter | Description | Default |
|-----------|-------------|---------|
| `backend.ingesterEndpoint` | Region: `eu` | `eu` |
| `secrets.agent.clusterApiKeySecretName` | Name of the secret containing `cluster-api-key` | `cp-agent-secret` |
| `agent.replicaCount` | Number of agent replicas | `3` |
| `agent.config.intervalSeconds` | Metric collection interval (seconds) | `60` |
| `image.tag` | Pin to a specific agent version | Chart `appVersion` |

Full values reference: [docs/configuration.md](docs/configuration.md)

## GitOps examples

| Tool | File |
|------|------|
| ArgoCD | [examples/argocd-app.yaml](examples/argocd-app.yaml) |
| Flux | [examples/flux-helmrelease.yaml](examples/flux-helmrelease.yaml) |
| Kustomize | [examples/kustomize/](examples/kustomize/) |

## Upgrading

```bash
helm upgrade costpilot \
  oci://ghcr.io/cost-pilot/helm/cost-pilot-agent \
  --namespace costpilot \
  --reuse-values
```

## Uninstalling

```bash
helm uninstall costpilot --namespace costpilot
kubectl delete namespace costpilot
```

> **Note:** This removes all agent pods and the operator. Your historical data in the CostPilot platform is not affected.

## Security

- The agent is **read-only** — it never writes to your cluster and never reads secrets or config maps
- Runs as non-root (`UID 1000`) with no privilege escalation
- Compatible with the `restricted` Pod Security Standard
- All data is shipped over mTLS

See [docs/rbac.md](docs/rbac.md) for the full RBAC breakdown.

## Releases

Agent images and Helm charts are published automatically on each version tag. See [Releases](https://github.com/cost-pilot/public/releases) for changelogs and install assets.

Each release includes:
- A Helm chart `.tgz` for air-gapped installs
- An `install.yaml` manifest for `kubectl apply` without Helm

| Artefact | Registry |
|----------|----------|
| Agent image | `ghcr.io/cost-pilot/agent:{version}` |
| Helm chart (OCI) | `oci://ghcr.io/cost-pilot/helm/cost-pilot-agent:{version}` |

## Support

- **Issues / bugs:** [GitHub Issues](https://github.com/cost-pilot/public/issues)
- **Docs:** [docs.cost-pilot.com](https://docs.cost-pilot.com)
- **Email:** help@cost-pilot.com
