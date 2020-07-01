## Kubernetes

----

### Kubernetes concepts

Kubernetes est un orchestrateur de containers. Son rôle est de faire tourner des containers dans un cluster

----

### Qu'est-ce que Kubernetes peut faire ?

- Démarrer 5 containers de l'image ```spring-projects/spring-petclinic:2.3.1```
- Mettre un load-balancer interne devant ces containers
- Démarrer 10 containers de l'image ```elasticsearch/elasticsearch:7.7.0```
- Mettre un load-balancer externe devant ces containers

----

### Qu'est-ce que Kubernetes peut faire ?

- Faire grossir le cluster et ajouter des containers pour lisser une charge grandissante
- Déployer une nouvelle release, par exemple ```elasticsearch/elasticsearch:7.8.0```
- Continuer à répondre aux requêtes durant la mise à jour

----

### Ce que Kubernetes peut aussi faire ?

- Auto-scaling basique
- Blue/green deployment, Canary deployment
- Batch jobs, mais aussi des services longs

----

### Ce que Kubernetes peut aussi faire ?

- Faire tourner des services stateful (bases de données, etc.)
- Gérer finement les droits : *qui* peut faire *quoi* sur *quelles* ressources
- Intégrer des services externes (catalogue de services)
- Automatiser des opérations complexes (operators)

----

### Orchestrateur de containers

Afin de garantir qu'un container fonctionne, il est nécessaire d'avoir un orchestrateur de container

Celui-ci garanti que le container est toujours en fonctionnement, même si les noeuds du cluster se mettent à jour ou tombent en panne

----

### Kubernetes architecture

<img src="https://training.codeforcloud.tech/images/k8s-arch1.png" width="70%" />

----

### Kubernetes architecture

