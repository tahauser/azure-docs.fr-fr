---
title: Forum Aux Questions sur Azure Active Directory | Microsoft Docs
description: "Le Forum Aux Questions sur Azure Active Directory répond aux questions courantes concernant Azure et Azure Active Directory, la gestion des mots de passe et l’accès aux applications."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: markvi
ms.openlocfilehash: 7640d2ba495d465f06bd918df0c08f0d85b42e76
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-active-directory-faq"></a>Forum Aux Questions sur Azure Active Directory
Azure Active Directory (Azure AD) est une solution IDaaS (Identity as a Service) complète qui couvre tous les aspects de l’identité, la gestion des accès et la sécurité.

Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Accédez à Azure et à Azure Active Directory
**Q : Pourquoi le message « Aucun abonnement trouvé » s’affiche-t-il lorsque j’essaie d’accéder à Azure AD dans le portail Azure ?**

**R :** Pour accéder au portail Azure, chaque utilisateur doit y être autorisé dans le cadre d’un abonnement Azure. Si vous possédez un abonnement Office 365 ou Azure AD payant, accédez à [http://aka.ms/accessAAD](http://aka.ms/accessAAD) pour procéder à une étape d’activation unique. Si ce n’est pas le cas, vous devez activer un [compte Azure](https://azure.microsoft.com/pricing/free-trial/) gratuit ou un abonnement payant.

Pour plus d'informations, consultez les pages suivantes :

* [Association des abonnements Azure avec Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestion du répertoire de l’abonnement Office 365 dans Azure](active-directory-manage-o365-subscription.md)

- - -
**Q : Quelle est la relation entre Azure AD, Office 365 et Azure ?**

**R :** Azure AD vous fournit une identité et des fonctionnalités d’accès communes à tous les services web. Que vous utilisiez Office 365, Microsoft Azure, Intune ou d’autres outils, vous utilisez déjà Azure AD afin d’activer l’authentification et la gestion des accès pour l’ensemble de ces services.

Tous les utilisateurs autorisés à utiliser les services web sont définis en tant que comptes d’utilisateurs dans une ou plusieurs instances d’Azure AD. Vous pouvez configurer ces comptes pour des fonctionnalités Azure AD gratuites, par exemple l’accès aux applications cloud.

Les services Azure AD payants comme Enterprise Mobility + Security complètent d’autres services web comme Office 365 et Microsoft Azure avec des solutions avancées de gestion et de sécurité à l’échelle de l’entreprise.

- - -

**Q : Quelles sont les différences entre l’administrateur de l’abonnement et l’administrateur du répertoire ?**

**R :** Par défaut, le rôle d’administrateur d’abonnement vous est attribué dès lors que vous vous abonnez à Azure. Un administrateur d’abonnement peut utiliser un compte Microsoft ou un compte professionnel ou scolaire provenant du répertoire auquel l’abonnement Azure est associé.  Ce rôle est autorisé à gérer les services sur le portail Azure.

Si d’autres personnes doivent se connecter et accéder aux services à l’aide de l’abonnement, vous pouvez les ajouter en tant que coadministrateurs. Ce rôle possède les mêmes privilèges d’accès que l’administrateur de services fédérés, à ceci près qu’il ne peut pas modifier la manière dont les abonnements sont associés aux répertoires Azure.  Pour plus d’informations sur les administrateurs d’abonnements, consultez [Ajout ou modification de rôles d’administrateur Azure](../billing-add-change-azure-subscription-administrator.md) et [Association des abonnements Azure avec Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).


Azure AD propose un autre ensemble de rôles administratifs pour gérer le répertoire et les fonctionnalités liées à l’identité.  Ces administrateurs ont accès à diverses fonctionnalités du portail Azure. Le rôle de l’administrateur détermine les opérations qu’il peut effectuer : créer ou modifier des utilisateurs, attribuer des rôles d’administrateur à d’autres personnes, réinitialiser les mots de passe utilisateur, gérer les licences utilisateur et les domaines, etc.  Pour plus d’informations sur les administrateurs de répertoires Azure AD et leurs rôles, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Par ailleurs, les services Azure AD payants comme Enterprise Mobility + Security complètent d’autres services web, tels qu’Office 365 et Microsoft Azure, avec des solutions avancées de gestion et de sécurité à l’échelle de l’entreprise.

- - -
**Q : Existe-t-il un rapport qui indique la date d’expiration de mes licences utilisateur Azure AD ?**

**R :** Non.  Ce n’est pas le cas pour l’instant.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Prise en main d’Azure AD hybride


**Q : Comment quitter un client quand je suis ajouté comme collaborateur ?**

**R :** Lorsque vous êtes ajouté comme collaborateur au client d’une autre organisation, vous pouvez utiliser le « sélecteur de client » dans le coin supérieur droit pour basculer entre les clients.  Il n’existe actuellement aucun moyen de quitter l’organisation à l’origine de l’invitation, mais Microsoft travaille à la mise en place de cette fonctionnalité.  En attendant que cette fonctionnalité soit disponible, vous pouvez demander à l’organisation à l’origine de l’invitation de vous retirer de son client.
- - -
**Q : Comment puis-je connecter mon annuaire local à Azure AD ?**

**R :** Vous pouvez connecter votre annuaire local à Azure AD à l’aide d’Azure AD Connect.

Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md).

