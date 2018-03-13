---
title: "Créer et publier un article de Marketplace dans Azure Stack | Microsoft Docs"
description: "Créez et publiez un article de Marketplace dans Azure Stack."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 77e5f60c-a86e-4d54-aa8d-288e9a889386
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5ac91dac3cb446abaf07492d8b6ec8aa0c120ef4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-publish-a-marketplace-item"></a>Créer et publier un article de la Place de marché

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="create-a-marketplace-item"></a>Créer un élément de Marketplace
1. [Téléchargez](http://www.aka.ms/azurestackmarketplaceitem) l’outil Azure Gallery Packager et l’échantillon de Marketplace Azure Stack.
2. Ouvrez l’échantillon de Marketplace et renommez le dossier **SimpleVMTemplate**. (Utilisez le même nom que votre article de Marketplace, par exemple, **Contoso.TodoList**.) Ce dossier contient les éléments suivants :
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Créez un modèle Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) ou choisissez un modèle sur GitHub. L’article de Marketplace utilise ce modèle pour créer une ressource.
4. Pour vérifier que le déploiement de la ressource peut réussir, testez le modèle avec les API Microsoft Azure Stack.
5. Si votre modèle repose sur une image de machine virtuelle, suivez les instructions pour [ajouter une image de machine virtuelle sur Azure Stack](azure-stack-add-vm-image.md).
6. Enregistrez votre modèle Azure Resource Manager dans le dossier **/Contoso.TodoList/DeploymentTemplates/**.
7. Choisissez les icônes et le texte de votre article de Marketplace. Ajouter les icônes au dossier **Icônes** et le texte au fichier des **ressources** dans le dossier **Chaînes**. Utilisez la convention de dénomination Petit, Moyen, Grand et Large pour vos icônes. Vous trouverez une description détaillée sur la page [Référence : interface utilisateur de l’article de Marketplace](#reference-marketplace-item-ui).
   
   > [!NOTE]
   > Les quatre tailles d’icône (petite, moyenne, grande, large) sont requises pour créer correctement l’article de Marketplace.
   > 
   > 
8. Dans le fichier **manifest.json**, remplacez le **nom** par celui de votre article de Marketplace. Modifiez également **l’éditeur** en indiquant votre nom ou votre société.
9. Sous **artefacts**, remplacez le **nom** et le **chemin d’accès** par les informations correctes du modèle Azure Resource Manager que vous avez inclus.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Remplacez **Mes articles de Marketplace** par la liste des catégories dans lesquelles votre article de Marketplace doit apparaître.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Pour toute autre modification apportée à manifest.json, consultez la page [Référence : manifest.json d’un article de Marketplace](#reference-marketplace-item-manifestjson).
12. Pour créer un package dans un fichier .azpkg, ouvrez une invite de commandes et exécutez la commande suivante :
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > Le chemin d’accès complet au package de sortie doit exister. Par exemple, si le chemin de sortie est C:\MarketPlaceItem\votrepackage.azpkg, le dossier C:\MarketPlaceItem doit exister.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Publier un élément du Marketplace
1. Utilisez PowerShell ou l’Explorateur Stockage Azure pour charger votre article de Marketplace (.azpkg) sur le Stockage Blob Azure. Vous pouvez effectuer le chargement sur le stockage Azure Stack local ou sur le Stockage Azure. (Il s’agit d’un emplacement temporaire du package.) Assurez-vous que l’objet blob est accessible publiquement.
2. Sur la machine virtuelle cliente de l’environnement Microsoft Azure Stack, assurez-vous que votre session PowerShell est configurée avec vos informations d’identification d’administrateur de services fédérés. Vous trouverez des instructions sur l’authentification de PowerShell dans Azure Stack sur la page [Déployer un modèle avec PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Utilisez la cmdlet PowerShell **Add-AzureRMGalleryItem** pour publier l’article de Marketplace sur Azure Stack. Par exemple : 
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Paramètre | Description |
   | --- | --- |
   | SubscriptionID |ID d’abonnement d’administrateur. Vous pouvez le récupérer avec PowerShell. Si vous préférez le récupérer sur le portail, accédez à l’abonnement du fournisseur et copiez l’ID d’abonnement. |
   | GalleryItemUri |URI Blob du package de galerie déjà chargé sur le stockage. |
   | Apiversion |Définir sur **2015-04-01**. |
4. Accédez au portail. L’article de Marketplace apparaît maintenant sur le portail, en tant qu’opérateur ou qu’utilisateur.
   
   > [!NOTE]
   > Le package peut mettre quelques minutes à s’afficher.
   > 
   > 
5. Votre article de Marketplace est maintenant enregistré sur la Marketplace Azure Stack. Vous pouvez choisir de le supprimer de votre emplacement de Stockage Blob.
6. Vous pouvez supprimer un article de Marketplace avec la cmdlet **Remove-AzureRMGalleryItem**. Exemple :
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > L’interface utilisateur Marketplace peut présenter une erreur après la suppression d’un élément. Pour résoudre le problème, cliquez sur **Paramètres** sur le portail. Sélectionnez ensuite **Ignorer les modifications** sous **Personnalisation du portail**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Référence : manifest.json d’un article de Marketplace
### <a name="identity-information"></a>Informations d’identité
| NOM | Obligatoire | type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| NOM |X |Chaîne |[A-Za-z0-9]+ | |
| Publisher |X |Chaîne |[A-Za-z0-9]+ | |
| Version |X |Chaîne |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Métadonnées
| NOM | Obligatoire | type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |Chaîne |Recommandation : 80 caractères |Le portail risque de ne pas afficher correctement le nom de votre élément s’il comporte plus de 80 caractères. |
| PublisherDisplayName |X |Chaîne |Recommandation : 30 caractères |Le portail risque de ne pas afficher correctement le nom de votre éditeur s’il comporte plus de 30 caractères. |
| PublisherLegalName |X |Chaîne |256 caractères maximum | |
| Résumé |X |Chaîne |60 à 100 caractères | |
| LongSummary |X |Chaîne |140 à 256 caractères |Non encore applicable dans Azure Stack |
| Description |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |Entre 500 et 5 000 caractères | |

### <a name="images"></a>Images
La Marketplace utilise les icônes suivantes :

| NOM | Largeur | Hauteur | Notes |
| --- | --- | --- | --- |
| Large |255 px |115 px |Toujours obligatoire |
| grand |115 px |115 px |Toujours obligatoire |
| Moyenne |90 px |90 px |Toujours obligatoire |
| Petite |40 px |40 px |Toujours obligatoire |
| Capture d'écran |533 px |32 px |Facultatif |

### <a name="categories"></a>Catégories
Chaque article de Marketplace doit être étiqueté avec une catégorie qui identifie l’endroit où il apparaît sur l’interface utilisateur du portail. Vous pouvez choisir l’une des catégories existantes d’Azure Stack (calcul, données + stockage, etc.) ou une nouvelle.

### <a name="links"></a>Liens
Chaque article de Marketplace peut comporter différents liens vers du contenu supplémentaire. Les liens sont spécifiés comme une liste de noms et d’URI.

| NOM | Obligatoire | type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |Chaîne |64 caractères maximum | |
| Uri |X |URI | | |

### <a name="additional-properties"></a>Propriétés supplémentaires
En plus des métadonnées précédentes, les auteurs de la Marketplace peuvent fournir des données de paire clé-valeur personnalisées sous la forme suivante :

| NOM | Obligatoire | type | Contraintes | Description |
| --- | --- | --- | --- | --- |
| DisplayName |X |Chaîne |25 caractères maximum | |
| Valeur |X |Chaîne |30 caractères maximum | |

### <a name="html-sanitization"></a>Nettoyage du HTML
Pour tout champ autorisant le HTML, les éléments et attributs suivants sont permis :

h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i

## <a name="reference-marketplace-item-ui"></a>Référence : interface utilisateur d’article de Marketplace
Voici les icônes et le texte des articles de Marketplace qui s’affichent sur le portail Azure Stack.

### <a name="create-blade"></a>Panneau Créer
![Panneau Créer](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Panneau Détails de l’élément du Marketplace
![Panneau Détails de l’élément du Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)

