# argocd

Deploy Argo CD:
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Run port-forward for getting to the dashboard https://127.0.0.1:8080:
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

ID: `admin` Pass:
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

---

### CI/CD Workflow:
```mermaid
flowchart TD
   subgraph localhost
   LocalA(git commit) --> LocalB(git push)
   end
   subgraph Github
   LocalB --> GitHubRepo[GitHub Repo]
   GitHubRepo --> GitHubRepoCI
   subgraph Github Actions
   subgraph GitHubRepoCI[GitHub Repo CI]
   GitHubRepoCIA(docker build) --> GitHubRepoCIB(docker push)
   end
   subgraph ArgoCD[Argo Apps Repo CI]
   ArgoCDA(git commit) -- Update Kubernetes Manifest --> ArgoCDB(git push)
   end
   GitHubRepoCI -- Successful Build --> ArgoCD
   end
   ArgoCD --> ArgoAppsRepo[Argo Apps Repo]
   end
   subgraph Kubernetes[Kubernetes Cluster]
   ArgoWorkflow[ArgoCD] -- Kubernetes Manifest --> Deployment{App Deployment}
   Vault[HashiCorp Vault] -- Secrets --> Deployment
   Volumes[(Database Volumes)] <== Read and Write ==> Deployment
   Deployment --> Caddy
   end
   GitHubRepoCIB --> DockerHub{{DockerHub}}
   ArgoAppsRepo --> ArgoWorkflow
   DockerHub -- Docker Images --> Deployment
   Caddy -- TLS Encryption --> Internet((Internet / Public URL))
```
