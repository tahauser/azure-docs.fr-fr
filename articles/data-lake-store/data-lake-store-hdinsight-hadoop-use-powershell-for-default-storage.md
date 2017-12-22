---
title: "Créer des clusters HDInsight avec Data Lake Store comme stockage par défaut à l’aide de PowerShell | Microsoft Docs"
description: "Utiliser Azure PowerShell pour créer et utiliser les clusters HDInsight avec Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8917af15-8e37-46cf-87ad-4e6d5d67ecdb
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 2f1793c2de2b68a8b155ada73044c6bc36882612
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-as-default-storage-by-using-powershell"></a>Créer des clusters HDInsight avec Data Lake Store comme stockage par défaut à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Utiliser le portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Utiliser PowerShell (pour le stockage par défaut)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Utiliser PowerShell (pour le stockage supplémentaire)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utiliser Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

Apprenez à utiliser Azure PowerShell pour configurer un cluster Azure HDInsight avec Azure Data Lake Store comme stockage par défaut. Pour obtenir des instructions sur la création d’un cluster HDInsight avec Data Lake Store comme stockage supplémentaire, consultez [Créer un cluster HDInsight avec Data Lake Store en tant que stockage supplémentaire](data-lake-store-hdinsight-hadoop-use-powershell.md).

Voici quelques considérations importantes pour l’utilisation de HDInsight avec Data Lake Store :

* L’option permettant de créer des clusters HDInsight avec accès à Data Lake Store comme stockage par défaut est disponible si vous utilisez HDInsight versions 3.5 et 3.6.

* L’option permettant de créer des clusters HDInsight avec accès à Data Lake Store comme stockage par défaut n’est *pas disponible* pour les clusters HDInsight Premium.

Pour configurer HDInsight de façon à fonctionner avec Data Lake Store à l’aide de PowerShell, suivez les instructions des cinq prochaines sections.

## <a name="prerequisites"></a>Composants requis
Avant de commencer le didacticiel, veillez à ce que vos mots de passe répondent aux exigences suivantes :

