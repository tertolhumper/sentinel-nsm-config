# sentinel-nsm-config

Sentinel NSM Kubernetes GitOps configuration repository. All cluster state is managed here. Application source code and CI pipelines live in [sentinel-nsm-app](https://github.com/tertolhumper/sentinel-nsm-app).

## Repository Structure

    argocd/           ArgoCD Application definitions
    apps/lemp/        LEMP stack manifests — nginx, PHP-FPM, ConfigMaps
    helm/argocd/      ArgoCD Helm values
    helm/jenkins/     Jenkins Helm values
    helm/nfs/         NFS provisioner Helm values
    helm/prometheus/  kube-prometheus-stack Helm values
    helm/vault/       HashiCorp Vault Helm values

## Deployment Flow

    Push to sentinel-nsm-app
      → Jenkins CI builds PHP-FPM image via Kaniko (no privileged pods)
      → Pushes image to ghcr.io/tertolhumper/sentinel-nsm-app:<build>
      → Jenkins commits updated image tag to apps/lemp/php-fpm-deployment.yaml
      → ArgoCD detects commit and syncs to cluster

    Infrastructure changes (nginx config, replicas, manifests)
      → Commit directly to this repo
      → ArgoCD detects commit and syncs to cluster
      → Jenkins is not involved

## ArgoCD Applications

| Application | Path | Namespace |
|---|---|---|
| sentinel-nsm | helm/argocd | argocd |
| sentinel-php-app | apps/lemp | lemp |

## LEMP Stack Manifests (apps/lemp/)

| File | Purpose |
|---|---|
| nginx-deployment.yaml | nginx Deployment — 4 replicas |
| nginx-service.yaml | nginx NodePort Service (30080) |
| nginx-configmap.yaml | nginx site configuration |
| php-fpm-deployment.yaml | PHP-FPM Deployment — 4 replicas |
| php-app.yaml | PHP app ConfigMap (index.php) |

## Stack

- Kubernetes v1.37.0 — 6-node HA cluster on Proxmox
- Cilium CNI
- HashiCorp Vault HA — secrets management
- External Secrets Operator
- kube-prometheus-stack — Prometheus, Grafana, Alertmanager
- ArgoCD v3.5.4 — GitOps continuous delivery
- Jenkins 2.580.2 — CI pipelines with Kaniko builds
- LEMP — nginx, PHP-FPM, MariaDB
- Cloudflare Tunnel — external access at sentinel-nsm.com
