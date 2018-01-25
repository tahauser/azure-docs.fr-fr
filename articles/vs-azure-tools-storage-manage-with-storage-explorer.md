---
title: "Prise en main de l’Explorateur de stockage (version préliminaire) | Microsoft Docs"
description: "Gérer les ressources de stockage Azure avec l’Explorateur de stockage (version préliminaire)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: cccab530e86373fee8a78b42c8cba532b05c1bab
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>Prise en main de l’Explorateur de stockage (version préliminaire)
## <a name="overview"></a>Vue d’ensemble
L’Explorateur de stockage Azure (version préliminaire) est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux. Dans cet article, vous découvrez les différentes façons de vous connecter à vos comptes de stockage Azure et de les gérer.

![Explorateur de stockage Microsoft Azure (version préliminaire)][15]

## <a name="prerequisites"></a>configuration requise
* [Télécharger et installer l’Explorateur de stockage (version préliminaire)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Connexion à un service ou un compte de stockage
L’Explorateur de stockage (version préliminaire) offre de nombreuses façons de se connecter à des comptes de stockage. Vous pouvez par exemple :
* Vous connecter à des comptes de stockage associés à vos abonnements Azure.
* Vous connecter à des comptes de stockage et à des services partagés à partir d’autres abonnements Azure.
* Vous connecter au stockage local et le gérer à l’aide de l’émulateur de stockage Azure. 

En outre, vous pouvez utiliser des comptes de stockage Azure à l’échelle internationale et nationale :

* [Connexion à un abonnement Azure](#connect-to-an-azure-subscription) : gérez les ressources de stockage appartenant à votre abonnement Azure.
* [Utilisation du stockage de développement local](#work-with-local-development-storage) : gérez le stockage local à l’aide de l’émulateur de stockage Azure.
* [Attachement au stockage externe](#attach-or-detach-an-external-storage-account) : gérez les ressources de stockage qui appartiennent à un autre abonnement Azure ou qui sont dans des clouds Azure nationaux en utilisant les points de terminaison, la clé et le nom du compte de stockage.
* [Attachement d’un compte de stockage à l’aide d’une SAP](#attach-storage-account-using-sas) : gérez les ressources de stockage qui appartiennent à un autre abonnement Azure à l’aide d’une signature d’accès partagé (SAP).
* [Attachement d’un service à l’aide d’une SAP](#attach-service-using-sas) : gérez un service de stockage spécifique (conteneur de blobs, file d’attente ou table) qui appartient à un autre abonnement Azure à l’aide d’une SAP.
* [Connexion à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string) : gérez un compte Azure Cosmos DB à l’aide d’une chaîne de connexion.

## <a name="connect-to-an-azure-subscription"></a>Connexion à un abonnement Azure
> [!NOTE]
> Si vous ne possédez pas de compte Azure, vous pouvez [vous inscrire à un essai gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez les **paramètres de compte Azure**.

    ![paramètres de compte Azure][0]

2. Le volet gauche affiche tous les comptes Microsoft auxquels vous vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte** et suivez les indications pour vous connecter avec un compte Microsoft associé à un ou plusieurs abonnements Azure actifs.

    >[!NOTE]
    >La connexion aux comptes de stockage Azure nationaux (Azure Germany, Azure Government et Azure China par le biais d’une connexion) n’est pas prise en charge pour l’instant. Pour plus d’informations sur la procédure de connexion aux comptes de stockage Azure nationaux, consultez la section « Attacher ou détacher un compte de stockage externe ».

3. Après vous être connecté avec un compte Microsoft, le volet gauche est renseigné avec les abonnements Azure associés à ce compte. Sélectionnez les abonnements Azure que vous souhaitez utiliser, puis sélectionnez **Appliquer**. (La case à cocher **Tous les abonnements** permet de sélectionner ou de désélectionner l’ensemble des abonnements Azure répertoriés.)

    ![Sélectionner les abonnements Azure][3]  
    Le volet gauche affiche les comptes de stockage associés aux abonnements Azure sélectionnés.

    ![Abonnements Azure sélectionnés][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Connexion à un abonnement Azure Stack

Pour en savoir plus sur la connexion à un abonnement Azure Stack, consultez [Connecter l’Explorateur de stockage à un abonnement Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md).

## <a name="work-with-local-development-storage"></a>Utilisation du stockage de développement local
Avec l’Explorateur de stockage (version préliminaire), vous pouvez travailler sur le stockage local à l’aide de l’émulateur de stockage Azure. Cette approche vous permet d’écrire du code pour le stockage et de le tester sans nécessairement disposer d’un compte de stockage déployé sur Azure, étant donné que le compte de stockage est émulé par l’émulateur de stockage Azure.

> [!NOTE]
> Actuellement, l’émulateur de stockage Azure est pris en charge uniquement pour Windows.
>
>

1. Dans le volet gauche de l’Explorateur de stockage (version préliminaire), développez le nœud **(Local et attaché)** > **Comptes de stockage** > **(Développement)**.

    ![Nœud de développement local][21]

2. Si vous n’avez pas encore installé l’émulateur de stockage Azure, vous êtes invité à le faire par le biais d’une barre d’informations. Si la barre d’informations s’affiche, sélectionnez **Télécharger la dernière version** et installez l’émulateur.

    ![Invite de téléchargement de l’émulateur de stockage Azure][22]

3. Une fois que l’émulateur est installé, vous pouvez créer et utiliser des tables, des files d’attente et des blobs locaux. Pour apprendre à utiliser chaque type de compte de stockage, consultez l’un des liens suivants :

    * [Manage Azure blob storage resources (Gérer les ressources Azure Blob Storage)](vs-azure-tools-storage-explorer-blobs.md)
    * Manage Azure file share storage resources (Gérer les ressources de stockage de partage de fichiers Azure) - *Bientôt disponible*
    * Manage Azure queue storage resources (Gérer les ressources de stockage File d’attente Azure) - *Bientôt disponible*
    * Manage Azure table storage resources (Gérer les ressources de stockage Table Azure) - *Bientôt disponible*

## <a name="attach-or-detach-an-external-storage-account"></a>Attacher ou détacher un compte de stockage externe
L’Explorateur de stockage (version préliminaire) vous permet d’effectuer un attachement à des comptes de stockage externes, pour un partage simplifié des comptes de stockage. Cette section explique la procédure d’attachement à des comptes de stockage externes (et la procédure de détachement).

### <a name="get-the-storage-account-credentials"></a>Obtention des informations d’identification du compte de stockage
Pour partager un compte de stockage externe, le propriétaire du compte doit d’abord obtenir les informations d’identification du compte (nom et clé du compte), puis les partager avec les personnes souhaitant effectuer un attachement à ce compte (externe). Vous pouvez obtenir les informations d’identification du compte de stockage via le portail Azure en suivant ces étapes :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Parcourir**.

3. Sélectionnez **Comptes de stockage**.

4. Dans le panneau **Comptes de stockage**, sélectionnez le compte de stockage souhaité.

5. Dans le panneau **Paramètres** du compte de stockage sélectionné, sélectionnez **Clés d’accès**.

    ![Option Clés d’accès][5]

6. Dans le panneau **Clés d’accès**, copiez les valeurs **Nom du compte de stockage** et **key1** à utiliser pour l’attachement au compte de stockage.

    ![Clés d’accès][6]

### <a name="attach-to-an-external-storage-account"></a>Attachement à un compte de stockage externe
Pour attacher à un compte de stockage externe, vous avez besoin du nom et de la clé du compte. La section « Obtention des informations d’identification du compte de stockage » explique comment obtenir ces valeurs à partir du portail Azure. Toutefois, dans le portail, la clé du compte est appelée **key1**. Par conséquent, lorsque l’Explorateur de stockage (version préliminaire) demande une clé de compte, vous entrez la valeur **key1**.

1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

    ![Option Se connecter à Azure Storage][23]

2. Dans la boîte de dialogue **Connexion au stockage Azure**, spécifiez la clé de compte (la valeur **key1** du portail Azure), puis sélectionnez **Suivant**.

    > [!NOTE]
    > Vous pouvez entrer la chaîne de connexion de stockage appartenant à un compte de stockage Azure national. Par exemple, pour vous connecter à des comptes de stockage Azure Germany, entrez des chaînes de connexion semblables à ce qui suit : 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<storage_account_key>
    >* EndpointSuffix=core.cloudapi.de
    
    >Vous pouvez obtenir la chaîne de connexion à partir du portail Azure en suivant les instructions de la section « Obtention des informations d’identification du compte de stockage ».

    ![Boîte de dialogue Connexion au stockage Azure][24]

3. Dans la boîte de dialogue **Attacher un stockage externe**, entrez le nom du compte de stockage dans la zone **Nom du compte**, spécifiez tout autre paramètre souhaité, puis sélectionnez **Suivant**.

    ![Boîte de dialogue Attacher un stockage externe][8]

4. Vérifiez les informations de la boîte de dialogue **Résumé de connexion**. Si vous souhaitez modifier quoi que ce soit, sélectionnez **Précédent** et saisissez de nouveau les paramètres souhaités. 

5. Sélectionnez **Connecter**.

6. Une fois connecté, le compte de stockage externe s’affiche avec **(Externe)** ajouté au nom.

    ![Résultat de la connexion à un compte de stockage externe][9]

### <a name="detach-from-an-external-storage-account"></a>Détachement d’un compte de stockage externe
1. Cliquez avec le bouton droit sur le compte de stockage externe que vous souhaitez détacher, puis sélectionnez **Détacher**.

    ![Option Détacher d’un compte de stockage][10]

2. Cliquez sur **Oui** dans le message de confirmation pour confirmer le détachement du compte de stockage externe.

## <a name="attach-a-storage-account-by-using-an-sas"></a>Attachement d’un compte de stockage à l’aide d’une SAP
Une [SAP](storage/common/storage-dotnet-shared-access-signature-part-1.md) permet à l’administrateur d’un abonnement Azure d’accorder un accès temporaire à un compte de stockage sans avoir à fournir des informations d’identification pour l’abonnement Azure.

Pour illustrer ce scénario, supposons que l’utilisateur A est l’administrateur d’un abonnement Azure et qu’il souhaite autoriser l’utilisateur B à accéder à un compte de stockage pour une durée limitée, avec certaines autorisations :

1. L’utilisateur A génère une SAP (composée de la chaîne de connexion du compte de stockage) pour une période spécifique, avec les autorisations souhaitées.

2. L’utilisateur A partage la SAP avec la personne souhaitant accéder au compte de stockage, en l’occurrence l’utilisateur B.  

3. L’utilisateur B utilise l’Explorateur de stockage (version préliminaire) pour effectuer l’attachement au compte appartenant à l’utilisateur A à l’aide de la SAP fournie.

### <a name="get-an-sas-for-the-account-you-want-to-share"></a>Obtention d’une SAP pour le compte à partager
1. Dans l’Explorateur de stockage (version préliminaire), cliquez avec le bouton droit sur le compte de stockage que vous souhaitez partager, puis sélectionnez **Obtenir une signature d’accès partagé**.

    ![Option de menu contextuel Obtenir une signature d’accès partagé][13]

2. Dans la boîte de dialogue **Signature d’accès partagé**, spécifiez la période et les autorisations souhaitées pour le compte, puis sélectionnez **Créer**.

    ![Boîte de dialogue Obtenir une signature d’accès partagé][14]  
    La seconde boîte de dialogue **Signature d’accès partagé** s’ouvre et affiche la SAP.

3. Sélectionnez **Copier** en regard de la **Chaîne de connexion** pour la copier dans le Presse-papiers, puis sélectionnez **Fermer**.

### <a name="attach-to-the-shared-account-by-using-the-sas"></a>Attachement au compte partagé à l’aide de la SAP
1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

    ![Option Se connecter à Azure Storage][23]

2. Dans la boîte de dialogue **Connexion au stockage Azure**, spécifiez la chaîne de connexion, puis sélectionnez **Suivant**.

    ![Boîte de dialogue Connexion au stockage Azure][24]

3. Vérifiez les informations de la boîte de dialogue **Résumé de connexion**. Pour apporter des modifications, sélectionnez **Précédent**, puis entrez les paramètres souhaités. 

4. Sélectionnez **Connecter**.

5. Une fois attaché, le compte de stockage s’affiche avec **(SAP)** ajouté au nom fourni.

    ![Résultat de l’attachement à un compte à l’aide d’une SAP][17]

## <a name="attach-a-service-by-using-an-sas"></a>Attacher un service à l’aide d’une SAP
La section « Attachement d’un compte de stockage à l’aide d’une SAP » illustre la façon dont l’administrateur d’un abonnement Azure peut accorder un accès temporaire à un compte de stockage en générant et en partageant une SAP pour le compte de stockage. De la même façon, une SAP peut être générée pour un service spécifique (conteneur de blobs, file d’attente ou table) dans un compte de stockage.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Génération d’une SAP pour le service à partager
Dans ce contexte, un service peut être un conteneur d’objets blob, une file d’attente ou une table. Pour générer la SAP d’un service répertorié, consultez :

* [Obtenir la signature d’accès partagé pour un conteneur d’objets blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Get the SAS for a file share (Obtenir la signature d’accès partagé pour un partage de fichiers) - *Bientôt disponible*
* Get the SAS for a queue (Obtenir la signature d’accès partagé pour une file d’attente) - *Bientôt disponible*
* Get the SAS for a table (Obtenir la signature d’accès partagé pour une table) - *Bientôt disponible*

### <a name="attach-to-the-shared-account-service-by-using-the-sas"></a>Attachement au service de compte partagé à l’aide de la SAP
1. Dans l’Explorateur de stockage (version préliminaire), sélectionnez **Se connecter à Azure Storage**.

    ![Option Se connecter à Azure Storage][23]

2. Dans la boîte de dialogue **Connexion au stockage Azure**, spécifiez l’URI de SAP, puis sélectionnez **Suivant**.

    ![Boîte de dialogue Connexion au stockage Azure][24]

3. Vérifiez les informations de la boîte de dialogue **Résumé de connexion**. Pour apporter des modifications, sélectionnez **Précédent**, puis entrez les paramètres souhaités. 

4. Sélectionnez **Connecter**.

5. Une fois attaché, le nouveau service s’affiche sous le nœud **(Service SAS)** (SAP de service).

    ![Résultat de l’attachement à un service partagé à l’aide d’une SAP][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connexion à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion
En plus de gérer des comptes Azure Cosmos DB par le biais d’un abonnement Azure, une autre façon de se connecter à un compte Azure Cosmos DB consiste à utiliser une chaîne de connexion. Utilisez les étapes suivantes pour vous connecter à l’aide d’une chaîne de connexion.

1. Rechercher **Locaux et joints** dans l’arborescence de gauche, cliquez avec le bouton droit sur **Comptes Azure Cosmos DB**, choisissez **Se connecter à Azure Cosmos DB...**

    ![connexion à Azure Cosmos DB avec une chaîne de connexion][33]

2. Choisissez l’API Azure Cosmos DB, collez votre **chaîne de connexion**, puis cliquez sur **OK** pour vous connecter au compte Azure Cosmos DB. Pour plus d’informations sur la récupération de la chaîne de connexion, consultez [Obtenir la chaîne de connexion](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][32]

## <a name="search-for-storage-accounts"></a>Recherche de comptes de stockage
Si vous avez une longue liste de comptes de stockage, la zone de recherche située en haut du volet gauche offre un moyen rapide de rechercher un compte spécifique.

À mesure que vous tapez dans la zone de recherche, le volet gauche affiche les comptes de stockage qui correspondent à la valeur en cours de saisie. Par exemple, une recherche de tous les comptes de stockage dont le nom contient **tarcher** est illustrée dans la capture d’écran suivante :

![Recherche de compte de stockage][11]

## <a name="next-steps"></a>étapes suivantes
* [Gérer les ressources de Stockage Blob Azure avec l’Explorateur de stockage (version préliminaire)](vs-azure-tools-storage-explorer-blobs.md)
* [Gérer Azure Cosmos DB dans l’Explorateur Stockage Azure (préversion)](./cosmos-db/storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png
[32]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.PNG
[33]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.PNG
