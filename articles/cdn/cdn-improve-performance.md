---
title: "Compression des fichiers dans Azure CDN pour améliorer les performances | Microsoft Docs"
description: "Découvrez comment améliorer la vitesse de transfert de fichiers et les performances de chargement de page en compressant vos fichiers dans Azure CDN."
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: mazha
ms.openlocfilehash: 743d1db803cdb58ae8fa37430ccffa10ca003f93
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Compression des fichiers dans Azure CDN pour améliorer les performances
La compression de fichiers est une méthode simple et efficace qui vise à améliorer la vitesse de transfert des fichiers et à accroître les performances de chargement des pages en réduisant la taille des fichiers avant leur envoi à partir du serveur. La compression de fichiers peut réduire les coûts de bande passante et offre à vos utilisateurs davantage de réactivité.

Il existe deux façons d’activer la compression de fichiers :

- Activer la compression sur votre serveur d’origine. Dans ce cas, le CDN fait transiter les fichiers compressés et les remet aux clients qui les demandent.
- Activer la compression directement sur les serveurs de périmètre CDN. Dans ce cas, le CDN compresse les fichiers et les sert aux utilisateurs finals, même s’ils ne sont pas compressés par le serveur d’origine.

> [!IMPORTANT]
> Les changements de configuration CDN peuvent prendre un certain temps pour se propager sur le réseau. Pour les profils du **CDN Azure fourni par Akamai** , la propagation s’effectue généralement en moins d’une minute.  Pour les profils du **CDN Azure fourni par Verizon**, la propagation s’effectue généralement dans un délai de 90 minutes. Si vous configurez la compression de votre point de terminaison CDN pour la première fois, patientez 1 à 2 heures avant de chercher à résoudre un problème, pour être certain que les paramètres de compression ont été propagés aux POP.
> 
> 

## <a name="enabling-compression"></a>Activation de la compression
Les niveaux Standard et Premium de CDN fournissent les mêmes fonctionnalités, mais l’interface utilisateur est différente. Pour plus d’informations sur les différences entre les niveaux Standard et Premium de CDN, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md).

### <a name="standard-tier"></a>Niveau standard
> [!NOTE]
> Cette section s’applique au profil **Azure CDN Standard fourni par Verizon** et au profil **Azure CDN Standard fourni par Akamai**.
> 
> 

1. Depuis la page de profil CDN, sélectionnez le point de terminaison CDN que vous souhaitez gérer.
   
    ![Points de terminaison du profil CDN](./media/cdn-file-compression/cdn-endpoints.png)
   
    La page du point de terminaison CDN s’ouvre.
2. Sélectionnez **Compression**.

    ![Sélection de fichiers CDN](./media/cdn-file-compression/cdn-compress-select-std.png)
   
    La page de compression s’ouvre.
3. Sélectionnez **Activer** pour activer la compression.
   
    ![Options de compression de fichiers CDN](./media/cdn-file-compression/cdn-compress-standard.png)
4. Utilisez les types MIME par défaut ou modifiez la liste en supprimant ou en ajoutant des types MIME.
   
   > [!TIP]
   > Bien que cela soit possible, il n’est pas recommandé d’appliquer la compression aux formats compressés. Par exemple, ZIP, MP3, MP4 ou JPG.
   > 
 
5. Une fois vos modifications effectuées, sélectionnez **Enregistrer**.

### <a name="premium-tier"></a>Niveau Premium
> [!NOTE]
> Cette section s’applique uniquement aux profils du **CDN Azure CDN Premium fourni par Verizon**.
> 

