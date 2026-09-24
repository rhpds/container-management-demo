# Container Management Demo — Delivery Guide

## About this document

This delivery guide covers the **Container Management Demo** end to end. Read it thoroughly and complete all preparation **before** presenting to a customer.

> **This demo addresses only the Container Management use case.** It is designed for audiences evaluating OpenShift as a platform to deploy and operate existing containerized workloads (ISV COTS software, containers built outside OpenShift). It is **not** suitable for audiences whose primary interest is application development, build pipelines, or CI/CD — those are Application Platform use cases covered by separate demos.

---

## Before presenting the demo

The demo is modular — **do not present it in full**. Curate 2–3 modules based on what matters to the customer. Before the meeting, gather this information:

1. **Identify the customer's workloads.** What software are they planning to containerize or migrate? This validates that their preferred technologies are supported and certified for OpenShift.
2. **Ask about their container runtime strategy.** Are they evaluating multiple Kubernetes distributions? Do they already run vanilla Kubernetes or a competitor? Are they on a hyperscaler? This determines how much emphasis to place on the "OpenShift vs. vanilla Kubernetes" comparisons throughout the demo.
3. **Gauge the audience's security focus.** Are security team members attending? If security is secondary, shorten Module 3 to just SCCs and skip Network Policies. If security is primary, present Module 3 in full.
4. **Assess observability interest.** Module 5 (Observability) adds ~30 minutes and requires pre-setup. Only include it if the customer has expressed interest in monitoring, logging, or incident management — otherwise, treat it as optional.

---

## Timing and customization guide

The full demo contains approximately **3 hours** of material. Presenters typically curate 2–3 modules for a **90–120 minute** session.

| Module | Full timing | Skip if tight |
|--------|------------|---------------|
| Module 1: Console Overview | ~45 min | Quick Starts (saves ~10 min) |
| Module 2: Deploying Applications | ~40 min | SQL Server or Online Boutique (saves ~15 min) |
| Module 3: Secure by Default | ~35 min | Network Security (saves ~15 min) |
| Module 4: Cluster Operations | ~30 min | Lightspeed (saves ~10 min) |
| Module 5: Observability | ~30 min | Entire module (optional, time-permitting) |

### Recommended 90-minute curated run

| Segment | Time |
|---------|------|
| Opening (business context) | 5 min |
| Module 1 — skip Quick Starts | 35 min |
| Module 2 — all four sections | 40 min |
| Module 3 — SCCs + RBAC only | 20 min |
| Closing (reconnect to outcomes) | 5 min |

### Recommended opening (5 minutes)

Start with the customer outcome, not the technology:

> *"A Container Management Platform gives you a trusted, consistent way to deploy and operate containerized workloads at scale — whether those are ISV products, internal tools, or workloads your team has already containerized elsewhere. Today I'm going to show you what that looks like in practice on a live OpenShift cluster."*

### Recommended closing (5–10 minutes)

- Reconnect every module to the customer's stated problem
- If you showed the OTA upgrade UI in Module 4, this is the moment to trigger the actual upgrade — it runs unattended overnight
- Capture follow-up actions and open questions

---

## Environment and demo preparation

### Provisioning

Provision a **Single Node OpenShift (SNO)** cluster from the Red Hat Demo Platform. The cluster comes with:
- Cluster-admin access
- OpenShift Lightspeed pre-installed and configured against Azure OpenAI Service
- Default StorageClass present and healthy

### Pre-demo setup (allow 30–45 minutes)

Before the audience joins, complete every step in the **Initial Setup** page (`00-initial-setup.adoc`) included in the lab guide. This page covers:

1. **Web Terminal Operator** — install from OperatorHub (required for Module 1)
2. **User Workload Monitoring** — enable via ConfigMap (required for Module 5)
3. **MinIO** — deploy as LokiStack storage backend (required for Module 5)
4. **Logging and Loki Operators** — install from OperatorHub (required for Module 5)
5. **LokiStack** — deploy with `1x.demo` sizing profile (required for Module 5)
6. **Log collector and ClusterLogForwarder** — deploy with correct prerequisites (required for Module 5)
7. **Cluster Observability Operator + UI plugins** — install and enable three UIPlugins (required for Module 5)

> ⚠️ **Do not skip this setup or attempt it live during the demo.** Several steps take minutes to reconcile, and the logging stack must be fully operational before Module 5. Starting the demo without completing this page will cause sections to fail visibly.

### Pre-demo checklist

Run through this immediately before the audience joins:

- [ ] Console is reachable and cluster is healthy (green status on Main Dashboard)
- [ ] Web Terminal icon (>_) visible in the console top bar
- [ ] *(Module 5 only)* `oc get pods -n openshift-user-workload-monitoring` — all pods Running
- [ ] *(Module 5 only)* MinIO pod Running: `oc get pods -n demo-logging-storage`
- [ ] *(Module 5 only)* LokiStack + collector pods Running: `oc get pods -n openshift-logging`
- [ ] *(Module 5 only)* All three UIPlugins Available: `oc get uiplugin`
- [ ] *(Module 5 only)* Console hard-refreshed (Ctrl+Shift+R) after UIPlugin setup
- [ ] *(Module 5 only)* `Observe → Logs` view appears in the console

