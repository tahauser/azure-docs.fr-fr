---
title: "Présentation des environnements Azure App Service"
description: "Vue d’ensemble rapide des environnements Azure App Service"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: 2a619943abaf8835e591872cba0ed046d4c6c4a9
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="introduction-to-app-service-environments"></a>Présentation des environnements App Service #
 
## <a name="overview"></a>Vue d'ensemble ##

Azure App Service Environment est une fonctionnalité d’Azure App Service qui fournit un environnement totalement isolé et dédié pour l’exécution sécurisée de vos applications App Service à grande échelle. Cette fonctionnalité peut héberger vos applications web, [applications mobiles][mobileapps], applications API et [fonctions][Functions].

Les environnements App Service (ASE) conviennent aux charges de travail d’application qui nécessitent :

- Une très grande échelle.
- Une isolation et un accès réseau sécurisé.
- Une utilisation élevée de la mémoire.

Les clients peuvent créer plusieurs environnements App Service au sein d’une même région Azure ou dans plusieurs régions Azure. Grâce à cette souplesse, les environnements ASE sont parfaits pour l’évolution horizontale des niveaux d’application sans état pour la prise en charge de lourdes charges de travail RPS.

Les environnements ASE sont isolés de façon à exécuter les applications d’un seul client et sont toujours déployés dans un réseau virtuel. Les clients peuvent contrôler précisément le trafic réseau entrant et sortant des applications. Les applications peuvent établir des connexions sécurisées à haute vitesse aux ressources d’entreprise locales par le biais de réseaux privés virtuels.

* Les environnements App Service autorisent l’hébergement d’application à grande échelle avec un accès réseau sécurisé. Pour plus d’informations, consultez l’[Immersion AzureCon](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) sur les environnements App Service.
* Vous pouvez utiliser plusieurs environnements App Service pour une mise à l’échelle horizontale. Pour plus d’informations, consultez [Mise à l’échelle géolocalisée avec les environnements App Service](app-service-app-service-environment-geo-distributed-scale.md).
* Vous pouvez utiliser des environnements App Service pour configurer l’architecture de sécurité, comme indiqué dans l’Immersion AzureCon. Pour découvrir comment a été configurée l’architecture de sécurité illustrée dans l’Immersion AzureCon, consultez l’[article sur l’implémentation d’une architecture de sécurité en couches](app-service-app-service-environment-layered-security.md) avec les environnements App Service.
* L’accès aux applications qui s’exécutent sur des environnements App Service peut être contrôlé par des appareils en amont tels que les pare-feu d’applications web (WAF). Pour plus d’informations, consultez [Configuration d’un pare-feu d’applications Web (WAF) pour un environnement App Service](app-service-app-service-environment-web-application-firewall.md).

## <a name="dedicated-environment"></a>Environnement dédié ##

Un environnement App Service est dédié exclusivement à un abonnement et peut héberger 100 instances. Il peut s’agir de 100 instances d’un même plan App Service ou de 100 plans App Service à instance unique, ou de toutes les combinaisons possibles des deux.

Un environnement App Service est composé de frontends et de workers. Les frontends sont responsables de la terminaison du protocole HTTP/HTTPS et de l’équilibrage de charge automatique des demandes d’application dans un environnement App Service. Ils sont ajoutés automatiquement à mesure que les plans App Service dans l’environnement App Service augmentent leur échelle.

Les workers sont des rôles qui hébergent des applications clientes. Ils sont disponibles dans trois tailles fixes :

* Un processeur virtuel/3,5 Go de RAM
* Deux processeurs virtuels/7 Go de RAM
* Quatre processeurs virtuels/14 Go de RAM

Les clients n’ont pas besoin de gérer les frontends et les workers. Toute infrastructure est automatiquement ajoutée à mesure que les clients augmentent l’échelle de leurs plans App Service. À mesure que des plans App Service sont créés ou mis à l’échelle dans un environnement App Service, l’infrastructure requise est ajoutée ou supprimée en conséquence.

Un tarif fixe mensuel couvre l’infrastructure d’un environnement App Service et ne change pas avec la taille de l’environnement. Chaque processeur virtuel de plan App Service présente aussi un coût. Toutes les applications hébergées dans un environnement App Service sont dans la référence (SKU) de prix Isolée. Pour plus d’informations sur les tarifs d’un environnement App Service, consultez la page de [tarification App Service][Pricing] et passez en revue les options disponibles pour les environnements App Service.

## <a name="virtual-network-support"></a>Prise en charge des réseaux virtuels ##

Un environnement App Service peut être créé uniquement dans un réseau virtuel Azure Resource Manager. Pour en savoir plus sur les réseaux virtuels Azure, consultez le [FAQ sur les réseaux virtuels Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Un environnement App Service existe toujours dans un réseau virtuel, et plus précisément un sous-réseau d’un réseau virtuel. Vous pouvez utiliser les fonctionnalités de sécurité des réseaux virtuels pour contrôler les communications réseau entrantes et sortantes de vos applications.

Un environnement App Service peut être soit accessible sur Internet avec une adresse IP publique, soit accessible en interne avec uniquement une adresse d’équilibreur de charge interne (ILB) Azure.

Les [groupes de sécurité réseau][NSGs] limitent les communications réseau entrantes vers le sous-réseau sur lequel réside l’environnement App Service. Vous pouvez utiliser des groupes de sécurité réseau pour exécuter des applications derrière des appareils et services en amont tels que des pare-feu d’applications web et des fournisseurs SaaS réseau.

Les applications doivent souvent accéder à des ressources d'entreprise telles que des bases de données internes et des services web. Si vous déployez l’environnement App Service sur un réseau virtuel qui a une connexion VPN au réseau local, les applications de l’environnement App Service peuvent accéder aux ressources locales. Cela est vrai, que le réseau privé virtuel soit un VPN [de site à site](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) ou un VPN [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/).

Pour plus d’informations sur le fonctionnement des environnements App Service avec des réseaux virtuels et des réseaux locaux, consultez [Considérations sur les réseaux d’environnement App Service][ASENetwork].

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>Environnement App Service v1 ##

L’environnement App Service est disponible en deux versions : ASEv1 et ASEv2. Les informations précédentes sont basées sur ASEv2. Cette section montre les différences entre ASEv1 et ASEv2. 

Dans ASEv1, vous devez gérer toutes les ressources manuellement. Celles-ci incluent les frontends, les workers et les adresses IP utilisées pour le protocole SSL basé sur IP. Pour pouvoir augmenter l’échelle de votre plan App Service, vous devez d’abord augmenter celle du pool de workers où vous voulez héberger le plan.

Les versions ASEv1 et ASEv2 utilisent un modèle de tarification différent. Dans ASEv1, vous payez pour chaque processeur virtuel alloué. Cela inclut les processeurs virtuels utilisés pour les frontends ou les workers qui n’hébergent pas de charges de travail. Dans la version ASEv1, la taille d’échelle maximale par défaut d’un environnement App Service correspond à un total de 55 hôtes, dont les workers et les frontends. L’un des avantages d’un ASEv1 est qu’il peut être déployé sur un réseau virtuel classique et sur un réseau virtuel Resource Manager. Pour plus d’informations sur ASEv1, consultez [Présentation de l’environnement App Service v1][ASEv1Intro].

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
