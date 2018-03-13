---
title: "Application d’orchestration des correctifs Azure Service Fabric pour Linux | Microsoft Docs"
description: "Application permettant d’automatiser la mise à jour corrective du système d’exploitation sur un cluster Service Fabric Linux."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/22/2018
ms.author: nachandr
ms.openlocfilehash: dac8068705e284b04d84d128eb1ce62c459d44ff
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Corriger le système d’exploitation Linux dans votre cluster Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

L’application d’orchestration des correctifs est une application Azure Service Fabric qui automatise les mises à jour correctives du système d’exploitation sur un cluster Service Fabric sans temps d’arrêt.

L’application d’orchestration des correctifs offre les fonctionnalités suivantes :

- **Installation automatique de mise à jour du système d’exploitation**. Des mises à jour du système d’exploitation sont téléchargées et installées automatiquement. Les nœuds de cluster sont redémarrés en fonction des besoins sans temps d’arrêt du cluster.

- **Application de correctifs et intégration de l’intégrité adaptées au cluster**. Lors de l’application des mises à jour, l’application d’orchestration des correctifs surveille l’intégrité des nœuds de cluster. Les nœuds de cluster sont mis à niveau l’un après l’autre, ou domaine de mise à niveau après domaine de mise à niveau. Si l’intégrité du cluster se dégrade en raison du processus de mise à jour corrective, la mise à jour est arrêtée pour éviter d’aggraver le problème.

## <a name="internal-details-of-the-app"></a>Détails internes de l’application

L’application d’orchestration des correctifs comprend les sous-composants suivants :

- **Service Coordinateur** : ce service avec état est responsable des aspects suivants :
    - Coordination de la tâche de mise à jour du système d’exploitation sur le cluster tout entier.
    - Stockage du résultat des opérations de mise à jour du système d’exploitation accomplies.
- **Service Agent du nœud** : ce service sans état s’exécute sur tous les nœuds de cluster Service Fabric. Les responsabilités du service sont les suivantes :
    - Amorçage du démon Agent du nœud sur Linux.
    - Surveillance du service du démon.
- **Démon Agent du nœud** : Ce service de démon Linux s’exécute à un niveau de privilège supérieur (racine). À l’opposé, les services Agent du nœud et Coordinateur s’exécutent à des niveaux de privilège inférieurs. Le service est chargé d’exécuter sur tous les nœuds de cluster les tâches de mise à jour suivantes :
    - Désactivation de la mise à jour automatique du système d’exploitation sur le nœud.
    - Téléchargement et installation de la mise à jour du système d’exploitation en fonction de la stratégie définie par l’utilisateur.
    - Redémarrage de l’ordinateur après l’installation de la mise à jour du système d’exploitation, si besoin.
    - Chargement des résultats des mises à jour du système d’exploitation apportées au service Coordinateur.
    - génération de rapport d’intégrité en cas d’échec de l’opération une fois toutes les nouvelles tentatives effectuées.

> [!NOTE]
> L’application d’orchestration des correctifs utilise le service système gestionnaire des réparations de Service Fabric pour désactiver ou activer le nœud et effectuer des vérifications d’intégrité. La tâche de réparation créée par l’application d’orchestration des correctifs suit la progression de l’exécution de la mise à jour pour chaque nœud.

## <a name="prerequisites"></a>Prérequis

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Vérifier que vos machines virtuelles Azure exécutent Ubuntu 16.04
Au moment de la rédaction de ce document, Ubuntu 16.04 (`Xenial Xerus`) est la seule version prise en charge.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-61x-and-above"></a>Vérifier que la version du cluster Service Fabric Linux est 6.1.x ou une version ultérieure

L’application d’orchestration des correctifs Linux utilise certaines fonctionnalités du runtime uniquement disponibles dans le runtime Service Fabric 6.1.x et versions ultérieures.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Activer le service de gestion des réparations (s’il est inactif)

Pour que l’application d’orchestration des correctifs puisse fonctionner, le service système de gestion des réparations doit être activé sur le cluster.

#### <a name="azure-clusters"></a>Clusters Azure

