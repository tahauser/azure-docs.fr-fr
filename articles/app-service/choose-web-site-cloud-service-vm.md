---
title: Comparaison entre Azure App Service, Virtual Machines, Service Fabric et Cloud Services | Microsoft Docs
description: Découvrez comment choisir entre Azure App Service, Virtual Machines, Service Fabric et Cloud Services pour l’hébergement d’applications web.
services: app-service\web, virtual-machines, cloud-services
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: f930cec984a8b92e00ec613ce3bba91a40518911
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparaison entre Azure App Service, Virtual Machines, Service Fabric et Cloud Services
## <a name="overview"></a>Vue d'ensemble
Azure propose plusieurs manières d’héberger des sites web : [Azure App Service][Azure App Service], [Machines virtuelles][Virtual Machines], [Service Fabric][Service Fabric] et [Services Cloud][Cloud Services]. Cet article vous aidera à comprendre ces différentes possibilités et à faire le bon choix en fonction de votre application web.

Azure App Service est le meilleur choix pour la plupart des applications web. Le déploiement et la gestion sont intégrés à la plateforme, les sites peuvent rapidement gérer des volumes importants de trafic et le gestionnaire d'équilibrage de charge et de trafic assurent une haute disponibilité. Vous pouvez facilement déplacer des sites existants vers Azure App Service avec un [outil de migration en ligne](https://www.migratetoazure.net/), utiliser une application open source de la galerie d’applications web ou bien créer un site avec l’infrastructure et les outils de votre choix. La fonctionnalité [WebJobs][WebJobs] simplifie l’ajout du traitement de tâches en arrière-plan à votre application web App Service.

Service Fabric est un bon choix si vous créez une application ou réécrivez une application existante pour utiliser une architecture de microservice. Les applications s’exécutent sur un pool partagé d’ordinateurs. Elles peuvent prendre en charge un petit nombre d’ordinateurs puis être déployées à grande échelle sur des centaines ou des milliers d’ordinateurs en fonction des besoins. Les services avec état permettent de stocker l’état des applications de manière cohérente et fiable, et Service Fabric gère automatiquement le partitionnement, la mise à l’échelle et la disponibilité des services.  Service Fabric prend également en charge WebAPI avec Open Web Interface for .NET (OWIN) et ASP.NET Core.  Par rapport à App Service, Service Fabric offre un meilleur contrôle de l’infrastructure sous-jacente ou un accès direct à cette dernière. Vous pouvez accéder à distance à vos serveurs ou configurer des tâches de démarrage du serveur. Cloud Services est similaire à Service Fabric en termes de niveau de contrôle et de facilité d’utilisation, mais il s’agit désormais d’un service hérité et Service Fabric est recommandé pour un nouveau développement.

Si vous avez une application qui nécessite des modifications importantes pour pouvoir fonctionner dans App Service ou Service Fabric, vous pouvez choisir Virtual Machines pour simplifier la migration vers le cloud. Cependant, la configuration, la sécurisation et la gestion des machines virtuelles nécessitent bien plus de temps et de compétences que l’utilisation d’Azure App Service et Service Fabric. Si vous envisagez d'utiliser Azure Virtual Machines, tenez bien compte du temps de maintenance nécessaire pour mettre à jour, mettre à niveau et gérer votre environnement de machine virtuelle. Les machines virtuelles Azure sont de type Infrastructure en tant que service (Infrastructure-as-a-Service, (IaaS)), tandis que App Service et Service Fabric sont de type plateforme en tant que service (Platform-as-a-Service (Paas)). 

