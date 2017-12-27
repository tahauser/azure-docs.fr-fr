---
title: "Mettre à l’échelle le traitement multimédia en ajoutant des unités d’encodage - Azure | Microsoft Docs"
description: "Découvrez comment ajouter des unités d’encodage avec .NET"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 4a1bf91c3b3e9dd4d42d84f205a9bc07a71525c2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Mise à l’échelle de l’encodage avec le Kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [Portail](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

> [!NOTE]
> Pour obtenir la dernière version du kit SDK Java et développer des applications avec Java, consultez [Prise en main du Kit de développement logiciel du client Java pour Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Pour télécharger le dernier kit SDK PHP pour Media Services, recherchez la version 0.5.7 du package Microsoft/WindowAzure dans le [référentiel Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Vue d'ensemble
> [!IMPORTANT]
> Pour obtenir plus d’informations sur la mise à l’échelle du traitement multimédia, consultez la rubrique de [présentation](media-services-scale-media-processing-overview.md) .
> 
> 

Pour modifier le type d’unité réservée et le nombre d’unités réservées d’encodage à l’aide du Kit de développement logiciel (SDK) .NET, procédez comme suit :

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Ouverture d'un ticket de support
Par défaut, chaque compte Media Services a une capacité maximale de 25 unités réservées d'encodage et 5 unités réservées de streaming à la demande. Vous pouvez demander une limite supérieure en ouvrant un ticket de support.

### <a name="open-a-support-ticket"></a>Ouverture d’un ticket de support
Pour ouvrir un ticket de support, procédez comme suit :

1. Cliquez sur [Obtenir un support](https://manage.windowsazure.com/?getsupport=true). Si vous n'êtes pas connecté, vous devrez entrer vos informations d'identification.
2. Sélectionnez votre abonnement.
3. Sous le type de support, sélectionnez « Technique ».
4. Cliquez sur « Créer un ticket ».
5. Sélectionnez « Azure Media Services » dans la liste de produits affichée sur la page suivante.
6. Sélectionnez un « type de problème » approprié pour votre problème.
7. Cliquez sur Continuer.
8. Suivez les instructions de la page suivante, puis entrez les détails relatifs à votre problème.
9. Cliquez sur Envoyer pour ouvrir le ticket.

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

