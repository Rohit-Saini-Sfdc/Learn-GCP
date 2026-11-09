# GCP Learning Notes

---

# Day 01: Cloud Computing Fundamentals & Google Cloud Platform (GCP)

## 1. Cloud Computing Core Concept
- **Definition**: On-demand delivery of computing power, database storage, applications, and other IT resources via the internet with pay-as-you-go pricing.
- **Financial Shift**:
  - **Traditional (CapEx - Capital Expenditure)**: Large upfront investment in physical hardware, data center space, power, and maintenance before writing any code.
  - **Cloud (OpEx - Operational Expenditure)**: Zero upfront hardware costs; treat infrastructure as operational cost, scaling spend strictly with actual usage.

---

## 2. Traditional IT vs. Cloud

| Feature | Traditional On-Premise IT | Cloud Computing (GCP) |
| :--- | :--- | :--- |
| **Procurement Time** | Weeks to months (hardware ordering, racking, cabling) | Minutes / Seconds via APIs, UI, or IaC |
| **Capacity Planning** | Provisioned for estimated peak traffic (idle waste) | Dynamic auto-scaling based on real-time traffic |
| **Maintenance** | In-house team handles physical hardware, cooling, power | Handled entirely by the cloud provider (Google) |
| **Cost Model** | Fixed CapEx + ongoing datacenter upkeep | Variable OpEx (Pay-as-you-go / Per-second billing) |
| **Global Reach** | Expensive and slow to replicate across regions | Multi-region deployment available globally in minutes |

---

## 3. Cloud Deployment Models
- **Public Cloud**: Shared infrastructure owned and operated by a third-party provider (e.g., GCP, AWS, Azure). Multi-tenant environment partitioned securely.
- **Private Cloud**: Cloud infrastructure dedicated solely to one organization, hosted on-premises or by a dedicated provider. Maximum control and data sovereignty.
- **Hybrid Cloud**: Combines private on-premise infrastructure and public cloud services, connected via secure tunnels (e.g., Cloud Interconnect, VPN).
- **Multi-Cloud**: Using services from multiple public cloud providers (e.g., GCP + AWS) to avoid vendor lock-in and optimize costs/features.

---

## 4. Cloud Service Models (Shared Responsibility)

| Model | What Google Manages | What You Manage | GCP Examples |
| :--- | :--- | :--- | :--- |
| **IaaS** (Infrastructure as a Service) | Data center, networking, physical hardware, virtualization/hypervisor | OS, runtime, middleware, data, application logic | Compute Engine (GCE), Persistent Disk |
| **PaaS** (Platform as a Service) | Hardware, OS, runtime environment, auto-scaling, patching | Application code, configuration, data | App Engine, Cloud Run |
| **SaaS** (Software as a Service) | Entire stack (hardware to software maintenance) | User access, configuration, data consumption | Google Workspace, BigQuery (Serverless Analytics) |

---

## 5. Essential Architecture Vocabulary (Interview Definitions)

- **Virtualization**: Abstraction of physical hardware using software (Hypervisor) to run multiple isolated virtual machines (VMs) on a single physical host.
- **Region**: A specific geographic area (e.g., `us-central1`, `asia-south1`) containing three or more zones.
- **Availability Zone (AZ)**: An isolated deployment area within a region equipped with independent power, cooling, and network links to prevent single-point failures.
- **Scalability vs. Elasticity**:
  - *Scalability*: Ability to handle growth (Vertical = scale up/larger machine; Horizontal = scale out/more instances).
  - *Elasticity*: Ability to dynamically scale out during demand spikes and automatically scale in when load drops to minimize cost.
- **High Availability (HA)**: Architecture design (typically across multiple zones/regions) ensuring system accessibility with minimum 99.9%–99.999% uptime.
- **Fault Tolerance**: Ability of a system to continue functioning without interruption despite hardware or component failures.
- **Load Balancing**: Distributing incoming traffic across healthy backend instances to maximize throughput and prevent bottlenecks (e.g., Cloud Load Balancing).

---

## 6. Why GCP? (Differentiators)
- **Global Private Network**: Google routes public traffic onto its own high-speed, low-latency private fiber backbone at the nearest point of presence (PoP).
- **Kubernetes & Container Origin**: Kubernetes originated from Google's internal cluster management system (Borg); Google Kubernetes Engine (GKE) is industry-leading.
- **Data Analytics & AI/ML**: Native powerhouses such as BigQuery, Vertex AI, and Tensor Processing Units (TPUs).
- **Cost Innovations**: Per-second billing, Sustained Use Discounts (automatic price drops for steady workloads without long-term contracts), and Custom Machine Types.

---

## 7. GCP Management & CLI Essentials
- **GCP Console**: Web-based GUI (`https://console.cloud.google.com`) for managing projects, IAM, billing, and services.
- **Cloud Shell**:
  - Free, browser-based Linux terminal (Debian VM).
  - Comes with 5GB persistent `$HOME` storage.
  - Pre-installed with `gcloud`, `kubectl`, `terraform`, Docker, and common language runtimes.
  - Automatically authenticated with current GCP user session.
- **Basic `gcloud` Commands**:
  ```bash
  # Check authenticated user accounts
  gcloud auth list

  # Display current active project and configuration settings
  gcloud config list

  # Set active project
  gcloud config set project <PROJECT_ID>

  # Set default compute region/zone
  gcloud config set compute/region us-central1
  gcloud config set compute/zone us-central1-a
  ```

---

## 8. High-Yield Interview Q&A

**Q: What is the difference between a Region and a Zone in GCP?**  
> **A:** A region is an independent geographic area with low latency communication. A zone is an isolated failure domain within a region. Each region has 3 or more zones connected through high-bandwidth, low-latency networks. Multi-zone deployments safeguard against datacenter outages.

**Q: Explain CapEx vs. OpEx in cloud adoption.**  
> **A:** CapEx requires heavy upfront capital investment for hardware with fixed capacity and continuous depreciation. OpEx converts infrastructure costs to variable operational expenses where companies only pay for consumed resources, drastically reducing financial risk and time-to-market.

**Q: What is the difference between Scalability and Elasticity?**  
> **A:** Scalability is the capacity of the system to handle increasing load by adding resources. Elasticity is the real-time automation of provisioning and de-provisioning resources dynamically to mirror fluctuations in demand.


---

# Day 02: GCP Resource Hierarchy, Projects & Cost Management

## 1. GCP Resource Hierarchy

GCP organizes resources hierarchically into four primary levels:

```
Organization (Domain root: example.com)
   └── Folders (Departments / Environments: Engineering, Prod, Dev)
         └── Projects (Base administrative unit: billing, APIs, IAM)
               └── Resources (Compute Engine, Cloud Storage, Cloud SQL, etc.)
```

### Hierarchy Breakdown:
1. **Organization**:
   - The root node of the GCP hierarchy, tied directly to a Google Workspace or Cloud Identity domain.
   - Provides central visibility and policy governance (Organization Policies, IAM access across the enterprise).
   - Super Admin manages organization-level roles.
2. **Folders**:
   - Logical containers beneath an Organization used to group multiple Projects or child Folders.
   - Ideal for structuring by **department** (e.g., Engineering, Marketing) or **environment** (e.g., Development, Staging, Production).
   - Supports nesting up to 300 levels deep.
3. **Projects**:
   - The fundamental operational and administrative unit in GCP.
   - **Every resource must belong to exactly one project.**
   - Projects serve as the boundary for:
     - Billing association (linked to a billing account).
     - IAM access control and role assignments.
     - Enabled APIs and service quotas.
4. **Resources**:
   - The actual cloud assets provisioned (e.g., GCE VMs, GCS buckets, BigQuery tables).

### Policy & IAM Inheritance Rule:
- **Top-Down Inheritance**: Policies and IAM permissions defined at a parent node (Organization or Folder) automatically propagate down to all child nodes.
- **Union of Permissions**: Permissions are additive. If access is granted at the Folder level, it cannot be revoked at the Project level. A child cannot restrict inherited permissions (unless using explicit Deny policies or specific Org Policy constraints).

---

## 2. Project Identifiers (Crucial Interview Detail)

Each GCP project has three distinct identifiers:

| Identifier | Characteristics | Mutability | Uniqueness Scope | Typical Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **Project Name** | User-defined, friendly display name | Mutable (can change anytime) | Does **not** need to be unique | Human recognition on GCP Console |
| **Project ID** | User-chosen or system-suggested alphanumeric string | **Immutable** (cannot be changed after creation) | **Globally unique** across all of GCP | `gcloud` CLI, Terraform configs, APIs |
| **Project Number** | Google-assigned unique integer (e.g., `482910481234`) | **Immutable** (assigned by GCP) | **Globally unique** | Internal system integrations, default service accounts (`<num>@cloudbuild.gserviceaccount.com`) |

---

## 3. GCP Billing Accounts

- **Decoupled Architecture**: Billing Accounts exist independently of Projects.
- **Relationship Model**:
  - **One-to-Many**: One Billing Account can pay for multiple Projects.
  - **Many-to-One is NOT allowed**: A single Project can be linked to **only one** Billing Account at any given moment.
