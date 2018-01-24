---
title: "Intégrer une application à un réseau virtuel Azure"
description: "Explique comment connecter une application d’Azure App Service à un réseau virtuel Azure nouveau ou existant"
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: ccompy
ms.openlocfilehash: b755197af7e8791e01273bcc25f72c0d92ef6bc2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Intégrer une application à un réseau virtuel Azure
Ce document décrit la fonctionnalité d’intégration au réseau virtuel d’Azure App Service et explique comment la configurer avec des applications dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Si vous n’êtes pas familiarisé avec les réseaux virtuels Azure, cette fonctionnalité vous permet de placer la plupart de vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Ces réseaux peuvent ensuite être connectés à vos réseaux locaux à l’aide d’une variété de technologies VPN. Pour en savoir plus sur les réseaux virtuels Azure, commencez par consulter la page [Présentation du réseau virtuel][VNETOverview]. 

Azure App Service se présente sous deux formes. 

1. Les systèmes mutualisés qui prennent en charge l’ensemble des plans de tarification.
2. L’environnement App Service (ASE, App Service Environment), une fonctionnalité Premium qui se déploie sur votre réseau virtuel. 

Ce document présente l’intégration au réseau virtuel et non l’environnement App Service. Si vous souhaitez en savoir plus sur la fonctionnalité ASE, commencez par consulter les informations suivantes : [Présentation de l’environnement App Service][ASEintro].

Grâce à cette intégration, votre application web a accès aux ressources se trouvant dans votre réseau virtuel sans que celui-ci puisse accéder à votre application web de façon privée. L’accès au site privé fait référence au fait de rendre votre application accessible uniquement à partir d’un réseau privé, par exemple à partir d’un réseau virtuel Azure. L’accès privé aux sites est disponible uniquement via un ASE configuré avec un équilibreur de charge interne (ILB). Pour obtenir des détails sur l’utilisation d’un ILB ASE, commencez par lire l’article suivant : [Création et utilisation d’un ASE avec un ILB][ILBASE]. 

L’intégration au réseau virtuel est couramment utilisée dans le scénario suivant : vous autorisez votre application web à accéder à une base de données ou à des services web exécutés dans une machine virtuelle de votre réseau virtuel Azure. Avec l’intégration au réseau virtuel, vous n’avez pas besoin d’exposer un point de terminaison public pour les applications sur votre machine virtuelle, mais vous pouvez utiliser à la place des adresses privées routables non-Internet. 

La fonctionnalité d’intégration au réseau virtuel :

* nécessite un plan de tarification Standard, Premium ou Isolé 
* fonctionne avec le réseau virtuel classique ou Gestionnaire des ressources 
* prend en charge les protocoles TCP et UDP ;
* fonctionne avec les applications API, web, mobiles et les applications de fonction
* permet à une application de se connecter à un seul réseau virtuel à la fois
* permet d’intégrer jusqu’à cinq réseaux virtuels dans un plan App Service 
* permet l’utilisation du même réseau virtuel par plusieurs applications d’un plan App Service
* prend en charge un contrat SLA de 99,9 % de par le contrat SLA sur la passerelle de réseau virtuel

Certains éléments ne sont pas pris en charge par l’intégration au réseau virtuel, notamment :

* montage d’un lecteur ;
* intégration AD ; 
* NetBios ;
* accès privé aux sites.

### <a name="getting-started"></a>Prise en main
Voici quelques informations à garder à l’esprit avant de connecter votre application web à un réseau virtuel.

* L’intégration au réseau virtuel fonctionne uniquement avec des applications faisant partie d’un plan de tarification **Standard**, **Premium** ou **Isolé**. Si vous activez la fonctionnalité et faites ensuite évoluer votre plan App Service vers un plan de tarification non pris en charge, vos applications perdront la connexion aux réseaux virtuels qu’elles utilisent. 
* Si votre réseau virtuel cible existe déjà, il doit prendre en charge la connexion VPN de point à site avec une passerelle de routage dynamique avant de pouvoir être connecté à une application. Vous ne pouvez pas activer le réseau privé virtuel (VPN) de point à site si votre passerelle est configurée avec un routage statique.
* Le réseau virtuel doit faire partie du même abonnement que votre plan App Service (ASP, App Service Plan). 
* Les applications intégrées à un réseau virtuel utilisent le serveur DNS spécifié pour ce réseau virtuel.
* Par défaut, vos applications intégrées acheminent le trafic sur votre réseau virtuel uniquement selon les itinéraires définis dans votre réseau virtuel. 

