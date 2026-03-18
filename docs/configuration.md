# Configuration Reference

All configuration is passed as Helm values. Override with `--set key=value` or a custom `values.yaml` file.

## Image

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `image.repository` | string | `agent` | Image repository (under `global.imageRegistry`) |
| `image.tag` | string | chart `appVersion` | Image tag — pin this for production |
| `image.pullPolicy` | string | `IfNotPresent` | Kubernetes pull policy |
| `global.imageRegistry` | string | `ghcr.io/cost-pilot` | Registry prefix |
| `global.imagePullSecrets` | list | `[]` | Pull secrets if registry is private |

## Backend

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `backend.ingesterEndpoint` | string | `eu` | CostPilot region — `eu` |

## Agent

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `agent.enabled` | bool | `true` | Deploy the agent |
| `agent.replicaCount` | int | `3` | Number of agent replicas (managed by operator) |
| `agent.config.intervalSeconds` | int | `60` | Metric collection interval in seconds |
| `agent.config.peerPort` | string | `18443` | Port for leader/follower coordination between replicas |

## Operator

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `operator.enabled` | bool | `true` | Deploy the operator |
| `operator.replicaCount` | int | `1` | Operator replicas (keep at 1) |
| `operator.resources.requests.cpu` | string | `50m` | CPU request |
| `operator.resources.requests.memory` | string | `64Mi` | Memory request |
| `operator.resources.limits.cpu` | string | `100m` | CPU limit |
| `operator.resources.limits.memory` | string | `128Mi` | Memory limit |
| `operator.serviceAccount.create` | bool | `true` | Create a dedicated service account |
| `operator.serviceAccount.annotations` | map | `{}` | Annotations (e.g. for IRSA/Workload Identity) |
| `operator.rbac.create` | bool | `true` | Create RBAC resources |

## Secrets

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `secrets.agent.clusterApiKeySecretName` | string | `cp-agent-secret` | Name of the Kubernetes secret containing `cluster-api-key` |

The secret must exist before install:

```bash
kubectl create secret generic cp-agent-secret \
  --namespace costpilot \
  --from-literal=cluster-api-key=YOUR_API_KEY
```

## Full example values file

```yaml
image:
  tag: "0.1.0" # Pin to a specific release

backend:
  ingesterEndpoint: eu

agent:
  replicaCount: 3
  config:
    intervalSeconds: 60

operator:
  resources:
    requests:
      cpu: 50m
      memory: 64Mi
    limits:
      cpu: 200m
      memory: 256Mi

secrets:
  agent:
    clusterApiKeySecretName: cp-agent-secret
```

Apply with:

```bash
helm upgrade --install costpilot \
  oci://ghcr.io/cost-pilot/helm/agent \
  --namespace costpilot \
  --values my-values.yaml
```