- **Billing Account Types**:
  - *Self-serve (Invoiced/Credit Card)*: Paid via credit/debit card or auto-debit.
  - *Invoiced (Offline / Enterprise)*: Monthly invoices paid via wire transfer (minimum spend thresholds apply).
- **Disabling Billing**: Unlinking or closing the billing account immediately suspends active paid services (e.g., VMs stop running) to prevent incurring charges.

---

## 4. Budgets & Budget Alerts

- **Purpose**: Cost tracking mechanism to prevent unexpected cloud bills by establishing monthly, quarterly, or annual spending limits.
- **Threshold Rules**: Configurable percentage thresholds (e.g., 50%, 90%, 100% of actual or forecasted spend).
- **Default Behavior (Critical Interview Trap)**:
  - Budget alerts are **informational only** by default (via Email notifications to Billing Admins/Users and Cloud Monitoring alerts).
  - **Budget alerts DO NOT automatically shut down, stop, or delete resources.**
- **Enforcing Hard Cost Caps (Automated Shutdown)**:
  - To stop resources when budget is exceeded, implement **Programmatic Budget Notifications**:
    1. Connect the Budget to a **Cloud Pub/Sub** topic.
    2. Trigger a **Cloud Function** / **Cloud Run** service from Pub/Sub messages.
    3. The function invokes the **Cloud Billing API** to programmatically disable billing on the project, or uses Compute Engine APIs to shut down instances.

---

## 5. GCP APIs & Services Architecture

- **Disabled by Default**: In a newly created project, nearly all service APIs (Compute, Storage, Kubernetes, etc.) are disabled by default following the principle of least privilege.
- **Per-Project Activation**: APIs must be explicitly enabled inside each project before resources can be provisioned.
- **Service Usage API**: Governs the enabling, disabling, and monitoring of Google APIs.

---

## 6. Essential CLI Commands (Project, Billing & APIs)

```bash
# --- Project Operations ---
# List all accessible projects
gcloud projects list

# Create a new project
gcloud projects create my-gcp-hero-proj --name="GCP Zero to Hero"

# Switch active working project
gcloud config set project my-gcp-hero-proj

# View project metadata (Number, ID, State)
gcloud projects describe my-gcp-hero-proj

# --- Billing Operations ---
# List linked billing accounts
gcloud billing accounts list

# Link a project to a billing account
gcloud billing projects link my-gcp-hero-proj --billing-account=012345-6789AB-CDEF01

# Check billing status of a project
gcloud billing projects describe my-gcp-hero-proj

# --- API Management ---
# List currently enabled APIs in the active project
gcloud services list --enabled

# Search if a specific API is available
gcloud services list --available | grep compute

# Enable core infrastructure APIs simultaneously
gcloud services enable \
    compute.googleapis.com \
    storage.googleapis.com \
    monitoring.googleapis.com \
    logging.googleapis.com \
    cloudresourcemanager.googleapis.com \
    iamcredentials.googleapis.com
```

---

## 7. High-Yield Interview Q&A

**Q: Can you restrict a child folder from inheriting an IAM role assigned at the Organization level?**  
> **A:** No. IAM permissions in GCP are inherited downwards and are strictly additive (union of permissions). If a user is granted `Editor` at the Organization level, they retain `Editor` rights on all folders and projects underneath. To restrict permissions, follow least privilege by granting roles at the lowest required level in the hierarchy, or use GCP Organization Policy constraints / IAM Deny policies.

**Q: If your GCP project exceeds 100% of its budget alert, will your virtual machines automatically shut down?**  
> **A:** No. By default, GCP budget alerts only send notification emails and Cloud Monitoring alerts. They do not stop or destroy running infrastructure. An automated shutdown requires setting up programmatic notifications via Cloud Pub/Sub and Cloud Functions to disable project billing or terminate VMs.

**Q: What is the difference between Project ID and Project Number?**  
> **A:** The Project ID is a user-chosen, globally unique alphanumeric identifier used in CLI scripts, URLs, and Terraform configurations. The Project Number is an immutable, Google-generated numeric ID assigned automatically at creation, primarily used internally and in standard service account identities.


---

# Day 03: GCP IAM (Identity & Access Management) Fundamentals

## 1. Core Concept & The IAM Equation

IAM defines access control in Google Cloud using a single fundamental formula:

$$\text{Who (Identity)} \xrightarrow{\text{can do What (Role)}} \text{on Which Resource (Scope)}$$

- **Permissions are never assigned directly to identities.**
- **Permissions** are grouped into **Roles**.
- **Roles** are assigned to **Identities** via **IAM Policies (Bindings)** applied at a specific resource hierarchy level.

---

## 2. IAM Identities ("Who")

| Identity Type | Format / Prefix | Description & Use Case |
| :--- | :--- | :--- |
| **Google Account** | `user:name@gmail.com` | Individual human identity (developers, admins). |
| **Google Group** | `group:devops-team@company.com` | Collection of Google accounts. **Best practice** for scaling user access management. |
| **Service Account** | `serviceAccount:app@proj.iam.gserviceaccount.com` | Non-human machine identity for applications, CI/CD pipelines, and VMs. |
| **Google Workspace / Cloud Identity** | `domain:company.com` | All users within an entire registered corporate domain. |
| **Public Special Identifiers** | `allUsers` (anyone on internet) / `allAuthenticatedUsers` (any valid Google account) | Used for public static web hosting or public datasets. High security risk if misconfigured. |

---

## 3. Types of IAM Roles ("What")

### 1. Basic (Primitive) Roles (Legacy - Avoid in Production)
- **Roles**: `Owner`, `Editor`, `Viewer`.
- **Scope**: Coarse-grained and project-wide.
  - `Owner`: Full management rights, modify IAM policies, manage billing, delete projects.
  - `Editor`: Modify resources across services, but cannot alter billing or IAM permissions.
  - `Viewer`: Read-only access across all services in the project.
- **Interview Note**: Violates the Principle of Least Privilege. Google recommends against using Basic roles in production environments.

### 2. Predefined Roles (Google-Managed)
- Fine-grained, service-specific roles maintained and updated by Google.
- Follows the naming convention `roles/<service>.<roleName>`.
  - Examples: `roles/storage.objectViewer`, `roles/compute.networkAdmin`, `roles/container.developer`.
- Automatically updated by Google when new API capabilities are introduced.

### 3. Custom Roles (User-Managed)
- User-tailored collection of discrete permissions when predefined roles are too broad or too restrictive.
- Example: Granting `compute.instances.start` and `compute.instances.stop` without allowing instance creation or deletion.
- **Constraints**:
  - Cannot be created at the folder level if managed by project (managed at Project or Org level).
  - Certain high-level or administrative permissions cannot be added to custom roles.
  - Maintenance overhead: Must be updated manually if underlying APIs change.

---

## 4. Service Accounts (Machine Identity)

- **Definition**: Special accounts intended for applications, automated scripts, and cloud resources to authenticate against GCP APIs without human credentials.
- **Default Service Accounts**:
  - Automatically created when certain APIs are enabled (e.g., Compute Engine default SA: `<project-number>-compute@developer.gserviceaccount.com`).
  - **Security Concern**: Default SAs historically come pre-configured with the legacy `Editor` role. In production, always replace with dedicated, least-privileged custom service accounts.
- **Keys & Authentication**:
  - *Google-Managed Keys*: Automatically rotated and used when running directly inside GCP workloads (GCE, Cloud Run, GKE) via the Instance Metadata Service.
  - *User-Managed Keys (JSON files)*: Downloadable private keys.
  - **Interview Best Practice**: **Avoid downloading JSON keys** due to credential leakage risks. Prefer **Workload Identity Federation** (for GitHub Actions, AWS, on-prem) or IAM Service Account Impersonation.

---

## 5. IAM Policy Inheritance & Evaluation

- **Inheritance Direction**: Organization $\rightarrow$ Folder $\rightarrow$ Project $\rightarrow$ Resource.
- **Additive Policy (Union)**: A user's effective permissions on a given resource are the **union** of all IAM policies granted at the resource level and all inherited parent levels.
- **Rule**: Permissions cannot be restricted down the tree. If a user is `Viewer` at the Project level, you cannot restrict them to "no access" on a single bucket inside that project using standard IAM grants (requires IAM Deny policies).

---

## 6. Production & DevOps Best Practices

1. **Principle of Least Privilege (PoLP)**: Grant only the minimum permissions necessary for the specific role or task.
2. **Assign Roles to Groups, Not Users**: Manage user access via Google Groups rather than individual bindings to simplify onboarding and offboarding.
3. **Eliminate Basic Roles**: Always default to Predefined roles or build Custom roles.
4. **Harden Service Accounts**:
   - Disable default service accounts with wide `Editor` access.
   - Avoid downloadable JSON keys; use Workload Identity / short-lived OAuth tokens.
5. **Periodic Auditing**: Review permissions via Cloud Audit Logs and act on recommendations from **IAM Recommender**.

---

## 7. Essential CLI Commands (IAM & Service Accounts)

