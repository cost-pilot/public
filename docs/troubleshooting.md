# Troubleshooting

## Agent pods not starting

Check operator logs first — the operator creates and manages agent pods:

```bash
kubectl logs -n costpilot -l app=costpilot-operator
```

Common causes:
- **Secret missing**: Ensure `cp-agent-secret` exists with key `cluster-api-key`
- **Invalid API key**: Check the operator logs for authentication errors
- **Image pull error**: Verify the image tag exists at `ghcr.io/cost-pilot/agent`

## No data appearing in dashboard

1. Check agent logs:
   ```bash
   kubectl logs -n costpilot -l app=costpilot-agent
   ```
2. Confirm the correct region is set (`backend.ingesterEndpoint: eu`)
3. Verify network egress is allowed — the agent needs outbound HTTPS to `ingest.eu.cost-pilot.com`

## Checking agent connectivity

```bash
kubectl exec -n costpilot deploy/costpilot-operator -- \
  wget -qO- https://ingest.eu.cost-pilot.com/health
```

## Helm install fails: chart not found

OCI Helm charts require Helm 3.8+. Check your version:

```bash
helm version
```

Make sure you're using the correct chart name:

```bash
helm upgrade --install costpilot \
  oci://ghcr.io/cost-pilot/helm/agent \
  --namespace costpilot
```

If you're behind a registry mirror that doesn't support OCI, download the chart `.tgz` from the [releases page](https://github.com/cost-pilot/public/releases) and install directly:

```bash
helm upgrade --install costpilot ./agent-0.1.0.tgz \
  --namespace costpilot
```

## Getting diagnostic info

```bash
# All CostPilot resources
kubectl get all -n costpilot

# Events (useful for ImagePullBackOff, CrashLoopBackOff)
kubectl get events -n costpilot --sort-by='.lastTimestamp'

# Current chart values
helm get values costpilot -n costpilot

# Agent version
helm get metadata costpilot -n costpilot | grep appVersion
```

## Still stuck?

Open an issue at [github.com/cost-pilot/public/issues](https://github.com/cost-pilot/public/issues) with the output of the diagnostic commands above.
