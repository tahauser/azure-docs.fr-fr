---
title: "Présentation des services de domaine Azure Active Directory | Microsoft Docs"
description: "Présentation des services de domaine Azure Active Directory"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 2c00d324adfc1b71d436cb017214af98ff3002cf
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="azure-active-directory-ad-domain-services"></a>Services de domaine Azure Active Directory (AD)
## <a name="overview"></a>Vue d'ensemble
Azure Infrastructure Services permet de déployer une large gamme de solutions informatiques de manière agile. Azure Virtual Machines permet un déploiement quasi instantané, et un paiement à la minute. Grâce à la prise en charge de Windows, Linux, SQL Server, Oracle, IBM, SAP et BizTalk, vous pouvez déployer n’importe quelle charge de travail et toutes les langues sur quasiment tous les systèmes d’exploitation. Ces avantages permettent de migrer des applications héritées déployées sur site vers Azure, tout en faisant des économies sur les dépenses d’exploitation.

Un aspect clé de la migration d’applications sur site vers Azure consiste à gérer les aspects liés à l’identité de ces applications. Les applications orientées répertoire peuvent s’appuyer sur le protocole LDAP pour obtenir un accès en lecture ou en écriture à l’annuaire d’entreprise, ou sur l’authentification intégrée Windows (authentification Kerberos ou NTLM) pour authentifier les utilisateurs finaux. Les applications métier (Line-of-business, LOB) s’exécutant sous Windows Server sont généralement déployées sur les ordinateurs appartenant au domaine, et peuvent être gérées en toute sécurité grâce à la stratégie de groupe. Pour « transférer » les applications de site vers le cloud, les dépendances par rapport à l’infrastructure d’identité doivent être résolues.

Pour répondre aux besoins liés à l’identité de leurs applications déployées dans Azure, les administrateurs adoptent souvent l’une des solutions suivantes :

* le déploiement d’une connexion VPN de site à site entre charges de travail en cours d’exécution dans les services d’infrastructure Azure et l’annuaire d’entreprise sur site.
* l’extension de l’infrastructure de domaine/forêt Active Directory d’entreprise par le biais de la configuration de contrôleurs de domaine de réplica à l’aide de machines virtuelles.
* le déploiement d’un domaine autonome dans Azure à l’aide de contrôleurs de domaine déployés en tant que machines virtuelles Azure.

Toutes ces approches pèchent par leur coût élevé et la surcharge administrative. Les administrateurs doivent déployer des contrôleurs de domaine en utilisant des machines virtuelles dans Azure. De plus, ils doivent gérer, assurer la sécurité, appliquer des correctifs, surveiller, sauvegarder et dépanner ces machines virtuelles. La dépendance vis-à-vis des connexions VPN dans l’annuaire sur site expose les charges de travail déployées dans Azure aux problèmes ou pannes réseau temporaires, qui réduisent les temps d’activité et affectent quelque peu la fiabilité de ces applications.

Nous avons conçu les Services de domaine Active Directory Azure pour offrir une alternative plus simple.

### <a name="watch-an-introductory-video"></a>Regarder une vidéo d’introduction

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Présentation des services de domaine Azure AD

Les Services de domaine Active Directory Azure fournissent des services de domaine gérés tels que la jonction de domaine, la stratégie de groupe, le protocole LDAP, l’authentification Kerberos/NTLM, etc. totalement compatibles avec Windows Server Active Directory. Vous pouvez utiliser ces services de domaine sans avoir à déployer, gérer et apporter des correctifs aux contrôleurs de domaine dans le cloud. Les services de domaine Azure AD s’intègrent au locataire Azure AD existant, permettant ainsi aux utilisateurs de se connecter à l’aide de leurs informations d’identification d’entreprise. En outre, vous pouvez utiliser des comptes d’utilisateurs et des groupes existants pour sécuriser l’accès aux ressources, et garantir un « transfert » plus simple des ressources locales vers les services d’infrastructure Azure.

La fonctionnalité des services de domaine Azure AD fonctionne de façon transparente, que votre locataire Azure AD soit situé uniquement dans le cloud ou synchronisé avec votre annuaire Active Directory local.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Services de domaine Azure AD pour les entreprises exclusivement dans le cloud

