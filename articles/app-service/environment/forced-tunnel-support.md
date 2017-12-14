---
title: "Configurer votre environnement Azure App Service pour le tunnéliser de force"
description: "Configurez votre environnement App Service pour qu’il fonctionne quand du trafic sortant est tunnélisé de force"
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
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurer votre environnement App Service avec le tunneling forcé

L’environnement Azure App Service est un déploiement d’Azure App Service dans une instance de réseau virtuel Azure d’un client. De nombreux clients configurent leurs réseaux virtuels en tant qu’extensions de leurs réseaux locaux avec des réseaux privés virtuels ou des connexions Azure ExpressRoute. En raison des stratégies d’entreprise ou d’autres contraintes de sécurité, ils configurent des itinéraires pour envoyer tout le trafic sortant localement avant qu’il ne gagne Internet. Le processus consistant à changer le routage du réseau virtuel afin que le trafic sortant du réseau virtuel rejoigne l’infrastructure locale en empruntant la connexion VPN ou ExpressRoute est appelé « tunneling forcé ». 

Le tunneling forcé peut entraîner des problèmes pour un environnement App Service. L’environnement App Service a de nombreuses dépendances externes, énumérées dans ce document sur [l’architecture réseau de l’environnement App Service][network]. Par défaut, l’environnement App Service requiert que toutes les communications sortantes passent par l’adresse IP virtuelle qui est provisionnée avec l’environnement App Service.

Les itinéraires sont un aspect essentiel de la nature du tunneling forcé et de son exploitation. Dans un réseau virtuel Azure, le routage repose sur la correspondance de préfixe la plus longue. S’il existe plusieurs itinéraires avec la même correspondance de préfixe la plus longue, un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :

* Itinéraire défini par l’utilisateur (UDR)
* Itinéraire BGP (lorsque ExpressRoute est utilisé)
* Itinéraire du système

Pour en savoir plus sur le routage dans un réseau virtuel, consultez [Itinéraires définis par l’utilisateur et transfert IP][routes]. 

Si vous souhaitez que votre environnement App Service fonctionne dans un réseau virtuel à tunnel forcé, vous avez deux possibilités :

* Configurez votre environnement App Service pour qu’il dispose d’un accès Internet direct.
* Changez le point de terminaison de sortie pour votre environnement App Service.

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>Configurer votre environnement App Service pour qu’il dispose d’un accès Internet direct

Pour que votre environnement App Service fonctionne alors que votre réseau virtuel est configuré avec une connexion ExpressRoute, vous pouvez :

* Configurer ExpressRoute pour qu’il publie 0.0.0.0/0. Par défaut, il tunnélise de force tout le trafic sortant local.
* Créer un UDR. L’appliquer au sous-réseau qui contient l’environnement App Service, avec le préfixe d’adresse 0.0.0.0/0 et le type de tronçon suivant Internet.

Si vous apportez ces deux modifications, le trafic à destination d’Internet provenant du sous-réseau de l’environnement App Service n’est plus acheminé de force via la connexion ExpressRoute et l’environnement App Service peut fonctionner.

> [!IMPORTANT]
> Les itinéraires définis dans un UDR doivent être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L’exemple précédent utilise la plage d’adresses 0.0.0.0/0 large. Il peut potentiellement être remplacé accidentellement par des annonces de routage utilisant des plages d’adresses plus spécifiques.
>
> Les environnements App Service ne sont pas pris en charge avec les configurations ExpressRoute qui annoncent de façon croisée des itinéraires à partir du chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent les publications de routage de Microsoft. Les publications contiennent un grand ensemble de plages d’adresses IP de Microsoft Azure. Si ces plages d’adresses sont publiées de façon croisée sur le chemin d’accès d’homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l’environnement App Service sont tunnélisés de force vers l’infrastructure réseau local d’un client. Ce flux de réseau n’est actuellement pas pris en charge par défaut par les environnements App Service. L’une des solutions à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée. Une autre solution consiste à autoriser votre environnement App Service à fonctionner dans une configuration de tunneling forcé.

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>Changer le point de terminaison de sortie pour votre environnement App Service ##