```bash
# --- IAM Project Bindings ---
# Grant a Predefined Role to a User
gcloud projects add-iam-policy-binding my-project-id \
    --member="user:devops@example.com" \
    --role="roles/viewer"

# Revoke a Role from a Member
gcloud projects remove-iam-policy-binding my-project-id \
    --member="user:devops@example.com" \
    --role="roles/viewer"

# Inspect the effective IAM policy on a project
gcloud projects get-iam-policy my-project-id

# --- Service Account Lifecycle ---
# Create a dedicated Service Account for CI/CD
gcloud iam service-accounts create cicd-pipeline \
    --display-name="CI/CD Deployment Service Account"

# Bind a role to the Service Account at Project level
gcloud projects add-iam-policy-binding my-project-id \
    --member="serviceAccount:cicd-pipeline@my-project-id.iam.gserviceaccount.com" \
    --role="roles/container.developer"

# --- Custom Roles ---
# Create a Custom Role from a YAML definition or inline flags
gcloud iam roles create vmRestarter \
    --project=my-project-id \
    --title="VM Restarter" \
    --description="Allows restarting GCE instances only" \
    --permissions="compute.instances.start,compute.instances.stop,compute.instances.get"
```

---

## 8. High-Yield Interview Q&A

**Q: If a user has `Viewer` at the Project level, but is given `roles/storage.objectAdmin` on a specific Cloud Storage bucket, what can they do?**  
> **A:** Because GCP IAM permissions are additive (union of permissions), the user will have read-only access to all other resources in the project, but full object administrative control (read, write, delete) on that specific Cloud Storage bucket.

**Q: Why is downloading Service Account JSON keys considered an anti-pattern in modern cloud security?**  
> **A:** Downloaded JSON keys are long-lived static credentials without automatic rotation. They are susceptible to accidental git commits, insider threats, and local machine compromise. The recommended approach is to use short-lived tokens via Workload Identity Federation (for external CI/CD or multi-cloud) or IAM Service Account Impersonation.

**Q: Can you grant a single permission directly to a user in GCP?**  
> **A:** No. In GCP IAM, permissions cannot be directly assigned to an identity. Permissions must be bundled inside a Predefined or Custom Role, and the role is then bound to the identity via an IAM policy.


---

# Day 04: Google Compute Engine (GCE), Networking & Custom Images

## 1. Compute Engine Architecture (IaaS)

Compute Engine is GCP’s Infrastructure-as-a-Service (IaaS) offering, providing configurable Virtual Machines (VMs) running on Google’s infrastructure.

- **Scope**: VM instances are **Zonal resources** (tied to a specific Availability Zone within a Region).
- **Machine Families**:
  - **General-Purpose (`E2`, `N2`, `C3`)**: Balanced price/performance for web servers, dev/test, and microservices. (`E2` is cost-optimized with dynamic resource scheduling).
  - **Compute-Optimized (`C2`, `C2D`)**: High CPU performance per core for compute-intensive workloads, HPC, and gaming.
  - **Memory-Optimized (`M1`, `M2`, `M3`)**: Ultra-high RAM for in-memory databases (e.g., SAP HANA).
  - **Accelerator-Optimized (`A2`, `G2`)**: Equipped with NVIDIA GPUs for AI/ML training, LLM inference, and graphics rendering.
- **Spot VMs (formerly Preemptible VMs)**:
  - Up to 60–91% discount compared to on-demand pricing.
  - Compute Engine can reclaim (preempt) these instances whenever capacity is needed elsewhere, with a **30-second termination notice**.
  - Ideal for fault-tolerant, batch, and stateless CI/CD workloads.

---

## 2. Storage: Persistent Disk vs. Local SSD

| Feature | Persistent Disk (PD) | Local SSD |
| :--- | :--- | :--- |
| **Connection Type** | Network-attached block storage | Physically attached to the host server |
| **Durability** | High (automatically replicated within zone/region) | Ephemeral (tied to the host hardware) |
| **Data Persistence** | Persists even if VM is stopped or terminated | Data is **lost** when VM is stopped |
| **Performance** | Scalable IOPS/throughput based on disk size | Ultra-low sub-millisecond latency & highest IOPS |
| **Use Case** | Boot disks, databases, permanent file storage | Scratch disks, caching layers, high-speed swap |

---

## 3. Automation: Startup Scripts vs. Custom Images

### Bootstrapping (Startup Scripts)
- Executed automatically as `root` during VM boot.
- Defined inline via `--metadata startup-script="..."` or external file `--metadata startup-script-url="gs://bucket/script.sh"`.
- **Key Behavior**: Runs on initial instance creation **and on every subsequent reboot**.
- *Trade-off*: Slower instance spin-up time because packages install on boot.

### Baking (Custom Images)
- Create a standardized "Golden Image" containing the base OS, pre-installed runtimes, patches, and binaries.
- Workflow: Stop VM $\rightarrow$ Create Image from source disk $\rightarrow$ Launch new instances directly from custom image.
- *Trade-off*: Rapid, consistent boot times (ideal for Auto-scaling Instance Groups), but requires CI/CD maintenance (e.g., HashiCorp Packer) to keep base images updated.

---

## 4. VPC Firewall Rules & Network Tags

- **Stateful Nature**: GCP firewall rules are stateful. If ingress traffic is allowed, outgoing response traffic over the same connection is automatically permitted regardless of egress rules.
- **Default State**:
  - All incoming traffic from the internet is **blocked by default** (except implied default-allow-internal, default-allow-ssh, default-allow-rdp, default-allow-icmp in the default VPC).
  - **Port 80 (HTTP) and Port 443 (HTTPS) are blocked** until an explicit ingress rule is created.
- **Network Tags**:
  - Lightweight string metadata attached to VM instances (e.g., `http-server`).
  - Firewall rules bind to instances using `--target-tags=http-server`, isolating rule application to matching VMs instead of the entire subnet.

---

## 5. VM Access: SSH & Management

- **`gcloud compute ssh <INSTANCE_NAME>`**:
  - Automatically generates and propagates SSH key pairs to instance metadata.
  - Automatically manages user accounts on the Linux guest OS.
- **OS Login**: Recommended enterprise approach using Cloud IAM permissions (`roles/compute.osLogin` / `roles/compute.osAdminLogin`) rather than managing individual SSH keys in metadata.
- **Identity-Aware Proxy (IAP) for TCP Forwarding**: Allows secure SSH into VMs that have **no public IP address** without needing a bastion/jump host.

---

## 6. Essential CLI Commands (Compute Engine Workflow)

```bash
# --- 1. Launch VM with Automated Startup Script and Tag ---
gcloud compute instances create web-server-vm \
    --zone=us-central1-a \
    --machine-type=e2-micro \
    --image-family=ubuntu-2204-lts \
    --image-project=ubuntu-os-cloud \
    --tags=http-server \
    --metadata startup-script='#!/bin/bash
    apt-get update
    apt-get install -y nginx
    systemctl enable nginx
    systemctl start nginx'

# --- 2. Create Firewall Rule to Expose HTTP Traffic ---
gcloud compute firewall-rules create allow-http-traffic \
    --direction=INGRESS \
    --priority=1000 \
    --network=default \
    --action=ALLOW \
    --rules=tcp:80 \
    --target-tags=http-server \
    --description="Allow incoming HTTP traffic to tagged instances"

# --- 3. Connect to VM via SSH ---
gcloud compute ssh web-server-vm --zone=us-central1-a

# --- 4. Create Reusable Golden Custom Image ---
# Stop VM to guarantee disk consistency
gcloud compute instances stop web-server-vm --zone=us-central1-a

# Create image from instance boot disk
gcloud compute images create nginx-golden-image-v1 \
    --source-disk=web-server-vm \
    --source-disk-zone=us-central1-a \
    --family=web-servers

# --- 5. Provision New VM Instantly from Custom Image ---
gcloud compute instances create web-server-clone \
    --zone=us-central1-a \
    --machine-type=e2-micro \
    --image=nginx-golden-image-v1 \
    --tags=http-server
```

---

## 7. High-Yield Interview Q&A

**Q: You deployed an Nginx web server on a GCE instance with a public IP, but you cannot reach it in your browser. What is the most likely root cause?**  
> **A:** GCP blocks all incoming ingress traffic on port 80 by default. To fix this, you must create a VPC firewall ingress rule allowing `tcp:80` and ensure the target tag of the rule matches the network tag applied to the GCE instance (e.g., `http-server`).

**Q: What is the difference between a Persistent Disk and a Local SSD in terms of lifecycle and durability?**  
> **A:** Persistent Disks are network-attached block devices whose data persists independently of the VM lifecycle (they survive instance stop/start and can be reattached). Local SSDs are physically attached to the host server, offering maximum IOPS and sub-millisecond latency, but are ephemeral: shutting down or terminating the VM permanently destroys the data on the Local SSD.

**Q: When would you use a Startup Script vs. a Custom Image (Baking vs. Bootstrapping)?**  
> **A:** Use startup scripts (bootstrapping) for development or lightweight configurations where dynamic parameter injection is required and boot delay is acceptable. Use Custom Images (baking) in production environments with Auto-scaling Managed Instance Groups (MIGs) where fast scaling, immutability, and predictable boot times are critical.


