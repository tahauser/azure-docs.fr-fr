---
title: "Modifier les paramètres de cluster Azure Service Fabric | Microsoft Docs"
description: "Cet article décrit les paramètres de structure et les stratégies de mise à niveau de la structure que vous pouvez personnaliser."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/09/2018
ms.author: chackdan
ms.openlocfilehash: 23f063d89c5030d440d50765eee9d121b4d8f5ba
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Personnaliser les paramètres de cluster Service Fabric et la stratégie de mise à niveau de la structure
Ce document vous explique comment personnaliser les différents paramètres et la stratégie de mise à niveau de la structure pour votre cluster Service Fabric. Vous pouvez les personnaliser sur le [portail Azure](https://portal.azure.com) ou à l’aide d’un modèle Azure Resource Manager.

> [!NOTE]
> Tous les paramètres ne sont pas disponibles dans le portail. Si un paramètre répertorié ci-dessous n’est pas disponible via le portail, personnalisez-le à l’aide d’un modèle Azure Resource Manager.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personnaliser les paramètres de cluster à l’aide de modèles Resource Manager
Les étapes ci-dessous montrent comment ajouter un nouveau paramètre *MaxDiskQuotaInMB* à la section *Diagnostics*.

1. Accédez à https://resources.azure.com.
2. Accédez à votre abonnement en développant **Abonnements** -> **Groupes de ressources** -> **Microsoft.ServiceFabric** -> **\<Nom de votre cluster>**.
3. Dans l’angle supérieur droit, sélectionnez **Lecture/écriture**.
4. Sélectionnez **Modifier**, mettez à jour l’élément JSON `fabricSettings` et ajoutez un nouvel élément :

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Voici une liste des paramètres Fabric que vous pouvez personnaliser, classés par section.

### <a name="section-name-diagnostics"></a>Nom de la section : Diagnostics
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| ConsumerInstances |Chaîne | Dynamique |Liste des instances de consommateur DCA. |
| ProducerInstances |Chaîne | Dynamique |Liste des instances de producteur DCA. |
| AppEtwTraceDeletionAgeInDays |Entier (valeur par défaut : 3) | Dynamique |Délai en jours à l’issue duquel nous supprimons les anciens fichiers ETL contenant les traces ETW d’application. |
| AppDiagnosticStoreAccessRequiresImpersonation |Valeur booléenne (valeur par défaut : true) | Dynamique |Indique si l’emprunt d’identité est requis lors de l’accès aux magasins de diagnostics pour le compte de l’application. |
| MaxDiskQuotaInMB |Entier (valeur par défaut : 65536) | Dynamique |Quota de disque (en Mo) pour les fichiers journaux de Windows Fabric. |
| DiskFullSafetySpaceInMB |Entier (valeur par défaut : 1024) | Dynamique |Espace disque restant (en Mo) à protéger contre toute utilisation par DCA. |
| ApplicationLogsFormatVersion |Entier (valeur par défaut : 0) | Dynamique |Version du format des journaux de l’application. Valeurs prises en charge : 0 et 1. La version 1 comprend davantage de champs de l’enregistrement d’événement ETW que la version 0. |
| ClusterId |Chaîne | Dynamique |ID unique du cluster. Valeur générée lors de la création du cluster. |
| EnableTelemetry |Valeur booléenne (valeur par défaut : true) | Dynamique |Ce paramètre active ou désactive la télémétrie. |
| EnableCircularTraceSession |Valeur booléenne (valeur par défaut : false) | statique |Indicateur spécifiant si les sessions de trace circulaire doivent être utilisées. |

### <a name="section-name-traceetw"></a>Nom de la section : Trace/Etw
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| Level |Entier (valeur par défaut : 4) | Dynamique |Le niveau etw de la trace accepte les valeurs 1, 2, 3, 4. Pour assurer la prise en charge, vous devez conserver le niveau de trace sur 4 |

### <a name="section-name-performancecounterlocalstore"></a>Nom de la section : PerformanceCounterLocalStore
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| IsEnabled |Valeur booléenne (valeur par défaut : true) | Dynamique |Indicateur spécifiant si la collection de compteurs de performance sur le nœud local est activée. |
| SamplingIntervalInSeconds |Entier (valeur par défaut : 60) | Dynamique |Intervalle d’échantillonnage des compteurs de performances collectés. |
| Counters |Chaîne | Dynamique |Liste séparée par des virgules, des compteurs de performance à collecter. |
| MaxCounterBinaryFileSizeInMB |Entier (valeur par défaut : 1) | Dynamique |Taille maximale (en Mo) de chaque fichier binaire de compteur de performances. |
| NewCounterBinaryFileCreationIntervalInMinutes |Entier (valeur par défaut : 10) | Dynamique |Durée maximale (en secondes) après lequel un fichier binaire de compteur de performances est créé. |

### <a name="section-name-setup"></a>Nom de la section : Setup
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| FabricDataRoot |Chaîne | Non autorisée |Répertoire racine des données Service Fabric. La valeur par défaut pour Azure est d:\svcfab |
| FabricDataRoot |Chaîne | Non autorisée |Répertoire racine du journal Service Fabric. Il s’agit de l’emplacement des journaux et des traces de SF. |
| ServiceRunAsAccountName |Chaîne | Non autorisée |Nom du compte sous lequel exécuter le service hôte Fabric. |
| SkipFirewallConfiguration |Valeur booléenne (valeur par défaut : false) | Non autorisée |Indique si les paramètres de pare-feu doivent être définis par le système ou non. Cela s’applique uniquement si vous utilisez le pare-feu Windows. Si vous utilisez des pare-feu tiers, vous devez alors ouvrir les ports que le système et les applications doivent utiliser |
|NodesToBeRemoved|Chaîne (valeur par défaut : "")| Dynamique |Les nœuds qui doivent être supprimés dans le cadre de la mise à niveau de la configuration. (Uniquement pour les déploiements autonomes)|
|ContainerNetworkSetup|valeur booléenne, valeur par défaut : FALSE| statique |Spécifie si un réseau de conteneur doit être configuré.|
|ContainerNetworkName|chaîne, valeur par défaut : L""| statique |Le nom du réseau à utiliser lors de la configuration d’un réseau de conteneur.|

### <a name="section-name-transactionalreplicator"></a>Nom de la section : TransactionalReplicator
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| MaxCopyQueueSize |Valeur Uint (valeur par défaut : 16384) | statique |Valeur maximale définissant la taille initiale de la file d’attente qui gère les opérations de réplication. Ce nombre doit être une puissance de 2. Si, pendant l’exécution, la file d’attente atteint cette taille, les opérations sont limitées entre les réplicateurs principal et secondaire. |
| BatchAcknowledgementInterval | Durée en secondes (valeur par défaut : 0.015) | statique | Spécifiez la durée en secondes. Détermine le délai respecté par le réplicateur après la réception d’une opération et avant de renvoyer un accusé de réception. Les autres opérations reçues pendant cette période verront leur accusé de réception renvoyé dans un seul message, ce qui diminue le trafic réseau mais peut potentiellement réduire le débit du réplicateur. |
| MaxReplicationMessageSize |Valeur Uint (valeur par défaut : 52428800) | statique | Taille maximale des messages des opérations de réplication. Valeur par défaut : 50 Mo. |
| ReplicatorAddress |Chaîne (valeur par défaut : "localhost:0") | statique | Point de terminaison sous la forme d’une chaîne « IP:port» utilisée par le réplicateur Windows Fabric pour se connecter à d’autres réplicas afin d’envoyer/recevoir des opérations. |
| InitialPrimaryReplicationQueueSize |Valeur Uint (valeur par défaut : 64) | statique |Valeur définissant la taille initiale de la file d’attente qui gère les opérations de réplication sur le réplicateur principal. Ce nombre doit être une puissance de 2.|
| MaxPrimaryReplicationQueueSize |Valeur Uint (valeur par défaut : 8192) | statique |Nombre maximum d’opérations pouvant exister dans la file d’attente de réplication principale. Ce nombre doit être une puissance de 2. |
| MaxPrimaryReplicationQueueMemorySize |Valeur Uint (valeur par défaut : 0) | statique |Valeur maximale de la file d’attente de réplication principale en octets. |
| InitialSecondaryReplicationQueueSize |Valeur Uint (valeur par défaut : 64) | statique |Valeur définissant la taille initiale de la file d’attente qui gère les opérations de réplication sur le réplicateur secondaire. Ce nombre doit être une puissance de 2. |
| MaxSecondaryReplicationQueueSize |Valeur Uint (valeur par défaut : 16384) | statique |Nombre maximum d’opérations pouvant exister dans la file d’attente de réplication secondaire. Ce nombre doit être une puissance de 2. |
| MaxSecondaryReplicationQueueMemorySize |Valeur Uint (valeur par défaut : 0) | statique |Valeur maximale de la file d’attente de réplication secondaire en octets. |
| SecondaryClearAcknowledgedOperations |Valeur booléenne (valeur par défaut : false) | statique |Valeur booléenne qui contrôle si les opérations sur le réplicateur secondaire sont effacées, une fois leur réception confirmée au réplicateur principal (vidées sur le disque). Le réglage de ce paramètre sur TRUE augmente le nombre de lectures de disque sur le nouveau réplica principal, lors du traitement des réplicas après un basculement. |
| MaxMetadataSizeInKB |Entier (valeur par défaut : 4) |Non autorisée|Taille maximale des métadonnées du flux de journal. |
| MaxRecordSizeInKB |Valeur Uint (valeur par défaut : 1024) |Non autorisée| Taille maximale de l’enregistrement du flux de journal. |
| CheckpointThresholdInMB |Entier (valeur par défaut : 50) |statique|Un point de contrôle est initialisé lorsque l’utilisation du journal dépasse cette valeur. |
| MaxAccumulatedBackupLogSizeInMB |Entier (valeur par défaut : 800) |statique|Taille cumulée maximale (en Mo) des journaux de sauvegarde dans une séquence de journaux de sauvegarde donnée. Des demandes de sauvegarde incrémentielle échouent si la sauvegarde incrémentielle génère un journal de sauvegarde qui provoque la cumulation des journaux de sauvegarde, étant donné que la sauvegarde complète pertinente est supérieure à cette taille. Dans ce cas, l’utilisateur doit effectuer une sauvegarde complète. |
| MaxWriteQueueDepthInKB |Entier (valeur par défaut : 0) |Non autorisée| Nombre entier correspondant à la profondeur de la file d’attente d’écritures que le journal de base peut utiliser de la manière spécifiée, en kilo-octets, pour le journal associé à ce réplica. Cette valeur est le nombre maximum d’octets pouvant être en attente pendant les mises à jour du journal de base. Ce peut être 0 pour que le journal de base calcule une valeur appropriée, ou un multiple de 4. |
| SharedLogId |Chaîne |Non autorisée|Identificateur du journal partagé. Cette valeur est un GUID et doit être propre à chaque journal partagé. |
| SharedLogPath |Chaîne |Non autorisée|Chemin d’accès au journal partagé. Si cette valeur n’est pas spécifiée, le journal partagé par défaut est utilisé. |
| SlowApiMonitoringDuration |Durée en secondes, la valeur par défaut est 300 |statique| Spécifiez la durée de l’API avant l’émission d’un événement d’avertissement.|
| MinLogSizeInMB |Entier (valeur par défaut : 0) |statique|Taille minimale du journal des transactions. Le journal ne peut pas être tronqué à une taille inférieure à ce paramètre. 0 indique que le réplicateur détermine la taille minimale du journal en fonction d’autres paramètres. Si vous augmentez cette valeur, vous augmentez la possibilité de faire des copies partielles et des sauvegardes incrémentielles, car la probabilité de la troncation des enregistrements de journaux pertinents est réduite. |

### <a name="section-name-fabricclient"></a>Nom de la section : FabricClient
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| NodeAddresses |Chaîne (valeur par défaut : "") |statique|Collection d’adresses (chaînes de connexion) sur différents nœuds pouvant servir à communiquer avec le service d’attribution de noms. Au début, le client se connecte en sélectionnant une des adresses de façon aléatoire. Si plusieurs chaînes de connexion sont fournies et qu’une échoue en raison d’une erreur de communication ou de délai d’attente, le client utilise l’adresse suivante dans la liste. Pour plus d’informations sur la sémantique des nouvelles tentatives, consultez la section sur les nouvelles tentatives concernant l’adresse du service d’attribution de noms. |
| ConnectionInitializationTimeout |Durée en secondes (valeur par défaut : 2) |Dynamique|Spécifiez la durée en secondes. Délai d’expiration de connexion à respecter pour chaque client avant de tenter d’établir une connexion à la passerelle. |
| PartitionLocationCacheLimit |Entier (valeur par défaut : 100000) |statique|Nombre de partitions mises en cache pour la résolution de service (0 pour aucune limite). |
| ServiceChangePollInterval |Durée en secondes (valeur par défaut : 120) |Dynamique|Spécifiez la durée en secondes. L’intervalle entre deux interrogations consécutives de modifications de service est différent entre le client et la passerelle pour les rappels de notifications de modification de service enregistrés. |
| KeepAliveIntervalInSeconds |Entier (valeur par défaut : 20) |statique|Durée pendant laquelle le transport FabricClient envoie des messages de conservation d’activité à la passerelle. À 0, le paramètre keepAlive est désactivé. Cette valeur doit être un entier positif. |
| HealthOperationTimeout |Durée en secondes (valeur par défaut : 120) |Dynamique|Spécifiez la durée en secondes. Délai d’expiration d’un message de rapport envoyé au Gestionnaire d’intégrité. |
| HealthReportSendInterval |Durée en secondes, la valeur par défaut est 30 |Dynamique|Spécifiez la durée en secondes. Délai à l’issue duquel le composant concerné envoie les rapports d’intégrité cumulés au Gestionnaire d’intégrité. |
| HealthReportRetrySendInterval |Durée en secondes, la valeur par défaut est 30 |Dynamique|Spécifiez la durée en secondes. Délai à l’issue duquel le composant concerné envoie des rapports d’intégrité cumulés au Gestionnaire d’intégrité. |
| RetryBackoffInterval |Durée en secondes (valeur par défaut : 3) |Dynamique|Spécifiez la durée en secondes. Délai de temporisation à respecter avant de retenter l’opération. |
| MaxFileSenderThreads |Valeur Uint (valeur par défaut : 10) |statique|Nombre maximum de fichiers transférés en parallèle. |

### <a name="section-name-common"></a>Nom de la section : Common
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| PerfMonitorInterval |Durée en secondes (valeur par défaut : 1) |Dynamique|Spécifiez la durée en secondes. Interface de surveillance des performances. Une valeur nulle ou négative désactive la surveillance. |

### <a name="section-name-healthmanager"></a>Nom de la section : HealthManager
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Valeur booléenne (valeur par défaut : false) |statique|Stratégie d’évaluation de l’intégrité du cluster : activer pour chaque évaluation de l’intégrité du type d’application. |

### <a name="section-name-nodedomainids"></a>Nom de la section : NodeDomainIds
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| UpgradeDomainId |Chaîne (valeur par défaut : "") |statique|Décrit le domaine de mise à niveau auquel un nœud appartient. |
| PropertyGroup |NodeFaultDomainIdCollection |statique|Décrit les domaines d’erreur auquel un nœud appartient. Le domaine par défaut est défini par un URI qui décrit l’emplacement du nœud dans le centre de données.  Les URI de domaine d’erreur sont au format de:/de suivi d’un segment de chemin d’accès d’URI.|

### <a name="section-name-nodeproperties"></a>Nom de la section : NodeProperties
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |statique|Collection de paires clé-valeur de chaîne pour les propriétés du nœud. |

### <a name="section-name-nodecapacities"></a>Nom de la section : NodeCapacities
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |statique|Collection des capacités du nœud pour les différentes mesures. |

### <a name="section-name-fabricnode"></a>Nom de la section : FabricNode
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| StateTraceInterval |Durée en secondes, la valeur par défaut est 300 |statique|Spécifiez la durée en secondes. Durée du suivi d’état sur chaque nœud et les nœuds actifs sur FM/FMM. |
| StartApplicationPortRange |Entier (valeur par défaut : 0) |statique|Début des ports d’application gérés par le sous-système d’hébergement. Requis si EndpointFilteringEnabled a la valeur true dans Hosting. |
| EndApplicationPortRange |Entier (valeur par défaut : 0) |statique|Fin (non inclusive) des ports d’application gérés par le sous-système d’hébergement. Requis si EndpointFilteringEnabled a la valeur true dans Hosting. |
| ClusterX509StoreName |Chaîne (valeur par défaut : "My") |Dynamique|Nom du magasin de certificats X.509 qui contient le certificat de cluster utilisé pour sécuriser les communications internes au cluster. |
| ClusterX509FindType |Chaîne (valeur par défaut : "FindByThumbprint") |Dynamique|Indique comment rechercher le certificat de cluster dans le magasin spécifié par les valeurs ClusterX509StoreName prises en charge : « FindByThumbprint » ; « FindBySubjectName » avec « FindBySubjectName ». S’il y a plusieurs correspondances, celui ayant la date d’expiration la plus lointaine est utilisé. |
| ClusterX509FindValue |Chaîne (valeur par défaut : "") |Dynamique|Valeur de filtre de recherche utilisée pour localiser le certificat de cluster. |
| ClusterX509FindValueSecondary |Chaîne (valeur par défaut : "") |Dynamique|Valeur de filtre de recherche utilisée pour localiser le certificat de cluster. |
| ServerAuthX509StoreName |Chaîne (valeur par défaut : "My") |Dynamique|Nom du magasin de certificats X.509 qui contient le certificat de serveur pour le service d’entrée. |
| ServerAuthX509FindType |Chaîne (valeur par défaut : "FindByThumbprint") |Dynamique|Indique comment rechercher le certificat de serveur dans le magasin spécifié par la valeur ServerAuthX509StoreName prise en charge : FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |Chaîne (valeur par défaut : "") |Dynamique|Valeur de filtre de recherche utilisée pour localiser le certificat de serveur. |
| ServerAuthX509FindValueSecondary |Chaîne (valeur par défaut : "") |Dynamique|Valeur de filtre de recherche utilisée pour localiser le certificat de serveur. |
| ClientAuthX509StoreName |Chaîne (valeur par défaut : "My") |Dynamique|Nom du magasin de certificats X.509 qui contient le certificat pour le rôle d’administrateur par défaut FabricClient. |
| ClientAuthX509FindType |Chaîne (valeur par défaut : "FindByThumbprint") |Dynamique|Indique comment rechercher le certificat de serveur dans le magasin spécifié par la valeur ClientAuthX509StoreName prise en charge : FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |Chaîne (valeur par défaut : "") | Dynamique|Valeur de filtre de recherche utilisée pour localiser le certificat du rôle d’administrateur par défaut FabricClient. |
| ClientAuthX509FindValueSecondary |Chaîne (valeur par défaut : "") |Dynamique|Valeur de filtre de recherche utilisée pour localiser le certificat du rôle d’administrateur par défaut FabricClient. |
| UserRoleClientX509StoreName |Chaîne (valeur par défaut : "My") |Dynamique|Nom du magasin de certificats X.509 qui contient le certificat pour le rôle d’utilisateur par défaut FabricClient. |
| UserRoleClientX509FindType |Chaîne (valeur par défaut : "FindByThumbprint") |Dynamique|Indique comment rechercher le certificat dans le magasin spécifié par la valeur UserRoleClientX509StoreName prise en charge : FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |Chaîne (valeur par défaut : "") |Dynamique|Valeur de filtre de recherche utilisée pour localiser le certificat du rôle d’utilisateur par défaut FabricClient. |
| UserRoleClientX509FindValueSecondary |Chaîne (valeur par défaut : "") |Dynamique|Valeur de filtre de recherche utilisée pour localiser le certificat du rôle d’utilisateur par défaut FabricClient. |

### <a name="section-name-paas"></a>Nom de la section : Paas
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| ClusterId |Chaîne (valeur par défaut : "") |Non autorisée|Magasin de certificats X509 utilisé par l’infrastructure pour protéger la configuration. |

### <a name="section-name-fabrichost"></a>Nom de la section : FabricHost
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| StopTimeout |Durée en secondes, la valeur par défaut est 300 |Dynamique|Spécifiez la durée en secondes. Délai d’attente pour l’activation, la désactivation et la mise à niveau du service hébergé. |
| StartTimeout |Durée en secondes, la valeur par défaut est 300 |Dynamique|Spécifiez la durée en secondes. Délai d’expiration pour le démarrage de fabricactivationmanager. |
| ActivationRetryBackoffInterval |Durée en secondes (valeur par défaut : 5) |Dynamique|Spécifiez la durée en secondes. Intervalle d’interruption à chaque échec d’activation. À chaque échec d’activation continue, le système retente l’activation jusqu’à atteindre la valeur du paramètre MaxActivationFailureCount. L’intervalle avant nouvelle tentative pour chaque tentative est une combinaison de l’échec de l’activation continue et de l’intervalle d’interruption d’activation. |
| ActivationMaxRetryInterval |Durée en secondes, la valeur par défaut est 300 |Dynamique|Spécifiez la durée en secondes. Intervalle maximum avant une nouvelle tentative d’activation. À chaque échec continu, l’intervalle avant nouvelle tentative est calculé de la manière suivante : Min (ActivationMaxRetryInterval; Nombre d’échecs continus * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Entier (valeur par défaut : 10) |Dynamique|Nombre maximum pour lequel le système va relancer l’activation ayant échoué avant d’abandonner. |
| EnableServiceFabricAutomaticUpdates |Valeur booléenne (valeur par défaut : false) |Dynamique|Ce paramètre permet d’activer les mises à jour automatiques de l’infrastructure via Windows Update. |
| EnableServiceFabricBaseUpgrade |Valeur booléenne (valeur par défaut : false) |Dynamique|Ce paramètre active la mise à jour de base du serveur. |
| EnableRestartManagement |Valeur booléenne (valeur par défaut : false) |Dynamique|Ce paramètre active le redémarrage du serveur. |


### <a name="section-name-failovermanager"></a>Nom de la section : FailoverManager
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| UserReplicaRestartWaitDuration |Durée en secondes (valeur par défaut : 60.0 * 30) |Dynamique|Spécifiez la durée en secondes. Lorsqu’un réplica persistant tombe en panne, Windows Fabric attend pendant cette durée que le réplica revienne en ligne avant de créer d’autres réplicas de remplacement (ce qui nécessite une copie de l’état). |
| QuorumLossWaitDuration |Durée en secondes (valeur par défaut : MaxValue) |Dynamique|Spécifiez la durée en secondes. Il s’agit de la durée maximale pour laquelle nous autorisons une partition à être dans un état de perte de quorum. Si la partition est toujours en perte de quorum au-delà de cette durée, elle est rétablie en considérant les réplicas arrêtés comme perdus. Notez que cela peut potentiellement provoquer une perte de données. |
| UserStandByReplicaKeepDuration |Durée en secondes (valeur par défaut : 3600.0 * 24 * 7) |Dynamique|Spécifiez la durée en secondes. Lorsqu’un réplica persistant n’est plus défaillant, il peut avoir déjà été remplacé. Ce minuteur détermine la durée pendant laquelle le FM conserve le réplica en attente avant de le rejeter. |
| UserMaxStandByReplicaCount |Entier (valeur par défaut : 1) |Dynamique|Nombre maximum par défaut de réplicas en attente que le système conserve pour les services de l’utilisateur. |
| ExpectedClusterSize|entier, valeur par défaut : 1|Dynamique|Lorsque le cluster est démarré pour la première fois, le FM attendra que ce nombre de nœuds soit atteint avant de placer d’autres services, notamment les services système comme l’affectation de noms.  Accroître cette valeur augmente le temps nécessaire au démarrage d’un cluster mais empêche toute surcharge des premiers nœuds ainsi que les déplacements supplémentaires qui sont nécessaires à mesure que d’autres nœuds sont mis en ligne.  Cette valeur doit être définie en général sur une petite fraction de la taille initiale du cluster. |
|ClusterPauseThreshold|entier, valeur par défaut : 1|Dynamique|Si le nombre de nœuds dans le système passe sous cette valeur, le placement, l’équilibrage de charge et le basculement sont arrêtés. |
|TargetReplicaSetSize|entier, valeur par défaut : 7|Non autorisée|Il s’agit du nombre cible de réplicas FM que Windows Fabric conservera.  Un nombre plus élevé entraîne une plus grande fiabilité des données FM, avec peu d’impact sur les performances. |
|MinReplicaSetSize|entier, valeur par défaut : 3|Non autorisée|Taille minimale du jeu de réplicas pour le FM.  Si le nombre de réplicas FM actifs passe sous cette valeur, le FM rejette toutes les modifications apportées au cluster tant que le nombre minimal de réplicas n’a pas été récupéré |
|ReplicaRestartWaitDuration|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(60.0 * 30)|Non autorisée|Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour FMService |
|StandByReplicaKeepDuration|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(3600.0 * 24 * 7)|Non autorisée|Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour FMService |
|PlacementConstraints|chaîne, valeur par défaut : L""|Non autorisée|Toutes les contraintes de placement pour les réplicas de Failover Manager |
|ExpectedNodeFabricUpgradeDuration|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamique|Spécifiez la durée en secondes. La durée prévue de la mise à niveau d’un nœud lors de la mise à niveau de Windows Fabric. |
|ExpectedReplicaUpgradeDuration|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamique|Spécifiez la durée en secondes. La durée prévue de la mise à niveau de tous les réplicas sur un nœud lors de la mise à niveau de l’application. |
|ExpectedNodeDeactivationDuration|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(60.0 * 30)|Dynamique|Spécifiez la durée en secondes. La durée prévue de la désactivation complète d’un nœud. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|Valeur booléenne, valeur par défaut : TRUE|Dynamique|Si cette valeur est définie sur true, les réplicas avec une taille de jeu de réplicas cible de 1 seront autorisés à se déplacer lors de la mise à niveau. |
|ReconfigurationTimeLimit|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(300)|Dynamique|Spécifiez la durée en secondes. Le délai de reconfiguration après lequel un rapport d’intégrité Warning est généré |
|BuildReplicaTimeLimit|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(3600)|Dynamique|Spécifiez la durée en secondes. Le délai de construction d’un réplica avec état après lequel un rapport d’intégrité Warning est généré |
|CreateInstanceTimeLimit|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(300)|Dynamique|Spécifiez la durée en secondes. Le délai de création d’un réplica sans état après lequel un rapport d’intégrité Warning est généré |
|PlacementTimeLimit|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(600)|Dynamique|Spécifiez la durée en secondes. Le délai pour atteindre le nom de réplicas cible après lequel un rapport d’intégrité Warning est généré |

### <a name="section-name-namingservice"></a>Nom de la section : NamingService
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Entier (valeur par défaut : 7) |Non autorisée|Nombre de jeux de réplicas pour chaque partition de la banque du magasin du service d’attribution de noms. En augmentant le nombre de jeux de réplicas, vous augmentez le niveau de fiabilité des informations contenues dans le magasin du Service d’attribution de noms, ce qui diminue le risque de perte d’informations suite à des défaillances de nœud, moyennant une charge accrue sur Windows Fabric et le temps nécessaire pour mettre à jour les données d’attribution de noms.|
|MinReplicaSetSize | Entier (valeur par défaut : 3) |Non autorisée| Nombre minimum de réplicas du Service d’attribution de noms requis dans lesquels effectuer des écritures pour finaliser une mise à jour. Si le nombre de réplicas actifs dans le système est inférieur à cette valeur, le système de fiabilité refuse les mises à jour du magasin du Service d’attribution de noms jusqu’à ce que les réplicas soient restaurés. Cette valeur ne doit jamais être supérieure à TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Durée en secondes (valeur par défaut : 60.0 * 30)|Non autorisée| Spécifiez la durée en secondes. Ce minuteur démarre lorsqu’un réplica du service d’attribution de noms tombe en panne.  Lorsqu’il arrive à expiration, le FM commence à remplacer les réplicas qui sont arrêtés (il ne les considère pas encore comme perdus). |
|QuorumLossWaitDuration | Durée en secondes (valeur par défaut : MaxValue) |Non autorisée| Spécifiez la durée en secondes. Ce minuteur démarre lorsqu’un service d’attribution de noms passe en perte de quorum.  Lorsque ce délai est écoulé, le FM considère les réplicas arrêtés comme perdus et tente de rétablir le quorum. Notez que cela peut entraîner une perte de données. |
|StandByReplicaKeepDuration | Durée en secondes (valeur par défaut : 3600.0*2) |Non autorisée| Spécifiez la durée en secondes. Lorsqu’un réplica du Service d’attribution de noms n’est plus défaillant, il peut avoir déjà été remplacé.  Ce minuteur détermine la durée pendant laquelle le FM conserve le réplica en attente avant de le rejeter. |
|PlacementConstraints | Chaîne (valeur par défaut : "") |Non autorisée| Contrainte de placement pour le Service d’attribution de noms. |
|ServiceDescriptionCacheLimit | Entier (valeur par défaut : 0) |statique| Nombre maximum d’entrées maintenues dans le cache de description du service LRU au niveau du magasin du Service d’attribution de noms (0 correspond à aucune limite). |
|RepairInterval | Durée en secondes (valeur par défaut : 5) |statique| Spécifiez la durée en secondes. Durée pendant laquelle la réparation d’une incohérence d’attribution de noms entre le propriétaire de l’autorité et le propriétaire du nom démarre. |
|MaxNamingServiceHealthReports | Entier (valeur par défaut : 10) |Dynamique|Nombre maximum d’opérations lentes que le magasin du Service d’attribution de noms signale comme défectueuses à la fois. Si vous spécifiez 0, toutes les opérations lentes sont envoyées. |
| MaxMessageSize |Entier, valeur par défaut : 4\*1024\*1024 |statique|Taille maximale des messages pour la communication de nœuds clients lors de l’utilisation du service d’attribution de noms. Atténuation d’attaque par déni de service. Valeur par défaut : 4 Mo. |
| MaxFileOperationTimeout |Durée en secondes, la valeur par défaut est 30 |Dynamique|Spécifiez la durée en secondes. Délai d’attente maximum autorisé pour l’opération du service de magasin de fichiers. Les requêtes spécifiant un délai d’expiration supérieur seront rejetées. |
| MaxOperationTimeout |Durée en secondes (valeur par défaut : 600) |Dynamique|Spécifiez la durée en secondes. Délai d’attente maximum autorisé pour les opérations du client. Les requêtes spécifiant un délai d’expiration supérieur seront rejetées. |
| MaxClientConnections |Entier, valeur par défaut : 1000 |Dynamique|Nombre maximum autorisé de connexions clientes par passerelle. |
| ServiceNotificationTimeout |Durée en secondes, la valeur par défaut est 30 |Dynamique|Spécifiez la durée en secondes. Délai d’expiration utilisé lors de l’envoi des notifications du service au client. |
| MaxOutstandingNotificationsPerClient |Entier, valeur par défaut : 1000 |Dynamique|Nombre maximum de notifications en attente avant la fermeture forcée de l’inscription du client par la passerelle. |
| MaxIndexedEmptyPartitions |Entier, valeur par défaut : 1000 |Dynamique|Nombre maximum de partitions vides qui resteront indexées dans le cache de notifications pour synchroniser les clients qui se reconnectent. Toutes les partitions vides au-delà de ce nombre seront supprimées de l’index dans l’ordre croissant des versions de recherche. Les clients qui se reconnectent peuvent toujours synchroniser et recevoir les mises à jour manquantes des partitions vides, mais le protocole de synchronisation devient plus coûteux. |
| GatewayServiceDescriptionCacheLimit |Entier (valeur par défaut : 0) |statique|Nombre maximum d’entrées maintenues dans le cache de description du service LRU au niveau de la passerelle d’attribution de noms (0 correspond à aucune limite). |
| PartitionCount |Entier (valeur par défaut : 3) |Non autorisée|Nombre de partitions du magasin du Service d’attribution de noms à créer. Chaque partition possède sa clé de partition unique qui correspond à son index. Les clés de partition [0; PartitionCount) existent donc. En augmentant le nombre de partitions du Services d’attribution de noms, vous augmentez l’étendue sur laquelle intervient le Service d’attribution de noms, tout en diminuant le volume moyen de données stockées par un jeu de réplicas de secours, moyennant une utilisation accrue des ressources (car les réplicas de service PartitionCount*ReplicaSetSize doivent être maintenus).|

### <a name="section-name-runas"></a>Nom de la section : RunAss
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| RunAsAccountName |Chaîne (valeur par défaut : "") |Dynamique|Indique le nom du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser ou ManagedServiceAccount. Les valeurs autorisées sont "domain\user" ou "user@domain". |
|RunAsAccountType|Chaîne (valeur par défaut : "") |Dynamique|Indique le type du compte RunAs. Ce paramètre est nécessaire pour toutes les sections RunAs. Les valeurs autorisées DomainUser/NetworkService/ManagedServiceAccount/LocalSystem.|
|RunAsPassword|Chaîne (valeur par défaut : "") |Dynamique|Indique le mot de passe du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser. |

### <a name="section-name-runasfabric"></a>Nom de la section : RunAs_Fabric
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| RunAsAccountName |Chaîne (valeur par défaut : "") |Dynamique|Indique le nom du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser ou ManagedServiceAccount. Les valeurs autorisées sont "domain\user" ou "user@domain". |
|RunAsAccountType|Chaîne (valeur par défaut : "") |Dynamique|Indique le type du compte RunAs. Ce paramètre est nécessaire pour toutes les sections RunAs. Les valeurs autorisées sont LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|Chaîne (valeur par défaut : "") |Dynamique|Indique le mot de passe du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser. |

### <a name="section-name-runashttpgateway"></a>Nom de la section : RunAs_HttpGateway
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| RunAsAccountName |Chaîne (valeur par défaut : "") |Dynamique|Indique le nom du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser ou ManagedServiceAccount. Les valeurs autorisées sont "domain\user" ou "user@domain". |
|RunAsAccountType|Chaîne (valeur par défaut : "") |Dynamique|Indique le type du compte RunAs. Ce paramètre est nécessaire pour toutes les sections RunAs. Les valeurs autorisées sont LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|Chaîne (valeur par défaut : "") |Dynamique|Indique le mot de passe du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser. |

### <a name="section-name-runasdca"></a>Nom de la section : RunAs_DCA
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| RunAsAccountName |Chaîne (valeur par défaut : "") |Dynamique|Indique le nom du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser ou ManagedServiceAccount. Les valeurs autorisées sont "domain\user" ou "user@domain". |
|RunAsAccountType|Chaîne (valeur par défaut : "") |Dynamique|Indique le type du compte RunAs. Ce paramètre est nécessaire pour toutes les sections RunAs. Les valeurs autorisées sont LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|Chaîne (valeur par défaut : "") |Dynamique|Indique le mot de passe du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser. |

### <a name="section-name-httpgateway"></a>Nom de la section : HttpGateway
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|IsEnabled|Valeur booléenne (valeur par défaut : false) |statique| Active/désactive le paramètre HttpGateway. HttpGateway est désactivé par défaut. |
|ActiveListeners |Valeur Uint (valeur par défaut : 50) |statique| Nombre de lectures à publier dans la file d’attente du serveur HTTP. Ce paramètre contrôle le nombre de requêtes concurrentes que la passerelle HTTP peut satisfaire. |
|MaxEntityBodySize |Valeur Uint (valeur par défaut : 4194304) |Dynamique|Ce paramètre indique la taille maximum du corps que l’on peut attendre d’une requête HTTP. Valeur par défaut : 4 Mo. Le paramètre httpgateway ne peut pas traiter une requête si son corps a une taille supérieure à cette valeur. La taille minimum du bloc de lectures est de 4096 octets. Cette valeur doit donc être supérieure ou égale à 4096. |
|HttpGatewayHealthReportSendInterval |Durée en secondes, la valeur par défaut est 30 |statique|Spécifiez la durée en secondes. Délai à l’issue duquel la passerelle HTTP envoie les rapports d’intégrité cumulés à Health Manager. |

### <a name="section-name-ktllogger"></a>Nom de la section : KtlLogger
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Entier (valeur par défaut : 1) |Dynamique|Indicateur spécifiant si les paramètres de mémoire doivent être configurés automatiquement et dynamiquement. Si vous spécifiez 0, les paramètres de configuration de la mémoire sont utilisés directement et ne sont pas modifiés par les conditions du système. Si vous spécifiez 1, les paramètres de mémoire sont configurés automatiquement et peuvent varier selon les conditions du système. |
|WriteBufferMemoryPoolMinimumInKB |Entier (valeur par défaut : 8388608) |Dynamique|Nombre de Ko à attribuer initialement au pool de mémoires-tampons d’écritures. Utilisez 0 pour spécifier aucune limite. La valeur par défaut doit être cohérente avec la valeur du paramètre SharedLogSizeInMB ci-dessous. |
|WriteBufferMemoryPoolMaximumInKB | Entier (valeur par défaut : 0) |Dynamique|Nombre de Ko maximum que le pool de mémoires-tampons d’écritures peut atteindre. Spécifiez 0 pour indiquer aucune limite. |
|MaximumDestagingWriteOutstandingInKB | Entier (valeur par défaut : 0) |Dynamique|Nombre de Ko que le journal partagé peut atteindre, en plus du journal dédié. Spécifiez 0 pour indiquer aucune limite.
|SharedLogPath |Chaîne (valeur par défaut : "") |statique|Chemin et nom de fichier du conteneur du journal partagé. Utilisez "" pour utiliser le chemin par défaut sous la racine des données de structure. |
|SharedLogId |Chaîne (valeur par défaut : "") |statique|GUID unique du conteneur du journal partagé. Utilisez "" si vous choisissez le chemin par défaut sous la racine des données de structure. |
|SharedLogSizeInMB |Entier (valeur par défaut : 8192) |statique|Nombre de Mo à allouer dans le conteneur de journal partagé. |

### <a name="section-name-applicationgatewayhttp"></a>Nom de la section : ApplicationGateway/Http
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|IsEnabled |Valeur booléenne (valeur par défaut : false) |statique| Active/désactive le paramètre HttpApplicationGateway. Le paramètre Httpgateway est désactivé par défaut et cette configuration doit être définie pour l’activer. |
|NumberOfParallelOperations | Valeur Uint (par défaut : 5000) |statique|Nombre de lectures à publier dans la file d’attente du serveur HTTP. Ce paramètre contrôle le nombre de requêtes concurrentes que la passerelle HTTP peut satisfaire. |
|DefaultHttpRequestTimeout |Durée en secondes. La valeur par défaut est 120 |Dynamique|Spécifiez la durée en secondes.  Indique le délai d’expiration par défaut des requêtes HTTP traitées par la passerelle HTTP. |
|ResolveServiceBackoffInterval |Durée en secondes (valeur par défaut : 5) |Dynamique|Spécifiez la durée en secondes.  Indique l’interface d’interruption par défaut avant de retenter une opération du service de résolution ayant échoué. |
|BodyChunkSize |Valeur Uint (valeur par défaut : 16384) |Dynamique| Indique la taille en octets du bloc utilisé pour lire le corps. |
|GatewayAuthCredentialType |Chaîne (valeur par défaut : "None") |statique| Indique le type d’informations d’identification de sécurité à utiliser comme point d’extrémité de la passerelle d’application HTTP. Les valeurs autorisées sont None/X509. |
|GatewayX509CertificateStoreName |Chaîne (valeur par défaut : "My") |Dynamique| Nom du magasin de certificats X.509 qui contient le certificat de la passerelle d’application HTTP. |
|GatewayX509CertificateFindType |Chaîne (valeur par défaut : "FindByThumbprint") |Dynamique| Indique comment rechercher un certificat dans le magasin spécifié par GatewayX509CertificateStoreName. Valeur prise en charge : FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | Chaîne (valeur par défaut : "") |Dynamique| Valeur du filtre de recherche utilisée pour localiser le certificat de la passerelle d’application HTTP. Ce certificat est configuré sur le point d’extrémité HTTPS et peut également servir à vérifier l’identité de l’application au besoin par les services. Le paramètre FindValue est consulté en premier. S’il n’existe pas, FindValueSecondary est consulté. |
|GatewayX509CertificateFindValueSecondary | Chaîne (valeur par défaut : "") |Dynamique|Valeur du filtre de recherche utilisée pour localiser le certificat de la passerelle d’application HTTP. Ce certificat est configuré sur le point d’extrémité HTTPS et peut également servir à vérifier l’identité de l’application au besoin par les services. Le paramètre FindValue est consulté en premier. S’il n’existe pas, FindValueSecondary est consulté.|
|HttpRequestConnectTimeout|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(5)|Dynamique|Spécifiez la durée en secondes.  Indique le délai d’expiration de la connexion des requêtes HTTP envoyées depuis la passerelle d’application HTTP.  |
|RemoveServiceResponseHeaders|chaîne, valeur par défaut : L"Date; Server"|statique|Liste séparée par des points-virgules/virgules d’en-têtes de réponse qui sera supprimée de la réponse du service avant son transfert vers le client. Si cette valeur est une chaîne vide, transmettre tels quels tous les en-têtes retournés par le service. Par exemple, ne pas remplacer la date et le serveur |
|ApplicationCertificateValidationPolicy|chaîne, valeur par défaut : L"None"|statique| ApplicationCertificateValidationPolicy : None : Ne pas valider le certificat de serveur ; échec de la demande. ServiceCertificateThumbprints : reportez-vous à la configuration ServiceCertificateThumbprints pour obtenir la liste séparée par des virgules des empreintes de certificats distants auxquels le proxy inversé peut faire confiance. ServiceCommonNameAndIssuer : reportez-vous à la configuration ServiceCommonNameAndIssuer pour le nom de l’objet et l’empreinte de l’émetteur des certificats distants auxquels le proxy inversé peut faire confiance. |
|ServiceCertificateThumbprints|chaîne, valeur par défaut : L""|Dynamique| |
|CrlCheckingFlag|uint, valeur par défaut : 0x40000000 |Dynamique| Indicateurs pour la validation de la chaîne de certificats de l’application/service, par exemple CRL checking 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Si définie sur 0, cette valeur désactive la vérification CRL La liste complète des valeurs prises en charge est documentée par dwFlags de CertGetCertificateChain : http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|IgnoreCrlOfflineError|Valeur booléenne, valeur par défaut : TRUE|Dynamique|Indique s’il faut ou non ignorer l’erreur en mode hors connexion de la liste CRL pour la vérification du certificat de l’application/service. |
|SecureOnlyMode|valeur booléenne, valeur par défaut : FALSE|Dynamique| SecureOnlyMode : true : le proxy inversé transfère uniquement les demandes vers les services qui publient des points de terminaison sécurisés. False : le proxy inversé peut transférer les demandes vers des points de terminaison sécurisés/non sécurisés.  |
|ForwardClientCertificate|valeur booléenne, valeur par défaut : FALSE|Dynamique| |

### <a name="section-name-applicationgatewayhttpservicecommonnameandissuer"></a>Nom de la section : ApplicationGateway/Http/ServiceCommonNameAndIssuer
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valeur par défaut : None|Dynamique|  |

### <a name="section-name-management"></a>Nom de la section : Management
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| ImageStoreConnectionString |SecureString |statique|Chaîne de connexion à la racine d’ImageStore. |
| ImageStoreMinimumTransferBPS | Entier (valeur par défaut : 1024) |Dynamique|Débit de transfert minimum entre le cluster et ImageStore. Cette valeur permet de déterminer le délai d’expiration lors de l’accès au magasin ImageStore externe. Ne modifiez cette valeur que si la latence entre le cluster et ImageStore est élevée afin de laisser davantage de temps au cluster pour effectuer des téléchargements à partir du magasin ImageStore externe. |
|AzureStorageMaxWorkerThreads | Entier (valeur par défaut : 25) |Dynamique|Nombre maximum de threads de travail en parallèle. |
|AzureStorageMaxConnections | Entier (valeur par défaut : 5000) |Dynamique|Nombre maximum de connexions simultanées au stockage Azure. |
|AzureStorageOperationTimeout | Durée en secondes (valeur par défaut : 6000) |Dynamique|Spécifiez la durée en secondes. Délai permettant de finaliser l’opération xstore. |
|ImageCachingEnabled | Valeur booléenne (valeur par défaut : true) |statique|Cette configuration nous permet d’activer ou de désactiver la mise en cache. |
|DisableChecksumValidation | Valeur booléenne (valeur par défaut : false) |statique| Cette configuration nous permet d’activer ou de désactiver la validation de la somme de contrôle pendant l’approvisionnement de l’application. |
|DisableServerSideCopy | Valeur booléenne (valeur par défaut : false) |statique|Cette configuration active ou désactive la copie côté serveur du package d’application sur le magasin ImageStore pendant l’approvisionnement de l’application. |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>Nom de la section : HealthManager/ClusterHealthPolicy
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| ConsiderWarningAsError |Valeur booléenne (valeur par défaut : false) |statique|Stratégie d’évaluation d’intégrité de cluster : avertissements traités comme des erreurs. |
| MaxPercentUnhealthyNodes | Entier (valeur par défaut : 0) |statique|Stratégie d’évaluation d’intégrité de cluster : pourcentage maximum de nœuds défaillants autorisés pour que le cluster soit fonctionnel. |
| MaxPercentUnhealthyApplications | Entier (valeur par défaut : 0) |statique|Stratégie d’évaluation d’intégrité de cluster : pourcentage maximum d’applications défaillantes autorisées pour que le cluster soit fonctionnel. |

### <a name="section-name-healthmanagerclusterupgradehealthpolicy"></a>Nom de la section : HealthManager/ClusterUpgradeHealthPolicy
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|entier, valeur par défaut : 10|statique|Stratégie d’évaluation d’intégrité de mise à niveau de cluster : pourcentage maximum du delta de nœuds défaillants autorisés pour que le cluster soit fonctionnel |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|entier, valeur par défaut : 15|statique|Stratégie d’évaluation d’intégrité de mise à niveau de cluster : pourcentage maximum de delta de nœuds défaillants autorisés pour que le cluster soit fonctionnel |

### <a name="section-name-faultanalysisservice"></a>Nom de la section : FaultAnalysisService
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Entier (valeur par défaut : 0) |statique|NOT_PLATFORM_UNIX_START Paramètre TargetReplicaSetSize pour FaultAnalysisService. |
| MinReplicaSetSize |Entier (valeur par défaut : 0) |statique|Paramètre MinReplicaSetSize pour FaultAnalysisService. |
| ReplicaRestartWaitDuration |Durée en secondes (valeur par défaut : 60 minutes)|statique|Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour FaultAnalysisService. |
| QuorumLossWaitDuration | Durée en secondes (valeur par défaut : MaxValue) |statique|Spécifiez la durée en secondes. Paramètre QuorumLossWaitDuration pour FaultAnalysisService. |
| StandByReplicaKeepDuration| Durée en secondes (valeur par défaut : 60*24*7 minutes) |statique|Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour FaultAnalysisService. |
| PlacementConstraints | Chaîne (valeur par défaut : "")|statique| Paramètre PlacementConstraints pour FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Entier (valeur par défaut : 3600) |statique|Fréquence de nettoyage du magasin.  Seules les actions dans un état terminal et celles qui exécutées il y a au moins CompletedActionKeepDurationInSeconds secondes seront supprimées. |
| CompletedActionKeepDurationInSeconds | Entier (valeur par défaut : 604800) |statique| Durée approximative de conservation des actions dans un état terminal.  Cela dépend également de StoredActionCleanupIntervalInSeconds, car le travail de nettoyage ne s’effectue que pendant cette durée. 604800 correspond à 7 jours. |
| StoredChaosEventCleanupIntervalInSeconds | Entier (valeur par défaut : 3600) |statique|Fréquence à laquelle le système évalue la nécessité du nettoyage. Si le nombre d’événements est supérieur à 30000, le nettoyage se déclenche. |
|DataLossCheckWaitDurationInSeconds|entier, valeur par défaut : 25|statique|Le délai total, en secondes, que le système attendra avant qu’une perte de données se produise.  Cette valeur est utilisée en interne lors de l’appel de l’api StartPartitionDataLossAsync(). |
|DataLossCheckPollIntervalInSeconds|entier, valeur par défaut : 5|statique|Il s’agit du délai que le système attendra entre les vérifications avant qu’une perte de données se produise.  Le nombre de fois où le nombre de pertes de données sera vérifié par itération interne est DataLossCheckWaitDurationInSeconds/this. |
|ReplicaDropWaitDurationInSeconds|entier, valeur par défaut : 600|statique|Ce paramètre est utilisé lorsque l’api de perte de données est appelée.  Il contrôle le délai que le système attend avant qu’un réplica soit abandonné après la suppression d’un réplica appelé en interne. |

### <a name="section-name-filestoreservice"></a>Nom de la section : FileStoreService
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| NamingOperationTimeout |Durée en secondes, la valeur par défaut est 60 |Dynamique|Spécifiez la durée en secondes. Délai pour effectuer l’opération d’attribution de noms. |
| QueryOperationTimeout | Durée en secondes, la valeur par défaut est 60 |Dynamique|Spécifiez la durée en secondes. Délai pour effectuer l’opération de requête. |
| MaxCopyOperationThreads | Valeur Uint (valeur par défaut : 0) |Dynamique| Nombre maximum de fichiers parallèles que le réplica secondaire peut copier à partir du réplica principal. 0 = nombre de cœurs. |
| MaxFileOperationThreads | Valeur Uint (valeur par défaut : 100) |statique| Nombre maximum de threads parallèles autorisés à effectuer des opérations sur les fichiers (copie/déplacement) dans le réplica principal. 0 == nombre de cœurs. |
| MaxStoreOperations | Valeur Uint (valeur par défaut : 4096) |statique|Nombre maximum d’opérations parallèles de transaction de magasin autorisées sur le réplica principal. 0 = nombre de cœurs. |
| MaxRequestProcessingThreads | Valeur Uint (valeur par défaut : 200) |statique|Nombre maximum de threads parallèles autorisés pour traiter les requêtes dans le réplica principal. 0 = nombre de cœurs. |
| MaxSecondaryFileCopyFailureThreshold | Valeur Uint (valeur par défaut : 25)|Dynamique|Nombre maximum de nouvelles tentatives de copie de fichier sur le réplica secondaire avant d’abandonner. |
| AnonymousAccessEnabled | Valeur booléenne (valeur par défaut : true) |statique|Activez ou désactivez l’accès anonyme aux partages FileStoreService. |
| PrimaryAccountType | Chaîne (valeur par défaut : "") |statique|Type de compte principal du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| PrimaryAccountUserName | Chaîne (valeur par défaut : "") |statique|Nom d’utilisateur du compte principal du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| PrimaryAccountUserPassword | Valeur SecureString (valeur par défaut : vide) |statique|Mot de passe du compte principal du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| PrimaryAccountNTLMPasswordSecret | Valeur SecureString (valeur par défaut : vide) |statique| Secret de mot de passe utilisé comme valeur initiale pour générer le même mot de passe en cas d’authentification NTLM. |
| PrimaryAccountNTLMX509StoreLocation | Chaîne (valeur par défaut : "LocalMachine")|statique| Emplacement du certificat X509 utilisé pour générer le code HMAC sur le PrimaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| PrimaryAccountNTLMX509StoreName | Chaîne (valeur par défaut : "MY")|statique| Nom du certificat X509 utilisé pour générer le code HMAC sur le PrimaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| PrimaryAccountNTLMX509Thumbprint | Chaîne (valeur par défaut : "")|statique|Empreinte du certificat X509 utilisé pour générer le code HMAC sur le PrimaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| SecondaryAccountType | Chaîne (valeur par défaut : "")|statique| Type de compte secondaire du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| SecondaryAccountUserName | Chaîne (valeur par défaut : "")| statique|Nom d’utilisateur du compte secondaire du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| SecondaryAccountUserPassword | Valeur SecureString (valeur par défaut : vide) |statique|Mot de passe du compte secondaire du principal utilisé pour contrôler l’accès aux partages FileStoreService.  |
| SecondaryAccountNTLMPasswordSecret | Valeur SecureString (valeur par défaut : vide) |statique| Secret de mot de passe utilisé comme valeur initiale pour générer le même mot de passe en cas d’authentification NTLM. |
| SecondaryAccountNTLMX509StoreLocation | Chaîne (valeur par défaut : "LocalMachine") |statique|Emplacement du certificat X509 utilisé pour générer le code HMAC sur le SecondaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| SecondaryAccountNTLMX509StoreName | Chaîne (valeur par défaut : "MY") |statique|Nom du certificat X509 utilisé pour générer le code HMAC sur le SecondaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| SecondaryAccountNTLMX509Thumbprint | Chaîne (valeur par défaut : "")| statique|Empreinte du certificat X509 utilisé pour générer le code HMAC sur le SecondaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
|CommonNameNtlmPasswordSecret|SecureString, valeur par défaut : Common::SecureString(L"")| statique|Secret de mot de passe utilisé comme valeur initiale pour générer le même mot de passe en cas d’authentification NTLM |
|CommonName1Ntlmx509StoreLocation|chaîne, valeur par défaut : L"LocalMachine"|statique|Emplacement du magasin du certificat X509 utilisé pour générer le code HMAC sur le CommonName1NtlmPasswordSecret en cas d’authentification NTLM |
|CommonName1Ntlmx509StoreName|chaîne, valeur par défaut : L"MY"| statique|Nom du certificat X509 utilisé pour générer le code HMAC sur le CommonName1NtlmPasswordSecret en cas d’authentification NTLM |
|CommonName1Ntlmx509CommonName|chaîne, valeur par défaut : L""|statique| Nom commun du certificat X509 utilisé pour générer le code HMAC sur le CommonName1NtlmPasswordSecret en cas d’authentification NTLM |
|CommonName2Ntlmx509StoreLocation|chaîne, valeur par défaut : L"LocalMachine"| statique|Emplacement du magasin du certificat X509 utilisé pour générer le code HMAC sur le CommonName2NtlmPasswordSecret en cas d’authentification NTLM |
|CommonName2Ntlmx509StoreName|chaîne, valeur par défaut : L"MY"|statique| Nom du certificat X509 utilisé pour générer le code HMAC sur le CommonName2NtlmPasswordSecret en cas d’authentification NTLM |
|CommonName2Ntlmx509CommonName|chaîne, valeur par défaut : L""|statique|Nom commun du certificat X509 utilisé pour générer le code HMAC sur le CommonName2NtlmPasswordSecret en cas d’authentification NTLM |

### <a name="section-name-imagestoreservice"></a>Nom de la section : ImageStoreService
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| activé |Valeur booléenne (valeur par défaut : false) |statique|Indicateur d’activation d’ImageStoreService. Par défaut : false |
| TargetReplicaSetSize | Entier (valeur par défaut : 7) |statique|Paramètre TargetReplicaSetSize pour ImageStoreService. |
| MinReplicaSetSize | Entier (valeur par défaut : 3) |statique|Paramètre MinReplicaSetSize pour ImageStoreService. |
| ReplicaRestartWaitDuration | Durée en secondes (valeur par défaut : 60.0 * 30) |statique|Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour ImageStoreService. |
| QuorumLossWaitDuration | Durée en secondes (valeur par défaut : MaxValue) |statique| Spécifiez la durée en secondes. Paramètre QuorumLossWaitDuration pour ImageStoreService. |
| StandByReplicaKeepDuration | Durée en secondes (valeur par défaut : 3600.0*2) |statique| Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour ImageStoreService. |
| PlacementConstraints | Chaîne (valeur par défaut : "") |statique| Paramètre PlacementConstraints pour ImageStoreService. |

### <a name="section-name-imagestoreclient"></a>Nom de la section : ImageStoreClient
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| ClientUploadTimeout |Durée en secondes (valeur par défaut : 1800) |Dynamique|Spécifiez la durée en secondes. Délai d’expiration de la requête de chargement de niveau supérieur dans ImageStoreService. |
| ClientCopyTimeout | Durée en secondes (valeur par défaut : 1800) |Dynamique| Spécifiez la durée en secondes. Délai d’expiration de la requête de copie de niveau supérieur dans ImageStoreService. |
|ClientDownloadTimeout | Durée en secondes (valeur par défaut : 1800) |Dynamique| Spécifiez la durée en secondes. Délai d’expiration de la requête de téléchargement de niveau supérieur dans ImageStoreService. |
|ClientListTimeout | Durée en secondes (valeur par défaut : 600) |Dynamique|Spécifiez la durée en secondes. Délai d’expiration de la requête de liste de niveau supérieur dans ImageStoreService. |
|ClientDefaultTimeout | Durée en secondes (valeur par défaut : 180) |Dynamique| Spécifiez la durée en secondes. Délai d’expiration de toutes les requêtes autres que de chargement/déchargement (existence ou suppression) dans ImageStoreService. |

### <a name="section-name-tokenvalidationservice"></a>Nom de la section : TokenValidationService
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| Fournisseurs |Chaîne (valeur par défault : "DSTS") |statique|Liste séparée par des virgules, des fournisseurs de validation de jeton à activer (fournisseurs valides : DSTS, AAD). Pour l’instant, un seul fournisseur peut être activé à la fois. |

### <a name="section-name-upgradeorchestrationservice"></a>Nom de la section : UpgradeOrchestrationService
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| TargetReplicaSetSize |Entier (valeur par défaut : 0) |statique |Paramètre TargetReplicaSetSize pour UpgradeOrchestrationService. |
| MinReplicaSetSize |Entier (valeur par défaut : 0) |statique |Paramètre MinReplicaSetSize pour UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Durée en secondes (valeur par défaut : 60 minutes)|statique| Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Durée en secondes (valeur par défaut : MaxValue) |statique| Spécifiez la durée en secondes. Paramètre QuorumLossWaitDuration pour UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Durée en secondes (valeur par défaut : 60*24*7 minutes) |statique| Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour UpgradeOrchestrationService. |
| PlacementConstraints | Chaîne (valeur par défaut : "") |statique| Paramètre PlacementConstraints pour UpgradeOrchestrationService. |
| AutoupgradeEnabled | Valeur booléenne (valeur par défaut : true) |statique| Interrogation automatique et action de mise à niveau basée sur un fichier d’état d’objectif. |
| UpgradeApprovalRequired | Valeur booléenne (valeur par défaut : false) | statique|La configuration de la mise à niveau du code requiert une autorisation de l’administrateur. |

### <a name="section-name-upgradeservice"></a>Nom de la section : UpgradeService
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| PlacementConstraints |Chaîne (valeur par défaut : "") |Non autorisée|Paramètre PlacementConstraints pour le service de mise à niveau. |
| TargetReplicaSetSize | Entier (valeur par défaut : 3) |Non autorisée| Paramètre TargetReplicaSetSize pour UpgradeService. |
| MinReplicaSetSize | Entier (valeur par défaut : 2) |Non autorisée| Paramètre MinReplicaSetSize pour UpgradeService. |
| CoordinatorType | Chaîne (valeur par défault : "WUTest")|Non autorisée|Paramètre CoordinatorType pour UpgradeService. |
| BaseUrl | Chaîne (valeur par défaut : "") |statique|Paramètre BaseUrl pour UpgradeService. |
| ClusterId | Chaîne (valeur par défaut : "") |statique|Paramètre ClusterId pour UpgradeService. |
| X509StoreName | Chaîne (valeur par défaut : "My")|Dynamique|Paramètre X509StoreName pour UpgradeService. |
| X509StoreLocation | Chaîne (valeur par défaut : "") |Dynamique| Paramètre X509StoreLocation pour UpgradeService. |
| X509FindType | Chaîne (valeur par défaut : "")|Dynamique| Paramètre X509FindType pour UpgradeService. |
| X509FindValue | Chaîne (valeur par défaut : "") |Dynamique| Paramètre X509FindValue pour UpgradeService. |
| X509SecondaryFindValue | Chaîne (valeur par défaut : "") |Dynamique| Paramètre X509SecondaryFindValue pour UpgradeService. |
| OnlyBaseUpgrade | Valeur booléenne (valeur par défaut : false) |Dynamique|Paramètre OnlyBaseUpgrade pour UpgradeService. |
| TestCabFolder | Chaîne (valeur par défaut : "") |statique| Paramètre TestCabFolder pour UpgradeService. |

### <a name="section-name-security"></a>Nom de la section : Sécurité
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|ClusterCredentialType|chaîne, valeur par défaut : L"None"|Non autorisée|Indique le type d’informations d’identification de sécurité à utiliser pour sécuriser le cluster. Les valeurs valides sont "None/X509/Windows" |
|ServerAuthCredentialType|chaîne, valeur par défaut : L"None"|statique|Indique le type d’informations d’identification de sécurité à utiliser pour sécuriser la communication entre FabricClient et le cluster. Les valeurs valides sont "None/X509/Windows" |
|ClientRoleEnabled|valeur booléenne, valeur par défaut : FALSE|statique|Indique si le rôle de client est activé ; si cette valeur est définie True ; les clients reçoivent des rôles selon leur identité. Pour V2 ; activer cette option signifie que le client qui ne figure pas dans AdminClientCommonNames/AdminClientIdentities peut uniquement exécuter des opérations en lecture seule. |
|ClusterCertThumbprints|chaîne, valeur par défaut : L""|Dynamique|Empreintes de certificats autorisées à rejoindre le cluster ; une liste de noms séparés par des virgules. |
|ServerCertThumbprints|chaîne, valeur par défaut : L""|Dynamique|Empreintes de certificats de serveur utilisés par le cluster pour communiquer avec les clients ; les clients l’utilisent pour authentifier le cluster. Il s’agit d’une liste de noms séparés par des virgules. |
|ClientCertThumbprints|chaîne, valeur par défaut : L""|Dynamique|Empreintes de certificats utilisés par les clients pour communiquer avec le cluster ; le cluster utilise ce paramètre pour autoriser la connexion entrante. Il s’agit d’une liste de noms séparés par des virgules. |
|AdminClientCertThumbprints|chaîne, valeur par défaut : L""|Dynamique|Empreintes de certificats utilisées par les clients dans le rôle d’administrateur. Il s’agit d’une liste de noms séparés par des virgules. |
|CrlCheckingFlag|uint, valeur par défaut : 0x40000000|Dynamique|Indicateur de validation de chaîne de certificats par défaut ; peut être remplacé par un indicateur spécifique au composant ; par exemple Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY Si définie sur 0, cette valeur désactive la vérification CRL La liste complète des valeurs prises en charge est documentée par dwFlags de CertGetCertificateChain : http://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|IgnoreCrlOfflineError|valeur booléenne, valeur par défaut : FALSE|Dynamique|Indique s’il faut ou non ignorer l’erreur en mode hors connexion de la liste CRL lorsque le côté serveur vérifie des certificats de client entrants |
|IgnoreSvrCrlOfflineError|Valeur booléenne, valeur par défaut : TRUE|Dynamique|Indique s’il faut ou non ignorer l’erreur en mode hors connexion de la liste CRL lorsque le côté client vérifie des certificats clients entrants ; valeur par défaut : true. Les attaques avec des certificats serveurs révoqués exigent de compromettre le DNS ; plus difficile qu’avec des certificats clients révoqués. |
|CrlDisablePeriod|TimeSpan, la valeur par défaut est Common::TimeSpan::FromMinutes(15)|Dynamique|Spécifiez la durée en secondes. Durée de la désactivation de la vérification de la liste CRL pour un certificat donné après une erreur en mode hors connexion ; l’erreur en mode hors connexion de la liste CRL peut être ignorée. |
|CrlOfflineHealthReportTtl|TimeSpan, la valeur par défaut est Common::TimeSpan::FromMinutes(1440)|Dynamique|Spécifiez la durée en secondes. |
|CertificateHealthReportingInterval|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(3600 * 8)|statique|Spécifiez la durée en secondes. Spécifiez l’intervalle de génération des rapports d’intégrité de certificats ; la valeur par défaut est 8 heures ; Si défini sur 0, désactive le rapport de contrôle d’intégrité de certificat |
|CertificateExpirySafetyMargin|TimeSpan, la valeur par défaut est Common::TimeSpan::FromMinutes(43200)|statique|Spécifiez la durée en secondes. Marge de sécurité pour l’expiration du certificat ; l’état d’intégrité du certificat passe de OK à Warning lorsque le délai d’expiration est plus proche que cette valeur. La valeur par défaut est 30 jours. |
|ClientClaimAuthEnabled|valeur booléenne, valeur par défaut : FALSE|statique|Indique si l’authentification basée sur les revendications est activée sur les clients ; si définie sur true, définit implicitement ClientRoleEnabled. |
|ClientClaims|chaîne, valeur par défaut : L""|Dynamique|Toutes les revendications possibles attendues des clients pour se connecter à la passerelle. Il s’agit d’une liste 'OR' : ClaimsEntry || ClaimsEntry || ClaimsEntry ... chaque ClaimsEntry est une liste "AND" : ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|AdminClientClaims|chaîne, valeur par défaut : L""|Dynamique|Toutes les revendications possibles attendues des clients d’administration ; même format que ClientClaims ; cette liste est ajoutée en interne à ClientClaims ; par conséquent, pas besoin d’ajouter également les mêmes entrées à ClientClaims. |
|ClusterSpn|chaîne, valeur par défaut : L""|Non autorisée|Nom de principal du service du cluster ; lorsque Fabric s’exécute en tant qu’utilisateur de domaine unique (gMSA/compte d’utilisateur de domaine). Il s’agit du nom de principal de service (SPN) des écouteurs de bail et écouteurs dans fabric.exe : écouteurs de fédération ; écouteurs de réplication internes ; écouteur de service runtime et écouteur de la passerelle d’affectation de noms. Doit être vide lorsque la structure s’exécute en tant que comptes d’ordinateur ; dans ce cas, connecter le SPN de l’écouteur côté ordinateur à partir de l’adresse de transport de l’écouteur. |
|ClusterIdentities|chaîne, valeur par défaut : L""|Dynamique|Identités Windows des nœuds de cluster ; utilisé pour l’autorisation de l’appartenance au cluster. Il s’agit d’une liste séparée par des virgules ; chaque entrée est un nom de compte de domaine ou nom de groupe |
|ClientIdentities|chaîne, valeur par défaut : L""|Dynamique|Identités Windows de FabricClient ; la passerelle d’affectation de noms utilise ce paramètre pour autoriser les connexions entrantes. Il s’agit d’une liste séparée par des virgules ; chaque entrée est un nom de compte de domaine ou nom de groupe. Pour des raisons pratiques ; le compte qui exécute fabric.exe est autorisé automatiquement ; par conséquent, les groupes ServiceFabricAllowedUsers et ServiceFabricAdministrators le sont aussi. |
|AdminClientIdentities|chaîne, valeur par défaut : L""|Dynamique|Identités Windows des clients de la structure dans le rôle d’administrateur ; utilisé pour autoriser des opérations de structure privilégiées. Il s’agit d’une liste séparée par des virgules ; chaque entrée est un nom de compte de domaine ou nom de groupe. Pour des raisons pratiques ; le compte qui exécute fabric.exe reçoit automatiquement un rôle d’administrateur ; par conséquent, le groupe ServiceFabricAdministrators l’est aussi. |
|AADTenantId|chaîne, valeur par défaut : L""|statique|ID de client (GUID) |
|AADClusterApplication|chaîne, valeur par défaut : L""|statique|Nom de l’application API Web ou ID représentant le cluster |
|AADClientApplication|chaîne, valeur par défaut : L""|statique|Nom de l’application cliente native ou ID représentant les clients Fabric |
|X509Folder|chaîne, valeur par défaut : /var/lib/waagent|statique|Dossier dans lequel se trouvent les certificats X509 et les clés privées |
|FabricHostSpn| chaîne, valeur par défaut : L"" |statique| Nom de principal du service de FabricHost ; lorsque Fabric s’exécute en tant qu’utilisateur de domaine unique (gMSA/compte d’utilisateur de domaine) et que FabricHost s’exécute sous le compte d’ordinateur. Il s’agit du SPN de l’écouteur IPC pour FabricHost ; qui par défaut doit être vide car FabricHost s’exécute sous le compte d’ordinateur |
|DisableFirewallRuleForPublicProfile| Valeur booléenne, valeur par défaut : TRUE | statique|Indique si la règle de pare-feu ne doit pas être activée pour le profil public |
|DisableFirewallRuleForPrivateProfile| Valeur booléenne, valeur par défaut : TRUE |statique| Indique si la règle de pare-feu ne doit pas être activée pour le profil privé | 
|DisableFirewallRuleForDomainProfile| Valeur booléenne, valeur par défaut : TRUE |statique| Indique si la règle de pare-feu ne doit pas être activée pour le profil de domaine |
|SettingsX509StoreName| chaîne, valeur par défaut : L"MY"| Dynamique|Magasin de certificats X509 utilisé par l’infrastructure pour protéger la configuration |

### <a name="section-name-securityadminclientx509names"></a>Nom de la section : Security/AdminClientX509Names
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valeur par défaut : None|Dynamique| |

### <a name="section-name-securityclientx509names"></a>Nom de la section : Security/ClientX509Names
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valeur par défaut : None|Dynamique| |

### <a name="section-name-securityclusterx509names"></a>Nom de la section : Security/ClusterX509Names
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valeur par défaut : None|Dynamique| |

### <a name="section-name-securityserverx509names"></a>Nom de la section : Security/ServerX509Names
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valeur par défaut : None|Dynamique| |

### <a name="section-name-securityclientcertificateissuerstores"></a>Nom de la section : Sécurité/ClientCertificateIssuerStores
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, la valeur par défaut est None |Dynamique|Magasins de certificats de l’émetteur X509 pour les certificats de clients ; Nom = clientIssuerCN ; Valeur = liste des magasins séparée par des virgules |

### <a name="section-name-securityclustercertificateissuerstores"></a>Nom de la section : Sécurité/ClusterCertificateIssuerStores
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, la valeur par défaut est None |Dynamique|Magasins de certificats de l’émetteur X509 pour les certificats de cluster ; Nom = clusterIssuerCN ; Valeur = liste des magasins séparée par des virgules |

### <a name="section-name-securityservercertificateissuerstores"></a>Nom de la section : Sécurité/ServerCertificateIssuerStores
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, la valeur par défaut est None |Dynamique|Magasins de certificats de l’émetteur X509 pour les certificats de serveurs ; Nom = serverIssuerCN ; Valeur = liste des magasins séparée par des virgules |

### <a name="section-name-securityclientaccess"></a>Nom de la section : Security/ClientAccess
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| CreateName |Chaîne (valeur par défault : "Admin") |Dynamique|Configuration de la sécurité pour la création d’URI d’attribution de noms. |
| DeleteName |Chaîne (valeur par défault : "Admin") |Dynamique|Configuration de la sécurité pour la suppression d’URI d’attribution de noms. |
| PropertyWriteBatch |Chaîne (valeur par défault : "Admin") |Dynamique|Configurations de la sécurité pour les opérations d’écriture de la propriété d’attribution de noms. |
| CreateService |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour la création de services. |
| CreateServiceFromTemplate |Chaîne (valeur par défault : "Admin") |Dynamique|Configuration de la sécurité pour la création de services à partir d’un modèle. |
| UpdateService |Chaîne (valeur par défault : "Admin") |Dynamique|Configuration de la sécurité pour les mises à niveau de services. |
| DeleteService  |Chaîne (valeur par défault : "Admin") |Dynamique|Configuration de la sécurité pour la suppression de services. |
| ProvisionApplicationType |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour l’approvisionnement du type d’application. |
| CreateApplication |Chaîne (valeur par défault : "Admin") | Dynamique|Configuration de la sécurité pour la création d’applications. |
| DeleteApplication |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour la suppression d’applications. |
| UpgradeApplication |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour démarrer ou arrêter des mises à niveau d’application. |
| RollbackApplicationUpgrade |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour annuler des mises à niveau d’application. |
| UnprovisionApplicationType |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour annuler l’approvisionnement du type d’application. |
| MoveNextUpgradeDomain |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour reprendre des mises à niveau d’application avec un domaine de mise à niveau explicite. |
| ReportUpgradeHealth |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour reprendre des mises à niveau d’application à leur état d’avancement actuel. |
| ReportHealth |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour créer un rapport d’intégrité. |
| ProvisionFabric |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour l’approvisionnement de MSI et/ou du manifeste de cluster. |
| UpgradeFabric |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour démarrer des mises à niveau de cluster. |
| RollbackFabricUpgrade |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour annuler des mises à niveau de cluster. |
| UnprovisionFabric |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour annuler l’approvisionnement de MSI et/ou du manifeste de cluster. |
| MoveNextFabricUpgradeDomain |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour reprendre des mises à niveau de cluster avec un domaine de mise à niveau explicite. |
| ReportFabricUpgradeHealth |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour reprendre des mises à niveau de cluster à leur état d’avancement actuel. |
| StartInfrastructureTask |Chaîne (valeur par défault : "Admin") | Dynamique|Configuration de la sécurité pour démarrer des tâches d’infrastructure. |
| FinishInfrastructureTask |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour terminer des tâches d’infrastructure. |
| ActivateNode |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour activer un nœud. |
| DeactivateNode |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour désactiver un nœud. |
| DeactivateNodesBatch |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour désactiver plusieurs nœuds. |
| RemoveNodeDeactivations |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour annuler la désactivation sur plusieurs nœuds. |
| GetNodeDeactivationStatus |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour vérifier l’état de désactivation. |
| NodeStateRemoved |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour signaler l’état de nœud supprimé. |
| RecoverPartition |Chaîne (valeur par défault : "Admin") | Dynamique|Configuration de la sécurité pour restaurer une partition. |
| RecoverPartitions |Chaîne (valeur par défault : "Admin") | Dynamique|Configuration de la sécurité pour restaurer des partitions. |
| RecoverServicePartitions |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour restaurer des partitions de service. |
| RecoverSystemPartitions |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour restaurer des partitions du service système. |
| ReportFault |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour signaler une défaillance. |
| InvokeInfrastructureCommand |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour les commandes de gestion des tâches d’infrastructure. |
| FileContent |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour le transfert de fichiers clients du magasin d’images (externe vers le cluster). |
| FileDownload |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour le lancement du téléchargement de fichiers clients du magasin d’images (externe vers le cluster). |
| InternalList |Chaîne (valeur par défault : "Admin") | Dynamique|Configuration de la sécurité pour l’opération de liste de fichiers clients deumagasin d’images (interne). |
| Supprimer |Chaîne (valeur par défault : "Admin") |Dynamique| Configurations de la sécurité pour l’opération de suppression de clients du magasin d’images (interne). |
| Télécharger |Chaîne (valeur par défault : "Admin") | Dynamique|Configuration de la sécurité pour l’opération de chargement de clients du magasin d’images. |
| GetStagingLocation |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour récupérer l’emplacement intermédiaire du client du magasin d’images. |
| GetStoreLocation |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour récupérer l’emplacement du client du magasin d’images. |
| NodeControl |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour démarrer, arrêter et redémarrer des nœuds. |
| CodePackageControl |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour redémarrer des packages de code. |
| UnreliableTransportControl |Chaîne (valeur par défault : "Admin") |Dynamique| Transport non fiable pour ajouter et supprimer des comportements. |
| MoveReplicaControl |Chaîne (valeur par défault : "Admin") | Dynamique|Déplacez un réplica. |
| PredeployPackageToNode |Chaîne (valeur par défault : "Admin") |Dynamique| API de pré-déploiement. |
| StartPartitionDataLoss |Chaîne (valeur par défault : "Admin") |Dynamique| Provoque la perte de données sur une partition. |
| StartPartitionQuorumLoss |Chaîne (valeur par défault : "Admin") |Dynamique| Provoque la perte de quorum sur une partition. |
| StartPartitionRestart |Chaîne (valeur par défault : "Admin") |Dynamique| Redémarre simultanément certains ou l’ensemble des réplicas d’une partition. |
| CancelTestCommand |Chaîne (valeur par défault : "Admin") |Dynamique| Annule une commande de test si elle est en cours. |
| StartChaos |Chaîne (valeur par défault : "Admin") |Dynamique| Démarre Chaos si ce n’est déjà fait. |
| StopChaos |Chaîne (valeur par défault : "Admin") |Dynamique| Arrête Chaos s’il a été démarré. |
| StartNodeTransition |Chaîne (valeur par défault : "Admin") |Dynamique| Configuration de la sécurité pour démarrer une transition de nœud. |
| StartClusterConfigurationUpgrade |Chaîne (valeur par défault : "Admin") |Dynamique| Déclenche StartClusterConfigurationUpgrade sur une partition. |
| GetUpgradesPendingApproval |Chaîne (valeur par défault : "Admin") |Dynamique| Déclenche GetUpgradesPendingApproval sur une partition. |
| StartApprovedUpgrades |Chaîne (valeur par défault : "Admin") |Dynamique| Déclenche StartApprovedUpgrades sur une partition. |
| Ping |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour les tests ping de clients |
| Requête |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour les requêtes. |
| NameExists |Chaîne, la valeur par défaut est « Admin\|\|User » | Dynamique|Configuration de la sécurité pour la vérification de l’existence des URI d’attribution de noms. |
| EnumerateSubnames |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour l’énumération d’URI d’attribution de noms. |
| EnumerateProperties |Chaîne, la valeur par défaut est « Admin\|\|User » | Dynamique|Configuration de la sécurité pour l’énumération de propriétés d’attribution de noms. |
| PropertyReadBatch |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour les opérations de lecture de propriétés d’attribution de noms. |
| GetServiceDescription |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour la lecture des descriptions de service et les notifications de sondage de longue durée. |
| ResolveService |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour la résolution de services en fonction de la réclamation. |
| ResolveNameOwner |Chaîne, la valeur par défaut est « Admin\|\|User » | Dynamique|Configuration de la sécurité pour résoudre le propriétaire de l’URI d’attribution de noms. |
| ResolvePartition |Chaîne, la valeur par défaut est « Admin\|\|User » | Dynamique|Configuration de la sécurité pour résoudre les services du système. |
| ServiceNotifications |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour les notifications de service basées sur l’événement. |
| PrefixResolveService |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour la résolution de préfixe de service en fonction de la réclamation. |
| GetUpgradeStatus |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour interroger l’état de mise à niveau de l’application. |
| GetFabricUpgradeStatus |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour interroger l’état de mise à niveau du cluster. |
| InvokeInfrastructureQuery |Chaîne, la valeur par défaut est « Admin\|\|User » | Dynamique|Configuration de la sécurité pour interroger les tâches d’infrastructure. |
| Liste |Chaîne, la valeur par défaut est « Admin\|\|User » | Dynamique|Configuration de la sécurité pour l’opération de liste de fichiers clients du magasin d’images. |
| ResetPartitionLoad |Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour la charge de réinitialisation d’une unité failoverUnit. |
| ToggleVerboseServicePlacementHealthReporting | Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour l’activation ou la désactivation du rapport d’intégrité détaillé sur le placement du service. |
| GetPartitionDataLossProgress | Chaîne, la valeur par défaut est « Admin\|\|User » | Dynamique|Récupère l’état d’avancement de l’invocation d’un appel d’API de perte de données. |
| GetPartitionQuorumLossProgress | Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Récupère l’état d’avancement de l’invocation d’un appel d’API de perte de quorum. |
| GetPartitionRestartProgress | Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Récupère l’état d’avancement de l’invocation d’un appel d’API de redémarrage de partition. |
| GetChaosReport | Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Récupère l’état de Chaos pendant une période donnée. |
| GetNodeTransitionProgress | Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Configuration de la sécurité pour obtenir l’état d’avancement d’une commande de transition de nœud. |
| GetClusterConfigurationUpgradeStatus | Chaîne, la valeur par défaut est « Admin\|\|User » |Dynamique| Déclenche GetClusterConfigurationUpgradeStatus sur une partition. |
| GetClusterConfiguration | Chaîne, la valeur par défaut est « Admin\|\|User » | Dynamique|Déclenche GetClusterConfiguration sur une partition. |
|CreateComposeDeployment|chaîne, valeur par défaut : L"Admin"| Dynamique|Crée un déploiement compose décrit par les fichiers compose |
|DeleteComposeDeployment|chaîne, valeur par défaut : L"Admin"| Dynamique|Supprime le déploiement compose |
|UpgradeComposeDeployment|chaîne, valeur par défaut : L"Admin"| Dynamique|Met à niveau le déploiement compose |
|ResolveSystemService|Chaîne, la valeur par défaut est L« Admin\|\|User »|Dynamique| Configuration de la sécurité pour résoudre les services du système |
|GetUpgradeOrchestrationServiceState|chaîne, valeur par défaut : L"Admin"| Dynamique|Déclenche GetUpgradeOrchestrationServiceState sur une partition |
|SetUpgradeOrchestrationServiceState|chaîne, valeur par défaut : L"Admin"| Dynamique|Déclenche SetUpgradeOrchestrationServiceState sur une partition |

### <a name="section-name-reconfigurationagent"></a>Nom de la section : ReconfigurationAgent
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Durée en secondes (valeur par défaut : 900) |Dynamique|Spécifiez la durée en secondes. Délai respecté par le système avant de mettre fin à des hôtes de service dont des réplicas sont bloqués en fermeture lors de la mise à niveau d’une application.|
| ServiceApiHealthDuration | Durée en secondes (valeur par défaut : 30 minutes) |Dynamique| Spécifiez la durée en secondes. ServiceApiHealthDuration définit le délai d’attente à respecter pour exécuter une API de service avant qu’elle soit signalée comme défectueuse. |
| ServiceReconfigurationApiHealthDuration | Durée en secondes, la valeur par défaut est 30 |Dynamique| Spécifiez la durée en secondes. ServiceReconfigurationApiHealthDuration définit le délai d’attente à respecter pour exécuter une API de service avant qu’elle soit signalée comme défectueuse. S’applique aux appels d’API qui affectent la disponibilité.|
| PeriodicApiSlowTraceInterval | Durée en secondes (valeur par défaut : 5 minutes) |Dynamique| Spécifiez la durée en secondes. PeriodicApiSlowTraceInterval définit la période pendant laquelle les appels d’API lents sont retracés par le moniteur d’API. |
| NodeDeactivationMaxReplicaCloseDuration | Durée en secondes (valeur par défaut : 900) |Dynamique|Spécifiez la durée en secondes. Délai respecté par le système avant de mettre fin à des hôtes de service dont des réplicas sont bloqués en fermeture lors de la désactivation d’un nœud. |
| FabricUpgradeMaxReplicaCloseDuration | Durée en secondes (valeur par défaut : 900) |Dynamique| Spécifiez la durée en secondes. Délai respecté par le système avant de mettre fin à des hôtes de service dont des réplicas sont bloqués en fermeture lors de la mise à niveau de Fabric. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(120)|Dynamique|Spécifiez la durée en secondes. Délai respecté par le système avant de mettre fin à des hôtes de service dont des réplicas sont bloqués en fermeture. Si cette valeur est définie sur 0, les réplicas ne reçoivent pas d’instruction de fermeture.|
|ReplicaChangeRoleFailureRestartThreshold|entier, valeur par défaut : 10|Dynamique| Entier. Spécifiez le nombre d’échecs d’API pendant la promotion principale après lesquels une action d’atténuation d’automatique (redémarrage du réplica) sera appliquée. |
|ReplicaChangeRoleFailureWarningReportThreshold|entier, valeur par défaut : 2147483647|Dynamique| Entier. Spécifiez le nombre d’échecs d’API pendant la promotion principale après lesquels des rapports d’intégrité Warning seront générés.|

### <a name="section-name-placementandloadbalancing"></a>Nom de la section : PlacementAndLoadBalancing
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| TraceCRMReasons |Valeur booléenne (valeur par défaut : true) |Dynamique|Spécifie si les raisons des mouvements effectués par CRM doivent être envoyés au canal des événements opérationnels. |
| ValidatePlacementConstraint | Valeur booléenne (valeur par défaut : true) |Dynamique| Spécifie si l’expression PlacementConstraint d’un service est validée lors de la mise à jour du paramètre ServiceDescription d’un service. |
| PlacementConstraintValidationCacheSize | Entier (valeur par défaut : 10000) |Dynamique| Limite la taille de la table utilisée pour la validation et la mise en cache rapides des expressions de contrainte de placement. |
|VerboseHealthReportLimit | Entier (valeur par défaut : 20) | Dynamique|Définit le nombre de fois que le placement d’un réplica doit être annulé avant qu’un avertissement d’intégrité ne soit émis (si le rapport d’intégrité détaillé est activé). |
|ConstraintViolationHealthReportLimit | Entier (valeur par défaut : 50) |Dynamique| Définit le nombre de fois qu’un réplica ne respectant pas la contrainte ne doit pas être corrigé de façon permanente avant que des diagnostics soient effectués et que des rapports d’intégrité soient émis. |
|DetailedConstraintViolationHealthReportLimit | Entier (valeur par défaut : 200) |Dynamique| Définit le nombre de fois qu’un réplica ne respectant pas la contrainte ne doit pas être corrigé de façon permanente avant que des diagnostics soient effectués et que des rapports d’intégrité détaillés soient émis. |
|DetailedVerboseHealthReportLimit | Entier (valeur par défaut : 200) | Dynamique|Définit le nombre de fois qu’un réplica non placé doit être non placé de façon permanente avant que des rapports d’intégrité détaillés soient émis. |
|ConsecutiveDroppedMovementsHealthReportLimit | Entier (valeur par défaut : 20) | Dynamique|Définit le nombre de fois consécutives que des mouvements émis par ResourceBalancer sont supprimés avant que des diagnostics soient effectués et que des avertissements d’intégrité soient émis. Negative : aucun avertissement émis sous cette condition. |
|DetailedNodeListLimit | Entier (valeur par défaut : 15) |Dynamique| Définit le nombre de nœuds par contrainte à inclure avant la troncation dans les rapports de réplica non placé. |
|DetailedPartitionListLimit | Entier (valeur par défaut : 15) |Dynamique| Définit le nombre de partitions par entrée de diagnostic pour une contrainte à inclure avant la troncation dans Diagnostics. |
|DetailedDiagnosticsInfoListLimit | Entier (valeur par défaut : 15) |Dynamique| Définit le nombre d’entrées de diagnostic (avec informations détaillées) par contrainte à inclure avant la troncation dans Diagnostics.|
|PLBRefreshGap | Durée en secondes (valeur par défaut : 1) |Dynamique| Spécifiez la durée en secondes. Définit le délai minimum à respecter avant que PLB ne réactualise l’état. |
|MinPlacementInterval | Durée en secondes (valeur par défaut : 1) |Dynamique| Spécifiez la durée en secondes. Définit le délai minimum à respecter avant deux passes de placement consécutives. |
|MinConstraintCheckInterval | Durée en secondes (valeur par défaut : 1) |Dynamique| Spécifiez la durée en secondes. Définit le délai minimum à respecter avant deux passes de vérification de contrainte consécutives. |
|MinLoadBalancingInterval | Durée en secondes (valeur par défaut : 5) |Dynamique| Spécifiez la durée en secondes. Définit le délai minimum à respecter avant deux passes d’équilibrage consécutives. |
|BalancingDelayAfterNodeDown | Durée en secondes (valeur par défaut : 120) |Dynamique|Spécifiez la durée en secondes. Ne démarrez pas l’équilibrage des activités pendant cette période après un événement d’arrêt de nœud. |
|BalancingDelayAfterNewNode | Durée en secondes (valeur par défaut : 120) |Dynamique|Spécifiez la durée en secondes. Ne démarrez pas l’équilibrage des activités pendant cette période après l’ajout d’un nouveau nœud. |
|ConstraintFixPartialDelayAfterNodeDown | Durée en secondes (valeur par défaut : 120) |Dynamique| Spécifiez la durée en secondes. Ne corrigez pas les violations de contrainte FaultDomain et UpgradeDomain pendant cette période après un événement d’arrêt de nœud. |
|ConstraintFixPartialDelayAfterNewNode | Durée en secondes (valeur par défaut : 120) |Dynamique| Spécifiez la durée en secondes. Ne corrigez pas les violations de contrainte FaultDomain et UpgradeDomain pendant cette période après l’ajout d’un nouveau nœud. |
|GlobalMovementThrottleThreshold | Valeur Uint (valeur par défaut : 1000) |Dynamique| Nombre maximum de mouvements autorisés dans la phase d’équilibrage de l’intervalle écoulé spécifié par GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Valeur Uint (valeur par défaut : 0) |Dynamique| Nombre maximum de mouvements autorisés dans la phase d’équilibrage de l’intervalle écoulé spécifié par GlobalMovementThrottleCountingInterval. 0 indique aucune limite.|
|GlobalMovementThrottleThresholdForBalancing | Valeur Uint (valeur par défaut : 0) | Dynamique|Nombre maximum de mouvements autorisés dans la phase d’équilibrage de l’intervalle écoulé spécifié par GlobalMovementThrottleCountingInterval. 0 indique aucune limite. |
|GlobalMovementThrottleCountingInterval | Durée en secondes (valeur par défaut : 600) |statique| Spécifiez la durée en secondes. Indiquez la durée de l’intervalle écoulé pendant lequel effectuer le suivi des mouvements de réplica de domaine (utilisés avec GlobalMovementThrottleThreshold). Réglez cette valeur sur 0 pour ignorer complètement la limitation globale. |
|MovementPerPartitionThrottleThreshold | Valeur Uint (valeur par défaut : 50) |Dynamique| Aucun mouvement d’équilibrage n’est effectué pour une partition si le nombre d’équilibrages des réplicas de cette partition a atteint ou dépassé la valeur MovementPerFailoverUnitThrottleThreshold pendant la période écoulée spécifiée par MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Durée en secondes (valeur par défaut : 600) |statique| Spécifiez la durée en secondes. Indiquez la durée de l’intervalle écoulé pendant lequel effectuer le suivi des mouvements de réplica pour chaque partition (utilisée avec MovementPerPartitionThrottleThreshold). |
|PlacementSearchTimeout | Durée en secondes (valeur par défaut est 0.5) |Dynamique| Spécifiez la durée en secondes. Lors du placement de services, effectuez une recherche au moins aussi longue avant de renvoyer un résultat. |
|UseMoveCostReports | Valeur booléenne (valeur par défaut : false) | Dynamique|Demande à LB d’ignorer l’élément de coût de la fonction de score, ce qui génère un nombre potentiellement important de déplacements et, donc, un placement mieux équilibré. |
|PreventTransientOvercommit | Valeur booléenne (valeur par défaut : false) | Dynamique|Détermine si PLB doit immédiatement compter sur les ressources qui seront libérées par les déplacements initiés. Par défaut, PLB peut initier un mouvement sortant et un mouvement entrant sur le même nœud, ce qui peut créer une survalidation temporaire. Le réglage de ce paramètre sur true empêche ces types de survalidation et désactive la défragmentation à la demande (également appelée placementWithMove). |
|InBuildThrottlingEnabled | Valeur booléenne (valeur par défaut : false) |Dynamique| Détermine si la limitation intégrée est activée. |
|InBuildThrottlingAssociatedMetric | Chaîne (valeur par défaut : "") |statique| Nom de la mesure associée à cette limitation. |
|InBuildThrottlingGlobalMaxValue | Entier (valeur par défaut : 0) |Dynamique|Nombre maximum de réplicas intégrés autorisés globalement. |
|SwapPrimaryThrottlingEnabled | Valeur booléenne (valeur par défaut : false)|Dynamique| Détermine si la limitation de basculement vers le réplica principal est activée. |
|SwapPrimaryThrottlingAssociatedMetric | Chaîne (valeur par défaut : "")|statique| Nom de la mesure associée à cette limitation. |
|SwapPrimaryThrottlingGlobalMaxValue | Entier (valeur par défaut : 0) |Dynamique| Nombre maximum de réplicas principaux basculés autorisés globalement. |
|PlacementConstraintPriority | Entier (valeur par défaut : 0) | Dynamique|Détermine la priorité de la contrainte de placement : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|PreferredLocationConstraintPriority | Entier (valeur par défaut : 2)| Dynamique|Détermine la priorité de la contrainte de placement préférée : 0 : dure ; 1 : souple ; 2 : optimisation ; valeur négative : à ignorer. |
|CapacityConstraintPriority | Entier (valeur par défaut : 0) | Dynamique|Détermine la priorité de la contrainte de capacité : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|AffinityConstraintPriority | Entier (valeur par défaut : 0) | Dynamique|Détermine la priorité de la contrainte d’affinité : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|FaultDomainConstraintPriority | Entier (valeur par défaut : 0) |Dynamique| Détermine la priorité de la contrainte de domaine d’erreur : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|UpgradeDomainConstraintPriority | Entier (valeur par défaut : 1)| Dynamique|Détermine la priorité de la contrainte de domaine de mise à niveau : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|ScaleoutCountConstraintPriority | Entier (valeur par défaut : 0) |Dynamique| Détermine la priorité de la contrainte de nombre de montées en charge : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|ApplicationCapacityConstraintPriority | Entier (valeur par défaut : 0) | Dynamique|Détermine la priorité de la contrainte de capacité : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|MoveParentToFixAffinityViolation | Valeur booléenne (valeur par défaut : false) |Dynamique| Paramètre déterminant si les réplicas parents peuvent être déplacés pour corriger les contraintes d’affinité.|
|MoveExistingReplicaForPlacement | Valeur booléenne (valeur par défaut : true) |Dynamique|Paramètre déterminant si le réplica doit être déplacé pendant le placement. |
|UseSeparateSecondaryLoad | Valeur booléenne (valeur par défaut : true) | Dynamique|Paramètre déterminant s’il convient d’utiliser une charge secondaire différente. |
|PlaceChildWithoutParent | Valeur booléenne (valeur par défaut : true) | Dynamique|Paramètre déterminant si le réplica de service enfant peut être placé si aucun réplica parent n’est actif. |
|PartiallyPlaceServices | Valeur booléenne (valeur par défaut : true) |Dynamique| Détermine si tous les réplicas de service dans le cluster seront placés selon le principe du « tout ou rien », en fonction du nombre limité de nœuds appropriés pour eux.|
|InterruptBalancingForAllFailoverUnitUpdates | Valeur booléenne (valeur par défaut : false) | Dynamique|Détermine si n’importe quel type de mise à jour des unités de basculement doit interrompre l’exécution lente ou rapide de l’équilibrage. Avec la valeur « false », l’exécution de l’équilibrage est interrompue si FailoverUnit est créé/supprimé, a des réplicas manquants, a modifié l’emplacement du réplica principal ou a modifié le nombre de réplicas. L’exécution de l’équilibrage n’est pas interrompue si FailoverUnit a des réplicas supplémentaires, a modifié un indicateur de réplica, n’a modifié que la version de la partition ou dans tout autre cas. |
|GlobalMovementThrottleThresholdPercentage|double, valeur par défaut : 0|Dynamique|Nombre maximal de mouvements totaux autorisés dans les phases d’équilibrage et de placement (exprimé en pourcentage du nombre total de réplicas dans le cluster) dans l’intervalle écoulé indiqué par GlobalMovementThrottleCountingInterval. 0 indique aucune limite. Si ce paramètre et le paramètre GlobalMovementThrottleThreshold sont tous deux spécifiés, la limite la plus conservatrice est utilisée.|
|GlobalMovementThrottleThresholdPercentageForBalancing|double, valeur par défaut : 0|Dynamique|Nombre maximal de mouvements autorisés dans la phase d’équilibrage (exprimé en pourcentage du nombre total de réplicas dans PLB) dans l’intervalle écoulé indiqué par GlobalMovementThrottleCountingInterval. 0 indique aucune limite. Si ce paramètre et le paramètre GlobalMovementThrottleThresholdForBalancing sont tous deux spécifiés, la limite la plus conservatrice est utilisée.|
|AutoDetectAvailableResources|Valeur booléenne, valeur par défaut : TRUE|statique|Cette configuration déclenchera la détection automatique des ressources disponibles sur le nœud (processeur et mémoire). Si cette configuration est définie sur true, nous lirons les capacités réelles et les corrigerons si l’utilisateur a spécifié des capacités de nœud incorrectes ou ne les a pas définies du tout. Si cette configuration est définie sur false, nous enverrons un avertissement que l’utilisateur a spécifié des capacités de nœud incorrectes, mais nous ne les corrigerons pas. Ce qui signifie que cet utilisateur souhaite que les capacités spécifiées soient > à celles du nœud ou si les capacités n’ont pas été définies ; cette valeur suppose une capacité illimitée |

### <a name="section-name-hosting"></a>Nom de la section : Hébergement
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| ServiceTypeRegistrationTimeout |Durée en secondes, la valeur par défaut est 300 |Dynamique|Durée maximale autorisée pour que ServiceType soit inscrit auprès de la structure |
| ServiceTypeDisableFailureThreshold |Nombre entier, la valeur par défaut est 1 |Dynamique|Il s’agit du seuil pour le nombre d’échecs après lequel FailoverManager (FM) reçoit une notification de désactiver le type de service sur ce nœud et d’essayer un autre nœud pour le positionnement. |
| ActivationRetryBackoffInterval |Durée en secondes, la valeur par défaut est 5 |Dynamique|Intervalle d’interruption sur chaque échec d’activation. Sur chaque cas d’échec d’activation continue, le système retente l’activation pour une durée équivalente à MaxActivationFailureCount. L’intervalle avant nouvelle tentative pour chaque tentative est une combinaison de l’échec de l’activation continue et de l’intervalle d’interruption d’activation. |
| ActivationMaxRetryInterval |Durée en secondes, la valeur par défaut est 300 |Dynamique|Pour chaque cas d’échec d’activation continue, le système retente l’activation pour une durée équivalente à ActivationMaxFailureCount. ActivationMaxRetryInterval spécifie l’intervalle de temps d’attente avant une nouvelle tentative après chaque échec d’activation |
| ActivationMaxFailureCount |Nombre entier, la valeur par défaut est 10 |Dynamique|Nombre de fois où les nouvelles tentatives système ont échoué à l’activation avant d’abandonner |
|ActivationTimeout| TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(180)|Dynamique| Spécifiez la durée en secondes. Délai d’attente pour l’activation, la désactivation et la mise à niveau de l’application. |
|ApplicationHostCloseTimeout| TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(120)|Dynamique| Spécifiez la durée en secondes. Lorsqu’une sortie de Fabric est détectée dans des processus auto-activés ; FabricRuntime ferme tous les réplicas dans le processus de l’hôte de l’utilisateur (applicationhost). Il s’agit du délai d’attente pour l’opération de fermeture. |
|ApplicationUpgradeTimeout| TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(360)|Dynamique| Spécifiez la durée en secondes. Délai de mise à niveau de l’application. Si le délai d’attente est inférieur à "ActivationTimeout", le déploiement échouera. |
|CreateFabricRuntimeTimeout|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(120)|Dynamique| Spécifiez la durée en secondes. La valeur du délai d’expiration pour l’appel de synchronisation FabricCreateRuntime |
|DeploymentMaxFailureCount|entier, valeur par défaut : 20| Dynamique|Le déploiement de l’application sera retenté DeploymentMaxFailureCount fois avant l’échec du déploiement de cette application sur le nœud.| 
|DeploymentMaxRetryInterval| TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(3600)|Dynamique| Spécifiez la durée en secondes. Intervalle maximum avant une nouvelle tentative de déploiement. À chaque échec continu, l’intervalle avant nouvelle tentative est calculé de la manière suivante : Min( DeploymentMaxRetryInterval; Nombre d’échecs continus * DeploymentRetryBackoffInterval) |
|DeploymentRetryBackoffInterval| TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(10)|Dynamique|Spécifiez la durée en secondes. Intervalle de temporisation pour l’échec du déploiement. À chaque échec de déploiement continu, le système retentera le déploiement jusqu'à la valeur MaxDeploymentFailureCount. L’intervalle entre chaque tentative est un produit de l’échec du déploiement continu et de l’intervalle de temporisation de déploiement. |
|EnableActivateNoWindow| valeur booléenne, valeur par défaut : FALSE|Dynamique| Le processus activé est créé en arrière-plan sans aucune console. |
|EnableProcessDebugging|valeur booléenne, valeur par défaut : FALSE|Dynamique| Permet de lancer des hôtes d’application dans le débogueur |
|EndpointProviderEnabled| valeur booléenne, valeur par défaut : FALSE|statique| Permet la gestion des ressources de points de terminaison par Fabric. Requiert la spécification de la plage de ports d’application de début et de fin dans FabricNode. |
|FabricContainerAppsEnabled| valeur booléenne, valeur par défaut : FALSE|statique| |
|FirewallPolicyEnabled|valeur booléenne, valeur par défaut : FALSE|statique| Permet l’ouverture des ports du pare-feu pour les ressources de points de terminaison avec des ports explicites spécifiés dans ServiceManifest |
|GetCodePackageActivationContextTimeout|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(120)|Dynamique|Spécifiez la durée en secondes. La valeur du délai d’expiration pour les appels CodePackageActivationContext. Ne s’applique pas aux services ad hoc. |
|IPProviderEnabled|valeur booléenne, valeur par défaut : FALSE|statique|Permet la gestion des adresses IP. |
|NTLMAuthenticationEnabled|valeur booléenne, valeur par défaut : FALSE|statique| Active la prise en charge pour l’utilisation de NTLM par les packages de code exécutés comme d’autres utilisateurs afin que les processus sur plusieurs ordinateurs puissent communiquer en toute sécurité. |
|NTLMAuthenticationPasswordSecret|SecureString, valeur par défaut : Common::SecureString(L"")|statique|Valeur chiffrée utilisée pour générer le mot de passe des utilisateurs NTLM. Doit être définie si NTLMAuthenticationEnabled a la valeur true. Validé par le déploiement. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, la valeur par défaut est Common::TimeSpan::FromMinutes(3)|Dynamique|Spécifiez la durée en secondes. Paramètres spécifiques à l’environnement. L’intervalle périodique selon lequel l’hébergement recherche de nouveaux certificats à utiliser pour la configuration NTLM FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, la valeur par défaut est Common::TimeSpan::FromMinutes(4)|Dynamique| Spécifiez la durée en secondes. Le délai d’attente pour la configuration des utilisateurs NTLM qui se servent de noms de certificats communs. Les utilisateurs NTLM sont nécessaires pour les partages FileStoreService. |
|RegisterCodePackageHostTimeout|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(120)|Dynamique| Spécifiez la durée en secondes. La valeur du délai d’expiration pour l’appel de synchronisation FabricRegisterCodePackageHost. Applicable uniquement pour les hôtes d’application avec plusieurs packages de code, par exemple FWP |
|RequestTimeout|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(30)|Dynamique| Spécifiez la durée en secondes. Représente le délai d’expiration de la communication entre l’hôte d’application de l’utilisateur et le processus Fabric pour différentes opérations associées à l’hébergement, notamment l’inscription des paramètres d’usine et l’inscription du runtime. |
|RunAsPolicyEnabled| valeur booléenne, valeur par défaut : FALSE|statique| Permet l’exécution des packages de code en tant qu’un utilisateur local autre que l’utilisateur sous lequel le processus Fabric est en cours d’exécution. Pour activer cette stratégie, Fabric doit être exécuté comme SYSTEM ou en tant qu’utilisateur avec SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(120)|Dynamique|Spécifiez la durée en secondes. La valeur du délai d’expiration pour l’appel de synchronisation Register(Stateless/Stateful)ServiceFactory |
|ServiceTypeDisableGraceInterval|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(30)|Dynamique|Spécifiez la durée en secondes. Intervalle de temps après lequel le type de service peut être désactivé |
|EnableDockerHealthCheckIntegration|Valeur booléenne, valeur par défaut : TRUE|statique|Permet l’intégration des événements de vérification d’intégrité Docker avec le rapport d’intégrité du système Service Fabric |

### <a name="section-name-federation"></a>Nom de la section : Fédération
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| LeaseDuration |Durée en secondes, la valeur par défaut est 30 |Dynamique|Durée de bail entre un nœud et ses voisins. |
| LeaseDurationAcrossFaultDomain |Durée en secondes, la valeur par défaut est 30 |Dynamique|Durée de bail entre un nœud et ses voisins dans des domaines d’erreur. |

### <a name="section-name-clustermanager"></a>Nom de la section : ClusterManager
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau** | **Conseils ou brève description** |
| --- | --- | --- | --- |
| UpgradeStatusPollInterval |Durée en secondes, la valeur par défaut est 60 |Dynamique|Fréquence d’interrogation de l’état de mise à niveau de l’application. Cette valeur détermine le taux de mise à jour pour tout appel GetApplicationUpgradeProgress |
| UpgradeHealthCheckInterval |Durée en secondes, la valeur par défaut est 60 |Dynamique|Fréquence des contrôles de l’état d’intégrité durant une mise à niveau de l’application surveillée |
| FabricUpgradeStatusPollInterval |Durée en secondes, la valeur par défaut est 60 |Dynamique|Fréquence d’interrogation de l’état de mise à niveau de la structure. Cette valeur détermine le taux de mise à jour pour tout appel GetFabricUpgradeProgress |
| FabricUpgradeHealthCheckInterval |Durée en secondes, la valeur par défaut est 60 |Dynamique|Fréquence des contrôles de l’état d’intégrité durant une mise à niveau de la structure surveillée |
|InfrastructureTaskProcessingInterval | Durée en secondes, la valeur par défaut est 10 |Dynamique|Spécifiez la durée en secondes. Intervalle de traitement utilisé par l’ordinateur d’état de traitement de la tâche infrastructure. |
|TargetReplicaSetSize |Entier (valeur par défaut : 7) |Non autorisée|Paramètre TargetReplicaSetSize pour ClusterManager. |
|MinReplicaSetSize |Entier (valeur par défaut : 3) |Non autorisée|Paramètre MinReplicaSetSize pour ClusterManager. |
|ReplicaRestartWaitDuration |Durée en secondes (valeur par défaut : 60.0 * 30)|Non autorisée|Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour ClusterManager. |
|QuorumLossWaitDuration |Durée en secondes (valeur par défaut : MaxValue) |Non autorisée| Spécifiez la durée en secondes. Paramètre QuorumLossWaitDuration pour ClusterManager. |
|StandByReplicaKeepDuration | Durée en secondes (valeur par défaut : 3600.0 * 2)|Non autorisée|Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour ClusterManager. |
|PlacementConstraints | Chaîne (valeur par défaut : "") |Non autorisée|Paramètre PlacementConstraints pour ClusterManager. |
|SkipRollbackUpdateDefaultService | Valeur booléenne (valeur par défaut : false) |Dynamique|CM ne rétablira pas les services par défaut mis à jour, lors de l’annulation de la mise à niveau de l’application. |
|EnableDefaultServicesUpgrade | Valeur booléenne (valeur par défaut : false) |Dynamique|Active la mise à niveau des services par défaut lors de la mise à niveau de l’application. Les descriptions de service par défaut sont remplacées après la mise à niveau. |
|InfrastructureTaskHealthCheckWaitDuration |Durée en secondes (valeur par défaut : 0)|Dynamique| Spécifiez la durée en secondes. Délai à respecter avant de commencer les vérifications d’intégrité après le post-traitement d’une tâche d’infrastructure. |
|InfrastructureTaskHealthCheckStableDuration | Durée en secondes (valeur par défaut : 0)|Dynamique| Spécifiez la durée en secondes. Délai à respecter pour examiner les contrôles d’intégrité passés avec succès consécutivement avant que le post-traitement d’une tâche d’infrastructure aboutisse. L’observation d’un contrôle d’intégrité ayant échoué réinitialise ce minuteur. |
|InfrastructureTaskHealthCheckRetryTimeout | Durée en secondes, la valeur par défaut est 60 |Dynamique|Spécifiez la durée en secondes. Délai à respecter pour relancer les contrôles d’intégrité ayant échoué, après le post-traitement d’une tâche d’infrastructure. L’examen d’un contrôle d’intégrité réussi réinitialise ce minuteur. |
|ImageBuilderTimeoutBuffer |Durée en secondes (valeur par défaut : 3) |Dynamique|Spécifiez la durée en secondes. Délai à respecter pour autoriser le renvoi au client des erreurs de délai d’expiration propres à Image Builder. Si cette mémoire tampon est trop petite, le délai d’expiration du client s’écoule avant le serveur et reçoit une erreur générique de délai d’expiration. |
|MinOperationTimeout | Durée en secondes, la valeur par défaut est 60 |Dynamique|Spécifiez la durée en secondes. Délai d’expiration global minimum pour le traitement interne des opérations sur ClusterManager. |
|MaxOperationTimeout |Durée en secondes (valeur par défaut : MaxValue) |Dynamique| Spécifiez la durée en secondes. Délai d’expiration global maximum pour le traitement interne des opérations sur ClusterManager. |
|MaxTimeoutRetryBuffer | Durée en secondes (valeur par défaut : 600) |Dynamique|Spécifiez la durée en secondes. Délai d’expiration maximum lorsque les nouvelles tentatives internes dues aux délais d’expiration sont <Original Time out> + <MaxTimeoutRetryBuffer>. Un délai d’expiration supplémentaire est ajouté en incréments de MinOperationTimeout. |
|MaxCommunicationTimeout |Durée en secondes (valeur par défaut : 600) |Dynamique|Spécifiez la durée en secondes. Délai d’expiration maximum pour les communications internes entre ClusterManager et d’autres services système (comme le Service d’attribution de noms, le Gestionnaire de basculement, etc.). Ce délai d’expiration doit être inférieur au MaxOperationTimeout global (car il peut y avoir plusieurs communications entre les composants du système pour chaque opération de client). |
|MaxDataMigrationTimeout |Durée en secondes (valeur par défaut : 600) |Dynamique|Spécifiez la durée en secondes. Délai d’expiration maximum pour les opérations de récupération de migration de données après une mise à niveau de l’infrastructure. |
|MaxOperationRetryDelay |Durée en secondes (valeur par défaut : 5)|Dynamique| Spécifiez la durée en secondes. Délai maximum des nouvelles tentatives internes lorsque des défaillances sont rencontrées. |
|ReplicaSetCheckTimeoutRollbackOverride |Durée en secondes (valeur par défaut : 1200) |Dynamique| Spécifiez la durée en secondes. Si ReplicaSetCheckTimeout est défini sur la valeur maximale de DWORD, il est remplacé par la valeur de cette configuration dans le cadre de la restauration. La valeur utilisée pour la restauration par progression n’est pas remplacée. |

### <a name="section-name-defragmentationemptynodedistributionpolicy"></a>Nom de la section : DefragmentationEmptyNodeDistributionPolicy
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, valeur par défaut : None|Dynamique|Spécifie la défragmentation de la stratégie qui survient lorsque vous videz des nœuds. Pour une métrique donnée, la valeur 0 indique que SF doit tenter de défragmenter les nœuds équitablement entre les domaines de mise à niveau et les domaines d’erreur ; la valeur 1 indique uniquement que les nœuds doivent être défragmentés |

### <a name="section-name-defragmentationmetrics"></a>Nom de la section : DefragmentationMetrics
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, valeur par défaut : None|Dynamique|Détermine le jeu de métriques qui doit être utilisé pour la défragmentation et pas pour l’équilibrage de charge. |

### <a name="section-name-defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>Nom de la section : DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valeur par défaut : None|Dynamique|Détermine le nombre de nœuds libres nécessaires pour défragmenter le cluster en spécifiant un pourcentage dans la plage [0.0 - 1.0) ou le nombre de nœuds vides lorsque le nombre >= 1.0 |

### <a name="section-name-dnsservice"></a>Nom de la section : DnsService
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|IsEnabled|valeur booléenne, valeur par défaut : FALSE|statique| |
|InstanceCount|entier, valeur par défaut : -1|statique|  |

### <a name="section-name-metricactivitythresholds"></a>Nom de la section : MetricActivityThresholds
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, valeur par défaut : None|Dynamique|Détermine l’ensemble de valeurs MetricActivityThresholds pour les métriques dans le cluster. L’équilibrage fonctionnera si maxNodeLoad est supérieure à MetricActivityThresholds. Pour les mesures de défragmentation, il définit la quantité de charge égale à inférieure à laquelle Service Fabric considère que le nœud est vide |

### <a name="section-name-metricbalancingthresholds"></a>Nom de la section : MetricBalancingThresholds
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valeur par défaut : None|Dynamique|Détermine l’ensemble de valeurs MetricBalancingThresholds pour les métriques dans le cluster. L’équilibrage fonctionnera si maxNodeLoad/minNodeLoad est supérieure à MetricBalancingThresholds. La défragmentation fonctionnera si la maxNodeLoad/minNodeLoad dans au moins un FD ou UD est inférieure à MetricBalancingThresholds. |

### <a name="section-name-nodebufferpercentage"></a>Nom de la section : NodeBufferPercentage
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valeur par défaut : None|Dynamique|Pourcentage de capacité de nœud par nom de mesure ; utilisé comme mémoire tampon pour maintenir un emplacement disponible sur un nœud en cas de basculement. |

### <a name="section-name-replication"></a>Nom de la section : Replication
| **Paramètre** | **Valeurs autorisées** | **Stratégie de mise à niveau**| **Conseils ou brève description** |
| --- | --- | --- | --- |
|MaxCopyQueueSize|uint, valeur par défaut : 1024|statique|Valeur maximale définissant la taille initiale de la file d’attente qui gère les opérations de réplication.  Ce nombre doit être une puissance de 2.  Si, pendant l’exécution, la file d’attente atteint cette taille, les opérations sont limitées entre les réplicateurs principal et secondaire.|
|BatchAcknowledgementInterval|TimeSpan, la valeur par défaut est Common::TimeSpan::FromMilliseconds(15)|statique|Spécifiez la durée en secondes. Détermine le délai respecté par le réplicateur après la réception d’une opération et avant de renvoyer un accusé de réception. Les autres opérations reçues pendant cette période verront leur accusé de réception renvoyé dans un seul message, ce qui diminue le trafic réseau mais peut potentiellement réduire le débit du réplicateur.|
|MaxReplicationMessageSize|uint, valeur par défaut : 52428800|statique|Taille maximale des messages des opérations de réplication. Valeur par défaut : 50 Mo.|
|ReplicatorAddress|chaîne, valeur par défaut : L"localhost:0"|statique|Point de terminaison sous la forme d’une chaîne « IP:port» utilisée par le réplicateur Windows Fabric pour se connecter à d’autres réplicas afin d’envoyer/recevoir des opérations.|
|ReplicatorListenAddress|chaîne, valeur par défaut : L"localhost:0"|statique|Point de terminaison sous la forme d’une chaîne 'IP:Port' utilisée par le réplicateur Windows Fabric pour recevoir des opérations d’autres réplicas.|
|ReplicatorPublishAddress|chaîne, valeur par défaut : L"localhost:0"|statique|Point de terminaison sous la forme d’une chaîne 'IP:Port' utilisée par le réplicateur Windows Fabric pour envoyer des opérations à d’autres réplicas.|
|MaxPrimaryReplicationQueueSize|uint, valeur par défaut : 1024|statique|Nombre maximum d’opérations pouvant exister dans la file d’attente de réplication principale. Ce nombre doit être une puissance de 2.|
|MaxPrimaryReplicationQueueMemorySize|uint, valeur par défaut : 0|statique|Valeur maximale de la file d’attente de réplication principale en octets.|
|MaxSecondaryReplicationQueueSize|uint, valeur par défaut : 2048|statique|Nombre maximum d’opérations pouvant exister dans la file d’attente de réplication secondaire. Ce nombre doit être une puissance de 2.|
|MaxSecondaryReplicationQueueMemorySize|uint, valeur par défaut : 0|statique|Valeur maximale de la file d’attente de réplication secondaire en octets.|
|QueueHealthMonitoringInterval|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(30)|statique|Spécifiez la durée en secondes. Cette valeur détermine la période de temps utilisée par le réplicateur pour surveiller tous les événements de contrôle d’intégrité d’erreur/avertissement dans les files d’attente des opérations de réplication. Une valeur '0' désactive le contrôle d’intégrité |
|QueueHealthWarningAtUsagePercent|uint, valeur par défaut : 80|statique|Cette valeur détermine l’utilisation de la file d’attente de réplication (en pourcentage) après laquelle nous envoyons un avertissement pour utilisation intensive de la file d’attente. Nous effectuons cette opération après un délai de grâce de QueueHealthMonitoringInterval. Si l’utilisation de la file d’attente est inférieure au pourcentage de ce délai de grâce|
|RetryInterval|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(5)|statique|Spécifiez la durée en secondes. Lorsqu’une opération est perdue ou rejetée, cette minuterie détermine la fréquence à laquelle le réplicateur réessaiera d’envoyer l’opération.|

### <a name="section-name-transport"></a>Nom de la section : Transport
| **Paramètre** | **Valeurs autorisées** |**Stratégie de mise à niveau** |**Conseils ou brève description** |
| --- | --- | --- | --- |
|ResolveOption|chaîne, valeur par défaut : L"unspecified"|statique|Détermine la façon dont le nom de domaine complet est résolu.  Les valeurs valides sont "unspecified/ipv4/ipv6". |
|ConnectionOpenTimeout|TimeSpan, la valeur par défaut est Common::TimeSpan::FromSeconds(60)|statique|Spécifiez la durée en secondes. Délai d’expiration pour la configuration de la connexion côté entrant et acceptant (y compris la négociation de sécurité en mode sécurisé) |

## <a name="next-steps"></a>Étapes suivantes
Lisez les articles suivants pour plus d’informations sur la gestion des clusters :

[Ajouter, restaurer, supprimer des certificats de votre cluster Azure ](service-fabric-cluster-security-update-certs-azure.md) 

