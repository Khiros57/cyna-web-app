README.md

![Build & Deploy](https://github.com/Khiros57/cyna-web-app/actions/workflows/deploy.yaml/badge.svg)

------

## 🔁 Recréation du cluster Kubernetes SKS (Exoscale)

Ce projet suppose que vous déployez un site vitrine sur un cluster SKS Exoscale. Voici les commandes officielles (inspirées de la documentation Exoscale) pour recréer un cluster complet.

### 📍 Zone : `de-fra-1`

### 1. Créer le cluster SKS
```bash
exo compute sks create cluster-cyna \
    --zone de-fra-1 \
    --service-level pro \
    --nodepool-name cyna-nodepool \
    --nodepool-size 3 \
    --nodepool-security-group sks-security-group
```
###The Exoscale orchestrator then creates a new instance pool with 3 instances on your behalf. By default, the instances sizes are Medium instances with 50 GB disks attached.

### 2. Ajouter un nodepool (3 nœuds standard)(optionnel si la précédente commande par défaut crée 3 instances dans le noeud)
```bash
exo --zone de-fra-1 compute sks nodepool add cluster-cyna \
  --name cyna-nodepool \
  --instance-type standard.small \
  --size 3
```

### 3. Récupérer le kubeconfig pour `kubectl`
```bash
mkdir -p ~/.kube
exo compute sks kubeconfig cluster-cyna kube-admin \
    --zone de-fra-1 \
    --group system:masters > ~/.kube/config
chmod 600 ~/.kube/config
```

### 4. Tester l'accès au cluster
```bash
kubectl get nodes
```

➡️ Une fois ces étapes effectuées, un `git push` vers la branche `main` déclenche le déploiement automatique via GitHub Actions (`.github/workflows/deploy.yaml`).

------

## 🛑 Arrêt et suppression du cluster Exoscale

Pour éviter toute facturation inutile, vous pouvez supprimer proprement votre cluster SKS.

### 1. Lister les nodepools
```bash
exo --zone de-fra-1 compute sks nodepool list cluster-cyna
```

### 2. Supprimer les nodepools (exemple avec "cyna-nodepool")
```bash
exo --zone de-fra-1 compute sks nodepool delete cluster-cyna cyna-nodepool
```

### 3. Supprimer le cluster SKS
```bash
exo --zone de-fra-1 compute sks delete cluster-cyna
```

✅ Ces étapes libèrent les ressources (VMs, volumes, IP) et arrêtent la facturation liée au cluster.
