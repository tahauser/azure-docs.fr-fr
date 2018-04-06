---
title: 'Manuel de preuve de concept Azure Active Directory : Blocs de construction | Microsoft Docs'
description: Explorer et implémenter rapidement des scénarios de gestion des identités et des accès
services: active-directory
keywords: azure active directory, manuel, preuve de concept, POC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: 1efb8d89b0a78dcf88c60c2e8cd3b968a725e8b9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Manuel de preuve de concept Azure Active Directory : Blocs de construction

## <a name="catalog-of-roles"></a>Catalogue des rôles

| Rôle | Description | Responsabilité de la preuve de concept (PoC) |
| --- | --- | --- |
| **Équipe Architecture d’identités/Développement** | Cette équipe est généralement celle qui conçoit la solution, implémente les prototypes, gère les approbations et transmet les informations à l’équipe des opérations | Fournir les environnements et évaluer les différents scénarios du point de vue de la facilité de gestion |
| **Équipe des opérations d’identité locale** | Gère les différentes sources d’identité en local : forêts Active Directory, annuaires LDAP, systèmes de RH et fournisseurs d’identité de fédération. | Fournir un accès aux ressources locales requises pour les scénarios POC.<br/>Elle doit intervenir aussi peu que possible|
| **Propriétaires techniques des applications** | Propriétaires techniques des applications et services cloud qui s’intègrent dans Azure AD | Fournir des informations sur les applications IaaS (éventuellement, instances de test) |
| **Administrateur général Azure AD** | Gère la configuration Azure AD | Fournir des informations d’identification pour configurer le service de synchronisation. Généralement, il s’agit de la même équipe que l’équipe Architecture d’identités lors de la POC, mais d’une équipe distincte pendant la phase des opérations|
| **Équipe de base de données** | Propriétaires de l’infrastructure de base de données | Fournir un accès à l’environnement SQL (ADFS ou Azure AD Connect) pour la préparation de scénarios spécifiques.<br/>Elle doit intervenir aussi peu que possible |
| **Équipe réseau** | Propriétaires de l’infrastructure réseau | Fournir l’accès requis au niveau du réseau pour que les serveurs de synchronisation accèdent aux sources de données et aux services cloud (règles de pare-feu, ports ouverts, règles IPSec, etc.) |
| **Équipe de sécurité** | Définit la stratégie de sécurité, analyse les rapports de sécurité issus de différentes sources et donne suite aux résultats. | Fournir des scénarios d’évaluation de sécurité cibles |

## <a name="common-prerequisites-for-all-building-blocks"></a>Configuration requise commune à tous les blocs de construction

Voici quelques conditions préalables pour toute POC avec Azure AD Premium.