## <a name="features"></a>Comparaison des fonctionnalités
Le tableau suivant compare les fonctionnalités d’Azure App Service, Azure Cloud Services, Azure Virtual Machines et Service Fabric afin de vous aider dans votre choix. Pour obtenir des informations à jour sur le contrat SLA de chaque option, consultez la page des [contrats de niveau de service Azure](https://azure.microsoft.com/support/legal/sla/).

| Fonctionnalité | Azure App Service (applications web) | Azure Cloud Services (rôles Web) | Virtual Machines | Service Fabric | Notes |
| --- | --- | --- | --- | --- | --- |
| Déploiement presque instantané |X | | |X |Le déploiement d’une application ou de la mise à jour d’une application sur un service cloud, ou la création d’une machine virtuelle prend au moins quelques minutes. Le déploiement d’une application vers une application web se fait en quelques secondes. |
| Évolution vers des machines plus volumineuses sans redéploiement |X | | |X | |
| Les instances de serveur web partagent le contenu et la configuration, ce qui fait que vous n'avez pas à refaire le déploiement ou la configuration en cas d'extension. |X | | |X | |
| Plusieurs environnements de déploiement (de production et intermédiaire) |X |X | |X |Service Fabric vous permet d’avoir plusieurs environnements pour vos applications ou de déployer différentes versions de votre application côte à côte. |
| Gestion automatique des mises à jour du système d'exploitation |X |X | | |Partiellement via l’application d’orchestration des correctifs, et complètement à l’avenir. |
| Migration transparente entre les plateformes (de 32 à 64 bits) |X |X | | | |
| Déploiement de code avec GIT, FTP |X | |X | | |
| Déploiement de code avec Web Deploy |X | |X | |Cloud Services prend en charge l'utilisation de Web Deploy pour déployer des mises à jour sur des instances de rôle. Cependant, vous ne pouvez pas l'utiliser pour le déploiement initial du rôle, et si vous utilisez Web Deploy pour une mise à jour, vous devez effectuer le déploiement séparément pour chaque instance du rôle. Plusieurs instances sont nécessaires afin de répondre aux exigences du contrat SLA Cloud Services pour les environnements de production. |
| Prise en charge de WebMatrix |X | |X | | |
| Accès aux services tels que Service Bus, Storage et Base de données SQL |X |X |X |X | |
| Hébergement des services Web ou niveaux d'une architecture multiniveau |X |X |X |X | |
| Hébergement du niveau intermédiaire d'une architecture multiniveau |X |X |X |X |Les applications web App Service peuvent facilement héberger un niveau intermédiaire d’API REST et la fonctionnalité [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) peut héberger des tâches de traitement en arrière-plan. Vous pouvez exécuter WebJobs sur un site web dédié pour obtenir une flexibilité indépendante pour cette couche. |
| Prise en charge intégrée de MySQL-as-a-service |X |X | | | |
| Prise en charge d'ASP.NET, d'ASP, de Node.js, de PHP et de Python |X |X |X |X |Service Fabric prend en charge la création d’un serveur web frontal à l’aide [d’ASP.NET 5](../service-fabric/service-fabric-reliable-services-communication-aspnetcore.md), ou vous pouvez déployer n’importe quel type d’application (Node.js, Java, etc.) en tant que [fichier exécutable invité](../service-fabric/service-fabric-guest-executables-introduction.md). |
| Montée en charge vers plusieurs instances sans redéploiement |X |X |X |X |Bien que les machines virtuelles puissent évoluer vers plusieurs instances, les services exécutés sur celles-ci doivent être écrits de manière à prendre en charge cette montée en charge. Vous devez configurer un programme d'équilibrage de la charge pour acheminer les demandes entre les machines et créer un groupe d'affinités afin d'empêcher les redémarrages simultanés de toutes les instances en cas de maintenance ou de panne matérielle. |
| Prise en charge de SSL |X |X |X |X |Pour les applications web App Service, le protocole SSL pour les noms de domaine personnalisés est pris en charge uniquement en mode De base et Standard. Pour plus d’informations sur l’utilisation de SSL avec les applications web, consultez la page [Configuration d’un certificat SSL pour un site web Azure](app-service-web-tutorial-custom-ssl.md). |
| Intégration de Visual Studio |X |X |X |X | |
| Débogage à distance |X |X |X | | |
| Déploiement de code avec TFS |X |X |X |X | |
| Isolation du réseau avec [Azure Virtual Network](/azure/virtual-network/) |X |X |X |X |Voir aussi [Intégration au réseau virtuel de Sites Web Azure](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| Prise en charge d' [Azure Traffic Manager](/azure/traffic-manager/) |X |X |X |X | |
| Surveillance intégrée des points de terminaison |X |X |X | | |
| Accès aux serveurs via le Bureau à distance | |X |X |X | |
| Installez n'importe quel MSI personnalisé | |X |X |X |Service Fabric vous permet d’héberger n’importe quel fichier exécutable en tant que [fichier exécutable invité](../service-fabric/service-fabric-guest-executables-introduction.md) ou vous pouvez installer n’importe quelle application sur les machines virtuelles. |
| Possibilité de définir et d'exécuter des tâches de démarrage | |X |X |X | |
| Possibilité d'écouter des événements ETW | |X |X |X | |

## <a name="scenarios"></a>Scénarios et recommandations
Voici quelques scénarios d'application courants avec des recommandations sur l'option d'hébergement web Azure la plus appropriée.

* [J’ai besoin d’un site web frontal avec traitement en arrière-plan et base de données backend pour exécuter des applications métiers intégrées à des ressources locales.](#onprem)
* [J’ai besoin d’un hébergement pour le site web de ma société, qui soit fiable, extensible et de portée mondiale.](#corp)
* [J’ai une application IIS6 qui s’exécute sur Windows Server 2003.](#iis6)
* [Je dirige une PME et je recherche une méthode peu onéreuse pour héberger mon site web, tout en gardant à l’esprit la future croissance de mon entreprise.](#smallbusiness)
* [Je suis un concepteur web/graphiste et je souhaite concevoir et développer des sites web pour mes clients.](#designer)
* [Je migre actuellement mon application multiniveau et un serveur web frontal dans le cloud.](#multitier)
* [Mon application dépend d’environnements Windows ou Linux hautement personnalisés et je souhaite la transférer dans le cloud.](#custom)
* [Mon site utilise des logiciels open source et je souhaite l’héberger dans Azure.](#oss)
* [Je possède une application métier qui doit se connecter au réseau de l’entreprise.](#lob)
* [Je souhaite héberger une API REST ou un service web pour des clients mobiles.](#mobile)

### <a id="onprem"></a>J’ai besoin d’un site web frontal avec traitement en arrière-plan et base de données backend pour exécuter des applications métiers intégrées à des ressources locales.
Azure App Service est une excellente solution pour l’hébergement d’applications métier complexes. Elle vous permet de développer des applications automatiquement mises à l'échelle sur les plateformes avec équilibrage de la charge. Elles sont aussi sécurisées grâce à Active Directory et se connectent à vos ressources locales. Elle simplifie la gestion de ces applications grâce au portail et aux API et vous permet de savoir comment vos clients les utilisent avec les outils d'analyse. La fonctionnalité [Webjobs][Webjobs] vous permet d’exécuter des processus et des tâches en arrière-plan dans la couche web. La connectivité hybride et les fonctionnalités VNET simplifient la connexion aux ressources locales. Azure App Service offre des contrats SLA pour des applications web à 0,001 % d’erreur et offre les possibilités suivantes :

* Exécution fiable de vos applications sur une plateforme cloud avec corrections et mises à jour automatiques.
* Extension automatique sur un réseau mondial de centres de données.
* Sauvegarde et restauration après sinistre.
* Conformité ISO, SOC2 et PCI.
* Intégration avec Active Directory

### <a id="corp"></a> J’ai besoin d’un hébergement pour le site web de ma société, qui soit fiable, extensible et de portée mondiale.
Azure App Service est une excellente solution pour l’hébergement de sites web d’entreprise. Il permet une mise à l’échelle rapide et simple des applications web pour faire face à la demande, grâce au réseau international de centres de données. Il offre un lien local, une grande tolérance aux pannes et une gestion intelligente du trafic. Le tout sur une plateforme avec des outils de pointe, qui offrent un accès facile et rapide aux informations de l'état du site et aux données de trafic. Azure App Service offre des contrats SLA pour des applications web à 0,001 % d’erreur et offre les possibilités suivantes :

* Exécution fiable de vos sites web sur une plateforme cloud avec corrections et mises à jour automatiques.
* Extension automatique sur un réseau mondial de centres de données.
* Sauvegarde et restauration après sinistre.
* Gestion des journaux et du trafic avec des outils intégrés.
* Conformité ISO, SOC2 et PCI.
* Intégration avec Active Directory

### <a id="iis6"></a> J’ai une application IIS6 qui s’exécute sur Windows Server 2003.
Azure App Service permet d’éviter facilement les coûts d’infrastructure liés à la migration d’anciennes applications IIS6. Microsoft a créé des [outils de migration simples et des informations de migration détaillées](https://www.migratetoazure.net/) qui vous permettent de vérifier la compatibilité et d'identifier les modifications nécessaires. L'intégration à Visual Studio, TFS et autres outils de CMS simplifie le déploiement d'applications IIS6 directement dans le cloud. Une fois déployé, le portail Azure offre l’accès à des outils de gestion robustes, afin de réduire vos coûts et de faire face à la demande. Avec l'outil de migration, vous pouvez :

* Migrer rapidement et facilement vos applications web Windows Server 2003 sur le cloud.
* Choisir de laisser votre base de données SQL attachée localement pour créer une application hybride.
* Déplacer automatiquement votre base de données SQL avec votre application héritée.

### <a id="smallbusiness"></a> Je dirige une PME et je recherche une méthode peu onéreuse pour héberger mon site web, tout en gardant à l’esprit la future croissance de mon entreprise.
Azure App Service est une solution idéale dans ce scénario, car vous pouvez démarrer gratuitement, puis ajouter gratuitement des fonctionnalités supplémentaires en fonction de vos besoins. Chaque application web gratuite est fournie avec un domaine Azure (*votre_société*.azurewebsites.net). La plateforme comprend des outils de déploiement et de gestion intégrés, ainsi qu’une galerie d’applications pour une mise en route rapide. De nombreux autres services et options de mise à l'échelle permettent de faire évoluer le site en fonction de l'augmentation de la demande utilisateur. Avec Azure App Service, vous pouvez effectuer les tâches suivantes :

* Commencez gratuitement, puis passez au niveau supérieur en fonction de vos besoins.
* Utilisez la galerie d'applications pour configurer rapidement des applications web reconnues, telles que WordPress.
* Ajoutez des services et fonctionnalités Azure à votre application à votre convenance.
* Sécurisez votre application web avec le protocole HTTPS.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a id="designer"></a> Je suis un concepteur web/graphiste et je souhaite concevoir et développer des sites web pour mes clients
Pour les développeurs web et les graphistes, Azure App Service s’intègre facilement à de nombreuses infrastructures et à de nombreux outils. Il comprend la prise en charge du déploiement avec Git et FTP et offre une excellente intégration à des outils et services tels que Visual Studio et Base de données SQL. Avec App Service, vous pouvez :

* utiliser les outils en ligne de commande pour les [tâches automatisées][scripting] ;
* utiliser des langages reconnus tels que [.Net][dotnet], [PHP][PHP], [Node.js][nodejs] et [Python][Python] ;
* sélectionner trois niveaux de mise à l'échelle pour bénéficier de capacités supérieures ;
* intégrer d’autres services Azure, tels que [SQL Database][sqldatabase], [Service Bus][servicebus] et [Stockage Azure][Storage] ou d’autres offres partenaire disponibles dans [Azure Store][azurestore], comme MySQL et MongoDB ;
* Intégrer des outils tels que Visual Studio, Git, WebMatrix, WebDeploy, TFS et FTP.

### <a id="multitier"></a>Je migre actuellement mon application multiniveau et un serveur web frontal dans le cloud
Si vous exécutez une application multiniveau, par exemple un serveur web qui se connecte à une base de données, Azure App Service est une bonne option, qui permet une intégration à la Base de données SQL Azure. Et vous pouvez utiliser la fonctionnalité WebJobs pour exécuter des processus principaux.

Choisissez Service Fabric pour une ou plusieurs couches si vous avez besoin de contrôler l’environnement de serveur de façon plus précise, par exemple pour pouvoir vous connecter à distance au serveur ou pour configurer les tâches de démarrage du serveur.

Choisissez Virtual Machines pour une ou plusieurs couches si vous voulez utiliser votre propre image ou exécuter des logiciels ou des services que vous ne pouvez pas configurer sur Service Fabric.

### <a id="custom"></a>Mon application dépend d’environnements Windows ou Linux hautement personnalisés et je souhaite la transférer dans le cloud.
Si votre application nécessite l'installation ou la configuration complexe des logiciels et du système d'exploitation, Virtual Machines représente probablement la meilleure solution. Virtual Machines vous permet d'effectuer les opérations suivantes :

* utiliser la galerie de machines virtuelles pour commencer avec un système d'exploitation tel que Windows ou Linux, puis personnaliser ce dernier en fonction des besoins de votre application ;
* créer et télécharger une image personnalisée d'un serveur local existant à exécuter sur une machine virtuelle dans Azure.

### <a id="oss"></a>Mon site utilise des logiciels open source et je souhaite l’héberger dans Azure
Si votre infrastructure open source est prise en charge par App Service, les langages et infrastructures nécessaires à votre application sont automatiquement configurés pour vous. Avec App Service, vous pouvez :

* utiliser de nombreux langages open source reconnus tels que [.NET][dotnet], [PHP][PHP], [Node.js][nodejs] et [Python][Python] ;
* configurer WordPress, Drupal, Umbraco, DNN et de nombreuses autres applications web tierces.
* migrer une application existante ou en créer une à partir de la galerie d'applications.

Si votre infrastructure open source n’est pas prise en charge par App Service, vous pouvez l’exécuter sur l’une des autres options d’hébergement web Azure. Virtual Machines vous permet d'installer et de configurer les logiciels sur l'image de machine virtuelle, qui peut être basée sur Windows ou Linux.

### <a id="lob"></a>Je possède une application métier qui doit se connecter au réseau de l’entreprise
Si vous souhaitez créer une application métier, votre site Web requiert peut-être un accès direct aux services et données figurant sur le réseau de l'entreprise. C’est possible sur App Service, Service Fabric et des machines virtuelles grâce au [service Azure Virtual Network](/azure/virtual-network/). Sur App Service, vous pouvez utiliser la fonction d’ [intégration VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), qui permet à vos applications Azure de s’exécuter comme si elles se trouvaient sur votre réseau d’entreprise.

### <a id="mobile"></a>Je souhaite héberger une API REST ou un service web pour des clients mobiles
Les services web HTTP permettent de prendre en charge un large éventail de clients, dont des clients mobiles. Les infrastructures telles que l'API web ASP.NET s'intègrent à Visual Studio, ce qui facilite la création et l'utilisation des services REST.  Ces derniers étant exposés à partir d'un point de terminaison Web, il est possible d'utiliser une des techniques d'hébergement Web dans Azure pour prendre en charge ce scénario. App Service constitue un choix idéal pour l’hébergement des API REST. Avec App Service, vous pouvez :

* créer rapidement une [application mobile](../app-service-mobile/app-service-mobile-value-prop.md) ou une application API afin d’héberger le service web HTTP dans l’un des centres de données Azure répartis dans le monde entier ;
* migrer des services existants ou en créer de nouveaux.
* se conformer au contrat SLA en termes de disponibilité d'une seule instance ou effectuer une montée en charge vers plusieurs machines dédiées ;
* utiliser le site publié pour fournir des API REST aux clients HTTP, dont les clients mobiles.

> [!NOTE]
> Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte, accédez au site <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez créer immédiatement un site de départ de courte durée gratuit dans Azure App Service. Aucune carte de crédit n’est requise, vous ne prenez aucun engagement.
> 
> 

## <a id="nextsteps"></a> Étapes suivantes
Pour en savoir plus sur les trois options d’hébergement web, consultez [Présentation d’Azure](../fundamentals-introduction-to-azure.md).

Pour choisir une ou plusieurs de ces options pour votre application, consultez les ressources suivantes :

* [Azure App Service](/azure/app-service/)
* [Azure Cloud Services](/azure/cloud-services/)
* [Azure Virtual Machines](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
