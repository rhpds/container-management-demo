# Module 03 — Secure by Default

## Brief Overview

This chapter demonstrates OpenShift's layered security model, which enforces safe defaults at the pod, user, and network levels without requiring additional tooling. The presenter covers Security Context Constraints — OpenShift's mechanism for controlling what a container is allowed to do at the kernel level — then walks through Role-Based Access Control to show how fine-grained permissions are applied to users and service accounts across namespaces. The chapter closes with a network security introduction using NetworkPolicy to show east-west traffic restriction within the cluster. Together, the three sections build the narrative that OpenShift ships with enterprise-grade security out of the box.

## Audience and Time

- **Audience:** Sales engineers and technical presenters; technical audience watching the demo
- **Estimated duration:** ~45 min

## Learning Objectives

- Demonstrate how Security Context Constraints (SCCs) restrict container privilege escalation and enforce kernel-level security boundaries
- Demonstrate Role-Based Access Control (RBAC) by creating and binding roles to users and service accounts within an OpenShift project
- Secure east-west cluster traffic by demonstrating NetworkPolicy rules that isolate application namespaces

## Lab Structure

| Section | Duration |
|---------|----------|
| Security and Compliance / Security Context Constraints (SCCs) | 15 min |
| Role-Based Access Control (RBAC) | 15 min |
| Introduction to network security (NetworkPolicy) | 20 min |
| **Chapter total** | **~50 min (content); ~45 min (curated)** |

## Key Takeaways

- OpenShift enforces a restrictive security posture by default — containers do not run as root unless explicitly permitted, which reduces attack surface without custom configuration
- SCCs provide a Kubernetes-native mechanism for expressing compliance requirements at the platform level, making it easier to pass security audits
- RBAC in OpenShift is namespace-scoped and integrable with enterprise identity providers, supporting least-privilege access models
- NetworkPolicy allows teams to implement micro-segmentation within the cluster, preventing lateral movement between application tiers
- The "secure by default" philosophy means development teams inherit security guardrails without needing deep security expertise
