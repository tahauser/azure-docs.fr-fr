---
title: "Intégrer un compte de stockage Azure à Azure CDN | Microsoft Docs"
description: "Découvrez comment utiliser le réseau de distribution de contenu (CDN) Azure pour diffuser du contenu haut débit en mettant en cache les objets blob à partir d’Azure Storage."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: mazha
ms.openlocfilehash: 022071f7825cb9184bd4c815c09e1c202a0a6f91
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Intégrer un compte de stockage Azure à Azure CDN
Vous pouvez activer Azure Content Delivery Network (CDN) pour mettre en cache le contenu du stockage Azure. Azure CDN offre aux développeurs une solution globale pour fournir du contenu à bande passante élevée. Il peut mettre en cache les objets blob et le contenu statique des instances de calcul au niveau de nœuds physiques aux États-Unis, ainsi qu’en Europe, Asie, Australie et Amérique du Sud.

## <a name="step-1-create-a-storage-account"></a>Étape 1 : création d’un compte de stockage
Utilisez la procédure suivante pour créer un compte de stockage pour un abonnement Azure. Un compte de stockage donne accès aux services de stockage Azure. Le compte de stockage représente le niveau le plus élevé de l’espace de noms pour l’accès à chacun des composants du service de stockage Azure : Azure Blob, File d’attente et Table. Pour plus d’informations, consultez l’article [Introduction à Microsoft Azure Storage](../storage/common/storage-introduction.md).

Pour créer un compte de stockage, vous devez être l’administrateur de service ou un co-administrateur de l’abonnement associé.

> [!NOTE]
> Vous pouvez utiliser plusieurs méthodes pour créer un compte de stockage, y compris le portail Azure et PowerShell. Ce didacticiel montre comment utiliser le portail Azure.   
> 

**Pour créer un compte de stockage pour un abonnement Azure**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le coin supérieur gauche, sélectionnez **Créer une ressource**. Dans le volet **Nouveau**, sélectionnez **Stockage**, puis **Compte de stockage - blob, fichier, table, file d'attente**.
    
    Le volet **Créer un compte de stockage** s’affiche.   

    ![Volet Créer un compte de stockage](./media/cdn-create-a-storage-account-with-cdn/cdn-create-new-storage-account.png)

3. Dans la zone **Nom**, entrez un nom de sous-domaine. Cette entrée peut être composée de 3 à 24 lettres minuscules et chiffres.
   
    Cette valeur devient le nom d’hôte contenu dans l’URI utilisé pour adresser les ressources d’objets blob, de files d’attente et de tables pour l’abonnement. Pour résoudre une ressource de conteneur dans le stockage Blob, utilisez un URI au format suivant :
   
    http://*&lt;LibelléCompteStockage&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    où *&lt;StorageAccountLabel&gt;* fait référence à la valeur que vous avez entrée dans la zone **Nom**.
   
    > [!IMPORTANT]    
    > L’étiquette de l’URL forme le sous-domaine de l’URI du compte de stockage et doit être unique parmi tous les services hébergés dans Azure.
   
    Cette valeur est également utilisée comme nom pour le compte de stockage dans le portail ou lorsque vous accédez à ce compte par programme.
    
4. Utilisez les valeurs par défaut des champs **Modèle de déploiement**, **Type de compte**, **Performances** et **Réplication**. 
    
5. Comme **Abonnement**, sélectionnez l’abonnement à utiliser avec le compte de stockage.
    
