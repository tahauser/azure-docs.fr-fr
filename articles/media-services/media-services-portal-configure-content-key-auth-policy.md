---
title: "Configurer la stratégie d’autorisation de clé de contenu à l’aide du portail Azure | Microsoft Docs"
description: "Apprenez à configurer une stratégie d’autorisation pour une clé de contenu."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 36ec76718d21cac5ae3203f1c6d4b8af2aacb9ed
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="configure-content-key-authorization-policy"></a>Configuration de la stratégie d’autorisation de clé de contenu
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Vue d'ensemble
Microsoft Azure Media Services vous permet de fournir des flux MPEG-DASH, Smooth Streaming et HLS (HTTP-Live-Streaming) protégés par AES (Advanced Encryption Standard) à l’aide de clés de chiffrement 128 bits ou [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). AMS vous permet également de diffuser des flux DASH chiffrés avec Widevine DRM. PlayReady et Widewine sont chiffrés conformément à la spécification de chiffrement commun (ISO/IEC 23001-7 CENC).

Media Services fournit également un **service de remise de clés/de licences** à partir duquel les clients peuvent obtenir des clés AES ou des licences PlayReady/Widevine pour lire le contenu chiffré.

Cet article montre comment utiliser le portail Azure pour configurer la stratégie d’autorisation de clé de contenu. Elles peuvent ensuite être utilisées pour chiffrer dynamiquement votre contenu. Actuellement, vous pouvez chiffrer les formats de diffusion en continu suivants : HLS, MPEG DASH et Smooth Streaming. Vous ne pouvez pas chiffrer les téléchargements progressifs.

Lorsqu’un lecteur demande un flux de données devant être chiffré dynamiquement, Media Services utilise la clé configurée pour chiffrer dynamiquement votre contenu à l’aide du chiffrement AES ou DRM. Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiées pour la clé.

Si vous prévoyez de disposer de plusieurs clés de contenu ou souhaitez spécifier une URL de **service de remise de clés/de licences** autre que le service de remise de clé Media Services, utilisez le Kit de développement logiciel (SDK) .NET Media Services ou des API REST.

[Configurer la stratégie d’autorisation de clé de contenu à l’aide du Kit de développement logiciel (SDK).NET Media Services](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurer la stratégie d’autorisation de clé de contenu à l’aide de l’API REST Media Services](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Certaines considérations s’appliquent :
* Une fois votre compte AMS créé, un point de terminaison de streaming **par défaut** est ajouté à votre compte à l’état **Arrêté**. Pour démarrer la diffusion en continu de votre contenu et tirer parti de l’empaquetage et du chiffrement dynamiques, votre point de terminaison de streaming doit se trouver à l’état **En cours d’exécution**. 
* Votre ressource doit contenir un ensemble de MP4 à débit adaptatif ou des fichiers de diffusion en continu lisse à débit adaptatif. Pour plus d'informations, consultez [Encoder une ressource](media-services-encode-asset.md).
* Le service de remise de clé met en cache ContentKeyAuthorizationPolicy et ses objets connexes (options de stratégie et restrictions) pendant 15 minutes.  Si vous créez une ContentKeyAuthorizationPolicy et que vous spécifiez l’utilisation d’une restriction « Jeton », puis la testez avant de mettre à jour la stratégie de restriction vers « Ouverte », vous devrez attendre environ 15 minutes avant que la stratégie bascule vers la version « Ouverte ».
* Le point de terminaison de streaming AMS définit la valeur de l’en-tête « Access-Control-Allow-Origin » CORS dans la réponse préliminaire comme le caractère générique '\*'. Ceci fonctionne bien avec la plupart des lecteurs, y compris notre Azure Media Player, Roku et JW et d’autres. Cependant, certains lecteurs qui exploitent dashjs ne fonctionnent pas, car, avec le mode des informations d’identification défini sur « inclure », XMLHttpRequest dans leur dashjs n’autorise pas le caractère générique « \* » comme la valeur de “'Access-Control-Allow-Origin”. Comme solution de contournement de cette limitation dans les dashjs, si vous hébergez votre client depuis un domaine unique, Azure Media Services peut indiquer ce domaine dans l’en-tête de la réponse préliminaire. Vous pouvez y arriver en ouvrant un ticket de support via le portail Azure.

## <a name="how-to-configure-the-key-authorization-policy"></a>Configuration de la stratégie d’autorisation de clé
Pour configurer la stratégie d’autorisation de clé, sélectionnez la page **PROTECTION DU CONTENU** .

Media Services prend en charge plusieurs méthodes d’authentification des utilisateurs effectuant des demandes de clé. La stratégie d’autorisation de clé de contenu peut disposer de restrictions d’autorisation de type **ouvert**, **jeton** ou **IP** (**l’IP** peut être configurée avec REST ou le Kit de développement logiciel (SDK) .NET).

### <a name="open-restriction"></a>Restriction ouverte
La restriction **ouverte** signifie que le système fournit la clé à toute personne effectuant une demande de clé. Cette restriction peut être utile à des fins de test.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restriction à jeton
Pour choisir la stratégie de restriction à jeton, cliquez sur le bouton **JETON** .

La stratégie de restriction à **jeton** doit être accompagnée d’un jeton émis par un **service de jeton sécurisé** (STS). Media Services prend en charge les jetons aux formats [SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (**Simple Web Tokens**) et JWT (**JSON Web Token**). Pour plus d’informations, consultez [Authentification à jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services ne fournit pas de **services de jeton sécurisé**. Vous pouvez créer un STS personnalisé ou utiliser l’ACS Microsoft Azure pour émettre des jetons. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton. Le service de remise de clé Media Services retourne la clé de chiffrement pour le client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé de contenu. Pour plus d’informations, consultez [Utilisation de l’ACS Azure pour émettre des jetons](http://mingfeiy.com/acs-with-key-services).

Lorsque vous configurez la stratégie de restriction par **jeton**, vous devez spécifier les paramètres principaux de **clé de vérification**, **émetteur** et **audience**. La **clé de vérification** principale contient la clé utilisée pour signer le jeton, **l’émetteur** est le service de jeton sécurisé qui émet le jeton. Le **public** (parfois appelé **l’étendue**) décrit l’objectif du jeton ou la ressource à laquelle le jeton autorise l’accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.

### <a name="playready"></a>PlayReady
Quand vous protégez votre contenu avec **PlayReady**, vous devez spécifier dans votre stratégie d'autorisation une chaîne XML qui définisse le modèle de licence PlayReady. Par défaut, la stratégie suivante est définie :

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices><ContentKey i:type="ContentEncryptionKeyFromHeader" /><LicenseType>Nonpersistent</LicenseType><PlayRight><AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput></PlayRight></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>

Vous pouvez cliquer sur le bouton **importer le xml de la stratégie** et fournir un autre XML conforme au schéma XML défini [ici](media-services-playready-license-template-overview.md).

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

