# Cluster Management

This repository manages Kubernetes manifests for application deployments using `Kustomize`, `Flux` and
`External Secrets Operator (ESO)`.

## Getting Started

### 1. Install Flux

```shell
brew install fluxcd/tap/flux
# or
curl -s https://fluxcd.io/install.sh | sudo bash
```

### 2. Create Namespace

```shell
kubectl create ns flux-system
```

### 3. Bootstrap Cluster

Assuming the repository is hosted in GitHub and _kube_ context is set:

```shell
flux bootstrap github --owner=<owner> --repository=cluster-management --path=flux/<environment> --personal
```

### 4. Install External Secrets Operator (ESO)

Follow the [official ESO installation guide](https://external-secrets.io/latest/introduction/getting-started/):

```shell
helm repo add external-secrets https://charts.external-secrets.io
helm install external-secrets external-secrets/external-secrets -n default
```

**Flux** will now reconcile:

- All applications and External Secrets in `/applications`
- The Gateway API definitions in `/gateways`
- External Secret Store in `/secret-store`

---

## Secrets Management

External Secrets Operator (ESO) fetches secrets from external providers and creates Kubernetes Secrets automatically.

Secrets are not stored in the repository, only the ExternalSecret manifests.

### Tooling

- [Flux](https://fluxcd.io)
- [Kustomize](https://kubectl.docs.kubernetes.io/pages/app_management/introduction.html)
- [External Secrets Operator](https://external-secrets.io/latest/)

---

## Repository structure

```
cluster-management/
├── applications/             # Application manifests (grouped by repository/name)
│   └── <app-name>/
│       ├── base/             # Base deployment + service
│       └── <environment>/    # ConfigMap, SealedSecret, env-specific values
│
├── flux/                     # Flux source reference
│   ├── <environment>/        # Flux Kustomizations per environment
│
├── gateways/                 # Gateway API definitions
│
├── secret-store/             # External Secret Store (ESO) source reference
│   └── <environment>/        # SecretStore definition per environment
```
