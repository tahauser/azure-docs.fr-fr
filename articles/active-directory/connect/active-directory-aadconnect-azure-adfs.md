---
title: "Services de fédération Active Directory dans Azure | Microsoft Docs"
description: "Dans ce document, vous allez apprendre à déployer AD FS dans Azure pour bénéficier d’une haute disponibilité."
keywords: "déployer AD FS dans azure, déployer azure adfs, azure adfs, azure ad fs, déployer adfs, déployer ad fs, adfs dans azure, déployer adfs dans azure, déployer AD FS dans azure, adfs azure, présentation d’AD FS, Azure, AD FS dans Azure, iaas, ADFS, déplacer adfs vers azure"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: mtillman
editor: 
ms.assetid: 692a188c-badc-44aa-ba86-71c0e8074510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba14244a3f5786ebcd667aa090d0245ce45f741d
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="deploying-active-directory-federation-services-in-azure"></a>Déploiement d’Active Directory Federation Services dans Azure
AD FS simplifie et sécurise la fédération des identités et l’authentification unique (SSO) sur le web. La fédération avec AD Azure ou O365 permet aux utilisateurs de s’authentifier à l’aide de leurs informations d’identification locales et d’accéder à toutes les ressources du cloud. Par conséquent, il est important de disposer d’une infrastructure AD FS hautement disponible pour garantir l’accès aux ressources locales et dans le cloud. Le déploiement d’AD FS dans Azure peut contribuer à bénéficier d’une haute disponibilité avec un minimum d’efforts.
Le déploiement d’AD FS dans Azure présente toute une série d’avantages, notamment :

* **Haute disponibilité** : la puissance des groupes à haute disponibilité Azure vous garantit une infrastructure hautement disponible.
* **Simplicité de mise à l’échelle** : besoin de meilleures performances ? Migrez facilement vers des ordinateurs plus puissants en seulement quelques clics dans Azure
* **Géo-redondance inter-région** : la redondance géographique Azure garantit une haute disponibilité de votre infrastructure dans le monde entier
* **Facilité de gestion** : le portail Azure propose des options de gestion extrêmement simplifiées, conçues pour faciliter et automatiser la gestion de votre infrastructure 

## <a name="design-principles"></a>Principes de conception
![Conception du déploiement](./media/active-directory-aadconnect-azure-adfs/deployment.png)

Le schéma ci-dessus présente la topologie de base recommandée pour amorcer le déploiement de votre infrastructure AD FS dans Azure. Vous trouverez ci-dessous les principes qui sous-tendent les différents composants de cette topologie :

* **Contrôleur de domaine / serveurs ADFS**: si votre environnement compte moins de 1 000 utilisateurs, vous pouvez installer simplement le rôle AD FS sur vos contrôleurs de domaine. Si vous ne souhaitez aucun impact sur les performances de vos contrôleurs de domaine ou si vous gérez plus de 1 000 utilisateurs, vous pouvez déployer AD FS sur des serveurs distincts.
* **Serveur WAP** : il est nécessaire de déployer des serveurs Web Application Proxy afin que les utilisateurs puissent également accéder à AD FS lorsqu’ils se trouvent en dehors du réseau d’entreprise.
* **DMZ**: les serveurs Web Application Proxy seront placés dans la zone DMZ et SEUL un accès TCP/443 sera autorisé entre la zone DMZ et le sous-réseau interne.
* **Équilibreurs de charge**: pour garantir une haute disponibilité du serveur AD FS et des serveurs Web Application Proxy, nous vous recommandons d’utiliser un équilibreur de charge interne pour les serveurs AD FS et de préférer l’utilisation d’Azure Load Balancer pour les serveurs Web Application Proxy.
* **Groupes à haute disponibilité**: pour apporter une redondance à votre déploiement AD FS, nous vous recommandons de regrouper plusieurs machines virtuelles dans un groupe à haute disponibilité pour des charges de travail similaires. Dans une telle configuration, vous avez la garantie qu’au moins une des machines virtuelles sera disponible pendant un événement de maintenance planifié ou non planifié.
* **Comptes de stockage**: il est recommandé de disposer de deux comptes de stockage. L’utilisation d’un seul compte de stockage peut entraîner la création d’un point de défaillance unique et provoquer une indisponibilité du déploiement dans l’éventualité (peu probable) où le compte de stockage serait inutilisable. Le choix de deux comptes de stockage permet d’associer un compte de stockage pour chaque ligne d’erreur.
* **Séparation des réseaux** : les serveurs Web Application Proxy doivent être déployés sur un réseau DMZ distinct. Vous pouvez diviser un réseau virtuel en deux sous-réseaux, puis déployer le ou les serveurs Web Application Proxy dans un sous-réseau isolé. Vous pouvez simplement configurer les paramètres du groupe de sécurité réseau pour chaque sous-réseau et autoriser uniquement les communications requises entre les deux sous-réseaux. Voir les détails ci-dessous en fonction du scénario de déploiement