### Optional customizations

- **Custom logo**: Add the customer's logo to the console — see [Adding a custom logo and product name](https://docs.openshift.com/container-platform/latest/web_console/customizing-the-web-console.html)
- **Dark mode caveat**: The Topology view in dark mode has low-contrast connector lines. Switch to light mode for presentations if the connections are hard to see.

---

## Tips for presenters

This demo is designed for **generalist Solution Architects and Sales Specialists**. You do not need deep OpenShift expertise to deliver it effectively. Here are the key guidelines:

### Focus on outcomes, not mechanics

Your audience cares about *what the platform does for them*, not how it works internally. When you show something, always connect it to one of these four outcomes:

| Outcome | Example talking point |
|---------|----------------------|
| **Time to value** | *"This took 2 minutes. The alternative takes days."* |
| **Risk reduction** | *"This is enforced by the platform. It can't be misconfigured."* |
| **Operational cost** | *"This replaces a manual process / a 3AM page / a separate tool."* |
| **Differentiation** | *"Vanilla Kubernetes can't do this out of the box."* |

### Don't read the YAML

When pasting YAML via Import YAML, say: *"This is a standard Kubernetes resource — the important thing is what it does, not every field in the manifest."* Then immediately show the result in the UI.

### Use failures as opportunities

If something fails live, use it. Say: *"This is a real cluster, not a recording. Let's diagnose this together."* This makes the demo more credible. If Lightspeed is available, ask it to diagnose the issue — that's a natural segue into Module 4.

### If you're asked a question you can't answer

Say: *"That's a great question — let me get the right specialist to follow up."* Don't guess. Note it down and move on.

### The "vanilla Kubernetes" comparison

Use this comparison at least once per module. It's the single most effective differentiator:

> *"On vanilla Kubernetes, you would have to [install/configure/maintain X yourself]. On OpenShift, it's already here."*

---

## Module 1 — Console Overview (~45 min)

### Introduction (say this before opening the console)

> *"While the underlying API of OpenShift is 100% Kubernetes, plain Kubernetes focuses primarily on container orchestration and requires you to manually add and integrate your own solutions for authentication, networking, security, monitoring, and log management. The OpenShift web console bridges this gap — it's a fully integrated, production-ready graphical interface that manages both standard Kubernetes resources and advanced platform-level requirements, out of the box."*

---

### 1.1 The Main Dashboard (~5 min)

**What to show:** Navigate to *Home → Overview* in the Administrator perspective.

Walk through each card and explain what it tells an operations team at a glance:

| Card | What it shows | What to say |
|------|--------------|-------------|
| **Details** | Cluster ID, provider, OpenShift version, update status | *"Version and update status in one place — no separate CMDB needed."* |
| **Status** | Color-coded health (green/warning/critical) | *"Click any sub-component to see exactly where a failure is."* |
| **Cluster Inventory** | Node, pod, PVC, VM counts | *"If Virtualization is active, VMs appear here too — one pane of glass."* |
| **Cluster Utilization** | CPU, memory, storage, network graphs | *"Toggle the time range directly on the card — no Grafana needed."* |
| **Activity** | Live event feed | *"Pod crashes, scale-ups, VM migrations — all in one feed."* |

**Business value:** *"This dashboard is available the moment the cluster is provisioned. On vanilla Kubernetes, you provision Prometheus separately, deploy Grafana on top of it, write your own dashboards, and maintain them as the platform evolves. Here, it's day-1 infrastructure."*

---

### 1.2 The Software Catalog (~10 min)

**What to show:** Navigate to *Ecosystem → Software Catalog*.

Walk through the catalog briefly, highlighting the different filter categories (Operators, Helm Charts, Templates) and the distinction between Certified, Community, and Red Hat Operators.

**Key advantages to mention:**
- **Self-service**: Developers provision software without filing a ticket
- **Guardrails**: Admins control what appears in the catalog
- **No snowflakes**: Every deployment of a given service uses the same validated configuration
- **Broad ecosystem**: Not just Red Hat — ISV, community, and Helm charts all in one place

#### Live demo: Deploy Grafana from OperatorHub

This demonstrates third-party software running inside the cluster within minutes.

1. **Create a project**: *Home → Projects → Create Project* → name it `grafana-demo`
2. **Find the Operator**: *Ecosystem → Software Catalog → OperatorHub* → search `Grafana` → select **Grafana Operator** by Grafana Labs (Community badge) → click *Install* → change namespace to `grafana-demo` → click *Install* again → wait for *Succeeded*
3. **Create a Grafana instance**: Click *Create Instance* from the Operator details page