Le service de gestion des réparations est activé par défaut pour les clusters Azure Linux aux niveaux de durabilité Silver et Gold. Le service de gestion des réparations est déactivé par défaut pour les clusters Azure au niveau de durabilité Bronze. Si le service est déjà activé, vous pouvez le voir s’exécuter dans la section des services système de Service Fabric Explorer.

##### <a name="azure-portal"></a>Portail Azure
Vous pouvez activer le gestionnaire des réparations à partir du portail Azure au moment de la configuration du cluster. Sélectionnez l’option **Inclure le gestionnaire de réparation** sous **Fonctionnalités complémentaires** lors de la configuration du cluster.
![Image représentant l’activation du gestionnaire des réparations à partir du portail Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Modèle de déploiement Azure Resource Manager
Vous pouvez également utiliser le [modèle de déploiement Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pour activer le service de gestion des réparations sur les clusters Service Fabric nouveaux et existants. Récupérez le modèle approprié pour le cluster que vous souhaitez déployer. Vous pouvez soit utiliser les exemples de modèles, soit créer un modèle de déploiement Azure Resource Manager personnalisé. 

Pour activer le service de gestion des réparations à l’aide du [modèle de déploiement Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) :

1. Commencez par vérifier que `apiversion` a la valeur `2017-07-01-preview` pour la ressource `Microsoft.ServiceFabric/clusters`. Si ce n’est pas le cas, vous devez mettre à jour `apiVersion` en affectant la valeur `2017-07-01-preview` ou une valeur supérieure :

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. À présent, activez le service de gestion des réparations en ajoutant la section `addonFeatures` suivante après la section `fabricSettings`:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Après avoir mis à jour votre modèle de cluster avec ces modifications, appliquez-les et laissez la mise à niveau s’accomplir. Vous pouvez maintenant voir le service système de gestion des réparations s’exécuter dans votre cluster. Il est appelé `fabric:/System/RepairManagerService` dans la section des services système de Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clusters locaux autonomes

Les clusters Service Fabric Linux autonomes ne sont pas pris en charge au moment de la rédaction de ce document.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Désactiver la mise à jour automatique du système d’exploitation sur tous les nœuds

Les mises à jour automatiques du système d’exploitation peuvent entraîner une perte de disponibilité ou une modification du comportement des applications en cours d’exécution. Par défaut, l’application d’orchestration des correctifs tente de désactiver les mises à jour automatiques du système d’exploitation sur chaque nœud du cluster pour éviter de telles situations.
Pour Ubuntu, les [mises à niveau sans assistance](https://help.ubuntu.com/community/AutomaticSecurityUpdates) sont désactivées par l’application d’orchestration des correctifs.

## <a name="download-the-app-package"></a>Télécharger le package de l’application

Téléchargez l’application à partir du [lien de téléchargement](https://go.microsoft.com/fwlink/?linkid=867984).

## <a name="configure-the-app"></a>Configurer l’application

Vous pouvez configurer le comportement de l’application d’orchestration des correctifs en fonction de vos besoins. Remplacez les valeurs par défaut en entrant le paramètre d’application durant la création ou la mise à jour de l’application. Il est possible de fournir des paramètres d’application en spécifiant `ApplicationParameter` pour les applets de commande `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication`.

|**Paramètre**        |**Type**                          | **Détails**|
|:-|-|-|
|MaxResultsToCache    |long                              | Nombre maximal de résultats d’exécution de mise à jour du système d’exploitation à mettre en cache. <br>La valeur par défaut est 3 000, en supposant ce qui suit : <br> - Le nombre de nœuds est 20. <br> - Le nombre de mises à jour par mois effectuées sur un nœud est 5. <br> - Le nombre maximal de résultats par opération est 10. <br> - Les résultats des trois derniers mois doivent être stockés. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indique la stratégie que le service Coordinateur doit utiliser pour installer les mises à jour sur les nœuds du cluster Service Fabric.<br>                         Les valeurs autorisées sont les suivantes : <br>                                                           <b>NodeWise</b>. Les mises à jour s’installent de façon séquentielle, nœud après nœud. <br>                                                           <b>UpgradeDomainWise</b>. Les mises à jour s’installent de façon séquentielle, domaine de mise à niveau après domaine de mise à niveau. (Au maximum, tous les nœuds appartenant à un domaine de mise à niveau peuvent bénéficier de la mise à niveau.)
| UpdateOperationTimeOutInMinutes | Int <br>(Par défaut : 180)                   | Spécifie le délai d’expiration de toute opération de mise à jour (télécharger ou installer). Si l’opération n’est pas terminée dans le délai imparti, elle est abandonnée.       |
| RescheduleCount      | Int <br> (Par défaut : 5)                  | Nombre maximal de fois que le service replanifie une mise à jour du système d’exploitation en cas d’échec persistant d’une opération.          |
| RescheduleTimeInMinutes  | Int <br>(Par défaut : 30) | Intervalle auquel le service replanifie une mise à jour du système d’exploitation en cas d’échec persistant. |
| UpdateFrequency           | Chaîne de valeurs séparées par des virgules (par défaut : « Hebdomadaire, Mercredi, 7:00:00 »).     | Fréquence d’installation des mises à jour du système d’exploitation sur le cluster. Le format et les valeurs possibles sont les suivants : <br>-   Mensuelle, JJ, HH:MM:SS, par exemple, Mensuelle, 5, 12:22:32. <br> -   Hebdomadaire, JOUR, HH:MM:SS, par exemple, Hebdomadaire, mardi, 12:22:32.  <br> -   Quotidienne, HH:MM:SS, par exemple, Quotidienne, 12:22:32.  <br> -  Aucune : indique que la mise à jour ne doit pas être effectuée.  <br><br> Toutes les heures sont exprimées en UTC.|
| UpdateClassification | Chaîne de valeurs séparées par des virgules (par défaut : « securityupdates »). | Type de mises à jour à installer sur les nœuds de cluster. Les valeurs acceptables sont securityupdates et all. <br> -  securityupdates (installation des mises à jour de sécurité uniquement) <br> -  all (installation de toutes les mises à jour disponibles depuis apt)|
| ApprovedPatches | Chaîne de valeurs séparées par des virgules (par défaut : "") | Voici la liste des mises à jour approuvées à installer sur les nœuds de cluster. Cette liste de valeurs séparées par des virgules contient les packages approuvés et éventuellement la version cible souhaitée.<br> Exemple : « apt-utils = 1.2.10ubuntu1, python3-jwt, apt-transport-https < 1.2.194, libsystemd0 >= 229-4ubuntu16 » <br> La liste ci-dessus permet d’installer : <br> - apt-utils avec la version 1.2.10ubuntu1 si elle est disponible dans apt-cache. Si cette version particulière n’est pas disponible, il n’y a pas d’opération. <br> - Les mises à niveau de python3-jwt vers la version disponible la plus récente. Si le package n’est pas présent, il n’y a pas d’opération. <br> - Les mises à niveau d’apt-transport-https vers la version la plus élevée inférieure à 1.2.194. En l’absence de cette version, il n’y a pas d’opération. <br> - Les mises à niveau de libsystemd0 vers la version la plus élevée supérieure ou égale à 229-4ubuntu16. Si une telle version n’existe pas, il n’y a pas d’opération.|
| RejectedPatches | Chaîne de valeurs séparées par des virgules (par défaut : "") | Voici la liste des mises à jour à ne pas installer sur les nœuds de cluster. <br> Exemple : « bash, sudo » <br> La liste précédente exclut bash, sudo de la réception des mises à jour. |


> [!TIP]
> Si vous voulez qu’une mise à jour du système d’exploitation ait lieu immédiatement, définissez `UpdateFrequency` par rapport à l’heure de déploiement de l’application. Par exemple, supposons que vous disposez d’un cluster de test de cinq nœuds et que vous prévoyez de déployer l’application vers 17 heures UTC. Si vous estimez que la mise à niveau ou le déploiement de l’application nécessite au maximum 30 minutes, affectez à UpdateFrequency la valeur « Quotidienne, 17:30:00 ».

## <a name="deploy-the-app"></a>Déployer l’application

1. Préparez le cluster en effectuant toutes les étapes prérequises.
2. Déployez l’application d’orchestration des correctifs comme toute autre application Service Fabric. Vous pouvez déployer l’application à l’aide de PowerShell ou de l’interface de ligne de commande Azure Service Fabric. Suivez les étapes indiquées dans [Déployer et supprimer des applications avec PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) ou [Déployer une application à l’aide de l’interface de ligne de commande Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application).
3. Pour configurer l’application au moment du déploiement, passez `ApplicationParamater` à l’applet de commande `New-ServiceFabricApplication` ou aux scripts fournis. Par commodité, des scripts Powershell (Deploy.ps1) et bash (Deploy.sh) sont fournis avec l’application. Pour utiliser le script :

    - Connectez-vous à un cluster Service Fabric.
    - Exécutez le script Deploy. Passez éventuellement le paramètre d’application au script. Exemple : .\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = "Quotidienne, 11:00:00"} OU ./Deploy.sh "{\"UpdateFrequency\":\"Quotidienne, 11:00:00\"}" 

> [!NOTE]
> Conservez le script et le dossier d’application PatchOrchestrationApplication dans le même répertoire.

## <a name="upgrade-the-app"></a>Mettre à niveau l’application

Pour mettre à niveau une application d’orchestration des correctifs existante, suivez les étapes indiquées dans [Mise à niveau d’applications Service Fabric à l’aide de PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) ou [Mise à niveau d’applications Service Fabric à l’aide de l’interface de ligne de commande Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade).

## <a name="remove-the-app"></a>Supprimer l’application

Pour supprimer l’application, suivez les étapes indiquées dans [Déployer et supprimer des applications avec PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) ou [Supprimer une application à l’aide de l’interface de ligne de commande Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete).

Par commodité, des scripts Powershell (Undeploy.ps1) et bash (Undeploy.sh) sont fournis avec l’application. Pour utiliser le script :

  - Connectez-vous à un cluster Service Fabric.
  - Exécuter le script Undeploy.ps1 ou Undeploy.sh

> [!NOTE]
> Conservez le script et le dossier d’application PatchOrchestrationApplication dans le même répertoire.

## <a name="view-the-update-results"></a>Afficher les résultats des mises à jour

L’application d’orchestration des correctifs expose les API REST pour afficher l’historique des résultats à l’utilisateur. Voici un exemple de résultat : ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

Les champs de l’objet JSON sont décrits comme suit :

Champ | Valeurs | Détails
-- | -- | --
OperationResult | 0 - Réussi<br> 1 - Réussi avec erreurs<br> 2 - Échec<br> 3 - Abandonné<br> 4 - Abandonné avec délai d’expiration | Indique le résultat de l’opération globale (généralement impliquant l’installation d’une ou de plusieurs mises à jour).
ResultCode | Identique à OperationResult | Ce champ indique le résultat de l’opération d’installation pour une mise à jour individuelle.
OperationType | 1 - Installation<br> 0 - Rechercher et télécharger.| L’installation est le seul OperationType qui s’affiche dans les résultats par défaut.
UpdateClassification | Valeur par défaut : « securityupdates » | Type des mises à jour installées pendant l’opération de mise à jour
UpdateFrequency | Valeur par défaut : « Toutes les semaines, mercredi, 7:00:00 » | Fréquence de mise à jour configurée pour cette mise à jour.
RebootRequired | true : le redémarrage était requis<br> false : le redémarrage n’était pas requis | Indique qu’un redémarrage était requis pour terminer l’installation des mises à jour.
ApprovedList | Valeur par défaut : "" | Liste des correctifs approuvés pour cette mise à jour
RejectedList | Valeur par défaut : "" | Liste des correctifs rejetés pour cette mise à jour

Si aucune mise à jour n’est planifiée, le JSON de résultat est vide.

Connectez-vous au cluster pour interroger les résultats des mises à jour. Déterminez ensuite l’adresse de réplica du serveur principal du service Coordinateur, puis accédez à l’URL à partir du navigateur : http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults.

Le point de terminaison REST pour le service Coordinateur a un port dynamique. Pour vérifier l’URL exacte, reportez-vous à Service Fabric Explorer. Par exemple, les résultats sont disponibles à l’adresse `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Image de point de terminaison REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Événements de diagnostic et d’intégrité

### <a name="diagnostic-logs"></a>Journaux de diagnostic

Les journaux de l’application d’orchestration des correctifs sont collectés en même temps que les journaux du runtime Service Fabric.

Si vous le souhaitez, vous pouvez capturer les journaux au moyen du pipeline ou de l’outil de diagnostic de votre choix. L’application d’orchestration des correctifs utilise les ID de fournisseur fixes suivants pour journaliser les événements par le biais [d’eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Rapports d'intégrité

L’application d’orchestration des correctifs publie également des rapports d’intégrité concernant le service Coordinateur ou le service Agent du nœud dans les cas suivants :

#### <a name="an-update-operation-failed"></a>Échec d’une opération de mise à jour

Si une opération de mise à jour échoue sur un nœud, un rapport d’intégrité est généré par rapport au service Agent du nœud. Les détails du rapport d’intégrité contiennent le nom du nœud problématique.

Une fois la mise à jour corrective effectuée avec succès sur le nœud problématique, le rapport est automatiquement effacé.

#### <a name="the-node-agent-daemon-service-is-down"></a>Le service du démon Agent du nœud est inactif

Si le service du démon Agent du nœud est inactif sur un nœud, un rapport d’intégrité de niveau avertissement est généré par rapport au service Agent du nœud.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Le service de gestion des réparations n’est pas activé

Un rapport d’intégrité de niveau avertissement est généré par rapport au service Coordinateur si le service de gestion des réparations est introuvable sur le cluster.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Q. **Pourquoi mon cluster présente-t-il un état d’erreur lorsque l’application d’orchestration des correctifs est en cours d’exécution ?**

R. Pendant le processus d’installation, l’application d’orchestration des correctifs désactive ou redémarre les nœuds. Cette opération peut entraîner une dégradation temporaire de l’intégrité du cluster.

Selon la stratégie définie pour l’application, une opération de mise à jour corrective peut entraîner la dégradation d’un nœud isolé *ou* d’un domaine de mise à niveau entier.

À la fin de l’installation, les nœuds sont réactivés après redémarrage.

Dans l’exemple suivant, le cluster a présenté temporairement un état d’erreur parce que deux nœuds ont été désactivés et la stratégie MaxPercentageUnhealthyNodes a été violée. L’erreur persiste temporairement jusqu’à ce que l’opération de mise à jour corrective soit en cours.

![Image de cluster défectueux](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Si le problème persiste, voir la section Résolution des problèmes.

Q. **L’application d’orchestration des correctifs est en état d’avertissement**

R. Vérifiez si un rapport d’intégrité publié concernant l’application est la cause première. En règle générale, l’avertissement contient des détails concernant le problème. Si le problème est temporaire, l’application est supposée récupérer automatiquement de cet état.

Q. **Que faire si mon cluster est défectueux et que je dois effectuer une mise à jour urgente du système d’exploitation ?**

R. L’application d’orchestration des correctifs n’installe pas de mises à jour lorsque le cluster est défectueux. Pour débloquer le workflow de l’application d’orchestration des correctifs, essayez de ramener votre cluster à un état sain.

Q. **Pourquoi la mise à jour corrective des clusters prend-elle autant de temps ?**

R. Le temps dont l’application d’orchestration des correctifs a besoin dépend principalement des facteurs suivants :

- La stratégie du service Coordinateur. 
  - La stratégie par défaut, `NodeWise` a pour effet d’appliquer la mise à jour corrective à un seul nœud à la fois. En cas de cluster de grande taille, nous vous recommandons d’utiliser la stratégie `UpgradeDomainWise` pour accélérer la mise à jour corrective de tout le cluster.
- Le nombre de mises à jour disponibles pour téléchargement et installation. 
- La durée moyenne nécessaire pour télécharger et installer une mise à jour, qui ne devrait pas dépasser quelques heures.
- Les performances de la machine virtuelle et la bande passante réseau.

Q. **Comment l’application d’orchestration des correctifs identifie-t-elle les mises à jour de sécurité ?**

R. L’application d’orchestration des correctifs utilise une logique propre à la distribution pour déterminer quelles mises à jour sont des mises à jour de sécurité parmi toutes celles disponibles. Par exemple : Dans Ubuntu, l’application recherche les mises à jour à partir des archives $RELEASE-security, $RELEASE-updates ($RELEASE = xenial ou la version de base standard Linux). 

 
Q. **Comment puis-je verrouiller une version spécifique du package ?**

R. Utilisez les paramètres ApprovedPatches pour verrouiller vos packages sur une version particulière. 


Q. **Que se passe-t-il pour les mises à jour automatiques activées dans Ubuntu ?**

R. Dès que vous installez l’application d’orchestration des correctifs sur votre cluster, les mises à niveau sans assistance sont désactivées sur votre nœud de cluster. Tout le workflow de mise à jour périodique est piloté par l’application d’orchestration des correctifs.
Pour que l’environnement reste cohérent dans tout le cluster, nous vous recommandons d’installer les mises à jour uniquement par le biais de l’application d’orchestration des correctifs. 
 
Q. **Après la mise à niveau, l’application d’orchestration des correctifs effectue-t-elle le nettoyage des packages inutilisés ?**

R. Oui, le nettoyage se produit dans le cadre des étapes post-installation. 

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="a-node-is-not-coming-back-to-up-state"></a>Un nœud ne se réactive pas

**Le nœud peut être bloqué en état de désactivation pour les raisons suivantes** :

Une vérification de sécurité est en attente. Pour résoudre ce problème, assurez-vous que les nœuds en état d’intégrité normal disponibles sont en nombre suffisant.

**Le nœud peut être bloqué en état désactivé pour les raisons suivantes** :

- Le nœud a été désactivé manuellement.
- Le nœud a été désactivé en raison d’un travail en cours sur l’infrastructure Azure.
- Le nœud a été désactivé temporairement par l’application d’orchestration des correctifs afin d’appliquer un correctif au nœud.

**Le nœud peut être bloqué en état inactif pour les raisons suivantes** :

- Le nœud a été mis en état inactif manuellement.
- Le nœud est en cours de redémarrage (qui peut être déclenché par l’application d’orchestration des correctifs).
- Le nœud est en état inactif en raison d’une défaillance de machine virtuelle ou d’ordinateur, ou de problèmes de connectivité réseau.

### <a name="updates-were-skipped-on-some-nodes"></a>Des mises à jour ont été ignorées sur certains nœuds

L’application d’orchestration des correctifs essaie d’installer une mise à jour conformément à la stratégie de replanification. Le service tente de récupérer le nœud et ignore la mise à jour en vertu de la stratégie d’application.

Dans ce cas, un rapport d’intégrité de niveau avertissement est généré concernant le service Agent du nœud. Le résultat de la mise à jour contient également la cause possible de l’échec.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>L’intégrité du cluster présente un état d’erreur pendant l’installation de la mise à jour

Une mise à jour défectueuse peut dégrader l’intégrité d’une application ou d’un cluster sur un nœud ou un domaine de mise à niveau particuliers. L’application d’orchestration des correctifs interrompt toute autre opération de mise à jour jusqu’à ce que le cluster retrouve son intégrité.

Un administrateur doit intervenir et déterminer la raison pour laquelle l’application ou le cluster ont perdu leur intégrité suite à une mise à jour précédemment installée.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

L’application d’orchestration des correctifs collecte des données de télémétrie pour effectuer le suivi de l’utilisation et des performances. La télémétrie de l’application suit la valeur du paramètre de télémétrie du runtime Service Fabric (activé par défaut).

## <a name="release-notes"></a>Notes de publication

### <a name="version-010"></a>Version 0.1.0
- Préversion privée

### <a name="version-200-latest"></a>Version 2.0.0 (la plus récente)
- Version publique
