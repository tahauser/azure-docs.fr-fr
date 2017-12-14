---
title: "Contrôler la validité d’une mise en production avec Vamp sur un cluster DC/OS Azure"
description: "Comment utiliser Vamp pour contrôler la validité de services de mise en production et appliquer un filtrage de trafic intelligent sur un cluster de contrôleur de domaine/système d’exploitation Azure Container Service"
services: container-service
author: gggina
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: e23b316311ff88d1587da8d5ef777b91bfe0a2e9
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Contrôler la validité de microservices de mise en production avec Vamp sur un cluster de contrôleur de domaine/système d’exploitation Azure Container Service

Dans cette procédure pas à pas, nous configurons Vamp sur Azure Container Service avec un cluster de contrôleur de domaine/système d’exploitation. Nous contrôlons la validité de la mise en production du service de démonstration Vamp « sava », puis résolvons une incompatibilité du service avec Firefox en appliquant un filtrage de trafic intelligent. 

> [!TIP] 
> Dans cette procédure pas à pas, Vamp s’exécute sur un cluster de contrôleur de domaine/système d’exploitation, mais vous pouvez également utiliser Vamp avec Kubernetes en tant qu’orchestrateur.
>

## <a name="about-canary-releases-and-vamp"></a>À propos du contrôle de la validité des mises en production et de Vamp


