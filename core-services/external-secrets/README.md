## Updating External Secrets Output

```bash
# 1. Add the external-secrets helm repo if it's not already added
helm repo add external-secrets https://charts.external-secrets.io --force-update

# 2. Update the helm repo
helm repo update

# 3. Generate the external-secrets.0.14.2.yaml file
# (replace the version with the desired version)
helm template \
  external-secrets external-secrets \
  --repo https://charts.external-secrets.io \
  --namespace external-secrets \
  --version 0.14.2 \
  --set installCRDs=true > external-secrets.0.14.2.yaml

# 4. Update the kustomization.yaml file with the new external-secrets.0.14.2.yaml file

# 5. Push the changes to the repo
git add .
git commit -m "Update external-secrets to v0.14.2"
git push

# 6. Wait for the changes to be applied via ArgoCD!
``` 