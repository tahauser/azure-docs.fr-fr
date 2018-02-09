---
title: "Configurer l’encodeur Haivision KB pour envoyer un flux temps réel à débit binaire unique vers Azure | Microsoft Docs"
description: "Cette rubrique explique comment configurer l’encodeur en direct Haivision KB pour envoyer un flux à débit binaire unique vers des canaux AMS activés pour l’encodage live."
services: media-services
documentationcenter: 
author: dbgeorge
manager: vsood
editor: 
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/02/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: 25077cd9338a2764c6dff9e755812033685f6641
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Utiliser l’encodeur Haivision KB pour envoyer un flux temps réel à débit binaire unique
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Cette rubrique explique comment configurer [l’encodeur en direct Haivision KB](https://www.haivision.com/products/kb-series/) pour envoyer un flux à débit binaire unique vers des canaux AMS activés pour l’encodage live. Pour plus d’informations, consultez [Utilisation de canaux activés pour effectuer un encodage en direct avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Ce didacticiel montre comment gérer Azure Media Services (AMS) avec l’outil Azure Media Services Explorer (AMSE). Cet outil est uniquement compatible avec les PC Windows. Si vous êtes sous Mac ou Linux, utilisez le portail Azure pour créer des [canaux](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) et des [programmes](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Prérequis
*   Accès à un encodeur Haivision KB exécutant le logiciel v5.01 ou version ultérieure.
* [Créer un compte Azure Media Services](media-services-portal-create-account.md)
* Vérifiez qu’un point de terminaison de streaming est en cours d’exécution. Pour plus d’informations, consultez [Gestion des points de terminaison de streaming dans un compte Media Services](media-services-portal-manage-streaming-endpoints.md)
* Installez la dernière version de l’outil [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Lancez l’outil et connectez-vous à votre compte AMS.

## <a name="tips"></a>Conseils
* Si possible, utilisez une connexion Internet câblée.
* Une bonne règle pour déterminer les besoins en bande passante consiste à doubler les débits binaires de streaming. Bien qu’il ne s’agisse pas d’une obligation, cela permet de réduire l’impact de l’encombrement du réseau.
* Lors de l’utilisation d’encodeurs logiciels, fermez tous les programmes inutiles.

## <a name="create-a-channel"></a>Créer un canal
1. Dans l’outil AMSE, accédez à l’onglet **Live**, puis cliquez avec le bouton droit dans la zone des canaux. Dans le menu qui s’affiche, sélectionnez **Créer un canal...** .
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Spécifiez un nom de canal (le champ Description est facultatif). Sous Paramètres du canal, sélectionnez **Standard** pour l’option Live Encoding, avec le protocole d’entrée défini sur **RTPM**. Vous pouvez laisser tous les autres paramètres inchangés. Vérifiez que l’option **Démarrer maintenant le nouveau canal** est sélectionnée.
3. Cliquez sur **Créer un canal**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Le démarrage du canal peut prendre jusqu’à 20 minutes.

## <a name="configure-the-haivision-kb-encoder"></a>Configurer l’encodeur Haivision KB
Dans ce didacticiel, les paramètres de sortie ci-dessous sont utilisés. Le reste de cette section décrit la procédure de configuration plus en détail.

Vidéo :
-   Codec : H.264
-   Profil : Élevé (niveau 4.0)
-   Débit binaire : 5 000 kbit/s
-   Image clé : 2 secondes (60 secondes)
-   Fréquence d’images : 30

Audio :
-   Codec : AAC (LC)
-   Débit binaire : 192 kbit/s
-   Taux d’échantillonnage : 44,1 kHz

## <a name="configuration-steps"></a>Configuration
1.  Connectez-vous à l’interface utilisateur de Haivision KB.
2.  Cliquez sur le bouton **Menu** dans le centre de contrôle de canal et sélectionnez **Add Channel (Ajouter un canal)**.  
    ![Capture d’écran 2017-08-14 at 9.15.09 AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Tapez le nom du canal (**Channel Name**) dans le champ Name et cliquez sur Next.  
    ![Capture d’écran 2017-08-14 at 9.19.07 AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Sélectionnez la source d’entrée du canal (**Channel Input Source**) dans la liste déroulante **Input Source** (Source d’entrée) et cliquez sur Next.
    ![Capture d’écran 2017-08-14 at 9.20.44 AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Dans la liste déroulante **Encoder Template** (Modèle d’encodeur), choisissez **H264-720-AAC-192** et cliquez sur Next.
    ![Capture d’écran 2017-08-14 at 9.23.15 AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Dans la liste déroulante **Select New Output** (Sélectionner la nouvelle sortie), choisissez **RTMP** et cliquez sur Next.  
    ![Capture d’écran 2017-08-14 at 9.27.51 AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Dans la fenêtre **Channel Output** (Sortie de canal), renseignez les informations sur le flux Azure. Collez le lien **RTMP** de la configuration de canal initiale dans la zone **Server**. Dans la zone **Output Name** (Nom de la sortie), tapez le nom du canal. Dans la zone Stream Name Template (Modèle de nom de flux), utilisez le modèle RTMPStreamName_%video_bitrate% pour nommer le flux.
    ![Capture d’écran 2017-08-14 at 9.33.17 AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Cliquez sur Next, puis sur Done (Terminé).
9.  Cliquez sur le bouton Lecture (**Play**) pour démarrer le canal de l’encodeur.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Tester la lecture
Accédez à l’outil AMSE et cliquez avec le bouton droit sur le canal à tester. Dans le menu, placez le pointeur sur Lire l’aperçu et sélectionnez avec Azure Media Player.

Si le flux s’affiche dans le lecteur, cela signifie que l’encodeur a été correctement configuré pour se connecter à AMS.

Si vous recevez une erreur, vous devez réinitialiser le canal et ajuster les paramètres de l’encodeur. Pour obtenir des conseils, consultez l’article sur la résolution des problèmes.

## <a name="create-a-program"></a>Créer un programme
1.  Une fois que vous avez vérifié que la lecture fonctionne sur le canal, créez un programme. Sous l’onglet Live de l’outil AMSE, cliquez avec le bouton droit dans la zone des programmes et sélectionnez Créer un programme.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nommez le programme et, si nécessaire, ajustez la longueur de la fenêtre d’archive (qui est de quatre heures par défaut). Vous pouvez également spécifier un emplacement de stockage ou conserver la valeur par défaut.
2.  Cochez la case Démarrer le programme maintenant.
3.  Cliquez sur Créer le programme.
4.  Une fois le programme en cours d’exécution, vérifiez que la lecture fonctionne. Pour ce faire, cliquez avec le bouton droit sur le programme, placez le pointeur sur Lire le(s) programme(s), puis sélectionnez avec Azure Media Player.
5.  Après confirmation, cliquez à nouveau avec le bouton droit sur le programme et sélectionnez Copier l’URL de sortie dans le Presse-papiers (ou obtenez cette information à l’aide de l’option Informations et paramètres du programme du menu).

Le flux est maintenant prêt à être incorporé dans un lecteur ou distribué à une audience pour un affichage en direct.

> [!NOTE]
> La création d’un programme prend moins de temps que la création d’un canal.