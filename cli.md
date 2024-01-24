# cli

Install argocd cli tool:
```bash
brew install argocd
```

Login to agrocd via k8s cluster:
```
argocd login argocd.example.com --core
```

Switch to argocd namespace:
```bash
kubens argocd
```

List all the deployed apps:
```bash
argocd app list
```

Sync app:
```bash
argocd app sync argocd/secrets
```