Le [contrôle de la validité des mises en production](https://martinfowler.com/bliki/CanaryRelease.html) est une stratégie de déploiement intelligent adoptée par des entreprises innovantes telles que Netflix, Facebook et Spotify. Cette approche est intéressante, car elle réduit les problèmes, introduit des filets de sécurité et stimule l’innovation. Alors pourquoi toutes les entreprises ne l’adoptent-elles pas ? L’extension d’un pipeline d’intégration continue/de livraison continue en vue d’inclure des stratégies de contrôle de validité ajoute de la complexité et nécessite une connaissance et une expérience approfondies du DevOps. Cela suffit à bloquer les organisations de taille modeste avant même la prise en main. 

[Vamp](http://vamp.io/) est un système open source conçu pour faciliter cette transition et apporter les fonctionnalités de contrôle de validité des mises en production à votre planificateur de conteneur favori. La fonctionnalité de contrôle de validité de Vamp va au-delà des déploiements basés sur un pourcentage. Le trafic peut être filtré et fractionné en fonction d’un vaste éventail de conditions, par exemple, pour cibler des utilisateurs, des plages d’adresses IP ou des appareils spécifiques. Vamp suit et analyse les métriques de performances, permettant ainsi une automatisation basée sur des données réelles. Vous pouvez configurer une restauration automatique en cas d’erreurs ou mettre à l’échelle des variantes de service en fonction de la charge ou de la latence.

## <a name="set-up-azure-container-service-with-dcos"></a>Configurer Azure Container Service avec un contrôleur de domaine/système d’exploitation



1. [Déployez un cluster de contrôleur de domaine/système d’exploitation](container-service-deployment.md) avec un maître et deux agents de la taille par défaut. 

2. [Créez un tunnel SSH](../container-service-connect.md) pour vous connecter au cluster de contrôleur de domaine/système d’exploitation. Cet article suppose que vous effectuez un tunneling vers le cluster sur le port local 80.


## <a name="set-up-vamp"></a>Configurer Vamp

À présent que vous avez un cluster de contrôleur de domaine/système d’exploitation opérationnel, vous pouvez installer Vamp à partir de l’interface utilisateur du contrôleur de domaine/système d’exploitation (http://localhost:80). 

![IU DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

L’installation s’effectue en deux étapes :

1. **Déploiement d’Elasticsearch**,

2. puis **déploiement de Vamp** en installant le package de l’univers du contrôleur de domaine/système d’exploitation Vamp.

### <a name="deploy-elasticsearch"></a>Déployer Elasticsearch

Vamp a besoin d’Elasticsearch pour la collecte et l’agrégation des métriques. Vous pouvez utiliser les [images de Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) pour déployer une pile Elasticsearch Vamp compatible.

1. Dans l’interface utilisateur du contrôleur de domaine/système d’exploitation, accédez à **Services**, puis cliquez sur **Déployer le service**.

2. Dans la fenêtre contextuelle **Deploy New Service** (Déployer un nouveau service), sélectionnez **JSON mode** (mode JSON).

  ![Sélectionnez le mode JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Collez dans le code JSON suivant. Cette configuration exécute le conteneur avec 1 Go de RAM et un contrôle d’intégrité de base sur le port Elasticsearch.
  
  ```JSON
  {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
  }
  ```
  

3. Cliquez sur **Déployer**.

  Le contrôleur de domaine/système d’exploitation déploie le conteneur Elasticsearch. Vous pouvez suivre la progression dans la page **Services**.  

  ![déployer Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Déployer Vamp

Une fois qu’Elasticsearch est **en cours d’exécution**, vous pouvez ajouter le package de l’univers du contrôleur de domaine/système d’exploitation Vamp. 

1. Accédez à **Univers**, puis recherchez **vamp**. 
  ![Vamp sur un univers de contrôleur de domaine/système d’exploitation](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Cliquez sur **Installer** en regard du package Vamp, puis choisissez **Installation avancée**.

3. Faites défiler vers le bas, puis entrez l’url elasticsearch suivante : `http://elasticsearch.marathon.mesos:9200`. 

  ![Entrer l’URL d’Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Cliquez sur **Vérifier et installer**, puis sur **Installer** pour lancer le déploiement.  

  Le contrôleur de domaine/système d’exploitation déploie tous les composants Vamp requis. Vous pouvez suivre la progression dans la page **Services**.
  
  ![Déployer Vamp en tant que package d’univers](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Une fois le déploiement terminé, vous pouvez accéder à l’interface utilisateur de Vamp :

  ![Service Vamp sur contrôleur de domaine/système d’exploitation](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Interface utilisateur de Vamp](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Déployer votre premier service

À présent que Vamp est opérationnel, déployez un service à partir d’un schéma. 

Dans sa forme la plus simple, un [schéma Vamp](http://vamp.io/documentation/using-vamp/blueprints/) décrit les points de terminaison (passerelles), clusters et services à déployer. Vamp utilise des clusters pour regrouper des variantes du même service en groupes logiques à des fins de contrôle de validité de mise en production ou de tests A/B.  

Ce scénario utilise un exemple d’application monolithique appelé [ **sava**](https://github.com/magneticio/sava), qui en est à la version 1.0. Le monolithe est empaqueté dans un conteneur Docker qui se trouve dans un hub Docker sous magneticio/sava:1.0.0. L’application s’exécute normalement sur le port 8080, mais vous souhaitez l’exposer sous le port 9050 dans ce cas. Déployez l’application via Vamp à l’aide d’un schéma simple.

1. Accédez à **Deployments**.

2. Cliquez sur **Add**.

3. Collez dans le fichier YAML de schéma suivant. Ce schéma contient un seul cluster avec une seule variante de service, que nous allons changer ultérieurement :

  ```YAML
  name: sava                        # deployment name
  gateways:
    9050: sava_cluster/webport      # stable endpoint
  clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
  ```

4. Cliquez sur **Save**. Vamp entame le déploiement.

Le déploiement est décrit dans la page **Déploiements**. Pour contrôler son état, cliquez dessus.

![Interface utilisateur de Vamp - Déploiement de sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![service Sava dans l’interface utilisateur de Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Deux passerelles sont créées, qui sont répertoriées dans la page **Passerelles** :

* un point de terminaison stable pour accéder au service en cours d’exécution (port 9050) ; 
* une passerelle interne gérée par Vamp (que nous décrirons plus en détail ultérieurement). 

![Interface utilisateur de Vamp - Passerelles sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Le service sava est à présent déployé, mais vous ne pouvez pas y accéder de l’extérieur, car Azure Load Balancer ignore encore comment transférer le trafic vers celui-ci. Pour accéder au service, mettez à jour la configuration réseau Azure.


## <a name="update-the-azure-network-configuration"></a>Mettre à jour la configuration réseau Azure

Vamp a déployé le service sava sur les nœud agents du contrôleur de domaine/système d’exploitation, en exposant un point de terminaison stable au port 9050. Pour accéder au service à partir de l’extérieur du cluster de contrôleur de domaine/système d’exploitation, apportez les modifications suivantes à la configuration réseau Azure dans le déploiement de votre cluster : 

1. **Configurez l’équilibrage de charge Azure** pour les agents (la ressource nommée **dcos-agent-lb-xxxx**) avec une sonde d’intégrité et une règle pour transférer le trafic sur le port 9050 vers les instances sava. 

2. **Mettez à jour le groupe de sécurité réseau** pour les agents publics (la ressource nommée **XXXX-agent-public-nsg-XXXX**) pour autoriser le trafic sur le port 9050.

Pour obtenir des instructions détaillées sur l’exécution de ces tâches à l’aide du portail Azure, voir [Autoriser l’accès public à une application Azure Container Service](container-service-enable-public-access.md). Spécifiez le port 9050 pour tous les paramètres de port.


Une fois que tout a été créé, accédez au panneau **Vue d’ensemble** de l’équilibrage de charge de l’agent du contrôleur de domaine/système d’exploitation (la ressource nommée **dcos-agent-lb-xxxx**). Recherchez l’**adresse IP publique** et utilisez-la pour accéder à sava sur le port 9050.

![Portail Azure - Obtenir un adresse IP publique](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Exécuter un contrôle de validité de mise en production

Supposons que vous disposez d’une nouvelle version de cette application donc vous voulez contrôler la validité de la mise en production. Vous l’avez mise en conteneur en tant que magneticio/sava:1.1.0 et êtes prêt à l’employer. Vamp vous permet d’ajouter facilement des services au déploiement en cours d’exécution. Ces services « fusionnés » sont déployés en même temps que les services existants dans le cluster, et une pondération de 0 % leur est affectée. Aucun trafic n’est routé vers un service nouvellement fusionné tant que vous n’avez pas ajusté la distribution du trafic. Le curseur de poids dans l’interface utilisateur de Vamp vous permet de contrôler totalement la distribution, en effectuant des ajustements incrémentiels (contrôle de validité de mise en production) ou une restauration instantanée.

### <a name="merge-a-new-service-variant"></a>Fusionner une nouvelle variante de service

Pour fusionner le nouveau service sava 1.1 avec le déploiement en cours d’exécution :

1. Dans l’interface utilisateur de Vamp, cliquez sur **Schémas**.

2. Cliquez sur **Ajouter**, puis collez dans le fichier YAML de schéma suivant : ce schéma décrit une nouvelle variante de service (sava:1.1.0) à déployer dans le cluster existant (sava_cluster).

  ```YAML
  name: sava:1.1.0      # blueprint name
  clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
  ```
  
3. Cliquez sur **Save**. Le schéma est stocké et répertorié dans la page **Schémas**.

4. Ouvrez le menu action sur le schéma sava:1.1 et cliquez sur **Fusionner vers**.

  ![Interface utilisateur de Vamp - Schémas](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Sélectionnez le déploiement **sava**, puis cliquez sur **Fusionner**.

  ![Interface utilisateur de Vamp - Fusionner le schéma dans le déploiement](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp déploie la nouvelle variante de service sava:1.1.0 décrite dans le schéma en même temps que sava:1.0.0 dans le **sava_cluster** du déploiement en cours d’exécution. 

![Interface utilisateur de Vamp - Déploiement de sava mis à jour](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

La passerelle **sava/sava_cluster/webport** (point de terminaison du cluster) est également mise à jour par l’ajout d’un itinéraire au sava:1.1.0 nouvellement déployé. À ce stade, aucun trafic n’est routé ici (le **POIDS** est défini sur 0 %).

![Interface utilisateur de Vamp - Passerelle de cluster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Contrôle la validité de la mise en production

Les deux versions de sava étant déployées dans le même cluster, ajustez la distribution du trafic entre elles en déplaçant le curseur **POIDS**.

1. Cliquez sur ![Interface utilisateur de Vamp - Modifier](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) en regard de **POIDS**.

2. Définissez la répartition du poids sur 50 %/50 %, puis cliquez sur **Enregistrer**.

  ![Interface utilisateur de Vamp - Curseur de poids de passerelle](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Revenez à votre navigateur et actualisez la page de sava quelques fois supplémentaires. L’application sava bascule entre une page sava:1.0 et une page sava:1.1.

  ![alternance des services sava1.0 et sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Cette alternance de la page fonctionne de façon optimale avec les modes « Incognito » ou « Anonyme » de votre navigateur en raison de la mise en cache des ressources statiques.
  >

### <a name="filter-traffic"></a>Filtrer le trafic

Supposons qu’après déploiement vous ayez découvert une incompatibilité dans sava:1.1.0 qui entraîne des problèmes d’affichage dans les navigateurs Firefox. Vous pouvez configurer Vamp pour filtrer le trafic entrant et rediriger tous les utilisateurs de Firefox vers le sava:1.0.0 stable connu. Ce filtre résout instantanément l’interruption pour les utilisateurs de Firefox, tandis toutes les autres personnes continuent de profiter des avantages du sava:1.1.0 amélioré.

Vamp utilise des **conditions** pour filtrer le trafic entre itinéraires au sein d’une passerelle. Le trafic est filtré, puis dirigé selon les conditions appliquées à chaque itinéraire. Tout le trafic restant est distribué en fonction du paramètre de poids de passerelle.

Vous pouvez créer une condition pour filtrer tous les utilisateurs de Firefox et les diriger vers l’ancien sava:1.0.0 :

1. Dans la page sava/sava_cluster/webport **Passerelles**, cliquez sur ![Interface utilisateur de Vamp - Modifier](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) pour ajouter une **CONDITION** à l’itinéraire sava/sava_cluster/sava:1.0.0/webport. 

2. Entrez la condition **agent utilisateur == Firefox**, puis cliquez sur ![Interface utilisateur de Vamp - Enregistrer](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp ajoute la condition avec une force par défaut de 0 %. Pour commencer à filtrer le trafic, vous devez ajuster la force de la condition.

3. Cliquez sur ![Interface utilisateur de Vamp - Modifier](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) pour modifier la **FORCE** appliquée à la condition.
 
4. Définissez la **FORCE** sur 100 % et cliquez sur ![Interface utilisateur de Vamp - Enregistrer](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) pour enregistrer.

  Vamp envoie désormais tout le trafic correspondant à la condition (tous les utilisateurs de Firefox) vers sava:1.0.0.

  ![Interface utilisateur de Vamp - Appliquer une condition à la passerelle](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Enfin, définissez le poids de passerelle pour l’envoi de tout le trafic restant (tous les utilisateurs autres que Firefox) pour le nouveau sava:1.1.0. Cliquez sur ![Interface utilisateur de Vamp - Modifier](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) en regard de **POIDS** et définir la distribution de poids de façon à ce que 100 % soient dirigés vers l’itinéraire sava/sava_cluster/sava:1.1.0/webport.

  Tout le trafic non filtré par la condition est maintenant dirigé vers le nouveau sava:1.1.0.

6. Pour afficher le filtre d’action, ouvrez deux navigateurs différents (un Firefox et un autre ), puis accédez au service sava à partir des deux. Toutes les demandes de Firefox sont envoyées à sava:1.0.0, tandis que tous les autres navigateurs sont dirigés vers sava:1.1.0.

  ![Interface utilisateur de Vamp - Filtrer le trafic](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Résumé

Cet article est une présentation rapide de Vamp sur un cluster de contrôleur de domaine/système d’exploitation. Les débutants ont obtenu Vamp opérationnel sur leur cluster de contrôleur de domaine/système d’exploitation Azure Container Service, déployé un service avec un schéma Vamp, et accédé à celui-ci via le point de terminaison exposé (passerelle).

Nous avons également évoqué certaines puissantes fonctionnalités de Vamp telles que la fusion d’une nouvelle variante de service dans le déploiement en cours d’exécution et son introduction incrémentielle, puis le filtrage du trafic pour résoudre une incompatibilité connue.


## <a name="next-steps"></a>Étapes suivantes

* Découvrez la gestion des actions de Vamp via l’[API REST Vamp](http://vamp.io/documentation/api/api-reference/).

* Générez des scripts d’automatisation de Vamp dans Node.js et exécutez-les en tant que [flux de travail Vamp](http://vamp.io/documentation/tutorials/create-a-workflow/).

* Consultez des [didacticiels Vamp](http://vamp.io/documentation/tutorials/overview/) supplémentaires.

