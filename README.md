# CostPilot

Kubernetes cost visibility and optimisation for engineering teams. CostPilot breaks down your spend by namespace, workload, team, and label — giving you the data to act.

## How it works

You install a lightweight agent into your cluster. The agent collects pod and node metrics and ships them to the CostPilot platform. Your team gets a live dashboard with cost breakdowns, efficiency insights, and budget alerts.

```
Your Cluster → CostPilot Agent → CostPilot Platform → Dashboard
```

## Prerequisites

- Kubernetes 1.24+
- Helm 3.8+ (for OCI registry support)
- A CostPilot account and cluster API key ([sign up](https://cost-pilot.com))

## Quick install

### 1. Create the namespace and API key secret

```bash
kubectl create namespace costpilot

kubectl create secret generic cp-agent-secret \
  --namespace costpilot \
  --from-literal=cluster-api-key=YOUR_API_KEY
```

### 2. Install via Helm (OCI)

```bash
helm upgrade --install costpilot \
  oci://ghcr.io/cost-pilot/helm/agent \
  --namespace costpilot \
  --set backend.ingesterEndpoint=eu
```

### 3. Verify

```bash
kubectl get pods -n costpilot
```

You should see the operator pod running within ~30 seconds, followed by the agent pods.

## Configuration

| Parameter                               | Description                                     | Default           |
| --------------------------------------- | ----------------------------------------------- | ----------------- |
| `backend.ingesterEndpoint`              | Region: `eu`                                    | `eu`              |
| `secrets.agent.clusterApiKeySecretName` | Name of the secret containing `cluster-api-key` | `cp-agent-secret` |
| `agent.replicaCount`                    | Number of agent replicas                        | `3`               |
| `agent.config.intervalSeconds`          | Metric collection interval                      | `60`              |
| `image.tag`                             | Pin to a specific agent version                 | `latest`          |

Full values reference: [docs/configuration.md](docs/configuration.md)

## Examples

| Tool      | File                                                             |
| --------- | ---------------------------------------------------------------- |
| ArgoCD    | [examples/argocd-app.yaml](examples/argocd-app.yaml)             |
| Flux      | [examples/flux-helmrelease.yaml](examples/flux-helmrelease.yaml) |
| Kustomize | [examples/kustomize/](examples/kustomize/)                       |

## Upgrading

```bash
helm upgrade costpilot \
  oci://ghcr.io/cost-pilot/helm/agent \
  --namespace costpilot \
  --reuse-values
```

## Uninstalling

```bash
helm uninstall costpilot --namespace costpilot
kubectl delete namespace costpilot
```

> **Note:** This will remove all agent pods and the operator. Your historical data in the CostPilot platform is not affected.

## Support

- **Issues / bugs:** [GitHub Issues](https://github.com/cost-pilot/public/issues)
- **Docs:** [docs.cost-pilot.com](https://docs.costpilot.io)
- **Email:** help@cost-pilot.com

## Releases

Agent images and Helm charts are published automatically on each version tag. See [Releases](https://github.com/cost-pilot/public/releases) for changelogs and install assets.

| Artefact         | Registry                                                    |
| ---------------- | ----------------------------------------------------------- |
| Agent image      | `ghcr.io/cost-pilot/agent:{version}`            |
| Helm chart (OCI) | `oci://ghcr.io/cost-pilot/helm/agent:{version}` |
