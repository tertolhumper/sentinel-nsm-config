# sentinel-nsm-config

Sentinel-NSM Kubernetes GitOps configuration repository.

## Overview

This repository contains all GitOps-managed configuration for the Sentinel-NSM Kubernetes cluster. Application source code and CI pipelines live in [sentinel-nsm-app](https://github.com/tertolhumper/sentinel-nsm-app).

## Components

- **Argo CD** – Continuous delivery and GitOps reconciliation against this repository.
- **Helm** – Kubernetes application and infrastructure configuration via chart values.
- **Kubernetes manifests** – Application resources, including the PHP application ConfigMap.

## Repository Structure

```text
argocd/       Argo CD Application definitions
apps/lemp/    PHP application ConfigMap
helm/         Helm chart values and configurations
```

## Deployment Flow

```text
[sentinel-nsm-app]                    [sentinel-nsm-config]
Code push → Jenkins CI                Config push → Argo CD
            ↓                                       ↓
            Build & test                            Sync Helm values / manifests
            ↓                                       ↓
            Push image → GHCR                       Kubernetes cluster
```

Jenkins handles all CI tasks: building, testing, and pushing container images to GHCR. Argo CD watches this repository and reconciles the cluster state on every config push.
