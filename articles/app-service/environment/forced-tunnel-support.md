---
title: "Configurer votre environnement Azure App Service pour le tunnéliser de force"
description: "Configurez votre environnement App Service pour qu’il fonctionne quand du trafic sortant est tunnélisé de force."
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>Configurer votre environnement App Service avec le tunneling forcé

L’environnement ASE (App Service Environment) est un déploiement d’Azure App Service dans le réseau virtuel Azure d’un client. De nombreux clients configurent leurs réseaux virtuels en tant qu’extensions de leurs réseaux locaux avec des réseaux privés virtuels ou des connexions ExpressRoute. En raison des stratégies d’entreprise ou d’autres contraintes de sécurité, ils configurent des itinéraires pour envoyer tout le trafic sortant localement avant qu’il ne gagne Internet. Le processus consistant à changer le routage du réseau virtuel afin que le trafic sortant du réseau virtuel rejoigne l’infrastructure locale en empruntant la connexion VPN ou ExpressRoute est appelé « tunneling forcé ».  

Le tunneling forcé peut entraîner des problèmes pour un ASE. L’ASE a de nombreuses dépendances externes, énumérées dans ce document sur [l’architecture réseau de l’ASE][network]. Par défaut, l’ASE requiert que toutes les communications sortantes passent par l’adresse IP virtuelle qui est provisionnée avec l’ASE.

Les itinéraires sont un aspect essentiel de la nature du tunneling forcé et de son exploitation. Dans un réseau virtuel Azure, le routage repose sur la correspondance de préfixe la plus longue.  S’il existe plusieurs itinéraires avec la même correspondance de préfixe la plus longue, un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :

1. Itinéraire défini par l’utilisateur
1. Itinéraire BGP (lorsque ExpressRoute est utilisé)
1. Itinéraire du système

Pour en savoir plus sur le routage dans un réseau virtuel, consultez [Itinéraires définis par l’utilisateur et transfert IP][routes]. 

Si vous souhaitez que votre ASE fonctionne dans un réseau virtuel à tunneling forcé, vous avez deux possibilités :

1. Autoriser votre ASE à avoir un accès direct à Internet
1. Changer le point de terminaison de sortie pour votre ASE

## <a name="enable-your-ase-to-have-direct-internet-access"></a>Autoriser votre ASE à avoir un accès direct à Internet

Pour que votre ASE fonctionne quand votre réseau virtuel est configuré avec un circuit ExpressRoute, vous pouvez :

* Configurer ExpressRoute pour qu’il publie 0.0.0.0/0. Par défaut, il tunnélise de force tout le trafic sortant local.
* Créer un UDR. Appliquez le au sous-réseau qui contient l’ASE, avec le préfixe d’adresse 0.0.0.0/0 et le type de tronçon Internet suivant.

Si vous apportez ces deux modifications, le trafic à destination d’Internet provenant du sous-réseau de l’ASE n’est plus acheminé de force via le circuit ExpressRoute et l’ASE peut fonctionner.

> [!IMPORTANT]
> Les itinéraires définis dans un UDR doivent être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L’exemple précédent utilise la plage d’adresses 0.0.0.0/0 large. Il peut potentiellement être remplacé accidentellement par des annonces de routage utilisant des plages d’adresses plus spécifiques.
>
> Les ASE ne sont pas pris en charge avec les configurations ExpressRoute qui annoncent de façon croisée des itinéraires à partir du chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée. Les configurations ExpressRoute ayant une homologation publique configurée reçoivent les publications de routage de Microsoft. Les publications contiennent un grand ensemble de plages d’adresses IP de Microsoft Azure. Si ces plages d’adresses sont publiées de façon croisée sur le chemin d’accès d’homologation privée, il en résulte que tous les paquets réseau sortants du sous-réseau de l’environnement App Service sont tunnélisés de force vers l’infrastructure réseau local d’un client. Par défaut, ce flux de réseau n’est pas pris en charge par les environnements App Service. L’une des solutions à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d’accès d’homologation publique vers le chemin d’accès d’homologation privée.  L’autre solution consiste à autoriser votre ASE à travailler dans une configuration de tunneling forcé.

## <a name="change-the-egress-endpoint-for-your-ase"></a>Changer le point de terminaison de sortie pour votre ASE ##

