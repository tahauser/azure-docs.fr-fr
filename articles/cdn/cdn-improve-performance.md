---
title: "Compression des fichiers dans Azure CDN pour améliorer les performances | Microsoft Docs"
description: "Découvrez comment améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers dans Azure CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b4e27ec57543daed35811fff347f457b0dd2cd5c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Compression des fichiers dans Azure CDN pour améliorer les performances
La compression est une méthode simple et efficace qui vise à améliorer la vitesse de transfert des fichiers et à accroître les performances de chargement des pages en réduisant la taille des fichiers avant leur envoi à partir du serveur. Elle permet de réduire les coûts de bande passante et offre à vos utilisateurs davantage de réactivité.

Il existe deux façons d’activer la compression :

* Activer la compression sur votre serveur d’origine. Dans ce cas, le CDN fait transiter les fichiers compressés et les remet aux clients qui les demandent.
* Activer la compression directement sur les serveurs de périmètre CDN. Dans ce cas, le CDN compresse les fichiers et les sert aux utilisateurs finals, même s’ils ne sont pas compressés par le serveur d’origine.

> [!IMPORTANT]
> Les changements de configuration CDN peuvent prendre un certain temps pour se propager sur le réseau.  Pour les profils du <b>CDN Azure fourni par Akamai</b> , la propagation s’effectue généralement en moins d’une minute.  Pour les profils <b>Azure CDN fourni par Verizon</b>, vous voyez généralement vos changements s’appliquer dans un délai de 90 minutes.  Si vous configurez la compression de votre point de terminaison CDN pour la première fois, patientez 1 à 2 heures avant de chercher à résoudre un problème, pour être certain que les paramètres de compression ont été propagés aux POP.
> 
> 

## <a name="enabling-compression"></a>Activation de la compression
> [!NOTE]
> Les niveaux Standard et Premium de CDN fournissent les mêmes fonctionnalités, mais l’interface utilisateur est différente.  Pour plus d’informations sur les différences entre les niveaux Standard et Premium de CDN, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md).
> 
> 

### <a name="standard-tier"></a>Niveau standard
> [!NOTE]
> Cette section s’applique au profil **Azure CDN Standard fourni par Verizon** et au profil **Azure CDN Standard fourni par Akamai**.
> 
> 