> ⚠️ **Important:** The default form may show a field labelled "External URL" (`spec.external.url`) marked as required. **Do not fill this field** — it is for connecting to an existing Grafana Cloud instance. Instead, click **YAML view** and replace the content with the minimal spec from the lab guide.

4. **Watch the pod start**: Navigate to *Workloads → Pods* in `grafana-demo`. Watch the pod progress from Pending → ContainerCreating → Running. Click into the pod and show the **Logs** tab — Grafana's startup sequence streams live.

> 💬 *"I didn't write a startup script or configure logging. The Operator packaged all of this. OpenShift is pulling the image, scheduling the pod, and streaming logs through the same interface you use for every workload."*

5. **Access Grafana**: Navigate to *Networking → Routes* → click the URL. Get the admin credentials from *Workloads → Secrets → admin-credentials → Reveal values*. Log in.

> 💬 *"You never wrote a Deployment, a Service, or a Route. The Operator encoded all of that operational knowledge — and OpenShift applied it through the same catalog and API surface it uses for its own components."*

---

### 1.3 The Events Page (~10 min)

**What to show:** Navigate to *Home → Events*.

Explain that events are records of real-time activities and state changes. Cover the filtering options (project scope, resource filter, type filter, search bar) and mention the 3-hour retention limit (events are in etcd; for permanent trails, use API Audit Logging or centralized logging from Module 5).

#### Live demo: Read and trigger events

**Part 1 — Read existing events (passive):**
1. In the project dropdown, select `grafana-demo`
2. The event history from the Grafana deployment is visible: image pull, container creation, pod start

**Part 2 — Trigger live events (active):**
1. Navigate to *Workloads → Pods* in `grafana-demo`
2. Delete the Grafana pod (three-dot menu → *Delete Pod* → confirm)
3. Switch immediately to *Home → Events* (still filtered to `grafana-demo`)
4. Watch events stream live: `Killing` → `Pulling`/`Pulled` → `Created` → `Started`

> 💬 *"I didn't restart anything manually. The platform detected that the desired state was one running pod, and it reconciled back to that state on its own. Every step is visible in the event stream. This same self-healing behaviour applies at cluster scale — we'll see that in Module 4."*

---

### 1.4 The Embedded Web Terminal (~10 min)

**What to show:** Click the terminal icon (>_) in the top-right corner.

Briefly explain the advantages: zero installation, automatic authentication, contextual namespace awareness, accessibility in restricted environments (regulated industries where installing binaries is blocked by IT policy).

#### Live demo: Terminal in action

1. **Open in context**: Navigate to `grafana-demo`, open the terminal, run `oc project` — it already knows you're in `grafana-demo`. No kubeconfig, no context switching.

> 💬 *"The terminal inherited the namespace from wherever you were in the UI."*

2. **Retrieve Grafana admin password**:
```bash
oc get secret grafana-demo-admin-credentials \
  -o jsonpath='{.data.GF_SECURITY_ADMIN_PASSWORD}' | base64 -d
```

> 💬 *"I just retrieved a Kubernetes Secret, decoded it, and printed the value — from a browser tab — without installing anything, without a kubeconfig file, and without leaving the console."*

3. **Exec into the container**:
```bash
oc exec -it $(oc get pod -l app=grafana -o name | head -1) -- /bin/sh
env | grep GF_
exit
```

> 💬 *"Every container on this cluster is inspectable and debuggable from the same browser session. No VPN, no SSH key, no local tools. This is especially powerful in regulated environments where installing binaries on a laptop is blocked by IT policy."*

---

### 1.5 User Management (~10 min)

**What to show:** Navigate to *User Management → Users*.

Briefly cover the four built-in capabilities: Identity Provider onboarding (OAuth wizard), user impersonation, visual RBAC mapping, and group management.

#### Live demo: From zero access to scoped access in 60 seconds

1. **Create a user** (web terminal): `oc create user demouser`
2. **Impersonate before granting access**: *User Management → Users → demouser → Impersonate User*. The project list is empty, most menus are gone.

> 💬 *"This user exists but has no permissions. This is the OpenShift default: zero access until explicitly granted. No implicit trust."*

3. Click *Stop Impersonation*.
4. **Grant scoped access**: Switch to `grafana-demo` project → *User Management → demouser → RoleBindings → Create binding*:
   - Binding type: **Namespace role binding**
   - Name: `demo-view`
   - Role name: **view**
   - Subject: **User**
   - Subject name: `demouser`

> ⚠️ **Important**: Bind to **User**, not Group. The console's impersonation feature does not resolve group memberships — a group-based binding won't be visible during impersonation. In production, groups are the preferred method via LDAP sync.

5. **Impersonate again**: `grafana-demo` now appears, resources are visible but all edit/delete actions are greyed out.

