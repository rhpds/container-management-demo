# Module 04 — Cluster Operations

## Brief Overview

This chapter focuses on OpenShift's day-2 operational story: keeping clusters up to date, self-healing, and intelligently diagnosable. The presenter triggers a full-stack over-the-air upgrade — covering both the control plane (via CVO) and node operating systems (via MCO and RHCOS) — to illustrate how OpenShift manages the entire stack as a versioned unit. A self-healing demonstration shows the cluster's continuous reconciliation loop recovering from a simulated configuration drift. The chapter closes with OpenShift Lightspeed, the AI-powered troubleshooting assistant, demonstrating how natural-language queries against live cluster state accelerate root-cause analysis.

## Audience and Time

- **Audience:** Sales engineers and technical presenters; technical audience watching the demo
- **Estimated duration:** ~45 min

## Learning Objectives

- Demonstrate a full-stack OTA upgrade using the Cluster Version Operator (CVO) and Machine Config Operator (MCO), showing how OpenShift manages control plane and node OS updates as a single versioned operation
- Demonstrate cluster self-healing through continuous reconciliation by simulating a configuration drift and observing automatic recovery
- Demonstrate AI-assisted troubleshooting with OpenShift Lightspeed by submitting natural-language queries against live cluster state

## Lab Structure

| No. | Section | Duration |
|-----|---------|----------|
| 1 | Full-Stack OTA Upgrades (CVO + MCO + RHCOS) | 15 min |
| 2 | Self-Healing by Design — Continuous Reconciliation (simulated drift + recovery) | 15 min |
| 3 | Intelligent Cluster Troubleshooting — OpenShift Lightspeed (NL query demo) | 20 min |
| — | **Chapter total** | **~50 min (content); ~45 min (curated)** |

## Key Takeaways

- OpenShift manages the full cluster stack — OS, control plane, and operators — as a single versioned unit, dramatically simplifying the upgrade lifecycle compared to DIY Kubernetes
- The CVO enforces a declarative desired-state model for cluster configuration, ensuring cluster components remain consistent over time
- RHCOS immutability and MCO-managed day-2 configuration make nodes self-repairing against drift, reducing toil for platform teams
- Continuous reconciliation means infrastructure and cluster-level configuration cannot drift silently — OpenShift detects and corrects it automatically
- OpenShift Lightspeed bridges the gap between raw cluster data and actionable insight, allowing presenters to answer audience questions in real time using natural language

## Infrastructure Notes

- OTA upgrade requires network access to the Red Hat OSUS update graph endpoint
- OpenShift Lightspeed requires a pre-configured external LLM provider secret (OpenAI, Azure OpenAI, or IBM watsonx); no on-cluster GPU is needed
- The self-healing demo typically involves modifying a MachineConfig or deleting a managed resource and observing MCO/CVO reconciliation — confirm the specific scenario in the presenter guide
