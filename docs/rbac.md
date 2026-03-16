# RBAC

CostPilot requires read-only access to Kubernetes resources to collect cost metrics. The Helm chart creates all necessary RBAC resources automatically when `operator.rbac.create: true` (default).

## What the agent reads

| Resource | API Group | Verbs |
|----------|-----------|-------|
| `nodes` | `""` (core) | `get`, `list` |
| `pods` | `""` (core) | `get`, `list` |
| `persistentvolumeclaims` | `""` (core) | `get`, `list` |
| `pods`, `nodes` | `metrics.k8s.io` | `get`, `list` |
| `verticalpodautoscalers` | `autoscaling.k8s.io` | `get`, `list` |

The agent **never writes** to the cluster and **never reads** secrets or config maps.

## Bring-your-own service account

If you manage RBAC externally (e.g. via OPA/Gatekeeper policy), disable chart-managed RBAC:

```yaml
operator:
  serviceAccount:
    create: false
    name: my-existing-sa   # must exist in the costpilot namespace
  rbac:
    create: false
```

You are responsible for binding the service account to an appropriate ClusterRole.

## Restricted pod security

The agent and operator run as non-root (`runAsUser: 1000`, `runAsNonRoot: true`) with no privilege escalation. They are compatible with the `restricted` Pod Security Standard.
