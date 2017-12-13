---
title: "Gérer le cluster DC/OS Azure avec l’interface utilisateur Marathon"
description: "Déployez des conteneurs dans un cluster Azure Container Service à l’aide de l’interface utilisateur web Marathon."
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b1c5fc223105b1dae0ce07f242a6b42b34fd2ab3
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>Gérer un cluster de contrôleur de domaine/système d’exploitation Azure Container Service via l’interface utilisateur web Marathon

DC/OS offre un environnement de déploiement et de mise à l’échelle des charges de travail en cluster tout en faisant abstraction du matériel sous-jacent. DC/OS sous-tend un framework qui gère la planification et l’exécution des charges de travail de calcul.

Bien qu’il existe des frameworks pour de nombreuses charges de travail courantes, ce document décrit comment commencer à déployer des conteneurs avec Marathon. 


## <a name="prerequisites"></a>Composants requis
Avant d’étudier ces exemples, vous devez avoir un cluster DC/OS configuré dans Azure Container Service. Vous devez également disposer d’une connectivité à distance à ce cluster. Pour plus d’informations sur ces éléments, voir les articles suivants :

* [Déploiement d’un cluster Azure Container Service](container-service-deployment.md)
* [Connexion à un cluster Azure Container Service](../container-service-connect.md)

> [!NOTE]
> Cet article suppose que vous créez un tunnel vers le cluster DC/OS via le port local 80.
>

## <a name="explore-the-dcos-ui"></a>Exploration de l’interface utilisateur de DC/OS
[Créez](../container-service-connect.md) un tunnel SSH (Secure Shell), puis accédez à http://localhost/. Cela charge l’interface utilisateur web de DC/OS et affiche des informations sur le cluster, notamment les ressources utilisées, les agents actifs, et les services en cours d’exécution.

![IU DC/OS](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Découverte de l’interface utilisateur Marathon
Pour afficher l’interface utilisateur Marathon, accédez à http://localhost/marathon. À partir de cet écran, vous pouvez démarrer un nouveau conteneur ou une autre application sur le cluster DC/OS d’Azure Container Service. Vous pouvez également voir des informations sur les conteneurs et les applications en cours d’exécution.  

![IU Marathon](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Déployer un conteneur au format Docker
Pour déployer un nouveau conteneur à l’aide de Marathon, cliquez sur **Créer une application** et entrez les informations suivantes dans les onglets du formulaire :

| Champ | Valeur |
| --- | --- |
| ID |nginx |
| Mémoire | 32 |
| Image |nginx |
| Réseau |Relié par un pont |
| Port de l’hôte |80 |
| Protocole |TCP |

![Nouvelle interface utilisateur d’application : général](./media/container-service-mesos-marathon-ui/dcos4.png)

![Nouvelle interface utilisateur d’application : conteneur Docker](./media/container-service-mesos-marathon-ui/dcos5.png)

![Nouvelle interface utilisateur d’application : détection de service et de ports](./media/container-service-mesos-marathon-ui/dcos6.png)

Si vous souhaitez mapper le port du conteneur de manière statique à un port de l’agent, cela doit être effectué à l’aide du mode JSON. Pour ce faire, basculez de l’Assistant Nouvelle Application en **Mode JSON** . Ensuite, entrez le paramètre suivant sous la section `portMappings` de la définition d’application. Cet exemple montre comment lier le port 80 du conteneur au port 80 de l’agent DC/OS. Vous pouvez basculer l’Assistant hors du mode JSON après avoir apporté cette modification.

```none
"hostPort": 80,
```

![Nouvelle interface utilisateur d’application : exemple de port 80](./media/container-service-mesos-marathon-ui/dcos13.png)

Si vous souhaitez activer les contrôles d’intégrité, définissez un chemin d’accès dans l’onglet **Contrôles d'intégrité**.

![Interface utilisateur de nouvelle application -- contrôles d’intégrité](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

Le cluster DC/OS est déployé avec un ensemble d’agents privés et publics. Pour que le cluster puisse accéder à des applications à partir d’Internet, vous devez déployer les applications vers un agent public. Pour ce faire, sélectionnez l’onglet **Facultatif** de l’Assistant Nouvelle Application et saisissez **slave_public** pour les **Rôles de ressources acceptés**.

Cliquez ensuite sur **Créer une application**.

![Nouvelle interface utilisateur d’application : paramètre de l’agent public](./media/container-service-mesos-marathon-ui/dcos14.png)

Sur la page principale de Marathon, vous pouvez voir l’état du déploiement du conteneur. Initialement, vous voyez l’état **Déploiement**. Après un déploiement réussi, l’état passe à **En cours d’exécution**.

![Interface utilisateur de la page principale de Marathon : état du déploiement du conteneur](./media/container-service-mesos-marathon-ui/dcos7.png)

Lorsque vous revenez à l’interface utilisateur web de DC/OS (http://localhost/), vous verrez qu’une tâche (en l’occurrence un conteneur formaté par Docker) est en cours d’exécution sur le cluster DC/OS.

![IU web DC/OS : tâche en cours d’exécution sur le cluster](./media/container-service-mesos-marathon-ui/dcos8.png)

Pour voir le nœud du cluster sur lequel la tâche est exécutée, cliquez sur l’onglet **Nœuds**.

![IU web DC/OS : nœud du cluster de tâche](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Atteindre le conteneur

Dans cet exemple, l’application s’exécute sur un nœud d’agent public. Vous atteignez l’application depuis Internet en accédant au nom de domaine complet de l’agent du cluster : `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`, où :

* **DNSPREFIX** est le préfixe DNS que vous avez fourni lors du déploiement du cluster.
* **REGION** est la région dans laquelle se trouve le groupe de ressources.

    ![Nginx à partir d’Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Étapes suivantes
* [Utilisation de DC/OS et de l’API Marathon](container-service-mesos-marathon-rest.md)

* Deep dive on the Azure Container Service with Mesos (Présentation détaillée d’Azure Container Service avec Mesos)

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