---

# Day 05: GCP Storage Services (GCS, Filestore, PD) & Secure VM Integration

## 1. GCP Storage Services Landscape

| Service | Storage Architecture | Interface / Protocol | Redundancy Scope | Primary DevOps Use Case | AWS Equivalent |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Cloud Storage (GCS)** | Object Storage | REST APIs (`gsutil`, `gcloud storage`, HTTPS) | Regional, Dual-Region, Multi-Region | Unstructured data, CI/CD artifacts, backups, Terraform state files | AWS S3 |
| **Persistent Disk (PD)** | Block Storage | POSIX block device (formatted with ext4/xfs) | Zonal or Regional (replicated across 2 zones) | VM boot disks, stateful container volumes, databases | AWS EBS |
| **Filestore** | File Storage | NFSv3 (Network File System) | Zonal or Regional | Shared storage across multiple VMs/GKE pods (e.g., shared CMS, Jenkins home) | AWS EFS |
| **Local SSD** | Ephemeral Block | High-speed NVMe bus | Host-attached (Zonal) | In-memory cache, high-speed swap, temporary scratch space | AWS Instance Store |
| **Cloud Bigtable** | Wide-Column NoSQL | HBase API / gRPC | Regional / Multi-Region | High-throughput time-series, IoT telemetry, real-time analytics | AWS DynamoDB |

---

## 2. Google Cloud Storage (GCS) Deep Dive

### Core Characteristics
- **Flat Namespace**: GCS stores objects (data + metadata) in **buckets**. Directories/folders in GCS are purely virtual, simulated via key naming prefixes (`/path/to/file.txt`).
- **Global Bucket Namespace**: Bucket names are globally unique across all Google Cloud customers worldwide (similar to domain names).
- **Durability**: Engineered for **11 nines (99.999999999%)** annual durability through automatic multi-device replication.
- **Strong Global Consistency**: GCS provides **strong read-after-write consistency** for all upload, overwrite, and metadata operations. Once a write succeeds, all subsequent reads immediately return the latest version.
- **Default Encryption**: Encrypted at rest (Google-managed keys, Cloud KMS Customer-Managed Encryption Keys [CMEK], or Customer-Supplied Keys [CSEK]) and in transit via TLS.

---

## 3. GCS Storage Classes & Lifecycle Management

| Storage Class | Minimum Storage Duration | Typical Access Frequency | Cost Structure | Best Suited For |
| :--- | :--- | :--- | :--- | :--- |
| **Standard** | None | Frequent (multiple times/day) | Highest storage cost, lowest/no retrieval fee | Web assets, streaming, active CI/CD artifacts |
| **Nearline** | 30 days | Infrequent ($\le$ once per month) | Lower storage cost, small retrieval fee | Monthly backups, recent log archives |
| **Coldline** | 90 days | Rare ($\le$ once per quarter) | Very low storage cost, moderate retrieval fee | Disaster recovery backups, quarterly audit logs |
| **Archive** | 365 days | Very rare ($<$ once per year) | Lowest storage cost, highest retrieval fee | Long-term compliance, regulatory data retention |

> [!WARNING]
> **Minimum Retention Penalty**: Deleting, overwriting, or transitioning an object before its class minimum duration (e.g., deleting a Coldline file after 20 days) incurs an **early deletion charge** for the remaining days.

### Object Lifecycle Management (OLM)
- Set automated JSON/declarative rules on buckets to transition storage classes or delete objects based on:
  - Object age (e.g., transition `Standard` $\rightarrow$ `Nearline` after 30 days $\rightarrow$ `Coldline` after 90 days $\rightarrow$ Delete after 365 days).
  - Creation date or number of newer versions (when Object Versioning is enabled).

---

## 4. GCS Access Control: Uniform vs. Fine-Grained

- **Uniform Bucket-Level Access (UBLA)** *(Recommended & Modern Standard)*:
  - Enforces IAM policies uniformly across all objects within the bucket.
  - Completely **disables legacy Object Access Control Lists (ACLs)**.
  - Simplifies security audits and prevents accidental object leaks caused by individual object permissions.
- **Fine-Grained Access**:
  - Employs both Cloud IAM and per-object ACLs.
  - Harder to audit and scale; used only for legacy architectures.
- **Signed URLs**:
  - Time-limited cryptographic links allowing external clients to download or upload specific objects without needing a Google account or IAM role.

---

## 5. Architecture Pattern: Keyless VM-to-GCS Integration

Connecting Compute Engine VMs to GCS without hardcoding credentials or downloading insecure JSON keys:

```
[ Compute Engine VM ]
        │
   (Instance Metadata Service)
   http://metadata.google.internal/computeMetadata/v1/instance/service-accounts/default/token
        │
[ Attached Service Account ] (e.g., gcs-app-sa)
        │
  (IAM Role: roles/storage.objectAdmin)
        │
[ GCS Bucket ] (gs://my-app-storage)
```

1. **Create Dedicated Service Account**: Provision a least-privileged SA (e.g., `gcs-app-sa`).
2. **Assign IAM Role**: Grant `roles/storage.objectViewer` (read) or `roles/storage.objectAdmin` (read/write) at the bucket or project level.
3. **Attach to VM**: Bind the Service Account to the VM during creation with full cloud API scope (`--scopes=cloud-platform`).
4. **Keyless Token Retrieval**: The application or `gcloud` inside the VM queries the internal **Instance Metadata Service** to fetch short-lived OAuth2 bearer tokens automatically.

---

## 6. Essential CLI Commands (GCS & Integration)

```bash
# --- 1. Create a Bucket with Uniform Access and Standard Class ---
gcloud storage buckets create gs://my-unique-gcp-bucket-101 \
    --location=us-central1 \
    --default-storage-class=STANDARD \
    --uniform-bucket-level-access

# --- 2. Object Operations ---
# Upload a file
gcloud storage cp app.log gs://my-unique-gcp-bucket-101/logs/

# List objects in bucket
gcloud storage ls --recursive gs://my-unique-gcp-bucket-101/

# Stream object content directly to terminal
gcloud storage cat gs://my-unique-gcp-bucket-101/logs/app.log

# --- 3. Provision Service Account for GCS Access ---
gcloud iam service-accounts create gcs-vm-sa \
    --display-name="GCS VM Access Service Account"

# Bind IAM role to SA at bucket level (Least Privilege)
gcloud storage buckets add-iam-policy-binding gs://my-unique-gcp-bucket-101 \
    --member="serviceAccount:gcs-vm-sa@my-project-id.iam.gserviceaccount.com" \
    --role="roles/storage.objectAdmin"

# --- 4. Launch VM with Attached Service Account ---
gcloud compute instances create storage-client-vm \
    --zone=us-central1-a \
    --machine-type=e2-micro \
    --service-account=gcs-vm-sa@my-project-id.iam.gserviceaccount.com \
    --scopes=cloud-platform
```

---

## 7. High-Yield Interview Q&A

**Q: What is the architectural difference between Google Cloud Storage (GCS) and Filestore?**  
> **A:** GCS is an object storage system accessed via HTTP/REST APIs, designed for unstructured files, massive scalability, and global durability without POSIX compliance. Filestore is a fully managed NFS (Network File System) providing POSIX-compliant file access over a private network, enabling multiple VMs or Kubernetes pods to mount the same shared file system simultaneously.

**Q: Why is Uniform Bucket-Level Access (UBLA) recommended over Fine-Grained access in GCS?**  
> **A:** UBLA disables legacy Object ACLs, ensuring all access is managed centrally and exclusively through Cloud IAM at the bucket level. This eliminates security blind spots where individual objects could have conflicting or overly permissive ACLs (such as public read access), simplifying compliance and security audits.

**Q: How do you allow a virtual machine in Compute Engine to upload files to a GCS bucket securely without credentials?**  
> **A:** Assign a custom Service Account with `roles/storage.objectAdmin` or `roles/storage.objectCreator` permissions to the GCE instance. Applications on the VM can then access GCS keylessly because the Google Cloud SDK and client libraries automatically fetch temporary credentials from the internal GCP Instance Metadata Service.


---

# Day 06: VPC (Virtual Private Cloud) Networking Deep Dive

## 1. What is a Virtual Private Cloud (VPC)?

A Virtual Private Cloud (VPC) is a logically isolated, software-defined virtual network provisioned in the cloud to run VMs, containers, and database workloads securely.

### The "Gated Community" Analogy (Mental Model)
- **VPC** $\rightarrow$ **The Gated Community**: An isolated, walled territory dedicated to your organization.
- **Subnets** $\rightarrow$ **Streets / Blocks**: Internal partitions grouping similar houses (e.g., Public residential street vs. Private restricted street).
- **Internet Gateway (IGW)** $\rightarrow$ **Main Community Entrance**: The gate connecting the community to the public road system (Internet).
- **Route Tables / Routes** $\rightarrow$ **Street Signs & Maps**: Directs packets where to go based on destination address.
- **Firewall Rules** $\rightarrow$ **Security Guards**: Inspects every incoming and outgoing visitor at the door based on allow/deny rules.
- **Cloud NAT** $\rightarrow$ **Outbound Courier Service**: Enables residents in private streets to order external goods from the outside world without letting outsiders enter or see inside.
- **VPC Peering** $\rightarrow$ **Private Bridge**: A secure road directly connecting two separate gated communities without using public highways.

