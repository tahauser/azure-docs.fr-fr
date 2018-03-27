---
title: Configurer votre environnement Azure App Service pour le tunneliser de force
description: Configurez votre environnement App Service pour qu’il fonctionne quand du trafic sortant est tunnelisé de force
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 3/6/2018
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 92073cd29f29c1ddf5863e23c4a12dfdf8e21598
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurer votre environnement App Service avec le tunneling forcé

L’environnement ASE (App Service Environment) est un déploiement d’Azure App Service dans le réseau virtuel Azure d’un client. De nombreux clients configurent leurs réseaux virtuels Azure en tant qu’extensions de leurs réseaux locaux avec des réseaux privés virtuels ou des connexions Azure ExpressRoute. Le tunneling forcé consiste à rediriger le trafic Internet sortant vers votre réseau privé virtuel ou vers une appliance virtuelle. Cette méthode est souvent utilisée pour répondre à des exigences de sécurité visant à inspecter et auditer tout le trafic sortant. 

L’environnement ASE a de nombreuses dépendances externes, décrites dans ce document sur [l’architecture réseau de l’environnement App Service][network]. Normalement, tout le trafic de dépendance sortant de l’ASE doit passer par l’adresse IP virtuelle qui est configurée avec l’ASE. Si vous modifiez le routage du trafic vers ou depuis l’ASE sans tenir compte des informations ci-dessous, votre ASE cessera de fonctionner.

Dans un réseau virtuel Azure, le routage repose sur la correspondance de préfixe la plus longue. S’il existe plusieurs itinéraires avec la même correspondance de préfixe la plus longue, un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :

* Itinéraire défini par l’utilisateur (UDR)
* Itinéraire BGP (lorsque ExpressRoute est utilisé)
* Itinéraire du système

Pour en savoir plus sur le routage dans un réseau virtuel, consultez [Itinéraires définis par l’utilisateur et transfert IP][routes]. 

Si vous souhaitez acheminer le trafic sortant de votre environnement ASE vers une autre destination que directement vers Internet, vous pouvez choisir parmi les options suivantes :

* Autoriser votre ASE à avoir un accès direct à Internet
* Configurer votre sous-réseau ASE pour qu’il utilise des points de terminaison de service vers Azure SQL et Stockage Azure
* Ajouter vos propres adresses IP au pare-feu Azure SQL de l’ASE

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Configurer votre environnement App Service pour qu’il dispose d’un accès Internet direct

Pour activer votre ASE afin qu’il accède directement à Internet, même si votre réseau virtuel Azure est configuré avec ExpressRoute, vous pouvez :

* Configurer ExpressRoute pour qu’il publie 0.0.0.0/0. Par défaut, il achemine de force tout le trafic sortant local.
* Créer un UDR avec le préfixe d’adresse 0.0.0.0/0 et le type de tronçon Internet suivant, puis l’appliquer au sous-réseau ASE.

Si vous apportez ces deux modifications, le trafic à destination d’Internet provenant du sous-réseau de l’environnement App Service n’est plus acheminé de force via la connexion ExpressRoute.

> [!IMPORTANT]
> Les itinéraires définis dans un UDR doivent être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L’exemple précédent utilise la plage d’adresses 0.0.0.0/0 large. Il peut potentiellement être remplacé accidentellement par des annonces de routage utilisant des plages d’adresses plus spécifiques.
>
> Les environnements App Service ne sont pas pris en charge avec les configurations ExpressRoute qui annoncent de façon croisée des itinéraires à partir du chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent les publications de routage de Microsoft. Les publications contiennent un grand ensemble de plages d’adresses Microsoft Azure. Si ces plages d’adresses sont publiées de façon croisée sur le chemin d’accès d’homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l’environnement App Service sont acheminés vers l’infrastructure réseau locale d’un client. Ce flux de réseau n’est pas pris en charge par défaut par les environnements App Service. L’une des solutions à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée. Une autre solution consiste à autoriser votre environnement App Service à fonctionner dans une configuration de tunneling forcé.

![Accès direct à Internet][1]

## <a name="configure-your-ase-with-service-endpoints"></a>Configurer votre ASE avec des points de terminaison de service

Pour acheminer tout le trafic sortant à partir de votre ASE, à l’exception de celui qui est acheminé vers Azure SQL et Stockage Azure, procédez comme suit :

1. Créez une table de routage et affectez-la à votre sous-réseau ASE. Pour retrouver les adresses qui correspondent à votre région, consultez [Adresses de gestion de l’environnement Azure App Service][management]. Créez des itinéraires pour ces adresses avec un tronçon Internet suivant. Cela est nécessaire car le trafic de gestion entrant de l’environnement App Service doit répondre à partir de la même adresse que celle à laquelle il a été envoyé.   

2. Activer des points de terminaison de service avec Azure SQL et Stockage Azure pour votre sous-réseau ASE

Les points de terminaison de service vous permettent de restreindre l’accès aux services multilocataires à un ensemble de sous-réseaux et de réseaux virtuels Azure. Pour en savoir plus sur les points de terminaison de service, consultez la documentation [Points de terminaison de service de réseau virtuel][serviceendpoints]. 

Lorsque vous activez les points de terminaison de service sur une ressource, certains itinéraires sont créés avec une priorité plus élevée que d’autres. Si vous utilisez des points de terminaison de service avec un ASE tunnelisé de force, le trafic de gestion Azure SQL et Stockage Azure n’est pas tunnelisé de force. L’autre trafic de dépendance ASE est tunnelisé de force et ne peut pas être perdu, ou l’ASE ne fonctionnerait pas correctement.

