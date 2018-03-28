---
title: Mise à l’échelle d’un cluster Azure Container Service pour Machine Learning | Microsoft Docs
description: Mise à l’échelle d’un cluster ACS - Mise à l’échelle automatique et mise à l’échelle statique ; mise à l’échelle du nombre de nœuds dans le cluster
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 58709b5a5b060552e1bc349a6f720d3f45348ba0
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Mise à l’échelle du cluster pour gérer le débit des services web

## <a name="why-scale-the-cluster"></a>Pourquoi procéder à la mise à l’échelle du cluster ?

La mise à l’échelle du cluster Azure Container Service (ACS) constitue un moyen efficace d’optimiser le débit des services tout en limitant la taille du cluster au minimum afin de réduire les coûts. 

Pour mieux comprendre la mise à l’échelle automatique, considérez l’exemple ci-après d’un cluster exécutant trois services web :

![Exemple : Trois services sur un cluster](media/how-to-scale-clusters/three-services.png)

Les pics de demande varient selon ces services : en période de pic de demande, le Service 1 (trait bleu) nécessite 40 pods, le Service 2 (trait orange) en requiert 38, et le Service 3 (trait gris) en exige 50. Si vous réservez la capacité maximale requise pour chacun de ces services individuellement, ce cluster nécessitera au moins 40 + 38 + 50 = 128 pods au total.

Toutefois, considérons l’utilisation réelle des pods à un instant donné, représentée par le trait noir en pointillé dans le graphique. Dans ce cas précis, le *nombre maximal de pods utilisés à tout moment* est de 64, à 20:00, pendant le pic de demande du Service 3. À cette heure-là, le Service 3 utilise 50 pods, mais le Service 2 n’en utilise que 9, et le Service 1 seulement 5. N’oubliez pas que nous parlons de *l’utilisation maximale* de ce cluster. Cela signifie qu’à aucun moment le cluster n’utilise plus de 64 pods, soit la moitié de la valeur totale requise de 128 pods que nous avons calculée précédemment pour les trois services mis à l’échelle indépendamment de l’utilisation maximale.

En réattribuant les pods du cluster, autrement dit, en procédant à une remise à l’échelle, pour répondre à la demande de chaque service à un instant donné, plutôt que d’exiger simplement des ressources suffisantes pour le pic de demande de la totalité des services, vous pouvez réduire la taille de votre cluster. Dans cet exemple simple, la mise à l’échelle automatique abaisse le nombre de pods requis de 128 à 64, et diminue ainsi de moitié la taille de cluster nécessaire.

La mise à l’échelle du nombre de pods est une opération relativement rapide, qui nécessite moins d’une minute, de sorte qu’elle n’a pas d’incidence significative sur la réactivité du service.

> [!NOTE]
> La mise à l’échelle d’un cluster ne permet pas de résoudre les problèmes de latence des requêtes. À des fins d’opérationnalisation, une montée en puissance devrait augmenter le nombre d’opérations réussies et diminuer le nombre d’erreurs d’indisponibilité du service. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Mise à l’échelle des services web sur votre cluster ACS

L’option de configuration de cluster disponible dans l’interface de ligne de commande de gestion des modèles configure par défaut deux agents et un pod dans votre environnement. Cette option installe également l’interface de ligne de commande Kubernetes.

Vous pouvez mettre à l’échelle les services web que vous avez déployés dans ACS en ajustant les éléments suivants :

* Nombre de nœuds d’agent dans le cluster
* Nombre de réplicas de pods Kubernetes en cours d’exécution sur les nœuds d’agent

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>Mise à l’échelle du nombre de nœuds dans le cluster

La commande ci-après définit le nombre de nœuds d’agent dans le cluster :

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