## <a name="steps-to-deploy-ad-fs-in-azure"></a>Procédure de déploiement d’AD FS dans Azure
Les étapes indiquées dans cette section expliquent comment déployer dans Azure l’infrastructure AD FS représentée ci-dessous.

### <a name="1-deploying-the-network"></a>1. Déployer le réseau
Comme indiqué ci-dessus, vous pouvez soit créer deux sous-réseaux dans un même réseau virtuel, soit créer deux réseaux virtuels totalement différents. Cet article se concentre sur le déploiement d’un seul réseau virtuel subdivisé en deux sous-réseaux. Cette approche est actuellement plus abordable dans la mesure où l’utilisation de deux réseaux virtuels distincts nécessiterait une passerelle de réseau virtuel à réseau virtuel pour l’établissement des communications.

**1.1 Création d’un réseau virtuel**

![Création d’un réseau virtuel](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)

Dans le portail Azure, sélectionnez le réseau virtuel de votre choix. D’un simple clic, vous pouvez dès lors déployer immédiatement le réseau virtuel et un sous-réseau. Un sous-réseau INT est également défini et prêt à recevoir des machines virtuelles.
L’étape suivante consiste à ajouter un autre sous-réseau au réseau, c’est-à-dire le sous-réseau DMZ. Pour créer le sous-réseau DMZ, procédez simplement comme suit :

* Sélectionnez le réseau que vous venez de créer
* Dans les propriétés, sélectionnez le sous-réseau
* Dans le panneau Sous-réseau, cliquez sur le bouton Ajouter
* Indiquez le nom du sous-réseau et les informations relatives à l’espace d’adresse pour créer le sous-réseau

![Sous-réseau](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)

