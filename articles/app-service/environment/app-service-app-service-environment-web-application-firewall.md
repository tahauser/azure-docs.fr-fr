---
title: "Configuration d'un pare-feu d'applications Web (WAF) pour un environnement App Service"
description: "Apprenez à configurer un pare-feu d'applications Web devant votre environnement App Service."
services: app-service\web
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: naziml
ms.openlocfilehash: 3c218a6fe3857c216bc185c5d3630025f332147b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configuration d'un pare-feu d'applications Web (WAF) pour un environnement App Service
## <a name="overview"></a>Vue d'ensemble
Les pare-feu d’applications web comme le [WAF Barracuda pour Azure](https://www.barracuda.com/programs/azure) qui est disponible sur la [Place de marché Azure](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) permettent de sécuriser vos applications web en inspectant le trafic web entrant pour bloquer les injections SQL, l’exécution de scripts de site à site, les téléchargements de logiciels malveillants, les attaques DDoS d’application et d’autres attaques. Ce type de pare-feu inspecte également les réponses des serveurs Web principaux pour prévention de perte de données (DLP). En association avec l'isolement et la mise à l'échelle supplémentaire fournis par les environnements App Service, ceci fournit un environnement idéal pour héberger des applications Web professionnelles critiques qui doivent résister aux requêtes malveillantes et à un volume de trafic élevé.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Paramétrage
Pour ce document, nous configurons l’environnement App Service derrière plusieurs instances à charge équilibrée de WAF Barracuda, afin que seul le trafic provenant du WAF puisse atteindre l’environnement App Service. Il n’est pas accessible depuis la zone DMZ. Nous avons également Azure Traffic Manager devant les instances WAF Barracuda pour équilibrer la charge entre les régions et les centres de données Azure. Un diagramme de haut niveau de la configuration ressemblerait à l’image suivante :

![Architecture][Architecture] 

> [!NOTE]
> Avec l’introduction de la [prise en charge de l’équilibreur de charge interne pour l’environnement App Service](app-service-environment-with-internal-load-balancer.md), vous pouvez configurer l’ASE de façon à ce qu’il soit inaccessible depuis le DMZ et uniquement disponible pour le réseau privé. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Configuration de votre environnement App Service
Pour configurer un environnement App Service, consultez [notre documentation](app-service-web-how-to-create-an-app-service-environment.md) sur le sujet. Une fois qu’un environnement App Service est créé, vous pouvez y créer des applications web, des applications d’API et des [applications mobiles](../../app-service-mobile/app-service-mobile-value-prop.md), qui seront toutes protégées derrière le WAF que nous allons configurer dans la section suivante.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configuration de votre service cloud WAF Barracuda
Barracuda propose un [article détaillé](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sur le déploiement de son WAF sur une machine virtuelle dans Azure. Mais étant donné que nous voulons la redondance sans introduire aucun point de défaillance, vous devez déployer au moins deux machines virtuelles d’instances WAF dans le même service cloud.

### <a name="adding-endpoints-to-cloud-service"></a>Ajout de points de terminaison au service cloud
Du moment où vous disposez d’au moins deux instances de machines virtuelles WAF dans votre service cloud, vous pouvez utiliser le [portail Azure](https://portal.azure.com/) pour ajouter des points de terminaison HTTP et HTTPS utilisés par votre application, comme illustré dans l’image suivante :

![Configurer le point de terminaison][ConfigureEndpoint]

Si vos applications utilisent d’autres points de terminaison, veillez à les ajouter à cette liste également. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configuration du WAF Barracuda avec son portail de gestion
Le WAF Barracuda utilise le port TCP 8000 pour sa configuration avec le portail de gestion. Si vous avez plusieurs instances de machines virtuelles WAF, vous devez répéter ces étapes pour chaque instance de machine virtuelle. 

> [!NOTE]
> Lorsque la configuration WAF est terminée, supprimez le point de terminaison TCP/8000 de toutes les machines virtuelles WAF pour sécuriser votre WAF.
> 
> 

Ajoutez le point de terminaison de gestion, comme illustré dans l’image ci-dessous, pour configurer votre WAF Barracuda.

![Ajouter un point de terminaison de gestion][AddManagementEndpoint]

Utilisez un navigateur pour accéder au point de terminaison de gestion sur votre service cloud. Si votre service cloud se nomme test.cloudapp.net, vous atteignez ce point de terminaison en accédant à http://test.cloudapp.net:8000. Une page de connexion, comme celle illustrée dans l’image ci-dessous, doit s’afficher. Vous pouvez vous connecter à l’aide des informations d’identification spécifiées durant la phase de configuration de la machine virtuelle WAF.

![Page de connexion à la gestion][ManagementLoginPage]

Lorsque vous êtes connecté, un tableau de bord comme celui illustré dans l’image ci-dessous doit s’afficher. Il présente des statistiques de base sur la protection du WAF.

![Tableau de bord de gestion][ManagementDashboard]

Cliquez sur l’onglet **Services** pour configurer votre WAF pour les services qu’il protège. Pour plus d’informations sur la configuration de votre WAF Barracuda, consultez [la documentation appropriée](https://techlib.barracuda.com/waf/getstarted1). Dans l’exemple suivant, une application Web Azure desservant le trafic HTTP et HTTPS a été configurée.

![Ajouter des services de gestion][ManagementAddServices]

> [!NOTE]
> Selon la configuration de vos applications et les fonctionnalités utilisées dans votre environnement App Service, vous devez transférer le trafic pour les ports TCP autres que 80 et 443 ; par exemple, si vous avez configuré IP SSL pour une application Web. Pour obtenir la liste des ports réseau utilisés dans les environnements App Service, consultez la section Ports réseau de la [documentation relative au contrôle du trafic entrant](app-service-app-service-environment-control-inbound-traffic.md).
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configuration de Microsoft Azure Traffic Manager (FACULTATIF)
Si votre application est disponible dans plusieurs régions, vous devez en équilibrer la charge derrière [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Pour ce faire, vous pouvez ajouter un point de terminaison dans le [portail Azure Classic](https://portal.azure.com) en utilisant le nom du service cloud de votre WAF dans le profil Traffic Manager, comme indiqué dans l’image suivante. 

![Point de terminaison Traffic Manager][TrafficManagerEndpoint]

Si votre application requiert une authentification, vérifiez que vous disposez d'une ressource qui ne nécessite pas d'authentification pour permettre à Traffic Manager d'exécuter la commande ping pour vérifier la disponibilité de votre application. Vous pouvez configurer l’URL sur la page **Configuration** dans le [Portail Azure](https://portal.azure.com) comme illustré dans l’image suivante :

![Configuration de Traffic Manager][ConfigureTrafficManager]

Pour transférer les exécutions de commande ping de Traffic Manager à partir de votre WAF à votre application, vous devez configurer des traductions de site Web sur votre WAF Barracuda pour transférer le trafic vers votre application, comme illustré dans l’exemple suivant :

![Traductions de site Web][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Sécurisation du trafic vers un environnement App Service à l’aide de groupes de sécurité réseau (NSG)
Consultez la [documentation relative au contrôle du trafic entrant](app-service-app-service-environment-control-inbound-traffic.md) pour plus d’informations sur la restriction du trafic du WAF vers votre environnement App Service uniquement en utilisant l’adresse IP virtuelle de votre service cloud. Voici un exemple de commande Powershell pour effectuer cette tâche pour le port TCP 80.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Remplacez SourceAddressPrefix par l'adresse IP virtuelle (VIP) du service cloud de votre WAF.

> [!NOTE]
> L’adresse IP virtuelle de votre service cloud change lorsque vous supprimez et créez de nouveau le service cloud. Dans ce cas, veillez à mettre à jour l'adresse IP dans le groupe de ressources réseau. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
