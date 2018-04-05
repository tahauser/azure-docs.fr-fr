---
title: "Déboguer Hadoop dans HDInsight : afficher les journaux et interpréter les messages d’erreur - Azure | Microsoft Docs"
description: "Découvrez les messages d'erreur susceptibles de s'afficher lorsque vous administrez HDInsight au moyen de PowerShell, ainsi que la procédure de récupération."
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: ashishthaps
documentationcenter: 
ms.assetid: 7e6ceb0e-8be8-4911-bc80-20714030a3ad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashish
ms.openlocfilehash: a5db3848eda2dbb6f117562e059b909575966993
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="analyze-hadoop-logs"></a>Analyser les journaux Hadoop

Chaque cluster Hadoop dans Azure HDInsight dispose d'un compte de stockage Azure utilisé comme système de fichiers par défaut. Le compte de stockage est désigné comme le compte de stockage par défaut. Le cluster utilise le stockage de tables Azure et le stockage d’objets Blob sur le compte de stockage par défaut pour stocker ses journaux.  Pour trouver le compte de stockage par défaut pour votre cluster, consultez [Gestion des clusters Hadoop dans HDInsight](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Les journaux sont conservés dans le compte de stockage, même après la suppression du cluster.

## <a name="logs-written-to-azure-tables"></a>Journaux écrits dans les tables Azure

Les journaux écrits dans les tables Azure fournissent un niveau de compréhension sur ce qui se passe avec un cluster HDInsight.

Lorsque vous créez un cluster HDInsight, six tables sont automatiquement créées pour les clusters Linux dans le stockage de tables par défaut :

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Les noms des fichiers de table sont au format **u<ClusterName>JJMoiAAAAàHHMMSSsss<TableName>**.

Ces tables contiennent les champs suivants :

* ClusterDnsName
* ComponentName
* EventTimestamp
* Host
* MALoggingHash
* Message
* N
* PreciseTimeStamp
* Rôle
* RowIndex
* Locataire
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Outils permettant d’accéder aux journaux
Il existe de nombreux outils permettant d’accéder aux données de ces tables :

* Visual Studio
* Azure Storage Explorer
* Power Query pour Excel

