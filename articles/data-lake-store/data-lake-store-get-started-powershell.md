---
title: "Prise en main d’Azure Data Lake Store à l’aide de PowerShell | Microsoft Docs"
description: "Utiliser Azure PowerShell pour créer un compte Data Lake Store et effectuer des opérations de base"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 31c98b930ccb8203316e3a3b13c3c0dc7d74dd9c
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Prise en main d'Azure Data Lake Store avec Azure PowerShell
> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

Apprenez à utiliser Azure PowerShell pour créer un compte Azure Data Lake Store et effectuer des opérations de base comme créer des dossiers, télécharger des fichiers de données, supprimer votre compte, etc. Pour plus d’informations sur Data Lake Store, consultez [Vue d’ensemble de Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Conditions préalables

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou version ultérieure**. Consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentification
Pour l’authentification auprès de Data Lake Store, cet article utilise une approche plus simple où vous êtes invité à entrer les informations d’identification de votre compte Azure. Le niveau d’accès au compte et au système de fichiers Data Lake Store est alors régi par le niveau d’accès de l’utilisateur connecté. Cependant, il existe d’autres approches pour l’authentification sur Data Lake Store, à savoir **l’authentification de l’utilisateur final** ou **l’authentification de service à service**. Pour obtenir des instructions et plus d’informations sur l’authentification, consultez [l’authentification de l’utilisateur final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [l’authentification de service à service](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte Azure Data Lake Store
1. Sur votre Bureau, ouvrez une nouvelle fenêtre Windows PowerShell. Entrez l’extrait de code suivant pour vous connecter à votre compte Azure, définir l’abonnement et inscrire le fournisseur Data Lake Store. Lorsque vous êtes invité à vous connecter, vérifiez que vous vous connectez en tant qu’administrateur/propriétaire de l’abonnement :

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Un compte Azure Data Lake Store est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Créer un groupe de ressources Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")
3. Créer un compte Azure Data Lake Store. Le nom que vous spécifiez doit contenir uniquement des lettres minuscules et des chiffres.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Créer un compte Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Créer un compte Azure Data Lake Store")
4. Vérifiez que le compte a bien été créé.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Le résultat de la cmdlet doit être **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Créer des structures de répertoires dans votre Azure Data Lake Store
Vous pouvez créer des répertoires sous votre compte Azure Data Lake Store pour gérer et stocker des données.

1. Spécifiez un répertoire racine.

        $myrootdir = "/"
2. Créez un répertoire appelé **mynewdirectory** sous la racine spécifiée.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Vérifiez que le répertoire a bien été créé.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Ceci devrait afficher un résultat, comme illustré sur la capture d’écran suivante :

    ![Vérifier le répertoire](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Vérifier le répertoire")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Télécharger des données sur votre Azure Data Lake Store
Vous pouvez télécharger vos données sur Data Lake Store directement à la racine ou dans un répertoire que vous avez créé dans le compte. Les extraits de code de cette section montrent comment télécharger des exemples de données dans le répertoire (**mynewdirectory**) que vous avez créé dans la section précédente.

Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Référentiel Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Téléchargez le fichier et stockez-le dans un répertoire local sur votre ordinateur, comme C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renommer, télécharger et supprimer des données de votre Data Lake Store
Utilisez la commande suivante pour renommer un fichier :

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Utilisez la commande suivante pour télécharger un fichier :

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Utilisez la commande suivante pour supprimer un fichier :

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quand vous y êtes invité, entrez **Y** pour supprimer l’élément. Si vous avez plusieurs fichiers à supprimer, vous pouvez fournir tous les chemins d'accès séparés par des virgules.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Supprimer votre compte Azure Data Lake Store
Utilisez la commande suivante pour supprimer votre compte Data Lake Store.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quand vous y êtes invité, entrez **Y** pour supprimer le compte.

## <a name="next-steps"></a>Étapes suivantes
* [Recommandations en matière d’optimisation des performances pour l’utilisation de PowerShell avec Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
* [Utiliser Azure Data Lake Store pour les données volumineuses](data-lake-store-data-scenarios.md) 
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)