* **Un abonnement Azure** : consultez la page [Obtention d’un essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou version ultérieure** : consultez [Installation et configuration de PowerShell](/powershell/azure/overview).
* **Kit de développement logiciel (SDK) Windows** : pour l’installer, consultez [Téléchargements et outils pour Windows 10](https://dev.windows.com/en-us/downloads). Le Kit de développement logiciel (SDK) vous permet de créer un certificat de sécurité.
* **Principal de service Active Directory Azure** : ce didacticiel explique comment créer un principal de service dans Azure Active Directory (Azure AD). Toutefois, vous devez être administrateur Azure AD pour pouvoir créer un principal du service. Si vous êtes administrateur, vous pouvez ignorer ce prérequis et poursuivre le didacticiel.

    >[!NOTE]
    >Vous pouvez créer un principal de service uniquement si vous être administrateur Azure AD. Votre administrateur Azure AD doit créer un principal de service. Vous pouvez ensuite créer un cluster HDInsight avec Data Lake Store. Le principal de service doit être créé à l’aide d’un certificat, comme décrit dans [Créer un principal de service avec certificat](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-a-data-lake-store-account"></a>Créer un compte Data Lake Store
Pour créer un compte Data Lake Store, procédez comme suit :

1. Sur votre bureau, ouvrez une fenêtre PowerShell et entrez l’extrait de code ci-dessous. Lorsque vous êtes invité à vous connecter, connectez-vous en tant qu’administrateur ou propriétaire de l’abonnement. 

        # Sign in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

    > [!NOTE]
    > Si vous recevez une erreur similaire à `Register-AzureRmResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` pendant l’inscription du fournisseur de ressources Data Lake Store, il est possible que votre abonnement ne figure pas dans la liste approuvée pour Data Lake Store. Pour activer votre abonnement Azure pour la préversion publique Data Lake Store, suivez les instructions à la page [Prise en main d’Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).
    >

2. Un compte Data Lake Store est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    Vous devriez obtenir un résultat similaire à ceci :

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Créez un compte Data Lake Store. Le nom de compte que vous spécifiez doit contenir uniquement des lettres minuscules et des chiffres.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    Un résultat similaire à ce qui suit s’affiche normalement :

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

4. L’utilisation de Data Lake Store en tant que stockage par défaut vous oblige à spécifier un chemin d’accès racine où les fichiers spécifiques au cluster sont copiés lors de la création du cluster. Pour créer un chemin d’accès racine, qui, dans l’extrait de code ci-dessous est **hdiadlcluster/clusters/**, utilisez les applets de commande ci-dessous :

        $myrootdir = "/"
        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/clusters/hdiadlcluster


## <a name="set-up-authentication-for-role-based-access-to-data-lake-store"></a>Configurer l'authentification pour définir un accès à Data Lake Store en fonction du rôle
Chaque abonnement Azure est associé à une entité Azure AD. Les utilisateurs et services qui accèdent aux ressources par le biais du portail Azure ou de l’API Azure Resource Manager doivent au préalable s’authentifier auprès d’Azure AD. L’accès est accordé aux abonnements et services Azure en leur affectant le rôle approprié sur une ressource Azure. Pour les services, un principal de service identifie le service dans Azure AD.

Cette section montre comment accorder l'accès à une ressource Azure (le compte Azure Data Lake Store créé précédemment) à un service d’application, comme HDInsight. Pour ce faire, créez un principal de service pour l’application, puis attribuez-lui des rôles avec Azure PowerShell.

Pour configurer l’authentification Active Directory pour Azure Data Lake, effectuez les tâches présentées dans les deux sections suivantes.

### <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé
Assurez-vous que le [SDK Windows](https://dev.windows.com/en-us/downloads) est installé avant de suivre la procédure décrite dans cette section. Vous devez également avoir créé un répertoire, comme *C:\mycertdir*, où sera créé le certificat.

1. Dans la fenêtre PowerShell, accédez à l’emplacement où vous avez installé le Kit de développement logiciel (SDK) Windows (en général, *C:\Program Files (x86)\Windows Kits\10\bin\x86*) et utilisez l’utilitaire [MakeCert][makecert] pour créer un certificat auto-signé et une clé privée. Utilisez les commandes suivantes :

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Vous devrez entrer le mot de passe de la clé privée. Une fois la commande exécutée avec succès, **CertFile.cer** et **mykey.pvk** apparaissent dans le répertoire du certificat que vous avez spécifié.
2. Utilisez l’utilitaire [Pvk2Pfx][pvk2pfx] pour convertir en un fichier .pfx les fichiers .pvk et .cer créés par MakeCert. Exécutez la commande suivante :

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Lorsque vous y êtes invité, entrez le mot de passe de la clé privée spécifié au préalable. La valeur que vous donnez au paramètre **-po** est le mot de passe associé au fichier .pfx. Une fois la commande exécutée avec succès, un **CertFile.pfx** est visible dans le répertoire du certificat que vous avez spécifié.

### <a name="create-an-azure-ad-and-a-service-principal"></a>Créer une application Azure AD et un principal de service
Cette section décrit les étapes pour créer un principal de service pour une application Azure AD, attribuer un rôle au principal de service et vous authentifier en tant que principal de service en fournissant un certificat. Pour créer une application dans Azure AD, exécutez les commandes suivantes :

1. Collez les applets de commande suivantes dans la fenêtre de la console PowerShell. Vérifiez que la valeur que vous donnez à la propriété **-DisplayName** est unique. Les valeurs pour **-HomePage** et **-IdentiferUris** sont des valeurs d’espace réservé et ne sont pas vérifiées.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzureRmADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Créez un principal de service en utilisant l’ID d’application.

        $servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Accordez au principal du service l’accès à la racine et à tous les dossiers du chemin d’accès racine spécifié précédemment. Utilisez les applets de commande suivantes :

        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters -AceType User -Id $objectId -Permissions All
        Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path /clusters/hdiadlcluster -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-store-as-the-default-storage"></a>Création d’un cluster Linux HDInsight avec Data Lake Store comme stockage par défaut

Dans cette section, vous allez créer un cluster HDInsight Hadoop Linux avec Data Lake Store comme stockage par défaut. Pour cette version, le cluster HDInsight et Data Lake Store doivent se situer au même emplacement.

1. Récupérez l’ID de locataire d’abonnement et stockez-le pour une utilisation ultérieure.

        $tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Créez le cluster HDInsight en utilisant les applets de commande suivantes :

        # Set these variables

        $location = "East US 2"
        $storageAccountName = $dataLakeStoreName                       # Data Lake Store account name
        $storageRootPath = "<Storage root path you specified earlier>" # E.g. /clusters/hdiadlcluster
        $clusterName = "<unique cluster name>"
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzureRmHDInsightCluster `
               -ClusterType Hadoop `
               -OSType Linux `
               -ClusterSizeInNodes $clusterNodes `
               -ResourceGroupName $resourceGroupName `
               -ClusterName $clusterName `
               -HttpCredential $httpCredentials `
               -Location $location `
               -DefaultStorageAccountType AzureDataLakeStore `
               -DefaultStorageAccountName "$storageAccountName.azuredatalakestore.net" `
               -DefaultStorageRootPath $storageRootPath `
               -Version "3.6" `
               -SshCredential $sshCredentials `
               -AadTenantId $tenantId `
               -ObjectId $objectId `
               -CertificateFilePath $certificateFilePath `
               -CertificatePassword $password

    Après exécution de l’applet de commande, le résultat énumère les détails du cluster.

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-store"></a>Exécuter des tâches de test sur le cluster HDInsight pour utiliser Data Lake Store
Une fois que vous avez configuré un cluster HDInsight, vous pouvez exécuter des tâches de test sur le cluster pour vérifier qu’il peut accéder à Data Lake Store. Pour ce faire, exécutez un exemple de tâche Hive qui crée une table avec les exemples de données déjà disponibles dans Data Lake Store dans *<cluster root>/example/data/sample.log*.

Dans cette section, vous établissez une connexion SSH (Secure Shell) au cluster HDInsight Linux que vous avez créé, puis vous exécutez l’exemple de requête Hive.

* Si vous utilisez un client Windows pour établir la connexion SSH au cluster, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si vous utilisez un client Linux pour établir la connexion SSH au cluster, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Une fois que vous avez établi la connexion, démarrez l'interface de ligne de commande Hive à l’aide de la commande suivante :

        hive
2. À l’aide de l’interface de ligne de commande, entrez les instructions suivantes pour créer une table nommée **vehicles** en utilisant les exemples de données dans Data Lake Store :

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'adl:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Vous devriez voir la sortie de la requête dans la console SSH.

    >[!NOTE]
    >Le chemin d’accès aux exemples de données de la commande CREATE TABLE ci-dessus est `adl:///example/data/`, où `adl:///` est la racine du cluster. Pour l’exemple de la racine de cluster spécifiée dans ce didacticiel, la commande est `adl://hdiadlstore.azuredatalakestore.net/clusters/hdiadlcluster`. Vous pouvez utiliser l’alternative la plus courte ou fournir le chemin d’accès complet vers la racine du cluster.
    >

## <a name="access-data-lake-store-by-using-hdfs-commands"></a>Accéder à Data Lake Store avec les commandes HDFS
Une fois que vous avez configuré le cluster HDInsight pour qu’il utilise Data Lake Store, vous pouvez utiliser les commandes de l’interpréteur de commandes HDFS (Hadoop Distributed File System) pour accéder au magasin.

Dans cette section, vous établissez une connexion SSH au cluster HDInsight Linux que vous avez créé, puis vous exécutez les commandes HDFS.

* Si vous utilisez un client Windows pour établir la connexion SSH au cluster, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Si vous utilisez un client Linux pour établir la connexion SSH au cluster, consultez [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Une fois que vous avez établi la connexion, répertoriez les fichiers dans Data Lake Store en utilisant la commande HDFS suivante.

    hdfs dfs -ls adl:///

Vous pouvez également utiliser la commande `hdfs dfs -put` pour charger des fichiers dans Data Lake Store, puis utiliser `hdfs dfs -ls` pour vérifier si les fichiers ont été chargés avec succès.

## <a name="see-also"></a>Voir aussi
* [Utiliser Data Lake Store avec des clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portail Azure : Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