> 💬 *"From zero access to scoped read-only in under a minute, without writing a single YAML file. In production this scales to hundreds of users through LDAP group sync."*

6. Click *Stop Impersonation*.

---

### 1.6 Quick Starts (~10 min)

**What to show:** Click the **?** (Help) icon → *Quick Starts*. Show the catalog of built-in tutorials.

Explain: Quick Starts are interactive, step-by-step tutorials built into the console. They highlight specific UI elements, include verification checks, and can be customized by platform teams to encode internal golden paths.

#### Live demo: Publish a custom Quick Start

1. Click the **+** (Import YAML) button and paste the `ConsoleQuickStart` YAML from the lab guide. Click *Create*.
2. Open **?** → *Quick Starts*. The new "Deploy Grafana from the Ecosystem Catalog" appears instantly — no reload needed.
3. Click it and walk through one task to show the side panel highlighting UI elements.

> 💬 *"A platform team can embed their own institutional knowledge — naming standards, security requirements, approved deployment patterns — directly into the console. Every new user gets the same guided path, without reading a wiki or opening a ticket."*

**Cleanup:** *Administration → CustomResourceDefinitions → ConsoleQuickStart → Instances* → delete `deploy-grafana-community-operator`.

---

## Module 2 — Deploying COTS and Existing Applications (~40 min)

### Introduction (say this before the demos)

> *"OpenShift provides three complementary patterns for deploying third-party software — each optimized for a different level of operational complexity."*

Briefly explain the three patterns:

| Pattern | When to use | Key advantage |
|---------|-------------|---------------|
| **Operators** | Complex, stateful apps (databases, messaging) | Handles Day-2: failover, backup, upgrades |
| **Helm Charts** | Stateless apps, community software | Flexible templating, environment-specific values |
| **Templates** | Internal "golden path" standards | Native to OpenShift, lightweight |

---

### 2.1 Deploying via Operators — Crunchy PostgreSQL (~15 min)

> 💡 **Tip:** If the customer uses a specific database or middleware that has a certified Operator, substitute it for Crunchy PostgreSQL. The point is the Operator pattern, not the specific software.

1. Create a new project for Crunchy
2. *Ecosystem → Software Catalog → OperatorHub* → search `Crunchy` → install into the project namespace
3. Create a **PostgresCluster** instance from the Operator details page (switch to YAML view, paste the HA spec from the lab guide)
4. Show the pods coming up: primary, replicas, pgBouncer proxy, backup repo
5. Deploy **PGAdmin** from the same Operator and access the console via a Route

**Self-healing demo:**
1. Go to *Workloads → Pods*, find the primary pod, delete it
2. Watch the Operator promote a replica to primary and provision a replacement

> 💬 *"In a traditional environment, a database failure means a 3AM call. Here, the Operator detected the loss, promoted a replica, and provisioned a replacement — all automatically, with zero data loss."*

**Scale-on-demand demo:**
1. Edit the PostgresCluster YAML: change `replicas: 2` to `replicas: 3`
2. A new pod spins up and starts replicating automatically

> 💬 *"We just scaled the data layer horizontally in 30 seconds with zero manual configuration."*

---

### 2.2 Deploying via Helm Charts — SQL Server (~10 min)

Explain the Operator vs. Helm distinction:
> *"Operators are the masters of Day-2 operations. Helm is the master of Day-1 packaging. For stateless workloads where Day-2 is just 'restart if it crashes,' Helm is more than enough."*

1. Create a new project for SQL Server
2. Click **+** (Import YAML) and paste the `HelmChartRepository` CR from the lab guide:
```yaml
apiVersion: helm.openshift.io/v1beta1
kind: HelmChartRepository
metadata:
  name: sqlserver-helm-charts
spec:
  connectionConfig:
    url: https://mpbravo.github.io/helm-charts/
```
3. Navigate to *Ecosystem → Software Catalog* → filter by **Helm Charts** → select the SQL Server chart → click *Create*
4. Set the SA password and click *Create*
5. Show the pod running in the Topology view

> 💬 *"From adding a Helm repo to a running SQL Server in under 3 minutes, entirely from the console UI. No `helm` CLI needed."*

---

### 2.3 Deploying Existing Applications — Online Boutique (~10 min)

> *"For workloads that already have container images or Helm charts, OpenShift deploys them directly. The Online Boutique is a realistic 11-service e-commerce app by Google Cloud — we'll use it as a reference workload for the rest of the demo."*

1. Create a project `online-boutique`
2. Add the Helm repository via Import YAML (same pattern as SQL Server, URL: `https://mpbravo.github.io/online-boutique-helm/`)
3. Deploy from the Software Catalog
4. Open *Networking → Routes* and access the storefront

> 💬 *"Eleven microservices, deployed from a single Helm chart, running on OpenShift in under a minute. This application becomes our prop for the Security and Operations modules."*