L’exécution de cette opération nécessite quelques minutes. Pour plus d’informations sur la mise à l’échelle du nombre de nœuds dans le cluster, consultez l’article [Mettre à l’échelle des nœuds d’agent dans un cluster Container Service](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>Mise à l’échelle du nombre de réplicas de pods Kubernetes dans un cluster
 
Vous pouvez mettre à l’échelle le nombre de réplicas de pods affecté au cluster à l’aide de l’interface CLI Azure Machine Learning ou du [tableau de bord Kubernetes] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Pour plus d’informations sur les réplicas de pods Kubernetes, consultez la documentation relative aux [pods Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/).

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Mise à l’échelle d’un cluster avec l’interface de ligne de commande Azure Machine Learning

Vous disposez de deux méthodes pour mettre à l’échelle un cluster à l’aide de l’interface de ligne de commande :

- Autoscale
- Mise à l’échelle statique

La mise à l’échelle automatique est activée par défaut lorsque le service est créé, et constitue dans la plupart des cas la méthode de mise à l’échelle recommandée.

##### <a name="autoscale"></a>Autoscale

La commande ci-après active la mise à l’échelle automatique et définit le nombre minimal et maximal de réplicas pour le service.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Par exemple, la définition de la commande `autoscale-min-replicas` sur la valeur 5 créera cinq réplicas. Pour définir un nombre de réplicas optimal pour le service web, commencez par configurer ce nombre sur une valeur telle que 10, puis surveillez le nombre de messages d’erreur 503. Ajustez alors le nombre de réplicas en conséquence.


| Nom du paramètre | type | Description |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | booléenne | Indique si la mise à l’échelle automatique est activée. Valeur par défaut : true. |
| `autoscale-min-replicas` | integer | Indique le nombre minimal de pods. Cette valeur doit être supérieure ou égale à 0. Valeur par défaut : 1. |
| `autoscale-max-replicas` | integer | Indique le nombre maximal de pods. Cette valeur doit être supérieure ou égale à 1. Si la valeur du paramètre autoscale-max-replicas est inférieure à celle du paramètre autoscale-min-replicas, le paramètre autoscale-max-replicas sera ignoré. Valeur par défaut : 10 |
| `autoscale-refresh-period-seconds` | integer | Indique la durée en secondes entre deux actualisations de mise à l’échelle automatique. Valeur par défaut : 1. |
| `autoscale-target-utilization` | integer | Indique le pourcentage d’utilisation ciblé par la mise à l’échelle automatique. Cette valeur est comprise entre 1 et 100. Valeur par défaut : 70. |

La mise à l’échelle automatique vérifie que les deux conditions ci-après sont remplies :

1. Le pourcentage d’utilisation ciblé est atteint.
2. La mise à l’échelle respecte toujours les valeurs minimale et maximale.

Les services d’un cluster sont en concurrence pour bénéficier des ressources du cluster. Un service ayant fait l’objet d’une mise à l’échelle automatique intensifie son utilisation des ressources du cluster lorsque son nombre de requêtes par seconde (RPS) augmente, et libère progressivement des ressources dès que son RPS diminue. Les ressources du cluster sont acquises à la demande du service aussi longtemps que ces ressources existent.

Pour plus d’informations sur les paramètres de mise à l’échelle automatique, consultez l’article [Informations de référence sur l’interface CLI de gestion des modèles](model-management-cli-reference.md).

##### <a name="static-scale"></a>Mise à l’échelle statique

La méthode de mise à l’échelle statique est généralement déconseillée, car contrairement à la mise à l’échelle automatique, elle ne permet pas de réduire la taille du cluster. Toutefois, l’utilisation de la mise à l’échelle statique est préconisée dans certains cas. Par exemple, lorsqu’un cluster est dédié à un seul service, la mise à l’échelle automatique n’offre aucun avantage, puisque toutes les ressources du cluster doivent être attribuées à ce service.

Pour procéder à la mise à l’échelle statique d’un cluster, vous devez désactiver la mise à l’échelle automatique. Pour désactiver la mise à l’échelle automatique, utilisez la commande suivante :

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Une fois la mise à l’échelle automatique désactivée, la commande ci-après met directement à l’échelle le nombre de réplicas pour un service.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Pour plus d’informations sur la mise à l’échelle du nombre de nœuds dans le cluster, consultez l’article Mettre à l’échelle des nœuds d’agent dans un cluster Container Service.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Mise à l’échelle du nombre de réplicas à l’aide du tableau de bord Kubernetes

Au niveau de la ligne de commande, entrez la commande suivante :

```
kubectl proxy
```

Dans Windows, l’emplacement d’installation de Kubernetes n’est pas automatiquement ajouté au chemin d’accès. Commencez par accéder au dossier d’installation :

```
c:\users\<user name>\bin
```

Une fois que vous avez exécuté la commande, vous devriez obtenir le message d’information suivant :

```
Starting to serve on 127.0.0.1:8001
```

Si le port est déjà en cours d’utilisation, vous voyez apparaître un message semblable à l’exemple suivant :

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Vous pouvez spécifier un autre numéro de port à l’aide du paramètre *--port*.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Une fois que vous avez démarré le serveur du tableau de bord, ouvrez un navigateur et entrez l’URL suivante :

```
127.0.0.1:<port number>/ui
```

Dans l’écran principal du tableau de bord, cliquez sur **Deployments** (Déploiements) dans la barre de navigation gauche. Si le volet de navigation ne s’affiche pas, sélectionnez l’icône ![Menu composé de trois lignes horizontales courtes](media/how-to-scale-clusters/icon-hamburger.png) dans le coin supérieur gauche.

Recherchez le déploiement à modifier, cliquez sur l’icône ![Icône de menu composée de trois points verticaux](media/how-to-scale-clusters/icon-kebab.png) sur la droite, puis cliquez sur **View/edit YAML** (Afficher/modifier YAML).

Dans l’écran Edit deployment (Modifier le déploiement), recherchez le nœud *spec*, modifiez la valeur *replicas* (réplicas), puis cliquez sur **Update** (Mettre à jour).
