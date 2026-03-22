# CostPilot Changelog

## 22 March 2026

### Dashboard

- **Added** — GPU cost tracking across accelerator nodes
  GPU costs are now separated from CPU and memory, letting you track ML infrastructure spend per team and identify underutilised GPUs.
- **Added** — Spend velocity with percentile bands
  The Spend page now shows burn rate at hourly, daily, and monthly resolution with P50/P95 percentile bands for trend analysis.
- **Added** — Budget rules with team-level scoping
  Set monthly spend limits per team label — not just per cluster or namespace — with forecasting and threshold alerts.
- **Improved** — Shared cost allocation now supports five strategies
  Choose how cluster overhead is distributed: hidden, separate line item, proportional by spend, even split, or CPU-weighted.
- **Improved** — Week-over-week namespace cost movers on the dashboard
  The main dashboard now highlights which namespaces saw the largest cost changes compared to the previous week.
- **Fixed** — Cost Explorer no longer resets filters when switching between trend and composition views

### API

- **Added** — MCP server for AI assistant integration
  Query your Kubernetes costs from Claude, Cursor, Windsurf, or any MCP-compatible client using natural language.
- **Added** — Public read-only API with eight endpoints
  Programmatic access to costs, trends, insights, clusters, and burn rate data. Bearer token authentication with per-minute and monthly rate limits.

### Agent

- **Added** — VPA recommendation collection
  When a Vertical Pod Autoscaler is installed, the agent now forwards per-container CPU and memory targets alongside regular metrics.
- **Improved** — Cloud provider auto-detection expanded to nine providers
  The agent now automatically identifies AWS, GCP, Azure, DigitalOcean, Hetzner, Scaleway, k3s-on-cloud, kind, and minikube clusters from node metadata.

### Helm / Kustomize

- **Added** — Kustomize base and production overlay examples
  Deploy CostPilot without Helm using the new Kustomize manifests with configurable resource patches.

### Platform

- **Added** — Eight alert delivery channels
  Route cost alerts to Slack, Microsoft Teams, PagerDuty, Datadog, AlertManager, Jira, Grafana, or custom webhooks with HMAC signing.
- **Added** — Real-time SSE notifications
  Live dashboard notifications via server-sent events with 50-event replay on reconnect.

---

## 19 March 2026

### Dashboard

- **Added** — Cost Explorer with command and visual builder modes
  Query costs across any dimension — namespace, workload, label, instance type, region — with full drill-down, breadcrumb navigation, and shareable URLs.
- **Added** — Attribution page with health indicators
  Group costs by any Kubernetes label or dimension. Coverage %, top group concentration, new group detection, and outlier warnings surface chargeback data quality issues.
- **Added** — Insights dashboard with 50+ automated checks
  Seven free analysers (idle, efficiency, node health, reliability, spot fleet, QoS, fleet composition) run for all tenants. Paid plans unlock 13 additional analysers covering cloud optimisation, temporal patterns, VPA, governance, and more.
- **Added** — Signals page for cost anomaly and spike detection
  Statistical anomaly detection using z-score baselines and spike detection with percentage thresholds — no manual configuration required.
- **Added** — Infrastructure fleet views
  Node heatmaps, lifecycle tracking, fleet composition breakdowns, and per-node cost and utilisation data across all connected clusters.

### API

- **Added** — Five alert rule types
  Cost spike, budget threshold, metric threshold, anomaly detection, and idle resource rules — evaluated every five minutes against live data.
- **Added** — Insight lifecycle management
  Fingerprint-based reconciliation prevents duplicate insights. Status tracking through active, acknowledged, dismissed, resolving, and auto-resolved states with full audit history.
- **Added** — Label optimisation engine
  Automatic cardinality analysis across your fleet. Pin high-signal labels, suppress noise, and map Kubernetes labels to business dimensions like team, environment, and cost centre.

### Agent

- **Fixed** — mTLS common name validation during certificate handshake
  Some clusters with non-standard certificate common names were failing authentication.
- **Fixed** — Missing RBAC permissions for VerticalPodAutoscaler resources

### Platform

- **Added** — Stripe billing with four plan tiers
  Basic (free), Starter (EUR 29/mo), Pro (EUR 79/mo), and Max (EUR 199/mo) with 14-day free trials on paid plans — no credit card required.
- **Improved** — CI pipelines migrated to GitHub Actions
  All repositories now use GitHub Actions for builds, tests, and releases.

---

## 16 March 2026

### Dashboard

- **Added** — Real-time cost visibility dashboard
  See total cost with CPU, memory, storage, and idle breakdowns. Efficiency grading (A–F), namespace cost tables, and budget tracking — all updating as metrics arrive.
- **Added** — Demo data mode for evaluation
  A fictional demo cluster with sample cost data lets you explore every feature before connecting a real cluster. Toggle on in Settings.
- **Added** — Multi-currency display
  View all costs in GBP, EUR, USD, or eight other currencies with configurable exchange rates.

### API

- **Added** — gRPC services with Connect-RPC
  Four service groups — Metrics, Analysis, Identity, and Management — accessible over HTTP/JSON without gRPC tooling.
- **Added** — ClickHouse-backed cost storage with multi-resolution aggregation
  Raw 1-minute metrics aggregate to hourly and daily summaries automatically. Query result caching with 5-minute TTL keeps repeated queries fast.
- **Added** — Cost allocation model
  Pod-level attribution based on resource requests with configurable CPU/memory weighting. Idle cost split into overprovisioned (pod-level waste) and unallocated (node-level spare capacity).

### Agent

- **Added** — Kubernetes metrics collector with operator-managed lifecycle
  A single Helm command deploys the operator, which creates and manages a 3-replica agent with automatic leader election. Only the leader collects; followers stand by for failover.
- **Added** — Per-pod and per-node metric collection at configurable intervals
  CPU, memory, storage, labels, QoS class, ownership chain, and node capacity — collected every 60 seconds by default with 45-second timeout protection.
- **Added** — Circuit breaker and buffered shipping
  Metrics buffer up to 8,000 pod records before flushing. Circuit breaker with exponential backoff (2 min to 30 min) prevents cascading failures if the ingester is unavailable.

### Helm / Kustomize

- **Added** — OCI-hosted Helm chart
  Install with `helm upgrade --install costpilot oci://ghcr.io/cost-pilot/helm/agent`. Full values reference with configurable replicas, collection interval, resource limits, and backend region.

### Platform

- **Added** — CostPilot platform launch
  Six repositories under the cost-pilot GitHub organisation: proto, server, workers, agent, iac, and public.
- **Added** — Clerk-based authentication with role-based access
  Four roles — Owner, Admin, Member, Viewer — with email-based invitations, pending status tracking, and session management.

### Docs

- **Added** — Full documentation site at docs.cost-pilot.com
  Getting started guides, feature walkthroughs, cloud provider configuration for seven providers, how-to guides, architecture concepts, and API reference.
- **Added** — Deployment examples for ArgoCD, Flux, and Kustomize
  Production-ready manifests for GitOps workflows alongside the standard Helm installation.
