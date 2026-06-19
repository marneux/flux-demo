# Flux Demo — GitOps Multi-Cluster

Projet d'infrastructure GitOps multi-clusters basé uniquement sur des manifests Kubernetes
(sans CLI Flux), inspiré des bonnes pratiques de
[flux2-kustomize-helm-example](https://github.com/fluxcd/flux2-kustomize-helm-example).

## Clusters

| Cluster      | Usage                          |
|--------------|--------------------------------|
| `localstack` | Postes de développement locaux |
| `dev`        | Cluster de dev sur aks   |

## Stack technique

Tous les déploiements via Helm. Charts Bitnami en priorité, sinon chart officiel de l'éditeur.

| Domaine          | Logiciels                                    | Source du chart              |
|------------------|----------------------------------------------|------------------------------|
| Observabilité    | Elasticsearch, Kibana, Fluent Bit            | Bitnami / Elastic            |
| Monitoring       | Prometheus, Grafana                          | prometheus-community (https://prometheus-community.github.io/helm-charts)                      |
| ML / AI          | Langfuse                                     | Langfuse                     |
| Réseau           | Nginx (reverse proxy), Kong (API Gateway)    | Bitnami, Kong                |
| Feature flags    | OpenFeature, Flagd                           | OpenFeature                  |
| Applications     | Microservices Python (Langfuse + Redis)      | Charts custom                |

## Structure du dépôt

```
apps/                   HelmReleases des applications
  hello/                Exemple Go hello-world
  nginx/                Exemple nginx Bitnami
  base/                 Définitions communes (namespace, release)
  localstack/           Overlays Kustomize localstack
  dev/                  Overlays Kustomize dev
infrastructure/         Infrastructure commune
  controllers/          HelmReleases des controllers (CRDs)
  configs/              Ressources Kubernetes (ClusterIssuer, Policy, etc.)
clusters/               Kustomizations Flux par cluster
  localstack/           Point d'entrée localstack
  dev/                  Point d'entrée dev
```

## Conventions GitOps

- Aucune commande Flux CLI — manifests uniquement (`HelmRelease`, `OCIRepository`, `Kustomization`)
- Kustomize pour les overlays et patches par environnement
- `OCIRepository` pour référencer les charts Helm en registre OCI
- `HelmRelease` : préférer `chartRef` pointant vers un `OCIRepository`
- Namespace `flux-system` pour les sources, namespace métier pour chaque application
- Mises à jour de versions par PR Git

## Tests de déploiement

Avant de considérer un travail comme terminé, **tous les manifests doivent être déployés et validés
sur le cluster k3d localstack** (`kubectl` pointe déjà sur ce cluster).

### Procédure de validation

1. Appliquer les manifests avec `kubectl apply -k <chemin>`
2. Vérifier que les `OCIRepository` passent en `Ready=True`
3. Vérifier que les `HelmRelease` passent en `Ready=True`
4. Vérifier que les pods sont `Running`
5. Nettoyer si nécessaire avec `kubectl delete -k <chemin>`

<!-- codebase-memory-mcp:start -->
# Codebase Knowledge Graph (codebase-memory-mcp)

This project uses codebase-memory-mcp to maintain a knowledge graph of the codebase.
ALWAYS prefer MCP graph tools over grep/glob/file-search for code discovery.

## Priority Order
1. `search_graph` — find functions, classes, routes, variables by pattern
2. `trace_path` — trace who calls a function or what it calls
3. `get_code_snippet` — read specific function/class source code
4. `query_graph` — run Cypher queries for complex patterns
5. `get_architecture` — high-level project summary

## When to fall back to grep/glob
- Searching for string literals, error messages, config values
- Searching non-code files (Dockerfiles, shell scripts, configs)
- When MCP tools return insufficient results
<!-- codebase-memory-mcp:end -->
