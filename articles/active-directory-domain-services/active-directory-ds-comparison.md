---
title: 'Services de domaine Azure AD : comparer les services de domaine Azure AD aux contrôleurs de domaine personnalisés | Microsoft Docs'
description: Comparaison entre les services de domaine Azure AD et les contrôleurs de domaine personnalisés
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: c384046d280e03de5a808d245dd273fdf7b44549
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Comment déterminer si les services de domaine Azure AD sont adaptés à votre cas d’utilisation ?
Azure AD Domain Services vous permet de déployer vos charges de travail dans les services d’infrastructure Azure, sans avoir à vous soucier de maintenir votre infrastructure d’identité dans Azure. Ce service géré est différent d’un déploiement Windows Server Active Directory standard que vous déployez et gérez par vous-même. Le service est facile à déployer et assure une surveillance et une correction automatisées de l’intégrité. Le service est en constante évolution pour inclure la prise en charge de scénarios de déploiement courants.

Pour décider de l’opportunité d’utiliser Azure AD Domain Services, nous vous recommandons de lire la documentation suivante :

* Consultez la liste des [fonctionnalités offertes par les services de domaine Azure AD](active-directory-ds-features.md).
* Consultez les [scénarios de déploiement courants pour les services de domaine Azure AD](active-directory-ds-scenarios.md).
* Enfin, [comparez les services de domaine Azure AD à une option AD personnalisable](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparer les services de domaine Azure AD à un domaine AD personnalisé dans Azure
Le tableau suivant vous permet de choisir entre l’utilisation des services de domaine Azure AD et la gestion de votre propre infrastructure AD dans Azure.

| **Fonctionnalité** | **Services de domaine Azure AD** | **Infrastructure AD personnalisée sur des machines virtuelles Azure** |
| --- |:---:|:---:|
| [**Service géré**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&amp;#x2715;** |
| [**Déploiements sécurisés**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |L’administrateur doit sécuriser le déploiement. |
| [**Serveur DNS**](active-directory-ds-comparison.md#dns-server) |**&#x2713;** (service géré) |**&#x2713;** |
| [**Domain or Enterprise administrator privileges**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&amp;#x2715;** |**&#x2713;** |
| [**Jonction de domaine**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Authentification de domaine à l’aide de NTLM et Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Délégation Kerberos contrainte**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|basée sur la ressource|basée sur la ressource et basée sur le compte|
| [**Structure d’unité d’organisation personnalisée**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Extensions de schéma**](active-directory-ds-comparison.md#schema-extensions) |**&amp;#x2715;** |**&#x2713;** |
| [**Approbations de domaine/forêt AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&amp;#x2715;** |**&#x2713;** |
| [**LDAP read**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**LDAP sécurisé (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP write**](active-directory-ds-comparison.md#ldap-write) |**&amp;#x2715;** |**&#x2713;** |
| [**Group Policy**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Déploiements géolocalisés**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&amp;#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Service géré
Les domaines des services de domaine Azure AD sont gérés par Microsoft. Vous n’avez pas à vous soucier des correctifs, des mises à jour, de la surveillance, des sauvegardes et de la disponibilité de votre domaine. Ces tâches de gestion sont proposées en tant que service par Microsoft Azure pour vos domaines gérés.

#### <a name="secure-deployments"></a>Déploiements sécurisés
Le domaine managé est verrouillé de façon sécurisée, conformément aux recommandations de sécurité de Microsoft pour les déploiements AD. Ces recommandations sont issues des dizaines d’années d’expérience de l’équipe produit AD en matière d’ingénierie et de prise en charge des déploiements AD. Pour les déploiements personnalisés, vous devez prendre des mesures de déploiement spécifiques pour verrouiller/sécuriser votre déploiement.

#### <a name="dns-server"></a>Serveur DNS
Un domaine géré des services de domaine Azure AD inclut des services DNS gérés. Les membres du groupe « AAD DC Administrators » peuvent gérer DNS sur le domaine géré. Les membres de ce groupe bénéficient de privilèges d’administration DNS complets pour le domaine géré. La gestion du service DNS peut être effectuée à l’aide de la « console d’administration DNS » incluse dans le package des outils d’administration du serveur distant (RSAT).
[Plus d’informations](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilèges d’administrateur de domaine ou d’entreprise
Ces privilèges élevés ne sont pas proposés sur un domaine géré AAD-DS. Les applications qui nécessitent ces privilèges élevés ne peuvent pas être déployées vers des domaines managés AAD-DS. Un sous-ensemble plus restreint de privilèges d’administration est disponible pour les membres du groupe d’administration déléguée appelé « AAD DC Administrators ». Ces privilèges incluent des autorisations pour configurer DNS, la stratégie de groupe, obtenir des privilèges d’administrateur sur les ordinateurs joints au domaine, etc.

#### <a name="domain-join"></a>jonction de domaine
Vous pouvez joindre des machines virtuelles au domaine géré de la même manière que pour les ordinateurs joints à un domaine AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Authentification de domaine à l’aide de NTLM et Kerberos
Avec les services de domaine Azure AD, vous pouvez utiliser vos informations d’identification d’entreprise pour l’authentification sur le domaine géré. Les informations d’identification sont synchronisées avec votre client Azure AD. Pour les clients synchronisés, Azure AD Connect permet de s’assurer que les modifications apportées aux informations d’identification en local sont synchronisées avec Azure AD. Avec une configuration de domaine personnalisée, vous devrez peut-être configurer une approbation de domaine AD avec votre AD local pour permettre aux utilisateurs de s’authentifier avec leurs informations d’identification d’entreprise. Vous devrez peut-être également configurer la réplication AD pour vous assurer que les mots de passe des utilisateurs sont synchronisés avec les machines virtuelles du contrôleur de domaine Azure.

#### <a name="kerberos-constrained-delegation"></a>Délégation Kerberos contrainte
Sur un domaine managé Azure AD Domain Services, vous n’avez pas les privilèges « Administrateur de domaine ». Par conséquent, vous ne pouvez pas configurer la délégation Kerberos contrainte basée sur le compte (traditionnelle). Toutefois, vous pouvez configurer la délégation contrainte basée sur les ressources, qui est plus sécurisée.
[Plus d’informations](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Structure d’unité d’organisation personnalisée
Les membres du groupe « AAD DC Administrators » peuvent créer des unités d’organisation personnalisées dans le domaine géré. Les utilisateurs qui créent des UO personnalisées bénéficient de privilèges administratifs complets sur l’UO.
[Plus d’informations](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Extensions de schéma
Vous ne pouvez pas étendre le schéma de base d’un domaine géré des services de domaine Azure AD. Par conséquent, les applications qui reposent sur les extensions de schéma AD (par exemple, les nouveaux attributs dans l’objet utilisateur) ne peuvent pas déplacées vers des domaines AAD-DS.

#### <a name="ad-domain-or-forest-trusts"></a>Approbations de domaine ou de forêt AD
Les domaines gérés ne peuvent pas être configurés pour définir des relations d’approbation (entrant/sortant) avec d’autres domaines. Par conséquent, les scénarios de déploiement de forêt de ressources ne peuvent pas utiliser Azure AD Domain Services. De même, les déploiements où vous préférez ne pas synchroniser les mots de passe sur Azure AD ne peuvent pas utiliser Azure AD Domain Services.

#### <a name="ldap-read"></a>Lecture LDAP
Le domaine géré prend en charge les charges de travail de lecture LDAP. Vous pouvez donc déployer des applications qui effectuent des opérations de lecture LDAP sur le domaine géré.

#### <a name="secure-ldap"></a>LDAP sécurisé
Vous pouvez configurer les services de domaine Azure AD pour fournir un accès LDAP sécurisé à votre domaine géré, notamment sur Internet.
[Plus d’informations](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>Écriture LDAP
Le domaine géré est en lecture seule pour les objets utilisateur. Par conséquent, les applications qui effectuent des opérations d’écriture LDAP sur les attributs de l’objet utilisateur ne fonctionnent pas dans un domaine géré. En outre, les mots de passe utilisateur ne peuvent pas être modifiés à partir du domaine géré. Un autre exemple : la modification des appartenances aux groupes ou des attributs de groupe dans le domaine géré n’est pas autorisée. Toutefois, les modifications apportées aux mots de passe ou aux attributs utilisateur dans Azure AD (via PowerShell/portail Azure) ou dans Active Directory sur site sont synchronisées avec le domaine géré AAD-DS.

#### <a name="group-policy"></a>Stratégie de groupe
Il existe un objet GPO intégré pour les conteneurs « AADDC Computers » et « AADDC Users ». Vous pouvez personnaliser ces GPO intégrés pour configurer une stratégie de groupe. Les membres du groupe « Administrateurs AAD DC » peuvent également créer des GPO personnalisés et les lier à des UO existantes (notamment les UO personnalisées).
[Plus d’informations](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Déploiements géographiquement dispersés
Les domaines gérés des services de domaine Azure AD sont disponibles dans un seul réseau virtuel dans Azure. Pour les scénarios qui requièrent que des contrôleurs de domaine soient disponibles dans plusieurs régions Azure dans le monde, la configuration de contrôleurs de domaine sur des machines virtuelles IaaS Azure peut constituer la meilleure solution.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Options de déploiement AD personnalisé
Vous disposez peut-être de cas d’utilisation de déploiement où vous avez besoin de certaines des fonctionnalités offertes par une installation Windows Server AD. Dans ces cas, envisagez l’une des options personnalisées suivantes :

* **Domaine cloud autonome :** vous pouvez configurer un domaine cloud autonome à l’aide de machines virtuelles Azure qui ont été configurées en tant que contrôleurs de domaine. Cette infrastructure ne s’intègre pas à votre environnement AD local. Cette option nécessite un ensemble différent d’informations d’identification cloud pour l’ouverture de session/la gestion des machines virtuelles sur le cloud.
* **Déploiement de forêt de ressources :** vous pouvez configurer un domaine dans la topologie de forêt de ressources à l’aide de machines virtuelles Azure configurées en tant que contrôleurs de domaine. Ensuite, vous pouvez configurer une relation d’approbation AD avec votre environnement AD local. Vous pouvez joindre au domaine des ordinateurs (machines virtuelles Azure) pour cette forêt de ressources dans le cloud. L’authentification de l’utilisateur a lieu sur une connexion VPN/ExpressRoute à votre répertoire local.
* **Étendre votre domaine local à Azure :** vous pouvez connecter un réseau virtuel Azure à votre réseau local à l’aide d’une connexion VPN/ExpressRoute. Cette configuration permet de joindre des machines virtuelles Azure à votre AD local. Une autre solution consiste à promouvoir des contrôleurs de domaine répliqués de votre domaine local dans Azure en tant que machine virtuelle. Vous pouvez alors configurer celle-ci pour la réplication via une connexion VPN/ExpressRoute à votre répertoire local. Ce mode de déploiement étend efficacement votre domaine local à Azure.

> [!NOTE]
> Vous pouvez décider qu’une option personnalisée est mieux adaptée à vos cas d’utilisation de déploiement. Pensez à [partager vos commentaires](active-directory-ds-contact-us.md) pour nous aider à comprendre quelles fonctionnalités vous aideraient à choisir les services de domaine Azure AD à l’avenir. Ces commentaires nous permettent de faire évoluer le service pour mieux répondre à vos besoins et cas d’utilisation de déploiement.
>
>

Nous avons publié des [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Windows Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) pour faciliter les installations personnalisées.

## <a name="related-content"></a>Contenu connexe
* [Fonctionnalités - Services de domaine Azure AD](active-directory-ds-features.md)
* [Scénarios de déploiement - Services de domaine Azure AD](active-directory-ds-scenarios.md)
* [Instructions pour le déploiement de Windows Server Active Directory sur Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)
