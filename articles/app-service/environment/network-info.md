---
title: "Considérations relatives à la mise en réseau avec un environnement Azure App Service"
description: "Cet article présente le trafic réseau d’un environnement App Service Environment (ASE) et explique comment définir des groupes de sécurité réseau et des itinéraires définis par l’utilisateur (UDR) avec votre ASE."
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ccompy
ms.openlocfilehash: 3ac630982b47f7105feb034982eae070faa72d9e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considérations relatives à la mise en réseau pour un environnement App Service #

## <a name="overview"></a>Vue d'ensemble ##

 [App Service Environment Azure][Intro] est un déploiement d’Azure App Service dans un sous-réseau d’un réseau virtuel Azure (VNet). Il existe deux types de déploiement pour un environnement App Service (ASE) :

- **ASE externe** : expose les applications hébergées dans l’ASE sur une adresse IP accessible via Internet. Pour plus d’informations, consultez [Créer un environnement App Service externe][MakeExternalASE].
- **ASE ILB** : expose les applications hébergées dans l’ASE sur une adresse IP à l’intérieur de votre réseau virtuel. Le point de terminaison interne est un équilibreur de charge interne (ILB), d’où cette appellation d’ASE ILB. Pour plus d’informations, consultez [Create and use an ILB ASE][MakeILBASE] (Créer et utiliser un ASE ILB).

Il existe deux versions de l’application App Service : ASEv1 et ASEv2. Pour plus d’informations sur ASEv1, consultez la [Présentation de l’environnement App Service v1][ASEv1Intro]. Un ASEv1 peut être déployé dans un réseau virtuel classique ou Resource Manager. Un ASEv2 peut uniquement être déployé dans un réseau virtuel Resource Manager.

Tous les appels d’un ASE à destination d’Internet quittent le réseau virtuel via une adresse IP virtuelle assignée à l’ASE. L’adresse IP publique de cette adresse IP virtuelle constitue ensuite l’adresse IP source de tous les appels de l’ASE à destination d’Internet. Si les applications hébergées dans votre environnement ASE appellent des ressources de votre réseau virtuel ou hébergées dans un VPN, l’adresse IP source sera l’une des adresses IP du sous-réseau utilisé par votre environnement ASE. Comme l’ASE se trouve à l’intérieur du réseau virtuel, il peut également accéder aux ressources de celui-ci sans configuration supplémentaire. Si le réseau virtuel est connecté à votre réseau local, les applications dans votre environnement ASE ont également accès aux ressources. Vous n’avez pas besoin de configurer l’ASE ou votre application plus en avant.

![ASE externe][1] 

Si vous avez un ASE externe, l’adresse IP virtuelle publique est également le point de terminaison que vos applications ASE peuvent résoudre :

* HTTP/S. 
* FTP/S. 
* Déploiement Web.
* Débogage à distance.

![ASE ILB][2]

Si vous possédez un ASE ILB, l’adresse IP de l’équilibreur de charge interne est le point de terminaison pour les protocoles HTTP/S et FTP/S, le déploiement Web et le débogage à distance.

Les ports d’accès normaux pour les applications sont les suivants :

| Utilisation | À partir | À |
|----------|---------|-------------|
|  HTTP/HTTPS  | Configurable par l’utilisateur |  80, 443 |
|  FTP/FTPS    | Configurable par l’utilisateur |  21, 990, 10001-10020 |
|  Débogage distant de Visual Studio  |  Configurable par l’utilisateur |  4016, 4018, 4020, 4022 |