- - -
**Q : Comment configurer l’authentification unique entre mon annuaire local et mes applications cloud ?**

**R :** Vous devez uniquement configurer l’authentification unique (SSO) entre votre annuaire local et Azure AD. Tant que vous accédez à vos applications cloud via Azure AD, le service conduit automatiquement vos utilisateurs à s’authentifier correctement avec leurs informations d’identification locales.

L’implémentation du SSO à partir de l’emplacement local peut être facilement mise en œuvre à l’aide de solutions de fédération telles que les services de fédération Active Directory (AD FS) ou en configurant la synchronisation du hachage de mot de passe. Vous pouvez facilement déployer les deux options à l’aide de l’Assistant de configuration Azure AD Connect.

Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](active-directory-aadconnect.md).

- - -
**Q : Azure AD fournit-il un portail en libre-service aux utilisateurs de mon organisation ?**

**R :** Oui, Azure AD fournit le [Panneau d’accès Azure AD](http://myapps.microsoft.com) pour l’accès aux applications en libre-service. Si vous êtes un client Office 365, vous trouverez la plupart des mêmes fonctionnalités dans le portail Office 365.

Pour plus d’informations, consultez [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

- - -
**Q : Azure AD m’aide-t-il à gérer mon infrastructure locale ?**

**R :** Oui. L’édition Azure AD Premium offre l’outil Azure AD Connect Health. Azure AD Connect Health vous permet de surveiller et d’analyser votre infrastructure d’identité locale et les services de synchronisation.  

Pour plus d’informations, consultez [Surveillez votre infrastructure d’identité locale et vos services de synchronisation dans le cloud](active-directory-aadconnect-health.md).  

- - -
## <a name="password-management"></a>Gestion des mots de passe
**Q : Puis-je utiliser l’écriture différée de mot de passe Azure AD sans synchronisation de mot de passe ? (Dans ce scénario, est-il possible d’utiliser la réinitialisation de mot de passe en libre-service d’Azure AD avec l’écriture différée de mot de passe et de ne pas stocker les mots de passe dans le cloud ?)**

**R :** Vous n’avez pas besoin de synchroniser vos mots de passe Active Directory sur Azure AD afin d’activer l’écriture différée. Dans un environnement fédéré, l’authentification unique (SSO) Azure AD s’appuie sur l’annuaire local pour authentifier l’utilisateur. Ce scénario ne nécessite pas le suivi du mot de passe local dans Azure AD.

- - -
**Q : Combien de temps faut-il pour qu’un mot de passe soit écrit de manière différée sur Active Directory en local ?**

**R :** L’écriture différée de mot de passe fonctionne en temps réel.

Pour en savoir plus, voir [Prise en main de la gestion de mot de passe](active-directory-passwords-getting-started.md).

- - -
**Q : Puis-je utiliser l’écriture différée de mot de passe avec des mots de passe gérés par un administrateur ?**

**R :** Oui, si cette fonction est activée, les opérations de mot de passe effectuées par un administrateur sont écrites de manière différée dans votre environnement local.  

Si vous avez d’autres questions concernant les mots de passe, consultez [Forum aux questions - Gestion des mots de passe](active-directory-passwords-faq.md).
- - -
**Q : Que faire si je ne me souviens pas de mon mot de passe Office 365/Azure AD actuel lorsque j’essaie de modifier mon mot de passe ?**

**R :** Pour ce type de situation, plusieurs options s’offrent à vous.  Utilisez la réinitialisation de mot de passe en libre-service si elle est disponible.  Le fonctionnement de la réinitialisation de mot de passe en libre-service dépend de la façon dont elle est configurée.  Pour plus d’informations, consultez [Fonctionnement du portail de réinitialisation de mot de passe](active-directory-passwords-best-practices.md).

Pour les utilisateurs Office 365, l’administrateur peut réinitialiser le mot de passe à l’aide de la procédure décrite dans [Réinitialiser les mots de passe utilisateur](https://support.office.com/en-us/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Pour les comptes Azure AD, les administrateurs peuvent réinitialiser les mots de passe à l’aide de l’une des opérations suivantes :

- [Réinitialiser les comptes dans le portail Azure](active-directory-users-reset-password-azure-portal.md)
- [Utiliser PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Sécurité
**Q : Les comptes sont-ils verrouillés après un nombre spécifique de tentatives infructueuses, ou une stratégie plus élaborée est-elle utilisée ?**

Nous appliquons une stratégie plus étoffée pour verrouiller les comptes.  Cette dernière repose sur l’adresse IP de la demande et sur les mots de passe entrés. La durée du verrouillage augmente également en fonction de la probabilité de l’existence d’une attaque.  

**Q : Certains mots de passe (courants) sont rejetés avec un message du type : « Ce mot de passe a été utilisé trop souvent ». Ce message fait-il référence aux mots de passe utilisés dans l’instance Active Directory actuelle ?**

Ce message s’applique aux mots de passe couramment utilisés au niveau mondial, tels que toutes les variantes de « Password » et de « 123456 ».

**Q : Une demande de connexion émanant de sources douteuses (botnets, point de terminaison Tor) sera-t-elle bloquée dans un client B2C ou nécessite-t-elle un client d’une édition De base ou Premium ?**

Nous disposons d’une passerelle qui filtre les demandes et offre une certaine protection contre les botnets. Elle s’applique à tous les clients B2C.

## <a name="application-access"></a>Accès aux applications

**Q : Où puis-je trouver une liste des applications qui sont déjà intégrées à Azure AD et leurs fonctionnalités ?**

**R :** Azure AD offre plus de 2 600 applications pré-intégrées développées par Microsoft, des fournisseurs de services d’application et des partenaires. Toutes les applications pré-intégrées prennent en charge l’authentification unique (SSO). L’authentification unique vous permet d’utiliser vos informations d’identification professionnelles pour accéder à vos applications. Certaines applications prennent également en charge l’approvisionnement et l’annulation d’approvisionnement automatisés.

Pour obtenir une liste complète des applications déjà intégrées, voir [Marketplace Active Directory](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**Q : Que se passe-t-il si l’application dont j’ai besoin ne figure pas dans le Marketplace Azure AD ?**

**R :** Azure AD Premium vous permet d’ajouter et de configurer n’importe quelle application. Vous pouvez configurer, selon les fonctionnalités de votre application et vos préférences, l’authentification unique et l’approvisionnement automatisé.  

Pour plus d'informations, consultez les pages suivantes :

* [Configuration de l'authentification unique pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory.](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)

- - -
**Q : Comment les utilisateurs peuvent-ils se connecter aux applications à l’aide d’Azure AD ?**

**R :** Azure AD offre aux utilisateurs plusieurs moyens pour afficher leurs applications et y accéder, par exemple :

* Panneau d’accès Azure AD
* Lanceur d’applications Office 365
* Authentification directe pour les applications fédérées
* Liens ciblés vers des applications fédérées, avec mot de passe ou des applications existantes

Pour plus d’informations, voir [Déploiement d’applications Azure AD intégrées pour les utilisateurs](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**Q : Par quels moyens Azure AD active-t-il l’authentification et l’authentification unique pour la connexion aux applications ?**

**R :** Azure AD prend en charge de nombreux protocoles standardisés pour l’authentification et l’autorisation, tels que SAML 2.0, OpenID Connect, OAuth 2.0 et WS-Federation. Azure AD prend également en charge la mise en coffre du mot de passe et les fonctionnalités d’authentification automatisées pour les applications qui prennent uniquement en charge l’authentification basée sur les formulaires.  

Pour plus d'informations, consultez les pages suivantes :

* [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md)
* [Protocoles d’authentification Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Fonctionnement de l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**Q : Puis-je ajouter des applications si l’exécution s’effectue en local ?**

**R :** Le proxy d’application Azure AD vous offre un accès facile et sécurisé aux applications web en local de votre choix. Vous pouvez accéder à ces applications de la même façon que vous accédez à vos applications SaaS (Software as a Service) dans Azure AD. Vous n’avez pas besoin de recourir à un VPN ou de modifier votre infrastructure réseau.  

Pour plus d’informations, consultez [Offrir un accès à distance sécurisé aux applications locales](active-directory-application-proxy-get-started.md).

- - -
**Q : Comment faire pour imposer l’authentification multifacteur aux utilisateurs qui accèdent à une application spécifique ?**

**R :** L’accès conditionnel Azure AD vous permet d’affecter une stratégie d’accès unique à chaque application. Dans votre stratégie, vous pouvez exiger l’authentification multificateur en permanence ou lorsque les utilisateurs ne sont pas connectés au réseau local.  

Pour plus d’informations, consultez [Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- - -
**Q : Qu’est-ce que l’approvisionnement automatique des utilisateurs pour les applications SaaS ?**

**R :** Azure AD vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans de nombreuses applications SaaS cloud populaires.

Pour plus d’informations, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md).

- - -
**Q : Puis-je configurer une connexion LDAP sécurisée avec Azure AD ?**

**R :** Non. Azure AD ne prend pas en charge le protocole LDAP.
