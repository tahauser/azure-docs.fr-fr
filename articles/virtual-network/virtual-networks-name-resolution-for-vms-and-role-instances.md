---
title: "Résolution pour les machines virtuelles et les instances de rôle"
description: "Scénarios de résolution de noms pour Microsoft Azure IaaS, les solutions hybrides, entre différents services cloud, Active Directory et à l’aide de votre propre serveur DNS  "
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2016
ms.author: jdial
ms.openlocfilehash: 5a298f535308cff90ddd249594b7bb5e36909867
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="name-resolution-for-vms-and-role-instances"></a>Résolution de noms pour les machines virtuelles et les instances de rôle
Selon la manière dont vous utilisez Azure pour héberger les solutions IaaS, PaaS et les solutions hybrides, vous pouvez configurer les machines virtuelles et les instances de rôles créées pour qu’elles communiquent entre elles. Bien que cette communication puisse être mise en place par le biais d’adresses IP, il est bien plus simple d’utiliser des noms dont vous vous souviendrez facilement et qui ne seront pas modifiés. 

Lorsque des instances de rôle et des machines virtuelles hébergées dans Azure doivent résoudre des noms de domaine en adresses IP internes, elles peuvent utiliser l’une des deux méthodes suivantes :

* [Résolution de noms dans Azure](#azure-provided-name-resolution)
* [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) (qui peut rediriger les requêtes vers les serveurs DNS fournis par Azure) 

Le type de résolution de noms que vous utilisez dépend de la manière dont vos machines virtuelles et vos instances de rôle doivent communiquer entre elles.

**Le tableau suivant illustre des scénarios et des solutions correspondantes de résolution de noms :**

| **Scénario** | **Solution** | **Suffixe** |
| --- | --- | --- |
| Résolution de noms entre des instances de rôle ou des machines virtuelles situées dans le même service cloud ou réseau virtuel |[Résolution de noms dans Azure](#azure-provided-name-resolution) |Nom d’hôte ou nom de domaine complet |
| Résolution de noms à partir d’Azure App Service (application web, fonction, Bot, etc.) à l’aide de l’intégration au réseau virtuel pour les instances de rôle ou machines virtuelles situées dans le même réseau virtuel |Serveurs DNS gérés par le client qui redirigent les requêtes entre les réseaux virtuels en vue de la résolution par Azure (proxy DNS).  Consultez [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) |Nom de domaine complet uniquement |
| Résolution de noms entre des instances de rôles ou des machines virtuelles situées dans différents réseaux virtuels |Serveurs DNS gérés par le client qui redirigent les requêtes entre les réseaux virtuels en vue de la résolution par Azure (proxy DNS).  Consultez [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) |Nom de domaine complet uniquement |
| Résolution des noms de service et d’ordinateur locaux à partir des instances de rôle ou des machines virtuelles dans Azure |Serveurs DNS gérés par le client (par exemple, contrôleur de domaine local, contrôleur de domaine en lecture seule local ou serveur DNS secondaire synchronisé à l’aide de transferts de zone).  See [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) |Nom de domaine complet uniquement |
| Résolution de noms d’hôte Azure à partir d’ordinateurs locaux |Rediriger les requêtes vers un serveur proxy DNS géré par le client dans le réseau virtuel correspondant ; le serveur proxy redirige les requêtes vers Azure en vue de la résolution. See [Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) |Nom de domaine complet uniquement |
| DNS inversé pour les adresses IP internes |[Résolution de noms à l’aide de votre propre serveur DNS](#name-resolution-using-your-own-dns-server) |n/a |
| Résolution de noms entre des machines virtuelles ou instances de rôle situées dans différents services cloud, non dans un réseau virtuel |Non applicable. La connectivité entre des machines virtuelles et des instances de rôle de différents services cloud n’est pas prise en charge en dehors d’un réseau virtuel. |n/a |

## <a name="azure-provided-name-resolution"></a>Résolution de noms dans Azure
Avec la résolution des noms DNS publics, Azure fournit la résolution de noms interne pour les machines virtuelles et les instances de rôle qui résident dans le même réseau virtuel ou service cloud.  Les machines virtuelles/instances d’un service cloud partagent le même suffixe DNS (le nom d’hôte seul suffit donc), mais, dans les réseaux virtuels classiques, les différents services cloud ayant des suffixes DNS distincts, le nom de domaine complet est nécessaire pour résoudre les noms entre les différents services cloud.  Dans les réseaux virtuels du modèle de déploiement Resource Manager, le suffixe DNS est cohérent sur le réseau virtuel (le nom de domaine complet n’est donc pas nécessaire) et des noms DNS peuvent être affectés à la fois à des cartes réseau et à des machines virtuelles. Bien que la résolution de noms fournie par Azure ne nécessite aucune configuration, elle ne convient pas à l’ensemble des scénarios de déploiement, comme illustré dans le tableau précédent.

> [!NOTE]
> Avec les rôles Web et de travail, il est également possible d’accéder aux adresses IP internes des instances de rôle basées sur le nom de rôle et le numéro d’instance à l’aide de l’API REST de gestion des services Microsoft Azure. Pour plus d’informations, voir [Référence de l’API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### <a name="features-and-considerations"></a>Fonctionnalités et considérations
**Fonctionnalités :**

* Simplicité d’utilisation : aucune configuration n’est requise pour utiliser la résolution de noms dans Azure.
* Le service de résolution de noms dans Azure est hautement disponible, vous évitant d’avoir à créer et à gérer des clusters de vos propres serveurs DNS.
* Peut être utilisé conjointement avec vos propres serveurs DNS pour résoudre les noms d’hôte locaux et Azure.
* La résolution de noms est proposée entre les instances de rôle/machines virtuelles du même service cloud, sans qu’un nom de domaine complet soit nécessaire.
* Une résolution de noms est effectuée entre les machines virtuelles des réseaux virtuels qui utilisent le modèle de déploiement Resource Manager, sans qu’un nom de domaine complet soit nécessaire. Les réseaux virtuels dans le modèle de déploiement classique nécessitent le nom de domaine complet lors de la résolution de noms dans des services cloud différents. 
* Vous pouvez utiliser des noms d’hôtes qui décrivent vos déploiements de manière plus appropriée, plutôt que des noms générés automatiquement.

**Considérations :**

* Le suffixe DNS créé par Azure ne peut pas être modifié.
* Vous ne pouvez pas enregistrer manuellement vos propres enregistrements.
* WINS et NetBIOS ne sont pas pris en charge. (Vous ne pouvez pas voir vos machines virtuelles dans l’Explorateur Windows.)
* Les noms d’hôte doivent être compatibles DNS (ils doivent comporter uniquement les caractères 0-9, a-z et « - » et ne peuvent pas démarrer ou se terminer par un « - ». Voir la section 2 de RFC 3696.)
* Le trafic de requêtes DNS est limité pour chaque machine virtuelle. Cela ne devrait pas avoir d’incidence sur la plupart des applications.  Si la limitation de requêtes est respectée, assurez-vous que la mise en cache côté client est activée.  Pour plus d’informations, consultez [Tirer le meilleur parti de la résolution de noms dans Azure](#Getting-the-most-from-Azure-provided-name-resolution).
* Seules les machines virtuelles des 180 premiers services cloud sont inscrites pour chaque réseau virtuel dans un modèle de déploiement classique. Cela ne s’applique pas aux réseaux virtuels dans les modèles de déploiement Resource Manager.

### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Tirer le meilleur parti de la résolution de noms dans Azure
**Mise en cache côté client :**

Les requêtes DNS n’ont pas toutes besoin d’être envoyées sur le réseau.  La mise en cache côté client permet de réduire la latence et d’améliorer la résilience aux spots réseau en résolvant les requêtes DNS récurrentes à partir d’un cache local.  Les enregistrements DNS ont une durée de vie qui permet au cache de les stocker aussi longtemps que possible sans affecter leur fraîcheur ; la mise en cache côté client convient donc à la plupart des situations.

Le client DNS Windows par défaut possède un cache DNS intégré.  Certaines distributions Linux n’incluant pas la mise en cache par défaut, il est recommandé de l’ajouter à chaque machine virtuelle Linux (après avoir vérifié qu’il n’existe pas déjà un cache local).

Un certain nombre de packages de mise en cache DNS différents sont disponibles, par exemple dnsmasq. Les étapes suivantes montrent comment installer dnsmasq sur les distributions les plus courantes :

* **Ubuntu (utilise resolvconf)**:
  * installez simplement le package dnsmasq (« sudo apt-get install dnsmasq »).
* **SUSE (utilise netconf)**:
  * installez le package dnsmasq (« sudo zypper install dnsmasq ») 
  * activez le service dnsmasq (« systemctl enable dnsmasq.service ») 
  * démarrez le service dnsmasq (« systemctl start dnsmasq.service ») 
  * modifiez « /etc/sysconfig/network/config » et remplacez NETCONFIG_DNS_FORWARDER="" par « dnsmasq »
  * mettez à jour resolv.conf (netconfig update) pour définir le cache en tant que programme de résolution DNS local
* **OpenLogic (utilise NetworkManager)**:
  * installez le package dnsmasq (« sudo yum install dnsmasq »)
  * activez le service dnsmasq (« systemctl enable dnsmasq.service »)
  * démarrez le service dnsmasq (« systemctl start dnsmasq.service »)
  * ajoutez « prepend domain-name-servers 127.0.0.1; » à « /etc/dhclient-eth0.conf »
  * redémarrez le service réseau (« service network restart ») pour définir le cache en tant que programme de résolution DNS local

> [!NOTE]
> Le package ’dnsmasq’ constitue l’un des nombreux caches DNS disponibles pour Linux.  Avant de l’utiliser, vérifiez son adéquation à vos besoins et assurez-vous qu’aucun autre cache n’est installé.
> 
> 

**Nouvelles tentatives côté client :**

DNS est principalement un protocole UDP.  Comme le protocole UDP ne garantit pas la remise des messages, la logique de nouvelle tentative est gérée dans le protocole DNS lui-même.  Chaque client DNS (système d’exploitation) peut afficher une logique de nouvelle tentative différente selon la préférence de son auteur :

* Les systèmes d’exploitation Windows effectuent de nouvelles tentatives après 1 seconde, puis à nouveau après 2 secondes, 4 secondes et encore 4 secondes. 
* La configuration Linux par défaut effectue une nouvelle tentative après cinq secondes.  Il est recommandé de modifier ce paramètre pour effectuer de nouvelles tentatives 5 fois à des intervalles de 1 seconde.  

Utilisez la commande « cat /etc/resolv.conf » pour vérifier les paramètres actuels sur une machine virtuelle Linux, puis examinez la ligne « options », par exemple :

    options timeout:1 attempts:5

Le fichier resolv.conf est généralement généré automatiquement et ne doit pas être modifié.  Les étapes spécifiques pour l’ajout de la ligne 'options' varient selon la distribution :

* **Ubuntu** (utilise resolvconf) :
  * ajoutez la ligne 'options' à '/etc/resolveconf/resolv.conf.d/head' 
  * exécutez 'resolvconf -u' pour mettre à jour
* **SUSE** (utilise netconf) :
  * ajoutez 'timeout:1 attempts:5' au paramètre NETCONFIG_DNS_RESOLVER_OPTIONS="" dans '/etc/sysconfig/network/config' 
  * exécutez 'netconfig update' pour mettre à jour
* **OpenLogic** (utilise NetworkManager) :
  * ajoutez 'echo "options timeout:1 attempts:5"' à '/etc/NetworkManager/dispatcher.d/11-dhclient' 
  * exécutez 'service network restart' pour mettre à jour

## <a name="name-resolution-using-your-own-dns-server"></a>Résolution de noms à l’aide de votre propre serveur DNS
Dans certaines situations, les fonctionnalités fournies par Azure peuvent ne pas couvrir vos besoins en matière de résolution de noms, par exemple si vous utilisez des domaines Active Directory ou que vous devez recourir à une résolution DNS entre des réseaux virtuels.  Dans le cadre de ces scénarios, Azure vous permet d’utiliser vos propres serveurs DNS.  

Les serveurs DNS dans un réseau virtuel peuvent rediriger les requêtes DNS vers les programmes de résolution récursifs d’Azure en vue de la résolution des noms d’hôtes au sein de ce réseau virtuel.  Par exemple, un contrôleur de domaine en cours d’exécution dans Azure peut répondre aux requêtes DNS concernant ses domaines et rediriger toutes les autres requêtes vers Azure.  Ainsi, les machines virtuelles peuvent voir vos ressources locales (par le biais du contrôleur de domaine) et les noms d’hôtes fournis par Azure (par le biais du redirecteur).  Les programmes de résolution récursifs d’Azure sont accessibles via l’adresse IP virtuelle 168.63.129.16.

Le transfert DNS permet aussi la résolution DNS entre réseaux virtuels et permet à vos machines locales de résoudre les noms d’hôtes fournis par Azure.  Pour résoudre le nom d’hôte d’une machine virtuelle, la machine virtuelle du serveur DNS doit résider dans le même réseau virtuel et être configurée pour rediriger les requêtes de nom d’hôte vers Azure.  Comme le suffixe DNS est différent dans chaque réseau virtuel, vous pouvez utiliser des règles de redirection conditionnelles pour envoyer les requêtes DNS au réseau virtuel approprié en vue de la résolution.  L’image suivante montre deux réseaux virtuels et un réseau local effectuant une résolution DNS entre réseaux virtuels à l’aide de cette méthode.  Un exemple de redirecteur DNS est disponible dans la [Galerie de modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) et sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![DNS entre réseaux virtuels](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Quand vous utilisez la résolution de noms dans Azure, un suffixe DNS interne (*.internal.cloudapp.net) est fourni à chaque machine virtuelle à l’aide de DHCP.  Cela permet la résolution des noms d’hôte, car les enregistrements de noms d’hôte se trouvent dans la zone internal.cloudapp.net.  Quand vous utilisez votre propre solution de résolution de noms, le suffixe IDNS n’est pas fourni aux machines virtuelles, car il interfère avec d’autres architectures DNS (comme les scénarios avec jointure de domaine).  Au lieu de cela, nous fournissons un espace réservé non fonctionnel (reddog.microsoft.com).  

Si nécessaire, le suffixe DNS interne peut être déterminé à l’aide de PowerShell ou de l’API :

* Pour les réseaux virtuels dans les modèles de déploiement Resource Manager, le suffixe est disponible via la ressource [carte réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou l’applet de commande [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx).    
* Dans les modèles de déploiement Classic, le suffixe est disponible via l’appel de l’[API d’obtention de déploiement](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou l’applet de commande [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx).

Si la redirection des requêtes vers Azure ne suffit pas, vous devez fournir votre propre solution DNS.  Votre solution DNS doit présenter les caractéristiques suivantes :

* Fournir la résolution de nom d’hôte approprié, par exemple par le biais de [DDNS](virtual-networks-name-resolution-ddns.md).  Si vous utilisez DNS dynamique, vous pouvez être amené à désactiver le nettoyage des enregistrements DNS, car les baux DHCP d’Azure sont très longs et le nettoyage risque de supprimer des enregistrements DNS prématurément. 
* Fournir la résolution récursive appropriée pour permettre la résolution de noms de domaines externes.
* Être accessible (TCP et UDP sur le port 53) à partir des clients qu’elle dessert et être en mesure d’accéder à internet.
* Être protégée contre tout accès à partir d’Internet, pour atténuer les menaces posées par les agents externes.

> [!NOTE]
> Pour de meilleures performances, lors de l’utilisation de machines virtuelles Azure en tant que serveurs DNS, le protocole IPv6 doit être désactivé et une [adresse IP publique de niveau d’instance](virtual-networks-instance-level-public-ip.md) doit être affectée à chaque machine virtuelle du serveur DNS.  Si vous choisissez Windows Server comme serveur DNS, [cet article](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fournit une analyse des performances et des optimisations.
> 
> 

### <a name="specifying-dns-servers"></a>Spécification des serveurs DNS
Lorsque vous utilisez vos propres serveurs DNS, Azure offre la possibilité de spécifier plusieurs serveurs DNS par réseau virtuel ou par interface réseau (Resource Manager)/service cloud (classique).  Les serveurs DNS spécifiés pour une interface réseau/un service cloud ont la priorité sur ceux spécifiés pour le réseau virtuel.

> [!NOTE]
> Les propriétés de connexion réseau, telles que les adresses IP de serveur DNS, ne doivent pas être modifiées directement sur les machines virtuelles Windows, car elles peuvent être effacées durant une réparation du service lors du remplacement de la carte réseau virtuelle. 
> 
> 

Quand vous utilisez le modèle de déploiement Resource Manager, des serveurs DNS peuvent être spécifiés dans le portail, les API/modèles ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) ou PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Quand vous utilisez le modèle de déploiement Classic, des serveurs DNS pour le réseau virtuel peuvent être spécifiés dans le portail ou [le fichier *Configuration réseau*](https://msdn.microsoft.com/library/azure/jj157100).  Pour les services cloud, les serveurs DNS sont spécifiés via [le fichier *Configuration du service*](https://msdn.microsoft.com/library/azure/ee758710) ou dans PowerShell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [!NOTE]
> Si vous modifiez les paramètres DNS pour un réseau virtuel/une machine virtuelle déjà déployés, vous devez redémarrer l’ensemble des machines virtuelles concernées pour que les modifications prennent effet.
> 
> 

## <a name="next-steps"></a>étapes suivantes
Modèle de déploiement Resource Manager :

* [Création ou mise à jour d’un réseau virtuel](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Création ou mise à jour d’une carte d’interface réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

Modèle de déploiement classique :

* [Schéma de configuration de service Microsoft Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Schéma de configuration du réseau virtuel](https://msdn.microsoft.com/library/azure/jj157100)
* [Configuration d'un réseau virtuel à l'aide d'un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md) 