## <a name="enabling-vnet-integration"></a>Activation de l’intégration au réseau virtuel

Vous pouvez connecter votre application à un réseau virtuel nouveau ou existant. Si vous créez un réseau dans le cadre de votre intégration, outre la création du réseau virtuel, une passerelle de routage dynamique est préconfigurée pour vous et le réseau VPN de point à site est activé. 

> [!NOTE]
> La configuration d’une nouvelle intégration au réseau virtuel peut prendre plusieurs minutes. 
> 
> 

Pour activer l’intégration au réseau virtuel, accédez aux paramètres de votre application, puis sélectionnez Mise en réseau. L’interface utilisateur qui s’ouvre offre trois options de mise en réseau. Ce guide porte uniquement sur l’intégration au réseau virtuel. Cependant, les connexions hybrides et environnements App Service sont décrits plus loin dans ce document. 

Si le plan de tarification n’est pas adapté à votre application, l’interface utilisateur vous permettra de le faire évoluer facilement vers un plan de tarification supérieur de votre choix.

![][1]

### <a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Activation de l’intégration au réseau virtuel avec un réseau virtuel existant
L’interface utilisateur d’intégration au réseau virtuel vous permet d’effectuer vos choix dans la liste de vos réseaux virtuels. Les réseaux virtuels Classic indiquent leur type avec le terme « Classique » entre parenthèses à côté de leur nom. La liste est triée de telle façon que les réseaux virtuels Resource Manager sont répertoriés en premier. Dans l’image ci-dessous, vous pouvez voir qu’un seul réseau virtuel peut être sélectionné. Un réseau virtuel peut être grisé pour plusieurs raisons, notamment :

* Le réseau virtuel est dans un abonnement différent de celui auquel votre compte a accès
* La connexion de point à site n’est pas activée pour le réseau virtuel
* Le réseau virtuel ne dispose pas d’une passerelle de routage dynamique

![][2]

Pour activer l’intégration, cliquez simplement sur le réseau virtuel souhaité. Votre application est automatiquement redémarrée, de sorte que les modifications soient appliquées. 

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Activation de la connexion point à site dans un réseau virtuel Classic
Si votre réseau virtuel ne dispose pas d’une passerelle ni de connexion de point à site, vous devez commencer par en configurer une. Pour effectuer cette opération pour un réseau virtuel Classic, accédez au [portail Azure][AzurePortal] et affichez la liste des réseaux virtuels (classiques). Cliquez sur le réseau avec lequel vous souhaitez effectuer l’intégration, puis cliquez sur la grande zone Connexions VPN, sous Essentials. À ce stade, vous pouvez créer votre réseau VPN de point à site et, à partir de ce réseau, créer une passerelle. Une fois créée la connexion de point à site avec passerelle, le processus peut prendre environ 30 minutes. 

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Activation de la connexion point à site dans un réseau virtuel Resource Manager
Pour configurer un réseau virtuel Resource Manager avec une passerelle et une connexion de point à site, vous pouvez utiliser PowerShell comme le décrit la page [Configurer une connexion de point à site à un réseau virtuel avec PowerShell][V2VNETP2S] ou le Portail Azure comme le décrit la page [Configurer une connexion de point à site sur un réseau virtuel avec le Portail Azure][V2VNETPortal]. L’interface utilisateur permettant d’effectuer cette opération n’est pas encore disponible. Notez que vous n’avez pas besoin de créer des certificats pour la configuration de point à site. Cela est configuré automatiquement lorsque vous connectez votre application web au réseau virtuel. 