#### <a name="use-power-query-for-excel"></a>Utiliser Power Query pour Excel
Power Query peut être installé à partir de [Microsoft Power Query pour Excel](http://www.microsoft.com/en-us/download/details.aspx?id=39379). Consultez la page de téléchargement pour la configuration système requise.

**Utiliser Power Query pour ouvrir et analyser le journal du service**

1. Ouvrez **Microsoft Excel**.
2. Dans le menu **Power Query**, cliquez sur **À partir d’Azure**, puis sur **À partir du stockage de tables Microsoft Azure**.
   
    ![HDInsight Hadoop Excel PowerQuery ouvrir le stockage de tables Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Entrez le nom du compte de stockage, le nom court ou le nom de domaine complet.
4. Entrez la clé du compte de stockage. Une liste de tables doit s’afficher :
   
    ![Journaux HDInsight Hadoop stockés dans le stockage de tables Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Cliquez avec le bouton droit de la souris sur la table hadoopservicelog dans le volet du **navigateur** et sélectionnez **Modifier**. Vous devez voir quatre colonnes. Vous pouvez supprimer les colonnes **Clé de partition**, **Clé de ligne** et **Horodatage** en les sélectionnant, puis en cliquant sur **Supprimer les colonnes** dans les options du ruban.
6. Cliquez sur l’icône de développement de la colonne Contenu pour choisir les colonnes que vous souhaitez importer dans la feuille de calcul Excel. Pour cette démonstration, j’ai choisi les colonnes TraceLevel et ComponentName : elles peuvent me donner des informations de base sur les composants ayant rencontré des problèmes.
   
    ![Choisir les colonnes des journaux HDInsight Hadoop](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Cliquez sur **OK** pour importer les données.
8. Sélectionnez les colonnes **TraceLevel**, Rôle et **ComponentName**, puis cliquez sur la commande **Regroupement** dans le ruban.
9. Cliquez sur **OK** dans la boîte de dialogue Regroupement.
10. Cliquez sur** Appliquer et fermer**.

Vous pouvez maintenant utiliser Excel pour filtrer et trier en fonction de vos besoins. Vous pouvez vouloir inclure d’autres colonnes (comme Message) afin d’approfondir les problèmes lorsqu’ils se produisent, mais le fait de sélectionner et de regrouper les colonnes décrites ci-dessus fournit un aperçu correct de ce qui se passe avec les services Hadoop. La même idée peut être appliquée aux tables setuplog et hadoopinstalllog.

#### <a name="use-visual-studio"></a>Utiliser Visual Studio
**Utiliser Visual Studio**

1. Ouvrez Visual Studio.
2. Dans le menu **Affichage**, cliquez sur **Cloud Explorer**. Ou cliquez simplement sur **CTRL+\, CTRL+X**.
3. Dans **Cloud Explorer**, sélectionnez **Types de ressources**.  L’autre option disponible est **Groupes de ressources**.
4. Développez **Comptes de stockage**, le compte de stockage par défaut pour votre cluster, puis **Tables**.
5. Double-cliquez sur **hadoopservicelog**.
6. Ajoutez un filtre. Par exemple : 
   
        TraceLevel eq 'ERROR'
   
    ![Choisir les colonnes des journaux HDInsight Hadoop](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Pour plus d’informations sur la création de filtres, consultez [Construction de chaînes de filtrage pour le Concepteur de tables](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Journaux écrits dans le stockage d’objets Blob Azure
[Les journaux écrits dans les tables Azure](#log-written-to-azure-tables) fournissent un niveau de compréhension sur ce qui se passe avec un cluster HDInsight. Toutefois, ces tables ne fournissent pas de journaux au niveau des tâches, qui peuvent être utiles pour explorer les problèmes lorsqu’ils se produisent. Pour fournir ce niveau de détail supérieur, les clusters HDInsight sont configurés pour écrire les journaux des tâches dans votre compte de stockage d’objets Blob pour toute tâche envoyée par le biais de Templeton. En pratique, cela signifie les tâches envoyées à l’aide des applets de commande Microsoft Azure PowerShell ou les API de soumission de tâche .NET, pas les tâches envoyées via RDP/accès par ligne de commande au cluster. 

Pour afficher les journaux, consultez [Accéder aux journaux des applications YARN dans HDInsight sous Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Pour plus d’informations sur les journaux des applications, consultez la page [Simplifier la gestion et l’accès des journaux utilisateur dans YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

## <a name="view-cluster-health-and-job-logs"></a>Afficher les journaux de travail et d’état d’intégrité du cluster
### <a name="access-the-ambari-ui"></a>Accéder à l’interface utilisateur Ambari
À partir du portail Azure, cliquez sur un nom de cluster HDInsight pour ouvrir le volet du cluster. Dans le volet du cluster, cliquez sur **Tableau de bord**.

![Tableau de bord du cluster](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Accéder à l’interface utilisateur Yarn
À partir du portail Azure, cliquez sur un nom de cluster HDInsight pour ouvrir le volet du cluster. Dans le volet du cluster, cliquez sur **Tableau de bord**. Quand vous y êtes invité, entrez les informations d’identification d’administrateur du cluster. Dans Ambari, sélectionnez **YARN** dans la liste des services sur la gauche. Dans la page qui s’affiche, sélectionnez **Liens rapides**, puis sélectionnez l’entrée du nœud principal actif et l’interface utilisateur du Gestionnaire des ressources.

Vous pouvez utiliser l’interface utilisateur YARN pour effectuer les opérations suivantes :

* **Obtenir l’état du cluster**. Dans le volet gauche, développez **Cluster**, puis cliquez sur **About**. Une série de détails sur l’état du cluster apparaissent, comme la mémoire totale allouée, les cœurs utilisés, l’état du gestionnaire des ressources de cluster ou la version du cluster.
  
    ![Tableau de bord du cluster](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Obtenir l’état du nœud**. Dans le volet gauche, développez **Cluster**, puis cliquez sur **Nodes**. Cette opération répertorie tous les nœuds du cluster, l’adresse HTTP de chaque nœud, les ressources allouées à chaque nœud, etc.
* **Surveiller l’état du travail**. Dans le volet gauche, développez **Cluster**, puis cliquez sur **Applications** pour répertorier tous les travaux dans le cluster. Si vous souhaitez examiner les travaux dans un état spécifique (comme nouveau, envoyé, en cours d’exécution, etc.), cliquez sur le lien approprié sous **Applications**. Vous pouvez cliquer sur le nom du travail pour obtenir des informations supplémentaires sur celui-ci, relatives par exemple à la sortie ou aux journaux.

### <a name="access-the-hbase-ui"></a>Accéder à l’interface utilisateur HBase
À partir du portail Azure, cliquez sur un nom de cluster HDInsight HBase pour ouvrir le volet du cluster. Dans le volet du cluster, cliquez sur **Tableau de bord**. Quand vous y êtes invité, entrez les informations d’identification d’administrateur du cluster. Dans Ambari, sélectionnez HBase dans la liste des services. Sélectionnez **Liens rapides** en haut de la page, pointez vers le lien de nœud Zookeeper actif, puis cliquez sur HBase Master UI (Interface utilisateur principale HBase).

## <a name="hdinsight-error-codes"></a>Codes d’erreur HDInsight
Les messages d’erreur répertoriés dans cette section visent à aider les utilisateurs de Hadoop dans Azure HDInsight à comprendre les conditions d’erreur auxquelles ils sont susceptibles d’être confrontés lorsqu’ils administrent le service avec Azure PowerShell et à les conseiller sur les mesures à prendre pour la récupération à la suite d’une erreur.

Certains de ces messages d'erreur peuvent également apparaître dans le portail Azure lorsqu'il sert à gérer les clusters HDinsight. Mais d'autres messages d'erreur que vous pouvez rencontrer ici sont moins granulaires, en raison des contraintes qui s'appliquent aux mesures correctives possibles dans ce contexte. D'autres encore sont fournis dans les contextes où l'atténuation est évidente. 

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
* **Description**: fournissez des informations détaillées sur la base de données SQL Azure pour au moins un composant afin d’utiliser les paramètres personnalisés pour les metastores Hive et Oozie.
* **Atténuation**: vous devez fournir un metastore SQL Azure valide et relancer la requête.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Description**: impossible de créer un cluster dans la région *nom_région*. Utilisez une région HDInsight valide et relancez la requête.
* **Atténuation**: créez le cluster dans une région qui les prend actuellement en charge : Asie du Sud-Est, Europe de l’Ouest, Europe du Nord, Est des États-Unis ou Ouest des États-Unis.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Description**: le serveur n’a pas pu trouver l’enregistrement de cluster demandé.  
* **Atténuation**: retentez l’opération.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Description**: le nom DNS du cluster *nom_DNS* est incorrect. Assurez-vous que le nom commence et se termine par un caractère alphanumérique et contient uniquement le caractère spécial '-'.  
* **Atténuation**: assurez-vous que vous avez utilisé un nom DNS correct pour votre cluster, c’est-à-dire un nom qui commence et se termine par un caractère alphanumérique et qui ne contient aucun caractère spécial à l’exception du tiret « - », puis retentez l’opération.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Description**: le nom de cluster *nom_cluster* n’est pas disponible. Choisissez un autre nom.  
* **Atténuation**: vous devez saisir un nom de cluster unique qui n’existe pas déjà, puis refaire une tentative. Si vous utilisez le portail, l'interface utilisateur vous avertira si un nom de cluster est déjà utilisé pendant la procédure de création.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Description** : le mot de passe de cluster n’est pas valide. Le mot de passe doit avoir une longueur d’au moins 10 caractères et contenir au moins un chiffre, une lettre majuscule, une lettre minuscule et un caractère spécial, sans espace. Par ailleurs, il ne doit pas contenir le nom d’utilisateur.  
* **Atténuation**: fournissez un mot de passe de cluster correct et retentez l’opération.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Description**: le nom d’utilisateur du cluster est incorrect. Assurez-vous que le nom d'utilisateur ne contient pas de caractères spéciaux ni d'espaces.  
* **Atténuation**: fournissez un nom d’utilisateur de cluster correct et retentez l’opération.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Description**: le nom DNS du cluster *nom_DNS_cluster* est incorrect. Assurez-vous que le nom commence et se termine par un caractère alphanumérique et contient uniquement le caractère spécial '-'.  
* **Atténuation**: fournissez un nom d’utilisateur de cluster DNS correct et retentez l’opération.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Description** : le nom du conteneur dans l’URI *URI_conteneur* et le nom DNS *nom_DNS* dans le corps de la requête doivent être identiques.  
* **Atténuation**: assurez-vous que le nom de votre conteneur et le nom de votre DNS sont identiques, puis retentez l’opération.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Description**: configuration du cluster incorrecte. Impossible de trouver des définitions de nœud de données dans la taille de nœud.  
* **Atténuation**: retentez l’opération.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Description**: échec de la suppression du déploiement pour le cluster  
* **Atténuation**: essayez de nouveau de supprimer.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Description**: erreur de configuration du service. Informations de mappage DNS requises introuvables.  
* **Atténuation**: supprimez le cluster et recréez-en un.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Description**: tentative de duplication d’un conteneur de cluster. Il existe un enregistrement pour *nom_conteneur* , mais les valeurs Etag ne correspondent pas.
* **Atténuation**: fournissez un nom unique pour le conteneur et retentez l’opération de création.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Description**: le service hébergé *nom_service_hébergé* contient déjà un cluster. Un service hébergé ne peut pas contenir plusieurs clusters.  
* **Atténuation**: hébergez le cluster dans un autre service hébergé.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Description**: le serveur n’a pas pu mettre à jour l’état du déploiement du cluster.  
* **Atténuation**: retentez l’opération. Si le problème se reproduit plusieurs fois, contactez CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Description**: Le cluster *nom_cluster* a été supprimé dans le cadre de la maintenance. Recréez le cluster.
* **Atténuation**: recréez le cluster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Description**: configuration du cluster incorrecte. Configuration de nœud principal introuvable dans les tailles de nœud.
* **Atténuation**: retentez l’opération.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Description**: impossible de créer le service hébergé *nom_service_hébergé*. Relancez la requête.  
* **Atténuation**: répétez la requête.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Description**: le service hébergé *nom_service_hébergé* a déjà un déploiement de production. Un service hébergé ne peut pas contenir plusieurs déploiements de production. Relancez la requête avec un nom de cluster différent.
* **Atténuation**: utilisez un nom de cluster différent et relancez la requête.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Description**: le service hébergé *nom_service_hébergé* pour le cluster est introuvable.  
* **Atténuation**: si le cluster est dans l’état d’erreur, supprimez-le et retentez l’opération.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Description**: aucun déploiement n’est associé au service hébergé *nom_service_hébergé* .  
* **Atténuation**: si le cluster est dans l’état d’erreur, supprimez-le et retentez l’opération.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Description** : l’ID d’abonnement *ID_abonnement* n’a plus de cœurs pour créer le cluster *nom_cluster*. Requis : *Ressources_requises*, disponible : *Ressources_disponibles*.  
* **Atténuation**: libérez des ressources dans votre abonnement ou augmentez les ressources disponibles pour l’abonnement et essayez de recréer le cluster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Description** : l’ID d’abonnement *ID_abonnement* n’a pas de quota pour un nouveau service hébergé permettant de créer le cluster *nom_cluster*.  
* **Atténuation**: libérez des ressources dans votre abonnement ou augmentez les ressources disponibles pour l’abonnement et essayez de recréer le cluster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Description**: une erreur interne s’est produite sur le serveur. Relancez la requête.  
* **Atténuation**: répétez la requête.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Description**: l’emplacement d’Azure Storage *nom_région_données* n’est pas un emplacement correct. Assurez-vous que la région est correcte et relancez la requête.
* **Atténuation**: sélectionnez un emplacement de stockage qui prend en charge HDInsight, vérifiez que votre cluster y est également enregistré et retentez l’opération.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Description**: la taille de la machine virtuelle est incorrecte pour les nœuds de données. Seule la taille « Machine virtuelle large » est prise en charge pour tous les nœuds de données.  
* **Atténuation**: spécifiez la taille de nœud prise en charge pour le nœud de données et retentez l’opération.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Description**: la taille de la machine virtuelle est incorrecte pour le nœud principal. Seule la taille « Machine virtuelle extra large » est prise en charge pour le nœud principal.  
* **Atténuation**: spécifiez la taille de nœud prise en charge pour le nœud principal et retentez l’opération.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Description** : l’ID d’abonnement *ID_abonnement* utilisé ne dispose pas des autorisations nécessaires pour exécuter l’opération de suppression sur le cluster *nom_cluster*.  
* **Atténuation**: si le cluster est dans l’état d’erreur, supprimez-le et retentez l’opération.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Description**: le nom du conteneur d’objets blob du compte de stockage externe *nom_conteneur* est incorrect. Assurez-vous que le nom commence par une lettre et contient uniquement des lettres minuscules, des chiffres et des tirets.  
* **Atténuation**: spécifiez un nom correct pour le conteneur d’objets blob du compte de stockage et retentez l’opération.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Description**: les détails de la clé secrète doivent être définis dans la configuration du compte de stockage externe *nom_compte_stockage* .  
* **Atténuation**: spécifiez une clé secrète correcte pour le compte de stockage et retentez l’opération.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Description**: l’en-tête de version *entête_version* n’est pas au format correct : aaaa-mm-jj.  
* **Atténuation**: spécifiez un format correct pour l’en-tête de version et retentez l’opération.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Description**: configuration du cluster incorrecte. Plusieurs configurations de nœud principal trouvées.  
* **Atténuation** : modifiez la configuration de façon à spécifier un seul nœud principal.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Description**: il n’a pas été possible d’exécuter l’opération dans le temps imparti ou en respectant le nombre maximum de tentatives. Relancez la requête.  
* **Atténuation**: répétez la requête.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Description**: le paramètre *nom_paramètre* ne peut pas être de type null ou vide.  
* **Atténuation**: spécifiez une valeur correcte pour le paramètre.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Description**: une ou plusieurs entrées de demande de création de cluster sont incorrectes. Assurez-vous que les valeurs d'entrée sont correctes et relancez la requête.  
* **Atténuation**: assurez-vous que les valeurs d’entrée sont correctes et relancez la requête.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Description** : capacité de région non disponible pour la région *nom_région* et l’ID d’abonnement *ID_abonnement*.  
* **Atténuation**: spécifiez une région qui prend en charge les clusters HDInsight. Les régions publiquement prises en charge sont les suivantes : Asie du Sud-Est, Europe du Nord, Europe de l’Ouest, Est des États-Unis ou Ouest des États-Unis.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Description** : le compte de stockage *nom_compte_stockage* se trouve dans la région *nom_région_actuelle*. La région doit être la même que celle du cluster *nom_région_cluster*.  
* **Atténuation**: spécifiez un compte de stockage dans la même région que votre cluster ou, si vos données se trouvent déjà dans le compte de stockage, créez un nouveau cluster dans la même région que le compte de stockage existant. Si vous utilisez le portail, l'interface utilisateur vous avertira de ce problème à l'avance.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Description**: l’ID d’abonnement *ID_abonnement* n’est pas actif.  
* **Atténuation**: réactivez votre abonnement ou obtenez un nouvel abonnement valide.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Description**: l’ID d’abonnement *ID_abonnement* est introuvable.  
* **Atténuation**: vérifiez que votre ID d’abonnement est correct et retentez l’opération.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Description**: impossible de résoudre le DNS *URL_DNS*. Veillez à fournir l'URL complète du point de terminaison d'objet blob.  
* **Atténuation**: fournissez une URL d’objet blob correcte. L'URL DOIT être entièrement correcte, notamment commencer par *http://* et se terminer par *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Description**: impossible de vérifier l’emplacement de la ressource *URL_DNS*. Veillez à fournir l'URL complète du point de terminaison d'objet blob.  
* **Atténuation**: fournissez une URL d’objet blob correcte. L'URL DOIT être entièrement correcte, notamment commencer par *http://* et se terminer par *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Description** : capacité de version non disponible pour la version *version_spécifiée* et l’ID d’abonnement *ID_abonnement*.  
* **Atténuation**: choisissez une version disponible et retentez l’opération.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Description**: la version *version_spécifiée* n’est pas prise en charge.
* **Atténuation**: choisissez une version qui est prise en charge et retentez l’opération.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Description** : la version *version_spécifiée* n’est pas disponible dans la région Azure *région_spécifiée*.  
* **Atténuation**: choisissez une version qui est prise en charge dans la région spécifiée et retentez l’opération.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Description**: configuration du cluster incorrecte. Configuration de compte WASB requise introuvable dans les comptes externes.  
* **Atténuation**: vérifiez que le compte existe et qu’il est correctement spécifié dans la configuration, puis retentez l’opération.

## <a name="next-steps"></a>étapes suivantes

* [Utiliser les vues Ambari pour déboguer les travaux Tez dans HDInsight](../hdinsight-debug-ambari-tez-view.md)
* [Activer les dumps de tas pour les services Hadoop sur HDInsight sur Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](../hdinsight-hadoop-manage-ambari.md)
