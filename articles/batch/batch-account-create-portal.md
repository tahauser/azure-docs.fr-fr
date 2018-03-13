---
title: "Créer un compte Batch dans le portail Azure | Microsoft Docs"
description: "Apprenez à créer un compte Azure Batch dans le portail Azure pour exécuter des charges de travail parallèles à grande échelle dans le cloud"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5efe804806cb9c14a483e7393f0e8202897d53d6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Créer un compte Batch avec le portail Azure

> [!div class="op_single_selector"]
> * [Portail Azure](batch-account-create-portal.md)
> * [Gestion de lots .NET](batch-management-dotnet.md)
>
>

Apprenez à créer un compte Azure Batch dans le [portail Azure][azure_portal] et choisissez les propriétés du compte qui correspondent à votre scénario de calcul. Découvrez où se trouvent les propriétés du compte importantes, telles que les clés d’accès et les URL du compte.

Pour plus d’informations sur les comptes et les scénarios Batch, consultez la [présentation des fonctionnalités](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Création d’un compte Batch

> [!NOTE]
> Lorsque vous créez un compte Batch, vous devez généralement choisir le mode **Service Batch** par défaut. Les pools sont alloués en arrière-plan dans des abonnements gérés par Azure. Dans l’autre mode d’**abonnement utilisateur**, qui n’est plus recommandé pour la plupart des scénarios, les machines virtuelles Batch et les autres ressources sont créées directement dans l’abonnement pendant la création d’un pool. Pour créer un compte Batch dans le mode abonnement utilisateur, vous devez également inscrire votre abonnement auprès d’Azure Batch et associer le compte avec une Azure Key Vault.

1. Connectez-vous au [portail Azure][azure_portal].
2. Cliquez sur **Créer une ressource** et recherchez le **Service Batch** sur la Place de marché.

    ![Batch dans Marketplace][marketplace_portal]
3. Sélectionnez **Service Batch**, cliquez sur **créer**, puis saisissez les paramètres du **Nouveau compte Batch**. Consultez les informations suivantes :

    ![Création d’un compte Batch][account_portal]

    a. **Nom du compte** : le nom que vous choisissez doit être unique dans la région Azure dans laquelle le compte est créé (voir **Emplacement** ci-dessous). Le nom de compte peut contenir uniquement des caractères minuscules ou des chiffres et doit comporter 3 à 24 caractères.

    b. **Abonnement** : l’abonnement dans lequel créer le compte Batch. Si vous n’avez qu’un seul abonnement, il est sélectionné par défaut.

    c. **Mode d’allocation de pool** : si ce paramètre s’affiche, acceptez le **service Batch** par défaut.

    c. **Groupe de ressources** : sélectionnez un groupe de ressources pour votre nouveau compte Batch (vous pouvez également en créer un).

    d. **Emplacement** : la région Azure dans laquelle créer le compte Batch. Seules les régions prises en charge par votre abonnement et votre groupe de ressources sont affichées.

    e. **Compte de stockage** (facultatif) : compte de stockage Azure à usage général à associer à votre compte Batch. Cela est recommandé pour la plupart des comptes Batch. Pour plus d’informations, consultez la section [Compte Stockage Azure lié](#linked-azure-storage-account) disponible plus loin dans cet article.

4. Cliquez sur **Créer** pour créer le compte.



## <a name="view-batch-account-properties"></a>Afficher les propriétés du compte Batch
Une fois le compte créé, cliquez dessus pour accéder à ses propriétés et paramètres. Vous avez accès à tous les paramètres et propriétés du compte dans le menu de gauche.

![Page du compte Batch dans le portail Azure][account_blade]

* **URL de compte Batch** : lorsque vous développez une application avec des [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), vous avez besoin d’une URL de compte pour accéder aux ressources de votre Batch. Une URL de compte Batch a le format suivant :

    `https://<account_name>.<region>.batch.azure.com`

![URL de compte Batch dans le portail][account_url]

* **Clés d’accès** : pour authentifier l’accès à votre compte Batch à partir de votre application, vous pouvez utiliser une clé d’accès. Batch prend également en charge l’authentification Azure Active Directory.

    Pour afficher ou régénérer les clés d’accès, sélectionnez **Clés**.

    ![Clés de compte Batch dans le portail Azure][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Compte Azure Storage lié

Vous pouvez lier un compte de stockage Azure à usage général à votre compte Batch, ce qui est utile dans de nombreux scénarios. La fonctionnalité de [packages d’application](batch-application-packages.md) de Batch utilise le stockage Azure Blob Azure comme la bibliothèque [Batch File Conventions .NET](batch-task-output.md). Ces fonctionnalités facultatives vous aident à déployer les applications exécutées par vos tâches Batch et à conserver les données qu’elles produisent.

Nous vous recommandons de créer un compte de stockage dédié à votre compte Batch. Azure Batch prend actuellement en charge uniquement le type de compte de stockage à usage général. Ce type de compte est décrit à l’étape 5 [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) de l’article [À propos des comptes de stockage Azure](../storage/common/storage-create-storage-account.md).

![Créer un compte de stockage à usage général][storage_account]

> [!NOTE]
> Soyez prudent lorsque vous régénérez les clés d’accès d’un compte de stockage lié. Régénérez une seule clé de compte de stockage et cliquez sur **Synchroniser les clés** dans la page du compte de stockage lié. Attendez cinq minutes que les clés se propagent aux nœuds de calcul de vos pools, puis régénérez et synchronisez l’autre clé si nécessaire. Si vous régénérez les deux clés en même temps, les nœuds de calcul ne pourront pas les synchroniser, car elles perdront l’accès au compte de stockage.
>
>

![Régénération de clés de compte de stockage][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuration supplémentaire pour le mode d’abonnement utilisateur

Si vous choisissez de créer un compte Batch en mode d’abonnement utilisateur, suivez les étapes supplémentaires ci-dessous avant de créer le compte.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Autoriser Azure Batch à accéder à l’abonnement (opération ponctuelle)
Quand vous créez votre premier compte Batch en mode Abonnement utilisateur, vous devez inscrire votre abonnement auprès de Batch. (Si vous l’avez déjà fait, passez à la section suivante.)

1. Connectez-vous au [portail Azure][azure_portal].

2. Cliquez sur **Plus de services** > **Abonnements**, puis cliquez sur l’abonnement que vous souhaitez utiliser pour le compte Batch.

3. Dans la page **Abonnement**, cliquez sur **Contrôle d’accès (IAM)** > **Ajouter**.

    ![Contrôle d’accès à l’abonnement][subscription_access]

4. Dans la page **Ajouter des autorisations**, sélectionnez le rôle **Contributeur** et recherchez l’API Batch. Recherchez chacune de ces chaînes, jusqu’à ce que vous trouviez l’API :
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Les locataires Azure AD les plus récents peuvent utiliser ce nom.
    3. La chaîne **ddbf3205-c6bd-46ae-8127-60eb93363864** correspond à l’ID de l’API Batch. 

5. Une fois que vous avez trouvé l’API Batch, sélectionnez-la, puis cliquez sur **Enregistrer**.

    ![Ajouter des autorisations Batch][add_permission]

### <a name="create-a-key-vault"></a>Création d’un coffre de clés
En mode Abonnement utilisateur, un coffre de clés Azure appartenant au même groupe de ressources que le compte Batch est requis. Assurez-vous que le groupe de ressources se trouve dans une région prise en charge par votre abonnement et dans laquelle Batch est [disponible](https://azure.microsoft.com/regions/services/).

1. Dans le [portail Azure][azure_portal], cliquez sur **Nouveau** > **Sécurité et identité** > **Key Vault**.

2. Dans la page **Créer un coffre de clés**, entrez un nom pour le coffre de clés et créez un groupe de ressources dans la région souhaitée pour votre compte Batch. Conservez les autres paramètres par défaut, puis cliquez sur **Créer**.




## <a name="batch-service-quotas-and-limits"></a>Quotas et limites du service Batch
À l’image de votre abonnement Azure et d’autres services Azure, certains [quotas et limites](batch-quota-limit.md) s’appliquent aux comptes Batch. Les quotas actuels d’un compte Batch s’affichent dans **Quotas**.

![Quotas de compte Batch dans le portail Azure][quotas]



De plus, il est possible de relever la plupart de ces quotas en envoyant une demande de support produit gratuit dans le portail Azure. Pour plus d’informations sur les demandes d’augmentation de quotas, consultez [Quotas et limites pour le service Azure Batch](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Autres options de gestion de comptes Batch
Vous pouvez créer et gérer des comptes Batch à l’aide du portail Azure, mais aussi avec les éléments suivants :

* [Applets de commande PowerShell pour Batch](batch-powershell-cmdlets-get-started.md)
* [interface de ligne de commande Azure](batch-cli-get-started.md)
* [Gestion de lots .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les concepts et fonctionnalités du service Batch, consultez [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). L’article traite des principales ressources Batch, notamment des pools, des nœuds de calcul, des travaux et des tâches et fournit une vue d’ensemble des fonctionnalités du service pour l’exécution de charges de travail de calcul à grande échelle.
* Découvrez les bases du développement d’une application Batch à l’aide de la [bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md) ou [Python](batch-python-tutorial.md). Cette présentation vous décrit la création d’une application opérationnelle qui utilise le service Batch pour exécuter une charge de travail sur plusieurs nœuds de calcul, et inclut l’utilisation d’Azure Storage pour la mise en attente et la récupération de fichiers de la charge de travail.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Régénération de clés de compte de stockage"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png