### <a name="creating-a-pre-configured-vnet"></a>Création d’un réseau virtuel préconfiguré
Si vous souhaitez créer un réseau virtuel configuré avec une passerelle et connexion point à site, l’interface réseau App Service a la possibilité de le faire, mais uniquement pour un réseau virtuel Resource Manager. Si vous souhaitez créer un réseau virtuel Classic avec une passerelle et une connexion point à site, vous devez le faire manuellement via l’interface utilisateur de réseau. 

Pour créer un réseau virtuel Resource Manager via l’interface utilisateur d’intégration au réseau virtuel, sélectionnez simplement **Créer un réseau virtuel** et indiquez les informations suivantes :

* Nom du réseau virtuel
* Bloc d’adresses du réseau virtuel
* Nom du sous-réseau
* Bloc d’adresses du sous-réseau
* Bloc d’adresses de la passerelle
* Bloc d’adresses de point à site

Si vous souhaitez que ce réseau virtuel puisse se connecter à d’autres réseaux, vous devez éviter de choisir un espace d’adressage IP qui chevauche celui de ces réseaux. 

> [!NOTE]
> La création du réseau virtuel Resource Manager avec passerelle prend environ 30 minutes. Actuellement, le réseau virtuel ne sera pas intégré à votre application. Une fois votre réseau virtuel créé avec la passerelle, vous devez revenir à l’interface d’intégration de réseau virtuel de votre application et sélectionner votre nouveau réseau virtuel.
> 
> 

![][3]

En règle générale, les réseaux virtuels Azure sont créés dans des plages d’adresses de réseau privées. Par défaut, la fonctionnalité d’intégration au réseau virtuel achemine tout le trafic destiné à ces plages d’adresses IP sur votre réseau virtuel. Les plages d’adresses IP privées sont les suivantes :

* 10.0.0.0/8 - identique à 10.0.0.0 - 10.255.255.255
* 172.16.0.0/12 - identique à 172.16.0.0 - 172.31.255.255 
* 192.168.0.0/16 - identique à 192.168.0.0 - 192.168.255.255

L’espace d’adressage du réseau virtuel doit être spécifié en notation CIDR. Si vous n’êtes pas familiarisé avec la notation CIDR, il s’agit d’une méthode permettant de spécifier des blocs d’adresses à l’aide d’une adresse IP et d’un entier qui représente le masque de réseau. À titre de référence rapide, considérez que 10.1.0.0/24 représenterait 256 adresses et 10.1.0.0/25 représenterait 128 adresses. Une adresse IPv4 avec /32 représenterait une seule adresse. 

Si vous définissez les informations de serveur DNS ici, elles seront définies pour votre réseau virtuel. Après la création du réseau virtuel, vous pouvez modifier ces informations à partir des expériences utilisateur correspondantes. Si vous modifiez le serveur DNS du réseau virtuel, vous devez effectuer une opération de synchronisation réseau.

Lorsque vous créez un réseau virtuel Classic à l’aide de l’interface utilisateur d’intégration au réseau virtuel, un réseau virtuel est créé dans le même groupe de ressources que votre application. 

## <a name="how-the-system-works"></a>Fonctionnement du système
Pour connecter votre application à votre réseau virtuel, cette fonctionnalité s’appuie sur la technologie de réseau VPN de point à site. Les applications dans Azure App Service présentent une architecture système mutualisée empêchant qu’une application soit directement approvisionnée dans un réseau virtuel comme dans le cas des machines virtuelles. Grâce à la technologie de point à site, nous limitons l’accès réseau à la seule machine virtuelle hébergeant l’application. Par ailleurs, l’accès au réseau est restreint à ces hôtes d’application afin que vos applications aient uniquement accès aux réseaux pour lesquels elles ont été configurées. 

![][4]

Si vous n'avez pas configuré un serveur DNS avec votre réseau virtuel, votre application devra utiliser des adresses IP pour atteindre la ressource sur le réseau virtuel. Lorsque vous utilisez des adresses IP, n’oubliez pas que le principal avantage de cette fonctionnalité est qu’elle vous permet d’utiliser les adresses privées au sein de votre réseau privé. Si vous définissez votre application de sorte qu’elle utilise des adresses IP publiques pour l’une de vos machines virtuelles, vous n’utilisez pas la fonctionnalité d’intégration au réseau virtuel et communiquez sur Internet.