6. Comme **Groupe de ressources**, sélectionnez ou créez un groupe de ressources. Pour plus d’informations sur les groupes de ressources, consultez la page [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
    
7. Comme **Emplacement**, sélectionnez l’emplacement de votre compte de stockage.
    
8. Sélectionnez **Créer**. Le processus de création du compte de stockage peut durer quelques minutes.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>Étape 2 : Activation du CDN pour le compte de stockage

Vous pouvez activer le CDN pour votre compte de stockage directement depuis votre compte de stockage. 

1. Sélectionnez un compte de stockage à partir du tableau de bord, puis sélectionnez **Azure CDN** dans le volet gauche. Si le bouton **Azure CDN** n’est pas visible immédiatement, vous pouvez entrer le CDN dans la zone de **recherche** du volet gauche.
    
    Le volet **Azure Content Delivery Network** apparaît.

    ![Créer un point de terminaison CDN](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)
    
2. Créez un nouveau point de terminaison en entrant les informations requises :
    - **Profil CDN** : créez un nouveau profil CDN ou utilisez un profil CDN existant.
    - **Niveau de tarification** : sélectionnez un niveau de tarification uniquement si vous créez un profil CDN.
    - **Nom du point de terminaison CDN** : entrez le nom d’un point de terminaison.

    > [!TIP]
    > Par défaut, le nouveau point de terminaison CDN utilise le nom d’hôte de votre compte de stockage en tant que serveur d’origine.

3. Sélectionnez **Créer**. Une fois le point de terminaison créé, il s'affiche dans la liste des points de terminaison.

    ![Nouveau point de terminaison CDN de stockage](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!NOTE]
> Si vous souhaitez spécifier des paramètres de configuration avancés pour votre point de terminaison CDN, notamment le type d’optimisation, vous pouvez utiliser à la place l[’extension Azure CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) pour créer un point de terminaison CDN ou un profil CDN.

## <a name="step-3-enable-additional-cdn-features"></a>Étape 3 : Activer d’autres fonctionnalités du CDN

Dans le volet **Azure CDN** du compte de stockage, sélectionnez le point de terminaison CDN dans la liste pour ouvrir le volet de configuration du CDN. Vous pouvez activer des fonctionnalités supplémentaires du CDN pour la livraison, comme la compression, la chaîne de requête et le filtrage géographique. Vous pouvez également ajouter le mappage de domaine personnalisé pour votre point de terminaison CDN et activer HTTPS sur les domaines personnalisés.
    
![Configuration du point de terminaison CDN de stockage](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)

## <a name="step-4-access-cdn-content"></a>Étape 4 : accès au contenu du CDN
Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. L’adresse d’un objet blob mis en cache est au format suivant :

http://<*nom_point_de_terminaison*\>.azureedge.net/<*MonConteneurPublic*\>/<*Nom_blob*\>

> [!NOTE]
> Dès que vous activez un accès au CDN pour un compte de stockage, tous les objets disponibles publiquement peuvent bénéficier de la mise en cache de périmètre du CDN. Si vous modifiez un objet actuellement mis en cache dans le CDN, le nouveau contenu n’est pas disponible via le CDN avant son actualisation, c’est-à-dire avant expiration de la durée de vie du contenu mis en cache.

## <a name="step-5-remove-content-from-the-cdn"></a>Étape 5 : Suppression de contenu du CDN
Si vous ne voulez plus mettre en cache un objet dans Azure CDN, vous pouvez procéder comme suit :

* Changez le statut du conteneur de public à privé. Pour plus d’informations, consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](../storage/blobs/storage-manage-access-to-resources.md).
* Désactivez ou supprimez le point de terminaison CDN à l’aide du portail Azure.
* Modifiez votre service hébergé pour qu’il ne réponde plus aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN Azure y reste jusqu'à ce que sa durée de vie expire ou que le point de terminaison soit purgé. Après expiration de la durée de vie, Azure CDN vérifie si le point de terminaison CDN est encore valide et si l’objet est encore accessible de manière anonyme. Si ce n’est pas le cas, l’objet n’est plus mis en cache.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Ajouter un domaine personnalisé à votre point de terminaison CDN](cdn-map-content-to-custom-domain.md)
* [Configurer HTTPS sur un domaine personnalisé Azure CDN](cdn-custom-ssl.md)

