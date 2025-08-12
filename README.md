# CCGC5504 Final Exam – GitOps (N01735066)

This repo contains a minimal app + CI + Helm + ArgoCD setup required by the exam.

## Structure
```
app/                      # NGINX site with student ID
  Dockerfile
  index.html
deploy/
  helm/                   # Helm chart
    Chart.yaml
    values.yaml
    templates/
      deployment.yaml
      service.yaml
  argocd/
    application.yaml      # ArgoCD Application (update repoURL)
.github/workflows/
  docker-build.yml        # CI to build/push & update Helm tag
```

## Fill in placeholders
- Replace `YOUR_DOCKERHUB_USERNAME` in:
  - `.github/workflows/docker-build.yml`
  - `deploy/helm/values.yaml`
- Replace `YOUR_GITHUB_USERNAME` in `deploy/argocd/application.yaml`.

## One-time setup
1. Create a public repo named **N01735066_CCGC5504_FINAL_EXAM**.
2. Add GitHub Secrets in the repo:
   - `DOCKER_USERNAME` → your Docker Hub username
   - `DOCKER_PASSWORD` → your Docker Hub password or token
3. Push this project to `main` branch.

## CI (GitHub Actions)
- On every push to `main`:
  - Builds image from `app/`
  - Pushes two tags: `latest` and the commit SHA
  - Updates `deploy/helm/values.yaml` with the new SHA and pushes back
- This change triggers ArgoCD to deploy the new image.

## k3s + ArgoCD quick start (local or VM)
```bash
curl -sfL https://get.k3s.io | sh -
kubectl get nodes

kubectl create ns argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

kubectl port-forward -n argocd svc/argocd-server 8080:443 &
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d ; echo
```

## Register the app in ArgoCD
- In ArgoCD UI → **New App** or apply:
```bash
kubectl apply -f deploy/argocd/application.yaml
```
- Sync and verify:
```bash
kubectl get pods,svc
curl http://127.0.0.1:30080
```

## Screenshots to capture (Deliverables)
1. GitHub repo URL and tree view showing files.
2. GitHub Actions run (green) with logs of build & push.
3. Docker Hub repository `nginx-n01735066` showing tags (`latest` & SHA).
4. ArgoCD dashboard Application `nginx-n01735066` in Synced/Healthy.
5. `kubectl get all` output in terminal.
6. Browser or `curl` showing the page with **N01735066**.
7. Grafana dashboard panel (if installed) showing NGINX metrics.