## <a name="managing-the-vnet-integrations"></a>Gestion des intégrations au réseau virtuel
La possibilité de se connecter à un réseau virtuel et de s’en déconnecter se situe au niveau de l’application. Les opérations qui peuvent affecter l’intégration au réseau virtuel entre plusieurs applications s’effectuent au niveau de l’ASP. Vous pouvez obtenir des détails sur votre réseau virtuel à partir de l’interface utilisateur qui apparaît au niveau de l’application. Pour la plupart, ces informations apparaissent également au niveau de l’ASP. 

![][5]

Sur la page État de la fonctionnalité réseau, vous pouvez voir si votre application est connectée à votre réseau virtuel. Si votre passerelle de réseau virtuel est en panne pour une raison quelconque, votre application apparaît comme non connectée. 

Les informations maintenant à votre disposition dans l’interface utilisateur d’intégration au réseau virtuel au niveau de l’application sont identiques aux informations détaillées que vous obtenez à partir de l’ASP. Il s’agit des informations suivantes :

* Nom du réseau virtuel : ce lien ouvre l’interface utilisateur du réseau virtuel Azure
* Emplacement : reflète l’emplacement de votre réseau virtuel. Il est possible d’effectuer une intégration à un réseau virtuel à un autre emplacement.
* État du certificat : certains certificats sont utilisés pour sécuriser la connexion VPN entre l’application et le réseau virtuel. Cela représente un test pour en vérifier la synchronisation.
* État de la passerelle - si vos passerelles sont en panne pour une raison quelconque, votre application ne peut pas accéder aux ressources du réseau virtuel. 
* Espace d’adressage du réseau virtuel : espace d’adressage IP pour votre réseau virtuel. 
* Espace d’adressage de point à site : espace d’adressage IP de point à site pour votre réseau virtuel. Votre application affichera la communication comme provenant de l’une des adresses IP de cet espace d’adressage. 
* Espace d’adressage de site à site - vous pouvez utiliser des réseaux VPN de site à site pour connecter votre réseau virtuel à vos ressources locales ou à d’autres réseaux virtuels. Si vous avez effectué cette configuration, les plages IP définies avec cette connexion VPN s’affichent à cet endroit.
* Serveurs DNS : si des serveurs DNS sont configurés avec votre réseau virtuel, ils sont répertoriés ici.
* Adresses IP acheminées vers le réseau virtuel : une liste des adresses IP définies pour le routage vers le réseau virtuel est définie, et ces adresses s’affichent ici. 

La seule opération que vous pouvez effectuer dans la vue d’application de votre interface d’intégration au réseau virtuel consiste à déconnecter votre application du réseau virtuel auquel elle est actuellement connectée. Pour ce faire, cliquez simplement sur Déconnecter dans la partie supérieure. Cette action ne modifie pas votre réseau virtuel. Le réseau virtuel et sa configuration, notamment les passerelles, demeurent inchangés. Si vous souhaitez ensuite supprimer votre réseau virtuel, vous devez d’abord supprimer les ressources qu’il contient, y compris les passerelles. 

La vue Plan App Service offre des opérations supplémentaires. Son accès s’effectue également hors de l’application. Pour accéder à l’interface utilisateur de mise en réseau ASP, il vous suffit d’ouvrir l’interface utilisateur ASP et de faire défiler l’affichage vers le bas. L’interface utilisateur comporte l’élément État de la fonctionnalité réseau. Celui-ci offre quelques détails secondaires sur votre intégration au réseau virtuel. Si vous cliquez sur cet élément, vous ouvrez l’interface utilisateur État de la fonctionnalité réseau. Si vous cliquez ensuite sur « Cliquez ici pour gérer », l’interface utilisateur qui répertorie les intégrations au réseau virtuel dans cet ASP s’ouvre.

![][6]

Veillez à mémoriser l’emplacement de l’ASP. Cela vous sera utile lorsque vous déterminerez les emplacements des réseaux virtuels avec lesquels vous effectuez une intégration. Lorsque le réseau virtuel est situé à un autre emplacement, il est très probable que vous rencontriez des problèmes de latence. 

