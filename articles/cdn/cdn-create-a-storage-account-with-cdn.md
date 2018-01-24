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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: dbdf263d9d7fdfbe4fbc47db9ba9f30637e8c3ad
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Intégrer un compte de stockage Azure à Azure CDN
CDN peut être activé pour mettre du contenu en cache depuis le stockage Azure. Il offre aux développeurs une solution globale pour la diffusion de contenu haut débit en mettant en cache les objets blob et le contenu statique des instances de calcul sur des nœuds physiques aux États-Unis, en Europe, en Asie, en Australie et en Amérique du Sud.

## <a name="step-1-create-a-storage-account"></a>Étape 1 : création d’un compte de stockage
Utilisez la procédure suivante pour créer un compte de stockage pour un abonnement Azure. Un compte de stockage donne accès aux services de stockage Azure. Le compte de stockage représente le niveau le plus élevé de l’espace de noms pour l’accès à chacun des composants du service de stockage Azure : services BLOB, services de file d’attente et services de Table. Pour plus d'informations, consultez [Introduction à Microsoft Azure Storage](../storage/common/storage-introduction.md).

Pour créer un compte de stockage, vous devez être l’administrateur de service ou un co-administrateur de l’abonnement associé.

> [!NOTE]
> Il existe plusieurs méthodes que vous pouvez utiliser pour créer un compte de stockage, y compris le portail Azure et Powershell.  Pour ce didacticiel, nous allons utiliser le portail Azure.  
> 
> 

**Pour créer un compte de stockage pour un abonnement Azure**

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le coin supérieur gauche, sélectionnez **Nouveau**. Dans la boîte de dialogue **Nouveau**, sélectionnez **Données + stockage**, puis cliquez sur **Compte de stockage**.
    
    Le panneau **Créer un compte de stockage** s’affiche.   

    ![Créer un compte de stockage][create-new-storage-account]  

3. Dans le champ **Nom** , tapez un nom de sous-domaine. Cette entrée peut être composée de 3 à 24 lettres minuscules et chiffres.
   
    Cette valeur devient le nom d’hôte contenu dans l’URI utilisé pour adresser les ressources d’objets blob, de files d’attente et de tables pour l’abonnement. Pour adresser une ressource de conteneur dans le service BLOB, vous utilisez un URI au format suivant, où *&lt;LibelléCompteStockage&gt;* fait référence à la valeur entrée dans **Enter a URL** :
   
    http://*&lt;LibelléCompteStockage&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **Important :** l’étiquette de l’URL forme le sous-domaine de l’URI du compte de stockage et doit être unique parmi tous les services hébergés dans Azure.
   
    Cette valeur est également utilisée comme nom pour ce compte de stockage dans le portail ou lors de l’accès à ce compte par programme.
4. Conservez les valeurs par défaut des champs **Modèle de déploiement**, **Type de compte**, **Performances** et **Réplication**. 
5. Sélectionnez l' **abonnement** à utiliser avec le compte de stockage.
6. Sélectionnez ou créez un **groupe de ressources**.  Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Sélectionnez l’emplacement de votre compte de stockage.
8. Cliquez sur **Créer**. Le processus de création du compte de stockage peut durer quelques minutes.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>Étape 2 : Activation du CDN pour le compte de stockage

Avec l’intégration la plus récente, vous pouvez maintenant activer CDN pour votre compte de stockage sans quitter votre extension de portail de stockage. 

1. Sélectionnez le compte de stockage, recherchez « CDN » ou faites défiler vers le bas dans le menu de navigation de gauche, puis cliquez sur « Azure CDN ».
    
    Le panneau **Azure CDN** s’affiche.

    ![cdn enable navigation][cdn-enable-navigation]
    
2. Créer un nouveau point de terminaison en entrant les informations requises
    - **Profil CDN** : vous pouvez en créer un nouveau ou utiliser un profil existant.
    - **Niveau de tarification** : vous devez sélectionner un niveau de tarification uniquement si vous créez un nouveau profil CDN.
    - **Nom du point de terminaison CDN** : entrez le nom de point de terminaison de votre choix.

    > [!TIP]
    > Le point de terminaison CDN créé utilise le nom d’hôte de votre compte de stockage en tant qu’origine par défaut.

    ![cdn new endpoint creation][cdn-new-endpoint-creation]

3. Après création, le nouveau point de terminaison s’affiche dans la liste de points de terminaison ci-dessus.

    ![cdn storage new endpoint][cdn-storage-new-endpoint]

> [!NOTE]
> Vous pouvez également accéder à l’extension Azure CDN pour activer le CDN. [Didacticiel](#Tutorial-cdn-create-profile).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>Étape 3 : Activer d’autres fonctionnalités du CDN

Dans le panneau « Azure CDN » du compte de stockage, cliquez sur le point de terminaison CDN dans la liste pour ouvrir le panneau de configuration du CDN. Vous pouvez activer des fonctionnalités supplémentaires du CDN pour la livraison, comme la compression, la chaîne de requête et le filtrage géographique. Vous pouvez également ajouter le mappage de domaine personnalisé pour votre point de terminaison CDN et activer HTTPS sur les domaines personnalisés.
    
![Configuration CDN stockage CDN][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>Étape 4 : accès au contenu du CDN
Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. L’adresse d’un objet blob mis en cache est au format suivant :

http://<*nom_point_de_terminaison*\>.azureedge.net/<*MonConteneurPublic*\>/<*Nom_blob*\>

> [!NOTE]
> Dès que vous activez un accès au CDN pour un compte de stockage, tous les objets disponibles publiquement peuvent bénéficier de la mise en cache de périmètre du CDN. Si vous modifiez un objet actuellement mis en cache dans le CDN, le nouveau contenu n’est pas disponible via le CDN avant son actualisation, c’est-à-dire une fois sa durée de vie écoulée.
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>Étape 5 : Suppression de contenu du CDN
Si vous ne souhaitez plus mettre en cache un objet dans le réseau de distribution de contenu (CDN) Azure, vous pouvez procéder comme suit :

* Vous pouvez changer le statut du conteneur de public à privé. Pour plus d'informations, consultez [Gestion de l'accès en lecture anonyme aux conteneurs et aux objets blob](../storage/blobs/storage-manage-access-to-resources.md) .
* Vous pouvez désactiver ou supprimer le point de terminaison CDN à l’aide du portail de gestion.
* Vous pouvez modifier votre service hébergé pour qu’il ne réponde plus aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN y reste jusqu'à ce que sa durée de vie expire ou que le point de terminaison soit purgé. Après cela, le CDN vérifie si le point de terminaison CDN est encore valide et si l’objet est encore accessible de manière anonyme. S’il ne l’est pas, l’objet n’est plus mis en cache.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Mappage du contenu CDN à un domaine personnalisé](cdn-map-content-to-custom-domain.md)
* [Activer HTTPS pour votre domaine personnalisé](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 