Cette section explique comment autoriser un ASE à fonctionner dans une configuration de tunneling forcé en changeant le point de terminaison de sortie utilisé par l’ASE. Si le trafic sortant de l’ASE est tunnélisé de force vers un réseau local, vous devez autoriser ce trafic à émaner d’adresses IP autres que l’adresse IP virtuelle de l’ASE.

Un ASE a non seulement des dépendances externes, mais il doit également écouter le trafic entrant pour sa propre gestion. L’ASE doit être en mesure de répondre à ce type de trafic. En outre, pour que soit préservée la connexion TCP, les réponses ne peuvent pas être retournées à partir d’une autre adresse.  Il existe donc trois étapes requises pour changer le point de terminaison de sortie pour l’ASE.

1. Définir une table de routage pour que le trafic de gestion entrant puisse repasser par la même adresse IP
1. Ajouter les adresses IP à utiliser en sortie vers le pare-feu de l’ASE
1. Définir les itinéraires du trafic sortant à partir de l’ASE à tunnéliser

![Flux réseau avec tunneling forcé][1]

Vous pouvez configurer l’ASE avec différentes adresses de sortie une fois qu’il est configuré et opérationnel, ou vous pouvez les définir durant le déploiement de l’ASE.  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>Modification de l’adresse de sortie une fois que l’ASE est opérationnel ###
1. Récupérez les adresses IP à utiliser comme adresses IP de sortie pour votre ASE. Si vous effectuez un tunneling forcé, il s’agit de vos adresses NAT ou adresses IP de passerelle.  Si vous voulez acheminer le trafic sortant de l’ASE via une appliance virtuelle réseau, l’adresse de sortie est l’adresse IP publique de l’appliance.
2. Définissez les adresses de sortie dans vos informations de configuration de l’ASE. Pour voir le code json qui décrit votre ASE, accédez à resource.azure.com, puis à Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>.  Vérifiez que la mention « read/write »apparaît au début.  Cliquez sur Edit (Modifier), faites défiler vers le bas et changez userWhitelistedIpRanges de  

       "userWhitelistedIpRanges": null 
      
  en quelque chose comme ce qui suit : Utiliser les adresses que vous souhaitez définir en tant que plage d’adresses de sortie. 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  Cliquez sur PUT (Placer) en haut. Cette opération déclenche une opération de mise à l’échelle sur votre ASE et ajuste le pare-feu.
   
3. Créez ou modifiez une table de routage et remplissez les règles qui autorisent l’accès aux (et depuis les) adresses de gestion qui correspondent à l’emplacement de votre ASE.  Les adresses de gestion sont ici : [Adresses de gestion de l’environnement Azure App Service][management] 

4. Ajustez les itinéraires appliqués au sous-réseau de l’ASE avec une table de routage ou des itinéraires BGP.  

Si l’ASE ne répond pas à partir du portail, un problème affecte vos modifications.  Peut-être que votre liste d’adresses de sortie était incomplète, ou que le trafic a été perdu ou bloqué.  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>Créer un ASE avec une adresse de sortie différente  ###

Si votre réseau virtuel est déjà configuré pour tunnéliser de force tout le trafic, vous devez suivre des étapes supplémentaires pour créer votre ASE de manière à ce qu’il soit opérationnel. Cela signifie que vous devez autoriser l’utilisation d’un autre point de terminaison de sortie pendant la création de l’ASE.  Pour ce faire, vous devez créer l’ASE avec un modèle qui spécifie les adresses de sortie autorisées.

1. Récupérez les adresses IP à utiliser comme adresses de sortie pour votre ASE.
1. Créez au préalable le sous-réseau que doit utiliser l’ASE. Cette opération est nécessaire pour définir des itinéraires et également parce que le modèle l’exige.  
1. Créez une table de routage avec les adresses IP de gestion qui correspondent à l’emplacement de votre ASE et affectez-la à votre ASE.
1. Suivez les instructions indiquées dans [Création d’un environnement App Service avec un modèle][template], puis extrayez le modèle approprié.
1. Modifiez la section « resources » du fichier azuredeploy.json. Incluez une ligne pour **userWhitelistedIpRanges** avec vos valeurs, comme dans l’exemple suivant :

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

Si la configuration est correcte, l’ASE doit démarrer sans problème.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
