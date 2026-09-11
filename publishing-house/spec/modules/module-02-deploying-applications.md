# Module 02 — Deploying Applications

## Brief Overview

This chapter demonstrates three distinct application deployment patterns available on OpenShift, covering the full spectrum from operator-managed stateful services to Helm-based enterprise middleware to lifting and shifting existing containerized applications. The presenter installs Crunchy PostgreSQL from OperatorHub — the most hands-on segment, showing how an operator drives the full lifecycle of a production-grade database cluster. A SQL Server deployment via a custom Helm repository illustrates bring-your-own-chart flexibility. The chapter concludes with deploying the Google Online Boutique microservices demo to show how existing containerized workloads migrate onto OpenShift with minimal change, then uses the resulting multi-service deployment to explore the Topology view — giving the audience a visual map of running workloads and their relationships.

## Audience and Time

- **Audience:** Sales engineers and technical presenters; technical audience watching the demo
- **Estimated duration:** ~45 min

## Learning Objectives

- Deploy a stateful database service using the Crunchy PostgreSQL Operator from OperatorHub, demonstrating operator-managed lifecycle management
- Deploy a Microsoft SQL Server instance on OpenShift using a custom Helm chart repository
- Deploy an existing multi-service containerized application (Online Boutique) on OpenShift to demonstrate workload portability
- Navigate the Topology view to visualise running workloads, their inter-service relationships, and health status

## Lab Structure

| No. | Section | Duration |
|-----|---------|----------|
| 1 | Using Operators — Crunchy PostgreSQL (OperatorHub install + cluster provisioning) | 30 min |
| 2 | Using Helm Charts — SQL Server (custom Helm repo, values override) | 10 min |
| 3 | Deploying existing applications — Online Boutique (import YAML / Helm deploy) | 10 min |
| 4 | The Topology view *(visualising Online Boutique's microservices graph)* | 5 min |
| — | **Chapter total** | **~55 min (content); ~45 min (curated)** |

> Note: The Crunchy PostgreSQL section is the most hands-on portion of the chapter. Operator installation and cluster provisioning can be parallelized by starting the install and moving to the next section while the operator reconciles.

## Key Takeaways

- OperatorHub gives OpenShift users access to a curated catalog of operator-managed services — including community and ISV offerings — without leaving the console
- Operators manage the full lifecycle of stateful workloads (install, scale, backup, upgrade), significantly reducing operational overhead
- Helm chart support is native to OpenShift, enabling teams to reuse existing enterprise charts from any accessible repository
- Existing containerized workloads can be migrated to OpenShift with minimal change, preserving investment in existing Dockerfiles and manifests
- OpenShift supports heterogeneous application portfolios: community databases, commercial middleware, and bespoke microservices coexist on the same platform
- The Topology view gives developers and operators an immediate visual map of running workloads, their inter-service connections, and health state — without writing any extra configuration

## Infrastructure Notes

- Crunchy PostgreSQL requires ~10-15 GiB of PVC storage — a default StorageClass must be present before starting this section
- SQL Server chart is sourced from mpbravo.github.io — network access to this custom Helm repository is required
- Online Boutique is a multi-container Google demo application; its manifests reference public container images
