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
ms.openlocfilehash: 27b3775d81ec6dc093dae4ee46167c5d5a9c9e19
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>Prise en main de l’Explorateur de stockage (version préliminaire)
## <a name="overview"></a>Vue d'ensemble
L’Explorateur de stockage Azure (version préliminaire) est une application autonome qui vous permet d’utiliser facilement les données Stockage Azure sur Windows, macOS et Linux. Dans cet article, vous découvrez différentes façons de vous connecter à vos comptes de stockage Azure et de les gérer.

![Explorateur de stockage Microsoft Azure (version préliminaire)][0]

## <a name="prerequisites"></a>Prérequis
* [Télécharger et installer l’Explorateur de stockage (version préliminaire)](http://www.storageexplorer.com)

> [!NOTE]
> Pour les distributions Linux autres que Ubuntu 16.04, vous devrez peut-être installer manuellement quelques dépendances. En général, les packages suivants sont nécessaires :
> * libgconf-2-4
> * libsecret
> * GCC à jour
>
> En fonction de votre distribution, vous devrez peut-être installer d’autres packages. Les [Notes de publication](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) de l’Explorateur Stockage contiennent des étapes propres à certaines distributions.
>
>

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

1. Dans l’Explorateur Stockage (préversion), sélectionnez **Gérer les comptes** pour accéder au **panneau de gestion des comptes**.

    ![Gérer les comptes][1]

2. Le volet gauche affiche tous les comptes Azure auxquels vous vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte**

3. Si vous souhaitez vous connecter à un cloud national ou une pile Azure Stack, cliquez sur la liste déroulante **Environnement Azure** pour sélectionner le cloud Azure à utiliser. Lorsque vous avez choisi votre environnement, cliquez sur le bouton **Se connecter...**. Si vous vous connectez à Azure Stack, consultez [Connecter l’Explorateur Stockage à un abonnement Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md) pour plus d’informations.

    ![Option de connexion][2]

3. Une fois que vous êtes connecté avec un compte Azure, le volet gauche est renseigné avec le compte et les abonnements Azure associés à ce compte. Sélectionnez les abonnements Azure que vous souhaitez utiliser, puis sélectionnez **Appliquer** (sélectionnez **Tous les abonnements :** pour sélectionner/désélectionner la totalité ou aucun des abonnements Azure répertoriés).

    ![Sélectionner les abonnements Azure][3]

    Le volet gauche affiche les comptes de stockage associés aux abonnements Azure sélectionnés.

    ![Abonnements Azure sélectionnés][4]

## <a name="work-with-local-development-storage"></a>Utilisation du stockage de développement local
Avec l’Explorateur de stockage (version préliminaire), vous pouvez travailler sur le stockage local à l’aide de l’émulateur de stockage Azure. Cette approche vous permet de simuler l’utilisation du Stockage Azure sans nécessairement disposer d’un compte de stockage déployé sur Azure, étant donné que le compte de stockage est émulé par l’émulateur de stockage Azure.

> [!NOTE]
> Actuellement, l’émulateur de stockage Azure est pris en charge uniquement pour Windows.
>
>

> [!NOTE]
> L’émulateur de stockage Azure ne prend pas en charge les partages de fichiers.
>
>

1. Dans le volet de gauche de l’Explorateur Stockage (préversion), développez le nœud **(Local et attaché)** > **Comptes de stockage** > **(Développement)**  >  **Conteneurs d’objets blob**.

    ![Nœud de développement local][5]

2. Si vous n’avez pas encore installé l’émulateur de stockage Azure, vous êtes invité à le faire par le biais d’une barre d’informations. Si la barre d’informations s’affiche, sélectionnez **Télécharger la dernière version** et installez l’émulateur.

    ![Invite de téléchargement de l’émulateur de stockage Azure][6]

3. Une fois que l’émulateur est installé, vous pouvez créer et utiliser des tables, des files d’attente et des blobs locaux. Pour apprendre à utiliser chaque type de compte de stockage, consultez les guides suivants :

    * [Manage Azure blob storage resources (Gérer les ressources Azure Blob Storage)](vs-azure-tools-storage-explorer-blobs.md)
    * Manage Azure file share storage resources (Gérer les ressources de stockage de partage de fichiers Azure) - *Bientôt disponible*
    * Manage Azure queue storage resources (Gérer les ressources de stockage File d’attente Azure) - *Bientôt disponible*
    * Manage Azure table storage resources (Gérer les ressources de stockage Table Azure) - *Bientôt disponible*

## <a name="attach-or-detach-an-external-storage-account"></a>Attacher ou détacher un compte de stockage externe
L’Explorateur de stockage (version préliminaire) vous permet d’effectuer un attachement à des comptes de stockage externes, pour un partage simplifié des comptes de stockage. Cette section explique la procédure d’attachement à des comptes de stockage externes (et la procédure de détachement).

### <a name="get-the-storage-account-credentials"></a>Obtention des informations d’identification du compte de stockage
Pour partager un compte de stockage externe, le propriétaire du compte doit d’abord obtenir les informations d’identification du compte (nom et clé du compte), puis les partager avec les personnes souhaitant effectuer un attachement à ce compte. Vous pouvez obtenir les informations d’identification du compte de stockage via le portail Azure en suivant ces étapes :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Parcourir**.

3. Sélectionnez **Comptes de stockage**.

4. Dans la liste **Comptes de stockage**, sélectionnez le compte de stockage souhaité.

5. Sous **Paramètres**, sélectionnez **Clés d’accès**.

    ![Option Clés d’accès][7]

6. Copiez le **nom de compte de stockage** et la clé **key1**.

    ![Clés d’accès][8]

### <a name="attach-to-an-external-storage-account"></a>Attachement à un compte de stockage externe
Pour attacher à un compte de stockage externe, vous avez besoin du nom et de la clé du compte. La section « Obtention des informations d’identification du compte de stockage » explique comment obtenir ces valeurs à partir du portail Azure. Toutefois, dans le portail, la clé du compte est appelée **key1**. Par conséquent, lorsque l’Explorateur Stockage (préversion) demande une clé de compte, vous entrez la valeur **key1**.

1. Dans l’Explorateur Stockage (préversion), ouvrez la **boîte de dialogue de connexion**.

    ![Option Se connecter à Azure Storage][9]

2. Dans la **boîte de dialogue de connexion**, choisissez **Utiliser le nom et la clé d’un compte de stockage**

    ![Option Ajouter avec le nom et la clé][10]

3. Collez le nom de votre compte dans la zone de texte **Nom du compte**, puis collez la clé de votre compte (valeur **key1** du portail Azure) dans la zone de texte **Clé du compte**, puis sélectionnez **Suivant**.

    ![Page Nom et clé][11]

    > [!NOTE]
    > Pour utiliser un nom et une clé à partir d’un cloud national, utilisez la liste déroulante **Domaine des points de terminaison de stockage :** pour sélectionner le domaine des points de terminaison approprié : 
    >
    >

4. Vérifiez les informations de la boîte de dialogue **Résumé de connexion**. Si vous souhaitez modifier quoi que ce soit, sélectionnez **Précédent** et saisissez de nouveau les paramètres souhaités. 

5. Sélectionnez **Connecter**.

6. Une fois le compte de stockage attaché, il s’affiche avec **(Externe)** ajouté à son nom.

    ![Résultat de la connexion à un compte de stockage externe][12]

### <a name="detach-from-an-external-storage-account"></a>Détachement d’un compte de stockage externe
1. Cliquez avec le bouton droit sur le compte de stockage externe que vous souhaitez détacher, puis sélectionnez **Détacher**.

    ![Option Détacher d’un compte de stockage][13]

2. Cliquez sur **Oui** dans le message de confirmation pour confirmer le détachement du compte de stockage externe.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Attacher un compte de stockage à l’aide d’une signature d’accès partagé (SAP)
Une signature d’accès partagé ou [SAP](storage/common/storage-dotnet-shared-access-signature-part-1.md) permet à l’administrateur d’un abonnement Azure d’accorder un accès temporaire à un compte de stockage sans avoir à fournir des informations d’identification pour l’abonnement Azure.

Pour illustrer ce scénario, supposons que l’utilisateur A est l’administrateur d’un abonnement Azure et qu’il souhaite autoriser l’utilisateur B à accéder à un compte de stockage pour une durée limitée, avec certaines autorisations :

1. L’utilisateur A génère une chaîne de connexion SAP pour une période spécifique, avec les autorisations souhaitées.

2. L’utilisateur A partage la SAP avec la personne souhaitant accéder au compte de stockage, en l’occurrence l’utilisateur B.  

3. L’utilisateur B utilise l’Explorateur de stockage (version préliminaire) pour effectuer l’attachement au compte appartenant à l’utilisateur A à l’aide de la SAP fournie.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Générer une chaîne de connexion SAP pour le compte à partager
1. Dans l’Explorateur Stockage (préversion), cliquez avec le bouton droit sur le compte de stockage que vous souhaitez partager, puis sélectionnez **Obtenir une signature d’accès partagé...**.

    ![Option de menu contextuel Obtenir une signature d’accès partagé][14]

2. Dans la boîte de dialogue **Générer la signature d’accès partagé**, spécifiez la période et les autorisations souhaitées pour le compte, puis cliquez sur le bouton **Créer**.

    ![Boîte de dialogue Obtenir une signature d’accès partagé][15]  

3. Sélectionnez **Copier** en regard de la zone de texte **Chaîne de connexion** pour la copier dans le Presse-papiers, puis cliquez sur **Fermer**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Attacher à un compte de stockage à l’aide d’une chaîne de connexion SAP
1. Dans l’Explorateur Stockage (préversion), ouvrez la **boîte de dialogue de connexion**.

    ![Option Se connecter à Azure Storage][9]

2. Dans la **boîte de dialogue de connexion**, choisissez **Utiliser un URI de chaîne de connexion ou de signature d’accès partagé** puis cliquez sur **Suivant**.

    ![Boîte de dialogue Connexion au stockage Azure][16]

3. Choisissez **Utiliser une chaîne de connexion** et collez votre chaîne de connexion dans le champ **Chaîne de connexion :**. Cliquez sur le bouton **Suivant**.

    ![Boîte de dialogue Connexion au stockage Azure][17]

4. Vérifiez les informations de la boîte de dialogue **Résumé de connexion**. Pour apporter des modifications, sélectionnez **Précédent**, puis entrez les paramètres souhaités. 

5. Sélectionnez **Connecter**.

6. Une fois le compte de stockage attaché, il s’affiche avec **(SAP)** ajouté à son nom.

    ![Résultat de l’attachement à un compte à l’aide d’une SAP][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Attacher un service à l’aide d’une signature d’accès partagé (SAP)
La section « Attachement d’un compte de stockage à l’aide d’une SAP » illustre la façon dont l’administrateur d’un abonnement Azure peut accorder un accès temporaire à un compte de stockage en générant et en partageant une SAP pour le compte de stockage. De la même façon, une SAP peut être générée pour un service spécifique (conteneur d’objets blob, file d’attente, table ou partage de fichier) dans un compte de stockage.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Génération d’une SAP pour le service à partager
Dans ce contexte, un service peut être un conteneur d’objets blob, une file d’attente, une table ou un partage de fichier. Pour générer la SAP d’un service répertorié, consultez :

* [Obtenir la signature d’accès partagé pour un conteneur d’objets blob](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Get the SAS for a file share (Obtenir la signature d’accès partagé pour un partage de fichiers) - *Bientôt disponible*
* Get the SAS for a queue (Obtenir la signature d’accès partagé pour une file d’attente) - *Bientôt disponible*
* Get the SAS for a table (Obtenir la signature d’accès partagé pour une table) - *Bientôt disponible*

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Attachement au service de compte partagé à l’aide d’un URI SAP
1. Dans l’Explorateur Stockage (préversion), ouvrez la **boîte de dialogue de connexion**.

    ![Option Se connecter à Azure Storage][9]

2. Dans la **boîte de dialogue de connexion**, choisissez **Utiliser un URI de chaîne de connexion ou de signature d’accès partagé** puis cliquez sur **Suivant**.

    ![Boîte de dialogue Connexion au stockage Azure][16]

3. Choisissez **Utiliser un URI SAP** et collez votre URI dans le champ **URI :**. Cliquez sur le bouton **Suivant**.

    ![Boîte de dialogue Connexion au stockage Azure][19]

3. Vérifiez les informations de la boîte de dialogue **Résumé de connexion**. Pour apporter des modifications, sélectionnez **Précédent**, puis entrez les paramètres souhaités. 

4. Sélectionnez **Connecter**.

5. Une fois le service attaché, il s’affiche sous le nœud **(SAS-Attached Services)** (Services attachés via SAP).

    ![Résultat de l’attachement à un service partagé à l’aide d’une SAP][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connexion à un compte Azure Cosmos DB à l’aide d’une chaîne de connexion
En plus de gérer des comptes Azure Cosmos DB par le biais d’un abonnement Azure, une autre façon de se connecter à un compte Azure Cosmos DB consiste à utiliser une chaîne de connexion. Utilisez les étapes suivantes pour vous connecter à l’aide d’une chaîne de connexion.

1. Rechercher **Locaux et joints** dans l’arborescence de gauche, cliquez avec le bouton droit sur **Comptes Azure Cosmos DB**, choisissez **Se connecter à Azure Cosmos DB...**

    ![connexion à Azure Cosmos DB avec une chaîne de connexion][21]

2. Choisissez l’API Azure Cosmos DB, collez votre **chaîne de connexion**, puis cliquez sur **OK** pour vous connecter au compte Azure Cosmos DB. Pour plus d’informations sur la récupération de la chaîne de connexion, consultez [Obtenir la chaîne de connexion](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

 ## <a name="connect-to-azure-data-lake-store-by-uri"></a>Se connecter à Azure Data Lake Store par URI
Vous souhaitez accéder à des ressources, qui n’existent pas dans votre abonnement, mais d’autres personnes vous permettent d’accéder à l’URI de ces ressources. Le cas échéant, vous pouvez vous connecter à Data Lake Store à l’aide de l’URI une fois que vous êtes connecté. Procédez comme suit.
1. Ouvrez l’Explorateur de stockage (version préliminaire).
2. Dans le volet gauche, développez **Local and Attached** (Local et attaché).
3. Cliquez avec le bouton droit sur **Data Lake Store**, puis, dans le menu contextuel, sélectionnez **Connect to Data Lake Store...** (Se connecter à Data Lake Store...).

    ![menu contextuel de connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Entrez l’URI. L’outil accède alors à l’emplacement de l’URL que vous venez d’entrer.

    ![boîte de dialogue contextuelle de connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![résultat de la connexion à Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>Recherche de comptes de stockage
Si vous avez besoin de rechercher une ressource de stockage et ne savez pas où elle se trouve, vous pouvez utiliser la zone de recherche en haut du volet gauche pour rechercher la ressource.

À mesure que vous tapez dans la zone de recherche, le volet gauche affiche toutes les ressources qui correspondent à la valeur en cours de saisie. Par exemple, la recherche de **points de terminaison** est indiquée dans la capture d’écran suivante :

![Recherche de compte de stockage][23]

> [!NOTE]
> Utilisez le **panneau de gestion des comptes** pour désélectionner tous les abonnements qui ne contiennent pas l’élément que vous recherchez afin d’améliorer le temps d’exécution de la recherche. Vous pouvez également cliquer avec le bouton droit sur un nœud et choisir **Rechercher à partir d’ici** pour démarrer la recherche à partir d’un nœud spécifique.
>
>

## <a name="next-steps"></a>étapes suivantes
* [Gérer les ressources de Stockage Blob Azure avec l’Explorateur de stockage (version préliminaire)](vs-azure-tools-storage-explorer-blobs.md)
* [Gérer Azure Cosmos DB dans l’Explorateur Stockage Azure (préversion)](./cosmos-db/storage-explorer.md)
* [Gérer les ressources Azure Data Lake Store avec l’Explorateur Stockage (préversion)](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/DevelopmentNode.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/EmulatorNotInstalled.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