---

### 2.4 The Topology View (~5 min)

**What to show:** Switch to the *Developer* perspective → *Topology* → select the `online-boutique` project.

The full microservice graph appears: all 11 services grouped into a single "application" card because they share the `app.kubernetes.io/part-of` label.

> 💬 *"This grouping is an OpenShift concept — vanilla Kubernetes has no notion of an 'Application.' Workloads there are isolated objects with no visual or logical grouping."*

#### Live demo: Scale and self-heal from the graph

1. Click `recommendationservice` → *Details* tab → click pencil icon next to Replicas → change to 2 → *Save*. Watch a second pod segment appear in the graph.

> 💬 *"No YAML, no terminal, no kubectl scale. The reconciliation engine is now managing your replicas — and you can see it happen live."*

2. With `recommendationservice` selected → *Resources* tab → three-dot menu on one pod → *Delete Pod* → confirm. The ring grays out, then a new pod appears.

> 💬 *"Continuous reconciliation in action. The platform's desired state says two replicas — the moment reality diverges, OpenShift corrects it. No alert, no runbook, no human intervention."*

3. Click the `frontend` node → *Resources* → click the Route URL to open the storefront.
4. Scale `recommendationservice` back to 1 to keep the environment clean.

---

## Module 3 — Secure by Default (~35 min)

### Introduction

> *"In a cloud-native environment, security isn't a final checklist — it's a continuous, automated layer built into the fabric of the platform. OpenShift is 'secure by default,' and this module shows you what that means in practice."*

---

### 3.1 Security & Compliance — Security Context Constraints (~10 min)

Explain SCCs briefly:
> *"Think of RBAC as controlling who can manipulate API objects. SCCs control what a running pod is allowed to do on the underlying Linux host — can it run as root? Can it access the host filesystem? OpenShift enforces this automatically."*

Mention the key default SCCs: `restricted-v2` (applied by default — non-root, no host access), `anyuid` (allows root UID), `privileged` (total control — infrastructure only).

#### Live demo: Try to run a "naughty" container

1. Create a project `demo-security`
2. Create a Deployment with image `docker.io/httpd:latest`, 1 replica
3. The pod fails — show the logs: `Permission denied: could not bind to address 0.0.0.0:80`

> 💬 *"Apache expects to run as root and bind to port 80. OpenShift said no. The platform enforced its security policy before the container even started. On vanilla Kubernetes, this container would have launched as root with no questions asked."*

4. Change the image to `registry.access.redhat.com/ubi9/httpd-24` — a Red Hat UBI image that runs as non-root on port 8080
5. The pod runs immediately

> 💬 *"Same web server, different image — one follows security best practices, the other doesn't. OpenShift told you which is which before it reached production."*

---

### 3.2 RBAC — The Compromised Account (~10 min)

> ⚠️ **This section builds on Module 1.** `demouser` must already exist with `view` access on `grafana-demo`.

Frame this as a security story, not a configuration walkthrough:

> 💬 *"A developer account has been compromised. The attacker has valid credentials and is logged in right now. Let's follow exactly what they can do on this cluster."*

#### Live demo

1. **Impersonate `demouser`**: *User Management → Users → demouser → Impersonate User*
2. **Attempt lateral movement**: Open the project dropdown — only `grafana-demo` is visible. Everything else is invisible.

> 💬 *"The attacker can't even see other projects exist. There's nothing to enumerate, nothing to pivot to. The blast radius is already contained."*

3. **Attempt privilege escalation**: Click **+** (Import YAML) → paste a `ClusterRoleBinding` granting `demouser` cluster-admin → click *Create*. The API rejects it immediately.

> 💬 *"Privilege escalation blocked. The platform does not trust a user to modify their own permissions. A stolen credential cannot become a cluster-admin credential."*

4. **Attempt destruction**: Navigate to `grafana-demo` → *Workloads → Deployments* → every destructive action (Delete, Edit, Scale) is greyed out.

> 💬 *"Valid credentials. Five minutes of effort. Zero damage. This is what least-privilege enforcement looks like — not as a policy document, but as a technical control the platform enforces automatically."*

5. Click *Stop Impersonation*.

---

### 3.3 Introduction to Network Security (~15 min)

> ⚠️ **This section uses the Online Boutique from Module 2.** It must be running.

Frame the demo:
> 💬 *"I'm not changing the application code. I'm not touching the firewall. I'm going to isolate one microservice with a single policy and show you exactly what that means."*

#### Before you start

Verify the pod labels: navigate to the Online Boutique Topology view → click `cartservice` → *Details* tab → check the Labels field. The standard chart uses `app=cartservice` and `app=frontend`.

#### Live demo

1. **Show the app working**: Open the Online Boutique route → browse to any product → click *Add to Cart*. Everything works.

> 💬 *"Right now every microservice can talk to every other one freely. This is the default. In a real environment, you'd want to lock this down."*

