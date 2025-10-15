## Flux “pull” vs Flux “image automation”

### 1. Flux “pull” model (standard reconciliation)

- You define a **GitRepository** CR that points to your Git repo.
    
- Flux controllers (source-controller + kustomize-controller/helm-controller) will **pull from Git** every few minutes (interval configurable).
    
- It applies manifests from Git into the cluster.
    
- If cluster drifts (someone edits a Deployment manually), Flux resets it to match Git.
    
- This is the classic GitOps model: **Git → Cluster**, but Git must already contain the _desired version_ of everything.
    

### 2. Flux “image automation” (keeping Git up-to-date)

- Sometimes you want deployments to update automatically when a new container image is pushed (e.g., new `v1.2.3` tag).
    
- For this, Flux adds **Image Automation Controller**:
    
    - **Image Repository CR**: watches a registry for new image tags.
        
    - **Image Policy CR**: defines which tag pattern to pick (e.g., “highest semver 1.x” or “latest”).
        
    - **Image Update Automation CR**: when a new valid tag appears, Flux will **commit back to the Git repo** and update the manifest (`Deployment` YAML, Helm values, etc.).
        
    - Then the normal reconciliation loop applies that updated manifest.
        

So:

- **Flux pull**: cluster is updated to match Git.
    
- **Flux image automation**: Git itself is updated to match new images → then cluster pulls it.