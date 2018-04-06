---
title: Personnaliser des clusters HDInsight à l'aide d’actions de script - Azure | Documents Microsoft
description: Ajoutez des composants personnalisés à des clusters HDInsight Linux à l’aide des actions de script. Les actions de script sont des scripts Bash qui permettent de personnaliser la configuration du cluster ou d’ajouter d’autres services et utilitaires comme Hue, Solr ou R.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: bc8078a1681b8977a0748f633df02beb2f2bdc8a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="customize-linux-based-hdinsight-clusters-using-script-actions"></a>Personnaliser des clusters HDInsight Linux à l’aide d’actions de script

HDInsight fournit une option de configuration intitulée **Action de script** , qui appelle des scripts personnalisés pouvant personnaliser le cluster. Ces scripts sont utilisés pour installer des composants supplémentaires et modifier des paramètres de configuration. Les actions de script peuvent être utilisées pendant ou après la création du cluster.

> [!IMPORTANT]
> La possibilité d’utiliser les actions de script sur un cluster déjà en cours d’exécution est uniquement disponible pour les clusters HDInsight sous Linux.
>
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Des actions de script peuvent également être publiées sur Azure Marketplace en tant qu’application HDInsight. Certains des exemples de ce document montrent comment vous pouvez installer une application HDInsight à l’aide de commandes d’action de script à partir de PowerShell et du Kit de développement logiciel (SDK) .NET. Pour plus d’informations sur les applications HDInsight, consultez [Publier des applications HDInsight dans Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Autorisations

Si vous utilisez un cluster HDInsight joint à un domaine, deux autorisations Ambari sont requises lorsque vous utilisez des actions de script avec le cluster :

* **AMBARI.RUN\_CUSTOM\_COMMAND** : le rôle Administrateur d’Ambari dispose de cette autorisation par défaut.
* **CLUSTER.RUN\_CUSTOM\_COMMAND** : l’Administrateur de cluster HDInsight et l’Administrateur Ambari disposent de cette autorisation par défaut.

Pour plus d’informations sur l’utilisation des autorisations avec un cluster HDInsight joint à un domaine, consultez [Gestion des clusters HDInsight joints à un domaine](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Contrôle d’accès

Si vous n’êtes pas l’administrateur/le propriétaire de votre abonnement Azure, votre compte doit posséder au moins un accès **Contributeur** au groupe de ressources qui contient le cluster HDInsight.

De plus, si vous créez un cluster HDInsight, une personne disposant d’au moins un accès **Collaborateur** à l’abonnement Azure doit avoir auparavant enregistré le fournisseur pour HDInsight. L’enregistrement du fournisseur se produit lorsqu’un utilisateur disposant d’un accès Contributeur à l’abonnement crée une ressource pour la première fois sur l’abonnement. Vous pouvez également faire cela sans créer une ressource en [enregistrant un fournisseur à l’aide de REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Pour plus d’informations sur le fonctionnement de la gestion des accès, consultez les documents suivants :

* [Prise en main de la gestion des accès dans le portail Azure](../active-directory/role-based-access-control-what-is.md)
* [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>Présentation des actions de script

Une action de script est un script bash qui s’exécute sur les nœuds dans un cluster HDInsight. Les éléments suivants constituent les caractéristiques et les fonctionnalités des actions de script.

* Elles doivent être stockées sur un URI accessible à partir du cluster HDInsight. Voici les emplacements de stockage possibles :

    * Un compte **Azure Data Lake Store** accessible via le cluster HDInsight. Pour plus d’informations sur l’utilisation d'Azure Data Lake Store avec HDInsight, voir [Créer un cluster HDInsight avec Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

        Lorsque vous utilisez un script stocké dans Data Lake Store, le format d’URI est `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

        > [!NOTE]
        > Le principal de service que HDInsight utilise pour accéder au Data Lake Store doit avoir accès en lecture au script.

    * Un blob est un **compte de stockage Azure** considéré comme compte de stockage principal ou supplémentaire pour le cluster HDInsight. HDInsight peut accéder à ces deux types de comptes de stockage lors de la création du cluster.

    * Un service de partage de fichiers public, comme Azure Blob, GitHub, OneDrive, Dropbox, etc.

        Par exemple les URI, consultez la section [Scripts d’actions de script exemples](#example-script-action-scripts).

        > [!WARNING]
        > HDInsight prend uniquement en charge les comptes de stockage Azure à __usage général__. Il ne prend actuellement pas en charge le type de compte __stockage Blob__.

* Ils peuvent être limités de manière à **s’exécuter sur certains types de nœuds uniquement**, par exemple des nœuds principaux ou des nœuds de travail (worker).

* Son état peut être **persistant** ou **ad hoc**.

    Les scripts **persistants** servent à personnaliser les nouveaux nœuds worker ajoutés au cluster lors d’opérations de mise à l’échelle. Un script persistant peut également appliquer des modifications à un autre type de nœud, tel qu’un nœud principal, quand des opérations de mise à l’échelle ont lieu.

  > [!IMPORTANT]
  > Les actions de script persistantes doivent avoir un nom unique.

    Les scripts**Ad hoc** ne sont pas conservés. Ils ne sont pas appliqués à des nœuds worker ajoutés au cluster après l’exécution du script. Vous pouvez toutefois promouvoir un script ad hoc en script persistant ou abaisser un script persistant en script ad hoc.

  > [!IMPORTANT]
  > Les actions de script utilisées lors de la création du cluster sont automatiquement rendues persistantes.
  >
  > Les scripts qui échouent ne sont pas rendus persistants, même si vous précisez qu’ils doivent l’être.

* Ils peuvent accepter les **paramètres** utilisés par le script lors de l’exécution.

* Ils sont exécutés avec des **privilèges au niveau de la racine** sur les nœuds du cluster.

* Ils peuvent être utilisés par le biais du **portail Azure**, **d’Azure PowerShell**, **d’Azure CLI v1.0** ou du **SDK HDInsight .NET**.

Le cluster conserve un historique de tous les scripts ont été exécutés. L’historique est utile lorsque vous devez rechercher l’ID d’un script pour promouvoir ou abaisser des opérations.

> [!IMPORTANT]
> Il n’existe aucune méthode automatique pour annuler les modifications apportées par une action de script. IL faut soit inverser les modifications manuellement, soit fournir un script qui les inverse.

### <a name="script-action-in-the-cluster-creation-process"></a>Action de script dans le processus de création de cluster

Les actions de script utilisées lors de la création d’un cluster sont légèrement différentes des actions de script exécutées dans un cluster existant :

* Le script est **automatiquement rendu persistant**.

* Un **échec** dans le script peut provoquer l’échec du processus de création du cluster.

Le diagramme suivant illustre le moment de l’exécution de l’action de script pendant le processus de création :

![Personnalisation du cluster HDInsight et procédure de création d’un cluster][img-hdi-cluster-states]

Le script est exécuté pendant la configuration de HDInsight. Le script s’exécute en parallèle sur tous les nœuds spécifiés dans le cluster, et s’exécute avec des privilèges racines sur les nœuds.

> [!NOTE]
> Vous pouvez effectuer des opérations telles que l’arrêt et le démarrage des services, notamment des services liés à Hadoop. Si vous arrêtez des services, vous devez vérifier que le service Ambari et d’autres services liés à Hadoop sont en cours d’exécution avant la fin du script. Ces services sont requis pour établir correctement l’intégrité et l’état du cluster pendant sa création.


Lors de la création du cluster, vous pouvez utiliser plusieurs actions de script à la fois. Ces scripts sont appelés dans l’ordre dans lequel ils ont été spécifiés.

> [!IMPORTANT]
> Les actions de script doivent se terminer dans les 60 minutes, faute de quoi elles expirent. Lors de l’approvisionnement du cluster, le script est exécuté en même temps que les autres processus d’installation et de configuration. En raison de cette concurrence pour les ressources, par exemple au niveau du temps processeur ou de la bande passante, l’exécution du script risque de prendre plus de temps que dans votre environnement de développement.
>
> Pour réduire le temps nécessaire à l’exécution du script, évitez les tâches comme le téléchargement et la compilation d’applications à partir de la source. Précompilez des applications et stockez le fichier binaire dans le stockage Azure.


### <a name="script-action-on-a-running-cluster"></a>Action de script sur un cluster en cours d’exécution

Un échec dans un script exécuté sur un cluster déjà en cours d’exécution n’entraîne pas l’échec automatique du cluster. Une fois le script terminé, le cluster doit revenir à son état « en cours d’exécution ».

> [!IMPORTANT]
> Bien que le cluster soit « en cours d’exécution », l’échec du script pourrait avoir endommagé des composants. Par exemple, un script peut supprimer les fichiers requis par le cluster.
>
> Les actions de scripts s’exécutent avec des privilèges racines. Soyez donc sûr de comprendre ce que fait un script avant de l’appliquer à votre cluster.

Quand vous appliquez un script à un cluster, l’état du cluster pour un script réussi passe **d’En cours d’exécution** à **Accepté**, puis à **Configuration HDInsight**. Il finit ensuite par revenir à **En cours d’exécution**. L’état du script est enregistré dans l’historique des actions de script et vous pouvez utiliser ces informations pour déterminer si le script a réussi ou échoué. Par exemple, l’applet de commande PowerShell `Get-AzureRmHDInsightScriptActionHistory` peut être utilisée pour afficher l’état d’un script. Cette commande renvoie des informations semblables au texte suivant :

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]
> Si vous avez modifié le mot de passe d’utilisateur (admin) du cluster après la création de ce dernier, les actions de script exécutées sur ce cluster risquent d’échouer. Si des actions de script persistantes ciblent des nœuds worker, elles peuvent échouer quand vous mettez le cluster à l’échelle.

## <a name="example-script-action-scripts"></a>Exemples de scripts d’action de script

Des scripts d’action de script peuvent être utilisés par les utilitaires suivants :

* Portail Azure
* Azure PowerShell
* Azure CLI v1.0
* Kit de développement logiciel (SDK) .NET de HDInsight

HDInsight propose des scripts pour installer les composants suivants sur des clusters HDInsight :

| NOM | Script |
| --- | --- |
| **Ajouter un compte Azure Storage** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh. Consultez la page [Ajouter un stockage supplémentaire à un cluster HDInsight](hdinsight-hadoop-add-storage.md). |
| **Installez Hue.** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Consultez [Installer et utiliser Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). |
| **Installer Presto** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh. Consultez la rubrique [Installer et utiliser Presto sur des clusters HDInsight](hdinsight-hadoop-install-presto.md). |
| **Installation de Solr** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Consultez [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). |
| **Installation de Giraph** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Consultez [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). |
| **Précharger les bibliothèques Hive** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh. Consultez [Ajouter des bibliothèques Hive sur des clusters HDInsight](hdinsight-hadoop-add-hive-libraries.md). |
| **Installer ou mettre à jour Mono** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash. Consultez [Installer ou mettre à jour Mono sur HDInsight](hdinsight-hadoop-install-mono.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Utiliser une action de script lors de la création d’un cluster

Cette section fournit des exemples sur les différentes façons d’utiliser des actions de script lors de la création d’un cluster HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Utiliser une action de script lors de la création d’un cluster à partir du portail Azure

1. Démarrez la création d'un cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Arrêtez lorsque vous atteignez la section __Résumé du cluster__.

2. Dans la section __Résumé du cluster__, sélectionnez le lien __modifier__ des__Paramètres avancés__.

    ![Lien des Paramètres avancés](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. Dans la section __Paramètres avancés__, sélectionnez __Actions de script__. Dans la section __Actions de script__, sélectionnez __+ Soumettre nouveau__

    ![Soumettre une nouvelle action de script](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. Utilisez l’entrée __Sélectionner un script__ pour sélectionner un script prédéfini. Pour utiliser un script personnalisé, sélectionnez __Personnaliser__, puis indiquez le __Nom__ et __URI de script Bash__ pour votre script.

    ![Ajouter un script sous la forme du script sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    La table suivante décrit les éléments sous la forme :

    | Propriété | Valeur |
    | --- | --- |
    | Sélectionner un script | Pour utiliser votre propre script, sélectionnez __Personnalisé__. Sinon, sélectionnez un des scripts fournis. |
    | NOM |Indiquez un nom pour l'action de script. |
    | URI de script bash |Spécifiez l’URI du script. |
    | Principal/Employé/Zookeeper |Spécifiez les nœuds (**Head**, **Worker** ou **ZooKeeper**) sur lesquels le script est exécuté. |
    | parameters |Spécifiez les paramètres, si le script le demande. |

    Utilisez l’entrée __Continuer cette action de script__ pour vous assurer que le script est appliqué aux nœuds lors de la mise à l’échelle.

5. Sélectionnez __Créer__ pour enregistrer le script. Vous pouvez ensuite utiliser __+ Soumettre nouveau__ pour ajouter un autre script.

    ![Plusieurs actions de script](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    Une fois l’ajout de scripts terminé, utilisez le bouton __Sélectionner__, puis le bouton __Suivant__ pour revenir à la section __Résumé du cluster__.

3. Sur la page __Résumé du cluster__, sélectionnez __Créer__ pour créer le cluster.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Utiliser une action de script à partir de modèles Azure Resource Manager

Les actions de script peuvent être utilisées avec des modèles Azure Resource Manager. Pour obtenir un exemple, voir [https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/](https://azure.microsoft.com/en-us/resources/templates/hdinsight-linux-run-script-action/).

Dans cet exemple, l’action de script est ajoutée à l’aide du code suivant :

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Pour plus d’informations sur le déploiement d’un modèle, consultez les documents suivants :

* [Déployer des ressources à l’aide de modèles et d’Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Déployer des ressources à l’aide de modèles et d’Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Utiliser une action de script lors de la création d’un cluster à partir d’Azure PowerShell

Dans cette section, vous allez utiliser la cmdlet [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) pour appeler des scripts afin de personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d'informations sur la configuration d'un poste de travail pour exécuter des applets de commande HDInsight PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azure/overview).

Le script suivant indique comment appliquer une action de script lors de la création d’un cluster à l’aide de PowerShell :

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

La création du cluster peut prendre plusieurs minutes.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Utiliser une action de script lors de la création d’un cluster à l’aide du Kit de développement logiciel (SDK) HDInsight .NET

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes qui facilitent l’utilisation d’HDInsight à partir d’une application .NET. Pour obtenir un exemple de code, consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).

## <a name="apply-a-script-action-to-a-running-cluster"></a>Appliquer une action de script à un cluster en cours d’exécution

Dans cette section, découvrez comment appliquer des actions de script à un cluster en cours d’exécution.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Appliquer une action de script à un cluster en cours d’exécution à partir du portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre cluster HDInsight.

2. Dans la vue d’ensemble du cluster HDInsight, sélectionnez la vignette **Actions de script**.

    ![Vignette Actions de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Vous pouvez également sélectionner **Tous les paramètres**, puis **Actions de script** dans la section Paramètres.

3. En haut de la section des actions de script, sélectionnez **Soumettre nouveau**.

    ![Ajouter une action de script à un cluster en cours d’exécution](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. Utilisez l’entrée __Sélectionner un script__ pour sélectionner un script prédéfini. Pour utiliser un script personnalisé, sélectionnez __Personnaliser__, puis indiquez le __Nom__ et __URI de script Bash__ pour votre script.

    ![Ajouter un script sous la forme du script sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    La table suivante décrit les éléments sous la forme :

    | Propriété | Valeur |
    | --- | --- |
    | Sélectionner un script | Pour utiliser votre propre script, sélectionnez __Personnalisé__. Sinon, sélectionnez un script fourni. |
    | NOM |Indiquez un nom pour l'action de script. |
    | URI de script bash |Spécifiez l’URI du script. |
    | Principal/Employé/Zookeeper |Spécifiez les nœuds (**Head**, **Worker** ou **ZooKeeper**) sur lesquels le script est exécuté. |
    | parameters |Spécifiez les paramètres, si le script le demande. |

    Utilisez l’entrée __Continuer cette action de script__ pour vous assurer que le script est appliqué aux nœuds lors de la mise à l’échelle.

5. Pour finir, cliquez sur le bouton **Créer** afin d’appliquer le script au cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Appliquer une action de script à un cluster en cours d’exécution à partir d’Azure PowerShell

Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d'informations sur la configuration d'un poste de travail pour exécuter des applets de commande HDInsight PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azure/overview).

L’exemple suivant décrit comment appliquer une action de script à un cluster en cours d'exécution :

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Une fois l’opération terminée, vous recevez des informations similaires au texte ci-dessous :

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Appliquer une action de script à un cluster en cours d’exécution à partir d’Azure CLI

Avant de poursuivre, assurez-vous que vous avez installé et configuré l'interface de ligne de commande Azure (CLI). Pour plus d’informations, consultez [Installer Azure CLI 1.0](../cli-install-nodejs.md).

> [!IMPORTANT]
> HDInsight nécessite Azure CLI 1.0. Actuellement, Azure CLI 2.0 ne fournit pas de commandes pour travailler avec HDInsight.

1. Pour passer en mode Azure Resource Manager, utilisez la commande suivante sur la ligne de commande :

    ```bash
    azure config mode arm
    ```

2. Utilisez la commande suivante pour vous identifier auprès de votre abonnement Azure.

    ```bash
    azure login
    ```

3. Utilisez la commande suivante pour appliquer une action de script à un cluster en cours d'exécution

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Si vous omettez les paramètres de cette commande, vous êtes invité à les saisir. Si le script que vous spécifiez avec `-u` accepte des paramètres, vous pouvez les spécifier à l’aide du paramètre `-p`.

    Les types de nœud valides sont `headnode`, `workernode` et `zookeeper`. Si le script doit être appliqué à plusieurs types de nœud, spécifiez ces types en les séparant par un « ; ». Par exemple : `-n headnode;workernode`.

    Pour conserver le script, ajoutez le paramètre `--persistOnSuccess`. Vous pouvez également continuer le script à une date ultérieure à l’aide de `azure hdinsight script-action persisted set`.

    Une fois le travail terminé, vous obtenez un résultat similaire au texte suivant :

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>Appliquer une action de script à un cluster en cours d’exécution à l’aide de l’API REST

Consultez [Run script actions on a running cluster (Appliquer des actions de script à un cluster en cours d’exécution)](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Appliquer une action de script à un cluster en cours d’exécution à partir du Kit de développement logiciel (SDK) HDInsight .NET

Pour obtenir un exemple d’utilisation du Kit de développement logiciel (SDK) .NET pour appliquer des scripts à un cluster, voir [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

## <a name="view-history-promote-and-demote-script-actions"></a>Afficher l’historique, promouvoir et abaisser des actions de script

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre cluster HDInsight.

2. Dans la vue d’ensemble du cluster HDInsight, sélectionnez la vignette **Actions de script**.

    ![Vignette Actions de script](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > Vous pouvez également sélectionner **Tous les paramètres**, puis **Actions de script** dans la section Paramètres.

4. Un historique des scripts pour ce cluster s’affiche dans la section des actions de script. Ces informations comprennent une liste de scripts persistants. Dans la capture d’écran ci-dessous, vous pouvez voir que le script Solr a été exécuté sur ce cluster. La capture d’écran n’affiche aucun script persistants.

    ![Section des actions de script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. la sélection d’un script dans l’historique permet d’afficher sa section Propriétés. Dans le coin supérieur de l’écran, vous pouvez réexécuter le script ou le promouvoir.

    ![Propriétés des actions de script](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. Vous pouvez également utiliser le **…** situé sur le côté droit des entrées de la section des actions de script pour effectuer des actions.

    ![Utilisation de … des actions de script](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell

| Utilisez... | Pour... |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |Récupérer des informations sur les actions de script persistantes |
| Get-AzureRmHDInsightScriptActionHistory |Récupérer l’historique des actions de script appliquées au cluster, ou les informations d’un script spécifique |
| Set-AzureRmHDInsightPersistedScriptAction |Promouvoir une action de script ad hoc en action de script persistante |
| Remove-AzureRmHDInsightPersistedScriptAction |Abaisser une action de script persistante en action ad hoc |

> [!IMPORTANT]
> L’utilisation de `Remove-AzureRmHDInsightPersistedScriptAction` n’annule pas les actions effectuées par un script. Cette applet de commande supprime uniquement l’indicateur persistant.

L’exemple de script suivant illustre l’utilisation des applets de commande pour promouvoir, puis pour abaisser un script.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

| Utilisez... | Pour... |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Récupérer une liste des actions de script persistantes |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Récupérer des informations sur les actions de script persistantes |
| `azure hdinsight script-action history list <clustername>` |Récupérer l’historique des actions de script appliquées au cluster |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Récupérer des informations sur une action de script spécifique |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Promouvoir une action de script ad hoc en action de script persistante |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Abaisser une action de script persistante en action ad hoc |

> [!IMPORTANT]
> L’utilisation de `azure hdinsight script-action persisted delete` n’annule pas les actions effectuées par un script. Cette applet de commande supprime uniquement l’indicateur persistant.

### <a name="using-the-hdinsight-net-sdk"></a>Utilisation du Kit de développement logiciel (SDK) HDInsight .NET

Pour obtenir un exemple d’utilisation du Kit de développement logiciel (SDK) .NET afin de récupérer l’historique des scripts d’un cluster, promouvoir ou abaisser des scripts, voir [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action).

> [!NOTE]
> Cet exemple montre également comment installer une application de HDInsight à l’aide du Kit de développement logiciel (SDK) .NET.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Prise en charge des logiciels open source utilisés sur les clusters HDInsight

Le service Microsoft Azure HDInsight utilise un écosystème de technologies open source formées autour de Hadoop. Microsoft Azure fournit un niveau général de prise en charge pour les technologies open source. Pour plus d’informations, consultez la section **Étendue de la prise en charge** du [site Web FAQ du support Azure](https://azure.microsoft.com/support/faq/). Le service HDInsight fournit un niveau supplémentaire de prise en charge pour les composants intégrés.

Deux types de composant open source sont disponibles dans le service HDInsight :

* **Composants intégrés** : ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Par exemple, YARN ResourceManager, le langage de requête Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster est disponible sur la page [Nouveautés des versions de cluster Hadoop fournies par HDInsight](hdinsight-component-versioning.md).
* **Composants personnalisés** : en tant qu’utilisateur du cluster, vous pouvez installer ou utiliser, dans votre charge de travail, tout composant qui est disponible dans la communauté ou que vous avez créé.

> [!WARNING]
> Les composants fournis avec le cluster HDInsight sont entièrement pris en charge. Le support Microsoft vous aide à isoler et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. La prise en charge de Microsoft peut être en mesure de résoudre le problème, SINON vous pourrez avoir besoin d’associer les chaînes disponibles pour les technologies open source lorsqu’il est possible de recourir à une expertise reconnue concernant cette technologie. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple: [Hadoop](http://hadoop.apache.org/).

Le service HDInsight fournit plusieurs méthodes d’utilisation de ces composants personnalisés. Quel que soit le mode d’utilisation ou d’installation du composant sur le cluster, le même niveau de support s’applique. La liste suivante décrit les méthodes les plus courantes d’utilisation des composants personnalisés sur des clusters HDInsight :

1. Envoi de travaux : il est possible d’envoyer des travaux Hadoop ou d’autres types de travaux au cluster qui exécute ou utilise des composants personnalisés.

2. Personnalisation de cluster : lors de la création du cluster, vous pouvez spécifier des paramètres supplémentaires et des composants personnalisés qui sont installés sur les nœuds de cluster.

3. Exemples : pour des composants personnalisés fréquemment utilisés, il arrive que Microsoft et d’autres éditeurs proposent des exemples illustrant leur utilisation sur les clusters HDInsight. Ces exemples sont fournis sans support.

## <a name="troubleshooting"></a>Résolution de problèmes

Vous pouvez utiliser l’interface utilisateur web de Ambari pour afficher les informations consignées par des actions de script. Si le script échoue pendant la création du cluster, les journaux sont également disponibles dans le compte de stockage par défaut associé au cluster. Cette section fournit des informations sur la façon de récupérer les journaux à l'aide de ces deux options.

### <a name="using-the-ambari-web-ui"></a>À l'aide de l'interface utilisateur Web d’Ambari

1. Dans votre navigateur, accédez à https://CLUSTERNAME.azurehdinsight.net. Remplacez CLUSTERNAME par le nom de votre cluster HDInsight.

    Lorsque vous y êtes invité, saisissez le nom de compte (admin) et le mot de passe correspondant au cluster. Vous devrez peut-être saisir de nouveau les informations d’identification d’administrateur dans un formulaire Web.

2. Dans la barre située en haut de la page, sélectionnez l’entrée **ops** . Une liste des opérations en cours et précédentes effectuées sur le cluster via Ambari s’affiche.

    ![Barre de l’interface utilisateur web Ambari avec ops sélectionné](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Recherchez les entrées comportant **run\_customscriptaction** dans la colonne **Operations**. Ces entrées sont créées lors de l’exécution des actions de script.

    ![Capture d’écran des opérations](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    Sélectionnez l’entrée run\customscriptaction et explorez les liens pour afficher les sorties STDOUT et STDERR. Cette sortie est générée lorsque le script s’exécute et peut contenir des informations utiles.

### <a name="access-logs-from-the-default-storage-account"></a>Accès aux journaux à partir du compte de stockage par défaut

Si la création d’un cluster échoue en raison d’une erreur de script, les journaux sont conservés dans le compte de stockage de cluster.

* Les journaux de stockage sont disponibles dans `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Capture d’écran des opérations](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    Sous ce répertoire, les journaux sont classés séparément selon les nœuds headnode, workernode et zookeeper. Voici quelques exemples :

    * **Headnode** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **Nœud Worker** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Nœud Zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Toutes les valeurs stdout et stderr de l'hôte correspondant sont téléchargées vers le compte de stockage. Il existe un fichier **output-\*.txt** et un fichier **errors-\*.txt** pour chaque action de script. Le fichier output-*.txt contient des informations sur l'URI du script que vous avez exécuté sur l'ordinateur hôte. Le texte suivant constitue un exemple de ces informations :

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Vous pouvez créer plusieurs fois un cluster d'action de script portant le même nom. Dans ce cas, vous pouvez différencier les journaux correspondants selon le nom de dossier DATE. Par exemple, la structure de dossiers d’un cluster (mycluster) créé à différentes dates ressemble à aux entrées de journaux suivantes :

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04``\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Si vous créez un cluster d'action de script avec le même nom le même jour, vous pouvez utiliser le préfixe unique pour identifier les fichiers journaux correspondants.

* Si vous créez un cluster vers 0 h 00 (minuit), il est possible que les fichiers journaux s'étendent sur deux jours. Dans ce cas, vous voyez deux dossiers de dates différents pour le même cluster.

* Le téléchargement des fichiers journaux vers le conteneur par défaut peut prendre jusqu'à 5 minutes, en particulier pour les clusters de grande taille. Par conséquent, si vous souhaitez accéder aux journaux, vous ne devez pas immédiatement supprimer le cluster en cas d'échec d'une action de script.

### <a name="ambari-watchdog"></a>Agent de surveillance Ambari

> [!WARNING]
> Ne modifiez pas le mot de passe pour l’agent de surveillance Ambari (hdinsightwatchdog) sur votre cluster HDInsight basé sur Linux. La modification du mot de passe pour ce compte élimine la possibilité d’exécuter de nouvelles actions de script sur le cluster HDInsight.

### <a name="cant-import-name-blobservice"></a>Impossible d’importer le nom BlobService

__Symptômes__ : échec de l’action de script. Un texte semblable à l’erreur suivante s’affiche lorsque vous observez l’opération dans Ambari :

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Cause__ : cette erreur se produit si vous mettez à niveau le client Python de stockage Azure qui est inclus dans le cluster HDInsight. HDInsight attend le client de stockage Azure 0.20.0.

__Résolution__ : pour résoudre cette erreur, connectez-vous manuellement à chaque nœud de cluster à l’aide de `ssh` et utilisez la commande suivante pour réinstaller la version correcte du client de stockage :

```bash
sudo pip install azure-storage==0.20.0
```

Pour en savoir plus sur la connexion au cluster via SSH, voir [Utiliser SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>L’historique n’affiche pas les scripts utilisés lors de la création du cluster

Si votre cluster a été créé avant le 15 mars 2016, une entrée peut ne pas s’afficher dans l’historique des actions de script. Le redimensionnement du cluster provoque l’affichage des scripts dans l’historique des actions de script.

Deux exceptions :

* Si votre cluster a été créé avant le 1er septembre 2015. Il s’agit de la date à laquelle les actions de script ont été introduites. Tout cluster créé avant cette date n’aurait pas pu utiliser les actions de script pour la création du cluster.

* Si vous avez utilisé plusieurs actions de script lors de la création du cluster et donné le même nom à plusieurs scripts, ou le même nom, le même URI, mais des paramètres différents pour plusieurs scripts. Dans ce cas, vous recevez l’erreur suivante :

    Aucune nouvelle action de script ne peut être exécutée sur ce cluster en raison d’un conflit de noms de script dans les scripts existants. Les noms de script fournis au moment de la création du cluster doivent être uniques. Les scripts existants sont exécutés lors du redimensionnement.

## <a name="next-steps"></a>Étapes suivantes

* [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Installer et utiliser Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Ajouter un stockage supplémentaire à un cluster HDInsight](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Procédure de création d’un cluster"