| Conditions préalables | Ressources |
| --- | --- |
| Locataire Azure AD défini avec un abonnement Azure valide | [Obtention d’un client Azure Active Directory](active-directory-howto-tenant.md)<br/>**Remarque :** si vous disposez déjà d’un environnement avec licences Azure AD Premium, vous pouvez obtenir un abonnement gratuit sur https://aka.ms/accessaad. <br/>Pour en savoir plus : https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ et https://technet.microsoft.com/library/dn832618.aspx |
| Domaines définis et vérifiés | [Ajouter un nom de domaine personnalisé à Azure Active Directory](active-directory-domains-add-azure-portal.md)<br/>**Remarque :** certaines charges de travail telles que Power BI peuvent avoir approvisionné un locataire Azure AD en arrière-plan. Pour voir si un domaine donné est associé à un client, accédez à https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Si vous obtenez une réponse correcte, cela signifie que le domaine est déjà affecté à un locataire, et une prise de contrôle peut être requise. Dans ce cas, contactez Microsoft pour obtenir des instructions supplémentaires. En savoir plus sur les options de prise de contrôle : [Qu’est-ce qu’une inscription libre-service à Azure ?](active-directory-self-service-signup.md) |
| Essai Azure AD Premium ou EMS activé | [Azure Active Directory Premium gratuit pendant un mois](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Vous avez affecté des licences Azure AD Premium ou EMS à des utilisateurs POC | [License yourself and your users in Azure Active Directory (Accorder une licence à vos utilisateurs et à vous-même dans Azure Active Directory)](active-directory-licensing-get-started-azure-portal.md) |
| Informations d’identification de l’administrateur général Azure AD | [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md) |
| Facultatif, mais vivement recommandé : environnement de laboratoire parallèle comme solution de secours | [Conditions préalables pour Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Synchronisation des répertoires - Synchronisation du code de hachage de mots de passe (PHS) - Nouvelle installation

Temps approximatif d’accomplissement : une heure pour moins de 1 000 utilisateurs POC

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Serveur pour exécuter Azure AD Connect | [Conditions préalables pour Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) |
| Utilisateurs POC cibles, dans le même domaine et faisant partie d’un groupe de sécurité, et unité d’organisation | [Installation personnalisée d’Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Les fonctionnalités Azure AD Connect requises pour la POC sont identifiées | [Connecter Active Directory à Azure Active Directory - Configuration de fonctionnalités de synchronisation](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Vous disposez des informations d’identification nécessaires pour les environnements locaux et cloud  | [Autorisations et comptes Azure AD Connect](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Téléchargez la dernière version d’Azure AD Connect | [Téléchargez Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) |
| Installez Azure AD Connect de la manière la plus simple : Express <br/>1. Filtrez les données en fonction de l’unité d’organisation cible afin de réduire le cycle de synchronisation<br/>2. Choisissez le jeu d’utilisateurs cible dans le groupe local<br/>3. Déployez les fonctionnalités requises par les autres thèmes POC | [Azure AD Connect : Installation personnalisée : Filtrage domaine et unité organisationnelle](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect : Installation personnalisée : Filtrage de synchronisation basé sur les groupes](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect : Intégration de vos identités locales avec Azure Active Directory : Configuration de fonctionnalités de synchronisation](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Ouvrez l’interface utilisateur Azure AD Connect et observez les profils d’exécution terminés (importation, synchronisation et exportation) | [Planificateur Azure AD Connect Sync](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Ouvrez le [portail de gestion Azure AD](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), accédez au panneau Tous les utilisateurs et ajoutez la colonne Source d’autorité. Les utilisateurs s’affichent avec l’indication de leur origine : Windows Server AD. | [Portail de gestion Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Considérations

1. Examinez les considérations de sécurité liées à la synchronisation du hachage de mot de passe [ici](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).  Si la synchronisation du hachage de mot de passe pour les utilisateurs de production pilotes ne convient pas, envisagez les options suivantes :
   * Créez des utilisateurs de test dans le domaine de production. Assurez-vous de ne synchroniser aucun autre compte.
   * Passez à un environnement UAT.
2.  Si vous souhaitez poursuivre la fédération, il est important de bien comprendre que les coûts associés à une solution fédérée avec un fournisseur d’identité local au-delà de la POC et évaluez-les en fonction des avantages que vous recherchez :
    * Il s’agit là d’un élément critique : vous devez donc imaginer une conception haute disponibilité.
    * Vous avez besoin de ce service local pour le plan de capacité
    * Vous avez besoin de ce service local pour la surveillance/la maintenance/les correctifs

En savoir plus : [Présentation de l’identité Office 365 et d’Azure Active Directory - Identité fédérée](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Personnalisation

Temps approximatif d’accomplissement : 15 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Ressources (images, logos, etc.). Pour une meilleure visualisation, vérifiez que les ressources ont la taille recommandée. | [Add company branding to your sign-in page in the Azure Active Directory (Ajouter la personnalisation de votre société à votre page de connexion dans Azure Active Directory)](active-directory-branding-custom-signon-azure-portal.md) |
| Facultatif : si l’environnement dispose d’un serveur ADFS, accédez au serveur pour personnaliser le thème web | [Personnalisation de la connexion utilisateur AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Ordinateur client pour réaliser l’expérience de connexion de l’utilisateur final |  |
| Facultatif : appareils mobiles pour valider l’expérience |  |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Accédez au portail de gestion Azure AD | [Portail de gestion Azure AD - Marque de société](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Chargez les ressources correspondant à la page de connexion (bannière, petit logo, étiquettes, etc.). Éventuellement, si vous avez AD FS, alignez les mêmes ressources sur les pages de connexion AD FS | [Ajout de votre société à vos pages de connexion et du panneau d’accès : Éléments personnalisables](customize-branding.md) |
| Patientez quelques minutes pour que les modifications entrent en vigueur |  |
| Connectez-vous avec les informations d’identification d’utilisateur de la preuve de concept sur https://myapps.microsoft.com |  |
| Vérifiez l’apparence dans le navigateur | [Ajout de votre société à vos pages de connexion et du panneau d’accès](customize-branding.md) |
| Si vous le souhaitez, vérifiez l’apparence sur d’autres appareils |  |

### <a name="considerations"></a>Considérations

Si l’ancienne apparence subsiste après la personnalisation, videz le cache du client navigateur et recommencez l’opération.

## <a name="group-based-licensing"></a>Gestion des licences par groupe

Temps approximatif d’accomplissement : 10 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Tous les utilisateurs POC font partie d’un groupe de sécurité (cloud ou local) | [Créer un groupe et ajouter des membres dans Azure Active Directory](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Accédez au panneau de licences dans le portail de gestion Azure AD | [Portail de gestion Azure AD : Licences](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| Affectez les licences au groupe de sécurité comportant les utilisateurs POC. | [Affecter des licences à un groupe d’utilisateurs dans Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Considérations

En cas de problème, consultez [Scénarios, limitations et problèmes connus liés à l’utilisation de groupes dans le cadre de la gestion des licences dans Azure Active Directory](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>Configuration de l’authentification unique fédérée SaaS

Temps approximatif d’accomplissement : 60 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Environnement de test de l’application SaaS disponible. Dans ce guide, nous prenons pour exemple ServiceNow.<br/>Nous vous recommandons vivement d’utiliser une instance de test afin de limiter les problèmes de navigation concernant les mappages et la qualité des données existantes. | Accédez à https://developer.servicenow.com/app.do#!/home pour démarrer le processus d’obtention d’une instance de test |
| Accès administrateur à la console de gestion ServiceNow | [Didacticiel : Intégration d’Azure Active Directory à ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Jeu d’utilisateurs cible auquel affecter l’application. Un groupe de sécurité contenant les utilisateurs POC est recommandé. <br/>S’il est impossible de créer le groupe, affectez directement les utilisateurs à l’application pour la POC | [Assign a user or group to an enterprise app in Azure Active Directory (Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory)](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Partagez le didacticiel avec tous les acteurs de la documentation Microsoft  | [Didacticiel : Intégration d’Azure Active Directory à ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Organisez une réunion de travail et suivez les étapes du didacticiel avec chaque acteur. | [Didacticiel : Intégration d’Azure Active Directory à ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Affectez l’application au groupe identifié dans les conditions préalables. Si la POC dispose d’un accès conditionnel dans l’étendue, vous pouvez y revenir ultérieurement et ajouter une authentification MFA ou autre. <br/>Cela lancera le processus d’approvisionnement (s’il a été configuré) |  [Assign a user or group to an enterprise app in Azure Active Directory (Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory)](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Créer un groupe et ajouter des membres dans Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Utilisez le portail de gestion Azure AD pour ajouter l’application ServiceNow à partir de la galerie| [Portail de gestion Azure AD : Applications d’entreprise](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Nouveautés relatives à la gestion des applications d’entreprise dans Azure Active Directory](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| Dans le panneau « Authentification unique » de l’application ServiceNow, activez « Authentification basée sur SAML » |  |
| Complétez les champs URL de connexion et Identificateur avec votre URL ServiceNow<br/>Cochez la case Activer le nouveau certificat<br/>et enregistrez les paramètres |  |
| Ouvrez le panneau Configurer ServiceNow en bas du volet pour afficher les instructions personnalisées vous permettant de configurer ServiceNow |  |
| Suivez les instructions de configuration de ServiceNow |  |
| Dans le panneau Approvisionnement de l’application ServiceNow, activez l’approvisionnement Automatique | [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise dans le nouveau portail Azure](active-directory-enterprise-apps-manage-provisioning.md) |
| Patientez quelques minutes pour que la configuration se termine.  En attendant, vous pouvez vérifier les rapports d’approvisionnement |  |
| Connectez-vous à https://myapps.microsoft.com/ en tant qu’utilisateur de test ayant accès | [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md) |
| Cliquez sur la mosaïque de l’application qui vient d’être créée. Confirmez l’accès |  |
| Si vous le souhaitez, vous pouvez vérifier les rapports d’utilisation de l’application. Il existe un temps de latence : vous devez patienter quelques instants avant de voir le trafic dans les rapports. | [Rapports d’activité de connexion dans le portail Azure Active Directory : Utilisation des applications gérées](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Stratégies de rétention des rapports Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Considérations

1. Le [didacticiel](active-directory-saas-servicenow-tutorial.md) ci-dessus fait référence à l’ancienne expérience de gestion Azure AD. Mais la POC repose sur l’expérience [Démarrage rapide](active-directory-enterprise-apps-whats-new-azure-portal.md#quick-start-get-going-with-your-new-application-right-away).
2. Si l’application cible n’est pas présente dans la galerie, vous pouvez utiliser Apportez votre propre application. Pour en savoir plus : [Découvrez les nouveautés en matière de gestion des applications d’entreprise dans Azure Active Directory : Ajout d’applications personnalisées à partir d’un seul emplacement](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>Configuration de l’authentification unique par mot de passe SaaS

Temps approximatif d’accomplissement : 15 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Environnement de test pour les applications SaaS. HipChat et Twitter sont des exemples d’authentification unique par mot de passe. Pour toute autre application, vous avez besoin de l’URL exacte de la page avec le formulaire d’inscription html. | [Twitter sur la Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[HipChat sur la Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Testez les comptes pour les applications. | [S’inscrire sur Twitter](https://twitter.com/signup?lang=en)<br/>[S’inscrire gratuitement sur HipChat](https://www.hipchat.com/sign_up) |
| Jeu d’utilisateurs cible auquel affecter l’application. Un groupe de sécurité contenant les utilisateurs est recommandé. | [Assign a user or group to an enterprise app in Azure Active Directory (Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory)](active-directory-coreapps-assign-user-azure-portal.md) |
| Accès administrateur local à un ordinateur afin de déployer l’extension du volet d’accès pour Internet Explorer, Chrome ou Firefox | [Extension du volet d’accès pour IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extension du volet d’accès pour Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extension du volet d’accès pour Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Installez l’extension de navigateur | [Extension du volet d’accès pour IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extension du volet d’accès pour Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extension du volet d’accès pour Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configurez l’application à partir de la galerie | [Nouveautés en matière de gestion des applications d’entreprise dans Azure Active Directory : Galerie d’applications innovante et améliorée](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configurez l’authentification unique par mot de passe | [Gestion de l’authentification unique pour les applications d’entreprise dans le nouveau portail Azure : Authentification par mot de passe](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Affectez l’application au groupe identifié dans les conditions préalables | [Assign a user or group to an enterprise app in Azure Active Directory (Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory)](active-directory-coreapps-assign-user-azure-portal.md) |
| Connectez-vous à https://myapps.microsoft.com/ en tant qu’utilisateur de test ayant accès |  |
| Cliquez sur la mosaïque de l’application qui vient d’être créée. | [Présentation du volet d’accès : Authentification unique avec mot de passe sans approvisionnement d’identité](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Fournissez les informations d’identification de l’application | [Présentation du volet d’accès : Authentification unique avec mot de passe sans approvisionnement d’identité](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Fermez le navigateur et reconnectez-vous. Cette fois-ci, l’utilisateur doit avoir un accès transparent à l’application. |  |
| Si vous le souhaitez, vous pouvez vérifier les rapports d’utilisation de l’application. Il existe un temps de latence : vous devez patienter quelques instants avant de voir le trafic dans les rapports. | [Rapports d’activité de connexion dans le portail Azure Active Directory : Utilisation des applications gérées](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Stratégies de rétention des rapports Azure Active Directory](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Considérations

Si l’application cible n’est pas présente dans la galerie, vous pouvez utiliser Apportez votre propre application. Pour en savoir plus : [Découvrez les nouveautés en matière de gestion des applications d’entreprise dans Azure Active Directory : Ajout d’applications personnalisées à partir d’un seul emplacement](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Gardez à l’esprit les exigences suivantes :
   * L’application doit avoir une URL de connexion connue
   * La page de connexion doit contenir un formulaire HTML avec un ou plusieurs champs de texte que les extensions de navigateur peuvent remplir automatiquement. Au minimum, elle doit contenir le nom d’utilisateur et le mot de passe.

## <a name="saas-shared-accounts-configuration"></a>Configuration des comptes partagés SaaS

Temps approximatif d’accomplissement : 30 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Liste des applications cibles et URL de connexion exactes au préalable. Par exemple, vous pouvez utiliser Twitter. | [Twitter sur la Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[S’inscrire sur Twitter](https://twitter.com/signup?lang=en) |
| Informations d’identification partagées pour cette application SaaS. | [Partage de comptes à l’aide d’Azure AD](active-directory-sharing-accounts.md)<br/>[Version préliminaire de la substitution automatisée du mot de passe Azure AD pour Facebook, Twitter et LinkedIn ! – Blog Enterprise Mobility + Security] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Informations d’identification pour au moins deux membres de l’équipe qui ont le même compte d’accès. Ils doivent faire partie d’un groupe de sécurité. | [Assign a user or group to an enterprise app in Azure Active Directory (Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory)](active-directory-coreapps-assign-user-azure-portal.md) |
| Accès administrateur local à un ordinateur afin de déployer l’extension du volet d’accès pour Internet Explorer, Chrome ou Firefox | [Extension du volet d’accès pour IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extension du volet d’accès pour Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extension du volet d’accès pour Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Installez l’extension de navigateur | [Extension du volet d’accès pour IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Extension du volet d’accès pour Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Extension du volet d’accès pour Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Configurez l’application à partir de la galerie | [Nouveautés en matière de gestion des applications d’entreprise dans Azure Active Directory : Galerie d’applications innovante et améliorée](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Configurez l’authentification unique par mot de passe | [Gestion de l’authentification unique pour les applications d’entreprise dans le nouveau portail Azure : Authentification par mot de passe](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| Affectez l’application au groupe identifié dans les conditions préalables lorsque vous leur affectez des informations d’identification | [Assign a user or group to an enterprise app in Azure Active Directory (Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory)](active-directory-coreapps-assign-user-azure-portal.md) |
| Connectez-vous en tant qu’utilisateurs différents en mesure d’accéder à l’application par le biais d’un **même compte partagé.**  |  |
| Si vous le souhaitez, vous pouvez vérifier les rapports d’utilisation de l’application. Il existe un temps de latence : vous devez patienter quelques instants avant de voir le trafic dans les rapports. | [Rapports d’activité de connexion dans le portail Azure Active Directory : Utilisation des applications gérées](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Stratégies de rétention des rapports Azure Active Directory](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Considérations

Si l’application cible n’est pas présente dans la galerie, vous pouvez utiliser Apportez votre propre application. Pour en savoir plus : [Découvrez les nouveautés en matière de gestion des applications d’entreprise dans Azure Active Directory : Ajout d’applications personnalisées à partir d’un seul emplacement](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Gardez à l’esprit les exigences suivantes :
   * L’application doit avoir une URL de connexion connue
   * La page de connexion doit contenir un formulaire HTML avec un ou plusieurs champs de texte que les extensions de navigateur peuvent remplir automatiquement. Au minimum, elle doit contenir le nom d’utilisateur et le mot de passe.

## <a name="app-proxy-configuration"></a>Configuration du proxy d’application

Temps approximatif d’accomplissement : 20 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Abonnement Microsoft Azure AD de base ou Premium, et annuaire Azure AD pour lequel vous êtes administrateur général | [Éditions d’Azure Active Directory](active-directory-editions.md) |
| Application web hébergée localement et que vous souhaitez configurer pour un accès à distance |  |
| Serveur exécutant Windows Server 2012 R2 ou Windows 8.1 ou version ultérieure et sur lequel vous pouvez installer le connecteur de proxy d’application | [Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md) |
| Si un pare-feu existe, assurez-vous qu’il autorise les requêtes HTTPS (TCP) du connecteur au proxy d’application | [Activer le proxy d’application dans le portail Azure : Conditions préalables pour le proxy d’application](active-directory-application-proxy-enable.md#application-proxy-prerequisites) |
| Si votre organisation utilise des serveurs proxy pour se connecter à Internet, consultez le billet de blog Working with existing on-premises proxy servers (Utilisation de serveurs proxy locaux existants) pour découvrir comment les configurer | [Travailler avec des serveurs proxy locaux existants](application-proxy-working-with-proxy-servers.md) |


### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Installez un connecteur sur le serveur | [Activer le proxy d’application dans le portail Azure : Installer et inscrire le connecteur](active-directory-application-proxy-enable.md#install-and-register-a-connector) |
| Publiez l’application locale dans Azure AD en tant qu’application de proxy d’application | [Publier des applications avec le Proxy d’application Azure AD](application-proxy-publish-azure-portal.md) |
| Affectez des utilisateurs de test | [Publier des applications avec le proxy d’application Azure AD : Ajouter un utilisateur de test](application-proxy-publish-azure-portal.md#add-a-test-user) |
| Si vous le souhaitez, vous pouvez configurer une expérience d’authentification unique pour vos utilisateurs | [Fournir l’authentification unique à l’aide du proxy d’application Azure AD](application-proxy-sso-azure-portal.md) |
| Testez l’application en vous connectant au portail MyApps en tant qu’utilisateur affecté | https://myapps.microsoft.com |

### <a name="considerations"></a>Considérations

1. Nous vous suggérons de placer le connecteur dans votre réseau d’entreprise, mais il se peut que les performances soient meilleures si vous le placez dans le cloud. En savoir plus : [Considérations sur la topologie du réseau lors de l’utilisation du proxy d’application Azure Active Directory](application-proxy-network-topology-considerations.md)
2. Pour plus de détails sur la sécurité et pour savoir comment il fournit une solution d’accès à distance sécurisé par le seul maintien des connexions sortantes, consultez : [Considérations de sécurité pour l’accès aux applications à distance à l’aide du proxy d’application Azure AD](application-proxy-security-considerations.md)

## <a name="generic-ldap-connector-configuration"></a>Configuration du connecteur LDAP générique

Temps approximatif d’accomplissement : 60 minutes

> [!IMPORTANT]
> Cette configuration avancée suppose d’avoir quelques connaissances concernant FIM/MIM. En production, nous vous recommandons de soumettre au [Support Premier](https://support.microsoft.com/premier) toutes les questions liées à cette configuration.

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Azure AD Connect installé et configuré | Bloc de construction : [Synchronisation des répertoires - Synchronisation du code de hachage de mots de passe](#directory-synchronization--password-hash-sync-phs--new-installation) |
| Instance ADLDS répondant aux exigences | [Référence technique au connecteur LDAP générique : Vue d’ensemble du connecteur LDAP générique](./connect/active-directory-aadconnectsync-connector-genericldap.md#overview-of-the-generic-ldap-connector) |
| Liste des charges de travail dont les utilisateurs se servent et attributs associés à ces charges de travail | [Azure AD Connect Sync : Attributs synchronisés avec Azure Active Directory](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Ajoutez un connecteur LDAP générique | [Référence technique au connecteur LDAP générique : Créer un connecteur](./connect/active-directory-aadconnectsync-connector-genericldap.md#create-a-new-connector) |
| Créez des profils d’exécution pour le connecteur créé (importation complète, importation d’écart, synchronisation complète, synchronisation d’écart, exportation) | [Create a Management Agent Run Profile (Créer un profil d’exécution d’agent de gestion)](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Utilisation de connecteurs avec Azure AD Connect Sync Service Manager](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Exécutez le profil d’importation complète et vérifiez la présence d’objets dans l’espace de connecteur | [Search for a Connector Space Object (Rechercher un objet d’espace de connecteur)](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Utilisation de connecteurs avec Azure AD Connect Sync Service Manager : Espace de connecteur de recherche](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Créez des règles de synchronisation afin que les objets de métaverse aient les attributs nécessaires pour les charges de travail | [Azure AD Connect Sync : Meilleures pratiques pour modifier la configuration par défaut : Modifications apportées aux règles de synchronisation](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Azure AD Connect Sync : Présentation de l’approvisionnement déclaratif](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Azure AD Connect Sync : Présentation des expressions d’approvisionnement déclaratif](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Démarrez le cycle de synchronisation complète | [Planificateur Azure AD Connect Sync : Démarrer le planificateur](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| En cas de problème, procédez au dépannage | [Dépanner un objet qui bloque la synchronisation avec Azure AD](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Vérifiez qu’un utilisateur LDAP peut se connecter et accéder à l’application | https://myapps.microsoft.com |

### <a name="considerations"></a>Considérations

> [!IMPORTANT]
> Cette configuration avancée suppose d’avoir quelques connaissances concernant FIM/MIM. En production, nous vous recommandons de soumettre au [Support Premier](https://support.microsoft.com/premier) toutes les questions liées à cette configuration.

## <a name="groups---delegated-ownership"></a>Groupes : propriété déléguée

Temps approximatif d’accomplissement : 10 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| L’application SaaS (authentification unique fédérée ou authentification unique par mot de passe) a déjà été configurée | Bloc de construction : [Configuration de l’authentification unique fédérée SaaS](#saas-federated-sso-configuration) |
| Le groupe de cloud auquel l’accès à l’application est affecté en priorité est identifié | Bloc de construction : [Configuration de l’authentification unique fédérée SaaS](#saas-federated-sso-configuration) <br/>[Créer un groupe et ajouter des membres dans Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Les informations d’identification du propriétaire du groupe sont disponibles | [Gérer l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md) |
| Les informations d’identification du professionnel de l’information qui accède aux applications ont été identifiées | [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Identifiez le groupe autorisé à accéder à l’application et configurez le propriétaire de ce groupe| [Manage the settings for a group in Azure Active Directory (Gérer les paramètres des groupes dans Azure Active Directory)](active-directory-groups-settings-azure-portal.md) |
| Connectez-vous en tant que propriétaire du groupe et consultez l’appartenance au groupe dans l’onglet Groupes du volet d’accès | [Gérer l’accès aux ressources avec les groupes Azure Active Directory](https://account.activedirectory.windowsazure.com/r/#/groups) |
| Ajoutez le professionnel de l’information que vous souhaitez tester |  |
| Connectez-vous en tant que professionnel de l’information et confirmez la disponibilité de la mosaïque | [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Considérations

Si l’approvisionnement est activé pour l’application, vous devrez peut-être patienter pendant quelques minutes, le temps que l’approvisionnement se termine, avant d’accéder à l’application en tant que professionnel de l’information.

## <a name="saas-and-identity-lifecycle"></a>SaaS et cycle de vie des identités

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| L’application SaaS (authentification unique fédérée ou authentification unique par mot de passe) a déjà été configurée | Bloc de construction : [Configuration de l’authentification unique fédérée SaaS](#saas-federated-sso-configuration) |
| Le groupe de cloud auquel l’accès à l’application est affecté en priorité est identifié | Bloc de construction : [Configuration de l’authentification unique fédérée SaaS](#saas-federated-sso-configuration) <br/>[Créer un groupe et ajouter des membres dans Azure Active Directory](active-directory-groups-create-azure-portal.md) |
| Les informations d’identification du professionnel de l’information qui accède aux applications ont été identifiées | [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Supprimez l’utilisateur du groupe auquel l’application est affectée | [Gérer l’appartenance à un groupe des utilisateurs dans votre client Azure Active Directory](active-directory-groups-members-azure-portal.md) |
| Attendez quelques minutes que l’approvisionnement soit annulé | [Approvisionnement automatique des utilisateurs pour les applications SaaS dans Azure AD : Comment fonctionne l’approvisionnement automatique ?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| Dans une session distincte du navigateur, connectez-vous en tant que professionnel de l’information au portail MyApps et vérifiez l’absence de cette mosaïque | http://myapps.microsoft.com |


### <a name="considerations"></a>Considérations

Extrapolez le scénario POC aux travailleurs quittant la société et/ou partant en congés. Si l’utilisateur est désactivé dans AD local ou s’il est supprimé, il n’est plus possible de se connecter à l’application SaaS.

## <a name="self-service-access-to-application-management"></a>Accès en libre-service à la gestion des applications

Temps approximatif d’accomplissement : 10 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Identifiez les utilisateurs POC qui demandent un accès aux applications, en tant que membres du groupe de sécurité | Bloc de construction : [Configuration de l’authentification unique fédérée SaaS](#saas-federated-sso-configuration) |
| Application cible déployée | Bloc de construction : [Configuration de l’authentification unique fédérée SaaS](#saas-federated-sso-configuration) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Accédez au panneau Applications d’entreprise du portail de gestion Azure AD | [Portail de gestion Azure AD : Applications d’entreprise](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Configurez l’application à partir des conditions préalables avec le libre-service | [Nouveautés en matière de gestion des applications d’entreprise dans Azure Active Directory : Configuration de l’accès aux applications en libre-service](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Connectez-vous en tant que professionnel de l’information au portail MyApps | http://myapps.microsoft.com |
| Un bouton +Ajouter une application se situe en haut de la page. Ce bouton permet d’accéder à l’application |  |

### <a name="considerations"></a>Considérations

Les applications choisies peuvent présenter des exigences d’approvisionnement. L’accès immédiat à l’application peut donc provoquer des erreurs. Si l’application choisie prend en charge l’approvisionnement avec Azure AD et si elle est configurée, vous pouvez en profiter pour afficher le flux complet de bout en bout. Consultez le bloc de construction [Configuration de l’authentification unique fédérée SaaS](#saas-federated-sso-configuration) pour obtenir des recommandations supplémentaires

## <a name="self-service-password-reset"></a>Réinitialisation de mot de passe en libre-service

Temps approximatif d’accomplissement : 15 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Activez la gestion des mots de passe en libre-service dans votre locataire. | [Réinitialisation de mot de passe Azure Active Directory pour les administrateurs informatiques](active-directory-passwords-update-your-own-password.md) |
| Activez l’écriture différée de mot de passe pour gérer les mots de passe en local. Cela nécessite des versions spécifiques d’Azure AD Connect | [Configuration requise pour la réécriture du mot de passe](active-directory-passwords-writeback.md) |
| Identifiez les utilisateurs POC qui se serviront de cette fonctionnalité et vérifiez qu’ils sont bien membres d’un groupe de sécurité. Les utilisateurs doivent être des non-administrateurs pour présenter pleinement cette fonctionnalité | [Personnalisation : Gestion du mot de passe Azure AD : Limiter l’accès à la réinitialisation des mots de passe](active-directory-passwords-writeback.md) |


### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Accédez au portail de gestion Azure AD : Réinitialisation du mot de passe | [Portail de gestion Azure AD : Réinitialisation du mot de passe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Configurez la stratégie de réinitialisation du mot de passe. Dans le cadre de la POC, vous pouvez recourir à un appel téléphonique et aux questions et réponses. Il est recommandé d’activer l’inscription requise lors de la connexion au volet d’accès. |  |
| Déconnectez-vous et reconnectez-vous en tant que professionnel de l’information |  |
| Fournissez les données de réinitialisation de mot de passe en libre-service comme configuré à l’étape 2 | https://aka.ms/ssprsetup |
| Fermez le navigateur |  |
| Recommencez le processus de connexion en tant que professionnel de l’information, déjà utilisé à l’étape 4 |  |
| Réinitialisez le mot de passe | [Mettre à jour votre mot de passe : Réinitialiser mon mot de passe](active-directory-passwords-update-your-own-password.md) |
| Essayez de vous connecter avec votre nouveau mot de passe Azure AD, ainsi qu’aux ressources locales |  |

### <a name="considerations"></a>Considérations

1. Si la mise à niveau d’Azure AD Connect est susceptible d’engendrer des problèmes, pensez aux comptes cloud ou faites une démonstration par rapport à un environnement distinct
2. Les administrateurs disposent d’une stratégie différente, et l’utilisation du compte Administrateur pour réinitialiser le mot de passe peut altérer la POC et entraîner une confusion. Vérifiez que vous utilisez un compte d’utilisateur normal pour tester les opérations de réinitialisation


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Azure Multi-Factor Authentication avec les appels téléphoniques

Temps approximatif d’accomplissement : 10 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Identifiez les utilisateurs qui se serviront de l’authentification MFA  |  |
| Utilisez un téléphone disposant d’une bonne réception pour la demande MFA  | [Présentation d'Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Accédez au panneau Utilisateurs et groupes dans le portail de gestion Azure AD | [Portail de gestion Azure AD : Utilisateurs et groupes](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Sélectionnez le panneau Tous les utilisateurs |  |
| Dans la barre supérieure, sélectionnez le bouton Multi-Factor Authentication | URL directe du portail Azure MFA : https://aka.ms/mfaportal |
| Dans les paramètres Utilisateur, sélectionnez les utilisateurs POC et activez-les pour l’authentification MFA | [États d’utilisateur dans Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) |
| Connectez-vous en tant qu’utilisateur POC et suivez le processus de vérification  |  |

### <a name="considerations"></a>Considérations

1. Les étapes de la POC dans ce bloc de construction configurent explicitement l’authentification MFA pour un utilisateur sur toutes les connexions. Il existe d’autres outils, tels que l’accès conditionnel et la protection d’identité, qui sollicitent l’authentification MFA sur plusieurs scénarios ciblés. C’est à prendre en compte lors du passage de la POC en production.
2. Les étapes de la POC dans ce bloc de construction utilisent explicitement les appels téléphoniques en tant que méthode MFA pour plus de commodité. Lorsque vous passez de la POC à la production, nous vous recommandons d’utiliser des applications telles que [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) en tant que second facteur dans la mesure du possible.
En savoir plus : [DRAFT NIST Special Publication 800-63B (Publication spéciale NIST 800-63B - version préliminaire)](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Accès conditionnel MFA pour les applications SaaS

Temps approximatif d’accomplissement : 10 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Identifiez les utilisateurs POC pour cibler la stratégie. Ces utilisateurs doivent appartenir à un groupe de sécurité pour l’extension de la stratégie d’accès conditionnel | [Configuration de l’authentification unique fédérée SaaS](#saas-federated-sso-configuration) |
| L’application SaaS a déjà été configurée |  |
| Les utilisateurs POC sont déjà affectés à l’application |  |
| Les informations d’identification de l’utilisateur POC sont disponibles |  |
| L’utilisateur POC est inscrit pour l’authentification MFA. Utilisation d’un téléphone disposant d’une bonne réception | https://aka.ms/ssprsetup |
| Appareil dans le réseau interne. Adresse IP configurée dans la plage d’adresses interne | Trouvez votre adresse IP : https://www.bing.com/search?q=what%27s+my+ip |
| Appareil du réseau externe (éventuellement, téléphone qui utilise le réseau mobile de l’opérateur) |  |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Accédez au panneau Accès conditionnel du portail de gestion Azure AD | [Portail de gestion Azure AD : Accès conditionnel](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Créez une stratégie d’accès conditionnel :<br/>- Ciblez les utilisateurs POC sous Utilisateurs et groupes<br/>- Ciblez l’application POC sous Applications cloud<br/>- Ciblez tous les emplacements, à l’exception des emplacements approuvés, sous Conditions > Emplacements **Remarque :** les adresses IP de confiance sont configurées dans le [portail MFA](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>- Demandez l’authentification MFA sous Autoriser | [Prise en main de l’accès conditionnel dans Azure Active Directory : Configuration de la stratégie](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Accédez à l’application à partir de l’intérieur du réseau d’entreprise | [Prise en main de l’accès conditionnel dans Azure Active Directory : Test de la stratégie](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Accédez à l’application à partir du réseau public | [Prise en main de l’accès conditionnel dans Azure Active Directory : Test de la stratégie](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Considérations

Si vous utilisez la fédération, vous pouvez vous servir du fournisseur d’identité local pour communiquer l’état interne/externe du réseau d’entreprise avec des revendications. Vous pouvez utiliser cette technique sans avoir à gérer la liste des adresses IP qui peuvent être difficiles à évaluer et à gérer dans les grandes organisations. Dans cette configuration, vous devez tenir compte du scénario d’itinérance de réseau (un utilisateur se connecte à partir du réseau interne et, pendant sa connexion, change de lieu pour aller dans un café, par exemple) et assurez-vous que vous comprenez bien toutes les implications. En savoir plus : [Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS : Adresses IP de confiance pour les utilisateurs fédérés](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Temps approximatif d’accomplissement : 15 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Identifiez l’administrateur général qui fera partie de la POC pour PIM | [Commencer à utiliser Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md) |
| Identifiez l’administrateur général qui deviendra l’administrateur de sécurité | [Commencer à utiliser Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md)<br/> [Rôles d’administrateur différent dans Azure Active Directory PIM](active-directory-privileged-identity-management-roles.md) |
| Facultatif : vérifiez si les administrateurs généraux ont accès à leur messagerie électronique pour leur envoyer des notifications électroniques dans PIM | [Qu’est-ce qu’Azure AD Privileged Identity Management ? : Configurer les paramètres d'activation de rôle](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Connectez-vous à https://portal.azure.com en tant qu’administrateur général et accédez au panneau PIM. Le rôle d’administrateur de sécurité est affecté à l’administrateur général qui exécute cette étape.  Appelons cet acteur GA1 | [Utilisation de l’Assistant Sécurité d’Azure AD Privileged Identity Management](active-directory-privileged-identity-management-security-wizard.md) |
| Identifiez l’administrateur général et faites-le passer du statut permanent au statut admissible. Pour plus de clarté, il doit s’agit d’un administrateur distinct de celui de l’étape 1. Appelons cet acteur GA2 | [Azure AD Privileged Identity Management : comment ajouter ou supprimer un rôle d’utilisateur](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Qu’est-ce qu’Azure AD Privileged Identity Management ? : Configurer les paramètres d'activation de rôle](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Maintenant, connectez-vous à https://portal.azure.com en tant que GA2 et essayez de modifier « Paramètres utilisateur ». Certaines options sont grisées. | |
| Dans un nouvel onglet et sous la même session qu’à l’étape 3, accédez maintenant à https://portal.azure.com et ajoutez le panneau PIM au tableau de bord. | [Comment activer ou désactiver des rôles dans Azure AD Privileged Identity Management : Ajout de l’application Privileged Identity Management](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| Demandez l’activation pour le rôle d’Administrateur général | [Comment activer ou désactiver des rôles dans Azure AD Privileged Identity Management : Activer un rôle](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Si GA2 ne s’est jamais inscrit pour l’authentification MFA, l’inscription pour l’authentification Azure MFA est requise |  |
| Retournez à l’onglet d’origine de l’étape 3, puis cliquez sur le bouton Actualiser du navigateur. Vous pouvez maintenant modifier les paramètres utilisateur | |
| Éventuellement, si la messagerie électronique de vos administrateurs généraux est activée, vous pouvez vérifier la boîte de réception de GA1 et de GA2, et consultez la notification du rôle activé |  |
| Vérifiez l’historique d’audit et consultez le rapport pour confirmer l’affichage de l’élévation de GA2. | [Qu’est-ce qu’Azure AD Privileged Identity Management ? : Passer en revue les activités de rôle](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Considérations

Cette fonctionnalité fait partie d’Azure AD Premium P2 et/ou EMS E5

## <a name="discovering-risk-events"></a>Détecter les événements à risque

Temps approximatif d’accomplissement : 20 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Appareil avec navigateur Tor téléchargé et installé | [Téléchargez le navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Accédez à l’utilisateur POC pour procéder à la connexion | [Manuel d’Azure Active Directory Identity Protection](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Ouvrez le navigateur Tor | [Téléchargez le navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Connectez-vous à https://myapps.microsoft.com avec le compte d’utilisateur de la preuve de concept | [Manuel d’Azure Active Directory Identity Protection : Simulation des événements à risque](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Patientez entre 5 et 7 minutes |  |
| Connectez-vous à https://portal.azure.com en tant qu’administrateur général et ouvrez le panneau Protection d’identité | https://aka.ms/aadipgetstarted |
| Ouvrez le panneau Événements à risque. Une entrée doit s’afficher sous Connexions depuis des adresses IP anonymes  | [Manuel d’Azure Active Directory Identity Protection : Simulation des événements à risque](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Considérations

Cette fonctionnalité fait partie d’Azure AD Premium P2 et/ou EMS E5

## <a name="deploying-sign-in-risk-policies"></a>Déployer des stratégies de risque de connexion

Temps approximatif d’accomplissement : 10 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Appareil avec navigateur Tor téléchargé et installé | [Téléchargez le navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Endossez le rôle d’utilisateur POC pour effectuer la connexion test |  |
| L’utilisateur POC est inscrit avec une authentification MFA. Assurez-vous d’utiliser un téléphone disposant d’une bonne réception | Bloc de construction : [Azure Multi-Factor Authentication avec les appels téléphoniques](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Connectez-vous à https://portal.azure.com en tant qu’administrateur général et ouvrez le panneau Protection d’identité | https://aka.ms/aadipgetstarted |
| Activez une stratégie en matière de risque à la connexion de la manière suivante :<br/>- Affectée à : utilisateur POC<br/>- Conditions : Risque à la connexion moyen ou élevé (une connexion à partir d’un emplacement anonyme est associée à un niveau de risque moyen)<br/>- Contrôles : demandez l’authentification MFA | [Manuel d’Azure Active Directory Identity Protection : Risque à la connexion](active-directory-identityprotection-playbook.md) |
| Ouvrez le navigateur Tor | [Téléchargez le navigateur Tor](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Connectez-vous à https://myapps.microsoft.com avec le compte d’utilisateur de la preuve de concept |  |
| Notez la demande MFA | [Expériences de connexion avec Azure AD Identity Protection : Récupération de connexion à risque](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Considérations

Cette fonctionnalité fait partie d’Azure AD Premium P2 et/ou EMS E5. Pour en savoir plus sur les événements à risque, consultez [Événements à risque dans Azure Active Directory](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Configurer l’authentification par certificat

Temps approximatif d’accomplissement : 20 minutes

### <a name="pre-requisites"></a>Conditions préalables

| Conditions préalables | Ressources |
| --- | --- |
| Appareil avec certificat utilisateur alloué (Windows, iOS ou Android) à partir de la PKI d’entreprise | [Déployer des certificats d’utilisateur](https://msdn.microsoft.com/library/cc770857.aspx) |
| Domaine Azure AD fédéré avec ADFS | [Fédération avec Azure AD Connect](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Vue d’ensemble des services de certificats Active Directory](https://technet.microsoft.com/library/hh831740.aspx)|
| Pour les appareils iOS sur lesquels l’application Microsoft Authenticator est installée | [Prise en main de l’application Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Étapes

| Étape | Ressources |
| --- | --- |
| Activez l’authentification de certificat sur ADFS | [Configurer des stratégies d’authentification : Pour configurer l’authentification principale globalement dans Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Facultatif : Activez l’authentification par certificat dans Azure AD pour les clients Exchange Active Sync | [Bien démarrer avec l’authentification par certificat dans Azure Active Directory](active-directory-certificate-based-authentication-get-started.md) |
| Accédez au volet d’accès et authentifiez-vous à l’aide du certificat utilisateur | https://myapps.microsoft.com |

### <a name="considerations"></a>Considérations

Pour en savoir plus sur les mises en garde de ce déploiement, consultez : [ADFS: Certificate Authentication with Azure AD & Office 365 (ADFS : Authentification par certificat avec Azure AD et Office 365)](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> La possession du certificat utilisateur doit être protégée. Soit par la gestion des appareils ou avec un code confidentiel dans le cas des cartes à puce.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
