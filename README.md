# k8-devsecops GitOps

Desired-state configuration for deploying the FastAPI DevSecOps lab application to Kubernetes with Argo CD and Helm.

## Structure

- `applications/devsecops-api.yaml`: Argo CD Application (bootstrap once)
- `environments/dev/Chart.yaml`: Helm chart metadata
- `environments/dev/values.yaml`: development deployment configuration
- `environments/dev/templates/`: Kubernetes Deployment and Service templates

## Bootstrap

Ensure Argo CD is running in the local kind cluster, then apply the Argo CD Application:

```sh
kubectl apply -f applications/devsecops-api.yaml
kubectl get applications -n argocd
```

Argo CD watches `environments/dev` on `main` and deploys it to the `devsecops` namespace. It will automatically sync and self-heal; pruning is disabled initially for safer lab onboarding.

## Image updates

After the CI workflow publishes a new image to GHCR, update `image.tag` in `environments/dev/values.yaml` to the corresponding commit SHA and commit the change. Argo CD will reconcile the cluster.

The container image must be accessible to the cluster. If the GHCR package is private, configure an imagePullSecret before syncing.
