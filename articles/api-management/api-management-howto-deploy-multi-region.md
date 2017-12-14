---
title: "Déployer des services Gestion des API Azure sur plusieurs régions Azure | Microsoft Docs"
description: "Découvrez comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: e126e34bc9fce21243b0ef79f5ab661aec3a2de6
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Comment déployer une instance de service Gestion des API Azure dans plusieurs régions Azure
Gestion des API prend en charge le déploiement sur plusieurs régions, ce qui permet aux éditeurs d’API de ne distribuer qu’un seul service Gestion des API sur le nombre de régions Azure. Ceci permet de réduire la latence de la demande telle qu’elle est perçue par les utilisateurs distribués de l’API. La disponibilité du service est également améliorée si une région est mise hors connexion. 

Lors de la création initiale du service Gestion des API, il ne contient qu’une seule [unité][unit] et se trouve dans une seule région Azure, désignée comme région primaire. D’autres régions peuvent être facilement ajoutées via le portail Azure. Un serveur de passerelle Gestion des API est déployé dans chaque région et le trafic d’appel est acheminé vers la passerelle la plus proche. Si une région est déconnectée, le trafic est automatiquement redirigé vers la passerelle suivante la plus proche. 

> [!IMPORTANT]
> Le déploiement multi-régions est disponible uniquement dans le niveau **[Premium][Premium]**.
> 
> 

## <a name="add-region"></a>Déploiement d’une instance de service Gestion des API sur une nouvelle région
> [!NOTE]
> Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d’une instance de service Gestion des API][Create an API Management service instance].
> 
> 

Dans le portail Azure, accédez à la page **Scale and pricing (Échelle et tarification)** de votre instance du service Gestion des API. 

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour procéder au déploiement vers une nouvelle région, cliquez sur **+ Ajouter une région** dans la barre d’outils.

![Ajouter une région][api-management-add-region]

Sélectionnez l’emplacement dans la liste déroulante et définissez le nombre d’unités à l’aide du curseur.

![Spécifier les unités][api-management-select-location-units]

Cliquez sur **Ajouter** pour placer votre sélection dans la table des emplacements. 

Répétez cette procédure jusqu’à ce que vous ayez configuré tous les emplacements, puis cliquez sur **Enregistrer** dans la barre d’outils pour démarrer le déploiement.

## <a name="remove-region"></a>Suppression d’une instance de service Gestion des API dans un emplacement
Dans le portail Azure, accédez à la page **Scale and pricing (Échelle et tarification)** de votre instance du service Gestion des API. 

![Onglet Mettre à l’échelle][api-management-scale-service]

Pour l’emplacement que vous souhaitez supprimer, ouvrez le menu contextuel à l’aide du bouton **...** situé à l’extrême droite du tableau. Sélectionnez l’option **Supprimer**.

![Supprimer la région][api-management-remove-region]

Confirmez la suppression, puis cliquez sur **Enregistrer** pour appliquer les modifications.

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