Les réseaux virtuels avec lesquels vous effectuez l’intégration représentent un rappel quant au nombre de réseaux virtuels auxquels vos applications sont intégrées dans cet ASP et au nombre autorisé. 

Pour afficher des détails supplémentaires sur chaque réseau virtuel, il vous suffit de cliquer sur le réseau virtuel qui vous intéresse. Outre les détails mentionnés antérieurement, vous verrez également une liste des applications de cet ASP qui utilisent ce réseau virtuel. 

Deux actions clés sont possibles. D’une part, vous avez la possibilité d’ajouter des itinéraires qui dirigent le trafic issu de votre application vers votre réseau virtuel. D’autre part, vous pouvez synchroniser des certificats et des informations réseau.

![][7]

**Routage** Comme indiqué précédemment, les itinéraires définis dans votre réseau virtuel sont utilisés pour diriger le trafic vers votre réseau virtuel à partir de votre application. Cependant, dans certains cas, les clients veulent envoyer un trafic sortant supplémentaire à partir d’une application dans le réseau virtuel. Cette fonctionnalité leur est alors fournie. Ensuite, le traitement du trafic dépend de la façon dont le client configure son réseau virtuel. 

**Certificats** L’état des certificats reflète la vérification effectuée par App Service pour garantir que les certificats que nous utilisons pour la connexion VPN sont toujours valables. Lorsque l’intégration au réseau virtuel est activée, s’il s’agit de la première intégration à ce réseau virtuel à partir d’une application quelconque de cet ASP, un échange de certificats est nécessaire pour garantir la sécurité de la connexion. Avec les certificats, nous obtenons la configuration DNS, les itinéraires et d’autres éléments similaires qui décrivent le réseau.
Si ces certificats ou informations réseau sont modifiés, vous devez cliquer sur « Synchronisation réseau ». **REMARQUE** : lorsque vous cliquez sur « Synchronisation réseau », la connectivité entre votre application et votre réseau virtuel est brièvement interrompue. Votre application ne sera pas redémarrée, et la perte de connectivité peut altérer le fonctionnement de votre site. 

## <a name="accessing-on-premises-resources"></a>Accès aux ressources sur site
La fonctionnalité d’intégration au réseau virtuel offre notamment l’avantage suivant : si votre réseau virtuel est connecté à votre réseau local avec une connexion VPN de site à site, vos applications peuvent accéder à vos ressources locales à partir de votre application. Pour que cela fonctionne, vous devrez peut-être mettre à jour votre passerelle VPN locale avec les itinéraires relatifs à votre plage IP de point à site. Lorsque vous configurez le réseau VPN de site à site pour la première fois, les scripts utilisés pour le configurer doivent définir les itinéraires, y compris votre réseau VPN de point à site. Si vous ajoutez le réseau VPN de point à site après avoir créé votre réseau VPN de site à site, vous devrez mettre à jour les itinéraires manuellement. La procédure détaillée dépend de la passerelle et n’est pas décrite ici. 

> [!NOTE]
> La fonctionnalité d’intégration au réseau virtuel n’intègre pas d’application à un réseau virtuel doté d’une passerelle ExpressRoute. Même si la passerelle ExpressRoute est configurée en [mode de coexistence][VPNERCoex], l’intégration du réseau virtuel ne fonctionnera pas. Si vous avez besoin d’accéder aux ressources via une connexion ExpressRoute, vous pouvez utiliser un [App Service Environment][ASE] s’exécutant dans votre réseau virtuel.
> 
> 

## <a name="pricing-details"></a>Détails de la tarification
Lorsque vous utilisez la fonctionnalité d’intégration au réseau virtuel, vous devez connaître quelques nuances concernant la tarification. L’utilisation de cette fonctionnalité implique 3 coûts :

* exigences liées au niveau tarifaire de l’ASP ;
* coût de transfert des données ;
* coûts de la passerelle VPN.

Pour que vos applications puissent utiliser cette fonctionnalité, elles doivent faire partie d’un plan App Service Standard ou Premium. Pour plus d’informations sur les coûts, voir : [Tarification App Service][ASPricing]. 

