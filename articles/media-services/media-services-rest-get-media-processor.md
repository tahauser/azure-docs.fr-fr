---
title: "Obtention d’une instance de processeur multimédia à l’aide de REST | Microsoft Docs"
description: "Apprenez à créer un composant processeur multimédia pour encoder, chiffrer ou déchiffrer un contenu multimédia, ou convertir son format pour Azure Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: 4e673a92a9740b96eac20cdf5673395bacca8b77
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-get-a-media-processor-instance"></a>Obtention d’une instance de processeur multimédia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Vue d'ensemble
Les processeurs multimédias sont un composant qui gère une tâche de traitement vidéo ou audio spécifique, telle que l’encodage, la conversion de format, le chiffrement ou le déchiffrement de contenu multimédia. Toutes les tâches envoyées à Media Services ont besoin d’un processeur multimédia pour encoder, chiffrer ou convertir le contenu vidéo ou audio. 

## <a name="azure-media-processors"></a>Processeurs multimédias Azure 

La rubrique suivante fournit une liste de processeurs multimédias :

* [Processeurs multimédias d’encodage](scenarios-and-availability.md#encoding-media-processors)
* [Processeurs multimédias Analytics](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Lors de l’accès aux entités dans Media Services, vous devez définir les valeurs et les champs d’en-tête spécifiques dans vos requêtes HTTP. Pour plus d'informations, consultez [Installation pour le développement REST API de Media Services](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Connexion à Media Services

Pour savoir comment vous connecter à l’API AMS, consultez [Accéder à l’API Azure Media Services avec l’authentification Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Obtention d’un processeur multimédia

L’appel REST suivant montre comment obtenir une instance de processeur multimédia par nom (dans ce cas, **Media Encoder Standard**). 

Demande :

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.17
    Host: media.windows.net

Réponse :

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment obtenir une instance de processeur multimédia, consultez l’article [Prendre en main la diffusion de contenus à la demande avec REST](media-services-rest-get-started.md) qui montre comment utiliser Media Encoder Standard afin d’encoder un élément multimédia.

