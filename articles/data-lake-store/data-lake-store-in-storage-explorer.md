---
title: "Gérer les ressources Azure Data Lake Store dans l’Explorateur Stockage Azure"
description: "Permet aux utilisateurs d’accéder aux données et ressources ADLS et de les gérer dans l’Explorateur Stockage Azure"
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: 
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 4c69acebceac6719cf3f12ff0a93879f1b5a8943
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-data-lake-store-resources-with-storage-explorer-preview"></a>Gérer les ressources Azure Data Lake Store avec l’Explorateur Stockage (préversion)

[Azure Data Lake Store (ADLS)](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) est un service destiné à stocker de grandes quantités de données non structurées, telles que des données de texte ou binaires. Les utilisateurs peuvent accéder aux données en tout lieu via HTTP ou HTTPS. Dans l’Explorateur Stockage Azure, ADLS permet aux utilisateurs d’accéder aux ressources et aux données ADLS et de les gérer. Il en va de même avec d’autres entités Azure telles que les objets blob et file d’attente. À présent, les utilisateurs peuvent se servir du même outil pour gérer leurs différentes entités Azure au même endroit.

L’un des autres avantages est que les utilisateurs n’ont pas besoin de posséder une autorisation d’abonnement pour gérer les données ADLS. Dans l’Explorateur Stockage, les utilisateurs peuvent attacher le chemin d’accès ADLS au nœud « Local et attaché» tant que d’autres utilisateurs leur accordent l’autorisation.

## <a name="prerequisites"></a>configuration requise
Pour effectuer les étapes indiquées dans cet article, vous avez besoin des éléments requis suivants :

