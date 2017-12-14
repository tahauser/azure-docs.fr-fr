---
title: "Protéger votre contenu avec Azure Media Services | Microsoft Docs"
description: "Cet article donne une vue d’ensemble de la protection du contenu avec Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 6475a865b9d1b263bd7cc68c99acdb5f6959531e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="protecting-content-overview"></a>Vue d’ensemble de la protection du contenu
Microsoft Azure Media Services vous permet de sécuriser votre contenu multimédia dès lors qu’il quitte votre ordinateur via le stockage, le traitement et la remise. Media Services permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des systèmes DRM principaux : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés. 

L’image suivante illustre le flux de travail de protection du contenu Azure Media Services. 

![Protéger avec PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Cet article explique les concepts et la terminologie pertinents pour comprendre la protection du contenu avec AMS. Il fournit également des liens aux articles qui abordent la protection du contenu. 

## <a name="dynamic-encryption"></a>Chiffrement dynamique
Azure Media Services vous permet de transmettre du contenu chiffré de manière dynamique avec le chiffrement à clé en clair AES ou DRM : Microsoft PlayReady, Google Widevine et Apple FairPlay. Actuellement, vous pouvez chiffrer les formats de diffusion en continu suivants : HLS, MPEG DASH et Smooth Streaming. Le chiffrement sur des téléchargements progressifs n’est pas pris en charge. Chaque méthode de chiffrement prend en charge les protocoles de diffusion en continu suivants :
- AES : MPEG-DASH, Smooth Streaming et HLS
- PlayReady : MPEG-DASH, Smooth Streaming et HLS
- Widevine : MPEG-DASH
- FairPlay : HLS

Pour chiffrer un élément, vous devez associer une clé de contenu de chiffrement à votre élément et configurer également une stratégie d'autorisation pour la clé. Les clés de chiffrement peuvent être spécifiées ou générées automatiquement par Media Services.

Vous devez également configurer la stratégie de remise de l’élément multimédia. Si vous souhaitez diffuser en continu un élément avec chiffrement de stockage, assurez-vous de spécifier comment vous souhaitez le remettre en configurant la stratégie de remise d’éléments.

Lorsqu’un flux est demandé par un lecteur, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu à l’aide du chiffrement de clé en clair AES ou DRM. Pour déchiffrer le flux, le lecteur demande la clé au service de remise des clés AMS. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiées pour la clé.

## <a name="aes-128-clear-key-vs-drm"></a>Clé en clair AES-128 contre DRM
Les clients se demandent souvent s’ils devraient utiliser un chiffrement AES ou un système DRM. La principale différence entre l’utilisation du chiffrement AES et des systèmes DRM est qu’avec le chiffrement AES, la clé de contenu est transmise au client dans un format non chiffré (« en clair »). Par conséquent, la clé utilisée pour chiffrer le contenu peut être affichée dans la trace réseau du client en texte en clair. La clé en clair AES-128 convient à des cas d’utilisation dans lesquels l’observateur est fiable (par ex. le chiffrement de vidéos d’entreprise distribuées dans une société et destinées aux employés).

PlayReady, Widevine et FairPlay offrent tous un haut niveau de chiffrement comparé au chiffrement à clé en clair AES-128. La clé de contenu est transmise dans un format chiffré. De plus, le déchiffrement est pris en charge dans un environnement sécurisé au niveau du système d’exploitation, où il est significativement plus difficile pour un utilisateur malintentionné d’attaquer. DRM est recommandé dans des cas d’utilisation où l’observateur peut ne pas être fiable et où donc un niveau de sécurité supérieur est requis.

## <a name="storage-encryption"></a>Chiffrement du stockage
Vous pouvez utiliser le chiffrement du stockage pour chiffrer votre contenu localement à l’aide du chiffrement AES 256 bits, puis chargez-le vers Azure Storage où il est stocké au repos sous forme chiffrée. Les éléments multimédias protégés par le chiffrement de stockage sont automatiquement déchiffrés et placés dans un système de fichiers chiffré avant d’être encodés, puis éventuellement rechiffrés avant d’être rechargés sous la forme d’un nouvel élément multimédia de sortie. Le principal cas d'utilisation du chiffrement de stockage concerne la sécurisation de fichiers multimédias d'entrée de haute qualité avec un chiffrement renforcé au repos sur le disque.

Pour fournir un élément multimédia avec chiffrement de stockage, vous devez configurer la stratégie de remise de l'élément multimédia afin que Media Services sache comment vous souhaitez remettre votre contenu. Pour que votre élément puisse être diffusé en continu, le serveur de diffusion en continu supprime le chiffrement et diffuse en continu votre contenu à l'aide de la stratégie de remise spécifiée (par exemple AES, chiffrement commun ou aucun chiffrement).

## <a name="types-of-encryption"></a>Types de chiffrement
PlayReady et Widevine utilisent le chiffrement commun (mode CTR AES). FairPlay utilise le chiffrement en mode CBS AES. Le chiffrement à clé en clair AES-128 utilise le chiffrement d’enveloppe.

## <a name="licenses-and-keys-delivery-service"></a>Service de remise de licences et de clés
Media Services fournit un service de remise de licences DRM (PlayReady, Widevine, FairPlay) et de clés AES aux clients autorisés. Vous pouvez utiliser [le portail Azure Classic](media-services-portal-protect-content.md), l’API REST ou le kit de développement logiciel (SDK) Media Services pour .NET pour configurer des stratégies d’authentification et d’autorisation pour vos licences et vos clés.

## <a name="control-content-access"></a>Contrôle de l’accès au contenu
Vous pouvez contrôler qui a accès à votre contenu en configurant la stratégie d’autorisation de clé de contenu. La stratégie d’autorisation de clé de contenu prend en charge la restriction ouverte ou par jeton.

### <a name="open-authorization"></a>Autorisation ouverte
Avec une stratégie d’autorisation ouverte, la clé de contenu est envoyée à un client, quel qu’il soit (sans restriction).

### <a name="token-authorization"></a>Autorisation de jeton
Avec une stratégie d’autorisation de jeton, la clé de contenu n’est envoyée qu’à un client qui présente un JSON Web Token (JWT) ou Simple Web Token (SWT) valide dans la requête de clé/licence. Ce jeton doit être émis par un service d'émission de jeton de sécurité (STS). Vous pouvez utiliser Microsoft Active Directory en tant que STS ou déployer un STS personnalisé. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton. Le service de remise de clé Media Services retourne la clé/licence demandée au client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé/licence.

Lorsque vous configurez la stratégie de restriction par jeton, vous devez définir les paramètres principaux de clé de vérification, émetteur et public. La clé de vérification principale contient la clé utilisée pour signer le jeton, l’émetteur est le service de jeton sécurisé qui émet le jeton. L’audience, parfois appelé étendue, décrit l’objectif du jeton ou la ressource à laquelle le jeton autorise l’accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.

## <a name="streaming-urls"></a>URL de diffusion
Si votre ressource a été chiffrée avec plusieurs DRM, vous devez utiliser une balise de chiffrement dans l’URL de streaming : (format=’m3u8-aapl’, encryption=’xxx’).

Les considérations suivantes s'appliquent :
* Pas plus d’un seul type de chiffrement peut être spécifié.
* Le type de chiffrement ne doit pas être spécifié dans l’URL si un seul chiffrement a été appliqué à l’élément.
* Le type de chiffrement ne tient pas compte de la casse.
* Les types de chiffrement suivants peuvent être spécifiés :  
  * **cenc** : pour PlayReady ou Widevine (chiffrement commun)
  * **cbcs-aapl**: pour FairPlay (chiffrement CBC AES)
  * **cbc**: pour le chiffrement d’enveloppe AES (chiffrement d’enveloppe)

## <a name="next-steps"></a>Étapes suivantes
Les articles suivants décrivent les prochaines étapes pour prendre en main la protection du contenu :
* [Protection avec le chiffrement du stockage](media-services-rest-storage-encryption.md)
* [Protection avec le chiffrement AES](media-services-protect-with-aes128.md)
* [Protection avec PlayReady et/ou Widevine](media-services-protect-with-playready-widevine.md)
* [Protection avec FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Liens connexes
[Explication de la tarification de la remise de licences PlayReady d’Azure Media Services](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Débogage d’un flux de données chiffré utilisant le chiffrement AES dans Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Authentification par jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Intégration d'une application Azure Media Services basée sur OWIN MVC avec Azure Active Directory et une remise de clés de contenu basée sur les revendications JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