---

## 2. GCP VPC Architecture vs. AWS (Critical Differentiator)

| Feature | Google Cloud (GCP) | AWS / Traditional Cloud |
| :--- | :--- | :--- |
| **VPC Scope** | **Global** (a single VPC spans all regions worldwide) | **Regional** (VPC is confined to one region) |
| **Subnet Scope** | **Regional** (each subnet is bound to a specific region) | **Zonal** (each subnet is bound to a single AZ) |
| **Cross-Region Traffic** | Routes privately over Google's global fiber backbone without internet gateways or complex VPNs | Requires cross-region VPC peering or Transit Gateways |

---

## 3. Auto Mode vs. Custom Mode VPCs

| Feature | Auto Mode VPC | Custom Mode VPC *(Production Standard)* |
| :--- | :--- | :--- |
| **Subnet Creation** | Automatically creates one subnet per GCP region with predefined CIDRs (`10.128.0.0/9`) | Zero subnets created initially; fully defined by user |
| **IP Planning** | Fixed, rigid IP ranges | Complete control over IP address ranges and regions |
| **Enterprise Readiness** | **Anti-pattern**: Overlapping subnets cause collisions during VPC Peering or Hybrid VPN connections | **Best practice**: Clean CIDR allocation preventing IP conflicts |
| **Conversion** | Can be converted to Custom Mode | **Cannot** be converted back to Auto Mode |

---

## 4. Subnets & CIDR IP Addressing

- **Private Address Ranges (RFC 1918)**:
  - `10.0.0.0/8` (Large enterprises)
  - `172.16.0.0/12` (Medium enterprises)
  - `192.168.0.0/16` (Small networks / home labs)
- **Subnet Sizing (CIDR Math)**:
  - Total addresses = $2^{(32 - \text{prefix})}$. (e.g., `/24` gives $2^8 = 256$ IPs).
  - **GCP Reserved IPs**: In every subnet, GCP reserves **4 IP addresses**:
    1. Network address (e.g., `.0`)
    2. Default Gateway (e.g., `.1`)
    3. Second-to-last reserved for future use (e.g., `.254`)
    4. Broadcast address (e.g., `.255`)
    - *Example*: A `/24` subnet has $256 - 4 = 252$ usable IPs.

---

## 5. Cloud NAT vs. Private Google Access (PGA)

Two essential mechanisms for VMs in **private subnets** (no external public IPs):

```
                        ┌───────────────────┐
                        │   Public Internet │ (Apt repos, GitHub, third-party APIs)
                        └─────────▲─────────┘
                                  │ (Outbound Only)
                            [ Cloud NAT ]
                                  ▲
                                  │
┌─────────────────────────────────┴─────────────────────────────────┐
│ Private Subnet (No External IPs)                                  │
│                                                                   │
│   [ Private VM ] ──────────────► [ Private Google Access (PGA) ]   │
│                                                   │               │
└───────────────────────────────────────────────────┼───────────────┘
                                                    ▼
                                       ┌─────────────────────────┐
                                       │   Google APIs (Internal)│ (GCS, BigQuery, Pub/Sub)
                                       └─────────────────────────┘
```

1. **Cloud NAT (Network Address Translation)**:
   - Enables private VMs to initiate outbound requests to the internet (e.g., OS updates, downloading packages, calling external webhooks).
   - **Blocks unsolicited inbound connections** from the internet.
   - Fully managed, software-defined service (no NAT instances to maintain or scale).
2. **Private Google Access (PGA)**:
   - Allows private VMs to reach Google APIs and services (Cloud Storage, BigQuery, Secret Manager) directly across Google’s internal backbone.
   - Enabled per subnet with a single toggle; **does not require Cloud NAT or internet egress**.

---

## 6. VPC Network Peering & Routing

- **VPC Network Peering**:
  - Connects two VPC networks directly via Google's internal SDN backbone.
  - Traffic stays entirely on Google's private network (no public IP transit, high throughput, low latency).
  - Works across different projects and different organizations.
- **Non-Transitive Routing (Critical Rule)**:
  - If VPC A is peered with VPC B, and VPC B is peered with VPC C, **VPC A cannot communicate with VPC C** through VPC B.
  - Direct peering or a hub-and-spoke model (Cloud VPN / Network Connectivity Center) must be used for transit.

---

## 7. Essential CLI Commands (Custom VPC & Networking)

```bash
# --- 1. Create Custom VPC Network (No Auto-Subnets) ---
gcloud compute networks create production-vpc \
    --subnet-mode=custom \
    --bgp-routing-mode=regional

# --- 2. Create Regional Subnet with Private Google Access ---
gcloud compute networks subnets create us-central-app-subnet \
    --network=production-vpc \
    --region=us-central1 \
    --range=10.10.1.0/24 \
    --enable-private-ip-google-access

# --- 3. Set Up Cloud Router and Cloud NAT for Outbound Internet ---
# Create Cloud Router
gcloud compute routers create nat-router-us-central \
    --network=production-vpc \
    --region=us-central1

# Attach Cloud NAT to Router for the subnet
gcloud compute routers nats create production-nat-gw \
    --router=nat-router-us-central \
    --region=us-central1 \
    --auto-allocate-nat-external-ips \
    --nat-all-subnet-ip-ranges

# --- 4. Create Ingress Firewall Rule for Internal Traffic ---
gcloud compute firewall-rules create allow-internal-traffic \
    --network=production-vpc \
    --direction=INGRESS \
    --priority=1000 \
    --action=ALLOW \
    --rules=all \
    --source-ranges=10.10.0.0/16 \
    --description="Allow all traffic within VPC CIDR block"
```

---

## 8. High-Yield Interview Q&A

**Q: How does the scope of a VPC in GCP differ from that in AWS?**  
> **A:** An AWS VPC is regional (confined to a single AWS region). A GCP VPC is inherently **global**, meaning a single VPC network can span every GCP region globally without setting up inter-region peering or transit gateways. Subnets in GCP are regional.

**Q: Why should production cloud environments avoid Auto-Mode VPCs?**  
> **A:** Auto-mode VPCs automatically assign the same predefined subnet IP ranges (`10.128.0.0/9`) across all regions. If you later need to establish VPC Peering, Cloud Interconnect, or an on-premises VPN, the overlapping CIDR blocks will cause routing conflicts that prevent peering. Custom-mode VPCs provide deterministic, conflict-free IP planning.

**Q: A backend VM with no public IP needs to download security patches from the internet and upload logs to a GCS bucket. How should you architect this?**  
> **A:** 
> 1. Enable **Private Google Access (PGA)** on the subnet so the VM reaches the GCS bucket over Google's internal network backbone.
> 2. Provision **Cloud NAT** (backed by a Cloud Router) in that region so the VM can initiate outbound internet connections for package updates without exposing itself to inbound internet attacks.

**Q: Is VPC Peering transitive in GCP?**  
> **A:** No. VPC peering is strictly non-transitive. If VPC 1 peers with VPC 2, and VPC 2 peers with VPC 3, instances in VPC 1 cannot communicate with VPC 3 unless a direct peering connection is established between VPC 1 and VPC 3 or a transit appliance/hub architecture is deployed.


---

# Day 07: High Availability with Managed Instance Groups (MIGs) & Global Load Balancing

## 1. High Availability (HA) & Scaling Architecture

- **Single VM Instance**: Single point of failure (SPOF). Host failure or zone outage brings down the application.
- **Unmanaged Instance Group**: A collection of heterogeneous VMs. No autoscaling, no auto-healing, and no rolling update capabilities.
- **Managed Instance Group (MIG)**: A collection of homogeneous (identical) VMs created from a common **Instance Template**.
  - **Zonal MIG**: Deploys instances into a single zone.
  - **Regional MIG** *(Production Standard)*: Distributes instances evenly across multiple availability zones within a region, providing resilience against entire data center outages.

---

## 2. Core Pillars of Managed Instance Groups (MIGs)

### 1. Instance Template
- An immutable blueprint defining machine configuration:
  - Machine family/type (e.g., `e2-micro`).
  - Base OS boot disk image or custom golden image.
  - Startup scripts / metadata.
  - Network tags (for firewall rules) and service accounts.
- **Update Workflow**: You cannot edit an existing template; you create a new version and perform a rolling update on the MIG.

### 2. Auto-Healing
- Continuously probes instance health via a dedicated application-level **Health Check** (e.g., HTTP `GET /health` on port 80).
- If an instance fails consecutive health checks, the MIG marks it unhealthy, stops/deletes it, and **automatically provisions a replacement VM** based on the instance template.

### 3. Autoscaling
- Dynamically adjusts the number of VM instances based on demand:
  - **Scaling Signals**: Average CPU utilization (e.g., maintain 60%), Load Balancing serving capacity, Cloud Monitoring custom metrics, or schedule-based scaling.
  - **Cool-Down Period**: Waiting period allowing new instances to complete boot and initialization before collecting metric data to prevent thrashing.