![DMZ de sous-réseau](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. Création des groupes de sécurité réseau**

Un groupe de sécurité réseau (NSG) contient une liste des règles de liste de contrôle d’accès (ACL) qui autorise ou rejette les instances de machine virtuelle dans un réseau virtuel. Des groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des instances de machine virtuelle au sein de ce sous-réseau. Lorsqu’un groupe de sécurité réseau est associé à un sous-réseau, les règles ACL s’appliquent à toutes les instances de machine virtuelle présentes dans ce sous-réseau.
Dans cet article, nous allons créer deux groupes de sécurité réseau : un pour un réseau interne, l’autre pour la zone DMZ. Ces groupes seront respectivement nommés NSG_INT et NSG_DMZ.

![Création d’un groupe de sécurité réseau](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Une fois le groupe de sécurité réseau créé, il n’y a aucune règle de trafic entrant ni aucune règle de trafic sortant. Une fois que les rôles sont installés et opérationnels sur les serveurs concernés, les règles de trafics entrant et sortant peuvent être modulées selon le niveau de sécurité souhaité.

![Initialisation d’un groupe de sécurité réseau](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Une fois les groupes de sécurité réseau créés, associez NSG_INT au sous-réseau INT et NSG_DMZ à la zone DMZ du sous-réseau. Exemple de capture d’écran :

![Configuration d’un groupe de sécurité réseau](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Cliquez sur Sous-réseaux pour ouvrir le panneau correspondant
* Sélectionnez le sous-réseau à associer au groupe de sécurité réseau 

Après la configuration, le panneau Sous-réseaux doit se présenter comme suit :

![Sous-réseaux après un groupe de sécurité réseau](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. Création d’une connexion en local**

Nous avons besoin d’une connexion en local afin de déployer le contrôleur de domaine (DC) dans Azure. Azure propose différentes options de connectivité permettant de connecter votre infrastructure locale à votre infrastructure Azure.

* Point à site
* Réseau virtuel de site à site
* ExpressRoute

Nous vous recommandons d’utiliser ExpressRoute. ExpressRoute vous permet de créer des connexions privées entre les centres de données Azure et une infrastructure locale ou dans un environnement de colocalisation. Les connexions ExpressRoute ne sont pas établies par le biais de l'Internet public. Elles offrent davantage de fiabilité, des vitesses supérieures, des latences inférieures et une sécurité renforcée par rapport aux connexions classiques sur Internet.
Bien qu’il soit recommandé d’utiliser ExpressRoute, vous pouvez choisir n’importe quelle méthode de connexion qui vous semble la plus adaptée à votre organisation. Pour en savoir plus sur ExpressRoute et sur les différentes options de connectivité basées sur ExpressRoute, consultez l’article [Présentation technique d’ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Créer des comptes de stockage
Pour maintenir une haute disponibilité et éviter toute dépendance à un seul compte de stockage, vous pouvez créer deux comptes de stockage. Répartissez en deux groupes les machines virtuelles de chaque groupe à haute disponibilité, puis affectez chaque groupe à un compte de stockage distinct.

![Créer des comptes de stockage](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. Créer des groupes à haute disponibilité
Pour chaque rôle (contrôleur de domaine/AD FS et WAP), créez des groupes à haute disponibilité qui contiendront au minimum deux machines virtuelles, ce afin de garantir une meilleure disponibilité pour chaque rôle. Lorsque vous créez des groupes à haute disponibilité, vous devez impérativement étudier les aspects suivants :

* **Domaines d’erreur**: les machines virtuelles du même domaine d’erreur partagent la même source d’alimentation et le même commutateur réseau physique. Il est recommandé d’utiliser au moins 2 domaines d’erreur. Vous pouvez utiliser la valeur par défaut (3) pour les besoins de ce déploiement
* **Domaines de mise à jour**: les machines attachées au même domaine de mise à jour seront redémarrées simultanément pendant une mise à jour. Vous devez disposer d’au moins 2 domaines de mise à jour. Vous pouvez utiliser la valeur par défaut (5) pour les besoins de ce déploiement

![Groupes à haute disponibilité](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Créez les groupes à haute disponibilité suivants :

| Groupe à haute disponibilité | Rôle | Domaines d’erreur | Domaines de mise à jour |
|:---:|:---:|:---:|:--- |
| contosodcset |Contrôleur de domaine/AD FS |3 |5. |
| contosowapset |WAP |3 |5. |

### <a name="4-deploy-virtual-machines"></a>4. Déployer les machines virtuelles
L’étape suivante consiste à déployer les machines virtuelles qui hébergeront les différents rôles de votre infrastructure. Nous vous recommandons d’affecter au moins deux machines virtuelles à chaque groupe à haute disponibilité. Créez quatre machines virtuelles dans le cadre du déploiement de base.

| Ordinateur | Rôle | Sous-réseau | Groupe à haute disponibilité | Compte de stockage | Adresse IP |
|:---:|:---:|:---:|:---:|:---:|:---:|
| contosodc1 |Contrôleur de domaine/AD FS |INT |contosodcset |contososac1 |statique |
| contosodc2 |Contrôleur de domaine/AD FS |INT |contosodcset |contososac2 |statique |
| contosowap1 |WAP |DMZ |contosowapset |contososac1 |statique |
| contosowap2 |WAP |DMZ |contosowapset |contososac2 |statique |

Comme vous l’avez peut-être remarqué, aucun groupe de sécurité réseau n’a été spécifié, car Azure vous autorise à utiliser un groupe de sécurité réseau au niveau du sous-réseau. Vous pouvez dès lors contrôler le trafic réseau de la machine à l’aide du groupe de sécurité réseau précisément associé au sous-réseau ou à l’objet de carte réseau. Pour en savoir plus, consultez l’article [Présentation du groupe de sécurité réseau](https://aka.ms/Azure/NSG).
Nous vous recommandons d’utiliser une adresse IP statique si vous gérez le serveur DNS. Vous pouvez aussi utiliser Azure DNS et, au lieu des enregistrements DNS de votre domaine, référencer les nouvelles machines virtuelles par leurs noms de domaine complets Azure.
Une fois le déploiement terminé, le volet de votre machine virtuelle devrait ressembler à ce qui suit :

![Machines virtuelles déployées](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. Configurer le contrôleur de domaine / les serveurs AD FS
 Afin d’authentifier les demandes entrantes, AD FS doit pouvoir contacter le contrôleur de domaine. Pour éviter un transfert coûteux entre Azure et le contrôleur de domaine local dans le cadre de l’authentification, il est recommandé de déployer un réplica du contrôleur de domaine dans Azure. Pour bénéficier d’une haute disponibilité, il est recommandé de créer un groupe à haute disponibilité comprenant au moins 2 contrôleurs de domaine.

| Contrôleur de domaine | Rôle | Compte de stockage |
|:---:|:---:|:---:|
| contosodc1 |Réplica |contososac1 |
| contosodc2 |Réplica |contososac2 |

* Déployez les deux serveurs en tant que réplicas de contrôleurs de domaine avec DNS
* Configurez les serveurs AD FS en installant le rôle AD FS à l’aide du gestionnaire de serveurs.

### <a name="6-deploying-internal-load-balancer-ilb"></a>6. Déployer l’équilibreur de charge interne (ILB)
**6.1. Création de l’équilibreur de charge interne**

Pour déployer un équilibreur de charge interne, sélectionnez Équilibreurs de charge dans le portail Azure, puis cliquez sur Ajouter (+).

> [!NOTE]
> Si vous ne voyez pas l’option de menu **Équilibreurs de charge**, cliquez sur **Parcourir** en bas à gauche du portail, puis faites défiler jusqu’à **Équilibreurs de charge**.  Cliquez ensuite sur l’étoile jaune pour ajouter l’option à votre menu. Sélectionnez maintenant la nouvelle icône d’équilibreur de charge pour ouvrir le panneau qui vous permettra de configurer l’équilibreur de charge.
> 
> 

![Rechercher un équilibreur de charge](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nom**: attribuez un nom approprié à l’équilibreur de charge
* **Schéma**: dans la mesure où cet équilibreur de charge sera placé devant les serveurs AD FS et où il a été conçu EXCLUSIVEMENT pour des connexions réseau internes, vous devez sélectionner l’option « Interne »
* **Réseau virtuel**: choisissez le réseau virtuel dans lequel vous allez déployer vos services AD FS
* **Sous-réseau**: sélectionnez ici votre sous-réseau interne
* **Affectation d’adresses IP** : Statique

![Équilibreur de charge interne](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)

Cliquez sur Créer pour déployer l’équilibreur de charge interne ; celui-ci doit normalement apparaître dans la liste des équilibreurs de charge :

![Équilibreurs de charge après équilibreur de charge interne](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)

L’étape suivante consiste à configurer le pool principal et la sonde principale.

**6.2. Configuration du pool principal de l’équilibreur de charge interne**

Dans le panneau Équilibreurs de charge, sélectionnez l’équilibreur de charge interne que vous venez de créer. Vous accédez au panneau Paramètres. 

1. Sélectionnez les pools principaux à partir du panneau Paramètres
2. Dans le panneau Ajouter un pool principal, cliquez sur Ajouter une machine virtuelle
3. Dans le panneau qui s’affiche, vous pouvez choisir votre groupe à haute disponibilité
4. Choisissez le groupe à haute disponibilité AD FS

![Configuration du pool principal de l’équilibreur de charge interne](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)

**6.3. Configuration de la sonde**

Dans le panneau Équilibreurs de charge internes, sélectionnez Sondes.

1. Cliquez sur Ajouter
2. Indiquez les détails de la sonde a. **Nom** : nom de la sonde b. **Protocole** : TCP c. **Port** : 443 (HTTPS) d. **Intervalle** : 5 (valeur par défaut) : il s’agit de l’intervalle auquel l’équilibreur de charge interne interrogera les machines virtuelles du pool principal e. **Unhealthy threshold limit**(Seuil de défaillance d’intégrité) : 2 (valeur par défaut) : il s’agit du seuil limite de défaillances consécutives de la sonde au-delà duquel l’équilibreur de charge interne considérera une machine du pool principal comme non réactive et cessera de lui envoyer du trafic.

![Configuration de la sonde d’équilibreur de charge interne](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)

**6.4. Création des règles d’équilibrage de charge**

Pour équilibrer au mieux le trafic, l’équilibreur de charge interne doit être configuré avec des règles d’équilibrage de charge. Pour créer une règle d’équilibrage de charge, procédez comme suit : 

1. Dans le panneau Paramètres de l’équilibreur de charge interne, sélectionnez Règle d’équilibrage de charge
2. Cliquez sur Ajouter dans le panneau Règle d’équilibrage de charge
3. Dans le panneau Ajouter une règle d’équilibrage de charge, renseignez les champs suivants : a. **Nom** : indiquez le nom de la règle b. **Protocole** : sélectionnez TCP c. **Port** : 443 d. **Port principal** : 443 e. **Pool principal** : sélectionnez le pool que vous avez précédemment créé pour le cluster AD FS f. **Sonde**: sélectionnez la sonde que vous avez précédemment créée pour les serveurs AD FS

![Configuration des règles d’équilibrage de l’équilibreur de charge interne](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. Mise à jour du serveur DNS avec l’équilibreur de charge interne**

Accédez à votre serveur DNS et créez un enregistrement CNAME pour l’équilibreur de charge interne. L’enregistrement CNAME doit correspondre au service de fédération avec une adresse IP pointant vers l’adresse IP de l’équilibreur de charge interne. Par exemple, si l’adresse IP de l’équilibreur de charge interne est 10.3.0.8 et si le service de fédération installé est fs.contoso.com, créez un enregistrement CNAME pour fs.contoso.com pointant vers 10.3.0.8.
De cette manière, toutes les communications concernant fs.contoso.com pointeront vers l’équilibreur de charge interne et seront routées de façon appropriée.

### <a name="7-configuring-the-web-application-proxy-server"></a>7. Configurer le serveur Web Application Proxy
**7.1. Configuration des serveurs Web Application Proxy pour atteindre les serveurs AD FS**

Pour faire en sorte que les serveurs Web Application Proxy soient en mesure d’atteindre les serveurs AD FS situés derrière l’équilibreur de charge interne, créez un enregistrement dans le répertoire %systemroot%\system32\drivers\etc\hosts de l’équilibreur de charge interne. Notez que le nom unique (DN) doit être le nom du service de fédération (par exemple fs.contoso.com). L’entrée IP doit être celle de l’adresse IP de l’équilibreur de charge interne (10.3.0.8, dans notre exemple).

**7.2. Installation du rôle Web Application Proxy**

Après avoir vérifié que les serveurs Web Application Proxy sont bien en mesure d’atteindre les serveurs AD FS situés derrière l’équilibreur de charge interne, vous pouvez installer les serveurs Web Application Proxy. Les serveurs Web Application Proxy ne doivent pas être joints au domaine. Installez les rôles Web Application Proxy sur les deux serveurs Web Application Proxy en sélectionnant le rôle Accès à distance. Le gestionnaire de serveurs vous guide dans l’installation du serveur WAP.
Pour plus d’informations sur le déploiement de WAP, consultez la page [Installer et configurer le serveur proxy d’application web](https://technet.microsoft.com/library/dn383662.aspx).

### <a name="8--deploying-the-internet-facing-public-load-balancer"></a>8.  Déployer l’équilibreur de charge (public) accessible sur Internet
**8.1.  Création d’un équilibreur de charge (public) accessible sur Internet**

Dans le portail Azure, sélectionnez Équilibreurs de charge, puis cliquez sur Ajouter. Dans le panneau Créer un équilibreur de charge, entrez les informations suivantes :

1. **Nom**: nom de l’équilibreur de charge
2. **Schéma**: Public ; cette option indique à Azure que cet équilibreur de charge aura besoin d’une adresse publique.
3. **Adresse IP**: créez une nouvelle adresse IP (dynamique)

![Équilibreur de charge accessible sur Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Après le déploiement, l’équilibreur de charge s’affiche dans la liste des équilibreurs de charge.

![Liste des équilibreurs de charge](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)

**8.2. Attribution d’un nom DNS à l’adresse IP publique**

Dans le panneau Équilibreurs de charge, cliquez sur la nouvelle entrée d’équilibreur de charge pour afficher le panneau de configuration. Suivez les étapes ci-dessous pour configurer le nom DNS pour l’adresse IP publique :

1. Cliquez sur l’adresse IP publique. Vous accédez au panneau correspondant à l’adresse IP publique et à ses paramètres
2. Cliquez sur Configuration
3. Indiquez un nom DNS. Ce nom deviendra ensuite le nom DNS public auquel vous pouvez accéder depuis n’importe quel emplacement, par exemple contosofs.westus.cloudapp.azure.com. Vous pouvez ajouter une entrée dans le DNS externe pour le service de fédération (par exemple, fs.contoso.com) qui résout le nom DNS de l’équilibreur de charge externe (contosofs.westus.cloudapp.azure.com).

![Configuration de l’équilibreur de charge accessible sur Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configuration de l’équilibreur de charge accessible sur Internet (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. Configuration d’un pool principal pour l’équilibreur de charge (public) accessible sur Internet** 

Suivez les mêmes étapes que pour la création de l’équilibreur de charge interne afin de configurer le pool principal de l’équilibreur de charge (public) accessible sur Internet en tant que groupe à haute disponibilité pour les serveurs WAP. Par exemple, contosowapset.

![Configuration d’un pool principal pour l’équilibreur de charge accessible sur Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)

**8.4. Configuration de la sonde**

Suivez les mêmes étapes que pour la configuration de l’équilibreur de charge interne afin de configurer la sonde pour le pool principal de serveurs WAP.

![Configuration de la sonde d’équilibreur de charge accessible sur Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)

**8.5. Création d’une ou plusieurs règles d’équilibrage de charge**

Suivez les mêmes étapes que pour l’équilibreur de charge interne afin de configurer la règle d’équilibrage de charge pour le port TCP 443.

![Configuration des règles d’équilibrage de l’équilibreur de charge accessible sur Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)

### <a name="9-securing-the-network"></a>9. Sécuriser le réseau
**9.1. Sécurisation du sous-réseau interne**

En général, vous devez appliquer les règles suivantes pour sécuriser efficacement votre sous-réseau interne (dans l’ordre indiqué ci-dessous)

| Règle | Description | Flux |
|:--- |:--- |:---:|
| AllowHTTPSFromDMZ |Autoriser la communication HTTPS à partir de la zone DMZ |Trafic entrant |
| DenyInternetOutbound |Aucun accès à Internet |Règle de trafic sortant |

![Règles d’accès INT (entrant)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

<!--
[comment]: <> (![INT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png))
[comment]: <> (![INT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
-->

**9.2. Sécurisation du sous-réseau DMZ**

| Règle | Description | Flux |
|:--- |:--- |:---:|
| AllowHTTPSFromInternet |Autoriser le trafic HTTPS entre Internet et la zone DMZ |Trafic entrant |
| DenyInternetOutbound |Tout trafic est bloqué, à l’exception du trafic HTTPS vers Internet |Règle de trafic sortant |

![Règles d’accès EXT (entrant)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

<!--
[comment]: <> (![EXT access rules (inbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png))
[comment]: <> (![EXT access rules (outbound)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))
-->

> [!NOTE]
> Si l’authentification du certificat utilisateur client (authentification clientTLS à l’aide de certificats utilisateur X509) est requise, AD FS nécessite l’activation du port TCP 49443 pour l’accès entrant.
> 
> 

### <a name="10-test-the-ad-fs-sign-in"></a>10. Tester l’authentification dans AD FS
Le moyen le plus simple consiste à tester AD FS à l’aide de la page IdpInitiatedSignon.aspx. Pour cela, vous devez activer l’authentification IdpInitiatedSignOn sur les propriétés AD FS. Suivez les étapes ci-dessous pour vérifier votre configuration AD FS

1. À l’aide de PowerShell, exécutez l’applet de commande ci-dessous sur le serveur AD FS pour l’activer.
   Set-AdfsProperties -EnableIdPInitiatedSignonPage $true 
2. À partir d’une machine externe, accédez à https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3. Vous devriez accéder à la page AD FS ci-dessous :

![Page de connexion de test](./media/active-directory-aadconnect-azure-adfs/test1.png)

Si l’authentification aboutit, vous obtenez le message de confirmation ci-dessous :

![Réussite du test](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Modèle de déploiement d’AD FS dans Azure
Le modèle déploie une installation à 6 machines, 2 par contrôleur de domaine, AD FS et WAP.

[Modèle de déploiement d’AD FS dans Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Vous pouvez utiliser un réseau virtuel existant ou créer un nouveau réseau virtuel lors du déploiement de ce modèle. Les différents paramètres disponibles pour la personnalisation du déploiement sont répertoriés ci-dessous avec la description de l’utilisation du paramètre dans le processus de déploiement. 

| Paramètre | Description |
|:--- |:--- |
| Lieu |Région dans laquelle déployer les ressources, par exemple, États-Unis de l'Est. |
| StorageAccountType |Type de compte de stockage créé |
| VirtualNetworkUsage |Indique si un réseau virtuel sera créé ou si un compte existant est utilisé |
| VirtualNetworkName |Nom du réseau virtuel à créer, obligatoire lors de l’utilisation du réseau virtuel nouveau ou existant |
| VirtualNetworkResourceGroupName |Spécifie le nom du groupe de ressources dans lequel réside le réseau virtuel existant. Lorsque vous utilisez un réseau virtuel existant, ce paramètre est obligatoire pour que le déploiement puisse trouver l’ID de réseau virtuel existant |
| VirtualNetworkAddressRange |Plage d’adresses du nouveau réseau virtuel, obligatoire si vous créez un nouveau réseau virtuel |
| InternalSubnetName |Nom du sous-réseau interne, obligatoire dans les deux options d’utilisation de réseau virtuel (nouveau ou existant) |
| InternalSubnetAddressRange |Plage d’adresses du sous-réseau interne, qui contient les contrôleurs de domaine et les serveurs ADFS, obligatoire si vous créez un nouveau réseau virtuel. |
| DMZSubnetAddressRange |Plage d’adresses du sous-réseau dmz, qui contient les serveurs proxy d’application Windows, obligatoire si vous créez un nouveau réseau virtuel. |
| DMZSubnetName |Nom du sous-réseau interne, obligatoire dans les deux options d’utilisation de réseau virtuel (nouveau ou existant). |
| ADDC01NICIPAddress |Adresse IP interne du premier contrôleur de domaine, cette adresse IP est affectée de manière statique au contrôleur de domaine et doit être une adresse IP valide au sein du sous-réseau interne |
| ADDC02NICIPAddress |Adresse IP interne du second contrôleur de domaine, cette adresse IP est affectée de manière statique au contrôleur de domaine et doit être une adresse IP valide au sein du sous-réseau interne |
| ADFS01NICIPAddress |Adresse IP interne du premier serveur ADFS, cette adresse IP est affectée de manière statique au serveur ADFS et doit être une adresse IP valide au sein du sous-réseau interne |
| ADFS02NICIPAddress |Adresse IP interne du second serveur ADFS, cette adresse IP est affectée de manière statique au serveur ADFS et doit être une adresse IP valide au sein du sous-réseau interne |
| WAP01NICIPAddress |Adresse IP interne du premier serveur WAP, cette adresse IP est affectée de manière statique au serveur WAP et doit être une adresse IP valide au sein du sous-réseau DMZ |
| WAP02NICIPAddress |Adresse IP interne du second serveur WAP, cette adresse IP est affectée de manière statique au serveur WAP et doit être une adresse IP valide au sein du sous-réseau DMZ |
| ADFSLoadBalancerPrivateIPAddress |Adresse IP interne de l’équilibrage de charge ADFS, cette adresse IP est affectée de manière statique à l’équilibrage de charge et doit être une adresse IP valide au sein du sous-réseau interne |
| ADDCVMNamePrefix |Préfixe du nom de machine virtuelle pour les contrôleurs de domaine |
| ADFSVMNamePrefix |Préfixe du nom de machine virtuelle pour les serveurs ADFS |
| WAPVMNamePrefix |Préfixe du nom de machine virtuelle pour les serveurs WAP |
| ADDCVMSize |Taille de machine virtuelle des contrôleurs de domaine |
| ADFSVMSize |Taille de machine virtuelle des serveurs ADFS |
| WAPVMSize |Taille de machine virtuelle des serveurs WAP |
| AdminUserName |Nom de l’administrateur local des machines virtuelles |
| AdminPassword |Mot de passe du compte administrateur local des machines virtuelles |

## <a name="additional-resources"></a>Ressources supplémentaires
* [Groupes à haute disponibilité](https://aka.ms/Azure/Availability) 
* [Équilibrage de charge Azure](https://aka.ms/Azure/ILB)
* [Équilibreur de charge interne](https://aka.ms/Azure/ILB/Internal)
* [Équilibreur de charge accessible sur Internet](https://aka.ms/Azure/ILB/Internet)
* [Comptes de stockage](https://aka.ms/Azure/Storage)
* [Réseaux virtuels Azure](https://aka.ms/Azure/VNet)
* [Liens AD FS et Web Application Proxy](https://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>étapes suivantes
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)
* [Configuration et gestion de vos services AD FS avec Azure AD Connect](active-directory-aadconnectfed-whatis.md)
* [Déploiement des services AD FS haute disponibilité par-delà les frontières dans Azure avec Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)

