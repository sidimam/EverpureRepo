# EverpureRepo

Welcome to the **EverpureRepo**! This repository serves as a centralized knowledge base and cheatsheet for Everpure commands, configurations, and operational procedures utilized during client deployments, troubleshooting, and daily operations.

## 📖 Overview

In this repository, you will find a curated collection of scripts, Kubernetes manifests, and step-by-step documentation. These resources are designed to streamline client interventions, ensure consistency across different environments, and provide quick access to frequently used commands in the field.

## 🗂️ Key Contents

This repository covers a wide range of cloud-native and storage technologies, including but not limited to:

*   **Managed Kubernetes (AKS & EKS):** Setup, scaling, custom roles, and management procedures.
*   **Portworx by Pure Storage:** Installations, StorageClass migrations (both legacy methods and Portworx snapshots), Disaster Recovery (AsyncDR) setups, Autopilot rules, and performance tuning.
*   **Pure Storage FlashArray (FA):** Advanced configurations, network LACP bonds, VLAN setups, and File Services (NFS) provisioning.
*   **Monitoring & Observability:** Complete Grafana dashboards (Node, Cluster, Volume, Performance) and Prometheus configurations tailored for Portworx and Kubernetes environments.
*   **Application Deployments:** Practical deployment examples using Helm (e.g., WordPress) and StatefulSets (e.g., MongoDB).

## 🚀 How to Use

To keep these procedures readily available on your local machine, clone the repository:

```bash
git clone git@github.com:sidimam/EverpureRepo.git
```

Navigate through the directories (such as the `PX Champion` folder) to find the specific `.md` guides, `.yaml` manifests, or `.sh` scripts you need for your current client engagement.

## 🔄 Updates

This repository is continuously updated. Whenever a new procedure is validated with a client or a new useful command is discovered, it is pushed here to keep the team's toolset sharp and up to date.

