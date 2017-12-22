---
title: "Mise à niveau d’un cluster Azure Service Fabric | Microsoft Docs"
description: "Mettez à niveau le code et/ou la configuration Service Fabric qui exécute un cluster Service Fabric. Cela inclut le mode de mise à jour du cluster, la mise à niveau de certificats, l’ajout de ports d’application, l’application des correctifs de système d’exploitation, etc. À quoi vous attendre lors de l'exécution des mises à niveau ?"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/10/2017
ms.author: chackdan
ms.openlocfilehash: 7ea71ab891583c51b3c07a4d0a9f0b4f54e56669
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="upgrade-an-azure-service-fabric-cluster"></a>Mettre à niveau un cluster Azure Service Fabric
> [!div class="op_single_selector"]
> * [Cluster Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Pour les systèmes modernes, la réussite à long terme d’un produit dépend de sa capacité à être mis à niveau. Un cluster Azure Service Fabric est une ressource que vous possédez mais qui est en partie gérée par Microsoft. Cet article décrit ce qui est géré automatiquement et ce que vous pouvez configurer vous-même.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Contrôle de la version de la structure qui s’exécute sur votre cluster
Vous pouvez définir votre cluster de façon à recevoir les nouvelles mises à niveau de structure automatiques publiées par Microsoft, ou opter pour la sélection de la version de structure prise en charge que vous voulez associer à votre cluster.

Pour ce faire, définissez la configuration de cluster « upgradeMode » sur le portail, ou utilisez Resource Manager lors de la création ou ultérieurement, sur un cluster activé. 

> [!NOTE]
> Faites en sorte que votre cluster exécute systématiquement une version prise en charge de la structure. Lorsque nous annonçons le lancement d’une nouvelle version de Service Fabric, la fin de la prise en charge de la version précédente est signalée 60 jours minimum après la date de lancement. Les nouvelles versions sont annoncées [sur le blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). La nouvelle version peut alors être sélectionnée. 
> 
> 

Un événement d’intégrité est généré 14 jours avant la date d’expiration de la version qu’exécute votre cluster. Il place le cluster dans un état d’avertissement. Ce dernier conserve cet état tant que vous n’avez pas effectué de mise à niveau vers une version prise en charge.

### <a name="setting-the-upgrade-mode-via-portal"></a>Définition du mode de mise à niveau via le portail
Vous pouvez définir le cluster sur le mode Manuel ou Automatique lors de la création du cluster.

![Create_Manualmode][Create_Manualmode]

Vous pouvez définir le cluster sur le mode Automatique ou Manuel dans le cas d’un cluster activé, à l’aide de l’expérience de gestion. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Mise à niveau vers une nouvelle version sur un cluster défini sur le mode Manuel, via le portail
Pour effectuer la mise à niveau vers une nouvelle version, il vous suffit de sélectionner la version disponible dans la liste déroulante, puis d’enregistrer. La mise à niveau de Service Fabric est lancée automatiquement. Les stratégies de contrôle d’intégrité du cluster (combinaison de l’intégrité des nœuds et de l’intégrité de toutes les applications en cours d’exécution dans le cluster) sont respectées pendant la mise à niveau.

Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Faites dérouler ce document pour en savoir plus sur la définition de ces stratégies de contrôle d’intégrité personnalisées. 

Une fois que vous avez corrigé les problèmes entraînant la restauration, vous devez initier à nouveau la mise à niveau, en suivant la procédure décrite précédemment.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Définition du mode de mise à niveau via un modèle Resource Manager
Ajoutez la configuration « upgradeMode » dans la définition de ressource Microsoft.ServiceFabric/clusters et définissez le paramètre « clusterCodeVersion » sur l’une des versions de structure prises en charge, comme indiqué ci-dessous, puis déployez le modèle. Les valeurs valides pour l’élément « upgradeMode » sont « Manuel » ou « Automatique ».

![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Mise à niveau vers une nouvelle version sur un cluster défini sur le mode Manuel, via un modèle Resource Manager
Lorsque le cluster est en mode Manuel, si vous voulez effectuer une mise à niveau vers une nouvelle version, modifiez le paramètre « clusterCodeVersion » en indiquant une version prise en charge et en la déployant. Le déploiement du modèle lance la mise à niveau de la structure automatiquement. Les stratégies de contrôle d’intégrité du cluster (combinaison de l’intégrité des nœuds et de l’intégrité de toutes les applications en cours d’exécution dans le cluster) sont respectées pendant la mise à niveau.

Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Faites dérouler ce document pour en savoir plus sur la définition de ces stratégies de contrôle d’intégrité personnalisées. 

Une fois que vous avez corrigé les problèmes entraînant la restauration, vous devez initier à nouveau la mise à niveau, en suivant la procédure décrite précédemment.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Obtenir la liste de toutes les versions disponibles pour tous les environnements dans le cas d’un abonnement donné
Exécutez la commande suivante. La sortie doit ressembler à ce qui suit.

Le paramètre « supportExpiryUtc » vous indique lorsqu’une version donnée arrive à expiration ou a expiré. La version la plus récente n’est pas associée à une date valide. Elle a donc la valeur « 9999-12-31T23:59:59.9999999 », qui signifie simplement que la date d’expiration n’est pas encore définie.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportement de mise à niveau de la structure lorsque le mode de mise à niveau du cluster est Automatique
Microsoft tient à jour le code de l’infrastructure et la configuration exécutés dans un cluster Azure. Nous effectuons des mises à niveau automatiques surveillées du logiciel en fonction des besoins. Ces mises à niveau peuvent concerner le code, la configuration ou les deux. Pour garantir que votre application n’est pas affectée par ces mises à niveau, ou l’est au minimum, nous effectuons ces dernières en plusieurs phases :

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Phase 1 : Une mise à niveau est effectuée à l'aide de toutes les stratégies d'intégrité du cluster
Pendant cette phase, les mises à niveau traitent un domaine de mise à niveau à la fois et les applications qui étaient en cours d'exécution dans le cluster continuent à fonctionner sans interruption. Les stratégies de contrôle d’intégrité du cluster (combinaison de l’intégrité des nœuds et de l’intégrité de toutes les applications en cours d’exécution dans le cluster) sont respectées pendant la mise à niveau.

Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Un message électronique est envoyé au propriétaire de l’abonnement. Le message électronique contient les informations suivantes :

* Une notification indiquant que nous avons dû annuler une mise à niveau du cluster.
* Des suggestions d'actions correctives, le cas échéant.
* Le nombre de jours (n) avant l’exécution de la Phase 2.

Nous essayons d'exécuter la même mise à niveau plusieurs fois dans le cas où les mises à niveau ont échoué pour des raisons d'infrastructure. Après le délai de n jours à partir de la date d'envoi du message électronique, nous passons à la Phase 2.

Si les stratégies d'intégrité du cluster sont respectées, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la première exécution ou l'une des exécutions de la mise à niveau suivantes dans cette phase. Aucun message électronique de confirmation d'une exécution réussie n'est envoyé. Ceci afin d'éviter de vous envoyer trop de messages électroniques, la réception d'un message devant être considérée comme une exception. Nous pensons que la plupart des mises à niveau du cluster s'exécuteront sans affecter la disponibilité de votre application.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Phase 2 : Une mise à niveau est effectuée à l'aide des stratégies d'intégrité par défaut uniquement
Les stratégies d'intégrité de cette phase sont définies de telle sorte que le nombre d'applications intègres au début de la mise à niveau reste identique pendant la durée de la mise à niveau. Comme lors de la Phase 1, les mises à niveau de la Phase 2 traitent un domaine de mise à niveau à la fois et les applications qui étaient en cours d'exécution dans le cluster continuent à fonctionner sans interruption. Les stratégies d’intégrité du cluster (une combinaison de l’intégrité du nœud et l’intégrité de toutes les applications en cours d’exécution dans le cluster) sont respectées pendant la durée de la mise à niveau.

Si les stratégies d'intégrité de cluster en vigueur ne sont pas respectées, la mise à niveau est annulée. Un message électronique est envoyé au propriétaire de l’abonnement. Le message électronique contient les informations suivantes :

* Une notification indiquant que nous avons dû annuler une mise à niveau du cluster.
* Des suggestions d'actions correctives, le cas échéant.
* Le nombre de jours (n) avant l’exécution de la Phase 3.

Nous essayons d'exécuter la même mise à niveau plusieurs fois dans le cas où les mises à niveau ont échoué pour des raisons d'infrastructure. Un message électronique de rappel est envoyé deux jours avant que le délai de n jours ne soit écoulé. Après le délai de n jours à partir de la date d'envoi du message électronique, nous passons à la Phase 3. Les messages électroniques que nous vous envoyons lors de la Phase 2 doivent être pris au sérieux et des actions correctives doivent être prises.

Si les stratégies d'intégrité du cluster sont respectées, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la première exécution ou l'une des exécutions de la mise à niveau suivantes dans cette phase. Aucun message électronique de confirmation d'une exécution réussie n'est envoyé.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Phase 3 : Une mise à niveau est effectuée à l'aide de stratégies d'intégrité agressives
Les stratégies d'intégrité de cette phase visent à compléter la mise à niveau plutôt qu’à protéger l'intégrité des applications. Un nombre très faible de mises à niveau du cluster se termine par cette phase. Si votre cluster atteint cette phase, il est très probable que votre application devienne défectueuse et/ou ne soit plus disponible.

Comme pour les deux autres phases, les mises à niveau de la Phase 3 traitent un domaine de mise à niveau à la fois.

Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Nous essayons d'exécuter la même mise à niveau plusieurs fois dans le cas où les mises à niveau ont échoué pour des raisons d'infrastructure. Après cela, le cluster est épinglé et il ne bénéficiera plus des mises à niveau et/ou du support.

Un message électronique contenant ces informations est envoyé au propriétaire de l’abonnement, ainsi que les actions correctives. Nous pensons qu'aucun cluster ne sera confronté à un échec de la Phase 3.

Si les stratégies d'intégrité du cluster sont respectées, la mise à niveau est considérée comme réussie et marquée comme terminée. Cela peut se produire pendant la première exécution ou l'une des exécutions de la mise à niveau suivantes dans cette phase. Aucun message électronique de confirmation d'une exécution réussie n'est envoyé.

## <a name="cluster-configurations-that-you-control"></a>Configurations de cluster que vous contrôlez
Outre la possibilité de définir le mode de mise à niveau du cluster, voici les configurations que vous pouvez modifier sur un cluster activé.

### <a name="certificates"></a>Certificats
Vous pouvez facilement ajouter ou supprimer des certificats pour le cluster et le client via le portail. Consultez [ce document pour obtenir des instructions détaillées](service-fabric-cluster-security-update-certs-azure.md)

![Capture d'écran qui affiche les empreintes numériques des certificats sur le portail Azure.][CertificateUpgrade]

### <a name="application-ports"></a>Ports d'application
Vous pouvez modifier les ports d'application en modifiant les propriétés de ressource d'équilibrage de charge associées au type de nœud. Vous pouvez utiliser le portail ou bien directement Resource Manager PowerShell.

Pour ouvrir un nouveau port sur toutes les machines virtuelles dans un type de nœud, vous devez effectuer les opérations suivantes :

1. Ajouter une nouvelle sonde à l'équilibrage de charge approprié.
   
    Si vous avez déployé votre cluster à l’aide du portail, les équilibrages de charge sont nommés « LB-nom du groupe de ressources-Nom du type de nœud », un pour chaque type de nœud. Étant donné que les noms de l'équilibrage de charge sont uniques dans un groupe de ressources seulement, il est préférable de les rechercher dans un groupe de ressources spécifique.
   
    ![Capture d'écran qui illustre l'ajout d'une sonde à un équilibrage de charge sur le portail.][AddingProbes]
2. Ajouter une nouvelle règle pour l'équilibrage de charge.
   
    Ajoutez une nouvelle règle au même équilibrage de charge à l'aide de la sonde créée à l'étape précédente.
   
    ![Ajout d’une nouvelle règle à un équilibrage de charge sur le portail][AddingLBRules]

### <a name="placement-properties"></a>Propriétés de positionnement
Pour chaque type de nœud, vous pouvez ajouter des propriétés de positionnement personnalisées que vous souhaitez utiliser dans vos applications. NodeType est une propriété par défaut que vous pouvez utiliser sans l'ajouter explicitement.

> [!NOTE]
> Pour plus d’informations sur l’utilisation et la définition de contraintes de placement et des propriétés de nœud, consultez la section « Contraintes de placement et propriétés de nœud » dans le document Gestionnaire de ressources de cluster Service Fabric sur la [Description de votre cluster](service-fabric-cluster-resource-manager-cluster-description.md).
> 
> 

### <a name="capacity-metrics"></a>Métriques de capacité
Pour chaque type de nœud, vous pouvez ajouter des mesures de capacité personnalisées que vous souhaitez utiliser dans vos applications pour créer un rapport sur la charge. Pour plus d’informations sur l’utilisation de métriques de capacité pour créer un rapport sur la charge, consultez les documents Gestionnaire de ressources de cluster Service Fabric sur la [Description de votre cluster](service-fabric-cluster-resource-manager-cluster-description.md) et les [Métriques et charge](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Paramètres de mise à niveau de la structure : stratégies de contrôle d’intégrité
Vous pouvez spécifier des stratégies de contrôle d’intégrité personnalisées pour la mise à niveau de la structure. Si vous avez défini votre cluster sur une mise à niveau automatique de la structure, ces stratégies sont appliquées lors de la phase 1 de cette mise à niveau.
Si vous avez défini votre cluster sur une mise à niveau manuelle de la structure, ces stratégies sont appliquées chaque fois que vous sélectionnez une nouvelle version, ce qui amène le système à déclencher la mise à niveau de la structure dans votre cluster. Si vous ne remplacez pas les stratégies, les valeurs par défaut sont utilisées.

Vous pouvez spécifier des stratégies de contrôle d’intégrité personnalisées ou passer en revue les paramètres actuels dans le panneau Fabric Upgrade (Mise à niveau de la structure), en sélectionnant les paramètres de mise à niveau avancés. L’image suivante vous explique comment procéder. 

![Gérer les stratégies de contrôle d’intégrité personnalisées][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personnaliser les paramètres de la structure pour votre cluster
Consultez les [paramètres de la structure du cluster Service Fabric](service-fabric-cluster-fabric-settings.md) pour savoir quels éléments vous pouvez personnaliser et connaître la procédure à suivre pour ce faire.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Correctifs de système d'exploitation sur les machines virtuelles qui composent le cluster
Reportez-vous à [Application d’orchestration des correctifs](service-fabric-patch-orchestration-application.md) qui peut être déployée sur votre cluster pour installer des correctifs à partir de Windows Update de manière orchestrée, en conservant les services disponibles en permanence. 

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Mises à niveau du système d'exploitation sur les machines virtuelles qui composent le cluster
Si vous devez mettre à niveau l'image du système d'exploitation sur les machines virtuelles du cluster, vous devez procéder une machine virtuelle à la fois. Vous êtes responsable de cette mise à niveau, rien n’est actuellement automatisé à ce sujet.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment personnaliser certains [paramètres de la structure du cluster Service Fabric](service-fabric-cluster-fabric-settings.md)
* Découvrez comment [mettre votre cluster à l’échelle](service-fabric-cluster-scale-up-down.md)
* Découvrez les [mises à niveau de l’application](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
