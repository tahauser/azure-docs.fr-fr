---
title: Mise en réseau dans Azure pour la récupération d’urgence Azure dans Azure Site Recovery | Microsoft Docs
description: Fournit une vue d’ensemble de la mise en réseau en vue d’une réplication des machines virtuelles Azure avec Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/26/2018
ms.author: sujayt
ms.openlocfilehash: 48be55632d9c1bece3f1a6e4f9ac12a68f9cb7ab
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Mise en réseau dans Azure pour la réplication d’Azure

>[!NOTE]
> La réplication Site Recovery pour les machines virtuelles Azure est actuellement en préversion.

Cet article fournit des instructions détaillées sur la mise en réseau quand vous répliquez et récupérez des machines virtuelles d’une région vers une autre avec [Azure Site Recovery](site-recovery-overview.md). 

## <a name="before-you-start"></a>Avant de commencer

Découvrez comment Site Recovery permet la récupération d’urgence pour [ce scénario](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Infrastructure réseau classique

Le diagramme suivant illustre un environnement Azure classique pour des applications qui s’exécutent sur des machines virtuelles Azure :

![environnement client](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Si vous utilisez Azure ExpressRoute ou une connexion VPN de votre réseau local vers Azure, l’environnement ressemble à ceci :

![environnement client](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Les réseaux sont généralement protégés à l’aide de pare-feu et de groupes de sécurité réseau. Les pare-feu se servent d’une liste blanche basée sur l’adresse IP ou l’URL pour contrôler la connectivité réseau. Les groupes de sécurité réseau appliquent des règles qui utilise les plages d’adresses IP pour contrôler la connectivité réseau.

>[!IMPORTANT]
> L’utilisation d’un proxy authentifié pour contrôler la connectivité réseau n’est pas pris en charge par Site Recovery, et la réplication ne peut pas être activée.


## <a name="outbound-connectivity-for-urls"></a>Connectivité sortante pour les URL

Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, autorisez ces URL Site Recovery :


**URL** | **Détails**  
--- | ---
*.blob.core.windows.net | Nécessaire pour que les données puissent être écrites dans le compte de stockage de cache dans la région source à partir de la machine virtuelle.
login.microsoftonline.com | Nécessaire pour l’autorisation et l’authentification aux URL du service Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Nécessaire pour que la communication du service Site Recovery puisse avoir lieu à partir de la machine virtuelle.
*.servicebus.windows.net | Nécessaire pour que les données de surveillance et de diagnostic Site Recovery puissent être écrites à partir de la machine virtuelle.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Connectivité sortante pour les plages d’adresses IP

Si vous utilisez des règles de groupe de sécurité réseau, de proxy ou de pare-feu basées sur une adresse IP pour contrôler la connectivité sortante, ces plages d’adresses IP doivent être autorisées.

- Toutes les plages d’adresses IP qui correspondent à l’emplacement source.
    - Vous pouvez télécharger les [plages d’adresses IP](https://www.microsoft.com/download/confirmation.aspx?id=41653).
    - Vous devez autoriser ces adresses pour que les données puissent être écrites dans le compte de stockage de cache, à partir de la machine virtuelle.
- Toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.
    - Si de nouvelles adresses sont ajoutées ultérieurement aux plages d’adresses IP Office 365, vous devez créer des règles de groupe de sécurité réseau.
- Adresses IP de points de terminaison du service Site Recovery. Elles sont disponibles dans un [fichier XML](https://aka.ms/site-recovery-public-ips), et dépendent de l’emplacement de votre cible.
-  Pour créer automatiquement les règles requises sur le groupe de sécurité réseau, vous pouvez [télécharger et utiliser ce script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702). 
- Nous vous recommandons de créer les règles de groupe de sécurité réseau requises sur un groupe de sécurité réseau de test, et de vérifier qu’il n’y a aucun problème avant de créer les règles sur un groupe de sécurité réseau de production.
- Pour créer le nombre nécessaire de règles de groupe de sécurité réseau, vérifiez que votre abonnement figure dans la liste verte. Contactez le support Azure pour augmenter la limite du nombre de règles de groupe de sécurité réseau dans votre abonnement.

Les plages d’adresses IP sont les suivantes :

>
   **Cible** | **IP Site Recovery** |  **Adresse IP de surveillance Site Recovery**
   --- | --- | ---
   Est de l'Asie | 52.175.17.132 | 13.94.47.61
   Asie du Sud-Est | 52.187.58.193 | 13.76.179.223
   Inde centrale | 52.172.187.37 | 104.211.98.185
   Inde du Sud | 52.172.46.220 | 104.211.224.190
   Centre-Nord des États-Unis | 23.96.195.247 | 168.62.249.226
   Europe du Nord | 40.69.212.238 | 52.169.18.8
   Europe de l'Ouest | 52.166.13.64 | 40.68.93.145
   Est des États-Unis | 13.82.88.226 | 104.45.147.24
   États-Unis de l’Ouest | 40.83.179.48 | 104.40.26.199
   États-Unis - partie centrale méridionale | 13.84.148.14 | 104.210.146.250
   Centre des États-Unis | 40.69.144.231 | 52.165.34.144
   Est des États-Unis 2 | 52.184.158.163 | 40.79.44.59
   Est du Japon | 52.185.150.140 | 138.91.1.105
   Ouest du Japon | 52.175.146.69 | 138.91.17.38
   Sud du Brésil | 191.234.185.172 | 23.97.97.36
   Est de l’Australie | 104.210.113.114 | 191.239.64.144
   Sud-est de l’Australie | 13.70.159.158 | 191.239.160.45
   Centre du Canada | 52.228.36.192 | 40.85.226.62
   Est du Canada | 52.229.125.98 | 40.86.225.142
   Centre-Ouest des États-Unis | 52.161.20.168 | 13.78.149.209
   Ouest des États-Unis 2 | 52.183.45.166 | 13.66.228.204
   Ouest du Royaume-Uni | 51.141.3.203 | 51.141.14.113
   Sud du Royaume-Uni | 51.140.43.158 | 51.140.189.52
   Sud du Royaume-Uni 2 | 13.87.37.4| 13.87.34.139
   Nord du Royaume-Uni | 51.142.209.167 | 13.87.102.68
   Centre de la Corée | 52.231.28.253 | 52.231.32.85
   Corée du Sud | 52.231.298.185 | 52.231.200.144
   
   
  

## <a name="example-nsg-configuration"></a>Exemple de configuration de groupe de sécurité réseau

Cet exemple montre comment configurer des règles de groupes de sécurité réseau pour une machine virtuelle à répliquer. 

- Si vous utilisez des règles de groupe de sécurité réseau pour contrôler la connectivité sortante, utilisez des règles « Autoriser le trafic HTTPS sortant » pour toutes les plages d’adresses IP requises.
- L’exemple part du principe que « Est des États-Unis » est l’emplacement source de la machine virtuelle, et que « Centre des États-Unis » en est l’emplacement cible.

### <a name="nsg-rules---east-us"></a>Règles de groupe de sécurité réseau - Est des États-Unis

1. Créez des règles qui correspondent aux [plages d’adresses IP d’Est des États-Unis](https://www.microsoft.com/download/confirmation.aspx?id=41653). Ceci est nécessaire pour que les données puissent être écrites dans le compte de stockage de cache à partir de la machine virtuelle.
2. Créez des règles pour toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.
3. Créez des règles qui correspondent à l’emplacement cible :

   **Lieu** | **Adresse IP Site Recovery** |  **Adresse IP de surveillance Site Recovery**
    --- | --- | ---
   Centre des États-Unis | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Règles de groupe de sécurité réseau - Centre des États-Unis 

Ces règles sont nécessaires pour que la réplication puisse être activée de la région cible vers la région source après le basculement :

* Règles qui correspondent aux [plages d’adresses IP de Centre des États-Unis](https://www.microsoft.com/download/confirmation.aspx?id=41653). Ces règles sont nécessaires pour que les données puissent être écrites dans le compte de stockage de cache à partir de la machine virtuelle.

* Règles pour toutes les plages d’adresses IP qui correspondent aux [points de terminaison IP V4 d’authentification et d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity) Office 365.

* Règles qui correspondent à l’emplacement source :
    - Est des États-Unis
    - Adresse IP Site Recovery : 13.82.88.226
    - Adresse IP de surveillance Site Recovery : 104.45.147.24


## <a name="expressroutevpn"></a>ExpressRoute/VPN 

Si vous avez une connexion ExpressRoute ou VPN entre l’infrastructure locale et l’emplacement Azure, suivez les instructions de cette section.

### <a name="forced-tunneling"></a>Tunneling forcé

En règle générale, vous définissez un itinéraire par défaut (0.0.0.0/0) qui force le trafic Internet sortant à circuler via l’emplacement local. Nous ne recommandons pas cette configuration. Le trafic de réplication et la communication du service Site Recovery ne doivent pas quitter la limite Azure. La solution consiste à ajouter des itinéraires définis par l’utilisateur pour [ces plages d’adresses IP](#outbound-connectivity-for-azure-site-recovery-ip-ranges) afin que le trafic de réplication n’accède pas à l’infrastructure locale.

### <a name="connectivity"></a>Connectivité 

Suivez ces instructions pour les connexions entre l’emplacement cible et l’emplacement local :
- Si votre application doit se connecter aux machines locales ou s’il existe des clients qui se connectent à l’application à partir de l’infrastructure locale par le biais d’un VPN/ExpressRoute, vérifiez que vous avez au moins une [connexion site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) entre votre région Azure cible et le centre de données local.

- Si vous prévoyez qu’un trafic élevé circulera entre votre région Azure cible et le centre de données local, vous devez créer une autre [connexion ExpressRoute](../expressroute/expressroute-introduction.md) entre la région Azure cible et le centre de données local.

- Si vous souhaitez conserver les adresses IP des machines virtuelles après leur basculement, laissez la connexion site à site/ExpressRoute de la région cible dans un état déconnecté. Ainsi, il n’y aura aucun conflit de plage entre les plages d’adresses IP de la région source et celles de la région cible.

### <a name="expressroute-configuration"></a>Configuration de la connexion ExpressRoute
Suivez ces bonnes pratiques pour la configuration d’ExpressRoute :

- Créez un circuit ExpressRoute dans les régions source et cible. Vous devez ensuite créer une connexion entre :
    - Le réseau virtuel source et le réseau local via le circuit ExpressRoute dans la zone source.
    - Le réseau virtuel cible et le réseau local via le circuit ExpressRoute dans la région cible.


- Dans le cadre de la norme ExpressRoute, vous pouvez créer des circuits dans la même région géopolitique. Pour créer des circuits ExpressRoute dans des régions géopolitiques différentes, Azure ExpressRoute Premium est obligatoire, ce qui implique un coût incrémentiel. (Si vous utilisez déjà ExpressRoute Premium, il n’y a aucun frais supplémentaires.) Pour plus d’informations, consultez le [document relatifs aux emplacements ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) et la [tarification ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

- Nous vous recommandons d’utiliser des plages d’adresses IP différentes dans les régions source et cible. Le circuit ExpressRoute ne pourra pas se connecter en même temps à deux réseaux virtuels Azure ayant les mêmes plages d’adresses IP.

- Vous pouvez créer des réseaux virtuels avec les mêmes plages d’adresses IP dans les deux régions, puis créer des circuits ExpressRoute dans les deux régions. En cas d’événement de basculement, déconnectez le circuit du réseau virtuel source et connectez le circuit sur le réseau virtuel cible.

 >[!IMPORTANT]
 > Si la région principale est complètement en panne, l’opération de déconnexion peut échouer. Cela empêchera le réseau virtuel cible d’obtenir la connectivité ExpressRoute.

## <a name="next-steps"></a>Étapes suivantes
Commencez à protéger vos charges de travail en [répliquant des machines virtuelles Azure](site-recovery-azure-to-azure.md).
