---
name: Bug report
about: Something not working as expected
labels: bug
---

**Agent version**
<!-- helm get metadata costpilot -n costpilot | grep appVersion -->

**Kubernetes version**
<!-- kubectl version -->

**Install method**
<!-- Helm / ArgoCD / Flux / Kustomize / kubectl apply -->

**What happened**

**What you expected to happen**

**Diagnostic output**
<!-- Please include output of:
kubectl get all -n costpilot
kubectl get events -n costpilot --sort-by='.lastTimestamp'
kubectl logs -n costpilot -l app=cost-pilot-operator --tail=50
kubectl logs -n costpilot -l app=cost-pilot-agent --tail=50
-->

```
(paste here)
```
