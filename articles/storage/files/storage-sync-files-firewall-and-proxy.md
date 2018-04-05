---
title: Paramètres de pare-feu et de proxy locaux d’Azure File Sync | Microsoft Docs
description: Configuration de réseau local d’Azure File Sync
services: storage
documentationcenter: ''
author: fauhse
manager: klaasl
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 81425c6ac4e463bd4242328206bd43ce78a1105a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Paramètres de proxy et de pare-feu d’Azure File Sync
Azure File Sync connecte vos serveurs locaux à Azure Files, activant des fonctionnalités de synchronisation multisite et de hiérarchisation cloud. Pour cela, un serveur local doit donc être connecté à Internet. Un administrateur informatique doit déterminer la meilleure voie d’accès aux services cloud Azure pour le serveur.

Cet article fournit des informations sur les exigences spécifiques et les options disponibles pour connecter un serveur en toute sécurité à Azure File Sync.

## <a name="overview"></a>Vue d'ensemble
Azure File Sync fait office de service d’orchestration entre votre serveur Windows Server, votre partage de fichiers Azure et plusieurs autres services Azure pour synchroniser les données définies dans votre groupe de synchronisation. Pour qu’Azure File Sync fonctionne correctement, vous devez configurer vos serveurs de manière qu’ils puissent communiquer avec les services Azure suivants :

- Stockage Azure
- Azure File Sync
- Azure Resource Manager
- Services d’authentification

> [!Note]  
> Comme l’agent Azure File Sync sur Windows Server initialise toutes les requêtes vers les services cloud, seul le trafic sortant est à prendre en compte du point de vue du pare-feu. <br /> Aucun service Azure n’établit de connexion avec l’agent Azure File Sync.


## <a name="ports"></a>Ports
Azure File Sync déplace les métadonnées et données de fichiers exclusivement via le protocole HTTPS et nécessite que le port 443 soit ouvert en sortie.
Par conséquent, tout le trafic est chiffré.

## <a name="networks-and-special-connections-to-azure"></a>Réseaux et connexions spéciales à Azure
L’agent Azure File Sync ne présente aucune exigence particulière concernant les canaux spéciaux, comme [ExpressRoute](../../expressroute/expressroute-introduction.md), vers Azure.

Azure File Sync prend en charge n’importe quel mode d’accès à Azure disponible, s’adaptant automatiquement aux différentes caractéristiques de réseau, telles que la bande passante et la latence, tout en offrant un contrôle d’administration pour optimiser les paramètres. Toutes les fonctionnalités ne sont pas disponibles pour le moment. Si vous souhaitez configurer un comportement spécifique, indiquez-le nous sur le [forum UserVoice consacré à Azure Files](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
Actuellement, Azure File Sync prend en charge les paramètres de proxy au niveau des ordinateurs. Cette configuration de proxy est transparente pour l’agent Azure File Sync, car l’ensemble du trafic du serveur est acheminé à travers ce proxy.

Les paramètres de proxy propres aux applications sont en cours de développement et seront pris en charge dans une prochaine version de l’agent Azure File Sync. Il sera ainsi possible de configurer un proxy spécifiquement pour le trafic d’Azure File Sync.

## <a name="firewall"></a>Pare-feu
Comme mentionné plus haut, le port 443 doit être ouvert en sortie. Selon les stratégies en place dans votre centre de données, votre succursale ou votre région, il se peut que vous souhaitiez ou deviez restreindre le trafic sur ce port à des domaines spécifiques.

Le tableau suivant décrit les domaines requis pour la communication :

| de diffusion en continu | Domaine | Usage |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | N’importe quel appel utilisateur (par exemple, PowerShell) est acheminé vers/à travers cette URL, y compris l’appel pour l’inscription initiale du serveur. |
| **Azure Active Directory** | https://login.windows.net | Les appels à Azure Resource Manager doivent être effectués par un utilisateur authentifié. Cette URL est utilisée pour l’authentification utilisateur. |
| **Azure Active Directory** | https://graph.windows.net/ | Dans le cadre du déploiement d’Azure File Sync, un principal de service est créé dans l’annuaire Azure Active Directory de l’abonnement. Cette URL est utilisée pour cela. Le principal créé sert à déléguer un ensemble minimal de droits au service Azure File Sync. L’utilisateur qui effectue la configuration initiale d’Azure File Sync doit être un utilisateur authentifié avec des privilèges de propriétaire d’abonnement. |
| **Stockage Azure** | &ast;.core.windows.net | Quand le serveur télécharge un fichier, il effectue ce déplacement de données plus efficacement lorsqu’il communique directement avec le partage de fichiers Azure dans le compte de stockage. Le serveur présente une clé SAS qui permet uniquement un accès ciblé au partage de fichiers. |
| **Azure File Sync** | &ast;.one.microsoft.com | Après l’inscription initiale du serveur, le serveur reçoit une URL régionale pour l’instance du service Azure File Sync dans cette région. Le serveur peut utiliser cette URL pour communiquer directement et plus efficacement avec l’instance qui gère sa synchronisation. |

> [!Important]
> Quand le trafic vers &ast;.one.microsoft.com est autorisé, la destination du trafic à partir du serveur n’est pas limitée au service de synchronisation. Les sous-domaines incluent de nombreux autres services Microsoft.

Si &ast;.one.microsoft.com est trop vaste, vous pouvez limiter les communications du serveur en autorisant uniquement des instances régionales explicites du service Azure Files Sync. Les instances à définir dépendent de la région du service de synchronisation de stockage où vous avez déployé et inscrit le serveur. C’est cette région que vous devez autoriser pour le serveur. D’autres URL seront bientôt disponibles afin d’offrir de nouvelles fonctionnalités de continuité d’activité. 

| Région | URL du point de terminaison régional d’Azure File Sync |
|--------|---------------------------------------|
| Est de l’Australie | https://kailani-aue.one.microsoft.com |
| Centre du Canada | https://kailani-cac.one.microsoft.com |
| Est des États-Unis | https://kailani1.one.microsoft.com |
| Asie du Sud-Est | https://kailani10.one.microsoft.com |
| Sud du Royaume-Uni | https://kailani-uks.one.microsoft.com |
| Europe de l'Ouest | https://kailani6.one.microsoft.com |
| États-Unis de l’Ouest | https://kailani.one.microsoft.com |

> [!Important]
> Si vous définissez ces règles de pare-feu détaillées, consultez régulièrement ce document et mettez à jour vos règles de pare-feu pour éviter les interruptions de service dues à des listes d’URL obsolètes ou incomplètes.

## <a name="summary-and-risk-limitation"></a>Résumé et limitation des risques
La liste fournie plus haut contient les URL avec lesquelles Azure File Sync communique actuellement. Les pare-feu doivent être en mesure d’autoriser le trafic sortant en direction de ces domaines, ainsi que les réponses provenant de ceux-ci. Microsoft s’efforce de maintenir cette liste à jour.

La configuration de règles de pare-feu restreignant les domaines peut permettre de renforcer la sécurité. Si vous utilisez ces configurations de pare-feu, gardez à l’esprit que des URL seront ajoutées et modifiées au fil du temps. Par conséquent, il est recommandé de consulter les tableaux de ce document dans le cadre d’un déploiement-test de la dernière version de l’agent Azure File Sync. Vous pourrez ainsi vous assurer que votre pare-feu est configuré de manière à autoriser le trafic vers les domaines requis par l’agent le plus récent.

## <a name="next-steps"></a>Étapes suivantes
- [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)
- [Déployer Azure File Sync (préversion)](storage-sync-files-deployment-guide.md)