![Image](https://d33wubrfki0l68.cloudfront.net/7016517375d10c702489167e704dcb99e570df85/7bb53/images/docs/components-of-kubernetes.png)

----

### Kubernetes architecture : les noeuds

Les noeuds qui exécutent les containers font tourner une collection de service :
- un moteur de container (généralement Docker)
- kubelet (l'agent)
- kube-proxy (un service de gestion du réseau)

----

### Kubernetes architecture : le "plan de contrôle"

Le "maître" est le cerveau de Kubernetes. Il fait tourner plusieurs services :
- l'API server (le point d'entrée pour tout !)
- les services principaux comme le *scheduler* et le *control manager*
- ```etc``` (une base de données distribuée, la BDD de Kubernetes)

----

### Les ressources de Kubernetes

L'API de Kubernetes définit beaucoup d'objets appelés des *ressources*

Ces ressources sont organisés par type, appelé des ```Kind``` (dans l'API)

----

### Les ressources de Kubernetes

Quelques exemples de ressources :
- ```node``` : une machine physique ou virtuel dans le cluster
- ```pod``` : un groupe de container tournant sur le même noeud
- ```service``` : un point d'accès pour se connecter à un ou plusieurs pods
- ```namespace``` : une isolation des ressources
- ```secret``` : un groupe de données sensibles

----

### Pods

Un pod est un regroupement de container au sein d'une même unité

<img src="https://d33wubrfki0l68.cloudfront.net/5cb72d407cbe2755e581b6de757e0d81760d5b86/a9df9/docs/tutorials/kubernetes-basics/public/images/module_03_nodes.svg" width="50%" />

----

### Deployment

Un déploiement est la manière de déployer un pod

Un déploiement est déclaratif, c'est-à-dire que Kubernetes va s'assurer que les pods sont toujours au bon nombre demandés

<img src="https://d33wubrfki0l68.cloudfront.net/152c845f25df8e69dd24dd7b0836a289747e258a/4a1d2/docs/tutorials/kubernetes-basics/public/images/module_02_first_app.svg" width="30%" />

----

### Services

Un service est la manière d'accéder aux pods

Un service expose une adresse IP, ainsi qu'un port

Il joue le role de load-balancer interne

<img src="https://d33wubrfki0l68.cloudfront.net/cc38b0f3c0fd94e66495e3a4198f2096cdecd3d5/ace10/docs/tutorials/kubernetes-basics/public/images/module_04_services.svg" width="30%" />

----

### Ingress

L'ingress sert à exposer à l'extérieur du cluster un service

Cela se représente traditionnellement par un load balancer externe, une URL ainsi que du SSL

----

### Cluster autoscaler

Il sert à augmenter le nombre de noeuds du cluster automatiquement quand celui-ci n'a plus assez de ressource, ou que les noeuds sont sous utilisés

----

### Daemonset

Un daemonset garanti qu'un noeud fasse tourner un ou plusieurs pods automatiquement

Pratique quand il faut de la supervision (datadog, ...), du logging (fluentd, ...) ou du stockage distribué (ceph, ...)

----

### Storageclass

Définie une classe de stockage basée sur la qualité de service, la stratégie de backup ou une policy définie par l'administrateur

----

### Persistent volume

Abstrait la notion de StorageClass pour fournir une API de pilotage de volume

Basé sur un système de plugin (iSCSI, NFS, ...)

----

### Persistent volume claim

Requête de stockage par l'utilisateur qui va se greffer sur un PV

Demandée par un pod

----

### Configmap

Défini une configuration à appliquer sur un pod

```yaml
apiVersion: v1
data:
  redis-config: |
    maxmemory 2mb
    maxmemory-policy allkeys-lru
kind: ConfigMap
metadata:
  creationTimestamp: 2016-03-30T18:14:41Z
  name: example-redis-config
  namespace: default
  resourceVersion: "24686"
  selfLink: /api/v1/namespaces/default/configmaps/example-redis-config
  uid: 460a2b6e-f6a3-11e5-8ae5-42010af00002
```

----

### Secret

Un secret contient des informations sensibles qui ne doit pas être exposé et accessible uniquement qu'à un pod

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```

----

### Interagir avec Kubernetes

Pour interagir avec Kubernetes, il existe une CLI ```kubectl```

```kubectl``` intègre un certain nombres de commandes pour voir les ressources du cluster

----

### Interagir avec Kubernetes

- kubectl get pods : voir tous les pods sur le cluster
- kubectl get ns : voir tous les namespaces
- kubectl logs -l ... --tail N -f : voir les logs d'un objet
- kubectl scale ... --replicas 8 : augmenter le nombre de réplicas d'un pod

----

### Offres managés cloud

La plupart des CSP offrent aujourd'hui des Kubernetes managés, comme par exemple AKS, EKS, GKE
----

## Déploiement avec Kubernetes

----

### Rolling update

<img src="https://d33wubrfki0l68.cloudfront.net/30f75140a581110443397192d70a4cdb37df7bfc/fa906/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates1.svg" width="50%" />

----

### Rolling update

<img src="https://d33wubrfki0l68.cloudfront.net/678bcc3281bfcc588e87c73ffdc73c7a8380aca9/703a2/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates2.svg" width="50%" />

----

### Rolling update

<img src="https://d33wubrfki0l68.cloudfront.net/9b57c000ea41aca21842da9e1d596cf22f1b9561/91786/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates3.svg" width="50%" />

----

### Rolling update

<img src="https://d33wubrfki0l68.cloudfront.net/6d8bc1ebb4dc67051242bc828d3ae849dbeedb93/fbfa8/docs/tutorials/kubernetes-basics/public/images/module_06_rollingupdates4.svg" width="50%" />

----

### Liveness and readiness probes

```yaml
livenessProbe:
    exec:
      command:
      - cat
      - /tmp/healthy
    initialDelaySeconds: 5
    periodSeconds: 5
```

----

### Liveness and readiness probes

```yaml
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

----

## Day 2 : ops sur Kubernetes

----

### Logging
#### Sur un noeud

![Image](https://d33wubrfki0l68.cloudfront.net/59b1aae2adcfe4f06270b99a2789012ed64bec1f/4d0ad/images/docs/user-guide/logging/logging-node-level.png)

----

### Logging
#### Avec un agent

![Image](https://d33wubrfki0l68.cloudfront.net/2585cf9757d316b9030cf36d6a4e6b8ea7eedf5a/1509f/images/docs/user-guide/logging/logging-with-node-agent.png)

----

### Logging
#### En streaming

![Image](https://d33wubrfki0l68.cloudfront.net/c51467e219320fdd46ab1acb40867b79a58d37af/b5414/images/docs/user-guide/logging/logging-with-streaming-sidecar.png)

----

### Logging
#### Avec un sidecar

![Image](https://d33wubrfki0l68.cloudfront.net/d55c404912a21223392e7d1a5a1741bda283f3df/c0397/images/docs/user-guide/logging/logging-with-sidecar-agent.png)

----

### Logging
#### Exposition des logs

![Image](https://d33wubrfki0l68.cloudfront.net/0b4444914e56a3049a54c16b44f1a6619c0b198e/260e4/images/docs/user-guide/logging/logging-from-application.png)

----

### Monitoring et alertes

Le monitoring d'une application se fait avec des outils tierces, comme Prometheus par exemple

----

### Monitoring et alertes

![Image](https://camo.githubusercontent.com/37141c73c72e1e73ec0669ae6451d834ebe1cd2e/68747470733a2f2f7777772e63616d696c2e6f72672f636f6e74656e742f696d616765732f323031372f636c75737465722e706e67)

----

## Installation de services tiers

----

### Helm

Helm est le package manager de Kubernetes

Déploit des paquets appelés chart, contenant l'architecture complète du logiciel

C'est l'équivalent de apt-get ou yum

----

### Helm architecture

![Image](https://image.slidesharecdn.com/k8shelm-170706065852/95/helm-application-deployment-management-for-kubernetes-13-638.jpg?cb=1499324812)

----

### Service Mesh

Un service mesh est un service qui assure la communication entre différentes versions d'une même application

Il sert aussi pour tout ce qui est distributed tracing et debugging

----

### Istio architecture

<img src="https://478h5m1yrfsa3bbe262u7muv-wpengine.netdna-ssl.com/wp-content/uploads/2018/02/istio_sysdig_arch_overview.png" width="70%" />