# Deployments

The declarative definition of all resource/infra deployments

## Project Structure

This repository contains declarative definitions for all resource and infrastructure deployments. The project is structured as follows:

*   `application-sets/`: Contains ArgoCD ApplicationSets that define how applications are deployed across multiple clusters.
*   `applications/`: Contains individual application definitions, including deployments, services, and other Kubernetes resources, managed by ArgoCD.
*   `core-services/`: Contains definitions for core services, such as external secrets, which are essential for the platform's operation.

## ArgoCD and GitOps

ArgoCD is a GitOps continuous delivery tool for Kubernetes that automates application deployments by synchronizing the cluster state with the desired state defined in a Git repository, ensuring consistency and reliability.

### Sync Waves

Argo CD uses sync waves to manage the order of resource deployment, ensuring interdependent components are applied in sequence. Lower wave numbers are deployed before higher ones.

#### Sync Phases and Waves

Argo CD organizes resources by phase (pre-sync, sync, post-sync) and wave. Resources in each phase are ordered by wave value, Kubernetes kind, and name. Argo CD applies resources in the lowest wave that has out-of-sync or unhealthy resources.

#### Sync Wave Phases

*   PreSync: Executes before the main sync operation (e.g., database migrations).
*   Sync: Deploys application components, respecting dependencies.
*   PostSync: Executes after the main sync operation (e.g., cleanup, notifications).
*   SyncFail: Executes if the sync process fails.

#### Best Practices

*   Plan waves carefully based on resource dependencies.
*   Use negative waves for pre-deployment steps and positive waves for post-deployment steps.
*   Keep the wave structure simple.

### Phase and Sync Wave Strategy

The following phase and sync wave strategy should be followed to ensure that core infrastructure and services are deployed before applications, and that post-deployment tasks are executed only after the main application components are running:

*   PreSync Phase:
    *   `-n to -1`: Infrastructure components (e.g., namespaces, CRDs)
*   Sync Phase:
    *   `0 to 99`: Core services (e.g., external secrets)
    *   `100 to 499`: Applications
*   PostSync Phase:
    *   `500+`: Post-deployment tasks (e.g., notifications)

## Leaving Room For Imperativeness

It may be desired to leave room for some imperativeness/automation, and not have everything defined in your Git manifests. For example, if you want the number of your deployment's replicas to be managed by a Horizontal Pod Autoscaler, then you would not want to track replicas in Git.

Example: Don't include replicas (`spec.replicas`) in a deployment manifest if you want replicas to be controlled by HPA

## Sync Policy Best Practices

*   Use automated syncPolicy for ArgoCD apps with `prune=true` and `selfHeal=true` for most applications.
*   For applications that require manual approval, remove the automated sync policy.
*   Use retry to handle transient errors.