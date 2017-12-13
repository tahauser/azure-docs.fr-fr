---
title: Utiliser Azure Media Clipper dans le portail | Microsoft Docs
description: "Créer des clips à l’aide d’Azure Media Clipper à partir du portail Azure"
services: media-services
keywords: "clip;sous-clip;encodage;média"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1deca68cd6a61ede7536c4d5544036a10c54209b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Créer des clips avec Azure Media Clipper dans le portail
Vous pouvez utiliser Azure Media Clipper dans le portail pour créer des clips à partir d’éléments multimédias dans vos comptes de services de média. Pour commencer, accédez à votre compte de services de média dans le portail. Sélectionnez ensuite l’onglet **Sous-clip**.

Dans l’onglet **Sous-clip**, vous pouvez commencer la composition de clips. Dans le portail, le Clipper charge des MP4 à débit unique, des MP4 multidébits et des archives dynamiques qui sont publiés avec un localisateur de diffusion en continu valide. Les éléments multimédias non publiés ne sont pas chargés.

Clipper est actuellement disponible en préversion publique. Pour accéder à Clipper dans le portail Azure, accédez à la [page de préversion publique](https://portal.azure.com/?feature.subclipper=true).

L’image suivante illustre la page d’accueil Clipper dans votre compte de services de média : ![Azure Media Clipper dans le portail Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Production de clips
Pour créer un clip, faites glisser et déposez un élément multimédia sur l’interface de clip. Si vous connaissez les heures de marquage, vous pouvez les entrer manuellement dans l’interface. Sinon, lisez l’élément multimédia ou déplacez la tête de lecture pour rechercher les heures de marquage de début et de fin souhaitées. Si une heure de marquage de début ou de fin n’est pas indiquée, le clip commence au début ou continue jusqu’à la fin de l’élément multimédia d’entrée, respectivement.

Pour naviguer à la précision d’image/de GOP, utilisez les boutons d’avance par image/GOP ou de recul par image/GOP. Pour découper par rapport à plusieurs éléments multimédias, faites glisser et déposez les éléments multimédias dans l’interface de clip à partir du panneau de sélection d’éléments multimédias. Vous pouvez sélectionner et réorganiser les éléments multimédias dans l’interface dans l’ordre souhaité. Le panneau de sélection d’éléments multimédias indique la durée de l’élément multimédia, le type et les métadonnées de résolution de chaque élément multimédia. Lorsque vous concaténez plusieurs éléments multimédias ensemble, prenez en compte la résolution source de chaque fichier d’entrée. Si la résolution source est différente, l’entrée de résolution inférieure est augmentée pour correspondre à la résolution de l’élément multimédia à la résolution la plus élevée. Pour afficher un aperçu du résultat du travail de découpage, sélectionnez le bouton Aperçu, le clip est lu à partir des heures de marquage sélectionnées.

## <a name="producing-dynamic-manifest-filters"></a>Production de filtres de manifeste dynamique
Les [filtres de manifeste dynamique](https://azure.microsoft.com/blog/dynamic-manifest/) correspondent à un ensemble de règles basées sur les attributs du manifeste et la chronologie d’élément multimédia. Ces règles déterminent comment votre point de terminaison de streaming manipule la liste de lecture de sortie (manifeste). Le filtre peut être utilisé pour modifier les segments diffusés en continu pour la lecture. Les filtres produits par le Clipper sont des filtres locaux et sont spécifiques à l’élément multimédia source. Contrairement aux clips de rendu, les filtres ne sont pas des nouveaux éléments multimédias et ne nécessitent pas d’encodage pour être produits. Ils peuvent être rapidement créés via le [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) ou l’[API REST](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest). Toutefois, ils offrent une précision GOP uniquement. Les éléments multimédias encodés pour la diffusion en continu ont généralement une taille de GOP de deux secondes.

Pour créer un filtre de manifeste dynamique, accédez à l’onglet **Ressources** et sélectionnez la ressource requise. Sélectionnez le bouton **Sous-clip** dans le menu supérieur. Sélectionnez le filtre de manifeste dynamique comme mode de découpage dans le menu des paramètres avancés. Vous pouvez ensuite suivre le processus de production d’un clip rendu pour créer le filtre. Les filtres ne peuvent être produits qu’à partir d’un seul élément multimédia.

L’image suivante illustre Clipper dans le mode de filtre de manifeste dynamique dans le portail Azure : ![Azure Media Clipper en mode de filtre de manifeste dynamique dans le portail Azure](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Envoi des travaux de découpage
Une fois la composition du clip terminée, sélectionnez le bouton d’envoi du travail pour lancer le travail de découpage correspondant ou l’appel de manifeste dynamique.

## <a name="next-steps"></a>Étapes suivantes
Pour bien démarrer avec Azure Media Clipper et découvrir comment déployer le widget, lisez l’article de [prise en main](media-services-azure-media-clipper-getting-started.md).