### 4. Rolling Updates
- Seamless deployment of new instance templates with zero downtime:
  - **Canary Testing**: Update a subset of instances (e.g., 10%) to validate stability before rolling out to 100%.
  - **Max Surge / Max Unavailable**: Controls how many extra instances can be created above target size, and how many can be offline during rollout.

---

## 3. GCP Global HTTP(S) Load Balancer (Layer 7) Architecture

The GCP Global External HTTP(S) Load Balancer is a software-defined proxy service running on Google's globally distributed Edge Points of Presence (PoPs).

### Traffic Ingress Flow:
$$\text{Client} \xrightarrow{} \text{Anycast IP} \xrightarrow{} \text{Forwarding Rule} \xrightarrow{} \text{Target Proxy} \xrightarrow{} \text{URL Map} \xrightarrow{} \text{Backend Service} \xrightarrow{} \text{MIG Backends}$$

```
                          [ Client Anywhere in World ]
                                       │
                                       ▼ (BGP Anycast Routing)
               [ Global Anycast IP: Port 80 / 443 (Edge PoP) ]
                                       │
                                       ▼
                       [ Global Forwarding Rule ]
                                       │
                                       ▼
                    [ Target HTTP(S) Proxy (SSL Offloading) ]
                                       │
                                       ▼
                     [ URL Map (Host & Path Routing) ]
                                ┌──────┴──────┐
                    (Path /api/*)             (Default /*)
                                ▼                         ▼
                    [ Backend Service: API ]   [ Backend Service: Web ]
                                │                         │
                        (Health Check)            (Health Check)
                                │                         │
                                ▼                         ▼
                        [ Regional MIG: API ]     [ Regional MIG: Web ]
```

### Component Breakdown:
1. **Anycast Global IP**: A single public IP address advertised globally via BGP. Clients connect to the nearest Google Edge PoP, entering Google’s private fiber network immediately.
2. **Forwarding Rule**: Directs incoming external traffic matching specific IP, port, and protocol to a Target Proxy.
3. **Target HTTP(S) Proxy**: Terminates client TCP connections, decrypts SSL/TLS certificates (SSL Offloading), and forwards HTTP requests to the URL Map.
4. **URL Map (Routing Engine)**: Evaluates HTTP request paths and hostnames (e.g., route `/video/*` to a video backend service and `/api/*` to an API backend service).
5. **Backend Service**:
   - Central coordinator for backends (MIGs or Network Endpoint Groups [NEGs]).
   - Defines session affinity, connection draining, timeout policies, and links the application health check.
6. **Health Check**:
   - Probes backends at regular intervals (e.g., every 5 seconds). Backends failing `unhealthy-threshold` checks are detached from routing until they pass `healthy-threshold` checks.

---

## 4. Health Check Prober IP Ranges (Critical Firewall Gotcha)

> [!IMPORTANT]
> Google Cloud health check probes originate from distributed internal Google infrastructure, not the load balancer's external Anycast IP. You **must explicitly allow ingress traffic** from these exact Google probe IP ranges in your VPC firewall:
> - **`35.191.0.0/16`**
> - **`130.211.0.0/22`**
> 
> If these ranges are blocked, health checks report backends as `UNHEALTHY`, causing the load balancer to return HTTP `502 Bad Gateway`.

---

## 5. End-to-End Production Deployment Flow (CLI)

```bash
# --- 1. Create Startup Script ---
cat <<'EOF' > startup.sh
#!/bin/bash
apt-get update
apt-get install -y nginx
echo "Node: $(hostname) | Zone: $(curl -s -H 'Metadata-Flavor: Google' http://metadata.google.internal/computeMetadata/v1/instance/zone)" > /var/www/html/index.html
systemctl restart nginx
EOF

# --- 2. Create Instance Template ---
gcloud compute instance-templates create web-template \
    --machine-type=e2-micro \
    --image-family=debian-11 \
    --image-project=debian-cloud \
    --metadata-from-file startup-script=startup.sh \
    --tags=http-server

# --- 3. Create Multi-Zone Regional MIG (HA Across 3 Zones) ---
gcloud compute instance-groups managed create web-mig \
    --template=web-template \
    --size=3 \
    --region=us-central1 \
    --zones=us-central1-a,us-central1-b,us-central1-c

# --- 4. Configure Autoscaling on the MIG ---
gcloud compute instance-groups managed set-autoscaling web-mig \
    --region=us-central1 \
    --min-num-replicas=3 \
    --max-num-replicas=9 \
    --target-cpu-utilization=0.6 \
    --cool-down-period=60

# --- 5. Create Health Check for Backend Verification ---
gcloud compute health-checks create http web-health-check \
    --port=80 \
    --check-interval=5s \
    --timeout=5s \
    --unhealthy-threshold=2 \
    --healthy-threshold=2

# --- 6. Firewall Rule: Allow Health Check Probers & User Traffic ---
gcloud compute firewall-rules create allow-hc-and-http \
    --network=default \
    --direction=INGRESS \
    --priority=1000 \
    --action=ALLOW \
    --rules=tcp:80 \
    --source-ranges=130.211.0.0/22,35.191.0.0/16,0.0.0.0/0 \
    --target-tags=http-server

# --- 7. Configure Backend Service & Attach MIG ---
gcloud compute backend-services create web-backend \
    --protocol=HTTP \
    --port-name=http \
    --health-checks=web-health-check \
    --enable-logging \
    --logging-sample-rate=1.0 \
    --global

gcloud compute backend-services add-backend web-backend \
    --instance-group=web-mig \
    --instance-group-region=us-central1 \
    --balancing-mode=UTILIZATION \
    --max-utilization=0.8 \
    --global

# --- 8. Create URL Map & Target HTTP Proxy ---
gcloud compute url-maps create web-map \
    --default-service=web-backend

gcloud compute target-http-proxies create web-proxy \
    --url-map=web-map

# --- 9. Reserve Global Static IP & Create Forwarding Rule ---
gcloud compute addresses create web-global-ip --global

gcloud compute forwarding-rules create web-forwarding-rule \
    --address=web-global-ip \
    --global \
    --target-http-proxy=web-proxy \
    --ports=80
```

---

## 6. High-Yield Interview Q&A

**Q: What is the difference between a Zonal MIG and a Regional MIG?**  
> **A:** A Zonal MIG restricts all instances to a single availability zone, leaving the system vulnerable to zone-level datacenter failures. A Regional MIG distributes instances evenly across multiple availability zones within a region, providing automated cross-zone failover, higher availability SLAs, and disaster resilience.

**Q: Your Google Cloud HTTP(S) Load Balancer is returning HTTP `502 Bad Gateway` even though the backend Nginx VMs are active and serving traffic locally. What is the most probable cause?**  
> **A:** Missing or misconfigured VPC firewall ingress rules. GCP Load Balancer health checkers probe from specific IP ranges (`35.191.0.0/16` and `130.211.0.0/22`). If the VPC firewall does not permit ingress from these CIDR ranges on the health check port, the backends are marked `UNHEALTHY` and the proxy refuses to route traffic.

**Q: How does Anycast IP routing benefit global applications on GCP?**  
> **A:** With Anycast, a single global IP address is announced across all Google Edge Points of Presence globally. Client requests enter Google's private network at the geographically closest edge PoP rather than traveling over the unpredictable public internet, minimizing latency, mitigating DDoS exposure, and terminating SSL handshakes close to the user.

**Q: How does Auto-healing differ from Autoscaling in a MIG?**  
> **A:** Autoscaling adjusts instance quantity dynamically to handle fluctuations in traffic load (capacity management). Auto-healing continuously monitors the operational status of individual instances using application health check signals and replaces failed, corrupted, or non-responsive instances with fresh VMs (health management).


---

# Day 08: Production-Grade Architecture — Private App, Regional ALB, Cloud NAT & Cloud DNS

## 1. End-to-End System Architecture

```
                       [ Internet Users: example.com ]
                                     │
                             (DNS Resolution)
                                     ▼
                        [ Google Cloud DNS Zone ]
                        (ns-cloud-*.googledomains.com)
                                     │
                             (A Record: LB IP)
                                     ▼
                [ Regional External Application Load Balancer ]
                                     │
                                     ▼
                     [ Proxy-Only Subnet: 10.10.30.0/24 ]
                        (Envoy Proxy Managed Layer)
                                     │
                                     ▼ (Data Plane TCP:8080)
┌────────────────────────────────────┼────────────────────────────────────┐
│ Custom VPC: prod-vpc               │                                    │
│                                    ▼                                    │
│   ┌──────────────────────────────────────────────────────────────┐     │
│   │ Private App Subnet (10.10.20.0/24) - NO EXTERNAL IPs         │     │
│   │                                                              │     │
│   │   [ Managed Instance Group: Flask + Gunicorn on Port 8080 ]  │     │
│   │             │                                                │     │
│   │             │ (Outbound Only: apt-get / pip)                 │     │
│   │             ▼                                                │     │
│   │       [ Cloud NAT Gateway ] (router-asia-south1)             │     │
│   └─────────────┬────────────────────────────────────────────────┘     │
└─────────────────┼───────────────────────────────────────────────────────┘
                  ▼
         [ Public Internet ] (PyPI, Ubuntu Package Repositories)
```

