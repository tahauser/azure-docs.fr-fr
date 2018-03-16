---
title: "Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Azure | Microsoft Docs"
description: "Si vous savez comment déployer des services de domaine AD et des services de fédération AD localement, découvrez comment ils fonctionnent sur des machines virtuelles Azure."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
ms.assetid: 04df4c46-e6b6-4754-960a-57b823d617fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: femila
ms.openlocfilehash: 7a56876dfa545d273807444b105de3645dd79d34
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure
Cet article présente les principales différences d’un déploiement en local des services de domaine Windows Server Active Directory (AD DS) et des services de fédération Active Directory (AD FS) par rapport à un déploiement des mêmes outils sur des machines virtuelles Microsoft Azure.

## <a name="scope-and-audience"></a>Portée et public ciblé
Cet article a été rédigé à l’attention des utilisateurs ayant déjà une expérience du déploiement d’Active Directory en local. Il explique les différences entre un déploiement d’Active Directory sur des machines virtuelles Microsoft Azure ou sur des réseaux virtuels Azure et les déploiements traditionnels d’Active Directory en local. Les machines virtuelles Azure et les réseaux virtuels Azure s’inscrivent dans le cadre d’une offre IaaS (Infrastructure-as-a-Service) qui permet aux organisations de tirer parti des ressources informatiques hébergées dans le cloud.

