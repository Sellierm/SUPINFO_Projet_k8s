# Boutique GitOps (Argo CD)

Lab GitOps en équipe de 3 : l'application `boutique` est déployée par Argo CD à partir de ce dépôt. Chacun a son propre cluster k3d, et les trois surveillent ce même dépôt.

**Plus personne ne fait `kubectl apply` sur l'application.** Tout passe par Git : branche → merge request relue par un autre membre → fusion dans `main` → Argo CD applique.

## Règles

- Pas de push direct sur `main` : tout passe par une pull request.
- Personne ne fusionne sa propre pull request.
- Rotation de relecture : P1 relit P2, P2 relit P3, P3 relit P1.
- Avant chaque nouvelle branche : `git switch main && git pull`.

## Rôles et structure

```
.
├── bootstrap/root.yaml    P1 : Application racine (app of apps)
├── argocd/                P1 : Applications plateforme, web, api, donnees
├── plateforme/            P1 : ResourceQuota, LimitRange, NetworkPolicies
├── web/                   P2 : nginx, Service, Ingress, HPA, PDB
├── api/                   P3 : whoami (Deployment, Service)
└── donnees/               P3 : redis (StatefulSet), CronJob compteur
```

## URL du dépôt pour Argo CD

Dans les Applications (`repoURL`), utiliser :

```
https://github.com/Sellierm/SUPINFO_Projet_k8s.git
```

## Démarrage (une fois `argocd/` et `bootstrap/` fusionnés)

```bash
git pull
kubectl apply -f bootstrap/root.yaml
kubectl get applications -n argocd
```

Site : http://boutique.localhost:8080, API : http://boutique.localhost:8080/api
