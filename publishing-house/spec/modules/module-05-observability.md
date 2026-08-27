# Module 05 — Observability

## Brief Overview

This chapter demonstrates OpenShift's integrated observability stack, covering metrics, logs, and signal correlation. The presenter begins with Prometheus-based monitoring — exploring the built-in dashboards, alerting rules, and Thanos Querier for long-term metric retention. The chapter's centerpiece is a CLI-intensive LokiStack deployment, where the presenter installs the Loki Operator and OpenShift Logging, configures MinIO as an S3 backend, and queries aggregated logs across the cluster. The chapter closes with the Troubleshooting Panel (powered by Korrel8r and the Cluster Observability Operator), which correlates metrics, logs, and events into a unified view to accelerate root-cause analysis.

## Audience and Time

- **Audience:** Sales engineers and technical presenters; technical audience watching the demo
- **Estimated duration:** ~55 min
- **Note:** The LokiStack section is the most CLI-intensive portion of the entire demo. Presenters should be comfortable with `oc` commands and YAML editing.

## Learning Objectives

- Monitor cluster and application health using the built-in Prometheus dashboards, Alertmanager, and Thanos Querier
- Deploy and configure LokiStack with MinIO as an S3 backend using the Loki Operator and OpenShift Logging Operator, and query aggregated logs via the console
- Observe and correlate metrics, logs, and Kubernetes events using the Troubleshooting Panel (Korrel8r) to demonstrate unified signal navigation

## Lab Structure

| Section | Duration |
|---------|----------|
| Monitoring with Prometheus (dashboards, alerts, Thanos Querier) | 20 min |
| Centralized logging with LokiStack (Loki Operator install, MinIO S3 config, log queries) | 30 min |
| Correlating signals with the Troubleshooting Panel (Korrel8r, Cluster Observability Operator) | 15 min |
| **Chapter total** | **~65 min (content); ~55 min (curated)** |

> Note: The chapter contains ~65 min of material. The ~55 min curated duration reflects a typical presenter's pacing with pre-staged LokiStack components.

## Key Takeaways

- OpenShift ships with a production-grade Prometheus stack pre-integrated — no additional monitoring infrastructure is required to get cluster-wide and application-level metrics
- LokiStack on OpenShift delivers centralized log aggregation with multi-tenant isolation, replacing the need for external ELK/EFK stacks
- MinIO provides a cost-effective, on-cluster S3 backend for LokiStack in demo and development environments, keeping the observability story self-contained
- The Troubleshooting Panel (Korrel8r) eliminates context switching between monitoring, logging, and events interfaces — a single signal can traverse all three data sources in one workflow
- The Cluster Observability Operator (Technology Preview) represents the direction of OpenShift's observability roadmap, providing a single operator for managing the full signals stack

## Infrastructure Notes

- Loki Operator and OpenShift Logging Operator must be installed from OperatorHub before this section; both are installed live during the demo
- MinIO requires a PVC (~10 GiB) backed by the default StorageClass — confirm StorageClass availability before starting this chapter
- Cluster Observability Operator 1.x is Technology Preview; the presenter should acknowledge this status when discussing the Troubleshooting Panel
- The LokiStack section is CLI-intensive: have the embedded web terminal or a local `oc` session ready
