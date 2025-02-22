## Updating cert-manager Output

```bash
# 1. Add the jetstack helm repo if it's not already added
helm repo add jetstack https://charts.jetstack.io --force-update

# 2. Update the helm repo
helm repo update

# 3. Generate the cert-manager.1.17.0.yaml file
# (replace the version with the desired version)
helm template \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.17.0 \
  --set crds.enabled=true > cert-manager.1.17.0.yaml

# 4. Update the kustomization.yaml file with the new cert-manager.1.17.0.yaml file

# 5. Push the changes to the repo
git add .
git commit -m "Update cert-manager to v1.17.0"
git push

# 6. Wait for the changes to be applied via ArgoCD!
```

