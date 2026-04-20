
## Flux "pull" vs Flux "image automation"

### 1. Flux "pull" model (standard reconciliation)

- You define a **GitRepository** CR that points to your Git repo.
- Flux controllers (source-controller + kustomize-controller/helm-controller) will **pull from Git** every few minutes (interval configurable).
- It applies manifests from Git into the cluster.
- If the cluster drifts (someone edits a Deployment manually), Flux resets it to match Git.
- This is the classic GitOps model: **Git → Cluster**, but Git must already contain the desired manifest versions.

### 2. Flux "image automation" (keeping Git up-to-date)

- Sometimes you want deployments to update automatically when a new container image is pushed (for example, a new `v1.2.3` tag).
- For this, Flux adds the Image Automation Controller:
    - **ImageRepository** CR: watches a registry for new image tags.
    - **ImagePolicy** CR: defines which tag pattern to pick (e.g., highest semver 1.x or latest).
    - **ImageUpdateAutomation** CR: when a new valid tag appears, Flux can commit back to the Git repo and update manifests (Deployment YAML, Helm values, etc.).
    - Then the normal reconciliation loop applies the updated manifests to the cluster.

So:

- **Flux pull**: cluster is updated to match Git.
- **Flux image automation**: Git is updated to match new images, then the cluster pulls those updates.