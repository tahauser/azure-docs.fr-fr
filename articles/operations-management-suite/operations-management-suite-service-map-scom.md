---
title: "Intégration de Service Map avec System Center Operations Manager | Microsoft Docs"
description: "Service Map est une solution comprise dans Azure qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Cet article décrit l’utilisation de Service Map pour créer automatiquement des diagrammes d’application distribuée dans Operations Manager."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: e8614a5a-9cf8-4c81-8931-896d358ad2cb
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: bwren;dairwin
ms.openlocfilehash: 906a90acb8754ff4b70235256cd184e2611ff5a0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="service-map-integration-with-system-center-operations-manager"></a>Intégration de Service Map avec System Center Operations Manager
  > [!NOTE]
  > Cette fonctionnalité est en version préliminaire publique.
  > 
  
La solution Service Map détecte automatiquement les composants d’application sur les systèmes Windows et Linux, et mappe la communication entre les services. Service Map vous permet d’afficher les serveurs comme des systèmes interconnectés qui fournissent des services critiques. Carte de service affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée à TCP, sans configuration requise autre que l’installation d’un agent. Pour plus d’informations, consultez la [documentation de Service Map](operations-management-suite-service-map.md).

Avec cette intégration entre Service Map et System Center Operations Manager, vous pouvez créer automatiquement des diagrammes d’application distribuée dans Operations Manager basés sur des cartes de dépendance dynamique dans Service Map.

## <a name="prerequisites"></a>Prérequis
* Un groupe d’administration d’Operations Manager (2012 R2 ou version ultérieure) qui gère un ensemble de serveurs.
* Un espace de travail Log Analytics avec la solution Service Map activée.
* Un ensemble de serveurs (au moins un) gérés par Operations Manager et envoyant des données à Service Map. Les serveurs Windows et Linux sont pris en charge.
* Un principal de service disposant d’un accès à l’abonnement Azure associé à l’espace de travail Log Analytics. Pour plus d’informations, consultez l’article [Créer un principal du service](#creating-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installation du pack d’administration de Service Map
Vous activez l’intégration entre Operations Manager et Service Map en important le groupement de packs d’administration Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Vous pouvez télécharger l’offre groupée de packs d’administration à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). Le groupe contient les packs d’administration suivants :
* Microsoft Service Map - Vues de l’application
* Microsoft System Center Service Map - Interne
* Microsoft System Center Service Map - Valeurs de remplacement
* Microsoft System Center Service Map

## <a name="configure-the-service-map-integration"></a>Configuration de l’intégration de Service Map
Après avoir installé le pack d’administration Service Map, vous trouverez un nouveau nœud, **Service Map**, dans le volet **Administration** **d’Operations Management Suite**. 

Pour configurer l’intégration de Service Map, procédez comme suit :

1. Cliquez sur **Ajouter un espace de travail** dans le volet de **vue d’ensemble de Service Map** pour ouvrir l’Assistant Configuration.  

    ![Vue d’ensemble de Service Map](media/oms-service-map/scom-configuration.png)

