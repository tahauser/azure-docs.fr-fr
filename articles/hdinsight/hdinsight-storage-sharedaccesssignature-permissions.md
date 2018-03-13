---
title: "Restreindre l’accès à l’aide des signatures d’accès partagé (SAP)- Azure HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser les signatures d’accès partagé pour limiter l’accès HDInsight aux données stockées dans des objets blob de stockage Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/25/2018
ms.author: larryfr
ms.openlocfilehash: d3e37ba0f590cf0572b84a53bdd407af63a19d36
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Utilisation des signatures d’accès partagé du stockage Azure pour restreindre l’accès aux données dans HDInsight

HDInsight dispose d’un accès total aux données dans les comptes de stockage Azure associés au cluster. Vous pouvez utiliser des signatures d’accès partagé sur le conteneur d’objets blob pour restreindre l’accès aux données. Par exemple, pour fournir un accès en lecture seule aux données. Les signatures d’accès partagé (SAP) sont une fonctionnalité des comptes de stockage Azure qui vous permet de limiter l’accès aux données. Par exemple, en fournissant un accès en lecture seule aux données.

> [!IMPORTANT]
> Pour une solution utilisant Apache Ranger, envisagez d’utiliser les clusters HDInsight joints à un domaine. Pour plus d’informations, consultez le document [Configurer des clusters HDInsight joints à un domaine (préversion)](./domain-joined/apache-domain-joined-configure.md).

> [!WARNING]
> HDInsight doit disposer d’un accès total au stockage par défaut pour le cluster.

## <a name="requirements"></a>Configuration requise

* Abonnement Azure
* C# ou Python. Un exemple de code C# est fourni en tant que solution Visual Studio.

  * La version de Visual Studio doit être 2013, 2015 ou 2017
  * La version de Python doit être 2.7 ou une version ultérieure.

