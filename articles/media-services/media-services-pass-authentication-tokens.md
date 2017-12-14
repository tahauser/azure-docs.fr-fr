---
title: "Jeton d’authentification de passage à Azure Media Services | Microsoft Docs"
description: "Découvrir comment envoyer des jetons d’authentification du client au service de remise des clés Azure Media Services"
services: media-services
keywords: protection de contenu, DRM, authentification de jeton
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>Comment les clients passent des jetons au service de remise de clés d’Azure Media Services
Nous recevons constamment des questions concernant comment un lecteur peut passer un jeton à nos services de remise des clés, qui sera vérifié et le lecteur obtient la clé. Nous prenons en charge les deux formats de jetons Simple Web Token (SWT) et JSON Web Token (JWT). L’authentification de jeton peut être appliquée à tout type de clé, que vous fassiez un chiffrement commun ou un chiffrement d’enveloppe AES dans le système.

Voici les façon de passer le jeton avec votre lecteur, en fonction du lecteur et de la plateforme ciblé :
- Via l’en-tête d’autorisation HTTP.
> [!NOTE]
> Notez que le préfixe « Porteur » est attendu pour les spécifications OAuth 2.0. Il existe un lecteur en exemple avec l’authentification de jeton hébergée sur la [page de démo](http://ampdemo.azureedge.net/) Azure Media Player. Veuillez sélectionner AES ( jeton JWT) ou AES (jeton SWT) pour définir la source vidéo. Le jeton est passé par l’en-tête d’autorisation.

- En ajoutant un paramètre de requête URL avec « token=tokenvalue ».  
> [!NOTE]
> Notez qu’aucun préfixe « Porteur » n’est attendu. Puisque le jeton est envoyé via une URL, vous devrez blinder la chaîne de jetons. Voici un code d’exemple C# sur comment procéder :

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- Via le champ CustomData.
Pour l’acquisition de licence PlayReady uniquement, via le champ CustomData du défi d’acquisition de la licence PlayReady. Dans ce cas, le jeton doit être à l’intérieur du document xml décrit ci-dessous.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
Placez votre jeton d’authentification dans l’élément <Token>.

- Via une autre liste de lecture HLS. Si vous devez configurer l’authentification par jeton pour la liste de lecture AES + HLS sur iOS/Safari, il n’y a aucun moyen d’envoyer directement le jeton. Veuillez consulter ce [billet de blog](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) sur comment remplacer la liste de lecture pour activer ce scénario.

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]