Si vous n’avez aucune expérience du déploiement d’Active Directory, consultez le document [AD DS Deployment Guide](https://technet.microsoft.com/library/cc753963) (Guide de déploiement d’AD DS) ou l’article [Planifier votre déploiement AD FS](https://technet.microsoft.com/library/dn151324.aspx).

Pour suivre cet article, le lecteur doit être familiarisé avec les concepts suivants :

* Déploiement et gestion de Windows Server AD DS
* Déploiement et configuration de DNS pour la prise en charge d’une infrastructure Windows Server AD DS
* Déploiement et gestion de Windows Server AD FS
* Déploiement, configuration et gestion des applications par partie de confiance (sites et services web) qui consomment des jetons Windows Server AD FS
* Concepts généraux d’une machine virtuelle, notamment la procédure de configuration d’une machine virtuelle, de disques virtuels et de réseaux virtuels

Ce document présente les conditions requises pour un scénario de déploiement hybride dans lequel les services Windows Server AD FS ou AD DS sont déployés en partie localement et en partie sur des machines virtuelles Azure. Il examine d’abord les différences essentielles entre l’exécution de Windows Server AD DS et AD FS sur des machines virtuelles Azure et l’exécution des mêmes services en local, et décrit les choix importants susceptibles d’affecter la conception et le déploiement. Le reste du document décrit plus en détail les instructions qu’il convient de suivre pour chaque décision à prendre et explique comment appliquer les instructions à différents scénarios de déploiement.

Cet article n’aborde pas la configuration d’ [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), un service REST qui offre des fonctionnalités de contrôle d’accès et de gestion des identités pour les applications cloud. Azure Active Directory (Azure AD) et Windows Server AD DS sont toutefois conçus pour fonctionner conjointement afin de fournir une solution de gestion des identités et des accès pour les environnements informatiques hybrides et les applications modernes. Afin de mieux comprendre les différences et les relations entre Windows Server AD DS et Azure AD, tenez compte des remarques suivantes :

1. Vous pouvez exécuter Windows Server AD DS dans le cloud sur des machines virtuelles lorsque vous utilisez Azure pour étendre votre centre de données local dans le cloud.
2. Vous pouvez utiliser Azure AD pour fournir à vos utilisateurs une authentification unique aux applications SaaS (Software-as-a-Service). Microsoft Office 365, par exemple, fait appel à cette technologie, et les applications exécutées sous Azure ou d’autres plateformes cloud peuvent également y recourir.
3. Vous pouvez utiliser Azure AD (et son service de contrôle d’accès) pour permettre aux utilisateurs de se connecter en utilisant des identités Facebook, Google, Microsoft et d’autres fournisseurs d’identité dans les applications hébergées sur le cloud ou en local.

Pour plus d’informations sur ces différences, consultez l’article [Principes de base de la gestion des identités Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Ressources associées
Vous pouvez télécharger et exécuter l’outil [Azure Virtual Machine Readiness Assessment](https://www.microsoft.com/download/details.aspx?id=40898). Cette évaluation inspecte automatiquement votre environnement local et génère un rapport personnalisé tenant compte des conseils décrits dans cette rubrique afin de vous aider à migrer votre environnement vers Azure.

Nous vous recommandons également de consulter les didacticiels, guides et vidéos sur les thèmes suivants :

* [Configuration d’un réseau virtuel sur le cloud uniquement dans le portail Azure](../virtual-network/quick-create-portal.md)
* [Configuration d’un VPN de site à site dans le portail Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md)
* [Installation d’un contrôleur de domaine Active Directory de réplication dans un réseau virtuel Azure](active-directory-install-replica-active-directory-domain-controller.md)
* [Iaas des professionnels de l’informatique Microsoft Azure : principes de base des machines virtuelles (01)](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Iaas des professionnels de l’informatique Microsoft Azure :(05) Création de réseaux virtuels pour la connectivité entre différents locaux](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introduction
Les exigences fondamentales de déploiement de Windows Server Active Directory sur des machines virtuelles Azure diffèrent peu d’un déploiement sur des machines virtuelles (et, dans une certaine mesure, sur des ordinateurs physiques) en local. Par exemple, dans le cas de Windows Server AD DS, si les contrôleurs de domaine déployés sur des machines virtuelles Azure sont des réplicas d’un domaine et/ou d’une forêt d’entreprise existants en local, le déploiement d’Azure peut alors être abordé de la même façon que n’importe quel autre site Windows Server Active Directory. Autrement dit, vous devez définir des sous-réseaux dans Windows Server AD DS, créer un site, lier les sous-réseaux à ce site et les connecter à d’autres sites via les liaisons de sites appropriées. Il existe toutefois certaines différences communes à l’ensemble des déploiements Azure, dont certaines varient selon le scénario de déploiement spécifique. Deux différences fondamentales sont décrites ci-après :

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Les machines virtuelles Azure auront peut-être besoin d’un accès au réseau local d’entreprise.
La reconnexion des machines virtuelles Azure à un réseau d’entreprise local nécessite un réseau virtuel Azure, lequel comprend un composant de réseau privé virtuel (VPN) site à site ou site à point capable de connecter de façon transparente les machines virtuelles Azure et les ordinateurs locaux. Ce composant VPN peut également permettre aux ordinateurs membres du domaine local d’accéder à un domaine Windows Server Active Directory dont les contrôleurs de domaine sont hébergés exclusivement sur des machines virtuelles Azure. Il est toutefois important de noter qu’un échec du VPN entraînera également l’échec de l’authentification et des autres opérations qui dépendent de Windows Server Active Directory. Bien que les utilisateurs puissent se connecter à l’aide des informations d’identification existantes mises en cache, aucune tentative d’authentification d’égal à égal ou de client à serveur pour laquelle des tickets doivent être émis ou sont obsolètes ne pourra aboutir.

Consultez la page [Réseau virtuel](http://azure.microsoft.com/documentation/services/virtual-network/) pour obtenir une vidéo de démonstration et une liste de didacticiels pas à pas, notamment pour savoir [configurer un VPN de site à site dans le portail Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Vous pouvez également déployer Windows Server Active Directory sur un réseau virtuel Azure qui ne dispose pas d’accès à un réseau local. Cependant, les instructions de cette rubrique impliquent l’utilisation d’un réseau virtuel Azure, car il intègre les fonctions d’adressage IP indispensables à Windows Server.
> 
> 

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Les adresses IP doivent être configurées à l’aide d’Azure PowerShell.
Les adresses dynamiques sont allouées par défaut, mais vous pouvez utiliser l’applet de commande Set-AzureStaticVNetIP pour attribuer une adresse IP statique. Cette applet de commande définit une adresse IP statique qui persistera pendant la réparation de service et l’arrêt/le redémarrage de la machine virtuelle. Pour plus d’informations, consultez [Static internal IP address for virtual machines](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)(Adresse IP interne statique pour les machines virtuelles).

## <a name="BKMK_Glossary"></a>Termes et définitions
Voici une liste non exhaustive de termes référencés dans le présent article concernant diverses technologies Azure.

* **Machines virtuelles Azure**: offre IaaS dans Azure qui permet aux clients de déployer des machines virtuelles exécutant pratiquement n’importe quelle charge de travail classique de serveur local.
* **Réseau virtuel Azure**: service de mise en réseau dans Azure qui permet aux clients de créer et gérer des réseaux virtuels dans Azure et de les associer en toute sécurité à leur propre infrastructure réseau locale à l’aide d’un réseau privé virtuel (VPN).
* **Adresse IP virtuelle**: adresse IP Internet qui n’est pas liée à un ordinateur ou à une carte d’interface réseau spécifique. Une adresse IP virtuelle est allouée aux services cloud pour leur permettre de recevoir le trafic réseau qui est redirigé vers une machine virtuelle Azure. Une adresse IP virtuelle est une propriété d’un service cloud qui contient une ou plusieurs machines virtuelles Azure. Notez également qu’un réseau virtuel Azure peut contenir un ou plusieurs services cloud. Les adresses IP virtuelles fournissent des fonctions d’équilibrage de charge.
* **Adresse IP dynamique**: adresse IP utilisée uniquement en interne. Elle doit être configurée comme adresse IP statique (à l’aide de l’applet de commande Set-AzureStaticVNetIP) pour les machines virtuelles hébergeant les rôles de serveur DC/DNS.
* **Réparation de service**: processus au cours duquel Azure restaure automatiquement l’état d’exécution d’un service après avoir détecté un échec du service. La réparation de service représente un des aspects d’Azure au service de la disponibilité et de la résilience. Bien que cela soit peu probable, le résultat obtenu après un incident de réparation de service pour un contrôleur de domaine exécuté sur une machine virtuelle est semblable à un redémarrage non planifié, mais présente quelques inconvénients :
  
  * la carte réseau virtuelle de la machine virtuelle sera modifiée ;
  * l’adresse MAC de la carte réseau virtuelle sera modifiée ;
  * l’ID de processeur/unité centrale de la machine virtuelle sera modifié ;
  * la configuration IP de la carte réseau virtuelle ne sera pas modifiée tant que la machine virtuelle est attachée à un réseau virtuel et que l’adresse IP de la machine virtuelle est statique.
  
  Aucun de ces comportements n’affecte Windows Server Active Directory puisqu’il n’existe aucune dépendance sur l’adresse MAC ou l’ID de processeur/unité centrale. En outre, il est recommandé d’exécuter tous les déploiements de Windows Server Active Directory sur un réseau virtuel Azure comme expliqué ci-dessus.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>La virtualisation de contrôleurs de domaine Windows Server Active Directory peut-elle s’effectuer en toute sécurité ?
Le déploiement de contrôleurs de domaine Windows Server Active Directory sur des machines virtuelles Azure est soumis aux mêmes instructions que l’exécution de contrôleurs de domaine en local dans une machine virtuelle. L’exécution de contrôleurs de domaine virtualisés constitue une pratique prudente tant que vous respectez les instructions de sauvegarde et de restauration des contrôleurs de domaine. Pour plus d’informations sur les contraintes et les recommandations d’exécution des contrôleurs de domaine virtualisés, consultez la page [Exécution de contrôleurs de domaine dans Hyper-V](https://technet.microsoft.com/library/dd363553).

Les hyperviseurs fournissent ou banalisent les technologies susceptibles de poser problème pour plusieurs systèmes distribués, notamment Windows Server Active Directory. Par exemple, sur un serveur physique, vous pouvez cloner un disque ou utiliser des méthodes non prises en charge pour restaurer l’état d’un serveur, notamment en utilisant des SAN, mais il est beaucoup plus difficile d’effectuer cette opération sur un serveur physique que de restaurer un instantané de machine virtuelle dans un hyperviseur. Certaines fonctionnalités d’Azure peuvent entraîner les mêmes effets indésirables. Par exemple, il est déconseillé de copier les fichiers VHD des contrôleurs de domaine au lieu d’effectuer des sauvegardes régulières, car leur restauration risque d’entraîner une situation semblable à l’utilisation des fonctionnalités de restauration d’instantané.

De telles restaurations introduisent des bulles USN qui peuvent provoquer un état définitivement divergent entre les contrôleurs de domaine. Cela peut être la cause de divers problèmes, notamment :

* des objets en attente ;
* des mots de passe incohérents ;
* des valeurs d’attribut incohérentes ;
* des incompatibilités de schéma si le contrôleur de schéma est restauré.

Pour plus d’informations sur la manière dont les contrôleurs de domaine peuvent être affectés, consultez [USN and USN Rollback](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback)(USN et restauration d’USN).

Depuis Windows Server 2012, [des dispositifs de protection supplémentaires sont intégrés aux services AD DS](https://technet.microsoft.com/library/hh831734.aspx). Ces dispositifs protègent les contrôleurs de domaine virtualisés contre les problèmes susmentionnés, à condition que la plateforme de l’hyperviseur sous-jacent prenne en charge VM-GenerationID. Azure prend en charge VM-GenerationID, ce qui signifie que les contrôleurs de domaine qui exécutent Windows Server 2012 ou version ultérieure sur des machines virtuelles Azure disposent de dispositifs de protection supplémentaires.

> [!NOTE]
> Il est recommandé d’éteindre et redémarrer une machine virtuelle exécutant le rôle de contrôleur de domaine dans Azure depuis le système d’exploitation invité plutôt que d’utiliser l’option **Arrêter** du portail Azure. Aujourd’hui, l’utilisation du portail pour arrêter une machine virtuelle a pour effet d’annuler l’allocation de cette dernière. Une machine virtuelle qui n’est plus allouée présente l’avantage de ne pas entraîner de frais ; pour autant, elle a pour effet de réinitialiser le VM-GenerationID, ce qui n’est pas souhaitable pour un contrôleur de domaine. Lorsque le VM-GenerationID est réinitialisé, l’invocationID de la base de données AD DS est également réinitialisé, le pool RID est supprimé et SYSVOL est marqué comme ne faisant pas autorité. Pour plus d’informations, consultez l’article [Présentation de la virtualisation des services de domaine Active Directory (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) et la rubrique [Safely Virtualizing DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx) (Virtualisation DFSR sécurisée).
> 
> 

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Pourquoi déployer Windows Server AD DS sur des machines virtuelles Azure ?
De nombreux scénarios de déploiement de Windows Server AD DS sont parfaitement adaptés au déploiement sur des machines virtuelles Azure. Supposons par exemple que vous gérez une société en Europe qui a besoin d’authentifier des utilisateurs sur un site distant en Asie. La société n’a pas encore déployé de contrôleurs de domaine Windows Server Active Directory en Asie en raison de leur coût de déploiement et d’une expertise limitée en matière de gestion des serveurs après le déploiement. Les demandes d’authentification provenant d’Asie sont donc traitées par les contrôleurs de domaine déployés en Europe, mais avec des performances mitigées. Dans ce cas, vous pouvez déployer un contrôleur de domaine sur une machine virtuelle qui doit s’exécuter dans le centre de données Azure en Asie. Le fait d’attacher ce contrôleur de domaine à un réseau virtuel Azure directement connecté au site distant améliorera les performances de l’authentification.

Azure convient également en tant que substitut des sites de récupération d’urgence coûteux. Le coût relativement faible lié à l’hébergement d’un petit nombre de contrôleurs de domaine et d’un seul réseau virtuel sur Azure représente une solution intéressante.

Pour finir, vous pouvez déployer une application réseau sur Azure, telle que SharePoint, qui nécessite Windows Server Active Directory mais qui n’a aucune dépendance sur le réseau local ou sur l’instance Windows Server Active Directory d’entreprise. Dans ce cas, il est intéressant de déployer une forêt isolée sur Azure pour répondre aux exigences de SharePoint. Là encore, le déploiement d’applications réseau qui nécessitent une connectivité au réseau local et à l’instance Active Directory d’entreprise est également pris en charge.

> [!NOTE]
> Grâce à une connexion de couche 3, le composant VPN qui assure la connectivité entre un réseau virtuel Azure et un réseau local peut également permettre aux serveurs membres exécutés en local d’exploiter les contrôleurs de domaine exécutés en tant que machines virtuelles sur un réseau virtuel Azure. En revanche, si la connexion VPN est indisponible, la communication entre les ordinateurs locaux et les contrôleurs de domaine Azure ne pourra pas être établie, ce qui entraînera des problèmes d’authentification et d’autres erreurs.  
> 
> 

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Différences entre le déploiement de contrôleurs de domaine Windows Server Active Directory sur des machines virtuelles Azure et leur déploiement en local
* Pour tout scénario de déploiement de Windows Server Active Directory comprenant une seule machine virtuelle, il est nécessaire d’utiliser un réseau virtuel Azure pour garantir la cohérence des adresses IP. Notez que ce guide suppose que les contrôleurs de domaine s’exécutent sur un réseau virtuel Azure.
* Comme pour les contrôleurs de domaine locaux, l’utilisation d’adresses IP statiques est recommandée. Une adresse IP statique peut uniquement être configurée à l’aide d’Azure PowerShell. Pour plus d’informations, consultez [Static internal IP address for VMs](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) (Adresse IP interne statique pour les machines virtuelles). Si vous disposez de systèmes de surveillance ou d’autres solutions permettant de vérifier la configuration des adresses IP statiques dans le système d’exploitation invité, vous pouvez affecter la même adresse IP statique aux propriétés de carte réseau de la machine virtuelle. Sachez toutefois que la carte réseau sera ignorée si la machine virtuelle subit une réparation de service ou si elle est arrêtée dans le portail Azure et que son adresse est désallouée. Dans ce cas, l’adresse IP statique dans l’invité devra être réinitialisée.
* Le déploiement de machines virtuelles sur un réseau virtuel n’implique (ni ne nécessite) de connectivité à un réseau local ; le réseau virtuel le permet simplement. Vous devez créer un réseau virtuel pour établir la communication privée entre Azure et votre réseau local. Vous devez déployer un point de terminaison VPN sur le réseau local. Le VPN est ouvert entre Azure et le réseau local. Pour plus d’informations, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md) et l’article dédié à la [configuration d’un VPN de site à site dans le portail Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [!NOTE]
> Il existe une option de [création de VPN de point à site](../vpn-gateway/vpn-gateway-point-to-site-create.md) permettant de connecter directement des ordinateurs Windows à un réseau virtuel Azure.
> 
> 

* Que vous créiez ou non un réseau virtuel, Azure facture le trafic sortant, mais pas le trafic entrant. Les différents choix de conception de Windows Server Active Directory peuvent affecter le volume de trafic sortant généré par un déploiement. Par exemple, le déploiement d’un contrôleur de domaine en lecture seule (RODC) limite le trafic sortant, puisqu’il ne le réplique pas. Mais la décision de déployer un contrôleur de domaine en lecture seule doit être contrebalancée avec la nécessité d’effectuer des opérations d’écriture sur le contrôleur de domaine et avec la [compatibilité](https://technet.microsoft.com/library/cc755190) des applications et services du site avec les contrôleurs de domaine en lecture seule. Pour plus d’informations sur les frais liés au trafic, consultez la page [Tarification Azure](http://azure.microsoft.com/pricing/).
* Bien que vous ayez un contrôle total sur les ressources serveur à utiliser pour les machines virtuelles locales, notamment la quantité de RAM et la taille du disque, sur Azure, vous devez effectuer une sélection dans une liste de tailles de serveur préconfigurées. Pour un contrôleur de domaine, vous devez ajouter un disque de données au disque du système d’exploitation afin de stocker la base de données Windows Server Active Directory.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Est-il possible de déployer Windows Server AD FS sur des machines virtuelles Azure ?
Oui, vous pouvez déployer Windows Server AD FS sur des machines virtuelles, les [meilleures pratiques en matière de déploiement d’AD FS](https://technet.microsoft.com/library/dn151324.aspx) en local s’appliquant également au déploiement d’AD FS sur Azure. Cependant, certaines meilleures pratiques, comme l’équilibrage de la charge et la haute disponibilité, supposent de faire appel à des technologies allant au-delà des fonctions proposées par AD FS. Elles doivent être fournies par l’infrastructure sous-jacente. Nous allons passer en revue quelques-unes de ces meilleures pratiques et voir comment les appliquer à l’aide de machines virtuelles Azure et d’un réseau virtuel Azure.

1. **Ne jamais exposer de serveurs de service d’émission de jeton de sécurité (STS) directement sur Internet.**
   
    Ce point est important car le service STS émet des jetons de sécurité. Par conséquent, les serveurs STS tels que des serveurs AD FS doivent être traités avec le même niveau de protection que celui d’un contrôleur de domaine. Si un service d’émission de jeton de sécurité est corrompu, les utilisateurs malveillants ont la possibilité d’émettre des jetons d’accès qui contiennent potentiellement les revendications de leur choix dans les applications par partie de confiance et d’autres serveurs STS d’organisations fiables.
2. **Déployer des contrôleurs de domaine Active Directory pour tous les domaines d’utilisateurs dans le même réseau que les serveurs AD FS.**
   
    Les serveurs AD FS utilisent Active Directory Domain Services pour l’authentification des utilisateurs. Il est recommandé de déployer des contrôleurs de domaine sur le même réseau que les serveurs AD FS, ce afin de garantir la continuité d’activités si la liaison entre le réseau Azure et votre réseau local est rompue, tout en diminuant la latence et en améliorant les performances des connexions.
3. **Déployer plusieurs nœuds AD FS pour la haute disponibilité et l’équilibrage de la charge.**
   
    Dans la plupart des cas, l’échec d’une application activée par AD FS est inacceptable, car les applications qui requièrent des jetons de sécurité sont souvent critiques. Par conséquent, et étant donné que AD FS se situe désormais sur le chemin d’accès aux applications critiques, le service AD FS doit être hautement disponible via plusieurs proxys et serveurs AD FS. Pour assurer la distribution des demandes, des équilibreurs de charge sont généralement déployés devant les proxys et les serveurs AD FS.
4. **Déployer un ou plusieurs nœuds de proxy d’application web pour accéder à Internet.**
   
    Lorsque les utilisateurs doivent accéder à des applications protégées par le service AD FS, le service AD FS doit être disponible à partir d’internet. Pour cela, vous devez déployer le service de proxy d’application web. Il est fortement recommandé de déployer plusieurs nœuds pour garantir à la fois la haute disponibilité et l’équilibrage de la charge.
5. **Restreindre l’accès aux ressources réseau internes à partir des nœuds de proxy d’application web.**
   
    Pour permettre aux utilisateurs externes d’accéder aux services AD FS à partir d’Internet, vous devez déployer des nœuds de proxy d’application web (ou proxy AD FS dans les versions antérieures de Windows Server). Les nœuds de proxy d’application web sont directement exposés à Internet. Il n’est pas nécessaire de les joindre au domaine et ils doivent uniquement accéder aux serveurs AD FS via les ports TCP 443 et 80. Il est fortement recommandé de bloquer la communication vers tous les autres ordinateurs (en particulier les contrôleurs de domaine).
   
    Pour cela, vous devez généralement agir en local au moyen d’une zone démilitarisée (DMZ). Les pare-feu utilisent un mode d’autorisation par liste d’approbation pour limiter le trafic entre la DMZ et le réseau local (ce qui signifie que seul le trafic provenant des adresses IP et des ports spécifiés est autorisé, tout autre trafic étant bloqué).

Le schéma suivant illustre ce concept en présentant un exemple de déploiement AD FS en local.

![Diagramme d’un déploiement local de services de fédération Active Directory traditionnels](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Cependant, puisqu’Azure ne fournit pas de fonctionnalités de pare-feu complètes de façon native, vous devez utiliser d’autres options pour restreindre le trafic. Le tableau suivant présente chaque option, avec ses avantages et ses inconvénients.

| Option | Avantage | Inconvénient |
| --- | --- | --- |
| [Listes de contrôle d’accès (ACL) du réseau Azure](../virtual-network/virtual-networks-acl.md) |Configuration initiale moins coûteuse et plus simple. |Configuration ACL réseau supplémentaire requise en cas d’ajout de machines virtuelles au déploiement. |
| [Appliance Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) |Mode d’autorisation par liste d’approbation ; aucune configuration ACL réseau requise. |Augmentation des coûts et configuration initiale plus coûteuse. |

Dans ce cas, la procédure générale pour le déploiement d’AD FS est la suivante :

1. Créer un réseau virtuel avec une connectivité intersite, à l’aide d’un réseau privé virtuel (VPN) ou [d’ExpressRoute](http://azure.microsoft.com/services/expressroute/).
2. Déployer des contrôleurs de domaine sur le réseau virtuel. Cette étape est facultative mais recommandée.
3. Déployer des serveurs AD FS joints au domaine sur le réseau virtuel.
4. Créer un [jeu d’équilibrage de la charge interne](http://azure.microsoft.com/blog/internal-load-balancing/) qui inclut les serveurs AD FS et utilise une nouvelle adresse IP privée dans le réseau virtuel (une adresse IP dynamique).
   
   1. Mettre à jour le DNS pour créer le nom de domaine complet afin de pointer vers l’adresse IP privée (dynamique) du jeu d’équilibrage de la charge interne.
5. Créer un service cloud (ou un réseau virtuel distinct) pour les nœuds de proxy d’application web.
6. Déployer les nœuds de proxy d’application web dans le service cloud ou dans un réseau virtuel.
   
   1. Créer un jeu d’équilibrage de la charge externe qui inclut les nœuds de proxy d’application web.
   2. Mettre à jour le nom DNS externe (FQDN) pour pointer vers l’adresse publique du service cloud (l’adresse IP virtuelle).
   3. Configurer les proxys AD FS pour utiliser le nom de domaine complet correspondant au jeu d’équilibrage de la charge interne pour les serveurs AD FS.
   4. Mettre à jour les sites web basés sur les revendications afin d’utiliser le nom de domaine complet externe pour leur fournisseur de revendications.
7. Limiter l’accès entre le proxy d’application web et les ordinateurs du réseau virtuel AD FS.

Pour limiter le trafic, vous devez configurer le jeu d’équilibrage de la charge de l’équilibreur de charge interne Azure pour qu’il accepte uniquement le trafic vers les ports 80 et 443, bloquant ainsi le trafic vers l’adresse IP dynamique interne du jeu d’équilibrage de la charge.

![Diagramme des ACL réseau ADFS avec autorisation TCP 443 + 80.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Le trafic vers les serveurs AD FS doit uniquement être autorisé par les sources suivantes :

* l’équilibreur de charge interne Azure ;
* l’adresse IP d’un administrateur sur le réseau local.

> [!WARNING]
> La conception doit empêcher les nœuds de proxy d’application web d’atteindre les autres machines virtuelles du réseau virtuel Azure ou tout autre emplacement sur le réseau local. Pour ce faire, configurez les règles de pare-feu dans l’appliance locale pour les connexions ExpressRoute ou le périphérique VPN pour les connexions VPN de site à site.
> 
> 

Cette option présente un inconvénient : vous devez configurer les ACL réseau pour plusieurs appareils, y compris l’équilibreur de charge interne, les serveurs AD FS et les autres serveurs ajoutés au réseau virtuel. Si un appareil est ajouté au déploiement sans configurer les ACL réseau pour limiter le trafic entrant, l’intégralité du déploiement peut être menacée. Si les adresses IP des nœuds du proxy d’application web sont modifiées, l’ACL réseau doit être réinitialisé (ce qui signifie que les serveurs proxy doivent être configurés pour utiliser des [adresses IP dynamiques statiques](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)).

![ADFS sur Azure avec ACL réseau.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Une autre option consiste à utiliser l’appliance [Barracuda NG Firewall](https://www.barracuda.com/products/ngfirewall) pour contrôler le trafic entre les serveurs proxy AD FS et les serveurs AD FS. Cette option est conforme aux meilleures pratiques en matière de sécurité et de haute disponibilité et nécessite moins d’efforts d’administration après la configuration initiale, car l’appliance Barracuda NG Firewall fournit un mode d’autorisation par liste d’approbation pour l’administration du pare-feu et peut être installée directement sur un réseau virtuel Azure. Cela permet de supprimer la nécessité de configurer les ACL réseau chaque fois qu’un nouveau serveur est ajouté au déploiement. Néanmoins, cette option ajoute un certain degré de complexité au déploiement initial et entraîne des coûts supplémentaires.

Dans ce cas, deux réseaux virtuels sont déployés au lieu d’un seul. Nous les appellerons le réseau virtuel 1 et le réseau virtuel 2. Le réseau virtuel 1 contient les proxys et le réseau virtuel 2 comprend les STS et la connexion réseau vers le réseau d’entreprise. Le réseau virtuel 1 est donc physiquement (bien que virtuellement) isolé du réseau virtuel 2 et du réseau d’entreprise. Le réseau virtuel 1 est ensuite connecté au réseau virtuel 2 via une technologie de tunneling spéciale appelée architecture de réseau d’information des télécommunications (Transport Independent Network Architecture, TINA). Le tunnel TINA est associé à chacun des réseaux virtuels à l’aide d’un pare-feu Barracuda NG (avec un pare-feu Barracuda sur chaque réseau virtuel).  Pour bénéficier d’une haute disponibilité, nous vous recommandons de déployer deux pare-feu Barracuda sur chaque réseau virtuel ; l’un actif, l’autre passif. Ils offrent des capacités de pare-feu très complètes qui nous permettent de reproduire le fonctionnement d’un réseau de périmètre local traditionnel dans Azure.

![ADFS sur Azure avec pare-feu.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Pour plus d’informations, consultez [AD FS : étendre une application frontale locale prenant en charge les revendications à Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Alternative au déploiement AD FS si votre objectif est l’authentification unique Office 365 uniquement
Il existe une autre solution pour déployer totalement AD FS si votre objectif est uniquement d’activer la connexion à Office 365. Dans ce cas, vous pouvez simplement déployer DirSync avec synchronisation des mots de passe en local et obtenir le même résultat avec un degré de complexité minimal, puisque cette approche ne nécessite pas AD FS ni Azure.

Le tableau suivant compare les processus de connexion avec et sans le déploiement d’AD FS.

| Authentification unique Office 365 avec AD FS et DirSync | Connexion à Office 365 identique avec DirSync + synchronisation de mots de passe |
| --- | --- |
| 1. L’utilisateur se connecte à un réseau d’entreprise et est authentifié à Windows Server Active Directory. |1. L’utilisateur se connecte à un réseau d’entreprise et est authentifié à Windows Server Active Directory. |
| 2. L’utilisateur tente d’accéder à Office 365 (je suis @contoso.com). |2. L’utilisateur tente d’accéder à Office 365 (je suis @contoso.com). |
| 3. Office 365 redirige l’utilisateur vers Azure AD. |3. Office 365 redirige l’utilisateur vers Azure AD. |
| 4. Dans la mesure où Azure AD ne peut pas authentifier l’utilisateur et comprend qu’il existe une approbation avec AD FS en local, il redirige l’utilisateur vers AD FS. |4. Azure AD ne peut pas accepter de tickets Kerberos directement et aucune relation d’approbation n’existe. L’utilisateur est donc invité à saisir des informations d’identification. |
| 5. L’utilisateur envoie un ticket Kerberos au STS AD FS. |5. L’utilisateur saisit le même mot de passe local et Azure AD le valide en fonction du nom d’utilisateur et du mot de passe qui ont été synchronisés par DirSync. |
| 6. AD FS transforme le ticket Kerberos pour les revendications/le format de jeton requis et redirige l’utilisateur vers Azure AD. |6. Azure AD redirige l’utilisateur vers Office 365. |
| 7. L’utilisateur s’authentifie à Azure AD (une autre transformation se produit). |7. L’utilisateur peut se connecter à Office 365 et OWA à l’aide du jeton Azure AD. |
| 8. Azure AD redirige l’utilisateur vers Office 365. | |
| 9. L’utilisateur est connecté en mode silencieux à Office 365. | |

Dans le scénario de connexion à Office 365 avec la synchronisation de mot de passe (sans AD FS), l’authentification unique est remplacée par « l’authentification identique » où « identique » signifie simplement que les utilisateurs doivent confirmer leurs informations d’identification locales identiques lorsqu’ils accèdent à Office 365. Veuillez noter que ces informations peuvent être conservées par le navigateur de l’utilisateur, ce qui permet de réduire le nombre d’invites à chaque tentative de connexion.

### <a name="additional-food-for-thought"></a>Autres éléments de réflexion
* Si vous déployez un proxy AD FS sur une machine virtuelle Azure, une connectivité aux serveurs AD FS est nécessaire. Si ces serveurs sont locaux, nous vous recommandons de tirer parti de la connectivité VPN site à site fournie par le réseau virtuel pour permettre aux nœuds de proxy d’application web de communiquer avec leurs serveurs AD FS.
* Si vous déployez un serveur AD FS sur une machine virtuelle Azure, la connectivité aux contrôleurs de domaine Windows Server Active Directory, aux magasins d’attributs et aux bases de données de configuration est nécessaire et peut également nécessiter une ExpressRoute ou une connexion VPN site à site entre le réseau virtuel Azure et le réseau local.
* Des frais supplémentaires s’appliquent pour l’ensemble du trafic à partir des machines virtuelles Azure (trafic sortant). Si le coût est un facteur déterminant, nous vous recommandons de déployer les nœuds de proxy d’application web sur Azure, en laissant les serveurs AD FS en local. Si les serveurs AD FS sont déployés sur des machines virtuelles Azure, des frais supplémentaires s’appliquent pour authentifier les utilisateurs locaux. Le trafic sortant génère un coût, qu’il traverse ou non l’ExpressRoute ou la connexion VPN site à site.
* Si vous décidez d’utiliser les fonctionnalités Azure natives d’équilibrage de charge pour assurer la haute disponibilité des serveurs AD FS, notez que l’équilibrage de charge fournit les sondes utilisées pour déterminer l’intégrité des machines virtuelles dans le service cloud. Dans le cas de machines virtuelles Azure (par opposition à des rôles de travail ou web), une sonde personnalisée doit être utilisée puisque l’agent qui répond aux sondes par défaut n’est pas présent sur les machines virtuelles Azure. Pour plus de simplicité, vous pouvez utiliser une sonde TCP personnalisée. Cela nécessite uniquement qu’une connexion TCP (un segment TCP SYN envoyé et qui obtient une réponse d’un segment TCP SYN ACK) soit correctement établie pour déterminer l’intégrité de la machine virtuelle. Vous pouvez configurer la sonde personnalisée pour utiliser n’importe quel port TCP que vos machines virtuelles écoutent.

> [!NOTE]
> Les machines qui ont besoin d’exposer le même ensemble de ports directement à Internet (tels que les ports 80 et 443) ne peuvent pas partager le même service cloud. Par conséquent, nous vous recommandons de créer un service cloud dédié pour vos serveurs Windows Server AD FS afin d’éviter les chevauchements éventuels entre les exigences de port pour une application et Windows Server AD FS.
> 
> 

## <a name="deployment-scenarios"></a>Scénarios de déploiement
La section suivante décrit les scénarios courants de déploiement et met l’accent sur les considérations importantes à prendre en compte. Chaque scénario comporte des liens vers plus d’informations sur les décisions et les facteurs dont il faut tenir compte.

1. [AD DS : déployer une application compatible AD DS ne nécessitant pas de connectivité au réseau d’entreprise](#BKMK_CloudOnly)
   
    Par exemple, un service SharePoint accessible sur Internet est déployé sur une machine virtuelle Azure. L’application n’a pas de dépendances sur les ressources du réseau d’entreprise. L’application nécessite Windows Server AD DS, mais NE nécessite PAS le Windows Server AD DS d’entreprise.
2. [AD FS : étendre une application frontale locale prenant en charge les revendications à Internet.](#BKMK_CloudOnlyFed)
   
    Par exemple, une application prenant en charge les revendications qui a été correctement déployée en local et utilisée par les utilisateurs d’entreprise doit devenir accessible à partir d’Internet. L’application doit être accessible directement depuis Internet par des partenaires commerciaux à l’aide de leurs propres identités d’entreprise et par les utilisateurs d’entreprise existants.
3. [AD DS : déployer une application compatible Windows Server AD DS qui nécessite une connexion au réseau d’entreprise](#BKMK_HybridExt)
   
    Par exemple, une application compatible LDAP qui prend en charge l’authentification Windows intégrée et utilise Windows Server AD DS en tant que référentiel pour les données de profil utilisateur et de configuration est déployée sur une machine virtuelle Azure. Il est souhaitable que l’application tire parti du Windows Server AD DS d’entreprise existant et fournisse une authentification unique. L’application ne prend pas en charge les revendications.

### <a name="BKMK_CloudOnly"></a>1. AD DS : déployer une application compatible AD DS ne nécessitant pas de connectivité au réseau d’entreprise
![Déploiement AD DS dans le cloud seulement](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figure 1**

#### <a name="description"></a>Description
SharePoint est déployé sur une machine virtuelle Azure et l’application n’a pas de dépendances sur les ressources du réseau d’entreprise. L’application nécessite Windows Server AD DS, mais ne nécessite *pas* le Windows Server AD DS d’entreprise. Aucune approbation Kerberos ou fédérée n’est requise, puisque les utilisateurs sont configurés automatiquement via l’application dans le domaine Windows Server AD DS qui est également hébergé dans le cloud sur des machines virtuelles Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considérations sur le scénario et comment les domaines technologiques s’appliquent au scénario
* [Topologie du réseau](#BKMK_NetworkTopology): créez un réseau virtuel Azure sans connectivité intersite (également appelée connectivité site à site).
* [Configuration du déploiement du contrôleur de domaine](#BKMK_DeploymentConfig): déployez un nouveau contrôleur de domaine dans une nouvelle forêt Windows Server Active Directory à un seul domaine. Ce déploiement doit être effectué en parallèle du déploiement du serveur DNS Windows.
* [Topologie du site Windows Server Active Directory](#BKMK_ADSiteTopology): utilisez le site Windows Server Active Directory par défaut (tous les ordinateurs seront dans Default-First-Site-Name).
* [Adressage IP et DNS](#BKMK_IPAddressDNS):
  
  * Définissez une adresse IP statique pour le contrôleur de domaine à l’aide de l’applet de commande Set-AzureStaticVNetIP Azure PowerShell.
  * Installez et configurez le serveur DNS Windows Server sur les contrôleurs de domaine sur Azure.
  * Configurez les propriétés du réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de contrôleur de domaine et de serveur DNS.
* [Catalogue global](#BKMK_GC): le premier contrôleur de domaine de la forêt doit être un serveur de catalogue global. Les contrôleurs de domaine supplémentaires doivent également être configurés en tant que catalogues globaux car dans une forêt à un seul domaine, le catalogue global ne requiert aucun travail supplémentaire du contrôleur de domaine.
* [Emplacement de la base de données Windows Server AD DS et SYSVOL](#BKMK_PlaceDB): ajoutez un disque de données aux contrôleurs de domaine exécutés en tant que machines virtuelles Azure, afin de stocker la base de données Windows Server Active Directory, les journaux et SYSVOL.
* [Sauvegarde et restauration](#BKMK_BUR): déterminez l’endroit où vous souhaitez stocker les sauvegardes de l’état du système. Si nécessaire, ajoutez un autre disque de données à la machine virtuelle du contrôleur de domaine pour stocker les sauvegardes.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS : étendre une application frontale locale prenant en charge les revendications à Internet
![Fédération avec connectivité intersite](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figure 2**

#### <a name="description"></a>Description
Une application prenant en charge les revendications qui a été correctement déployée en local et utilisée par les utilisateurs d’entreprise doit devenir directement accessible à partir d’Internet. L’application fait office de serveur web frontal pour une base de données SQL dans laquelle les données sont stockées. Les serveurs SQL utilisés par l’application sont également disponibles sur le réseau d’entreprise. Deux STS Windows Server AD FS et un équilibreur de charge ont été déployés en local pour fournir un accès aux utilisateurs d’entreprise. L’application doit désormais être accessible directement depuis Internet par des partenaires commerciaux à l’aide de leurs propres identités d’entreprise et par les utilisateurs d’entreprise existants.

Dans le but de simplifier et de répondre aux besoins de déploiement et de configuration de cette nouvelle exigence, deux autres serveurs web frontaux et deux serveurs de proxy Windows Server AD FS sont installés sur les machines virtuelles Azure. Les quatre machines virtuelles seront exposées directement à Internet et bénéficieront d’une connectivité au réseau local à l’aide de la fonctionnalité VPN site à site du réseau virtuel Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considérations sur le scénario et comment les domaines technologiques s’appliquent au scénario
* [Topologie du réseau](#BKMK_NetworkTopology) : créez un réseau virtuel Azure et [configurez la connectivité intersite](../vpn-gateway/vpn-gateway-site-to-site-create.md).
  
  > [!NOTE]
  > Pour chacun des certificats Windows Server AD FS, assurez-vous que l’URL définie dans le modèle de certificat et les certificats qui en résultent sont accessibles par les instances Windows Server AD FS s’exécutant sur Azure. Cela peut nécessiter une connectivité entre les différents locaux à des parties de votre infrastructure à clé publique. Par exemple, si un point de terminaison de la liste de révocation de certificats est basé sur LDAP et hébergé exclusivement en local, alors une connectivité entre les différents locaux sera nécessaire. Si cela n’est pas souhaitable, vous pouvez utiliser des certificats émis par une autorité de certification dont la liste de révocation de certificats est accessible sur Internet.
  > 
  > 
* [Configuration des services cloud](#BKMK_CloudSvcConfig): assurez-vous d’avoir deux services cloud pour fournir deux adresses IP virtuelles à équilibrage de charge. L’adresse IP virtuelle du premier service cloud est dirigée vers les deux machines virtuelles de proxy Windows Server AD FS sur les ports 80 et 443. Les machines virtuelles de proxy Windows Server AD FS sont configurées pour pointer vers l’adresse IP de l’équilibreur de charge local devant les STS Windows Server AD FS. L’adresse IP virtuelle du deuxième service cloud est dirigée vers les deux machines virtuelles exécutant une nouvelle fois le serveur web frontal sur les ports 80 et 443. Configurez une sonde personnalisée pour vous assurer que l’équilibreur de charge dirige uniquement le trafic vers les machines virtuelles du serveur web frontal et de proxy Windows Server AD FS en cours de fonctionnement.
* [Configuration du serveur de fédération](#BKMK_FedSrvConfig): configurez Windows Server AD FS en tant que serveur de fédération (STS), afin de générer des jetons de sécurité pour la forêt Windows Server Active Directory créée dans le cloud. Définissez des relations d’approbation de fournisseur de revendications de fédération avec les différents partenaires dont vous souhaitez accepter les identités et configurez des relations d’approbation de partie de confiance avec les différentes applications pour lesquelles vous souhaitez générer des jetons.
  
    Dans la plupart des scénarios, les serveurs de proxy Windows Server AD FS sont déployés dans une fonctionnalité sur Internet pour des raisons de sécurité, tandis que leurs homologues de fédération Windows Server AD FS restent isolés d’une connectivité directe à Internet. Quel que soit votre scénario de déploiement, vous devez configurer votre service cloud avec une adresse IP virtuelle qui fournira une adresse IP exposée publiquement et le port qui est en mesure d’équilibrer la charge entre vos deux instances STS Windows Server AD FS ou de proxy.
* [Configuration de la haute disponibilité de Windows Server AD FS](#BKMK_ADFSHighAvail): nous vous recommandons de déployer une batterie Windows Server AD FS avec au moins deux serveurs pour le basculement et l’équilibrage de charge. Vous pouvez envisager l’utilisation de la base de données interne Windows (WID) pour les données de configuration Windows Server AD FS et vous servir de la fonction d’équilibrage de charge interne d’Azure pour répartir les demandes entrantes sur les serveurs de la batterie.

Pour plus d’informations, consultez le [Guide de déploiement d’AD DS](https://technet.microsoft.com/library/cc753963).

### <a name="BKMK_HybridExt"></a>3. AD DS : déployer une application compatible Windows Server AD DS qui nécessite une connexion au réseau d’entreprise
![Déploiement AD DS dans différents locaux](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**Figure 3**

#### <a name="description"></a>Description
Une application compatible LDAP est déployée sur une machine virtuelle Azure. Elle prend en charge l’authentification Windows intégrée et utilise Windows Server AD DS comme référentiel pour les données de profil utilisateur et de configuration. L’objectif est que l’application tire parti du Windows Server AD DS d’entreprise existant et fournisse une authentification unique. L’application ne prend pas en charge les revendications. Les utilisateurs doivent également accéder à l’application directement à partir d’Internet. Pour optimiser les performances et les coûts, les deux contrôleurs de domaine supplémentaires, qui font partie du domaine d’entreprise, sont déployés avec l’application dans Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considérations sur le scénario et comment les domaines technologiques s’appliquent au scénario
* [Topologie du réseau](#BKMK_NetworkTopology) : créez un réseau virtuel Azure avec [une connectivité intersite](../vpn-gateway/vpn-gateway-site-to-site-create.md).
* [Méthode d’installation](#BKMK_InstallMethod): déployez des contrôleurs de domaine réplicas à partir du domaine Windows Server Active Directory d’entreprise. Pour un contrôleur de domaine réplica, vous pouvez installer Windows Server AD DS sur la machine virtuelle et éventuellement utiliser la fonctionnalité d’installation à partir d’un support pour réduire la quantité de données devant être répliquées sur le nouveau contrôleur de domaine lors de l’installation. Pour obtenir un didacticiel, consultez [Installation d’un contrôleur de domaine Active Directory réplica dans Azure](active-directory-install-replica-active-directory-domain-controller.md). Même si vous recourez à cette fonctionnalité, il peut être plus efficace de créer le contrôleur de domaine en local et de déplacer l’intégralité du disque dur virtuel dans le cloud plutôt que de répliquer le Windows Server AD DS lors de l’installation. Pour des raisons de sécurité, nous vous recommandons de supprimer le disque dur virtuel du réseau local une fois qu’il a été copié dans Azure.
* [Topologie du site Windows Server Active Directory](#BKMK_ADSiteTopology): créez un nouveau site Azure dans les sites et services Active Directory. Créez un objet de sous-réseau de Windows Server Active Directory pour représenter le réseau virtuel Azure, puis ajoutez le sous-réseau au site. Créez un nouveau lien de sites comprenant le nouveau site Azure et le site dans lequel le point de terminaison VPN du réseau virtuel Azure se trouve pour contrôler et optimiser le trafic Windows Server Active Directory depuis et vers Azure.
* [Adressage IP et DNS](#BKMK_IPAddressDNS):
  
  * Définissez une adresse IP statique pour le contrôleur de domaine à l’aide de l’applet de commande Set-AzureStaticVNetIP Azure PowerShell.
  * Installez et configurez le serveur DNS Windows Server sur les contrôleurs de domaine sur Azure.
  * Configurez les propriétés du réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de contrôleur de domaine et de serveur DNS.
* [Contrôleurs de domaine géolocalisés](#BKMK_DistributedDCs): configurez les réseaux virtuels supplémentaires si nécessaire. Si votre topologie de site Active Directory nécessite des contrôleurs de domaine dans des zones géographiques qui correspondent à différentes régions Azure, vous devez alors créer des sites Active Directory en conséquence.
* [Contrôleurs de domaine en lecture seule](#BKMK_RODC): vous pouvez déployer un contrôleur de domaine en lecture seule dans le site Azure selon vos conditions requises pour effectuer des opérations d’écriture sur le contrôleur de domaine et la compatibilité des applications et services dans le site avec les contrôleurs de domaine en lecture seule. Pour plus d’informations sur la compatibilité des applications, consultez le [guide de compatibilité des applications de contrôleurs de domaine en lecture seule](https://technet.microsoft.com/library/cc755190).
* [Catalogue global](#BKMK_GC): des catalogues globaux sont nécessaires pour traiter les demandes de connexion dans les forêts avec plusieurs domaines. Si vous ne déployez pas de catalogue global dans le site Azure, des coûts relatifs au trafic sortant vous seront facturés, puisque les demandes d’authentification entraînent des requêtes au niveau des catalogues globaux sur d’autres sites. Pour réduire ce trafic, vous pouvez activer la mise en cache de l’appartenance au groupe universel pour le site Azure dans les sites et services Active Directory.
  
    Si vous déployez un catalogue global, configurez les liens de sites et les coûts de liens de sites afin que le catalogue global dans le site Azure ne soit pas utilisé comme source du contrôleur de domaine par d’autres catalogues globaux qui doivent répliquer les mêmes partitions partielles de domaine.
* [Emplacement de la base de données Windows Server AD DS et SYSVOL](#BKMK_PlaceDB): ajoutez un disque de données aux contrôleurs de domaine exécutés sur des machines virtuelles Azure afin de stocker la base de données Windows Server Active Directory, les journaux et SYSVOL.
* [Sauvegarde et restauration](#BKMK_BUR): déterminez l’endroit où vous souhaitez stocker les sauvegardes de l’état du système. Si nécessaire, ajoutez un autre disque de données à la machine virtuelle du contrôleur de domaine pour stocker les sauvegardes.

## <a name="deployment-decisions-and-factors"></a>Facteurs et décisions relatifs au déploiement
Le tableau suivant résume les domaines technologiques de Windows Server Active Directory qui sont affectés dans les scénarios précédents et les décisions correspondantes à prendre en compte, avec des liens pour plus d’informations. Certains domaines technologiques peuvent ne pas être applicables à tous les scénarios de déploiement, et certains domaines technologiques peuvent être plus importants pour un scénario de déploiement que les autres domaines technologiques.

Par exemple, si vous déployez un contrôleur de domaine réplica dans un réseau virtuel et que votre forêt ne dispose que d’un seul domaine, alors choisir de déployer un serveur de catalogue global n’affectera pas le scénario de déploiement, étant donné qu’aucune exigence de réplication supplémentaire ne sera générée. En revanche, si la forêt possède plusieurs domaines, alors la décision de déployer un catalogue global sur un réseau virtuel peut affecter la bande passante disponible, les performances, l’authentification, les recherches dans le référentiel, etc.

| Domaine technologique Windows Server Active Directory | Décisions | Facteurs |
| --- | --- | --- |
| [Topologie du réseau](#BKMK_NetworkTopology) |Devez-vous créer un réseau virtuel ? |<li>Configuration requise pour accéder aux ressources d’entreprise</li> <li>Authentification</li> <li>Account management</li> |
| [Configuration du déploiement du contrôleur de domaine](#BKMK_DeploymentConfig) |<li>Déployer une forêt distincte sans aucune approbation ?</li> <li>Déployer une nouvelle forêt avec fédération ?</li> <li>Déployer une nouvelle forêt avec approbation de forêt Windows Server Active Directory ou Kerberos ?</li> <li>Étendre la forêt d’entreprise en déployant un contrôleur de domaine réplica ?</li> <li>Étendre la forêt d’entreprise en déployant un nouveau domaine enfant ou une arborescence de domaine ?</li> |<li>Sécurité</li> <li>Conformité</li> <li>Coût</li> <li>Résilience et tolérance de pannes</li> <li>Compatibilité des applications</li> |
| [Topologie du site Windows Server Active Directory](#BKMK_ADSiteTopology) |Comment devez-vous configurer les liens de sites, sites et sous-réseaux avec le réseau virtuel Azure pour optimiser le trafic et réduire les coûts ? |<li>Définitions de site et de sous-réseau</li> <li>Propriétés du lien de site et notification de modification</li> <li>Compression de la réplication</li> |
| [Adressage IP et DNS](#BKMK_IPAddressDNS) |Comment configurer les adresses IP et la résolution de noms ? |<li>Utilisez la cmdlet Set-AzureStaticVNetIP pour attribuer une adresse IP statique</li> <li>Installez le serveur DNS Windows Server et configurez les propriétés du réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de contrôleur de domaine et de serveur DNS</li> |
| [Contrôleurs de domaine géolocalisés](#BKMK_DistributedDCs) |Comment répliquer les contrôleurs de domaine sur des réseaux virtuels distincts ? |Si votre topologie de site Active Directory nécessite des contrôleurs de domaine dans des zones géographiques qui correspondent à différentes régions Azure, vous devez alors créer des sites Active Directory en conséquence. [Configurez le réseau virtuel pour la connectivité du réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) , afin d’assurer la réplication entre les contrôleurs de domaine sur des réseaux virtuels distincts. |
| [Contrôleurs de domaine en lecture seule](#BKMK_RODC) |Utilisez des contrôleurs de domaine en lecture seule ou en écriture ? |<li>Filtrage des attributs HBI/PII</li> <li>Filtrage des secrets</li> <li>Limiter le trafic sortant</li> |
| [Catalogue global](#BKMK_GC) |Installer un catalogue global ? |<li>Pour une forêt à un seul domaine, configurez tous les contrôleurs de domaine en tant que catalogues globaux</li> <li>Pour une forêt à plusieurs domaines, les catalogues globaux sont requis pour l’authentification</li> |
| [Méthode d’installation](#BKMK_InstallMethod) |Comment installer un contrôleur de domaine dans Azure ? |Au choix :  <li>Installation d’AD DS à l’aide de Windows PowerShell ou Dcpromo</li> <li>Déplacer le disque dur virtuel d’un contrôleur de domaine virtuel local</li> |
| [Emplacement de la base de données Windows Server AD DS et de SYSVOL](#BKMK_PlaceDB) |Où placer la base de données Windows Server AD DS, les journaux et SYSVOL ? |Modifier les valeurs par défaut de Dcpromo.exe Ces fichiers Active Directory critiques *doivent* être placés sur des disques de données Azure plutôt que sur des disques de système d’exploitation qui implémentent le cache d’écriture. |
| [Sauvegarde et restauration](#BKMK_BUR) |Comment sauvegarder et récupérer des données ? |Créer des sauvegardes d’états du système |
| [Configuration du serveur de fédération](#BKMK_FedSrvConfig) |<li>Déployer une nouvelle forêt avec fédération dans le cloud ?</li> <li>Déployer AD FS en local et exposer un proxy dans le cloud ?</li> |<li>Sécurité</li> <li>Conformité</li> <li>Coût</li> <li>Accès aux applications par des partenaires commerciaux</li> |
| [Configuration des services cloud](#BKMK_CloudSvcConfig) |Un service cloud est déployé implicitement la première fois que vous créez une machine virtuelle. Devez-vous déployer des services cloud supplémentaires ? |<li>Une ou plusieurs machines virtuelles doivent-elles être directement exposées à Internet ?</li> <li> Le service requiert-il un équilibrage de charge ?</li> |
| [Configuration requise du serveur de fédération pour l’adressage IP privé et public (adresse IP dynamique et adresse IP virtuelle)](#BKMK_FedReqVIPDIP) |<li>L’instance Windows Server AD FS doit-elle être accessible directement depuis Internet ?</li> <li>L’application déployée dans le cloud doit-elle avoir ses propres port et adresse IP accessibles sur Internet ?</li> |Créer un service cloud pour chaque adresse IP virtuelle requise par votre déploiement |
| [Configuration de la haute disponibilité de Windows Server AD FS](#BKMK_ADFSHighAvail) |<li>Combien y a-t-il de nœuds dans ma batterie de serveurs Windows Server AD FS ?</li> <li>Combien y a-t-il de nœuds à déployer dans ma batterie de serveurs proxy Windows Server AD FS ?</li> |Résilience et tolérance de pannes |

### <a name="BKMK_NetworkTopology"></a>Topologie du réseau
Pour respecter les exigences de cohérence des adresses IP et de DNS de Windows Server AD DS, vous devez d’abord créer un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) auquel vous attacherez vos machines virtuelles. Lors de sa création, vous devez décider d’étendre ou non la connectivité à votre réseau d’entreprise local, qui connecte les machines virtuelles Azure de façon transparente à des ordinateurs locaux. Cette opération est effectuée à l’aide de technologies VPN classiques et nécessite l’exposition d’un point de terminaison VPN sur le périmètre du réseau d’entreprise. Autrement dit, le VPN est lancé à partir d’Azure sur le réseau d’entreprise, pas l’inverse.

Notez que des frais supplémentaires s’appliquent lors de l’extension d’un réseau virtuel sur votre réseau local, en plus des frais standard appliqués pour chaque machine virtuelle. Plus précisément, il existe des frais pour le temps processeur de la passerelle de réseau virtuel Azure et le trafic sortant généré par chaque machine virtuelle communiquant avec des ordinateurs locaux sur le VPN. Pour plus d’informations sur les frais de trafic réseau, voir [Aperçu rapide de la tarification Azure](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuration du déploiement du contrôleur de domaine
La manière dont vous configurez le contrôleur de domaine dépend de la configuration du service que vous souhaitez exécuter sur Azure. Par exemple, vous pouvez déployer une nouvelle forêt isolée de votre propre forêt d’entreprise pour tester une preuve de concept, une nouvelle application ou un autre projet à court terme qui nécessite des services d’annuaire, mais pas un accès spécifique aux ressources d’entreprise internes.

L’avantage est qu’un contrôleur de domaine de forêt isolée ne peut pas être répliqué avec des contrôleurs de domaine locaux, ce qui diminue le trafic réseau sortant généré par le système lui-même, réduisant directement les coûts. Pour plus d’informations sur les frais de trafic réseau, voir [Aperçu rapide de la tarification Azure](http://azure.microsoft.com/pricing/).

Prenons un autre exemple : supposons qu’un service exige la confidentialité, mais qu’il dépend de l’accès à Windows Server Active Directory en interne. Si vous êtes autorisé à héberger des données pour le service dans le cloud, vous pouvez déployer un nouveau domaine enfant pour votre forêt interne sur Azure. Dans ce cas, vous pouvez déployer un contrôleur de domaine pour le nouveau domaine enfant (sans le catalogue global, pour faciliter la résolution des problèmes de confidentialité). Ce scénario, combiné au déploiement d’un contrôleur de domaine réplica, nécessite un réseau virtuel pour la connectivité avec vos contrôleurs de domaine locaux.

Si vous créez une nouvelle forêt, choisissez s’il faut utiliser les [approbations Active Directory](https://technet.microsoft.com/library/cc771397) ou les [approbations de fédération](https://technet.microsoft.com/library/dd807036). Équilibrez les exigences en matière de compatibilité, de sécurité, de conformité, de coût et de résilience. Par exemple, pour tirer parti de [l’authentification sélective](https://technet.microsoft.com/library/cc755844) , vous pouvez choisir de déployer une nouvelle forêt sur Azure et de créer une approbation Windows Server Active Directory entre la forêt locale et la forêt du cloud. Toutefois, si l’application prend en charge les revendications, vous pouvez déployer des approbations de fédération plutôt que des approbations de forêt Active Directory. Un autre facteur est à prendre en compte : le coût de la réplication d’un plus grand nombre de données si vous étendez votre Windows Server Active Directory local dans le cloud, par rapport au coût de la génération d’un trafic sortant supplémentaire lié à la charge d’authentifications et de requêtes.

Les exigences de disponibilité et de tolérance de pannes ont également une répercussion sur votre choix. Par exemple, si le lien est interrompu, les applications exploitant une approbation Kerberos ou une approbation de fédération risquent d’être totalement isolées, sauf si vous avez déployé une infrastructure suffisante sur Azure. Les autres configurations de déploiement telles que les contrôleurs de domaine réplica (accessibles en écriture ou en lecture seule) augmentent la probabilité de tolérance aux pannes de liens.

### <a name="BKMK_ADSiteTopology"></a>Topologie du site Windows Server Active Directory
Vous devez définir correctement les sites et les liens des sites afin d’optimiser le trafic et de réduire les coûts. Les sites, les liens des sites et les sous-réseaux affectent la topologie de réplication entre les contrôleurs de domaine et le flux du trafic d’authentification. Tenez compte des frais de trafic suivants, puis déployez et configurez des contrôleurs de domaine selon les besoins de votre scénario de déploiement :

* La passerelle elle-même implique des frais nominaux par heure :
  
  * Elle peut être démarrée et arrêtée selon vos besoins.
  * Si elle est arrêtée, les machines virtuelles Azure sont isolées du réseau d’entreprise.
* Le trafic entrant est gratuit.
* Le trafic sortant est facturé, conformément à [l’Aperçu rapide de la tarification Azure](http://azure.microsoft.com/pricing/). Vous pouvez optimiser les propriétés des liens de sites entre les sites locaux et les sites dans le cloud comme suit :
  
  * Si vous utilisez plusieurs réseaux virtuels, configurez les liens de sites et leurs coûts de manière à empêcher Windows Server AD DS de donner la priorité au site Azure plutôt qu’à un autre capable de fournir les mêmes niveaux de service sans frais. Vous pouvez également envisager de désactiver l’option Relier tous les liens de sites (qui est activée par défaut). Cela garantit que seuls les sites connectés directement sont répliqués avec un autre. Les contrôleurs de domaine des sites connectés transitivement ne peuvent plus être répliqués directement entre eux, mais doivent être répliqués via un ou plusieurs sites communs. Si les sites intermédiaires deviennent indisponibles pour une raison quelconque, la réplication entre les contrôleurs de domaine sur les sites connectés transitivement n’a pas lieu même si la connectivité entre les sites est disponible. Enfin, chaque fois que des sections de comportement de réplication transitive restent souhaitables, créez des ponts de liaison de sites appropriés contenant des liens de sites et des sites, par exemple des sites locaux et des sites de réseau d’entreprise.
  * [Configurez les coûts des liens des sites](https://technet.microsoft.com/library/cc794882) de manière à éviter le trafic involontaire. Par exemple, si le réglage **Essayer le site le plus proche suivant** est activé, vérifiez que les sites de réseau virtuel ne sont pas les sites les plus proches suivants en augmentant le coût associé de l’objet lien de sites qui connecte le site Azure au réseau d’entreprise.
  * Configurez les [intervalles](https://technet.microsoft.com/library/cc794878) et les [planifications](https://technet.microsoft.com/library/cc816906) de liens de sites en fonction des exigences de cohérence et du taux de modifications d’objet. Alignez la planification des réplications avec la tolérance de latence. Les contrôleurs de domaine répliquent uniquement le dernier état d’une valeur, si bien que la diminution de l’intervalle de réplication peut réduire les coûts si le taux de modification d’objet est suffisant.
* Si la réduction des coûts est une priorité, vérifiez que la réplication est planifiée et que la notification de modification n’est pas activée. Il s’agit de la configuration par défaut lors de la réplication entre les sites. Cela n’est pas important si vous déployez un contrôleur de domaine en lecture seule sur un réseau virtuel car ce type de contrôleur de domaine ne réplique aucune modification sortante. Par contre, si vous déployez un contrôleur de domaine accessible en écriture, vous devez vous assurer que le lien de sites n’est pas configuré pour répliquer les mises à jour à une fréquence inutile. Si vous déployez un serveur de catalogue global (GC), assurez-vous que chaque autre site contenant un GC réplique les partitions de domaine à partir d’un contrôleur de domaine source dans un site connecté avec un lien de sites ou des liens de sites dont le coût est inférieur au GC du site Azure.
* Il est possible de réduire encore le trafic réseau généré par la réplication entre les sites en modifiant l’algorithme de compression de la réplication. L’algorithme de compression est contrôlé par l’algorithme de compression HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicatorde l’entrée de registre REG_DWORD. La valeur par défaut est 3, qui effectue la corrélation avec l’algorithme de compression Xpress. Vous pouvez remplacer la valeur par 2, ce qui modifie l’algorithme en MSZip. Dans la plupart des cas, la compression augmente, au détriment de l’utilisation du processeur. Pour plus d’informations, voir [How Active Directory replication topology works](https://technet.microsoft.com/library/cc755994)(Fonctionnement de la topologie de réplication Active Directory).

### <a name="BKMK_IPAddressDNS"></a>Adressage IP et DNS
Par défaut, des « adresses avec bail DHCP » sont allouées aux machines virtuelles Azure. Dans la mesure où les adresses dynamiques de réseau virtuel Azure persistent avec la machine virtuelle pendant toute sa durée de vie, les exigences de Windows Server AD DS sont satisfaites.

En conséquence, lorsque vous utilisez une adresse dynamique sur Azure, vous utilisez en fait une adresse IP statique car elle est routable pendant la durée du bail, et la durée du bail est égale à la durée de vie du service cloud.

Toutefois, l’adresse dynamique est désallouée si la machine virtuelle est arrêtée. Pour éviter que l’adresse IP soit désallouée, vous pouvez [utiliser Set-AzureStaticVNetIP pour attribuer une adresse IP statique](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Pour la résolution de noms, déployez votre propre infrastructure de serveur DNS (ou exploitez celle qui existe déjà) ; le DNS fourni par Azure ne satisfait pas les besoins avancés de résolution de nom de Windows Server AD DS. Par exemple, il ne prend pas en charge les enregistrements SRV dynamiques, et ainsi de suite. La résolution de noms est un élément de configuration critique pour les contrôleurs de domaine et les clients joints au domaine. Les contrôleurs de domaine doivent être capables d’inscrire des enregistrements de ressource et de résoudre d’autres enregistrements de ressource de contrôleurs de domaine.
Pour des raisons de performances et de tolérance de pannes, il est souhaitable d’installer le service DNS de Windows Server sur les contrôleurs de domaine qui s’exécutent sur Azure. Ensuite, configurez les propriétés du réseau virtuel Azure avec le nom et l’adresse IP du serveur DNS. Lorsque d’autres machines virtuelles démarrent sur le réseau virtuel, leurs paramètres de résolution client DNS sont configurés avec le serveur DNS dans le cadre de l’allocation d’adresses IP dynamique.

> [!NOTE]
> Vous ne pouvez pas joindre des ordinateurs locaux à un domaine Active Directory Windows Server AD DS hébergé sur Azure directement via Internet. La configuration requise pour les ports d’Active Directory et l’opération de rattachement au domaine ne permettent pas d’exposer directement et facilement les ports nécessaires, qui plus est un contrôleur de domaine complet, à Internet.
> 
> 

Les machines virtuelles inscrivent leur nom DNS automatiquement au démarrage ou lors d’une modification de nom.

Pour plus d’informations sur cet exemple et un autre exemple qui montre comment configurer la première machine virtuelle et installer AD DS dessus, voir [Installation d’une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md). Pour plus d’informations sur l’utilisation de Windows PowerShell, voir [Installer Azure PowerShell](/powershell/azureps-cmdlets-docs) et [Azure Management Cmdlet](/powershell/module/azurerm.compute/#virtual_machines) (Applets de commande de gestion Azure).

### <a name="BKMK_DistributedDCs"></a>Contrôleurs de domaine géolocalisés
Azure présente des avantages lors de l’hébergement de plusieurs contrôleurs de domaine sur différents réseaux virtuels :

* Tolérance de pannes sur plusieurs sites
* Proximité physique des filiales (latence réduite)

Pour plus d’informations sur la configuration d’une communication directe entre les réseaux virtuels, voir [Configurer une connexion de réseau virtuel à réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Contrôleurs de domaine en lecture seule
Vous devez choisir entre le déploiement de contrôleurs de domaine accessibles en lecture seule ou en écriture. Vous pouvez être tenté de déployer des contrôleurs de domaine en lecture seule parce que cela vous éviter d’avoir à les contrôler physiquement. Or, ces contrôleurs sont conçus pour être déployés dans des sites où ils sont exposés aux risques, par exemple les filiales.

Azure ne présente pas le risque de sécurité physique d’une filiale, mais les contrôleurs de domaine en lecture seule peuvent tout de même s’avérer plus efficaces, car les fonctionnalités qu’ils fournissent sont bien adaptées à ces environnements, quoique pour des raisons très différentes. Par exemple, les contrôleurs de domaine en lecture seule ne génèrent pas de réplication sortante et peuvent renseigner sélectivement des secrets (mots de passe). En revanche, si de tels secrets ne sont pas connus, le trafic sortant à la demande peut avoir à les valider chaque fois qu’un utilisateur ou un ordinateur s’authentifie. Toutefois, il est possible de pré-renseigner et mettre en cache les secrets de manière sélective.

Les contrôleurs de domaine accessibles en lecture seule fournissent un avantage supplémentaire pour gérer les problèmes de données HBI et PII et associés, car vous pouvez ajouter des attributs qui contiennent des données sensibles au jeu d’attributs filtré du contrôleur de domaine accessible en lecture seule. Le jeu d’attributs filtré comprend des attributs personnalisables qui ne sont pas répliqués vers le contrôleur de domaine accessible en lecture seule. Vous pouvez utiliser le jeu d’attributs filtré comme solution de secours si vous n’êtes pas autorisé à stocker les attributs PII ou HBI sur Azure, ou si vous ne voulez pas le faire. Pour plus d’informations, voir [RODC filtered attribute set (jeu d’attributs filtré du contrôleur de domaine accessible en lecture seule)[(https://technet.microsoft.com/library/cc753459)].

Assurez-vous que les applications sont compatibles avec les contrôleurs que vous prévoyez d’utiliser. De nombreuses applications compatibles avec Windows Server Active Directory fonctionnent bien avec les contrôleurs de domaine en lecture seule, mais certaines peuvent être inefficaces ou échouer si elles n’ont pas accès à un contrôleur de domaine accessible en écriture. Pour plus d’informations, voir [Compatibilité des applications avec les contrôleurs de domaine en lecture seule](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catalogue global
Vous devez choisir d’installer ou non un catalogue global (GC). Dans une forêt de domaine unique, vous devez configurer tous les contrôleurs de domaine comme des serveurs de catalogue global. Cela n’augmente pas les coûts car aucun trafic de réplication supplémentaire n’est généré.

Dans une forêt multidomaine, les catalogues globaux sont nécessaires pour développer des appartenances au groupe universel pendant le processus d’authentification. Si vous ne déployez pas un catalogue global, les charges de travail sur le réseau virtuel authentifiées auprès d’un contrôleur de domaine sur Azure génèrent indirectement un trafic d’authentification sortant pour interroger les catalogues globaux locaux lors de chaque tentative d’authentification.

Les coûts associés aux catalogues globaux sont moins prévisibles, car ils hébergent chaque domaine (en partie). Si la charge de travail héberge un service accessible sur Internet et authentifie les utilisateurs via Windows Server AD DS, les coûts peuvent être totalement imprévisibles. Pour réduire les requêtes de catalogue global en dehors du site du cloud pendant l’authentification, vous pouvez [activer la mise en cache d’appartenance au groupe universel](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Méthode d’installation
Vous devez choisir comment installer les contrôleurs de domaine sur le réseau virtuel :

* Promouvoir de nouveaux contrôleurs de domaine. Pour plus d’informations, voir [Installation d’une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md).
* Déplacer le disque dur virtuel d’un contrôleur de domaine virtuel local vers le cloud. Dans ce cas, vous devez vous assurer que le contrôleur de domaine virtuel local est « déplacé », pas « copié » ou « cloné ».

Utilisez uniquement des machines virtuelles Azure pour les contrôleurs de domaine (par opposition aux machines virtuelles Azure ayant un rôle « Web » ou « travail »). Elles sont durables et la durabilité de l’état d’un contrôleur de domaine est essentielle. Les machines virtuelles Azure sont conçues pour des charges de travail telles que des contrôleurs de domaine.

N’utilisez pas SYSPREP pour déployer ou cloner des contrôleurs de domaine. La capacité de clonage des contrôleurs de domaine est uniquement disponible à partir de Windows Server 2012. La fonctionnalité de clonage nécessite la prise en charge de VMGenerationID dans l’hyperviseur sous-jacent. Hyper-V dans Windows Server 2012 et les réseaux virtuels Azure prennent tous deux en charge VMGenerationID, tout comme les fournisseurs de logiciels de virtualisation tiers.

### <a name="BKMK_PlaceDB"></a>Emplacement de la base de données Windows Server AD DS et de SYSVOL
Choisissez où placer la base de données Windows Server AD DS, les journaux et SYSVOL. Ils doivent être déployés sur des disques de données Azure.

> [!NOTE]
> Les disques de données Azure sont limités à 4 To.
> 
> 

Par défaut, les disques de données ne mettent pas les écritures en cache. Les disques de données qui sont attachés à une machine virtuelle utilisent le cache à double écriture. Le cache à double écriture garantit que l’écriture est validée dans un stockage Azure durable avant la fin de la transaction du point de vue du système d’exploitation de la machine virtuelle. La durabilité est ainsi assurée, même si les écritures sont légèrement plus lentes.

Pour Windows Server AD DS, cela est important car le cache d’écriture différée rend les hypothèses émises par le contrôleur de domaine non valides. Windows Server AD DS tente de désactiver le cache d’écriture, mais c’est le système d’E/S du disque qui valide. L’incapacité de désactiver le cache d’écriture peut, dans certaines circonstances, entraîner la restauration d’USN et produire des objets en attente et d’autres problèmes.

Pour les contrôleurs de domaine virtuels, nous vous recommandons la bonne pratique suivante :

* Sur le disque de données Azure, définissez le paramètre Préférences de cache d’hôte sur AUCUN. Cela évite les problèmes de cache d’écriture pour les opérations AD DS.
* Stockez la base de données, les journaux et SYSVOL sur le même disque de données ou sur des disques de données distincts. Généralement, il s’agit d’un disque distinct du disque utilisé pour le système d’exploitation. L’essentiel à retenir est que la base de données Windows Server AD DS et SYSVOL ne doivent pas être stockés sur un type de disque Système d’exploitation Azure. Par défaut, le processus d’installation AD DS installe ces composants dans le dossier %systemroot%, qui n’est PAS recommandé pour Azure.

### <a name="BKMK_BUR"></a>Sauvegarde et restauration
Soyez attentif à ce qui est pris en charge ou non dans le cadre de la sauvegarde et de la restauration des contrôleurs de domaine, en particulier ceux qui sont exécutés sur une machine virtuelle. Voir [Observations sur la sauvegarde et la restauration des contrôleurs de domaine virtualisés](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Créez des sauvegardes d’état en utilisant seulement des logiciels de sauvegarde compatibles avec les exigences de sauvegarde de Windows Server AD DS, telles que Sauvegarde Windows Server.

Ne copiez ou ne clonez pas les fichiers des disques durs virtuels des contrôleurs de domaine au lieu d’effectuer des sauvegardes régulières. Si une restauration doit être effectuée, vous introduirez des bulles USN en utilisant pour cela des disque durs virtuels clonés ou copiés sans Windows Server 2012 et un hyperviseur pris en charge.

### <a name="BKMK_FedSrvConfig"></a>Configuration du serveur de fédération
La configuration des serveurs de fédération (STS) Windows Server AD FS dépend en partie de la nécessité pour les applications déployées sur Azure d’accéder aux ressources de votre réseau local.

Si les applications répondent aux critères suivants, vous pouvez déployer les applications de manière isolée à partir de votre réseau local.

* Elles acceptent les jetons de sécurité SAML.
* Elles peuvent être exposées sur Internet.
* Elles n’accèdent pas aux ressources locales.

Dans ce cas, configurez les STS Windows Server AD FS comme suit :

1. Configurez une forêt à domaine unique isolée sur Azure.
2. Fournissez un accès fédéré à la forêt en configurant une batterie de serveurs de fédération Windows Server AD FS.
3. Configurez Windows Server AD FS (batterie de serveurs de fédération et batterie de serveurs proxy de fédération) dans la forêt locale.
4. Établissez une relation d’approbation de fédération entre les instances locales et Azure de Windows Server AD FS.

En revanche, si les applications nécessitent un accès aux ressources locales, vous pouvez configurer Windows Server AD FS avec l’application sur Azure comme suit :

1. Configurez la connectivité entre les réseaux locaux et Azure.
2. Configurez une batterie de serveurs de fédération Windows Server AD FS dans la forêt locale.
3. Configurez une batterie de serveurs proxy de fédération Windows Server AD FS sur Azure.

Cette configuration présente l’avantage de réduire l’exposition des ressources locales, comme la configuration de Windows Server AD FS avec des applications dans un réseau de périmètre.

Notez que dans les deux cas, vous pouvez établir des relations d’approbation avec d’autres fournisseurs d’identité, si une collaboration d’entreprise à entreprise est requise.

### <a name="BKMK_CloudSvcConfig"></a>Configuration des services cloud
Les services cloud sont nécessaires si vous souhaitez exposer une machine virtuelle directement à Internet ou exposer une application d’équilibrage de charge accessible sur Internet. Cela est possible car chaque service cloud propose une seule adresse IP virtuelle configurable.

### <a name="BKMK_FedReqVIPDIP"></a>Configuration requise du serveur de fédération pour l’adressage IP privé et public (adresse IP dynamique et adresse IP virtuelle)
Chaque machine virtuelle Azure reçoit une adresse IP dynamique. Une adresse IP dynamique est une adresse privée accessible uniquement dans Azure. Dans la plupart des cas, toutefois, il est nécessaire de configurer une adresse IP virtuelle pour vos déploiements Windows Server AD FS. L’adresse IP virtuelle est nécessaire pour exposer des points de terminaison Windows Server AD FS à Internet, et est utilisée par les clients et partenaires fédérés pour l’authentification et la gestion continue. Une adresse IP virtuelle est une propriété d’un service cloud qui contient une ou plusieurs machines virtuelles Azure. Si l’application prenant en charge les revendications déployée sur Azure et Windows Server AD FS sont accessibles sur Internet et partagent des ports communs, ils doivent avoir leur propre adresse IP virtuelle. Il est donc nécessaire de créer un service cloud pour l’application et un autre pour Windows Server AD FS.

Pour connaître les définitions des termes adresse IP virtuelle et adresse IP dynamique, voir [Termes et définitions](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuration de la haute disponibilité de Windows Server AD FS
Bien qu’il soit possible de déployer des services de fédération Windows Server AD FS autonomes, il est recommandé de déployer une batterie de serveurs avec au moins deux nœuds pour les STS et les serveurs proxy AD FS dans les environnements de production.

Voir [AD FS 2.0 deployment topology considerations](https://technet.microsoft.com/library/gg982489) (Observations concernant la topologie du déploiement AD FS 2.0) dans le [AD FS 2.0 Design Guide](https://technet.microsoft.com/library/dd807036) (Guide de conception AD FS 2.0) pour choisir les options de configuration du déploiement les plus adaptées à vos besoins particuliers.

> [!NOTE]
> Pour parvenir à l’équilibrage de charge des points de terminaison Windows Server AD FS sur Azure, configurez tous les membres de la batterie de serveurs Windows Server AD FS dans le même service cloud, et utilisez la fonction d’équilibrage de charge d’Azure pour les ports HTTP (80 par défaut) et HTTPS (443 par défaut). Pour plus d’informations, voir [Sonde d’équilibrage de charge Azure](https://msdn.microsoft.com/library/azure/jj151530).
> L’équilibrage de la charge réseau (NLB) de Windows Server n’est pas pris en charge sur Azure.
> 
> 