En raison du mode de gestion des réseaux VPN de point à site, vous encourez systématiquement des coûts liés aux données sortantes lors de la connexion pour l’intégration au réseau virtuel, et ce, même si le réseau virtuel est situé dans le même centre de données. Pour connaître ces coûts, voir : [Détails de la tarification de transfert de données][DataPricing]. 

Les passerelles de réseau virtuel représentent le dernier élément de coût. Si vous n’utilisez pas les passerelles à d’autres fins, par exemple pour des réseaux VPN de site à site, vous payez la prise en charge de la fonctionnalité d’intégration au réseau virtuel par les passerelles. Pour plus d’informations sur ces coûts, voir : [tarification de passerelle VPN][VNETPricing]. 

## <a name="troubleshooting"></a>Résolution de problèmes
Même si cette fonctionnalité est facile à configurer, il se peut que vous rencontriez certains problèmes. Si vous rencontrez des difficultés pour accéder au point de terminaison souhaité, certains utilitaires vous permettent de tester la connectivité à partir de la console de l’application. Vous pouvez utiliser deux consoles : La première est la console Kudu et l’autre est la console accessible à l’aide du portail Azure. Pour ouvrir la console Kudu, à partir de votre application, accédez à Outils -> Kudu. Vous pouvez également ouvrir la page [nom_site].scm.azurewebsites.net, puis accéder à l’onglet Console de débogage. Pour accéder à la console hébergée par le portail Azure, à partir de votre application, accédez à outils -> Console. 

#### <a name="tools"></a>Outils
Des contraintes de sécurité empêchent d’utiliser les outils ping, nslookup et tracert au moyen de la console. Deux outils distincts ont été ajoutés pour les remplacer. Pour tester les fonctionnalités DNS, nous avons ajouté un outil nommé nameresolver.exe. La syntaxe est :

    nameresolver.exe hostname [optional: DNS Server]

Vous pouvez utiliser nameresolver pour vérifier les noms d’hôte dont dépend votre application. De cette façon, vous pouvez tester si des éléments de votre serveur DNS sont mal configurés ou si vous n’avez pas accès à votre serveur DNS.

L’outil suivant vous permet de tester la connectivité TCP avec une combinaison hôte/port. Il s’agit de l’outil tcpping.exe, dont la syntaxe est la suivante :

    tcpping.exe hostname [optional: port]

L’utilitaire tcpping vous indique si vous pouvez atteindre un hôte et un port spécifiques. Il peut afficher un succès si une application à écoute sur la combinaison d’hôte et de port, et si l’accès réseau à de votre application à l’hôte et au port est disponible.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Débogage de l’accès aux ressources hébergées sur un réseau virtuel
Plusieurs choses peuvent empêcher votre application d’atteindre un hôte et un port spécifiques. La plupart du temps, il s’agit de l’une des trois raisons suivantes :

* **Présence d’un pare-feu** Si vous utilisez un pare-feu, vous dépassez le délai d’expiration TCP. Dans ce cas, il est de 21 secondes. Utilisez l’outil tcpping pour tester la connectivité. Les délais d’expiration TCP peuvent avoir de nombreuses autres origines, mais commencez par vérifier ce point. 
* **DNS inaccessible** Le délai d’expiration du DNS est de trois secondes par serveur DNS. Si vous avez deux serveurs DNS, le délai d’expiration est de 6 secondes. Utilisez nameresolver pour vérifier que le DNS fonctionne correctement. Vous ne pouvez pas utiliser nslookup, car il n’utilise pas le DNS avec lequel votre réseau virtuel est configuré.
* **Plage d’IP P2S non valide** La plage d’adresses IP de point vers site doit se trouver dans les plages d’IP privées RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255). Si la plage utilise des adresses IP en dehors de cette plage, cela ne fonctionnera pas. 

Si ces réponses ne résolvent pas votre problème, commencez par répondre aux questions simples ci-dessous : 

* Le portail indique-t-il que la passerelle fonctionne ?
* Les certificats s’affichent comme étant synchronisés ?
* Un utilisateur a-t-il modifié la configuration du réseau sans effectuer de « Synchronisation réseau » dans les ASP affectés ? 