*   Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial).
*   Un compte Azure Data Lake Store. Pour savoir comment en créer un, consultez [Prise en main d’Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="installation"></a>Installation

Installez la dernière version de l’Explorateur Stockage Azure ici : [Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/). Maintenant, nous prenons en charge les versions Windows, Linux et MAC.

## <a name="connect-to-an-azure-subscription"></a>Connexion à un abonnement Azure

1. Après avoir installé l’**Explorateur Stockage Azure**, cliquez sur l’icône de **plug-in** à gauche, comme illustré dans l’image suivante.
       
   ![Icône de plug-in](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Sélectionnez **Ajouter un compte Azure**, puis cliquez sur **Connexion**.

   ![Se connecter à un abonnement Azure](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. Dans la boîte de dialogue **Connexion à Azure**, sélectionnez **Se connecter** et entrez vos informations d’identification Azure.

    ![Se connecter](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Sélectionnez votre abonnement dans la liste, puis cliquez sur **Appliquer**.

    ![Appliquer](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Le volet Explorateur se met à jour et affiche les comptes de l’abonnement sélectionné.

    ![Liste de comptes](./media/data-lake-store-in-storage-explorer/account-list.png)

    Vous êtes connecté à votre instance **Azure Data Lake Store** dans votre abonnement Azure.

## <a name="connect-to-data-lake-store"></a>Connexion à Data Lake Store
Vous souhaitez accéder à des ressources, qui n’existent pas dans votre abonnement, mais d’autres personnes vous permettent d’accéder à l’URI de ces ressources. Le cas échéant, vous pouvez vous connecter à Data Lake Store à l’aide de l’URI une fois que vous êtes connecté. Procédez comme suit.
1. Ouvrez l’Explorateur de stockage (version préliminaire).
2. Dans le volet gauche, développez **Local and Attached** (Local et attaché).
3. Cliquez avec le bouton droit sur **Data Lake Store**, puis, dans le menu contextuel, sélectionnez **Connect to Data Lake Store...** (Se connecter à Data Lake Store...).

      ![menu contextuel de connexion à Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Entrez l’URI. L’outil accède alors à l’emplacement de l’URL que vous venez d’entrer.

      ![boîte de dialogue contextuelle de connexion à Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![résultat de la connexion à Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Afficher le contenu d’un compte Azure Data Lake Store
Les ressources d’un compte Azure Data Lake Store contiennent des fichiers et dossiers.

La procédure suivante indique comment afficher le contenu d’un compte ADLS dans l’Explorateur Stockage (préversion) :

1. Ouvrez l’Explorateur de stockage (version préliminaire).
2. Dans le volet gauche, développez l’abonnement qui contient le compte Azure Data Lake Store que vous souhaitez afficher.
3. Développez **Data Lake Store**.
4. Cliquez avec le bouton droit sur le nœud du compte Azure Data Lake Store que vous souhaitez afficher, puis, dans le menu contextuel, sélectionnez **Ouvrir**. Vous pouvez également double-cliquer sur le compte ADLS à ouvrir. 
5. Le volet principal affiche le contenu du compte ADLS.

     ![volet principal](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="azure-data-lake-store-resources-management"></a>Gestion des ressources Azure Data Lake Store

Vous pouvez gérer les ressources Azure Data Lake Store en effectuant les opérations suivantes :
*   accéder aux ressources ADLS de plusieurs comptes ADL ;  
*   utiliser une chaîne de connexion pour se connecter à ADLS et le gérer directement ; 
*   afficher les ressources ADLS partagées par d’autres utilisateurs via ACL dans Local & Attached (Local et attaché) ;
*   effectuer des opérations CRUD sur fichier/dossier : prise en charge de fichiers à sélection multiple et de dossiers récursifs ; 
*   faire glisser, déplacer et ajouter des dossiers pour bénéficier d’un accès rapide et aux emplacements récents, comme dans l’Explorateur de fichiers du Bureau ; 
*   copier et ouvrir le lien hypertexte ADL en un clic avec l’Explorateur Stockage ; 
*   afficher le journal d’activité dans le volet inférieur droit pour afficher l’état de l’activité ;
*   afficher la propriété de fichier et les statistiques des dossiers.

## <a name="manage-resources-in-azure-storage-explorer"></a>Gérer les ressources dans l’Explorateur Stockage Azure
Une fois que vous avez créé un compte Azure Data Lake Store, vous pouvez charger des dossiers et fichiers, procéder à des téléchargements et ouvrir des ressources sur votre ordinateur local. Vous pouvez également cliquer sur Épingler sur Accès rapide, Nouveau dossier, Copier l’URL et Sélectionner tout. En outre, vous pouvez copier, coller, renommer, supprimer et actualiser une sélection, et accéder aux statistiques des dossiers.

Les éléments suivants indiquent comment gérer les ressources au sein d’un compte Azure Data Lake Store. Suivez ces étapes en fonction de la tâche que vous souhaitez effectuer :
   * **Charger des fichiers**

     1. Dans la barre d’outils du volet principal, sélectionnez **Charger**, puis **Charger des fichiers...** dans le menu déroulant.

        ![Télécharger des fichiers - Menu](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

     2. Dans la boîte de dialogue **Sélectionner les fichiers à charger**, sélectionnez les fichiers à charger.

        ![Charger un dossier - Boîte de dialogue](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

     3. Sélectionnez **Ouvrir** pour commencer le chargement.
   * **Charger un dossier**

     1. Dans la barre d’outils du volet principal, sélectionnez **Charger**, puis **Upload Folder...** (Charger un dossier...) dans le menu déroulant.

        ![Télécharger un dossier - Menu](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     2. Dans la boîte de dialogue **Select folder to Upload** (Sélectionner le dossier à charger), sélectionnez le dossier à charger.

        ![Charger un dossier - Boîte de dialogue](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

     3. Sélectionnez **Sélectionner un dossier** pour commencer le chargement des dossiers.

        ![Charger un dossier - Boîte de dialogue](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

      > [!NOTE] 
          > Vous pouvez directement faire glisser les dossiers et fichiers de l’ordinateur local pour implémenter le chargement. 
       
   * **Télécharger des dossiers ou des fichiers sur votre ordinateur local**

     1. Sélectionnez les dossiers ou fichiers que vous souhaitez télécharger.
     2. Dans la barre d’outils du volet principal, sélectionnez **Télécharger**.
     3. Dans la boîte de dialogue **Select a folder to save the downloaded files into** (Sélectionner un dossier pour y enregistrer les fichiers téléchargés), spécifiez l’emplacement de téléchargement des dossiers et fichiers. Indiquez également le nom que vous souhaitez lui attribuer.
     4. Sélectionnez **Enregistrer**.
   * **Ouvrir un dossier ou un fichier à partir de votre ordinateur local**

     1. Sélectionnez le dossier ou le fichier que vous souhaitez ouvrir.
     2. Dans la barre d’outils du volet principal, sélectionnez **Ouvrir** ou cliquez avec le bouton droit sur le dossier ou le fichier sélectionné, puis cliquez sur **Ouvrir** dans le menu contextuel.
     3. Le fichier est téléchargé et ouvert à l’aide de l’application associée au type sous-jacent du fichier. Le dossier est également ouvert dans le volet principal.

        ![Ouvrir le fichier](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

   * **Copier des dossiers ou des fichiers dans le Presse-papiers**

     1. Sélectionnez les dossiers ou fichiers que vous souhaitez copier.
     2. Dans la barre d’outils du volet principal, sélectionnez **Copier** ou cliquez avec le bouton droit sur les dossiers ou les fichiers sélectionnés, puis cliquez sur **Copier** dans le menu contextuel.
     3. Dans le volet gauche, accédez à un autre compte ADLS, puis double-cliquez dessus pour l’afficher dans le volet principal.
     4. Dans la barre d’outils du volet principal, sélectionnez **Coller** pour en créer une copie. Vous pouvez également cliquer sur l’option **Coller** du menu contextuel dans la destination.

        ![copier/coller un dossier ou un fichier](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

      > [!NOTE] 
          > + L’opération Copier/Coller entre types de stockage **n’est pas** prise en charge. Vous pouvez copier des dossiers ou fichiers ADLS et les coller vers un autre compte ADLS, mais vous **ne pouvez pas** copier des dossiers ou fichiers ADLS et les coller dans le stockage d’objets blob, et inversement. 
          > + Pour l’opération Copier/Coller, téléchargez les dossiers ou fichiers sur l’ordinateur local, puis chargez-les vers l’emplacement de destination. L’outil n’effectue **pas** l’action sur le serveur principal. L’exécution de l’opération Copier/Coller sur les fichiers volumineux est lente. L’optimisation de l’opération Copier/Déplacer des fichiers hautes performances est en cours.
   * **Supprimer des dossiers ou des fichiers**

     1. Sélectionnez les dossiers ou fichiers que vous souhaitez supprimer.
     2. Dans la barre d’outils du volet principal, sélectionnez **Supprimer** ou cliquez avec le bouton droit sur les dossiers ou les fichiers sélectionnés, puis cliquez sur **Supprimer** dans le menu contextuel.
     3. Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

        ![copier/coller un dossier ou un fichier](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

   * **Épingler sur Accès rapide**

     1. Sélectionnez le dossier à épingler.
     2. Dans la barre d’outils du volet principal, sélectionnez **Épingler sur Accès rapide**.
     3. Dans le volet gauche, vous voyez que le dossier sélectionné est ajouté au nœud **Accès rapide**.

        ![épingler sur accès rapide](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)
     4. Après avoir créé l’accès rapide, vous pouvez accéder facilement aux ressources.
   * **Liens ciblés**
     1. Si vous disposez d’une URL, vous pouvez simplement l’entrer dans le chemin d’adresse de **l’Explorateur de fichiers** ou du navigateur.
     2. **Storage Explorer.exe** est alors lancé automatiquement pour accéder à l’emplacement de l’URL que vous venez d’entrer.

        ![lien ciblé dans l’explorateur de fichiers](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Étapes suivantes
* Consultez les [dernières notes de publication et vidéos de l’Explorateur de stockage (version préliminaire)](http://www.storageexplorer.com).
* Découvrez comment [gérer Azure Cosmos DB dans l’Explorateur Stockage Azure](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer).
* Pour en savoir plus sur l’Explorateur Stockage, voir [Prise en main de l’Explorateur Stockage (préversion)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* Pour la prise en main d’Azure Data Lake Store (ADLS), voir [Présentation d’Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview).
* Visionnez la vidéo suivante pour voir [comment utiliser Azure Cosmos DB dans l’Explorateur Stockage Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
