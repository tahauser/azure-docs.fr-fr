---
title: "Télécharger des éléments de la Place de marché à partir d’Azure | Microsoft Docs"
description: "Je peux télécharger des éléments de la Place de marché à partir d’Azure dans mon déploiement Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 3437bc9f164cbdc6c923498b978291ced6278744
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*


Quand vous décidez du contenu à inclure dans votre Place de marché Azure Stack, il est recommandé de prendre en compte le contenu disponible auprès de la Place de marché Azure. Vous pouvez télécharger à partir d’une liste organisée d’éléments de la Place de marché Azure, qui ont été préalablement testés pour s’exécuter sur Azure Stack. De nouveaux éléments sont fréquemment ajoutés à cette liste : consultez-la donc régulièrement pour voir ce qu’elle contient de nouveau.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Télécharger des éléments de la Place de marché dans un scénario connecté (avec connectivité Internet)

1. Pour télécharger des éléments de la Place de marché, vous devez d’abord [inscrire Azure Stack auprès d’Azure](azure-stack-register.md).
2. Connectez-vous au portail d’administration d’Azure Stack (https://portal.local.azurestack.external).
3. Certains éléments de la Place de marché peuvent être volumineux. Vérifiez que vous avez suffisamment d’espace sur votre système en cliquant sur **Fournisseurs de ressources** > **Stockage**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Cliquez sur **Autres services** > **Gestion de la Place de marché**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Cliquez sur **Ajouter à partir d’Azure** pour afficher la liste des éléments disponibles au téléchargement. Vous pouvez cliquer sur chaque élément de la liste pour afficher sa description et la taille du téléchargement.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Sélectionnez l’élément que vous voulez dans la liste, puis cliquez sur **Télécharger**. Le téléchargement de l’image de machine virtuelle correspondant à l’élément sélectionné démarre. Le temps de téléchargement varie.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Une fois le téléchargement terminé, vous pouvez déployer votre nouvel élément de Place de marché en tant qu’opérateur ou utilisateur Azure Stack. Cliquez sur **+ Nouveau**, recherchez le nouvel élément de Place de marché parmi les catégories, puis sélectionnez l’élément.
7. Cliquez sur **Créer** pour ouvrir l’expérience de création de l’élément qui vient d’être téléchargé. Suivez les instructions pas à pas pour déployer votre élément.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Télécharger des éléments de la Place de marché dans un scénario déconnecté ou partiellement connecté (avec connectivité Internet limitée)

Lorsque vous déployez Azure Stack en mode déconnecté (sans connexion Internet), vous ne peut pas utiliser le portail Azure Stack pour télécharger des éléments de la Place de marché. Vous pouvez cependant pouvez utiliser l’outil de syndication de la Place de marché pour télécharger des éléments de la Place de marché sur un ordinateur qui dispose d’une connexion Internet, puis les transférer vers votre environnement Azure Stack.

### <a name="prerequisites"></a>Prérequis
Avant de pouvoir utiliser l’outil de syndication de la Place de marché, vérifiez que vous avez bien [inscrit Azure Stack auprès de votre abonnement Azure](azure-stack-register.md).  

À partir de l’ordinateur connecté à Internet, procédez de la manière suivante pour télécharger les éléments de la Place de marché dont vous avez besoin :

1. Ouvrez une console PowerShell en tant qu’administrateur et [installez les modules PowerShell spécifiques à Azure Stack](azure-stack-powershell-install.md). Assurez-vous d’installer **PowerShell version 1.2.11 ou ultérieure**.  

2. Ajoutez le compte Azure que vous avez utilisé pour inscrire Azure Stack. Pour ce faire, exécutez la cmdlet **Add-AzureRmAccount** sans aucun paramètre. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à 2 facteurs en fonction de la configuration de votre compte.  

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous avez utilisé pour l’inscription :  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Téléchargez la dernière version de l’outil de syndication de la Place de marché à l’aide du script suivant :  

   ```PowerShell
   # Download the tools archive.
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd \AzureStack-Tools-master

   ```

5. Importez le module de syndication et lancez l’outil en exécutant les commandes suivantes :  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Une fois l’outil exécuté, vous êtes invité à entrer les informations d’identification de votre compte Azure. Connectez-vous au compte Azure que vous avez utilisé pour inscrire Azure Stack. Une fois connecté, vous devriez voir l’écran suivant avec la liste des éléments de la Place de marché disponibles.  

   ![Fenêtre contextuelle des éléments de la Place de marché Azure](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Sélectionnez l’image que vous souhaitez télécharger, puis notez la version de l’image. Vous pouvez sélectionner plusieurs images en maintenant la touche Ctrl enfoncée. Utilisez la version de l’image pour importer l’image dans la section suivante.  Cliquez ensuite sur **OK**, puis acceptez les conditions juridiques en cliquant sur **Oui**. Vous pouvez également filtrer la liste des images à l’aide de l’option **Ajouter des critères**. 

   La durée du téléchargement varie selon la taille de l’image. Une fois l’image téléchargée, elle apparaît dans le chemin d’accès de destination que vous avez indiqué précédemment. Le téléchargement contient le fichier VHD et les éléments de la galerie au format Azpkg.

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>Importer l’image et la publier sur la Place de marché Azure Stack

1. Après avoir téléchargé le package d’images et de galerie, enregistrez-le avec son contenu dans le dossier AzureStack-Tools-master sur un lecteur de disque amovible et copiez le dossier dans l’environnement Azure Stack (vous pouvez le copier en local dans tout emplacement, par exemple « C:\MarketplaceImages »).     

2. Avant d’importer l’image, vous devez vous connecter à l’environnement de l’opérateur Azure Stack en suivant la procédure décrite dans [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md).  

3. Importez l’image dans Azure Stack à l’aide de l’applet de commande Add-AzsVMImage. Lorsque vous utilisez cette cmdlet, veillez à remplacer les valeurs *publisher*, *offer* et les autres valeurs de paramètre par les valeurs de l’image que vous importez. Vous pouvez obtenir les valeurs *publisher*, *offer* et *sku* de l’image à partir de l’objet imageReference du fichier Azpkg que vous avez téléchargé précédemment, et obtenir la valeur *version* à partir de l’étape 6 de la section précédente.

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Remplacez les valeurs de paramètre et exécutez la commande suivante :

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

4. Utilisez le portail pour télécharger votre élément de la Place de marché (. Azpkg) dans Azure Stack Blob Storage. Vous pouvez effectuer le chargement sur le stockage Azure Stack local ou sur le Stockage Azure. (Il s’agit d’un emplacement temporaire du package.) Assurez-vous que l’objet blob est accessible publiquement et notez l’URI.  

5. Utilisez l’applet de commande **Add-AzsGalleryItem** pour publier l’élément de la Place de marché sur Azure Stack. Par exemple : 

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. Une fois l’élément de galerie publié, vous pouvez l’afficher à partir du volet **Nouveau** > **Place de marché**.  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>Étapes suivantes

[Créer et publier un élément de Place de Marché](azure-stack-create-and-publish-marketplace-item.md)
