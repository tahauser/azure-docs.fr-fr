---
title: Index d’articles pour la gestion des applications dans Azure Active Directory | Microsoft Azure
description: Apprenez à personnaliser la date d’expiration pour vos certificats de fédération, mais aussi à renouveler les certificats arrivant à expiration.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 5321b8e4-2afa-4dfe-8d53-4add7abb5ec8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: ffb8e822644ba8f0bccc3e81edc4da6085ff3ec7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Index d’articles pour la gestion des applications dans Azure Active Directory
Cette page fournit la liste complète des documents qui traitent des diverses fonctionnalités d’application dans Azure Active Directory (Azure AD).

Vous trouverez une brève introduction à chaque domaine de fonctionnalités principal, ainsi que des conseils sur les articles à lire suivant les informations recherchées.

## <a name="overview-articles"></a>Articles généraux
Les articles ci-dessous constituent de bons points de départ pour les personnes souhaitant simplement une brève explication des fonctionnalités de gestion d’applications Azure AD.

| Guide des articles |  |
|:---:| --- |
| Présentation des problèmes de gestion d’application résolus par Azure AD |[Gestion des applications avec Azure Active Directory (AD)](active-directory-enable-sso-scenario.md) |
| Vue d’ensemble des différentes fonctionnalités dans Azure AD liées à l’activation de l’authentification unique, à la définition des personnes pouvant accéder aux applications et à la façon dont les utilisateurs lancent des applications |[Accès aux applications et authentification unique dans Azure Active Directory](active-directory-appssoaccess-whatis.md) |
| Examen des différentes étapes de l’intégration des applications à Azure AD |[Intégration d’applications dans Azure Active Directory](active-directory-integrating-applications-getting-started.md)<br /><br />[Activation de l’authentification unique pour les applications SaaS](active-directory-enterprise-apps-manage-sso.md)<br /><br />[Gestion de l’accès aux applications](active-directory-managing-access-to-apps.md) |
| Explication technique de la représentation des applications dans Azure AD |[Comment et pourquoi les applications sont ajoutées à Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Articles de résolution des problèmes
Cette section fournit un accès rapide aux guides de résolution des problèmes appropriés. Vous trouverez plus d’informations sur chaque domaine de fonctionnalités sur cette page.

| Domaine de fonctionnalités |  |
|:---:| --- |
| Authentification unique fédérée |[Dépannage de l’authentification unique basée sur SAML](active-directory-saml-debugging.md) |
| Authentification unique par mot de passe |[Résolution des problèmes liés à l'extension du volet d'accès pour Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Proxy d’application |[Guide de résolution des problèmes pour le proxy d’application](active-directory-application-proxy-troubleshoot.md) |
| Authentification unique entre AD en local et Azure AD |[Dépannage de la synchronisation de hachage du mot de passe](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Résolution des problèmes de réécriture du mot de passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Appartenances au groupe dynamique |[Résolution des problèmes liés à l’appartenance au groupe dynamique](active-directory-accessmanagement-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Authentification unique (SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Authentification unique fédérée : se connecter à plusieurs applications à l’aide d’une seule identité
L’authentification unique permet aux utilisateurs d’accéder à différentes applications et services à l’aide d’un seul ensemble d’informations d’identification. La fédération est une méthode par le biais de laquelle vous pouvez activer l’authentification unique. Quand les utilisateurs tentent de se connecter à des applications fédérées, ils sont redirigés vers la page de connexion officielle de leur organisation rendue par Azure Active Directory, puis vers l’application une fois correctement authentifiés.

| Guide des articles |  |
|:---:| --- |
| Introduction à la fédération et à d’autres types d’authentification |[Authentification unique avec Azure AD](active-directory-appssoaccess-whatis.md) |
| Des milliers d’applications SaaS pré-intégrées à Azure AD avec des étapes de configuration de l’authentification unique simplifiées |[Prise en main de la galerie d’applications Azure AD](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Liste complète des applications pré-intégrées qui prennent en charge la fédération](active-directory-saas-tutorial-list.md)<br /><br />[Comment ajouter votre application à la galerie d’applications Azure AD](active-directory-app-gallery-listing.md) |
| Plus de 150 didacticiels d’application sur la configuration de l’authentification unique pour les applications telles que [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md), et bien plus encore |[Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md) |
| Comment configurer et personnaliser manuellement votre configuration de l’authentification unique |[Configuration de l'authentification unique fédérée pour les applications ne faisant pas partie de la galerie d'applications Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Comment personnaliser les revendications émises dans le jeton SAML pour les applications pré-intégrées](active-directory-saml-claims-customization.md) |
| Guide de dépannage pour les applications fédérées qui utilisent le protocole SAML |[Dépannage de l’authentification unique basée sur SAML](active-directory-saml-debugging.md) |
| Comment configurer la date d’expiration du certificat de votre application et renouveler vos certificats |[Gestion des certificats pour l’authentification unique fédérée sur Azure Active Directory](active-directory-sso-certs.md) |

L’authentification unique fédérée est disponible pour toutes les éditions d’Azure AD dans la limite de dix applications par utilisateur. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) prend en charge un nombre illimité d’applications. Si votre organisation a [Azure AD Standard](https://azure.microsoft.com/pricing/details/active-directory/) ou [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), vous pouvez [utiliser des groupes pour affecter l’accès à des applications fédérées](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Authentification unique par mot de passe : partage de compte et authentification unique pour les applications non fédérées
Pour activer l’authentification unique pour les applications qui ne prennent pas en charge la fédération, Azure AD offre des fonctionnalités de gestion de mot de passe qui peuvent stocker de façon sécurisée les mots de passe pour les applications SaaS et connecter automatiquement les utilisateurs à ces applications. Vous pouvez facilement distribuer des informations d’identification pour les comptes créés et partager des comptes d’équipe avec plusieurs personnes. Les utilisateurs ne doivent pas nécessairement connaître les informations d’identification pour les comptes auxquels ils ont accès.

| Guide des articles |  |
|:---:| --- |
| Introduction au fonctionnement de l’authentification unique par mot de passe et brève vue d’ensemble technique |[Authentification unique par mot de passe avec Azure AD](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| Résumé des scénarios relatifs au partage de compte et résolution de ces problèmes par Azure AD |[Partage de comptes avec Azure AD](active-directory-sharing-accounts.md) |
| Modifier automatiquement le mot de passe pour certaines applications à intervalles réguliers |[Substitution de mot de passe automatique (version préliminaire)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Guides de déploiement et de dépannage pour la version Internet Explorer de l’extension de gestion de mot de passe Azure AD |[Déploiement de l'extension du volet d'accès pour Internet Explorer à l'aide de la stratégie de groupe](active-directory-saas-ie-group-policy.md)<br /><br />[Résolution des problèmes liés à l'extension du volet d'accès pour Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

L’authentification unique par mot de passe est disponible pour toutes les éditions d’Azure AD dans la limite de dix applications par utilisateur. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) prend en charge un nombre illimité d’applications. Si votre organisation a [Azure AD Standard](https://azure.microsoft.com/pricing/details/active-directory/) ou [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), vous pouvez [utiliser des groupes pour affecter l’accès à des applications](#managing-access-to-applications). La substitution de mot de passe automatique est une fonctionnalité [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Proxy d’application : authentification unique et accès distant aux applications locales
Si votre réseau privé contient des applications qui doivent être accessibles à des utilisateurs et appareils qui n’appartiennent pas au réseau, vous pouvez utiliser le proxy d’application Azure AD pour permettre un accès sécurisé à distance à ces applications.

| Guide des articles |  |
|:---:| --- |
| Vue d’ensemble du proxy d’application Azure AD et présentation de son fonctionnement |[Offrir un accès distant sécurisé à des applications locales](active-directory-application-proxy-get-started.md) |
| Didacticiels sur la façon de configurer le proxy d’application et de publier votre première application |[Guide de configuration du proxy d’application Azure AD](active-directory-application-proxy-enable.md)<br /><br />[Comment installer silencieusement le connecteur du Proxy d'application](active-directory-application-proxy-silent-installation.md)<br /><br />[Publication d’applications à l’aide du proxy d’application](active-directory-application-proxy-publish.md)<br /><br />[Comment utiliser votre propre nom de domaine](active-directory-application-proxy-custom-domains.md) |
| Comment activer l’authentification unique et l’accès conditionnel pour les applications publiées avec le proxy d’application |[Authentification unique avec le proxy d’application](active-directory-application-proxy-sso-using-kcd.md)<br /><br />[Accès conditionnel et proxy d’Application](application-proxy-enable-remote-access-sharepoint.md) |
| Aide sur l’utilisation du proxy d’application pour les scénarios suivants |[Prise en charge des applications clientes natives](active-directory-application-proxy-native-client.md)<br /><br />[Prise en charge des applications prenant en charge les revendications](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[Comment prendre en charge les applications publiées sur des réseaux et emplacements distincts](active-directory-application-proxy-connectors-azure-portal.md) |
| Guide de résolution des problèmes pour le proxy d’application |[Guide de résolution des problèmes pour le proxy d’application](active-directory-application-proxy-troubleshoot.md) |

Le proxy d’application est disponible pour toutes les éditions d’Azure AD dans la limite de dix applications par utilisateur. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) prend en charge un nombre illimité d’applications. Si votre organisation a [Azure AD Standard](https://azure.microsoft.com/pricing/details/active-directory/) ou [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), vous pouvez [utiliser des groupes pour affecter l’accès à des applications](#managing-access-to-applications).

Vous pouvez également être intéressé par les [services de domaine Azure AD](../active-directory-domain-services/active-directory-ds-overview.md), qui vous permettent de migrer vos applications locales vers Azure tout en répondant aux besoins d’identité de ces applications.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Activation de l’authentification unique entre Azure AD et AD en local
Si votre organisation gère un annuaire Windows Server Active Directory local parallèlement à Azure Active Directory dans le cloud, vous pouvez avoir intérêt à activer l’authentification unique entre ces deux systèmes. Azure AD Connect (outil qui intègre ces deux systèmes ensemble) offre plusieurs options pour configurer l’authentification unique : établir une fédération avec AD FS ou un autre fournisseur de fédération ou activer la synchronisation des mots de passe.

| Guide des articles |  |
|:---:| --- |
| Vue d’ensemble des options d’authentification unique proposées dans Azure AD Connect, ainsi que des informations sur la gestion des environnements hybrides |[Options d’authentification de l’utilisateur dans Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Conseils généraux sur la gestion des environnements comportant à la fois Active Directory en local et Azure Active Directory |[Considérations relatives à la conception d'identités hybrides Azure AD](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md) |
| Aide sur l’utilisation de la synchronisation de mot de passe pour activer l’authentification unique |[Implémenter la synchronisation de mot de passe avec Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Résoudre les problèmes de synchronisation de mot de passe](https://support.microsoft.com/en-us/kb/2855271) |
| Aide sur l’utilisation de la réécriture du mot de passe pour activer l’authentification unique |[Prise en main de la gestion de mot de passe dans Azure AD](active-directory-passwords-getting-started.md)<br /><br />[Résoudre les problèmes de réécriture du mot de passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Aide sur l’utilisation de fournisseurs d’identité tiers pour activer l’authentification unique |[Liste des fournisseurs d’identité de tiers compatibles pouvant être utilisés pour activer l’authentification unique](https://aka.ms/ssoproviders) |
| Comment les utilisateurs de Windows 10 peuvent profiter des avantages de l’authentification unique par le biais d’Azure AD Join |[Extension des fonctionnalités du cloud aux appareils Windows 10 par le biais d’Azure Active Directory Join](active-directory-azureadjoin-overview.md) |

Azure AD Connect est disponible pour [toutes les éditions d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). La réinitialisation du mot de passe libre-service Azure AD est disponible pour [Azure AD Standard](https://azure.microsoft.com/pricing/details/active-directory/) et [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). La réécriture du mot de passe sur Active Directory en local est une fonctionnalité [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Accès conditionnel : appliquer des exigences de sécurité supplémentaires pour les applications à haut risque
Une fois que vous avez défini l’authentification unique pour vos applications et ressources, vous pouvez sécuriser davantage les applications sensibles en imposant des exigences de sécurité spécifiques à chaque connexion à l’application concernée. Par exemple, vous pouvez utiliser Azure AD pour imposer que tout accès à une application particulière exige le recours systématique à l’authentification multifacteur, même si cette application ne prend pas en charge intrinsèquement cette fonctionnalité. De même, vous pouvez imposer que les utilisateurs soient connectés au réseau approuvé de l’entreprise pour accéder à une application particulièrement sensible.

| Guide des articles |  |
|:---:| --- |
| Présentation des fonctionnalités d’accès conditionnel offertes dans Azure AD, Office 365 et Intune |[Gestion des risques avec accès conditionnel](active-directory-conditional-access-azure-portal.md) |
| Comment activer l’accès conditionnel pour les types de ressources suivants |[Accès conditionnel pour les applications SaaS](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Accès conditionnel pour les services Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Accès conditionnel pour des applications locales](active-directory-conditional-access-azure-portal.md)<br /><br />[Accès conditionnel pour les applications locales publiées par le biais du proxy d’application Azure AD](application-proxy-enable-remote-access-sharepoint.md) |
| Comment inscrire des appareils auprès d’Azure Active Directory pour activer des stratégies d’accès conditionnel en fonction de l’appareil |[Vue d’ensemble du service d’inscription d’appareil Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Activation de l’inscription automatique des appareils pour les appareils joints à un domaine Windows](active-directory-conditional-access-automatic-device-registration.md)<br />— [Étapes pour les appareils Windows 8.1](active-directory-conditional-access-automatic-device-registration-setup.md)<br />— [Étapes pour les appareils Windows 7](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Utilisation de l’application Microsoft Authenticator pour la vérification en deux étapes |[Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

L’accès conditionnel est une fonctionnalité [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="apps--azure-ad"></a>Applications et Azure AD
### <a name="cloud-app-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud App Discovery : recherchez les applications SaaS en cours d’utilisation dans votre organisation
Cloud App Discovery aide les services informatiques à déterminer les applications SaaS en cours d’utilisation dans l’ensemble de l’organisation. Un service informatique peut l’utiliser pour mesurer l’utilisation et la popularité des applications afin de déterminer celles qui gagneront le plus à être placées sous son contrôle et intégrées à Azure AD.

| Guide des articles |  |
|:---:| --- |
| Vue d’ensemble de son fonctionnement |[Détection des applications cloud non approuvées avec Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md) |
| Examen approfondi de son fonctionnement, avec des réponses aux questions sur la confidentialité |[Considérations relatives à la confidentialité et à la sécurité](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Forum Aux Questions (FAQ) |[Forum Aux Questions consacré à Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Didacticiels de déploiement de Cloud App Discovery |[Guide de déploiement de stratégie de groupe](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[Guide de déploiement de System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[Installation sur les serveurs proxy avec ports personnalisés](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Journal des modifications pour les mises à jour de l’agent Cloud App Discovery |[Journal des modifications](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Cloud App Discovery est une fonctionnalité [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Approvisionnement automatique des comptes d’utilisateur dans les applications SaaS et annulation de l’approvisionnement
Automatisez la création, la maintenance et la suppression d’identités utilisateur dans les applications SaaS comme Dropbox, Salesforce, ServiceNow et bien plus encore. Faites correspondre et synchronisez des identités existantes entre Azure AD et vos applications SaaS, et contrôlez l’accès en désactivant automatiquement les comptes quand les utilisateurs quittent l’organisation.

| Guide des articles |  |
|:---:| --- |
| Découvrir son fonctionnement et trouver les réponses aux questions les plus fréquentes |[Automatiser l’approvisionnement/annuler l’approvisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md) |
| Configurer le mappage des informations entre Azure AD et votre application SaaS |[Personnalisation des mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Écriture d’expressions pour les mappages d’attributs](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Comment activer l’approvisionnement automatique pour toute application qui prend en charge le protocole SCIM |[Configurer l’approvisionnement automatique des utilisateurs pour toute application prenant en charge le protocole SCIM](active-directory-scim-provisioning.md) |
| Comment créer des rapports et résoudre les problèmes d’approvisionnement d’utilisateurs |[Création de rapports sur l’approvisionnement d’utilisateurs automatique](active-directory-saas-provisioning-reporting.md)<br><br>[Résolution des problèmes d’approvisionnement d’utilisateurs](active-directory-application-provisioning-content-map.md) |
| Limiter les personnes pouvant être approvisionnées pour une application en fonction de ses valeurs d’attribut |[Filtres d’étendue](active-directory-saas-scoping-filters.md) |

L’approvisionnement automatique des utilisateurs est disponible pour toutes les éditions d’Azure AD dans la limite de dix applications par utilisateur. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) prend en charge un nombre illimité d’applications. Si votre organisation a [Azure AD Standard](https://azure.microsoft.com/pricing/details/active-directory/) ou [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), vous pouvez [utiliser des groupes pour gérer les utilisateurs pouvant être approvisionnés](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Création d’applications qui s’intègrent à Azure AD
Si votre organisation développe ou gère des applications métiers, ou que vous développez des applications pour des clients qui utilisent Azure Active Directory, les didacticiels suivants vous aident à intégrer vos applications à Azure AD.

| Guide des articles |  |
|:---:| --- |
| Conseils pour les professionnels de l’informatique et les développeurs d’applications sur l’intégration d’applications à Azure AD |[Guide des professionnels de l’informatique pour le développement d’applications pour Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Guide des développeurs pour Azure Active Directory](active-directory-developers-guide.md) |
| Comment les fournisseurs d’application peuvent ajouter leurs applications à la galerie d’applications Azure AD |[Affichage de votre application dans la galerie d’applications Azure AD](active-directory-app-gallery-listing.md) |
| Comment gérer l’accès aux applications développées à l’aide d’Azure Active Directory |[Activation de l’attribution d’utilisateur pour les applications développées](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Affectation d’utilisateurs à votre application](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Affectation d’un groupe à votre application](active-directory-applications-guiding-developers-assigning-groups.md) |

Si vous développez des applications orientées consommateurs, pensez à utiliser [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) ; ainsi, vous n’êtes pas obligé de développer votre propre système d’identité pour gérer vos utilisateurs. [En savoir plus](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Gestion de l’accès aux applications
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Utilisation de groupes et du libre-service pour gérer qui a accès à quelles applications
Pour vous aider à gérer qui doit avoir accès à quelles ressources, Azure Active Directory vous permet de définir des affectations et des autorisations à grande échelle à l’aide de groupes. Le service informatique peut choisir d’activer les fonctionnalités de libre-service pour que les utilisateurs puissent demander l’autorisation simplement quand ils en ont besoin.

| Guide des articles |  |
|:---:| --- |
| Vue d’ensemble des fonctionnalités de gestion d’accès Azure AD |[Introduction à la gestion de l'accès aux applications](active-directory-managing-access-to-apps.md)<br /><br />[Fonctionnement de la gestion des accès dans Azure AD](active-directory-manage-groups.md)<br /><br />[Comment utiliser des groupes pour gérer l’accès aux applications SaaS](active-directory-accessmanagement-group-saasapps.md) |
| Activer la gestion des applications et groupes en libre-service |[Gestion des applications en libre-service](active-directory-self-service-application-access.md)<br /><br />[Gestion des groupes en libre-service](active-directory-accessmanagement-self-service-group-management.md) |
| Instructions pour configurer vos groupes dans Azure AD |[Création de groupes de sécurité](active-directory-groups-create-azure-portal.md)<br /><br />[Procédure de désignation des propriétaires d’un groupe](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Comment utiliser le groupe « Tous les utilisateurs »](active-directory-accessmanagement-dedicated-groups.md) |
| Utiliser des groupes dynamiques pour renseigner automatiquement l’appartenance au groupe à l’aide de règles d’appartenance basées sur des attributs |[Appartenance au groupe dynamique  : règles avancées](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Résolution des problèmes liés à l’appartenance au groupe dynamique](active-directory-accessmanagement-troubleshooting.md) |

La gestion de l’accès aux applications basée sur un groupe est disponible pour [Azure AD Standard](https://azure.microsoft.com/pricing/details/active-directory/) et [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). La gestion des groupes en libre-service, la gestion des applications en libre-service et les groupes dynamiques sont des fonctionnalités [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>B2B Collaboration : activer l’accès partenaire aux applications
Si votre entreprise a noué des partenariats avec d’autres sociétés, vous devez probablement gérer l’accès partenaire à vos applications d’entreprise. Azure Active Directory B2B Collaboration offre un moyen simple et sécurisé pour partager vos applications avec des partenaires.

| Guide des articles |  |
|:---:| --- |
| Vue d’ensemble des différentes fonctionnalités Azure AD qui vous aident à gérer des utilisateurs externes tels que des partenaires ou des clients. |[Comparaison des capacités de gestion des identités externes dans Azure AD](active-directory-b2b-compare-external-identities.md) |
| Présentation et prise en main de B2B Collaboration |[Intégration de partenaires cloud simple et sécurisée avec Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Azure Active Directory B2B Collaboration](active-directory-b2b-collaboration-overview.md) |
| Examen approfondi d’Azure AD B2B Collaboration et de son utilisation |[Collaboration B2B : Fonctionnement](active-directory-b2b-how-it-works.md)<br /><br />[Limites actuelles d’Azure AD B2B Collaboration](active-directory-b2b-current-limitations.md)<br /><br />[Procédure pas à pas détaillée de l’utilisation d’Azure AD B2B Collaboration](active-directory-b2b-detailed-walkthrough.md) |
| Articles de référence contenant des détails techniques sur le fonctionnement d’Azure AD B2B Collaboration |[Format de fichier CSV pour l’ajout d’utilisateurs partenaires](active-directory-b2b-references-csv-file-format.md)<br /><br />[Attributs de l’utilisateur affectés par Azure AD B2B Collaboration](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Format de jeton utilisateur pour les utilisateurs partenaires](active-directory-b2b-references-external-user-token-format.md) |

B2B Collaboration est actuellement disponible pour [toutes les éditions d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Volet d’accès : portail pour accéder aux applications et fonctionnalités de libre-service
Grâce au volet d’accès Azure AD, les utilisateurs finaux peuvent lancer leurs applications et accéder aux fonctionnalités de libre-service qui leur permettent de gérer leurs applications et les appartenances aux groupes. Outre le volet d’accès, d’autres options d’accès aux applications prenant en charge l’authentification unique sont incluses dans la liste ci-dessous.

| Guide des articles |  |
|:---:| --- |
| Comparaison des différentes options de déploiement d’applications avec authentification unique pour les utilisateurs |[Déploiement d’applications Azure AD intégrées pour les utilisateurs](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) |
| Vue d’ensemble du volet d’accès et des applications MyApp mobiles équivalentes |[Introduction au volet d’accès et aux applications MyApp](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Comment accéder aux applications Azure AD depuis le site web Office 365 |[Utilisation du lanceur d’applications Office 365](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Comment accéder aux applications Azure AD depuis l’application mobile Intune Managed Browser |[Intune Managed Browser](https://technet.microsoft.com/en-us/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Comment accéder aux applications Azure AD à l’aide de liens ciblés pour lancer l’authentification unique |[Établissement de liens d’authentification directs vers vos applications](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Le volet d’accès est disponible pour [toutes les éditions d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Rapports : auditez les modifications d’accès aux applications et surveillez les connexions aux applications facilement
Azure Active Directory fournit plusieurs rapports et alertes qui vous aident à surveiller l’accès aux applications de votre organisation. Vous pouvez recevoir des alertes en cas de connexions anormales à vos applications, et vous pouvez suivre quand et pourquoi l’accès d’un utilisateur à une application a changé.

| Guide des articles |  |
|:---:| --- |
| Vue d’ensemble des fonctionnalités de création de rapports dans Azure Active Directory |[Prise en main de la création de rapports Azure AD](active-directory-reporting-getting-started.md) |
| Comment surveiller les connexions et l’utilisation des applications par vos utilisateurs |[Affichage de vos rapports d’accès et d’utilisation](active-directory-view-access-usage-reports.md) |
| Effectuer le suivi des personnes pouvant accéder à une application spécifique |[Événements de rapport d'audit d'Azure Active Directory](active-directory-reporting-audit-events.md) |
| Exporter les données de ces rapports dans vos outils préférés à l’aide de l’API de création de rapports |[Prise en main de l’API de création de rapports Azure AD](active-directory-reporting-api-getting-started.md) |

Pour afficher les rapports qui sont inclus dans les différentes éditions d’Azure Active Directory, [cliquez ici](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Voir aussi
[Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Services de domaine Azure Active Directory](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