---

## 2. Network Topology & Subnet Segregation

A production VPC must isolate network tiers according to workload accessibility and operational function:

| Subnet Name | CIDR Prefix | Role & Access Level | Special Purpose / Settings |
| :--- | :--- | :--- | :--- |
| **`subnet-public`** | `10.10.10.0/24` | Optional DMZ / Bastion / Jump hosts | Direct internet routing |
| **`subnet-app`** | `10.10.20.0/24` | **Private Workloads**: Application VMs | **No External IPs**; Outbound internet via Cloud NAT only |
| **`subnet-proxy-only`** | `10.10.30.0/24` | **Managed Envoy Proxies** | Purpose: `REGIONAL_MANAGED_PROXY` (Required by GCP Regional ALBs) |

> [!IMPORTANT]
> **What is a Proxy-Only Subnet?**
> Google Cloud's Regional External Application Load Balancers and Internal Application Load Balancers are powered by distributed **Envoy proxies**. These proxies do not run on the client's network or the VM's network; GCP allocates internal IP addresses from this **Proxy-Only Subnet** to establish the backend TCP connections to your application VMs.

---

## 3. Outbound Internet for Private VMs (Cloud NAT)

- Application instances in `subnet-app` have **zero external (public) IP addresses** to prevent direct inbound internet attacks.
- To execute bootstrapping commands (e.g., `apt-get update`, `pip install flask gunicorn`), the subnet is routed through **Cloud NAT** managed by a **Cloud Router**.
- Cloud NAT performs source network address translation (SNAT) for outbound traffic while dropping any unsolicited inbound connection attempts.

---

## 4. Defense-in-Depth Firewall Architecture

Production security isolates backend instances by explicitly granting ingress traffic **only** to authorized Google services:

1. **Google Health Check Ingress**:
   - **Source CIDRs**: `35.191.0.0/16`, `130.211.0.0/22`
   - **Port**: `TCP:8080`
   - **Target**: Application VMs (`tag: app-server`)
2. **Proxy-Only Data Plane Ingress**:
   - **Source CIDR**: `10.10.30.0/24` (The Proxy-Only Subnet)
   - **Port**: `TCP:8080`
   - **Target**: Application VMs (`tag: app-server`)
   - *Security Guarantee*: Internet clients **cannot** bypass the Load Balancer to reach the backend application VMs directly.

---

## 5. Application Architecture & Service Management

### Best Practices Enforced:
- **Least-Privilege User**: The application runs under a dedicated, unprivileged system user (`appuser`), never as `root`.
- **Isolated Environment**: Python packages are installed inside a Virtual Environment (`venv`) to avoid polluting the system Python runtime.
- **Production WSGI Server**: Uses **Gunicorn** with multiple worker processes instead of the single-threaded Flask development server.
- **Process Supervision**: Managed via **Systemd** (`flask.service`) with `Restart=always` for self-healing process crashes.
- **Standard Health Check Endpoint**: Exposes `/health` returning HTTP 200 independently of user-facing routes.

---

## 6. Custom Domain & Cloud DNS Integration

```
[ Domain Registrar (e.g., GoDaddy, Namecheap) ]
                   │
                   ▼ (Nameserver Delegation)
[ Google Cloud DNS Public Zone (Authoritative) ]
  ├── Nameservers: ns-cloud-a1.googledomains.com (x4)
  ├── Apex A Record:    example.com  ──► [ Load Balancer External IP ]
  └── CNAME Record: www.example.com  ──► example.com
```

### Delegation Workflow:
1. Create a Public Managed DNS Zone in **Cloud DNS** for the domain name (e.g., `example.com`).
2. Cloud DNS assigns 4 authoritative nameservers (`ns-cloud-*.googledomains.com`).
3. Replace the registrar's default nameservers in the domain control panel with Google's 4 authoritative nameservers.
4. Add an **`A` record** mapping the apex domain (`@` or `example.com`) to the Load Balancer's public IP address.
5. Add a **`CNAME` record** pointing `www` to the apex domain.

---

## 7. Production CLI Implementation Reference

```bash
# --- 1. Create Custom VPC & Subnets ---
gcloud compute networks create prod-vpc --subnet-mode=custom

# Private application subnet
gcloud compute networks subnets create subnet-app \
    --network=prod-vpc \
    --region=asia-south1 \
    --range=10.10.20.0/24

# Proxy-only subnet (Mandatory for Regional Application Load Balancer)
gcloud compute networks subnets create subnet-proxy-only \
    --network=prod-vpc \
    --region=asia-south1 \
    --range=10.10.30.0/24 \
    --purpose=REGIONAL_MANAGED_PROXY \
    --role=ACTIVE

# --- 2. Configure Cloud Router & Cloud NAT for Private VM Egress ---
gcloud compute routers create router-asia-south1 \
    --network=prod-vpc \
    --region=asia-south1

gcloud compute routers nats create nat-asia-south1 \
    --router=router-asia-south1 \
    --region=asia-south1 \
    --auto-allocate-nat-external-ips \
    --nat-custom-subnet-ip-ranges=subnet-app

# --- 3. Firewall Rules (Strict Least Privilege) ---
# Allow health checks
gcloud compute firewall-rules create allow-health-checks-8080 \
    --network=prod-vpc \
    --direction=INGRESS \
    --action=ALLOW \
    --rules=tcp:8080 \
    --source-ranges=35.191.0.0/16,130.211.0.0/22 \
    --target-tags=app-server

# Allow data plane traffic from Proxy-Only Subnet only
gcloud compute firewall-rules create allow-proxy-only-8080 \
    --network=prod-vpc \
    --direction=INGRESS \
    --action=ALLOW \
    --rules=tcp:8080 \
    --source-ranges=10.10.30.0/24 \
    --target-tags=app-server

# --- 4. Create Instance Template (Private VM + Systemd Startup Script) ---
gcloud compute instance-templates create tmpl-flask-app \
    --region=asia-south1 \
    --machine-type=e2-micro \
    --network=prod-vpc \
    --subnet=subnet-app \
    --no-address \
    --tags=app-server \
    --metadata=startup-script='#!/bin/bash
    set -e
    apt-get update -y && apt-get install -y python3-pip python3-venv
    id -u appuser &>/dev/null || useradd -m -s /bin/bash appuser
    sudo -u appuser bash << "EOF"
    cd ~
    python3 -m venv venv
    source venv/bin/activate
    pip install --upgrade pip && pip install flask gunicorn
    cat > app.py << "PY"
from flask import Flask
app = Flask(__name__)
@app.route("/")
def index():
    return "Production Flask App running on Private GCP Subnet!\n"
@app.route("/health")
def health():
    return "ok", 200
PY
EOF
    cat > /etc/systemd/system/flask.service << "UNIT"
[Unit]
Description=Flask Production App via Gunicorn
After=network.target
[Service]
User=appuser
WorkingDirectory=/home/appuser
ExecStart=/home/appuser/venv/bin/gunicorn -w 2 -b 0.0.0.0:8080 app:app
Restart=always
[Install]
WantedBy=multi-user.target
UNIT
    systemctl daemon-reload && systemctl enable --now flask.service'

# --- 5. Deploy Managed Instance Group (MIG) ---
gcloud compute instance-groups managed create mig-flask-app \
    --template=tmpl-flask-app \
    --size=2 \
    --zone=asia-south1-a

# --- 6. Provision Regional HTTP Load Balancer ---
# Health Check
gcloud compute health-checks create http hc-flask-8080 \
    --region=asia-south1 \
    --port=8080 \
    --request-path=/health

# Backend Service
gcloud compute backend-services create backend-flask \
    --load-balancing-scheme=EXTERNAL_MANAGED \
    --protocol=HTTP \
    --port-name=http \
    --health-checks=hc-flask-8080 \
    --health-checks-region=asia-south1 \
    --region=asia-south1

# Add MIG as Backend
gcloud compute backend-services add-backend backend-flask \
    --instance-group=mig-flask-app \
    --instance-group-zone=asia-south1-a \
    --region=asia-south1

# URL Map
gcloud compute url-maps create url-map-flask \
    --default-service=backend-flask \
    --region=asia-south1

# Target HTTP Proxy
gcloud compute target-http-proxies create proxy-flask \
    --url-map=url-map-flask \
    --region=asia-south1

# External Static Regional IP & Forwarding Rule
gcloud compute addresses create ip-flask-lb --region=asia-south1

gcloud compute forwarding-rules create rule-flask-lb \
    --load-balancing-scheme=EXTERNAL_MANAGED \
    --network=prod-vpc \
    --subnet=subnet-proxy-only \
    --address=ip-flask-lb \
    --ports=80 \
    --region=asia-south1 \
    --target-http-proxy=proxy-flask \
    --target-http-proxy-region=asia-south1

# --- 7. Configure Cloud DNS ---
# Create Public Managed Zone
gcloud dns managed-zones create prod-domain-zone \
    --dns-name="example.com." \
    --description="Public Production Domain Zone" \
    --visibility=public

# Add Apex A Record pointing to the Load Balancer IP
LB_IP=$(gcloud compute addresses describe ip-flask-lb --region=asia-south1 --format="value(address)")
gcloud dns record-sets create "example.com." \
    --zone=prod-domain-zone \
    --type=A \
    --ttl=300 \
    --rrdatas="$LB_IP"
```

