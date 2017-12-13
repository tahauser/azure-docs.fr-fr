---
title: "Configuration des stratégies de protection du contenu à l’aide du portail Azure | Microsoft Docs"
description: "Cet article explique comment utiliser le portail Azure pour configurer des stratégies de protection du contenu. L’article montre également comment activer le chiffrement dynamique à vos ressources."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: ecc766abb5df38813b3eb6dde98cdc9afd24ac6b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configuration des stratégies de protection du contenu à l’aide du portail Azure
Microsoft Azure Media Services (AMS) vous permet de sécuriser votre contenu multimédia dès lors qu’il quitte votre ordinateur via le stockage, le traitement et la remise. Media Services permet de transmettre un contenu chiffré dynamiquement avec la norme AES (Advanced Encryption Standard) (à l’aide de clés de chiffrement 128 bits) et le chiffrement commun CENC (Common Encryption) en utilisant PlayReady et/ou Widevine DRM et Apple FairPlay. 

AMS fournit un service de remise de licences DRM et de clés en clair AES aux clients autorisés. Le portail Azure vous permet de créer une **stratégie d’autorisation de clé/licence** pour tous les types de chiffrement.

Cet article explique comment configurer une stratégie de protection du contenu avec le portail Azure. L’article explique également comment appliquer le chiffrement dynamique à vos ressources.

## <a name="start-configuring-content-protection"></a>Commencer à configurer la protection de contenu
Pour utiliser le portail pour commencer à configurer la protection de contenu, de manière globale sur votre compte AMS, procédez comme suit :
1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre compte Azure Media Services.
2. Sélectionnez **Paramètres** > **Protection du contenu**.

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>stratégie d’autorisation de clé/licence
AMS prend en charge plusieurs méthodes d’authentification des utilisateurs effectuant des demandes de clé ou de licence. La stratégie d’autorisation de la clé de contenu doit être configurée par vous et respectée par votre client afin que la clé/licence soit remise au client. La stratégie d’autorisation des clés de contenu peut comporter une ou plusieurs restrictions d’autorisation : **ouverte** ou **à jeton**.

Le portail Azure vous permet de créer une **stratégie d’autorisation de clé/licence** pour tous les types de chiffrement.

### <a name="open-authorization"></a>Autorisation ouverte
La restriction ouverte signifie que le système fournira la clé à toute personne effectuant une demande de clé. Cette restriction peut être utile à des fins de test. 

### <a name="token-authorization"></a>Autorisation de jeton
La stratégie de restriction à jeton doit être accompagnée d’un jeton émis par un service de jeton sécurisé (STS). Media Services prend en charge les jetons aux formats SWT (Simple Web Tokens) et JWT (JSON Web Token). Media Services ne fournit pas de services de jeton sécurisé. Vous pouvez créer un STS personnalisé ou utiliser l’ACS Microsoft Azure pour émettre des jetons. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton. Le service de remise de clé Media Services retourne la clé (ou licence) demandée au client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé (ou licence).

Lorsque vous configurez la stratégie de restriction par jeton, vous devez définir les paramètres principaux de clé de vérification, émetteur et public. La clé de vérification principale contient la clé utilisée pour signer le jeton, l’émetteur est le service de jeton sécurisé qui émet le jeton. Le public (parfois appelé l’étendue) décrit l’objectif du jeton ou la ressource à laquelle le jeton autorise l’accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Modèle de licence PlayReady
Le modèle de licence PlayReady définit la fonctionnalité activée sur votre licence PlayReady. Pour plus d’informations sur le modèle de licence PlayReady, consultez [Vue d’ensemble du modèle de licence PlayReady de Media Services](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>Non persistant
Si vous configurez la licence comme étant non persistante, celle-ci est conservée uniquement en mémoire lors de son utilisation par le lecteur.  

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistante
Si vous configurez la licence comme étant persistante, celle-ci est enregistrée dans un stockage persistant sur le client.

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Modèle de licence Widevine
Le modèle de licence Widevine définit la fonctionnalité activée sur vos licences Widevine.

### <a name="basic"></a>De base
Lorsque vous sélectionnez **De base**, le modèle est créé avec toutes les valeurs par défaut.

### <a name="advanced"></a>Avancé
Pour plus d’informations sur le modèle de droits Widevine, consultez [Présentation du modèle de licence Widevine](media-services-widevine-license-template-overview.md).

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuration de FairPlay
Pour activer le chiffrement de FairPlay, vous devez fournir le certificat de l’application et le secret de l’application via l’option de configuration de FairPlay. Pour obtenir des informations détaillées sur la configuration de FairPlay et de la configuration requise, consultez [cet](media-services-protect-hls-with-FairPlay.md) article.

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Appliquer le chiffrement dynamique à votre ressource
Pour tirer parti du chiffrement dynamique, vous devez encoder votre fichier source en un ensemble de fichiers MP4 à débit adaptatif.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Sélectionner une ressource que vous souhaitez chiffrer
Pour afficher touts vos éléments multimédias, sélectionnez **Paramètres** > **Éléments multimédias**.

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Chiffrer avec AES ou DRM
Quand vous appuyez sur **Chiffrer** sur un élément multimédia, deux options s’offrent à vous : **AES** ou **DRM**. 

#### <a name="aes"></a>AES
Le chiffrement de clé en clair est activé sur tous les protocoles de diffusion en continu : Smooth Streaming, HLS et MPEG-DASH.

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
Lorsque vous sélectionnez l’onglet DRM, vous avez le choix entre plusieurs stratégies de protection de contenu (que vous devez avoir configurées à ce stade) et un ensemble de protocoles de diffusion en continu.

* **PlayReady et Widevine avec MPEG-DASH** : chiffre dynamiquement votre flux MPEG-DASH avec les DRM PlayReady et Widevine.
* **PlayReady et Widevine avec MPEG-DASH+ FairPlay avec HLS** : chiffre dynamiquement votre flux MPEG-DASH avec les DRM PlayReady et Widevine. Chiffre également vos flux HLS avec FairPlay.
* **PlayReady uniquement avec Smooth Streaming, HLS et MPEG-DASH** : chiffre dynamiquement les flux Smooth Streaming, HLS, MPEG-DASH avec la DRM PlayReady.
* **Widevine uniquement avec MPEG-DASH** : chiffre dynamiquement votre MPEG-DASH avec la DRM Widevine.
* **FairPlay uniquement avec HLS** : chiffre dynamiquement votre flux HLS avec FairPlay.

Pour activer le chiffrement de FairPlay, vous devez fournir le certificat de l’application et le secret de l’application via l’option de configuration de FairPlay du panneau des paramètres de protection du contenu.

![Protéger du contenu](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Une fois le chiffrement sélectionné, appuyez sur **Appliquer**.

>[!NOTE] 
>Si vous envisagez de lire un flux HLS chiffré par AES dans Safari, consultez le [billet de blog HLS chiffré dans Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Étapes suivantes
Consultez les parcours d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