Cette section explique comment autoriser un environnement App Service à fonctionner dans une configuration de tunneling forcé en changeant le point de terminaison de sortie utilisé par l’environnement App Service. Si le trafic sortant de l’environnement App Service est tunnélisé de force vers un réseau local, vous devez autoriser ce trafic à émaner d’adresses IP autres que l’adresse IP virtuelle de l’environnement App Service.

Un environnement App Service a non seulement des dépendances externes, mais doit également écouter le trafic entrant et répondre à ce trafic. Les réponses ne peuvent pas être envoyées à partir d’une autre adresse bloquant TCP. Il existe trois étapes requises pour changer le point de terminaison de sortie pour l’environnement App Service :

1. Définissez une table de routage pour que le trafic de gestion entrant puisse repasser par la même adresse IP.

2. Ajoutez les adresses IP à utiliser en sortie vers le pare-feu de l’environnement App Service.

3. Définissez les itinéraires du trafic sortant à partir de l’environnement App Service à tunnéliser.

   ![Flux réseau avec tunneling forcé][1]

Vous pouvez configurer l’environnement App Service avec des adresses en sortie différentes lorsque l’environnement App Service est déjà configuré et opérationnel. Elles peuvent également être définies pendant le déploiement de l’environnement App Service.

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>Changer l’adresse en sortie une fois que l’environnement App Service est opérationnel ###
1. Récupérez les adresses IP que vous voulez utiliser comme adresses IP de sortie pour votre environnement App Service. Si vous effectuez un tunneling forcé, ces adresses proviennent de vos NAT ou adresses IP de passerelle. Si vous voulez acheminer le trafic sortant de l’environnement App Service via une appliance virtuelle réseau, l’adresse de sortie est l’adresse IP publique de l’appliance.

2. Définissez les adresses de sortie dans vos informations de configuration de l’environnement App Service. Accédez à resource.azure.com, puis à Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>. Vous voyez ainsi le code JSON qui décrit votre environnement App Service. Vérifiez que la mention **read/write** apparaît au début. Sélectionnez **Modifier**. Faites défiler vers le bas et modifiez la valeur **userWhitelistedIpRanges** **null** en quelque chose comme ce qui suit. Utiliser les adresses que vous souhaitez définir en tant que plage d’adresses de sortie. 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   Sélectionnez **PUT** (Placer) en haut. Cette option déclenche une opération de mise à l’échelle de votre environnement App Service et ajuste le pare-feu.
 
3. Créez ou modifiez une table de routage et remplissez les règles qui autorisent l’accès aux (et depuis les) adresses de gestion qui correspondent à l’emplacement de votre environnement App Service. Pour retrouver les adresses de gestion, consultez [Adresses de gestion de l’environnement Azure App Service][management].

4. Ajustez les itinéraires appliqués au sous-réseau de l’environnement App Service avec une table de routage ou des itinéraires BGP. 

Si l’environnement App Service ne répond pas à partir du portail, un problème affecte vos modifications. Le problème peut-être que votre liste d’adresses de sortie était incomplète, ou que le trafic a été perdu ou bloqué. 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>Créer un environnement App Service avec une adresse de sortie différente ###

Si votre réseau virtuel est déjà configuré pour tunnéliser de force tout le trafic, vous devez suivre des étapes supplémentaires pour créer votre environnement App Service de manière à ce qu’il soit opérationnel. Vous devez autoriser l’utilisation d’un autre point de terminaison de sortie pendant la création de l’environnement App Service. Pour ce faire, vous devez créer l’environnement App Service avec un modèle qui spécifie les adresses de sortie autorisées.

1. Récupérez les adresses IP à utiliser comme adresses de sortie pour votre environnement App Service.

2. Créez au préalable le sous-réseau que doit utiliser l’environnement App Service. Vous devez le faire pour pouvoir définir des itinéraires et également parce que le modèle l’exige.

3. Créez une table de routage avec les adresses IP de gestion qui correspondent à l’emplacement de votre environnement App Service. Affectez-la à votre environnement App Service.

4. Suivez les instructions de [Créer un environnement App Service avec un modèle][template]. Extrayez le modèle approprié.

5. Modifiez la section « resources » du fichier azuredeploy.json. Incluez une ligne pour **userWhitelistedIpRanges** avec vos valeurs, comme ce qui suit :

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Si cette section est correctement configurée, l’environnement App Service doit démarrer sans problème. 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