---

## 8. High-Yield Interview Q&A

**Q: Why is a Proxy-Only Subnet mandatory when deploying a Regional External Application Load Balancer in GCP?**  
> **A:** Regional External Application Load Balancers use Envoy proxy instances managed automatically by Google. Unlike Global Load Balancers that run at edge PoPs, Regional ALBs run inside your VPC region. GCP provisions internal IP addresses from this designated Proxy-Only Subnet (`--purpose=REGIONAL_MANAGED_PROXY`) to terminate client connections and initiate separate backend TCP connections to your private VMs.

**Q: In this architecture, why do we restrict the application firewall ingress to the Proxy-Only Subnet CIDR instead of `0.0.0.0/0`?**  
> **A:** Restricting ingress to the Proxy-Only Subnet (`10.10.30.0/24`) enforces perimeter security. It guarantees that client traffic can only reach the application servers through the Load Balancer (where security policies, DDoS mitigation, and SSL termination occur) and prevents any direct lateral network connections to backend VMs.

**Q: How does domain delegation work between a registrar like GoDaddy and Google Cloud DNS?**  
> **A:** When you create a public zone in Cloud DNS, Google assigns 4 authoritative nameservers. In the domain registrar's settings, you replace the registrar's default nameservers with Google's nameservers. The Top-Level Domain (TLD) registry is updated to delegate all authoritative DNS queries for that domain to Google's nameservers, which then resolve your `A` and `CNAME` records.


---

# Day 09: Google Cloud CLI (`gcloud`) Deep Dive & Automation

## 1. Google Cloud SDK Architecture

The Google Cloud CLI (`gcloud`) is the unified command-line tool for managing GCP infrastructure, identity, and services.

- **Unified API Contract**: Both the GCP Web Console and the `gcloud` CLI invoke the exact same underlying RESTful Google Cloud APIs.
- **Component Ecosystem**:
  - `gcloud`: Primary CLI for Compute Engine, IAM, VPC, GKE, Cloud Functions, and resource configuration.
  - `gcloud storage` *(replaces legacy `gsutil`)*: High-performance object storage management featuring automatic parallel composite uploads and multi-threading.
  - `bq`: Dedicated command-line tool for BigQuery dataset and SQL operations.
  - `kubectl`: Managed Kubernetes CLI, installable as a `gcloud` component.

---

## 2. Configuration & Multi-Environment Management

`gcloud` supports **Named Configurations**, allowing DevOps engineers to switch instantly between different accounts, projects, and environments (e.g., `dev`, `staging`, `prod`) without re-authenticating.

```bash
# --- Working with Named Configurations ---
# Create a dedicated configuration for production
gcloud config configurations create prod-env

# Activate a configuration
gcloud config configurations activate prod-env

# Set environment defaults within the active configuration
gcloud config set project my-production-project-id
gcloud config set compute/region us-central1
gcloud config set compute/zone us-central1-a
gcloud config set core/account devops-lead@company.com

# List all configurations and active pointer
gcloud config configurations list

# Display all active key-value settings
gcloud config list
```

---

## 3. Authentication & Credential Types

| Authentication Method | Command | Scope & Best Use Case |
| :--- | :--- | :--- |
| **User Interactive Auth** | `gcloud auth login` | Authenticates human users via OAuth browser flow for `gcloud` commands. |
| **Application Default Credentials (ADC)** | `gcloud auth application-default login` | Generates local credentials used by client libraries (Python, Go, Node.js) during local debugging. |
| **Service Account Key Auth** | `gcloud auth activate-service-account --key-file=sa-key.json` | Non-interactive authentication for automated external CI/CD pipelines (Jenkins, GitLab CI). |
| **Short-Lived Bearer Token** | `gcloud auth print-access-token` | Outputs a temporary OAuth2 token for making raw `curl` calls against Google APIs. |

---

## 4. Powerful Querying: `--filter`, `--format` & `--limit`

Instead of piping CLI outputs to external utilities like `grep`, `awk`, or `jq`, `gcloud` features native server-side and client-side filtering and formatting flags.

### 1. `--filter`: Resource Selection
Filter expressions evaluate boolean logic (`=`, `!=`, `:`, `~`, `AND`, `OR`, `NOT`).
```bash
# Filter instances by status and machine type
gcloud compute instances list --filter="status=RUNNING AND machineType:e2-micro"

# Filter by tags or metadata
gcloud compute instances list --filter="tags.items=http-server"
```

### 2. `--format`: Output Transformation
Formats output into `json`, `yaml`, `table`, `csv`, or projected `value(...)`.
```bash
# Extract VM External IP directly into a shell variable (No jq/awk needed)
VM_IP=$(gcloud compute instances list \
    --filter="name=day9-vm" \
    --format="value(networkInterfaces[0].accessConfigs[0].natIP)")

# Output a customized tabular report with specific columns
gcloud compute instances list \
    --format="table(name, zone, status, networkInterfaces[0].networkIP:label=INTERNAL_IP)"
```

---

## 5. Storage Optimization: `gcloud storage` vs. `gsutil`

Google introduced `gcloud storage` as the modern rewrite of `gsutil`:
- **Speed & Concurrency**: Written in Python with optimized native threading, delivering up to **94% faster throughput** for multi-file transfers.
- **Parallel Composite Uploads**: Automatically splits large files into chunks, uploads them concurrently, and recomposes them in GCS.
- **Unified Interface**: Uses consistent `gcloud` flag patterns (`--filter`, `--format`, `--project`) instead of disparate syntax.

---

## 6. Essential CLI Implementation Reference

```bash
# --- 1. Compute Instance Lifecycle ---
# Create VM with specified OS family, machine type, and tags
gcloud compute instances create day9-vm \
    --zone=us-central1-a \
    --machine-type=e2-micro \
    --image-family=debian-12 \
    --image-project=debian-cloud \
    --tags=http-server

# Retrieve internal and external IPs
gcloud compute instances describe day9-vm \
    --zone=us-central1-a \
    --format="yaml(networkInterfaces)"

# Connect securely via SSH
gcloud compute ssh day9-vm --zone=us-central1-a

# Stop and delete VM
gcloud compute instances stop day9-vm --zone=us-central1-a
gcloud compute instances delete day9-vm --zone=us-central1-a --quiet

# --- 2. High-Performance GCS Management ---
# Create bucket in specific location
BUCKET_NAME="devops-vault-$RANDOM"
gcloud storage buckets create gs://${BUCKET_NAME} \
    --location=us-central1 \
    --uniform-bucket-level-access

# Upload and download objects
echo "DevOps CLI Artifact" > artifact.txt
gcloud storage cp artifact.txt gs://${BUCKET_NAME}/release/artifact.txt

# Inspect object contents without downloading
gcloud storage cat gs://${BUCKET_NAME}/release/artifact.txt

# Recursive listing
gcloud storage ls --recursive gs://${BUCKET_NAME}/

# Purge and remove bucket
gcloud storage rm --recursive gs://${BUCKET_NAME}
gcloud storage buckets delete gs://${BUCKET_NAME}
```

---

## 7. High-Yield Interview Q&A

**Q: What is the difference between `gcloud auth login` and `gcloud auth application-default login`?**  
> **A:** `gcloud auth login` acquires user credentials to authorize the `gcloud` CLI itself. `gcloud auth application-default login` writes user credentials to the local well-known credentials file (`~/.config/gcloud/application_default_credentials.json`), allowing Google Cloud client libraries (in Python, Java, Go, etc.) running in your local development environment to automatically detect and use your credentials via Application Default Credentials (ADC).

**Q: How do you extract only the external IP address of a specific VM into a bash script variable without using external parsing tools like `grep`, `awk`, or `jq`?**  
> **A:** Use native `gcloud` `--filter` and `--format` projections:
> ```bash
> VM_IP=$(gcloud compute instances list \
>   --filter="name=my-instance" \
>   --format="value(networkInterfaces[0].accessConfigs[0].natIP)")
> ```

**Q: What are `gcloud` Named Configurations and when would you use them in enterprise operations?**  
> **A:** Named Configurations are isolated profiles containing environment-specific settings (`project`, `compute/region`, `compute/zone`, `core/account`). They allow engineers working across multiple GCP projects and client accounts to switch working context instantly via `gcloud config configurations activate <config-name>` without running `gcloud init` or repeatedly typing `--project` flags.

**Q: Why did Google replace `gsutil` with `gcloud storage`?**  
> **A:** `gcloud storage` offers significant performance improvements (up to 94% faster uploads and downloads) through automatic parallel processing, concurrent uploads of segmented composite objects, and faster checksum calculation algorithms. It also standardizes the command syntax under the main `gcloud` toolchain.