Un locataire Azure AD exclusivement dans le cloud (souvent appelé « locataires managés ») n’a aucune empreinte sur l’identité locale. En d’autres termes, les comptes d’utilisateurs, leurs mots de passe et les appartenances au groupe sont tous natifs du cloud, c’est-à-dire créés et gérés dans Azure AD. Imaginons un instant que Contoso est un locataire Azure AD uniquement dans le cloud. Comme indiqué sur l’illustration suivante, l’administrateur de Contoso a configuré un réseau virtuel dans les Services d’Infrastructure Azure. Les applications et les charges de travail de serveur sont déployées dans ce réseau virtuel sur des machines virtuelles Azure. Contoso étant un locataire exclusivement dans le cloud, toutes les identités des utilisateurs, leurs informations d’identification et les appartenances aux groupes sont créées et gérées dans Azure AD.

![Vue d’ensemble des services de domaine Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

L’administrateur informatique de Contoso peut activer les services de domaine Azure AD pour son locataire Azure AD et choisir de proposer des services de domaine dans ce réseau virtuel. Par la suite, les services de domaine Azure AD configurent un domaine géré et le rendent disponible sur le réseau virtuel. Tous les comptes utilisateur, les appartenances au groupe et les informations d’identification utilisateur disponibles dans le locataire Azure AD de Contoso sont également disponibles dans ce domaine nouvellement créé. Cette fonctionnalité permet aux utilisateurs de l’entreprise de se connecter au domaine à l’aide de leurs informations d’identification d’entreprise, par exemple, lors de la connexion à distance aux ordinateurs joints au domaine via le Bureau à distance. Les administrateurs peuvent configurer l’accès aux ressources dans le domaine à l’aide des appartenances aux groupes existantes. Les applications déployées sur les machines virtuelles dans le réseau virtuel peuvent utiliser des fonctionnalités telles que la jonction de domaine, la lecture LDAP, la liaison LDAP, l’authentification NTLM et Kerberos et la stratégie de groupe.

Voici quelques aspects fondamentaux du domaine géré configuré par les services de domaine Azure AD :

* L’administrateur informatique de Contoso n’a pas à gérer, appliquer les correctifs ou surveiller le domaine ou les contrôleurs de domaine pour ce domaine géré.
* Il est inutile de gérer la réplication AD pour ce domaine. Les comptes utilisateur, les appartenances aux groupes et les informations d’identification du locataire Azure AD de Contoso sont automatiquement disponibles dans ce domaine géré.
* Étant donné que le domaine est géré par les services de domaine Azure AD, l’administrateur informatique de Contoso ne dispose pas des privilèges d’administrateur de domaine ou d’administrateur d’entreprise sur ce domaine.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Services de domaine Azure AD pour les entreprises hybrides
Les entreprises dotées d’une infrastructure informatique hybride consomment à la fois des ressources de cloud et des ressources locales. Ces entreprises synchronisent les informations d’identité de leur annuaire local vers avec le locataire Azure AD. Comme les entreprises hybrides cherchent à migrer davantage d’applications locales vers le cloud, en particulier les applications héritées orientées annuaire, les services de domaine Azure AD peuvent leur être utiles.

La société Litware Corporation a déployé [Azure AD Connect](../active-directory/active-directory-aadconnect.md)afin de synchroniser les informations d’identité de l’annuaire local avec le locataire Azure AD. Ces informations d’identité incluent les comptes utilisateur, les hachages d’informations d’identification pour l’authentification (synchronisation de mot de passe) et les appartenances aux groupes.

> [!NOTE]
> **La synchronisation de mot de passe est obligatoire pour les entreprises hybrides qui veulent utiliser les services de domaine Azure AD**. Cette exigence est due au fait que les informations d’identification des utilisateurs sont obligatoires dans le domaine géré fourni par les services de domaine Azure AD afin d’authentifier les utilisateurs via les méthodes d’authentification NTLM ou Kerberos.
>
>

![Services de domaine Azure AD pour Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

L’illustration précédente montre comment des entreprises dotées d’une infrastructure informatique hybride, comme Litware, peuvent utiliser les services de domaine Azure AD. Les applications et charges de travail de serveurs de Litware nécessitant des services de domaine sont déployées dans un réseau virtuel dans les services d’infrastructure Azure. L’administrateur informatique de Litware peut activer les services de domaine Azure AD pour son locataire Azure AD et rendre un domaine géré disponible dans ce réseau virtuel. Vu que Litware est une entreprise hybride avec une infrastructure informatique hybride, les comptes d’utilisateurs, les groupes et les informations d’identification sont synchronisés avec leur locataire Azure AD à partir de leur annuaire local. Cette fonctionnalité permet aux utilisateurs de se connecter au domaine à l’aide de leurs informations d’identification d’entreprise, par exemple, lors de la connexion à distance aux ordinateurs joints au domaine via le Bureau à distance. Les administrateurs peuvent configurer l’accès aux ressources dans le domaine à l’aide des appartenances aux groupes existantes. Les applications déployées sur les machines virtuelles dans le réseau virtuel peuvent utiliser des fonctionnalités telles que la jonction de domaine, la lecture LDAP, la liaison LDAP, l’authentification NTLM et Kerberos et la stratégie de groupe.

Voici quelques aspects fondamentaux du domaine géré configuré par les services de domaine Azure AD :

* Le domaine géré est un domaine autonome. et non une extension du domaine local de Litware.
* L’administrateur informatique de Litware n’a pas à gérer, appliquer les correctifs ou analyser les contrôleurs de domaine de ce domaine géré.
* Il est inutile de gérer la réplication AD pour ce domaine. Les comptes utilisateur, les appartenances aux groupes et les informations d’identification de l’annuaire de Litware local sont synchronisés avec Azure AD via Azure AD Connect. Ces comptes utilisateur, appartenances aux groupes et informations d’identification sont automatiquement disponibles dans le domaine géré.
* Étant donné que le domaine est géré par les Services de domaine Azure AD, l’administrateur informatique de Litware administrateur ne dispose pas des privilèges d’administrateur ou des privilèges administrateur d’entreprise sur ce domaine.

## <a name="benefits"></a>Avantages
Avec les services de domaine Azure AD, vous pouvez bénéficier des avantages suivants :

* **Simple** : vous pouvez satisfaire les besoins d’identité d’ordinateurs virtuels déployés sur les services d’infrastructure Azure en quelques clics. Vous n’avez pas besoin de déployer et de gérer l’infrastructure d’identité dans Azure ni de reconfigurer la connectivité de votre infrastructure d’identité locale.
* **Intégré** : les services de domaine Azure AD s’intègrent parfaitement à votre locataire Azure AD. Désormais, vous pouvez utiliser Azure AD comme un annuaire d’entreprise dans le cloud intégré qui répond aux besoins de vos applications modernes ainsi qu’a ceux des applications de répertoires traditionnels.
* **Compatible** : les Services de domaine Azure AD reposent sur l’infrastructure de niveau entreprise de Windows Server Active Directory. Ainsi, vos applications peuvent bénéficier d’une plus grande compatibilité avec les fonctionnalités de Windows Server Active Directory. Les fonctionnalités disponibles dans Windows Server Active Directory ne sont actuellement pas toutes disponibles pour les Services de domaine Azure AD. Toutefois, les fonctionnalités disponibles sont compatibles avec les fonctionnalités correspondantes de Windows Server Active Directory présentes dans votre infrastructure locale. Les fonctionnalités LDAP, Kerberos, NTLM, Stratégie de groupe et de jonction de domaine constituent une solution ayant fait ses preuves, qui a été testée et encore affinée sur plusieurs versions de Windows Server.
* **Rentable** : avec les Services de domaine Azure AD, vous pouvez éviter les charges que représentent l’infrastructure et la gestion liées à la gestion de l’infrastructure d’identité associée aux applications orientées répertoire traditionnelles. Vous pouvez transférer ces applications vers les Services d’Infrastructure Azure et réaliser des économies substantielles sur les frais d’exploitation.


## <a name="next-steps"></a>Étapes suivantes
### <a name="learn-more-about-azure-ad-domain-services"></a>En savoir plus sur Azure AD Domain Services
* [Caractéristiques](active-directory-ds-features.md)
* [Scénarios de déploiement](active-directory-ds-scenarios.md)
* [Déterminer si la solution Azure AD Domain Services répond aux besoins de vos cas d’usage](active-directory-ds-comparison.md)
* [Comprendre comment la solution Azure AD Domain Services est synchronisée avec votre annuaire Azure AD](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Prise en main des services de domaine Azure AD
* [Activer Azure AD Domain Services à l’aide du portail Azure](active-directory-ds-getting-started.md)