* Cluster HDInsight basé sur Linux OU [Azure PowerShell][powershell] : si vous avez un cluster existant basé sur Linux, vous pouvez utiliser Ambari pour ajouter une signature d’accès partagé au cluster. Si ce n’est pas le cas, vous pouvez utiliser Azure PowerShell pour créer un cluster et ajouter une signature d’accès partagé lors de la création du cluster.

    > [!IMPORTANT]
    > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Fichiers d’exemple à l’adresse [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Ce dépôt contient les éléments suivants :

  * Un projet Visual Studio permettant de créer un conteneur de stockage, une stratégie stockée et une SAP pour une utilisation avec HDInsight.
  * Un script Python permettant de créer un conteneur de stockage, une stratégie stockée et une SAP pour une utilisation avec HDInsight
  * Un script PowerShell permettant de créer un cluster HDInsight et de le configurer afin d’utiliser la SAP.

## <a name="shared-access-signatures"></a>Les signatures d’accès partagé

Il existe deux types de signatures d’accès partagé :

* Ad hoc : l’heure de début, l’heure d’expiration et les autorisations associées à cette SAP sont spécifiées sur l’URI de SAP.

* Stratégie d’accès stockée : une stratégie d’accès stockée est définie sur un conteneur de ressources, tel qu’un conteneur d’objets blob. et permet de gérer les contraintes d’une ou de plusieurs signatures d’accès partagé. Lorsque vous associez une signature d'accès partagé à une stratégie d'accès stockée, la signature hérite des contraintes (heure de début, heure d'expiration et autorisations) définies pour la stratégie.

La différence entre les deux formes est importante pour un scénario clé : la révocation. Une signature d'accès partagé est une URL. Par conséquent, toute personne qui obtient la signature peut s'en servir, quel que soit celui qui l'a demandée initialement. Si une SAP est publiée publiquement, elle peut être utilisée par n’importe qui. Une clé d'accès partagé qui est distribuée est valide jusqu'à ce que l'un des quatre événements suivants ait lieu :

1. L'heure d'expiration spécifiée sur la signature d'accès partagé est atteinte.

2. Le délai d’expiration spécifié sur la stratégie d’accès stockée référencée par la SAP est atteint. Dans les scénarios suivants, l’heure d’expiration est atteinte :

    * L’intervalle de temps est écoulé.
    * La stratégie d’accès stockée est modifiée pour que son heure d’expiration soit passée. Modifier l’heure d’expiration est un moyen de révoquer la SAP.

3. La stratégie d'accès stockée référencée par la signature d'accès partagé est supprimée, ce qui est une autre manière de révoquer la signature d'accès partagé. Si vous recréez la stratégie d’accès stockée avec le même nom, tous les jetons de SAP de la stratégie précédente sont valides (si l’heure d’expiration sur la SAP n’est pas passée). Si vous avez l’intention de révoquer la SAP, veillez à utiliser un nom différent si vous recréez la stratégie d’accès avec une heure d’expiration située dans le futur.

4. La clé de compte qui a été utilisée pour créer la signature d'accès partagé est régénérée. Cette régénération provoque l’échec de l’authentification de toutes les applications qui utilisent la clé précédente. Mettez à jour tous les composants vers la nouvelle clé.

> [!IMPORTANT]
> L’URI d’une signature d’accès partagé est associé à la clé du compte utilisée pour créer la signature et à la stratégie d’accès stockée correspondante (le cas échéant). Si aucune stratégie d’accès stockée n’est spécifiée, la seule façon de révoquer une signature d’accès partagé consiste à modifier la clé du compte.

Nous vous recommandons de toujours utiliser des stratégies d’accès stockées. Lorsque vous utilisez des stratégies stockées, vous pouvez révoquer des signatures ou étendre la date d’expiration, selon vos besoins. Les étapes décrites dans ce document utilisent les stratégies d’accès stockées pour générer des SAP.

Pour plus d’informations sur les SAP, voir [Présentation du modèle SAP](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

### <a name="create-a-stored-policy-and-sas-using-c"></a>Créer une stratégie stockée et une SAP à l’aide de C\#

1. Ouvrez la solution dans Visual Studio.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SASToken**, puis sélectionnez **Propriétés**.

3. Sélectionnez **Paramètres** et ajoutez des valeurs pour les entrées suivantes :

   * StorageConnectionString : chaîne de connexion pour le compte de stockage pour lequel vous souhaitez créer une stratégie stockée et une SAP. Le format doit être `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`, où `myaccount` est le nom de votre compte de stockage et `mykey` est la clé pour le compte de stockage.

   * ContainerName : conteneur du compte de stockage auquel vous souhaitez restreindre l’accès.

   * SASPolicyName : nom à utiliser pour la stratégie stockée à créer.

   * FileToUpload : chemin d’accès à un fichier qui est chargé sur le conteneur.

4. Exécutez le projet. Des informations semblables au texte suivant s’affichent une fois la signature d’accès partagé générée :

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Enregistrez le jeton de stratégie SAP, le nom du compte de stockage et le nom du conteneur. Ces valeurs sont utilisées quand vous associez le compte de stockage à votre cluster HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Créer une stratégie stockée et une SAP à l’aide de Python

1. Ouvrez le fichier SASToken.py et modifiez les valeurs suivantes :

   * nom\_stratégie : nom à utiliser pour la stratégie stockée à créer.

   * storage\_account\_name : le nom de votre compte de stockage.

   * storage\_account\_key : la clé du compte de stockage.

   * storage\_container\_name : conteneur du compte de stockage auquel vous souhaitez restreindre l’accès.

   * exemple\_chemin\_fichier : chemin d’accès à un fichier qui est chargé sur le conteneur.

2. Exécutez le script. Il affiche le jeton SAP semblable au texte suivant quand il est terminé :

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Enregistrez le jeton de stratégie SAP, le nom du compte de stockage et le nom du conteneur. Ces valeurs sont utilisées quand vous associez le compte de stockage à votre cluster HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Utilisation de la SAP avec HDInsight

Lorsque vous créez un cluster HDInsight, vous devez spécifier un compte de stockage principal, et vous pouvez éventuellement indiquer des comptes de stockage supplémentaires. Ces deux méthodes d’ajout de stockage nécessitent un accès complet aux comptes de stockage et aux conteneurs qui sont utilisés.

Pour utiliser une signature d’accès partagé afin de limiter l’accès à un conteneur, ajoutez une entrée personnalisée à la configuration **core-site** du cluster.

* Pour les clusters HDInsight **basés sur Windows** ou **basés sur Linux**, vous pouvez ajouter l’entrée lors de la création du cluster à l’aide de PowerShell.
* Pour les clusters HDInsight **basés sur Linux**, modifiez la configuration après la création du cluster à l’aide d’Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Créer un cluster qui utilise la signature d’accès partagé

Un exemple de création de cluster HDInsight utilisant les SAP est inclus dans le répertoire `CreateCluster` du dépôt. Pour l’utiliser, procédez comme suit :

1. Ouvrez le fichier `CreateCluster\HDInsightSAS.ps1` dans un éditeur de texte et modifiez les valeurs suivantes au début du document.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Par exemple, remplacez `'mycluster'` avec le nom du cluster que vous souhaitez créer. Les valeurs SAP doivent correspondre aux valeurs des étapes précédentes lors de la création du compte de stockage et du jeton SAP.

    Une fois que vous avez modifié les valeurs, enregistrez le fichier.

2. Ouvrez une nouvelle invite Azure PowerShell. Si vous ne maîtrisez pas Azure PowerShell ou que vous ne l’avez pas installé, consultez la rubrique [Installer et configurer Azure PowerShell][powershell].

1. À partir de l’invite, utilisez la commande suivante pour vous identifier auprès de votre abonnement Azure :

    ```powershell
    Login-AzureRmAccount
    ```

    Quand vous y êtes invité, connectez-vous avec le compte associé à votre abonnement Azure.

    Si votre compte est associé à plusieurs abonnements Azure, vous devrez peut-être utiliser `Select-AzureRmSubscription` pour sélectionner l’abonnement que vous souhaitez utiliser.

4. À partir de l’invite, remplacez les répertoires par le répertoire `CreateCluster` qui contient le fichier HDInsightSAS.ps1. Utilisez ensuite la commande suivante pour exécuter le script

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Quand il s’exécute, le script consigne la sortie dans l’invite PowerShell et crée le groupe de ressources et les comptes de stockage. Il vous invite à entrer le nom d’utilisateur HTTP pour le cluster HDInsight. Ce compte est utilisé pour sécuriser l’accès HTTP/s au cluster.

    Si vous créez un cluster basé sur Linux, vous êtes invité à saisir un nom et un mot de passe de compte d’utilisateur SSH. Ce compte est utilisé pour la connexion à distance au cluster.

   > [!IMPORTANT]
   > Lorsque vous êtes invité à saisir le nom d’utilisateur et le mot de passe HTTP/s ou SSH, vous devez fournir un mot de passe qui répond aux critères suivants :
   >
   > * Il doit contenir au moins 10 caractères.
   > * Il doit contenir au moins un chiffre.
   > * Il doit contenir au moins un caractère non alphanumérique.
   > * Il doit contenir au moins une lettre minuscule et une lettre majuscule.

L’exécution de ce script prend un certain temps, environ 15 minutes en général. Lorsque le script se termine sans erreur, le cluster a été créé.

### <a name="use-the-sas-with-an-existing-cluster"></a>Utiliser la signature d’accès partagé avec un cluster existant

Si vous disposez d’un cluster existant basé sur Linux, vous pouvez ajouter la SAP pour la configuration **core-site** en procédant comme suit :

1. Ouvrez l’interface utilisateur web Ambari de votre cluster. L’adresse de cette page est https://VOTRE NOM DE CLISTER.azurehdinsight.net. À l’invite, authentifiez-vous auprès du cluster au moyen du nom et du mot de passe d’administrateur que vous avez utilisés lors de la création du cluster.

2. Dans la partie gauche de l’interface utilisateur web d’Ambari, sélectionnez **HDFS**, puis sélectionnez l’onglet **Configurations** au milieu de la page.

3. Sélectionnez l’onglet **Avancé** et faites défiler jusqu’à ce que vous trouviez la section **Configuration core-site personnalisée**.

4. Développez la section **Configuration core-site personnalisée**, puis faites défiler jusqu’à la fin et sélectionnez le lien **Ajouter une propriété...**. Utilisez les valeurs suivantes pour les champs **Clé** et **Valeur** :

   * **Clé**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Valeur**: la SAP retournée par l’application C# ou Python exécutée précédemment.

     Remplacez **CONTAINERNAME** avec le nom du conteneur que vous avez utilisé avec l’application C# ou SAP. Remplacez **STORAGEACCOUNTNAME** avec le nom du compte de stockage que vous avez utilisé.

5. Cliquez sur le bouton **Ajouter** pour enregistrer cette clé et cette valeur, puis cliquez sur le bouton **Enregistrer** pour enregistrer les modifications de configuration. Lorsque vous y êtes invité, ajoutez une description de la modification (« Ajout d’accès de stockage SAP », par exemple), puis cliquez sur **Enregistrer**.

    Cliquez sur **OK** lorsque les modifications ont été effectuées.

   > [!IMPORTANT]
   > Vous devez redémarrer plusieurs services pour que la modification prenne effet.

6. Dans l’interface utilisateur web Ambari, sélectionnez **HDFS** dans la liste sur la gauche, puis sélectionnez **Restart All Affected** (Redémarrer tous les éléments affectés) dans la liste déroulante **Actions de service** située à droite. Lorsque vous y êtes invité, sélectionnez __Confirm Restart All__ (Confirmer le redémarrage).

    Répétez ce processus pour les entrées MapReduce2 et YARN.

7. Une fois les services redémarrés, sélectionnez chacun d’entre eux et désactivez le mode maintenance à partir de la liste déroulante **Actions de service**.

## <a name="test-restricted-access"></a>Tester l’accès restreint

Pour vérifier que vous disposez d’un accès restreint, procédez comme suit :

* Pour les clusters HDInsight **basés sur Windows**, utilisez le Bureau à distance pour vous connecter au cluster. Pour plus d’informations, consultez [Se connecter à HDInsight à l’aide du protocole RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Une fois la connexion établie, utilisez l’icône de **ligne de commande Hadoop** du Bureau pour ouvrir une invite de commandes.

* Pour les clusters HDInsight **basés sur Linux** , utilisez SSH pour vous connecter au cluster. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Une fois connecté au cluster, procédez comme suit pour vérifier que vous pouvez uniquement lire et répertorier les éléments sur le compte de stockage SAP :

1. Pour répertorier le contenu du conteneur, utilisez la commande suivante à partir de l’invite : 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Remplacez **SASCONTAINER** avec le nom du conteneur créé pour le compte de stockage SAP. Remplacez **SASACCOUNTNAME** par le nom du compte de stockage utilisé pour la SAP.

    La liste inclut le fichier chargé lors de la création du conteneur et de la SAP.

2. Utilisez la commande suivante pour vérifier que vous pouvez lire le contenu du fichier. Remplacez **SASCONTAINER** et **SASACCOUNTNAME** comme à l’étape précédente. Remplacez **FILENAME** avec le nom du fichier affiché dans la commande précédente :

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Cette commande répertorie le contenu du fichier.

3. Pour télécharger le fichier sur le système de fichiers local, utilisez la commande suivante :

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Cette commande télécharge le fichier sur un fichier local nommé **testfile.txt**.

4. Utilisez la commande suivante pour charger le fichier local sur un nouveau fichier nommé **testupload.txt** sur le stockage SAP :

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Vous recevez un message similaire au texte suivant :

        put: java.io.IOException

    Cette erreur se produit, car l’emplacement de stockage est en lecture + liste uniquement. Pour placer les données sur le stockage par défaut pour le cluster, accessible en écriture, utilisez la commande suivante :

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Cette fois, l’opération doit se terminer normalement.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="a-task-was-canceled"></a>Une tâche a été annulée

**Symptômes**: lorsque vous créez un cluster à l’aide du script PowerShell, le message d’erreur suivant peut s’afficher :

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Cause** : cette erreur peut se produire si vous utilisez un mot de passe pour l’utilisateur admin/HTTP pour le cluster ou pour l’utilisateur SSH (pour les clusters basés sur Linux).

**Résolution**: utilisez un mot de passe qui répond aux critères suivants :

* Il doit contenir au moins 10 caractères.
* Il doit contenir au moins un chiffre.
* Il doit contenir au moins un caractère non alphanumérique.
* Il doit contenir au moins une lettre minuscule et une lettre majuscule.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment ajouter un stockage à accès limité à votre cluster HDInsight, découvrez d’autres façons de travailler avec des données sur votre cluster :

* [Utilisation de Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