1. Dans la page de profil CDN, sélectionnez **Gérer**.
   
    ![Gestion de sélection CDN](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Le portail de gestion CDN s'ouvre.
2. Pointez sur l’onglet **HTTP volumineux**, puis pointez sur le menu volant **Paramètres de cache**. Sélectionnez **Compression**.

    ![Sélection de fichiers CDN](./media/cdn-file-compression/cdn-compress-select.png)
   
    Les options de compression sont affichées.
   
    ![Options de compression de fichiers CDN](./media/cdn-file-compression/cdn-compress-files.png)
3. Activez la compression en sélectionnant **Compression activée**. Entrez les types MIME que vous souhaitez compresser sous forme de liste délimitée par des virgules (sans espace) dans la zone **Types de fichiers**.
   
   > [!TIP]
   > Bien que cela soit possible, il n’est pas recommandé d’appliquer la compression aux formats compressés. Par exemple, ZIP, MP3, MP4 ou JPG.
   > 
    
4. Après avoir apporté vos modifications, sélectionnez **Mettre à jour**.

## <a name="compression-rules"></a>Règles de compression

### <a name="azure-cdn-from-verizon-profiles-both-standard-and-premium-tiers"></a>Azure CDN à partir de profils de Verizon (niveaux standard et premium)

Pour les profils **Azure CDN fourni par Verizon**, seuls les fichiers éligibles sont compressés. Pour être éligible pour la compression, un fichier doit :
- Être supérieur à 128 octets
- Être inférieur à 1 Mo
 
Ces profils prennent en charge les encodages de compression suivants :
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 
 
Si la requête prend en charge plusieurs types de compression, ces types de compression sont prioritaires sur la compression brotli.

Lorsqu’une requête pour un composant spécifie compression la brotli (en-tête HTTP `Accept-Encoding: br`) et que la requête cause une absence dans le cache, Azure CDN effectue une compression brotli de l’élément multimédia sur le serveur d’origine. Ensuite, le fichier compressé est servi directement à partir du cache.

### <a name="azure-cdn-from-akamai-profiles"></a>Azure CDN depuis les profils Akamai

Pour les profils **Azure CDN fourni par Akamai**, tous les fichiers sont éligibles à la compression. Cependant, un fichier doit être de type MIME et [configuré pour la compression](#enabling-compression).

Ces profils prennent en charge uniquement l’encodage par compression gzip. Lorsqu’un point de terminaison de profil demande des fichiers encodés gzip, ils sont toujours demandés en provenance de l’origine, quelle que soit la requête du client. 

## <a name="compression-behavior-tables"></a>Tables de comportement de compression
Les tableaux suivants décrivent le comportement de compression du CDN Azure pour chaque scénario :

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>La compression est désactivée ou le fichier n’est pas éligible pour la compression
| Format demandé par le client (via l’en-tête Accept-Encoding) | Format de fichier mis en cache | La réponse du CDN au client | Notes&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Compressé |Compressé |Compressé | |
| Compressé |Non compressé |Non compressé | |
| Compressé |Non mis en cache |Compressés ou non compressé |La réponse d’origine détermine si le CDN effectue une compression. |
| Non compressé |Compressé |Non compressé | |
| Non compressé |Non compressé |Non compressé | |
| Non compressé |Non mis en cache |Non compressé | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>La compression est activée et le fichier est éligible pour la compression
| Format demandé par le client (via l’en-tête Accept-Encoding) | Format de fichier mis en cache | Réponse du CDN au client | Notes |
| --- | --- | --- | --- |
| Compressé |Compressé |Compressé |Transcode CDN entre les formats pris en charge. |
| Compressé |Non compressé |Compressé |CDN effectue une compression. |
| Compressé |Non mis en cache |Compressé |CDN effectue une compression si l’origine renvoie un fichier non compressé. <br/>**Azure CDN fourni par Verizon** transmet le fichier non compressé à la première demande, puis compresse et met en cache le fichier pour les demandes suivantes. <br/>Les fichiers avec l’en-tête Cache-Control : no-cache ne sont jamais compressés. |
| Non compressé |Compressé |Non compressé |CDN effectue la décompression. |
| Non compressé |Non compressé |Non compressé | |
| Non compressé |Non mis en cache |Non compressé | |

## <a name="media-services-cdn-compression"></a>Compression CDN Media Services
Pour les points de terminaison activés pour la diffusion en continu CDN de Media Services, la compression est activée par défaut pour les types MIME suivants : 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>Voir aussi
* [Résolution des problèmes de compression des fichiers CDN](cdn-troubleshoot-compression.md)    