Si votre passerelle est en panne, rétablissez-la. Si vos certificats ne sont pas synchronisés, accédez à la vue ASP de votre interface d’intégration au réseau virtuel et appuyez sur « Synchronisation réseau ». Si vous pensez qu’une modification a été apportée à votre configuration de réseau virtuel et qu’elle n’a pas été synchronisée avec vos ASP, accédez à la vue ASP de votre interface d’intégration au réseau virtuel et appuyez sur « Synchronisation réseau ». Pour rappel, la connexion au réseau virtuel et vos applications seront brièvement interrompues. 

Si tous ces aspects sont corrects, vous devez approfondir vos recherches :

* Existe-t-il d’autres applications utilisant l’intégration de réseau virtuel pour accéder à des ressources dans le même réseau virtuel ? 
* Pouvez-vous ouvrir la console de l’application et utiliser tcpping pour accéder à d’autres ressources de votre réseau virtuel ? 

Si l’un de ces aspects est vérifié, votre intégration au réseau virtuel est correcte, et le problème se situe ailleurs. La situation est alors plus complexe car il n’existe aucun moyen simple de comprendre pourquoi vous ne pouvez pas atteindre une combinaison hôte:port. Voici quelques-unes des causes possibles :

* Un pare-feu activé sur l’hôte empêche l’accès au port de l’application à partir de votre plage d’adresses IP de point à site. Des sous-réseaux croisés requièrent souvent un accès public.
* Votre hôte cible est hors-service.
* Votre application est arrêtée.
* L’IP ou le nom d’hôte est incorrect.
* Votre application est à l’écoute sur un port autre que celui que vous envisagiez. Pour vérifier cela, accédez à cet hôte et utilisez la commande « netstat -aon » à partir de l’invite de commandes. Cette commande affiche l’ID de processus à l’écoute et le port correspondant. 
* Les groupes de sécurité de votre réseau sont configurés de telle sorte qu’ils empêchent l’accès à l’hôte et au port de votre application à partir de votre plage d’adresses IP de point à site.

N’oubliez pas que vous ne connaissez pas l’adresse IP de la plage d’adresses IP de point à site qui sera utilisée par votre application. Vous devez donc autoriser l’accès à partir de la plage entière. 

Étapes de débogage supplémentaires :

* Connectez-vous à une autre machine virtuelle de votre réseau virtuel et essayez d’atteindre la combinaison hôte:port de vos ressources. Vous pouvez utiliser pour cela certains utilitaires ping TCP ou Telnet. L’objectif ici consiste simplement à déterminer si la connectivité est établie à partir de cette autre machine virtuelle. 
* Démarrez une application sur une autre machine virtuelle et testez l’accès à cet hôte et au port à partir de la console dans votre application.

#### <a name="on-premises-resources"></a>Ressources locales ####
Si votre application ne peut pas accéder aux ressources locales, commencez par vérifier si vous pouvez accéder à une ressource de votre réseau virtuel. Si cela fonctionne, essayez d’atteindre l’application locale à partir d’une machine virtuelle dans le réseau virtuel. Vous pouvez utiliser Telnet ou un utilitaire ping TCP. Si votre machine virtuelle ne peut pas accéder à votre ressource locale, vérifiez que votre connexion VPN de site à site fonctionne. Si c’est le cas, vérifiez les éléments indiqués précédemment, ainsi que la configuration et l’état de la passerelle locale. 

Maintenant, si votre machine virtuelle hébergée sur réseau virtuel peut atteindre votre système local, mais que votre application ne le peut pas, la raison est probablement l’une des suivantes :

* vos itinéraires ne sont pas configurés avec vos plages IP de point à site dans votre passerelle locale
* vos groupes de sécurité réseau bloquent l'accès de votre plage IP de point à site
* vos pare-feu local locaux bloquent le trafic à partir de votre plage IP de point à site
* vous avez un itinéraire défini par l’utilisateur (UDR) dans votre réseau virtuel qui empêche votre trafic de point à site d’atteindre votre réseau local

## <a name="powershell-automation"></a>Automation PowerShell