Lorsque les points de terminaison de service sont activés sur un sous-réseau avec une instance Azure SQL, toutes les instances Azure SQL connectées à partir de ce sous-réseau doivent avoir des points de terminaison de service activés. Si vous souhaitez accéder à plusieurs instances Azure SQL à partir du même sous-réseau, vous ne pouvez pas activer les points de terminaison de service sur une instance Azure SQL et pas sur une autre.  Stockage Azure ne se comporte pas de la même manière qu’Azure SQL.  Lorsque vous activez les points de terminaison de service avec Stockage Azure, vous verrouillez l’accès à cette ressource à partir de votre sous-réseau, mais pourrez toujours accéder aux autres comptes Stockage Azure même si les points de terminaison de service ne sont pas activés.  

Si vous configurez le tunneling forcé avec une appliance de filtre de réseau, n’oubliez pas que l’ASE possède un certain nombre de dépendances en plus d’Azure SQL et de Stockage Azure. Vous devez autoriser ce trafic, ou l’ASE ne fonctionnera pas correctement.

![Tunneling forcé avec points de terminaison de service][2]

## <a name="add-your-own-ips-to-the-ase-azure-sql-firewall"></a>Ajouter vos propres adresses IP au pare-feu Azure SQL de l’ASE ##

Pour tunneliser tout le trafic sortant à partir de votre ASE, à l’exception de celui qui est acheminé vers Stockage Azure, procédez comme suit :

1. Créez une table de routage et affectez-la à votre sous-réseau ASE. Pour retrouver les adresses qui correspondent à votre région, consultez [Adresses de gestion de l’environnement Azure App Service][management]. Créez des itinéraires pour ces adresses avec un tronçon Internet suivant. Cela est nécessaire car le trafic de gestion entrant de l’environnement App Service doit répondre à partir de la même adresse que celle à laquelle il a été envoyé. 

2. Activer des points de terminaison de service avec Stockage Azure pour votre sous-réseau ASE

3. Recherchez les adresses qui seront utilisés pour tout le trafic sortant de votre environnement App Service vers Internet. Si vous effectuez un tunneling forcé, il s’agit de vos adresses NAT ou adresses IP de passerelle. Si vous voulez acheminer le trafic sortant de l’environnement App Service via une appliance virtuelle réseau, l’adresse de sortie est l’adresse IP publique de l’appliance.

4. _Pour définir les adresses de sortie dans un environnement App Service existant :_ accédez à resource.azure.com, puis à Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Vous voyez ainsi le code JSON qui décrit votre environnement App Service. Vérifiez que la mention **read/write** apparaît au début. Sélectionnez **Modifier**. Faites défiler vers le bas. Modifiez la valeur **userWhitelistedIpRanges** **null** en quelque chose comme ce qui suit. Utiliser les adresses que vous souhaitez définir en tant que plage d’adresses de sortie. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Sélectionnez **PUT** (Placer) en haut. Cette option déclenche une opération de mise à l’échelle de votre environnement App Service et ajuste le pare-feu.

_Pour créer votre ASE avec les adresses de sortie_ : suivez les instructions de [Créer un environnement App Service à l’aide d’un modèle][template] et extrayez le modèle approprié.  Modifiez la section « resources » dans le fichier azuredeploy.json, mais pas dans le bloc « properties », et incluez une ligne pour **userWhitelistedIpRanges** avec vos valeurs.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/hostingEnvironments",
        "name": "[parameters('aseName')]",
        "kind": "ASEV2",
        "location": "[parameters('aseLocation')]",
        "properties": {
          "name": "[parameters('aseName')]",
          "location": "[parameters('aseLocation')]",
          "ipSslAddressCount": 0,
          "internalLoadBalancingMode": "[parameters('internalLoadBalancingMode')]",
          "dnsSuffix" : "[parameters('dnsSuffix')]",
          "virtualNetwork": {
            "Id": "[parameters('existingVnetResourceId')]",
            "Subnet": "[parameters('subnetName')]"
          },
        "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]
        }
      }
    ]

Ces modifications envoient le trafic directement vers Stockage Azure à partir de l’ASE et autorisent l’accès à Azure SQL à partir des adresses supplémentaires autres que l’adresse IP virtuelle de l’ASE.

   ![Tunneling forcé avec une liste verte SQL][3]

## <a name="preventing-issues"></a>Éviter les problèmes ##

Si la communication entre l’ASE et ses dépendances est interrompue, l’ASE sera défectueux.  S’il reste défectueux trop longtemps, l’ASE sera suspendu. Pour annuler la suspension de l’ASE, suivez les instructions de votre portail ASE.

Outre la simple interruption de la communication, vous pouvez nuire à votre ASE si vous introduisez trop de latence. Une trop grande latence peut se produire si votre ASE est trop loin de votre réseau local.  « Trop loin » peut signifier traverser un océan ou un continent pour atteindre votre réseau local, par exemple. De la latence peut également être introduite en raison d’une surcharge de l’Intranet ou de contraintes de bande passante sortante.


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/asedependencies.png
[2]: ./media/forced-tunnel-support/forcedtunnelserviceendpoint.png
[3]: ./media/forced-tunnel-support/forcedtunnelexceptstorage.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
