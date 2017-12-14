---
title: "Gestion d’Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure | Microsoft Docs"
description: "Apprenez à gérer des comptes Data Lake Analytics, des sources de données, des utilisateurs et des travaux à l'aide de l’interface de ligne de commande Azure."
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: 142a5baf0ffb0425856520fadccee27f3fc0517f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Gestion de Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure (CLI)
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Découvrez comment gérer des comptes Azure Data Lake Analytics, des sources de données, des utilisateurs et des travaux à l’aide de l’interface de ligne de commande Azure. Pour afficher les rubriques de gestion à l’aide d’autres outils, cliquez sur l’onglet de sélection ci-dessus.


**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Interface de ligne de commande Azure**. Consultez [Installation et configuration de l’interface de ligne de commande Azure](../cli-install-nodejs.md).
  * Téléchargez et installez les **outils d’interface de ligne de commande Azure** [version préliminaire](https://github.com/MicrosoftBigData/AzureDataLake/releases) pour effectuer cette démonstration.
* **Authentication**, en utilisant la commande  suivante :
  
        azure login
    Pour plus d’informations sur l’authentification à l’aide d’un compte professionnel ou scolaire, consultez la section [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure](/cli/azure/authenticate-azure-cli).
* **Passez en mode Azure Resource Manager**en exécutant la commande suivante :
  
        azure config mode arm

**Pour répertorier les commandes Data Lake Store et Data Lake Analytics :**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>Gérer les comptes
Avant d'exécuter des travaux Data Lake Analytics, vous devez avoir un compte Data Lake Analytics. Contrairement à Azure HDInsight, vous ne payez pas pour un compte Analytics lorsque celui-ci n'exécute aucun travail.  Vous payez uniquement lorsqu'il exécute un travail.  Pour plus d'informations, consultez [Présentation d'Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Création de comptes
      azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


### <a name="update-accounts"></a>Mise à jour de comptes
La commande suivante met à jour les propriétés d'un compte Data Lake Analytics existant

    azure datalake analytics account set "<Data Lake Analytics Account Name>"


### <a name="list-accounts"></a>Énumérer les comptes
Énumérer les comptes Data Lake Analytics 

    azure datalake analytics account list

Répertorier les comptes Data Lake Analytics dans un groupe de ressources spécifique

    azure datalake analytics account list -g "<Azure Resource Group Name>"

Obtenir des détails sur un compte Data Lake Analytics spécifique

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

### <a name="delete-data-lake-analytics-accounts"></a>Supprimer les comptes Data Lake Analytics
      azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gestion des sources de données du compte
Data Lake Analytics prend actuellement en charge les sources de données suivantes :

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Stockage Azure](../storage/common/storage-introduction.md)

Lorsque vous créez un compte Analytics, vous devez désigner un compte Azure Data Lake Storage comme compte de stockage par défaut. Le compte de stockage ADL par défaut est utilisé pour stocker les métadonnées du travail et les journaux d'audit du travail. Après la création d'un compte Analytics, vous pouvez ajouter des comptes Data Lake Storage et/ou des comptes Azure Storage supplémentaires. 

### <a name="find-the-default-adl-storage-account"></a>Recherchez le compte de stockage ADL par défaut
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

La valeur est répertoriée sous properties:datalakeStoreAccount:name.

### <a name="add-additional-azure-blob-storage-accounts"></a>Ajouter des comptes de stockage d'objets Blob Azure supplémentaires
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

> [!NOTE]
> Seuls les noms courts Blob Storage sont pris en charge.  N'utilisez pas de nom de domaine complet, comme « myblob.blob.core.windows.net ».
> 
> 

### <a name="add-additional-data-lake-store-accounts"></a>Ajouter des comptes Data Lake Store supplémentaires
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] est un commutateur facultatif pour indiquer si le Data Lake ajouté est le compte Data Lake par défaut. 

### <a name="update-existing-data-source"></a>Mettre à jour la source de données existante
Pour faire d’un compte existant de magasin Data Lake un compte par défaut :

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d

Pour mettre à jour une clé de compte Blob Storage existante :

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### <a name="list-data-sources"></a>Répertorier les sources de données :
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

![Data Lake Analytics : énumérer les sources de données](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Supprimer des sources de données :
Pour supprimer un compte de magasin Data Lake :

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Pour supprimer un compte de stockage Blob :

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## <a name="manage-jobs"></a>Gestion des travaux
Vous devez disposer d'un compte Data Lake Analytics avant de pouvoir créer un travail.  Pour plus d'informations, consultez [Gestion des comptes Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Répertorier les travaux
      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Data Lake Analytics : énumérer les sources de données](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obtenir les détails du travail
      azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"

### <a name="submit-jobs"></a>Soumettre les travaux
> [!NOTE]
> La priorité par défaut d'un travail est 1 000 et le degré de parallélisme par défaut d'un travail est 1.
> 
> 

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### <a name="cancel-jobs"></a>Annuler les travaux
Utilisez la commande list pour trouver l'id de travail, puis utilisez la fonction d’annulation pour annuler le travail.

      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
      azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## <a name="manage-catalog"></a>Gestion du catalogue
Le catalogue U-SQL est utilisé pour structurer les données et le code afin que les scripts U-SQL puissent les partager. Le catalogue permet les meilleures performances possibles avec les données comprises dans Azure Data Lake. Pour plus d'informations, consultez [Utilisation du catalogue U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-catalog-items"></a>Répertorier les éléments du catalogue
    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table

Les types incluent : database, schema, assembly, external data source, table, table valued function ou table statistics.

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-arm-groups"></a>Utilisez des groupes ARM
Les applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et services tiers. Azure Resource Manager (ARM) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe. Pour plus d'informations, consultez [Présentation d'Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

Un service Data Lake Analytics peut inclure les composants suivants :

* Compte Azure Data Lake Analytics
* Compte Azure Data Lake Storage par défaut requis
* Comptes Azure Data Lake Storage supplémentaires
* Comptes Azure Storage supplémentaires

Vous pouvez créer tous ces composants dans un groupe ARM pour les rendre plus facile à gérer.

![Compte et stockage Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un compte Data Lake Analytics et les compte de stockage dépendants doivent se trouver dans le même centre de données Azure.
Le groupe ARM peut cependant se trouver dans un autre centre de données.  

## <a name="see-also"></a>Voir aussi
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Prise en main des analyses Data Lake à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Gérer les analyses Azure Data Lake à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
* [Surveiller et résoudre les problèmes des tâches d’analyse Azure Data Lake à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