Vous pouvez intégrer App Service à un réseau virtuel Azure à l’aide de PowerShell. Pour obtenir un script prêt à l’exécution, consultez [Connect an app in Azure App Service to an Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).

## <a name="hybrid-connections-and-app-service-environments"></a>Connexions hybrides et environnements App Service
Trois fonctionnalités permettent d’accéder aux ressources hébergées sur le réseau virtuel. Il s'agit de :

* Intégration au réseau virtuel
* les connexions hybrides
* Environnements App Service

Pour les connexions hybrides, vous devez installer l’agent de relais Gestionnaire de connexion hybride (HCM, Hybrid Connection Manager) sur votre réseau. HCM doit pouvoir se connecter à Azure et à votre application. Cette solution est particulièrement efficace à partir d’un réseau distant, tel que votre réseau local ou un autre réseau hébergé dans le cloud, car elle ne nécessite pas de point de terminaison accessible sur Internet. HCM s’exécute uniquement sous Windows. Vous pouvez exécuter jusqu’à cinq instances pour bénéficier d’une haute disponibilité. Cependant, les connexions hybrides prennent en charge uniquement le protocole TCP, et chaque point de terminaison de connexion hybride doit correspondre à une combinaison hôte:port spécifique. 

Avec un environnement App Service, vous pouvez exécuter une instance d’Azure App Service dans votre réseau virtuel. Ainsi, vos applications peuvent accéder aux ressources de votre réseau virtuel sans étapes supplémentaires. Un environnement App Service offre d’autres avantages. Par exemple, vous pouvez utiliser des workers basés sur Dv2 avec jusqu’à 14 Go de RAM. En outre, vous pouvez étendre le système selon vos besoins. Contrairement aux environnements mutualisés où ASP est limité à 20 instances, dans un environnement App Service, vous pouvez évoluer jusqu'à 100 instances d’ASP. Un des avantages d’un ASE que n’offre pas l’intégration au réseau virtuel, est la possibilité d’utiliser un environnement App Service avec un réseau VPN ExpressRoute. 

Même si, dans certains cas, il est possible d’utiliser ces deux fonctionnalités, l’une ne remplace pas l’autre. Le choix de la fonctionnalité à utiliser dépend de vos besoins. Par exemple : 

* Si vous êtes un développeur et que vous souhaitez simplement exécuter un site dans Azure et lui permettre d’accéder à la base de données de votre station de travail locale, la solution la plus simple consiste à utiliser des connexions hybrides. 
* Si vous êtes une organisation de grande envergure et que vous souhaitez placer un grand nombre de propriétés web dans le cloud public et les gérer dans votre propre réseau, il est préférable de recourir à un environnement App Service. 
* Si vous possédez de nombreuses applications hébergées par App Service et que vous souhaitez simplement accéder aux ressources de votre réseau virtuel, privilégiez l’intégration au réseau virtuel. 

Au-delà de ces cas d’utilisation, certains aspects liés à la simplicité doivent être pris en compte. Si votre réseau virtuel est déjà connecté à votre réseau local, l’utilisation de l’intégration au réseau virtuel ou d’un environnement App Service est un moyen simple de consommer des ressources locales. En revanche, si votre réseau virtuel n’est pas connecté à votre réseau local, la configuration d’un réseau VPN de site à site avec votre réseau virtuel sera beaucoup plus complexe que l’installation du Gestionnaire de connexion hybride. 

Outre les différences fonctionnelles, il existe également des différences de tarification. L’environnement App Service est un service Premium offrant des possibilités de configuration de réseau inégalées et des fonctionnalités remarquables. L’intégration au réseau virtuel peut être utilisée avec des plans App Service Standard ou Premium. Elle est idéale pour la consommation sécurisée des ressources dans votre réseau virtuel à partir du système App Service mutualisé. Actuellement, les connexions hybrides dépendent d’un compte BizTalk dont les niveaux de tarification sont variables : une solution gratuite est proposée, puis le tarif augmente progressivement selon le nombre de connexions requises. Cependant, si vous devez utiliser de nombreux réseaux, les connexions hybrides représentent la solution idéale : elles vous permettent d’accéder aux ressources de plus de 100 réseaux distincts. 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
