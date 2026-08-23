# Cloud-Native Trading Stocks App Infrastructure (AWS EKS + Aurora MySQL)

An end-to-end Infrastructure as Code (IaC) implementation using **Terraform** to provision a resilient AWS foundation (VPC, Amazon EKS, AWS Aurora Serverless v2 MySQL, AWS Secrets Manager) and deploy a containerized multi-tier Trading Stocks web application.

---

## Architecture Overview

The infrastructure provisions the following resources across two Availability Zones in `us-west-2`:

* **Custom Amazon VPC:** Multi-tier subnet topology (Public, Private, Intra) with NAT Gateways for isolated pod routing.
* **Amazon EKS Cluster (v1.31):** Managed node groups running Kubernetes workers across private subnets.
* **Amazon Aurora Serverless v2 (MySQL 8.0):** Auto-scaling relational database backend securely integrated inside private subnets.
* **AWS Secrets Manager:** Centralized credential management for automated database bootstrapping.
* **NGINX Ingress Controller:** Helm-managed Layer 7 routing directing traffic to the frontend and backend API services.
* **Architecture Topologies Supported:**
  * **Arch 1 (Ingress Routing):** Dual-path routing where the NGINX Ingress routes UI traffic to the React frontend and `/api/*` directly to the Go Stocks API service.
  * **Arch 2 (Internal Service-to-Service):** UI proxy routing where external traffic hits the frontend, which handles internal DNS calls to the backend API inside the cluster.

---

## Repository Structure

```text
.
├── main.tf                    # Root orchestration for VPC, EKS, RDS, Helm & Apps
├── outputs.tf                 # Cluster endpoints and validation commands
├── modules/
│   ├── aurora/                # Aurora MySQL serverless module and schema setup
│   └── secretsmanager/        # Database credentials management
└── k8s/
    └── archs/
        ├── arch1/             # Architecture 1 Kubernetes manifests and install scripts
        └── arch2/             # Architecture 2 Kubernetes manifests and install scripts
