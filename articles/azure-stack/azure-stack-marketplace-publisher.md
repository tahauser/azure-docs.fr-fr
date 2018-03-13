---
title: "Utiliser la boîte à outils Place de Marché pour créer et publier des éléments de Place de Marché | Microsoft Docs"
description: "Découvrez comment créer rapidement des éléments de Place de Marché avec la boîte à outils de publication."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61ee3296429f9641643f1c9268ae89e3691fcfa1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Ajouter des éléments de Place de Marché à l’aide de l’outil de publication
L’ajout de votre contenu à la [Place de Marché Azure Stack](azure-stack-marketplace.md) met vos solutions à disposition de vos locataires (et de vous-même) pour le déploiement.  La boîte à outils Place de Marché crée des fichiers de packages Place de Marché Azure (.azpkg) basés sur vos extensions de machine virtuelle ou vos modèles IaaS Azure Resource Manager.  Vous pouvez également utiliser la boîte à outils Place de Marché pour publier des fichiers .azpkg créés avec l’outil ou par le biais d’étapes [manuelles](azure-stack-create-and-publish-marketplace-item.md).  Cette rubrique vous montre comment télécharger l’outil, créer un élément de Place de Marché basé sur un modèle de machine virtuelle, puis publier cet élément sur la Place de Marché Azure Stack.     


## <a name="prerequisites"></a>configuration requise
 - Vous devez exécuter la boîte à outils sur l’hôte Azure Stack ou disposer d’une connectivité [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) à partir de l’ordinateur sur lequel vous exécutez l’outil.

 - Téléchargez les [modèles de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) et effectuez leur extraction.

 - Téléchargez l’[outil d’empaquetage de la galerie Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - La publication sur la Place de Marché nécessite des icônes et un fichier de miniature.  Vous pouvez utiliser vos propres fichiers ou enregistrer les [exemples](azure-stack-marketplace-publisher.md#support-files) de fichiers localement pour cet exemple.

## <a name="download-the-tool"></a>Télécharger l’outil
Vous pouvez [télécharger la boîte à outils Place de Marché à partir du dépôt d’outils Azure Stack](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Créer des éléments de Place de Marché
Dans cette section, vous allez utiliser la boîte à outils Place de Marché pour créer un package d’élément Place de Marché au format .azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Fournir des informations de Place de Marché avec l’Assistant
1. Exécutez la boîte à outils Place de Marché à partir d’une session PowerShell :
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Cliquez sur l’onglet **Solution**.  Cet écran accepte des informations sur votre élément de Place de Marché. Entrez des informations sur votre élément, telles que vous souhaitez qu’elles apparaissent sur la Place de Marché.  Vous pouvez également spécifier un [fichier de paramètres](azure-stack-marketplace-publisher.md#use-a-parameters-file) pour préremplir le formulaire.  
    
    ![capture d’écran du premier écran de la boîte à outils Place de Marché](./media/azure-stack-marketplace-publisher/image7.png)
3. Cliquez sur **Parcourir** et sélectionnez un fichier image pour chaque champ d’icône et de capture d’écran.  Vous pouvez utiliser vos propres icônes ou les exemples fournis dans la section [Fichiers de prise en charge](azure-stack-marketplace-publisher.md#support-files).
4. Une fois tous les champs renseignés, sélectionnez « Aperçu de la solution » pour afficher un aperçu de la solution sur la Place de Marché.  Vous pouvez réviser et modifier le texte, les images et la capture d’écran avant de cliquer sur **Suivant**.  

### <a name="import-template-and-create-package"></a>Importer le modèle et créer le package
Dans cette section, vous allez importer le modèle et utiliser l’entrée pour votre solution.

1.  Cliquez sur **Parcourir** et sélectionnez le fichier *azuredeploy.json* dans le dossier 101-Simple-Windows-VM dans les modèles téléchargés.

    ![capture d’écran du deuxième écran de la boîte à outils Place de Marché](./media/azure-stack-marketplace-publisher/image8.png)
2.  L’Assistant Déploiement est renseigné avec une étape *De base* et des éléments d’entrée pour chaque paramètre spécifié dans le modèle.  Vous pouvez ajouter des étapes supplémentaires et déplacer des entrées d’une étape à une autre.  Par exemple, vous souhaiterez peut-être avoir des étapes « Configuration frontale » et « Configuration backend » pour votre solution.
3.  Spécifiez le chemin vers AzureGalleryPackager.exe.  
4.  Cliquez sur **Créer**. La boîte à outils Place de Marché empaquète votre solution dans un fichier .azpkg.  Une fois terminé, l’Assistant affiche le chemin de votre fichier de solution et vous donne la possibilité de continuer à publier votre package sur Azure Stack.


## <a name="publish-marketplace-items"></a>Publier des éléments de Place de Marché
Dans cette section, vous allez publier l’élément de Place de Marché sur votre Place de Marché Azure Stack.

![capture d’écran du premier écran de la boîte à outils Place de Marché](./media/azure-stack-marketplace-publisher/image9.png)

1.  L’Assistant a besoin d’informations pour publier votre solution :
    
    |Champ|Description|
    |-----|-----|
    | Nom de l’administrateur de service | Compte de l’administrateur de service.  Exemple : ServiceAdmin@mydomain.onmicrosoft.com |
    | Mot de passe | Mot de passe du compte d’administrateur de service. |
    | Point de terminaison d’API | Point de terminaison Azure Resource Manager Azure Stack.  Exemple : management.local.azurestack.external |
2.  Cliquez sur **Publier**. Le journal de publication apparaît.
3.  Vous pouvez désormais déployer votre élément publié par le biais du portail Azure Stack.


## <a name="use-a-parameters-file"></a>Utiliser un fichier de paramètres
Vous pouvez également utiliser un fichier de paramètres pour renseigner les informations de Place de Marché.  

La boîte à outils Place de Marché comprend un fichier *solution.parameters.ps1* que vous pouvez utiliser pour créer votre propre fichier de paramètres.


## <a name="support-files"></a>Fichiers de prise en charge
| Description | Exemple |
| ----- | ----- |
| icône .png de 40x40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| icône .png de 90x90 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| icône .png de 115x115 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| icône .png de 255x115 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| Miniature .png de 533x324 | ![](./media/azure-stack-marketplace-publisher/image5.png) |