1. Dans la page du profil CDN, cliquez sur le point de terminaison CDN que vous souhaitez gérer.
   
    ![Points de terminaison du profil CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    La page du point de terminaison CDN s’ouvre.
2. Cliquez sur le bouton **Configurer** .
   
    ![Bouton de gestion du profil CDN](./media/cdn-file-compression/cdn-config-btn.png)
   
    La page de configuration CDN s’ouvre.
3. Activez **Compression**.
   
    ![Options de compression CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Utilisez les types par défaut ou modifiez la liste en supprimant ou en ajoutant des types de fichier.
   
   > [!TIP]
   > Bien que cela soit possible, il n’est pas recommandé d’appliquer la compression aux formats compressés. Par exemple, ZIP, MP3, MP4 ou JPG.
   > 
 
5. Une fois vos modifications effectuées, cliquez sur le bouton **Enregistrer** .

### <a name="premium-tier"></a>Niveau Premium
> [!NOTE]
> Cette section s’applique aux profils du **CDN Azure CDN Premium fourni par Verizon** .
> 
> 

1. Dans la page de profil CDN, cliquez sur le bouton **Gérer**.
   
    ![Bouton de gestion du profil CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Le portail de gestion CDN s'ouvre.
2. Pointez sur l’onglet **HTTP volumineux**, puis pointez sur le menu volant **Paramètres de cache**.  Cliquez sur **Compression**.

    ![Sélection de la compression de fichiers](./media/cdn-file-compression/cdn-compress-select.png)
   
    Les options de compression sont affichées.
   
    ![Options de compression de fichiers](./media/cdn-file-compression/cdn-compress-files.png)
3. Activez la compression en cliquant sur la case d’option **Compression activée** .  Entrez les types MIME que vous souhaitez compresser sous forme de liste délimitée par des virgules (sans espace) dans la zone de texte **Types de fichiers** .
   
   > [!TIP]
   > Bien que cela soit possible, il n’est pas recommandé d’appliquer la compression aux formats compressés. Par exemple, ZIP, MP3, MP4 ou JPG.
   > 
4. Après avoir apporté vos modifications, cliquez sur le bouton **Mettre à jour** .

## <a name="compression-rules"></a>Règles de compression
Ces tableaux décrivent le comportement de compression du CDN Azure pour chaque scénario.

> [!IMPORTANT]
> Pour les profils **Azure CDN fourni par Verizon** (Standard et Premium), seuls les fichiers éligibles sont compressés.  Pour être éligible pour la compression, un fichier doit :
> 
> * Être supérieur à 128 octets.
> * Être inférieur à 1 Mo.
> 
> Ces profils prennent en charge l’encodage **gzip** (GNU zip), **deflate**, **bzip2** ou **br** (Brotli). Pour l’encodage Brotli, la compression doit être effectuée uniquement à l'origine. Le client/navigateur doit envoyer la demande d’encodage Brotli et la ressource compressée doit d’abord avoir été compressée au niveau de l’origine. 

> [!IMPORTANT]
> Pour les profils **Azure CDN fourni par Akamai**, tous les fichiers sont éligibles à la compression. Cependant, un fichier doit être de type MIME et [configuré pour la compression](#enabling-compression).
> 
>Ces profils prennent uniquement en charge l’encodage **gzip**. Lorsqu’un point de terminaison de profil demande des fichiers encodés **gzip**, ils sont toujours demandés en provenance de l’origine, quelle que soit la requête du client. 

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compression désactivée ou le fichier n’est pas éligible pour la compression
| Format demandé par le client (via l’en-tête Accept-Encoding) | Format de fichier mis en cache | Réponse du CDN au client | Remarques |
| --- | --- | --- | --- |
| Compressé |Compressé |Compressé | |
| Compressé |Non compressé |Non compressé | |
| Compressé |Non mis en cache |Compressés ou non compressé |Dépend de la réponse d’origine |
| Non compressé |Compressé |Non compressé | |
| Non compressé |Non compressé |Non compressé | |
| Non compressé |Non mis en cache |Non compressé | |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>La compression est activée et le fichier est éligible pour la compression
| Format demandé par le client (via l’en-tête Accept-Encoding) | Format de fichier mis en cache | Réponse du CDN au client | Remarques |
| --- | --- | --- | --- |
| Compressé |Compressé |Compressé |Transcode CDN entre les formats pris en charge |
| Compressé |Non compressé |Compressé |CDN effectue la compression |
| Compressé |Non mis en cache |Compressé |CDN effectue la compression si l’origine renvoie le format non compressé.  **Azure CDN fourni par Verizon** transmet le fichier non compressé à la première demande, puis compresse et met en cache le fichier pour les demandes suivantes.  Les fichiers avec l’en-tête `Cache-Control: no-cache` ne sont jamais compressés. |
| Non compressé |Compressé |Non compressé |CDN effectue la décompression |
| Non compressé |Non compressé |Non compressé | |
| Non compressé |Non mis en cache |Non compressé | |

## <a name="media-services-cdn-compression"></a>Compression CDN Media Services
Pour les points de terminaison de diffusion en continu CDN de Media Services, la compression est activée par défaut pour les types de contenu suivants : 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml. 

Vous ne pouvez pas activer ni désactiver la compression des types mentionnés par le biais du portail Azure.  

## <a name="see-also"></a>Voir aussi
* [Résolution des problèmes de compression des fichiers CDN](cdn-troubleshoot-compression.md)    