Ces ports s’appliquent aussi bien pour un ASE externe que pour un ASE ILB. Si vous êtes dans un ASE externe, appuyez sur ces ports sur l’adresse IP virtuelle publique. Si vous vous trouvez dans un ASE ILB, vous atteignez ces ports sur l’équilibreur de charge interne. Si vous verrouillez le port 443, certaines fonctionnalités exposées dans le portail peuvent être affectées. Pour plus d’informations, consultez la section [Dépendances du portail](#portaldep).

## <a name="ase-dependencies"></a>Dépendances d’un ASE ##

Un ASE présente la dépendance d’accès entrant suivante :

| Utilisation | À partir | À |
|-----|------|----|
| Gestion | Adresses de gestion App Service | Sous-réseau de l’ASE : 454, 455 |
|  Communications internes de l’ASE | Sous-réseau de l’ASE : tous les ports | Sous-réseau de l’ASE : tous les ports
|  Autoriser le trafic entrant provenant d’Azure Load Balancer | Équilibrage de charge Azure | Sous-réseau de l’ASE : tous les ports
|  Adresses IP affectées par l’application | Adresses affectées par l’application | Sous-réseau de l’ASE : tous les ports

Le trafic entrant fournit la commande et le contrôle de l’ASE en plus de la surveillance du système. Les adresses IP sources pour ce trafic sont répertoriées dans le document [Adresses de gestion App Service Environment][ASEManagement]. Par conséquent, la configuration de la sécurité réseau doit autoriser l’accès sur les ports 454 et 455 à partir de toutes les adresses IP.

Le sous-réseau de l’ASE comprend divers ports utilisés pour la communication des composants internes ; ces ports peuvent changer.  Tous les ports du sous-réseau de l’ASE doivent être accessibles à partir du sous-réseau de l’ASE. 

Pour permettre la communication entre l’équilibreur de charge Azure et le sous-réseau de l’ASE, les ports 454, 455 et 16001 (au minimum) doivent être ouverts. Le port 16001 sert à maintenir le trafic entre l’équilibreur de charge et le sous-réseau de l’ASE. Si vous utilisez un ASE ILB, vous pouvez limiter le trafic aux ports 454, 455 et 16001.  Si vous utilisez un ASE externe, vous devez prendre en compte les ports d’accès application normaux.  Si vous utilisez des adresses affectées par l’application, vous devez les ouvrir à tous les ports.  Quand une adresse est affectée à une application spécifique, l’équilibreur de charge utilise des ports qui ne sont pas connus à l’avance pour l’envoi du trafic HTTP et HTTPS à l’ASE.

Si vous utilisez des adresses IP affectées par l’application, vous devez autoriser le trafic entre les adresses IP affectées à vos applications et le sous-réseau de l’ASE.

Pour l’accès sortant, un ASE dépend de plusieurs systèmes externes. Ces dépendances système sont définies avec des noms DNS et ne sont pas mappées à un ensemble fixe d’adresses IP. Par conséquent, l’ASE requiert un accès sortant vers toutes les adresses IP sur divers ports à partir de son sous-réseau. Un ASE présente les dépendances d’accès sortant suivantes :

| Utilisation | À partir | À |
|-----|------|----|
| Azure Storage | Sous-réseau de l’ASE | table.core.windows.net, blob.core.windows.net, queue.core.windows.net, file.core.windows.net : 80, 443, 445 (le port 445 est requis uniquement pour ASEv1) |
| Azure SQL Database | Sous-réseau de l’ASE | database.windows.net : 1433, 11000-11999, 14000-14999 (pour plus d’informations, consultez [Port utilisé par SQL Database V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).)|
| Gestion d’Azure | Sous-réseau de l’ASE | management.core.windows.net, management.azure.com : 443 
| Vérification du certificat SSL |  Sous-réseau de l’ASE            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com : 443
| Azure Active Directory        | Sous-réseau de l’ASE            |  Internet : 443
| Gestion d’App Service        | Sous-réseau de l’ASE            |  Internet : 443
| DNS Azure                     | Sous-réseau de l’ASE            |  Internet : 53
| Communications internes de l’ASE    | Sous-réseau de l’ASE : tous les ports |  Sous-réseau de l’ASE : tous les ports

Si l’ASE n’a plus accès à ces dépendances, il cesse de fonctionner. Si cela dure plus d’un certain temps, l’ASE est suspendu.

### <a name="customer-dns"></a>DNS client ###

Si le réseau virtuel est configuré avec un serveur DNS défini par un client, les charges de travail du client l’utilisent. L’ASE a toujours besoin de communiquer avec le DNS Azure à des fins de gestion. 

Si le réseau virtuel est configuré avec un DNS client de l’autre côté d’un réseau VPN, le serveur DNS doit être accessible à partir du sous-réseau contenant l’ASE.

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Dépendances du portail ##

Outre les dépendances fonctionnelles que présente un ASE, il existe quelques éléments supplémentaires liés à l’utilisation du portail. Certaines des fonctionnalités du portail Azure dépendent d’un accès direct au _site du Gestionnaire de contrôle des services (SCM)_. Pour chaque application dans Azure App Service, il existe deux URL. La première URL sert à accéder à votre application. La seconde permet d’accéder au site SCM, également désigné sous le nom de _console Kudu_. Voici quelques-unes des fonctionnalités qui utilisent le site SCM :

-   Tâches web
-   Fonctions
-   Diffusion de journaux
-   Kudu
-   Extensions
-   Process Explorer
-   Console

Lorsque vous utilisez un ASE ILB, le site SCM n’est pas accessible depuis l’extérieur du réseau virtuel d’internet. Quand votre application est hébergée sur un ASE ILB, certaines fonctionnalités ne sont pas opérationnelles à partir du portail.  

La plupart des fonctionnalités qui dépendent du site SCM sont également disponibles dans la console Kudu. Vous pouvez vous y connecter directement au lieu d’utiliser le portail. Si votre application est hébergée dans un ASE ILB, vous devez vous connecter à l’aide de vos informations d’identification de publication. L’URL d’une application hébergée dans un ASE ILB permettant d’accéder au site SCM présente le format suivant : 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Si votre ASE ILB est le nom de domaine *contoso.net* et le nom de votre application est *testapp*, l’application est atteinte sur *testapp.contoso.net*. Le site SCM qui le suit est atteinte sur *testapp.scm.contoso.net*.

## <a name="functions-and-web-jobs"></a>Fonctions et tâches web ##

Les fonctions et tâches web varient selon le site SCM mais elles sont prises en charge pour une utilisation dans le portail, même si vos applications sont dans un ASE ILB, tant que votre navigateur peut accéder au site SCM.  Si vous utilisez un certificat auto-signé avec votre ASE ILB, vous devez activer votre navigateur pour approuver ce certificat.  Pour Internet Explorer ou Edge, cela signifie que le certificat doit se trouver dans le magasin d’approbations de l’ordinateur.  Si vous utilisez Chrome, cela signifie que vous avez préalablement accepté le certificat dans le navigateur, vraisemblablement en appuyant directement sur le site SCM.  La meilleure solution consiste à utiliser un certificat commercial qui se trouve dans la chaîne d’approbation du navigateur.  

## <a name="ase-ip-addresses"></a>Adresses IP d’un ASE ##

Un ASE présente quelques adresses IP qu’il est important de connaître. Il s'agit de :

- **Adresse IP entrante publique** : utilisée pour le trafic d’applications dans un ASE externe et pour le trafic de gestion aussi bien dans un ASE externe que dans un ASE ILB.
- **Adresse IP publique sortante**  : utilisée en tant qu’adresse IP source pour les connexions sortantes de l’ASE quittant le réseau virtuel, qui ne sont pas acheminées via un VPN.
- **Adresse IP ILB** : si vous utilisez un ASE ILB.
- **Adresse SSL basée sur IP attribuée par l’application** : uniquement possibles avec un ASE externe et lorsque le mode SSL basé sur IP est configuré.

Toutes ces adresses IP sont facilement visibles pour un ASEv2 à partir de l’interface utilisateur de l’ASE dans le portail Azure. Si vous possédez un ASE ILB, l’adresse IP de l’ILB est répertoriée.

![Adresses IP][3]

### <a name="app-assigned-ip-addresses"></a>Adresses IP attribuées par l’application ###

Avec un ASE externe, vous pouvez assigner des adresses IP à des applications individuelles. Ce n’est pas possible avec un ASE ILB. Pour savoir comment configurer votre application de sorte qu’elle possède sa propre adresse IP, consultez [Lier un certificat SSL existant à des applications Web Azure](../app-service-web-tutorial-custom-ssl.md).

Lorsqu’une application possède sa propre adresse SSL basée sur IP, l’ASE réserve deux ports pour le mappage à cette adresse IP. Un port est destiné au trafic HTTP et l’autre au trafic HTTPS. Ces ports sont répertoriés dans l’interface utilisateur de l’ASE, dans la section des adresses IP. Le trafic doit pouvoir atteindre ces ports à partir de l’adresse IP virtuelle. Sinon, les applications ne sont pas accessibles. Il est important de ne pas oublier cela lorsque vous configurez des groupes de sécurité réseau (NSG).

## <a name="network-security-groups"></a>Groupes de sécurité réseau ##

Les [groupes de sécurité réseau][NSGs] permettent de contrôler l’accès réseau au sein d’un réseau virtuel. Lorsque vous utilisez le portail, il existe une règle de refus implicite au niveau de priorité le plus bas qui fait que tout accès est refusé. Ce que vous créez sont vos règles d’autorisation.

Dans un ASE, vous n’avez pas accès aux machines virtuelles utilisées pour héberger l’ASE lui-même. Elles se trouvent dans un abonnement géré par Microsoft. Si vous souhaitez restreindre l’accès aux applications dans l’ASE, définissez les groupes de sécurité réseau sur le sous-réseau de l’ASE. Ce faisant, vous devez prêter une attention particulière aux dépendances de l’ASE. Si vous bloquez certaines dépendances, l’ASE cesse de fonctionner.

Les groupes de sécurité réseau peuvent être configurés à l’aide du portail Azure ou via PowerShell. Seul le portail Azure est illustré ici. Les groupes de sécurité réseau sont créés et gérés en tant que ressources de niveau supérieur dans la section **Mise en réseau** du portail.

En tenant compte des exigences liées au trafic entrant et sortant, les groupes de sécurité réseau doivent se ressembler aux groupes de sécurité réseau présentés dans cet exemple. La plage d’adresses du réseau virtuel est _192.168.250.0/16_ et le sous-réseau dans lequel l’ASE se trouve est _192.168.251.128/25_.

Les deux premières exigences liées au trafic entrant pour l’ASE figurent en haut de la liste dans cet exemple. Elles permettent la gestion de l’ASE et autorisent l’ASE à communiquer avec lui-même. Les autres entrées sont toutes configurables par le client et peuvent régir l’accès réseau aux applications hébergées dans l’ASE. 

![Règles de sécurité de trafic entrant][4]

Une règle par défaut permet la communication entre les adresses IP dans le réseau virtuel avec le sous-réseau ASE. Une autre règle par défaut permet la communication entre l’équilibrage de charges, également appelé l’adresse IP virtuelle publique, et l’ASE. Vous pouvez afficher les règles par défaut en sélectionnant **Règles par défaut** en regard de l’icône **Ajouter**. Si vous placez une règle de refus pour toute autre communication après les règles de groupes de sécurité réseau illustrées, vous empêchez le trafic entre l’adresse IP virtuelle et l’ASE. Pour éviter le trafic provenant de l’intérieur du réseau virtuel, ajoutez votre propre règle pour autoriser le trafic entrant. Utilisez une source égale à AzureLoadBalancer avec une destination **Tout** et une plage de ports **\***. Étant donné que la règle de groupes de sécurité réseau est appliquée au sous-réseau de l’ASE, vous n’avez pas besoin de définir une destination spécifique.

Si vous avez attribué une adresse IP à votre application, assurez-vous que vous conservez les ports ouverts. Vous pouvez consulter les ports utilisés en sélectionnant **App Service Environment** > **Adresses IP**.  

Tous les éléments affichés dans les règles de trafic sortant suivants sont nécessaires, à l’exception du dernier élément. Ils autorisent l’accès réseau aux dépendances de l’ASE décrites plus haut dans ce document. Si vous bloquez un d'entre eux, votre ASE cesse de fonctionner. Le dernier élément de la liste autorise votre ASE à communiquer avec les autres ressources de votre réseau virtuel.

![Règles de sécurité de trafic entrant][5]

Une fois vos groupes de sécurité réseau définis, vous devez les attribuer au sous-réseau dans lequel se trouve votre ASE. Si vous ne connaissez pas le réseau virtuel ou le sous-réseau de l’ASE, vous pouvez l’afficher dans la page du portail de l’ASE. Pour assigner le groupe de sécurité réseau à votre sous-réseau, accédez à l’interface utilisateur du sous-réseau et sélectionnez le groupe de sécurité réseau.

## <a name="routes"></a>Itinéraires ##

Les itinéraires posent le plus souvent problème lorsque vous configurez votre réseau virtuel avec Azure ExpressRoute. Il existe trois types d’itinéraires dans un réseau virtuel :

-   Itinéraires système
-   Itinéraires BGP
-   Itinéraires définis par l’utilisateur (UDR)

Les itinéraires BGP prennent le pas sur les itinéraires système. Les UDR prennent le pas sur les itinéraires BGP. Pour plus d’informations sur les itinéraires dans les réseaux virtuels Azure, consultez [Présentation des itinéraires définis par l’utilisateur][UDRs].

La base de données SQL Azure qu’utilise l’ASE pour gérer le système dispose d’un pare-feu. Il requiert une communication provenant de l’adresse IP virtuelle publique ASE. Les connexions à la base de données SQL à partir de l’ASE sont refusées si elles sont envoyées via la connexion ExpressRoute et depuis une autre adresse IP.

Si les réponses aux requêtes de gestion entrantes sont envoyées via le circuit ExpressRoute, l’adresse de réponse est différente de celle de destination. Cette incohérence interrompt la communication TCP.

Pour que votre ASE fonctionne lorsque votre réseau virtuel est configuré avec un circuit ExpressRoute, le plus simple consiste à :

-   Configurer ExpressRoute pour qu’il publie _0.0.0.0/0_. Par défaut, il tunnélise de force tout le trafic sortant local.
-   Créer un UDR. Appliquez l’UDR au sous-réseau qui contient l’ASE, avec le préfixe d’adresse _0.0.0.0/0_ et le type de tronçon suivant _Internet_.

Si vous apportez ces deux modifications, le trafic à destination d’Internet provenant du sous-réseau de l’ASE n’est plus acheminé de force via le circuit ExpressRoute et l’ASE peut fonctionner. 

> [!IMPORTANT]
> Les itinéraires définis dans un UDR doivent être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L’exemple précédent utilise la plage d’adresses 0.0.0.0/0 large. Il peut potentiellement être remplacé accidentellement par des annonces de routage utilisant des plages d’adresses plus spécifiques.
>
> Les ASE ne sont pas pris en charge avec les configurations ExpressRoute qui annoncent de façon croisée des itinéraires à partir du chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent les publications de routage de Microsoft. Les publications contiennent un grand ensemble de plages d’adresses IP de Microsoft Azure. Si ces plages d’adresses sont publiées de façon croisée sur le chemin d’accès d’homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l’environnement App Service sont tunnélisés de force vers l’infrastructure réseau local d’un client. Ce flux de réseau n’est actuellement pas pris en charge par les environnements App Service. L’une des solutions à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée.

Pour créer un UDR, procédez comme suit :

1. Accédez au portail Azure. Sélectionnez **Mise en réseau** > **Tables d’itinéraires**.

2. Créez une nouvelle table d’itinéraires dans la même région que votre réseau virtuel.

3. À partir de l’interface utilisateur de votre table d’itinéraires, sélectionnez **Itinéraires** > **Ajouter**.

4. Définissez le **Type de tronçon suivant** sur **Internet** et **Préfixe de l’adresse** sur **0.0.0.0/0**. Sélectionnez **Enregistrer**.

    Le résultat suivant s’affiche :

    ![Itinéraires fonctionnels][6]

5. Après avoir créé la nouvelle table d’itinéraires, accédez au sous-réseau contenant votre ASE. Dans la liste du portail, sélectionnez votre table d’itinéraires. Une fois la modification enregistrée, les groupes de sécurité réseau et les itinéraires s’affichent dans les informations de votre sous-réseau.

    ![Itinéraires et groupes de sécurité réseau][7]

### <a name="deploy-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>Déploiement dans des réseaux virtuels Azure existants intégrés à ExpressRoute ###

Pour déployer votre ASE dans un réseau virtuel intégré à ExpressRoute, préconfigurez le sous-réseau dans lequel vous souhaitez que l’ASE soit déployé. Utilisez alors un modèle Resource Manager pour le déployer. Pour créer un ASE dans un réseau virtuel pour lequel ExpressRoute est déjà configuré :

- Créez un sous-réseau pour héberger l’ASE.

    > [!NOTE]
    > Le sous-réseau doit contenir uniquement l’ASE. Veillez à choisir un espace d’adressage qui permet une croissance future. Vous ne pouvez pas modifier ce paramètre par la suite. Nous vous recommandons une taille de `/25` avec 128 adresses.

- Créez des UDR (par exemple des tables d’itinéraires) en suivant la procédure décrite précédemment et définissez-les sur le sous-réseau.
- Créez l’ASE à l’aide d’un modèle Resource Manager en suivant la procédure décrite dans [Création d’un ASE à l’aide d’un modèle ARM][MakeASEfromTemplate].

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

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
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