2. **Apply a deny-all policy**: *Networking → NetworkPolicies → Create NetworkPolicy*:
   - Name: `deny-cartservice-ingress`
   - Pod selector: Key=`app`, Value=`cartservice`
   - Policy type: ✅ Deny all ingress traffic

3. **Show the app breaking**: Refresh the Online Boutique → click *Add to Cart*. The cart feature fails.

> 💬 *"One policy. One pod selector. That service is now invisible to the rest of the cluster. No firewall changes, no application restarts, no code changes."*

4. **Add a surgical allow rule**: Create another NetworkPolicy:
   - Name: `allow-frontend-to-cartservice`
   - Pod selector: Key=`app`, Value=`cartservice`
   - Ingress: Allow from pods where Key=`app`, Value=`frontend`

5. **Verify recovery**: Refresh and add to cart — it works again.

> 💬 *"This is zero-trust networking for microservices. The cart service accepts traffic from exactly one source — the frontend — and rejects everything else. If the payment service is compromised, it cannot reach the cart. The default is deny — trust is explicit and narrow."*

**Cleanup:** Delete both policies from *Networking → NetworkPolicies*.

---

## Module 4 — Cluster Operations (~30 min)

### Introduction

> *"Every example in this module makes the same point from a different angle: OpenShift doesn't just get installed — it keeps managing itself. Your operations team spends less time on manual toil than they would maintaining the same capabilities on vanilla Kubernetes."*

---

### 4.1 Full-Stack OTA Upgrades (~10 min)

> ⚠️ **Do NOT trigger the upgrade during the demo.** On a single-node cluster, the MCO reboot takes the entire environment offline for several minutes. Walk through the UI, and leave the actual trigger as the very last action after all modules are complete.

**What to explain:**
- The cluster update covers both OpenShift components AND the underlying RHEL CoreOS operating system — as one supported, versioned operation
- The CVO queries the update graph, applies manifests in Runlevels (each ClusterOperator must report healthy before the next one starts), then the MCO cordons, drains, applies the OS image, reboots, health-checks, and uncordons
- Update channels: Fast (GA immediately), Stable (after regression monitoring), EUS (even-numbered versions for extended support)

**What to show:**
1. Navigate to *Administration → Cluster Settings*
2. Point out: current version, update status, configured channel, available updates
3. Click through to an available update — show the version, release notes link, and *Update* button — **but do not click it**
4. Explain what would happen if you did click it

> 💬 *"On a multi-node cluster this reboot happens one node at a time — true zero-downtime rolling updates. On this single-node environment the reboot is the only interruption, but the automation and safety checks are identical. You're not separately tracking a base OS patch level, a container runtime version, a CNI plugin, and an orchestrator version, hoping they've been tested together. One update graph, one supported path, one rollback story."*

**At the very end of the demo session**, come back to this page and click *Update* — it runs unattended.

---

### 4.2 Self-Healing by Design (~10 min)

Set the context:
> 💬 *"On a vanilla Kubernetes cluster, if you delete a system component — the dashboard, the ingress controller — it stays deleted. Nobody brings it back unless someone runs kubectl apply again. On OpenShift, every platform component is owned by an Operator that watches it continuously."*

#### Example 1: Break a platform component

1. Open the terminal and run:
```bash
oc delete deployment console -n openshift-console
```
2. You may briefly lose the web console — that is expected
3. Within 30–60 seconds, the Console Operator recreates it. Refresh the console — it's back.

> 💬 *"I just deleted the web console you're looking at. The platform brought it back in under a minute, with no manual intervention. This is continuous reconciliation."*

#### Example 2: Destroy an entire application

1. Open the Topology view for the Online Boutique — all pods green
2. In the terminal: `oc delete pods --all -n <online-boutique-namespace>`
3. Watch the Topology view: every node turns grey simultaneously, then cycles back to Running in under a minute

> 💬 *"I just deleted every service in a 10-microservice application. OpenShift rebuilt the entire thing in under a minute. No runbook. No alert. No one on call."*

#### Example 3: Host configuration as code (explain, don't trigger)

Show `oc get machineconfigpool` and `oc get machineconfig | grep rendered` to demonstrate that even OS-level settings (kernel arguments, NTP, SSH keys) are managed declaratively as `MachineConfig` objects — versioned, rendered per node pool, and applied via the same safe cordon-drain-reboot-uncordon mechanics as OTA upgrades.

> ⚠️ On a single-node cluster, applying a MachineConfig triggers a full reboot. **Do not apply it live** unless you're comfortable with the pause.

---

### 4.3 Intelligent Cluster Troubleshooting — OpenShift Lightspeed (~10 min)

**What to explain:**
OpenShift Lightspeed is a generative AI assistant built into the console that diagnoses issues using natural language.

