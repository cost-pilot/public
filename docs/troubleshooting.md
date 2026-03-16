# Troubleshooting

## Agent pods not starting

Check operator logs first — the operator creates and manages agent pods:

```bash
kubectl logs -n costpilot -l app=cost-pilot-operator
```

Common causes:
- **Secret missing**: Ensure `cp-agent-secret` exists with key `cluster-api-key`
- **Invalid API key**: Check the operator logs for authentication errors
- **Image pull error**: Verify the image tag exists at `ghcr.io/cost-pilot/agent`

## No data appearing in dashboard

1. Check agent logs:
   ```bash
   kubectl logs -n costpilot -l app=cost-pilot-agent
   ```
2. Confirm the correct region is set (`backend.ingesterEndpoint: eu` or `us`)
3. Verify network egress is allowed — the agent needs outbound HTTPS to `ingest.eu.costpilot.io` (or `ingest.us.costpilot.io`)

## Checking agent connectivity

```bash
kubectl exec -n costpilot deploy/cost-pilot-operator -- \
  wget -qO- https://ingest.eu.costpilot.io/health
```

## Helm install fails: chart not found

OCI Helm charts require Helm 3.8+. Check your version:

```bash
helm version
```

If you're behind a registry mirror that doesn't support OCI, contact support for an alternative install method.

## Getting diagnostic info

```bash
# All CostPilot resources
kubectl get all -n costpilot

# Events (useful for ImagePullBackOff, CrashLoopBackOff)
kubectl get events -n costpilot --sort-by='.lastTimestamp'

# Current chart values
helm get values costpilot -n costpilot
```

## Still stuck?

Open an issue at [github.com/cost-pilot/public/issues](https://github.com/cost-pilot/public/issues) with the output of the diagnostic commands above.
