![Kustomize Build](https://github.com/tinycloud-labs/flux/actions/workflows/kustomize-build.yml/badge.svg) 

Managed Kubernetes with FluxCD using an app-centric base/overlay structure. Bases and overlays are defined per application to keep deployments easy to reason about. There're some intentional non-DRY configuration to favor clarity and locality over abstraction (e.g. when working on `apps/foo/` configs, I don't want to jump between directories or mentally reconstruct how everything is wired together)

### Top-level view
```
apps/                # reusable applications definitions
    ├── ...
clusters/            # environment-specific wiring
    ├── dev/             # monitored by a Flux instance on a K3s-dev
    └── prod/            # monitored by a Flux instance on a K3s-prod
```

### Apps are reusable (not env aware)
```
apps/
├── foo/
│   ├── base/
│   │   ├── helm-values.yaml       # generic Helm values
│   │   ├── release.yaml
│   │   └── kustomization.yaml
│   └── overlays/
│       ├── dev/
│       │   ├── patch.yaml          # env-specific Helm values 
│       │   └── kustomization.yaml
│       └── prod/
│           ├── patch.yaml          # env-specific Helm values 
│           └── kustomization.yaml
└── ...
```

### Clusters call the needed releases
```
clusters/
└── prod/
    ├── flux-system/
    └── apps/
        ├── kustomization.yaml     # calls the apps from /apps
        └── common-patch.yaml      # applies cluster-specific params
```