> 💬 *"This is not a chatbot bolted onto the side. It's integrated into the console, it understands Kubernetes and OpenShift resources, and it can read pod logs, events, and resource definitions to diagnose issues."*

#### Example 1: CrashLoopBackOff

1. Create project `demo-lightspeed`
2. Via Import YAML, create a pod that immediately exits: `command: ["sh", "-c", "echo starting && exit 1"]`
3. The pod enters `CrashLoopBackOff`
4. Open Lightspeed and ask: *"My pod bad-app is in CrashLoopBackOff, what's wrong?"*
5. Follow its guidance — it will identify the exit code and suggest a fix

#### Example 2: ImagePullBackOff (using existing Grafana)

No new YAML needed — break the Grafana instance from Module 1:
1. Navigate to `grafana-demo` → *Workloads → Deployments* → Grafana → *Actions → Edit Deployment*
2. Change the image tag from `latest` to `doesnotexist` → *Save*
3. The new pod enters `ImagePullBackOff`
4. Ask Lightspeed: *"My Grafana pod is in ImagePullBackOff, what's wrong?"*
5. Fix it: restore the original image tag and save

#### Example 3: Broken liveness probe

1. Via Import YAML, create a deployment with nginx pointing the liveness probe at port 8080 (nginx listens on port 80)
2. The pod keeps restarting
3. Ask Lightspeed: *"My nginx deployment keeps restarting but the image is fine, why?"*
4. Lightspeed identifies the port mismatch

> 💬 *"Every Kubernetes incident follows a pattern: something is wrong, and the clues are scattered across pod status, events, and logs. Lightspeed collects those clues for you and proposes a fix in natural language — reducing mean time to resolution from minutes to seconds."*

---

## Module 5 — Observability: Logging and Monitoring (~30 min)

### Introduction

> *"Plain Kubernetes leaves you to assemble your own metrics, alerting, and log aggregation stack. OpenShift ships an integrated observability experience out of the box, and its newest capabilities go a step further by correlating signals for you."*

> ⚠️ **All pre-demo setup from the Initial Setup page must be complete before this module.** LokiStack and the Cluster Observability Operator must be running.

---

### 5.1 Monitoring Your Cluster and Applications (~10 min)

**What to explain:**
OpenShift includes a fully integrated monitoring stack based on Prometheus, Alertmanager, and Thanos Querier. It has two layers: core platform monitoring (always on) and User Workload Monitoring (opt-in, lets app teams bring their own metrics without cluster-admin access).

#### Example 1: Built-in dashboards

1. *Observe → Dashboards* → select `Kubernetes / Compute Resources / Cluster`
2. Show CPU, memory, and pod count for the entire environment

> 💬 *"On vanilla Kubernetes there is no built-in dashboard. You provision Prometheus separately, deploy Grafana, write your own dashboards, and maintain them. On OpenShift, this is day-1 infrastructure."*

#### Example 2: PromQL queries

1. *Observe → Metrics*
2. Run: `count(kube_pod_status_phase{phase="Running"}) by (namespace)` — shows running pods per namespace
3. Run: `sum(rate(container_cpu_usage_seconds_total{container!=""}[5m])) by (namespace)` — shows CPU usage per namespace
4. Switch between Table and Graph views

#### Example 3: Custom alert

1. Create project `demo-observability`
2. Via Import YAML, create a `PrometheusRule` with `expr: vector(1) > 0` — a synthetic always-firing alert
3. Wait ~1 minute → *Observe → Alerting* → the `DemoAlwaysFiring` alert appears
4. Click on it → click *Silence Alert* to demonstrate acknowledgement

> 💬 *"Every real alerting rule on this cluster follows the same pipeline. The infrastructure to receive, route, and silence alerts was already here when the cluster was provisioned."*

---

### 5.2 Centralized Logging with LokiStack (~10 min)

**What to explain:**
By default, container logs die with the pod. OpenShift Logging captures them centrally using Vector (collector) and LokiStack (store), queried via LogQL directly from the console — no separate Kibana or Grafana login.

#### Live demo

1. *Observe → Logs* — show the centralized log stream (application, infrastructure, audit)

> 💬 *"On vanilla Kubernetes, there is no built-in log aggregation. You assemble Fluentd, Elasticsearch, and Kibana yourself. This view was available the moment the logging stack was deployed."*

2. Filter to `demo-lightspeed` project → search for `bad-app`. The `echo starting && exit 1` log appears even though that pod was deleted.

> 💬 *"That pod no longer exists. But its log is here. Centralized logging decouples the life of your evidence from the life of your container. In a production incident, this is the difference between having a forensic trail and having nothing."*

3. Clear the namespace filter → search `CrashLoopBackOff` across all namespaces

> 💬 *"One search, one screen, entire cluster. Compare this to running kubectl logs across dozens of namespaces one at a time."*

---

### 5.3 Correlating Signals with the Troubleshooting Panel (~10 min)

