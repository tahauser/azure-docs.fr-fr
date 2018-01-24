---
title: "Intégration au centre de données Azure Stack : publier des points de terminaison"
description: "Découvrez comment publier des points de terminaison Azure Stack dans votre centre de données"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 01/16/2018
ms.author: victorh
keywords: 
ms.openlocfilehash: 1cc74cb2214918d6bfd0c0827cf5d9832b84f317
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Intégration au centre de données Azure Stack : publier des points de terminaison

*S’applique à : systèmes intégrés Azure Stack*

Azure Stack définit différents points de terminaison (VIP - adresses IP virtuelles) pour ses rôles d’infrastructure. Ces adresses IP virtuelles sont allouées à partir du pool d’adresses IP publiques. Chaque adresse IP virtuelle est sécurisée à l’aide d’une liste de contrôle d’accès (ACL) dans la couche réseau à définition logicielle. Les listes ACL sont également utilisées dans les commutateurs physiques (TOR et BMC) pour renforcer la solution. Une entrée DNS est créée pour chaque point de terminaison dans la zone DNS externe spécifiée au moment du déploiement.


Le diagramme architectural suivant montre les différentes couches réseau et les listes ACL :

![Diagramme architectural](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Ports et protocoles (en entrée)

Les adresses IP virtuelles d’infrastructure requises pour la publication des points de terminaison Azure Stack sur des réseaux externes sont répertoriées dans le tableau suivant. La liste affiche chaque point de terminaison, le port requis et le protocole. Les points de terminaison requis pour les fournisseurs de ressources supplémentaires, le fournisseur de ressources SQL entre autres, sont traités dans la documentation de déploiement spécifique au fournisseur de ressources.

Les adresses IP virtuelles ne sont pas répertoriées car elles ne sont pas requises pour la publication Azure Stack.

> [!NOTE]
> Les adresses IP virtuelles de l’utilisateur sont dynamiques, définies par les utilisateurs eux-mêmes, sans contrôle de la part de l’opérateur Azure Stack.


|Point de terminaison (VIP)|Enregistrement A d’hôte DNS|Protocole|Ports|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portail (administrateur)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (administrateur)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portail (utilisateur)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (utilisateur)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Liste de révocation de certificat|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP et UDP|53|
|Key Vault (utilisateur)|*.vault.*&lt;region>.&lt;fqdn>*|TCP|443|
|Key Vault (administrateur)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|TCP|443|
|File d’attente de stockage|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Table de stockage|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>Ports et URL (en sortie)

Azure Stack prend en charge uniquement les serveurs proxy transparents. Dans un déploiement où un proxy transparent transfère les données vers un serveur proxy traditionnel, vous devez autoriser les URL et les ports suivants pour les communications sortantes :


|Objectif|URL|Protocole|Ports|
|---------|---------|---------|---------|
|Identité|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Syndication de Place de marché|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Correctif et mise à jour|https://&#42;.azureedge.net|HTTPS|443|
|Inscription|https://management.azure.com|HTTPS|443|
|Usage|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|

## <a name="firewall-publishing"></a>Publication de pare-feu

Les ports répertoriés dans la section précédente s’appliquent aux communications entrantes lors de la publication de services Azure Stack via un pare-feu existant.

Nous vous recommandons d’utiliser un dispositif de pare-feu pour sécuriser Azure Stack. Mais il ne s’agit pas d’une condition stricte. Bien que les pare-feu peuvent être utiles en cas d’attaques par déni de service distribué (DDOS) et d’inspection du contenu, ils peuvent également constituer un goulot d’étranglement au niveau du débit des services de stockage Azure comme les objets blob, les tables et les files d’attente.

Selon le modèle d’identité (Azure AD ou AD FS), la publication du point de terminaison AD FS peut être requise ou pas. Si un mode de déploiement déconnecté est utilisé, vous devez publier le point de terminaison AD FS. (Pour plus d’informations, consultez la rubrique sur l’identité de l’intégration du centre de données).

Les points de terminaison Azure Resource Manager (administrateur), portail administrateur et Key Vault (administrateur) ne nécessitent aucune publication externe. Cela dépend du scénario. Par exemple, en tant que fournisseur de services, vous voudrez limiter la surface de l’attaque et administrer uniquement Azure Stack depuis votre réseau et non à partir d’Internet.

Pour une entreprise, le réseau externe peut être le réseau d’entreprise existant. Dans un tel scénario, vous devez publier ces points de terminaison pour exécuter Azure Stack à partir du réseau d’entreprise.

## <a name="edge-firewall-scenario"></a>Scénario de pare-feu de périmètre

Dans un déploiement de périmètre, Azure Stack est déployé directement derrière le routeur de périmètre (fourni par le fournisseur de services Internet), avec ou sans un pare-feu placé devant lui.

![Diagramme architectural d’un déploiement de périmètre Azure Stack](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

En règle générale, les adresses IP routables publiques sont spécifiées pour le pool d’adresses IP virtuelles publiques au moment d’un déploiement de périmètre. Ce scénario permet à un utilisateur de bénéficier d’une expérience de cloud automatique complet et autocontrôlé, comme dans un cloud public de type comme Azure.

### <a name="using-nat"></a>Utilisation de NAT

Bien que cette méthode soit déconseillée en raison de la charge, vous pouvez utiliser la traduction d’adresses réseau (NAT) pour publier des points de terminaison. Pour une publication de point de terminaison entièrement contrôlée par les utilisateurs, cette approche nécessite une règle NAT par adresse IP virtuelle d’utilisateur, qui contient tous les ports qu'un utilisateur peut exploiter.

Autre élément à considérer : Azure ne prend pas en charge la configuration d’un tunnel VPN vers un point de terminaison à l’aide de NAT dans un scénario de cloud hybride avec Azure.

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>Scénario de pare-feu de réseau d’entreprise/intranet/de périmètre

Dans un déploiement intranet/d’entreprise/de périmètre, Azure Stack est déployé derrière un second pare-feu, qui fait généralement partie d’un réseau de périmètre (également appelé DMZ).

![Scénario de pare-feu Azure Stack](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

Si des adresses IP routables publiques ont été spécifiées pour le pool d’adresses IP virtuelles publiques d’Azure Stack, ces adresses appartiennent logiquement au réseau de périmètre et requièrent des règles de publication sur le pare-feu principal.

### <a name="using-nat"></a>Utilisation de NAT

Si des adresses IP routables non publiques sont utilisées pour le pool d’adresses IP virtuelles publiques d’Azure Stack, NAT est utilisé au niveau du pare-feu secondaire pour publier des points de terminaison Azure Stack. Dans ce scénario, vous devez configurer les règles de publication sur le pare-feu principal au-delà du périmètre ainsi que sur le pare-feu secondaire. Si vous souhaitez utiliser NAT, tenez compte des points suivants :

- NAT ajoute une surcharge lors de la gestion des règles de pare-feu car les utilisateurs contrôlent leurs propres points de terminaison et leurs propres règles de publication dans la pile de mise en réseau logicielle (SDN). Les utilisateurs doivent contacter l’opérateur Azure Stack pour que leurs adresses IP virtuelles soient publiées et pour mettre à jour la liste des ports.
- Bien que l’utilisation de NAT limite l’expérience de l’utilisateur, elle donne à l’opérateur un contrôle total sur les demandes de publication.
- Pour les scénarios de cloud hybride avec Azure, tenez compte du fait qu’Azure ne prend pas en charge la configuration d’un tunnel VPN vers un point de terminaison à l’aide de NAT.


## <a name="next-steps"></a>étapes suivantes

[Intégration au centre de données Azure Stack - Sécurité](azure-stack-integrate-security.md)