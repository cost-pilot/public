---
name: Bug report
about: Something not working as expected
labels: bug
---

**Agent version**
<!-- helm get metadata costpilot -n costpilot | grep AppVersion -->

**Kubernetes version**
<!-- kubectl version --short -->

**Install method**
<!-- Helm / ArgoCD / Flux / kustomize -->

**What happened**

**What you expected to happen**

**Diagnostic output**
<!-- Please include output of:
kubectl get all -n costpilot
kubectl get events -n costpilot --sort-by='.lastTimestamp'
kubectl logs -n costpilot -l app=cost-pilot-operator --tail=50
-->

```
(paste here)
```