**What to explain:**
The Cluster Observability Operator's Troubleshooting Panel (powered by Korrel8r) lets you click a signal and see everything related — alerts, metrics, logs, Kubernetes resources — all scoped to the right time window automatically.

**Set the "before and after" context:**

> 💬 *"Without this feature, correlating an alert to its logs takes multiple manual steps: note the alert name, switch to Logs, write a LogQL query, align the time range, find the relevant lines, go back to metrics to cross-reference. That's 5–10 minutes per incident, and it requires platform knowledge not everyone has. With the Troubleshooting Panel, it takes one click."*

#### Live demo

1. *Observe → Alerting* → open the `DemoAlwaysFiring` alert (or `KubePodCrashLooping` if you recreated the bad-app pod)
2. Click on the alert → open the **Troubleshooting Panel** drawer
3. The panel shows related Pod, Deployment, and a direct link to Logs pre-filtered to the right time range
4. Click the log link — Logs view opens with correct filters already applied

> 💬 *"What used to take three tools, tribal knowledge of label names, and 5–10 minutes of manual correlation now takes one click. At 2AM during a production incident, that difference matters."*

**Wrap-up for Module 5:**

> *"Three tools, one story: Monitoring gives you metrics and alerting from day one. LokiStack gives you logs that outlive the pods that produced them. The Troubleshooting Panel ties them together with one click. Less time switching between tools during an incident means faster resolution and a flatter learning curve."*

---

## Appendix: Additional resources

| Resource | Link |
|----------|------|
| Lab guide (Showroom) | Included in the RHDP environment |
| OpenShift Documentation | https://docs.openshift.com/ |
| OpenShift Security Framework examples | https://github.com/ralvares/openshift-security-framework |
| OpenShift Monitoring documentation | https://docs.openshift.com/container-platform/latest/observability/monitoring/monitoring-overview.html |
| OpenShift Logging documentation | https://docs.openshift.com/container-platform/latest/observability/logging/logging-release-notes.html |
| Korrel8r (Troubleshooting Panel engine) | https://github.com/korrel8r/korrel8r |

### Optional external modules

These are **not** part of this demo's content but may be relevant for follow-up sessions:

- **Advanced Cluster Management (ACM)**: Refer to the [RHACM Demo Script — RHDP](https://demo.redhat.com)
- **Advanced Cluster Security (ACS)**: Refer to the [Red Hat ACS Demo Script](https://demo.redhat.com)

---

## Products and versions

| Product | Version |
|---------|---------|
| Red Hat OpenShift Container Platform | 4.22 |
| Red Hat OpenShift Virtualization | (installed for perspectives) |
| Red Hat Advanced Cluster Management | (installed for perspectives) |
| OpenShift Lightspeed | pre-installed in environment |
| Loki Operator | 6.x |
| Red Hat OpenShift Logging | 6.x |
| Cluster Observability Operator | 1.x (Technology Preview features may apply) |
| MinIO (demo storage for LokiStack) | latest |

---

## Troubleshooting common issues

| Problem | Fix |
|---------|-----|
| **Grafana instance stuck in Pending** | Check namespace selection. When creating the instance, use YAML view and omit `spec.external` entirely. |
| **`demouser` can't see grafana-demo during impersonation** | Bind the `view` role directly to the User subject `demouser`, not to a Group. Console impersonation doesn't resolve group memberships. |
| **Helm Charts filter not visible in the catalog** | Enable the Developer perspective: `oc patch console.operator.openshift.io/cluster --type='merge' -p '{"spec":{"customization":{"perspectives":[{"id":"dev","visibility":{"state":"Enabled"}}]}}}'`. Then hard-refresh. |
| **Network policy demo doesn't behave as expected** | Verify pod labels match the policy selectors (`app=cartservice`, `app=frontend`). |
| **Lightspeed can't answer questions** | Confirm the LLM credentials secret is configured and the Lightspeed Operator reports Available. |
| **Console unavailable after deleting the console Deployment** | Check `oc get clusteroperator console` — a Progressing condition is expected; give it 60–90 seconds. |
| **No custom metrics or alerts** | Confirm User Workload Monitoring is enabled (see Initial Setup). |
| **LokiStack stays in Pending** | Confirm the `logging-loki-s3` secret matches MinIO service name, port, and credentials, and the StorageClass exists. |
| **No collector pods after applying ClusterLogForwarder** | The CLF was applied before its prerequisites. Delete with `oc delete clusterlogforwarder instance -n openshift-logging` and reapply after SA, RBAC, and CA bundle are confirmed. |
| **Chapter 5 components under resource pressure** | LokiStack + MinIO + COO have a significant footprint on SNO. Treat Module 5 as optional if the environment is resource-constrained. |

---

*For questions or feedback about this demo content, contact the content authors through your Red Hat Demo Platform channels. For technical environment issues, reach out to the Red Hat Demo Platform team.*