2. Dans la fenêtre **Configuration des connexions**, entrez l’ID ou le nom du client, l’ID d’application (aussi appelé nom d’utilisateur ou ID client) et le mot de passe du principal de service, puis cliquez sur **Suivant**. Pour plus d’informations, consultez l’article [Créer un principal du service](#creating-a-service-principal).

    ![Fenêtre Configuration des connexions](media/oms-service-map/scom-config-spn.png)

3. Dans la fenêtre **Sélection d’un abonnement**, sélectionnez l’abonnement Azure, le groupe de ressources Azure (celui qui contient l’espace de travail Log Analytics) et l’espace de travail Log Analytics, puis cliquez sur **Suivant**.

    ![Espace de travail de configuration d’Operations Manager](media/oms-service-map/scom-config-workspace.png)

4. Dans la fenêtre **Sélection du groupe d’ordinateurs**, choisissez quels groupes d’ordinateurs Service Map vous voulez synchroniser à Operations Manager. Cliquez sur **Ajouter/supprimer des groupes d’ordinateurs**, choisissez des groupes dans la liste des **Groupes d’ordinateurs disponibles**, puis cliquez sur **Ajouter**.  Lorsque vous avez fini de sélectionner des groupes, cliquez sur **Ok** pour terminer.
    
    ![Groupes d’ordinateurs de configuration Operations Manager](media/oms-service-map/scom-config-machine-groups.png)
    
5. Dans la fenêtre **Sélection du serveur**, vous configurez le groupe de serveurs Service Map avec les serveurs que vous voulez synchroniser entre Operations Manager et Service Map. Cliquez sur **Ajouter/Supprimer des serveurs**.   
    
    Pour que l’intégration crée un diagramme d’application distribuée pour un serveur, le serveur doit être :

    * Géré par Operations Manager
    * Géré par Service Map
    * Répertorié dans le groupe de serveurs Service Map

    ![Groupe de configuration d’Operations Manager](media/oms-service-map/scom-config-group.png)

6. Facultatif : Sélectionnez le pool de ressources du serveur d’administration pour communiquer avec Log Analytics et cliquez sur **Ajouter un espace de travail**.

    ![Pool de ressources de configuration d’Operations Manager](media/oms-service-map/scom-config-pool.png)

    La configuration et l’inscription de l’espace de travail Log Analytics peuvent prendre quelques minutes. Une fois configuré, Operations Manager lance la première synchronisation de Service Map.

    ![Pool de ressources de configuration d’Operations Manager](media/oms-service-map/scom-config-success.png)


## <a name="monitor-service-map"></a>Surveillance de Service Map
Une fois l’espace de travail Log Analytics connecté, un nouveau dossier, Service Map, s’affiche dans le panneau **Analyse** de la console Operations Manager.

![Panneau Analyse d’Operations Manager](media/oms-service-map/scom-monitoring.png)

Le dossier Service Map possède quatre nœuds :
* **Alertes actives** : répertorie toutes les alertes actives sur la communication entre Operations Manager et Service Map.  Ces alertes ne correspondent pas aux alertes Log Analytics synchronisées à Operations Manager. 

* **Serveurs** : répertorie les serveurs analysés configurés pour la synchronisation à partir de Service Map.

    ![Panneau Analyse des serveurs d’Operations Manager](media/oms-service-map/scom-monitoring-servers.png)

* **Vues de dépendance de groupe d’ordinateurs** : répertorie tous les groupes d’ordinateurs synchronisés depuis Service Map. Vous pouvez cliquer sur n’importe quel groupe pour afficher son diagramme d’application distribuée.

    ![Diagramme d’application distribuée Operations Manager](media/oms-service-map/scom-group-dad.png)

* **Vues de dépendance au serveur** : répertorie tous les serveurs synchronisés à partir de Service Map. Vous pouvez cliquer sur n’importe quel serveur pour afficher son diagramme d’application distribuée.

    ![Diagramme d’application distribuée Operations Manager](media/oms-service-map/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Modification ou suppression de l’espace de travail
Vous pouvez modifier ou supprimer l’espace de travail configuré via le volet de **vue d’ensemble de Service Map** (volet **Administration** > **Operations Management Suite** > **Service Map**). Vous ne pouvez configurer qu’un seul espace de travail Log Analytics pour l’instant.

![Volet de modification de l’espace de travail d’Operations Manager](media/oms-service-map/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configuration des règles et des valeurs de remplacement
Une règle _Microsoft.SystemCenter.ServiceMapImport.Rule_ est créée pour extraire régulièrement des informations de Service Map. Pour modifier les intervalles de synchronisation, vous pouvez configurer des valeurs de remplacement pour la règle (volet **Création** > **Règles** > **Microsoft.SystemCenter.ServiceMapImport.Rule**).

![Fenêtre des propriétés des valeurs de remplacement Operations Manager](media/oms-service-map/scom-overrides.png)

* **Enabled** : activer ou désactiver les mises à jour automatiques. 
* **IntervalMinutes** : réinitialiser la durée entre les mises à jour. L’intervalle par défaut est égal à une heure. Si vous voulez synchroniser les mappages de serveurs plus régulièrement, vous pouvez modifier la valeur.
* **TimeoutSeconds** : réinitialiser la durée avant l’expiration de la demande. 
* **TimeWindowMinutes** : réinitialiser la fenêtre de temps pour l’interrogation des données. Valeur par défaut : fenêtre de 60 minutes. La valeur maximale autorisée par Service Map est de 60 minutes.

## <a name="known-issues-and-limitations"></a>Problèmes connus et limitations

La conception actuelle présente les problèmes et les limitations suivants :
* Vous ne pouvez vous connecter qu’à un seul espace de travail Log Analytics.
* Bien que vous puissiez ajouter des serveurs au Groupe de serveurs Service Map manuellement via le volet **Création**, les mappages de ces serveurs sont synchronisés immédiatement.  Ils seront synchronisés à partir de Service Map lors du prochain cycle de synchronisation.
* Si vous apportez des modifications aux diagrammes d’application distribuée créée par le pack d’administration, ces modifications sont probablement remplacées dans la prochaine synchronisation avec Service Map.

## <a name="create-a-service-principal"></a>Créer un principal du service
Pour obtenir une documentation Azure officielle sur la création d’un principal de service, consultez :
* [Création d’un principal de service à l’aide de PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Création d’un principal de service à l’aide d’Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Création d’un principal de service à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Commentaires
Avez-vous des commentaires à nous transmettre à propos de Service Map ou de sa documentation ? Consultez notre [page Voix utilisateur](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map) qui vous permet de suggérer des fonctionnalités ou de voter pour les suggestions en cours.
