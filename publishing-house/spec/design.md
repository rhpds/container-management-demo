# Container Management Demo: OpenShift as a Container Management Platform

## Overview

This presenter-led demo showcases Red Hat OpenShift Container Platform as a comprehensive container management solution, covering the console experience, application deployment patterns, security controls, cluster operations, and observability tooling. It targets sales engineers and technical presenters who need to walk a technical audience through OpenShift's key capabilities on a live Single Node OpenShift cluster. Presenters navigate the OpenShift Console across five chapters, deploying applications via Operators and Helm, demonstrating security policies in action, triggering an over-the-air cluster upgrade, and correlating monitoring and logging signals using the Troubleshooting Panel.

## Target Audience

- **Role:** Sales engineers and technical presenters
- **Experience level:** Beginner (Account SAs, SSPs); low technical depth assumed
- **What they already know:** Basic container and Kubernetes concepts, Red Hat product portfolio at a high level, cluster-admin access on a pre-provisioned SNO cluster
- **What they don't know:** OpenShift-specific console workflows, operator-driven deployments, OCP security primitives (SCCs, RBAC, NetworkPolicy), OTA upgrade flow, LokiStack/Korrel8r integration, and OpenShift Lightspeed AI-assisted troubleshooting

## Prerequisites

- Cluster-admin access on a pre-provisioned Single Node OpenShift 4.20 cluster (cannot be auto-verified)
- OpenShift Lightspeed configured with a valid external LLM provider credential (OpenAI, Azure OpenAI, or IBM watsonx)
- Basic familiarity with containers and Kubernetes concepts
- Custom Helm repositories accessible at mpbravo.github.io (SQL Server and MinIO charts)

## Learning Objectives

1. Demonstrate OpenShift Console capabilities across multiple perspectives, including topology views, the embedded web terminal, the Software Catalog, quick starts, and user management
2. Deploy applications on OpenShift using Operators (Crunchy PostgreSQL), Helm Charts (SQL Server), and existing containerized workloads (Online Boutique)
3. Demonstrate OpenShift security controls by walking through Security Context Constraints, Role-Based Access Control, and network policy enforcement
4. Demonstrate cluster lifecycle operations including a full-stack OTA upgrade, self-healing through continuous reconciliation, and AI-assisted troubleshooting with OpenShift Lightspeed
5. Observe and correlate cluster signals using Prometheus monitoring, LokiStack centralized logging, and the Troubleshooting Panel

## Content Type

Demo (presenter-led)

## Products & Technologies

- Red Hat OpenShift Container Platform 4.20
- Red Hat Enterprise Linux CoreOS (RHCOS)
- Red Hat OpenShift Lightspeed 1.1.1
- Red Hat OpenShift Logging 6.x / Loki Operator 6.x
- Cluster Observability Operator 1.x (Technology Preview)
- Machine Config Operator (MCO)
- Cluster Version Operator (CVO)
- Crunchy Postgres for Kubernetes (OperatorHub-certified, community)
- Microsoft SQL Server 2022 (via Helm)
- Prometheus / Alertmanager / Thanos Querier / Korrel8r
- MinIO (demo-only S3 backend for LokiStack)
- Online Boutique (Google demo application)
- Helm

## Module Map

| Module | Title | Duration |
|--------|-------|----------|
| 1 | Console Overview | ~45 min |
| 2 | Deploying Applications | ~45 min |
| 3 | Secure by Default | ~45 min |
| 4 | Cluster Operations | ~45 min |
| 5 | Observability | ~55 min |
| — | **Total (full demo)** | **~235 min** |
| — | Typical curated session (2–3 chapters) | ~90–120 min |

## Difficulty Level

Beginner

## Environment

**Presenter view:** A pre-provisioned Single Node OpenShift 4.20 cluster with cluster-admin access. The cluster arrives with a default StorageClass, a working update graph via Red Hat OSUS, and OpenShift Lightspeed configured with external LLM provider credentials. During the demo, the presenter installs the Crunchy Postgres, Loki Operator, OpenShift Logging, and Cluster Observability Operators live from OperatorHub. The Online Boutique and SQL Server workloads are deployed from custom Helm repos hosted at mpbravo.github.io. MinIO provides an S3 backend for LokiStack and is also deployed during the demo.

**Automation needed:** Yes — the cluster must be provisioned with the correct OCP version, a default StorageClass, the Red Hat OSUS update graph configured, and Lightspeed LLM credentials injected. Per-chapter operators and workloads are installed live by the presenter.

## Infrastructure Requirements

- **Cloud provider:** CNV (default)
- **Cluster type:** SNO (Single Node OpenShift)
- **OCP version:** 4.20
- **Topology:** Per-student (one cluster per demo session)
- **Sizing:** 1 control plane node (32 vCPU, 128GB RAM, 200GB disk); 0 worker nodes (SNO — control plane serves as worker)
- **Automation approach:** Ansible + GitOps (Helm + ArgoCD)
- **AI/MaaS:** None (OpenShift Lightspeed uses an external SaaS LLM; no on-cluster AI inference required)
- **External services:** None
- **Non-GA products:** Cluster Observability Operator 1.x (Technology Preview — access via standard OperatorHub channel)
