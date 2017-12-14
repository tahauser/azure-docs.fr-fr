---
title: "Questions fréquentes (FAQ) concernant les problèmes de connectivité et de mise en réseau pour Microsoft Azure Cloud Services | Microsoft Docs"
description: "Cet article répertorie les questions fréquentes sur la connectivité et la mise en réseau pour Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: genli
ms.openlocfilehash: d86f3ec043c504c9d79b18f1f0b4c9cf0adb115b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problèmes de connectivité et de mise en réseau pour Azure Cloud Services : questions fréquentes (FAQ)

Cet article comprend des questions fréquentes sur les problèmes de connectivité et de mise en réseau pour [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Impossible de réserver une adresse IP dans un service cloud à plusieurs adresses IP virtuelles
Tout d’abord, vérifiez que l’instance de machine virtuelle pour laquelle vous tentez de réserver l’adresse IP est activée. Ensuite, veillez à utiliser des adresses IP réservées aussi bien pour les déploiements intermédiaires que pour les déploiements de production. **Ne** modifiez pas les paramètres pendant la mise à niveau du déploiement.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Comment établir un Bureau à distance lorsque je possède un groupe de sécurité réseau ?
Ajoutez des règles à un groupe de sécurité réseau qui autorisent le trafic sur les ports **3389** et **20000**.  Bureau à distance utilise le port **3389**.  Les instances de service cloud sont soumis à l’équilibrage de charge, donc vous ne pouvez pas contrôler directement à quelle instance vous vous connectez.  Les agents *RemoteForwarder* et *RemoteAccess* gèrent le trafic RDP, et permettent au client d’envoyer un cookie RDP et de spécifier une instance individuelle à laquelle se connecter.  Les agents *RemoteForwarder* et *RemoteAccess* exigent que ce port **20000** soit ouvert : il peut être bloqué si vous avez un groupe de sécurité réseau.

## <a name="can-i-ping-a-cloud-service"></a>Est-il possible d’effectuer un test ping sur un service cloud ?

Non, pas en utilisant le test « ping » normal/protocole ICMP. Le protocole ICMP n’est pas autorisé via l’équilibreur de charge Azure.

Pour tester la connectivité, nous vous recommandons d’effectuer un test ping au niveau du port. Si Ping.exe utilise ICMP, d’autres outils tels que PSPing, Nmap et telnet permettent de tester la connectivité sur un port TCP spécifique.

Pour plus d’informations, consultez [Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/) (Tester la connectivité des machines virtuelles Azure à l’aide de tests ping au niveau du port à la place d’ICMP).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>Comment éviter de recevoir des milliers d’accès en provenance d’adresses IP inconnues qui portent à croire que le service cloud est la cible d’une sorte d’attaque malveillante ?
Azure met en œuvre une sécurité réseau multicouche pour protéger ses services de plateforme contre les attaques par déni de service distribué (DDoS). Le système de défense Azure DDoS fait partie intégrante du processus de surveillance continu d’Azure, qui est constamment amélioré à travers des tests de pénétration. Ce système de défense DDoS est conçu non seulement pour résister aux attaques extérieures, mais aussi à celles perpétrées par d’autres locataires Azure. Pour plus d’informations, consultez [Sécurité réseau Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Vous pouvez aussi créer une tâche de démarrage pour bloquer de manière sélective certaines adresses IP spécifiques. Pour plus d’informations, consultez [Bloquer une adresse IP spécifique](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quand j’essaie de me connecter à mon instance de service cloud via RDP, j’obtiens le message, « Le compte d’utilisateur a expiré ».
Vous pouvez obtenir le message d’erreur « Ce compte d’utilisateur a expiré » quand vous ne tenez pas compte de la date d’expiration configurée dans vos paramètres RDP. Vous pouvez modifier la date d’expiration à partir du portail en procédant comme suit :
1. Connectez-vous au [portail Azure](https://portal.azure.com), naviguez jusqu’à votre service cloud et sélectionnez l’onglet **Bureau à distance**.
2. Sélectionnez l’emplacement de déploiement **Production** ou **Intermédiaire**.
3. Modifiez la date dans le champ « Expire le », puis enregistrez la configuration.

Vous devriez maintenant pouvoir vous connecter à votre machine via RDP.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Pourquoi le service LoadBalancer n’équilibre-t-il pas équitablement le trafic ?
Pour plus d’informations sur le fonctionnement interne de l’équilibreur de charge, consultez [Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Nouveau mode de distribution d’Azure Load Balancer).

L’algorithme de distribution utilisé est un hachage à 5 tuples (IP source, port source, IP de destination, port de destination, type de protocole) pour mapper le trafic aux serveurs disponibles. Il fournit l’adhérence uniquement dans une session de transport. Les paquets de la même session TCP ou UDP sont dirigés vers la même instance IP (DIP) de centre de données derrière le point de terminaison d’équilibrage de charge. Lorsque le client ferme et rouvre la connexion ou démarre une nouvelle session à partir du même IP source, le port source change et contraint le trafic à se diriger vers un autre point de terminaison DIP.

## <a name="how-can-i-redirect-the-incoming-traffic-to-my-default-url-of-cloud-service-to-a-custom-url"></a>Comment puis-je rediriger le trafic entrant sur mon URL de service cloud par défaut vers une URL personnalisée ? 

Le module de réécriture d’URL pour IIS peut servir à rediriger le trafic arrivant sur l’URL par défaut du service cloud (par exemple, \*. cloudapp.net) vers des noms DNS/URL personnalisés. Étant donné que le module de réécriture d’URL est activé par défaut sur les rôles Web et que ses règles sont configurées dans le fichier web.config de l’application, il serait toujours disponible sur la machine virtuelle, indépendamment des redémarrages/réinitialisations. Pour plus d'informations, consultez les pages suivantes :

- [Création de règles de réécriture pour le module de réécriture d’URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Guide pratique pour supprimer le lien par défaut](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-the-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Comment puis-je bloquer/désactiver le trafic entrant sur l’URL par défaut de mon service cloud ? 

Si vous souhaitez empêcher l’arrivée du trafic entrant sur l’URL/nom par défaut de votre service cloud (par exemple, \*. cloudapp.net), définissez l’en-tête d’hôte sur un nom DNS personnalisé (par exemple, www.MyCloudService.com) dans la configuration de liaison de site du fichier de définition de service cloud (*.csdef) comme indiqué ci-dessous : 
 

    <?xml version="1.0" encoding="utf-8"?> 
    <ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6"> 
      <WebRole name="MyWebRole" vmsize="Small"> 
        <Sites> 
          <Site name="Web"> 
            <Bindings> 
              <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" /> 
            </Bindings> 
          </Site> 
        </Sites> 
        <Endpoints> 
          <InputEndpoint name="Endpoint1" protocol="http" port="80" /> 
        </Endpoints> 
        <ConfigurationSettings> 
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" /> 
        </ConfigurationSettings> 
      </WebRole> 
    </ServiceDefinition> 
 
Étant donné que cette liaison d’en-tête d’hôte est appliquée via le fichier csdef, le service serait uniquement accessible via le nom personnalisé 'www.MyCloudService.com', tandis que toutes les requêtes entrantes sur le domaine '*.cloudapp.net' échoueraient toujours. Cela dit, notez toutefois que si vous utilisez une sonde SLB personnalisée ou un équilibreur de charge interne dans le service, le blocage de l’URL/nom par défaut du service risque d’affecter le comportement de sondage. 

## <a name="how-to-make-sure-the-public-facing-ip-address-of-a-cloud-service-aka-vip-never-changes-so-that-it-could-be-customarily-whitelisted-by-few-specific-clients"></a>Comment s’assurer que l’adresse IP publique d’un service cloud (aka, adresse IP virtuelle) ne change jamais afin de pouvoir être habituellement dans la liste approuvée par des clients spécifiques ?

Pour ajouter l’adresse IP de vos services cloud à la liste verte, nous vous recommandons d’avoir une adresse IP réservée associée à ces derniers. Dans le cas contraire, l’adresse IP virtuelle fournie par Azure ne sera plus allouée à votre abonnement si vous supprimez le déploiement. Notez que pour garantir le correct échange d’adresses IP virtuelles, vous devriez disposer d’adresses IP réservées individuelles pour les emplacements de production et de préproduction, sans quoi l’échange d’adresses échouera. Suivez les instructions des articles suivants pour réserver une adresse IP et l’associer à vos services cloud :  
 
- [Réserver l’adresse IP d’un service cloud existant](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Associer une adresse IP réservée à un service cloud à l’aide d’un fichier de configuration de service](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Tant que vous avez plus d’une instance pour vos rôles, l’association d’un protocole RIP à votre service cloud ne devrait pas provoquer de temps d’arrêt. Vous pouvez également ajouter à la liste verte la plage d’adresses IP de votre centre de données Azure. Vous trouverez toutes les plages d’adresses IP Azure [ici](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Ce fichier contient les plages d’adresses IP (y compris les plages de calcul, SQL et de stockage) utilisées dans les centres de données Microsoft Azure. Un fichier mis à jour est publié chaque semaine et reflète les plages actuellement déployées et toutes les modifications à venir dans les plages IP. Les nouvelles plages figurant dans le fichier ne seront pas utilisées dans les centres de données avant une semaine minimum. Téléchargez le nouveau fichier xml chaque semaine et effectuez les modifications nécessaires sur votre site pour identifier correctement les services qui s’exécutent dans Azure. Les utilisateurs d’Express Route peuvent remarquer ce fichier utilisé pour mettre à jour la publication BGP de l’espace Azure la première semaine de chaque mois. 

## <a name="how-can-i-use-azure-resource-manager-vnets-with-cloud-services"></a>Comment puis-je utiliser des réseaux virtuels Azure Resource Manager avec des services cloud ? 

Les services cloud ne peuvent pas être intégrés à un réseau virtuel Azure Resource Manager. En revanche, un réseau virtuel Azure Resource Manager et un réseau virtuel classique peuvent être connectés en les homologuant. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md).