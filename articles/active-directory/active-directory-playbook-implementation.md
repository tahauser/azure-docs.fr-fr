---
title: "Implémentation du manuel de preuve de concept Azure Active Directory | Microsoft Docs"
description: "Explorer et implémenter rapidement des scénarios de gestion des identités et des accès"
services: active-directory
keywords: azure active directory, manuel, preuve de concept, PoC
documentationcenter: 
author: dstefanMSFT
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: e26dfe4aaa374f5587038a0de66c0bd8703c9a41
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-proof-of-concept-playbook-implementation"></a>Manuel de preuve de concept Azure Active Directory : Implémentation

## <a name="foundation---syncing-ad-to-azure-ad"></a>Base : synchronisation d’Active Directory à Azure AD 

Une identité hybride constitue le fondement de la plupart des clients d’entreprise qui disposent déjà d’un répertoire local. L’objectif ici est volontairement de présenter la valeur de l’identité réelle et des scénarios d’accès en un minimum de temps. 

| Scénario | Blocs de construction| 
| --- | --- |  
| [Étendre une identité locale sur le cloud](#extending-your-on-premises-identity-to-the-cloud) | [Synchronisation des répertoires - synchronisation du code de hachage de mots de passe](active-directory-playbook-building-blocks.md#directory-synchronization---password-hash-sync-phs---new-installation) <br/>**Remarque** : cette étape est facultative si vous disposez déjà de DirSync/ADSync ou de versions antérieures d’Azure AD Connect. Certains scénarios de ce guide peuvent nécessiter une version plus récente d’Azure AD Connect.  <br/>[Personnalisation](active-directory-playbook-building-blocks.md#branding) | 
| [Affecter des licences Azure AD avec des groupes](#assigning-azure-ad-licenses-using-groups) | [Gestion des licences par groupe](active-directory-playbook-building-blocks.md#group-based-licensing) |


### <a name="extending-your-on-premises-identity-to-the-cloud"></a>Étendre une identité locale sur le cloud 

1. Marc est l’administrateur Active Directory de Contoso. Il a pour mission d’activer l’identité en tant que service pour un ensemble d’utilisateurs. Après l’exécution de l’Assistant Azure AD Connect, l’identité des utilisateurs cibles sera disponible dans le cloud. 
2. Marc demande à Julie, qui figure parmi les utilisateurs cibles, de naviguer jusqu’au panneau d’accès d’Azure Active Directory et de vérifier si elle peut s’authentifier. Julie voit une page de connexion personnalisée et un panneau d’accès vide, prêt à activer l’accès aux applications futures.

### <a name="assigning-azure-ad-licenses-using-groups"></a>Affecter des licences Azure AD avec des groupes 

1. Marc, l’administrateur global d’Azure AD, veut attribuer des licences Azure AD à un ensemble spécifique d’utilisateurs dans le cadre du déploiement initial d’Azure AD.
2. Il crée un groupe pour les utilisateurs pilotes. 
3. Il affecte les licences au groupe
4. Julie, qui travaille dans l’information, est ajoutée au groupe de sécurité dans le cadre de ses fonctions professionnelles
5. Après un certain temps, Julie a accès à la licence premium d’Azure AD. Cela ouvrira par la suite la voie à d’autres scénarios POC.

## <a name="theme---lots-of-apps-one-identity"></a>Thème : un grand nombre d’applications, une seule identité

| Scénario | Blocs de construction| 
| --- | --- |  
| [Intégrer des applications SaaS : authentification unique fédérée](#integrate-saas-applications---federated-sso) | [Configuration de l’authentification unique fédérée SaaS](active-directory-playbook-building-blocks.md#saas-federated-sso-configuration) <br/>[Groupes : propriété déléguée](active-directory-playbook-building-blocks.md#groups---delegated-ownership) |
| [Intégrer des applications SaaS : authentification unique par mot de passe](#integrate-saas-applications---password-sso) | [Configuration de l’authentification unique par mot de passe SaaS](active-directory-playbook-building-blocks.md#saas-password-sso-configuration) |
| [Authentification unique et événements du cycle de vie des identités](#sso-and-identity-lifecycle-events) | [SaaS et cycle de vie des identités](active-directory-playbook-building-blocks.md#saas-and-identity-lifecycle) |
| [Sécuriser l’accès aux comptes partagés](#secure-access-to-shared-accounts) | [Configuration des comptes partagés SaaS](active-directory-playbook-building-blocks.md#saas-shared-accounts-configuration) |
| [Accès distant sécurisé à des applications locales](#secure-remote-access-to-on-premises-applications) | [Configuration du proxy d’application](active-directory-playbook-building-blocks.md#app-proxy-configuration) |
| [Synchroniser les identités LDAP sur Azure AD](#synchronize-ldap-identities-to-azure-ad) |  [Configuration du connecteur LDAP générique](active-directory-playbook-building-blocks.md#generic-ldap-connector-configuration) |

### <a name="integrate-saas-applications---federated-sso"></a>Intégrer des applications SaaS : authentification unique fédérée 

1. Marc, l’administrateur global d’Azure AD, reçoit une demande du département Marketing concernant l’activation de l’accès à leur instance ServiceNow. Marc trouve le didacticiel pas à pas dans la documentation Azure AD, le suit, puis délègue à Kevin, le responsable de l’équipe Marketing, la charge d’affecter les utilisateurs à l’application. 
2. Kevin se connecte en tant que propriétaire des droits ServiceNow et assigne Julie à l’application. Kevin remarque également que le profil de Julie a été créé automatiquement dans ServiceNow
3. Julie travaille dans l’information pour le département Marketing. Elle se connecte à Azure AD et voit toutes les applications SaaS qui lui ont été affectées dans le portail myapps. De là, elle obtient un accès en toute transparence à ServiceNow.
4. Le département Marketing souhaite effectuer un audit pour déterminer qui a accédé à ServiceNow. Marc télécharge un rapport d’activité et le partage avec Kevin par e-mail.  

### <a name="sso-and-identity-lifecycle-events"></a>Authentification unique et événements du cycle de vie des identités

1. Julie part en congé ; conformément à la stratégie de l’entreprise, son compte AD local est temporairement désactivé. Julie ne peut plus se connecter à Azure AD et, par conséquent, n’a plus accès à ServiceNow. 
2. Julie est mutée du département Marketing au service commercial. Kevin supprime son accès de ServiceNow. Julie se connecte au portail myapps d’Azure AD et ne voit plus la mosaïque ServiceNow. Après 10 minutes, Kevin confirme que le compte de Julie a été désactivé à partir de la console de gestion de ServiceNow.

### <a name="integrate-saas-applications---password-sso"></a>Intégrer des applications SaaS : authentification unique par mot de passe

1. Marc configure l’accès à Atlassian HipChat. HipChat intègre l’authentification unique du mot de passe et accorde l’accès à Julie
2. Julie se connecte au portail myapps et voit un lien permettant de télécharger l’extension du navigateur IE Azure AD, qu’elle télécharge
3. En cliquant sur le lien, elle est invitée à saisir ses informations d’identification (son nom d’utilisateur et son mot de passe) HipChat. Il s’agit d’une opération ponctuelle qui, une fois exécutée, lui permet d’accéder à HipChat
4. Quelques jours plus tard, Julie ouvre le portail myapps et clique de nouveau sur HipChat. Cette fois-ci, elle obtient un accès transparent
5. Kevin, le propriétaire de l’application HipChat, souhaite vérifier qui a accédé à l’application. Marc télécharge un rapport d’audit et le partage avec Kevin par e-mail. 

### <a name="secure-access-to-shared-accounts"></a>Sécuriser l’accès aux comptes partagés 

1. Marc est chargé de sécuriser le pseudo Twitter partagé pour les membres de l’équipe commerciale. Il ajoute Twitter en tant qu’application SSO et l’attribue au groupe de sécurité de l’équipe commerciale. Il a obtenu les informations d’identification pour ce compte et les renseigne dans le système. 
2. Le partage des informations d’identification Twitter n’est plus fiable car plusieurs personnes les connaissent. Marc active la substitution automatique du mot de passe Twitter.
3. Julie, membre de l’équipe commerciale, se connecte au portail myapps et voit un lien permettant de télécharger l’extension du navigateur IE Azure AD. Elle l’installe.
4. Lorsqu’elle clique sur le lien, elle accède directement à Twitter. Elle ne connaît pas le mot de passe.
5. Patrick fait également partie de l’équipe commerciale. Il a rencontré le même problème que Julie aux étapes 3 et 4
6. Le service commercial souhaite effectuer un audit pour déterminer qui a accédé à Twitter. Marc télécharge un rapport d’activité et le partage avec Kevin par e-mail. 

### <a name="secure-remote-access-to-on-premises-applications"></a>Accès distant sécurisé à des applications locales

1. Marc, administrateur global d’Azure AD, a reçu de nombreuses demandes visant à permettre aux employés travaillant à distance d’accéder à plusieurs ressources locales utiles, comme l’application de notes de frais. Il suit la [documentation du proxy d’application](active-directory-application-proxy-enable.md) pour installer un connecteur et publier les notes de frais comme application du proxy d’application. 
2. Marc partage l’URL externe de l’application Notes de frais avec Julie, une employée qui a besoin d’un accès à distance. Elle accède au lien, et une fois authentifiée à AAD, elle est en mesure d’accéder à l’application Notes de frais et de continuer à travailler à distance. 
3. Marc continue de publier d’autres applications en local en suivant le même processus et en donnant l’accès aux utilisateurs en fonction des besoins. Pour des raisons de sécurité, il ajoute un accès conditionnel et une authentification multifacteur pour les applications plus sensibles qu’il publie.

### <a name="synchronize-ldap-identities-to-azure-ad"></a>Synchroniser les identités LDAP sur Azure AD

1. La société de Marc repose sur une infrastructure d’identité complexe. La plupart des utilisateurs sont maintenus dans Windows Server Active Directory Domain Services (ADDS). Certains d’entre eux sont gérés par le système RH dans Active Directory Lightweight Directory Services (ADLDS).
2. Marc est chargé d’activer l’accès aux applications SaaS pour tous les utilisateurs (également ceux qui ne figurent pas dans ADDS).
3. Marc configure le connecteur LDAP générique pour extraire les données d’ADLDS dans Azure AD Connect.
4. Il crée des règles de synchronisation pour que les utilisateurs LDAP apparaissent dans Metaverse et dans Azure AD
5. Julie est un utilisateur LDAP ; elle accède à son application SaaS à l’aide de son identité synchronisée



> [!IMPORTANT] 
> Cette configuration avancée suppose d’avoir quelques connaissances de FIM/MIM. En production, nous vous recommandons de soumettre au [Support Premier](https://support.microsoft.com/premier) toutes les questions concernant cette configuration.



## <a name="theme---increase-your-security"></a>Thème : augmenter la sécurité 

| Scénario | Blocs de construction| 
| --- | --- |  
| [Sécuriser l’accès au compte Administrateur](#secure-administrator-account-access) | [Azure MFA avec appels téléphoniques](active-directory-playbook-building-blocks.md#azure-multi-factor-authentication-with-phone-calls) |
| [Accès sécurisé pour les applications](#secure-access-to-applications) | [Accès conditionnel pour les applications SaaS](active-directory-playbook-building-blocks.md#mfa-conditional-access-for-saas-applications) |
| [Activer l’administration juste-à-temps](#enable-just-in-time-jit-administration) | [Privileged Identity Management](active-directory-playbook-building-blocks.md#privileged-identity-management-pim) |
| [Protéger les identités en fonction du risque](#protect-identities-based-on-risk) | [Détecter les événements à risque](active-directory-playbook-building-blocks.md#discovering-risk-events) <br/>[Déployer des stratégies de risque de connexion](active-directory-playbook-building-blocks.md#deploying-sign-in-risk-policies) |
| [S’authentifier sans mots de passe avec l’authentification par certificat](#authenticate-without-passwords-using-certificate-based-authentication) | [Configurer l’authentification par certificat](active-directory-playbook-building-blocks.md#configuring-certificate-based-authentication)

### <a name="secure-administrator-account-access"></a>Sécuriser l’accès au compte Administrateur

1. Marc est l’administrateur global d’Azure AD. Il a identifié Pierre comme coadministrateur du service. 
2. Marc configure le compte de Pierre pour toujours demander l’authentification multifacteur afin d’améliorer la sécurité
3. Pierre se connecte au portail Azure et constate qu’il doit enregistrer son numéro de téléphone pour poursuivre la connexion
4. Les prochaines connexions de Pierre seront désormais protégées par l’authentification multifacteur et Pierre reçoit maintenant un appel téléphonique pour vérifier son identité.

### <a name="secure-access-to-applications"></a>Sécuriser l’accès aux applications

1. Kevin est le gérant de ServiceNow. La société veut désormais que ces utilisateurs se connectent avec l’authentification multifacteur pour tout accès effectué à l’extérieur du réseau d’entreprise.
2. Marc, notre administrateur global Azure AD, ajoute une stratégie d’accès conditionnel à l’application ServiceNow pour activer l’authentification multifacteur pour les accès externes
3. Julie, notre employée de l’information, se connecte au portail myapps et clique sur la mosaïque ServiceNow. Elle doit désormais se connecter avec l’authentification multifacteur.

### <a name="enable-just-in-time-jit-administration"></a>Activer l’administration juste-à-temps (JIT)

1. Marc et Pierre sont les administrateurs globaux d’Azure Active Directory. Ils souhaitent activer l’accès JIT aux rôles de gestion tout en conservant des enregistrements sur l’utilisation des rôles privilégiés.
2. Marc active PIM dans le locataire Azure AD et devient l’administrateur de sécurité. Il modifie, pour Pierre et lui-même, leur appartenance au rôle d’administrateur global pour passer de l’état permanent à éligible.
3. Marc et Pierre doivent maintenant activer leur rôle via le portail Azure avant de pouvoir procéder à des modifications dans la configuration Azure AD. 

### <a name="protect-identities-based-on-risk"></a>Protéger les identités en fonction du risque 

1. Julie, une employée de l’information, tente de se connecter à partir d’un navigateur tor. 
2. Marc vérifie le tableau de bord Azure AD Identity Protection et voit que Julie est connectée à partir d’une adresse IP anonyme. L’équipe de sécurité tient à ce que l’accès de ces utilisateurs soit soumis à une authentification multifacteur
3. Marc active la stratégie Azure AD Identity Protection pour imposer l’authentification multifacteur pour les événements présentant un risque moyen ou élevé
4. Le temps passe et Julie se connecte de nouveau à partir du navigateur Tor. Cette fois, elle reçoit la question de l’authentification multifacteur

### <a name="authenticate-without-passwords-using-certificate-based-authentication"></a>S’authentifier sans mots de passe avec l’authentification par certificat

1. Marc est l’administrateur global d’une institution financière qui interdit l’utilisation de mots de passe comme facteur d’authentification pour l’accès à ses applications.
2. Marc active et met en œuvre l’authentification par certificat sur ADFS et Azure AD
3. En accédant à une application, Julie est invitée à s’authentifier à l’aide du certificat

## <a name="theme---scale-with-self-service"></a>Thème : mettre à l’échelle avec le libre-service

| Scénario | Blocs de construction| 
| --- | --- |  
| [Réinitialisation de mot de passe en libre-service](#self-service-password-reset) | [Réinitialisation de mot de passe en libre-service](active-directory-playbook-building-blocks.md#self-service-password-reset) |
| [Accès en libre-service aux applications](#self-service-access-to-applications) | [Accès en libre-service aux applications](active-directory-playbook-building-blocks.md#self-service-access-to-application-management) |

### <a name="self-service-password-reset"></a>Réinitialisation de mot de passe en libre-service 

1. Marc est l’administrateur global d’Azure AD ; il active la gestion des mots de passe en libre-service pour un sous-ensemble d’utilisateurs, dont Julie. 
2. Julie se connecte au portail myapps et reçoit un message qui l’invite à enregistrer ses informations de sécurité afin d’anticiper les prochaines réinitialisations de mot de passe.
3. Après quelques jours, Julie oublie son mot de passe et le réinitialise via le portail Azure AD

### <a name="self-service-access-to-applications"></a>Accès en libre-service aux applications 

1. Kevin est le gérant de ServiceNow. Il souhaite que les utilisateurs « s’y inscrivent » à la demande, au lieu de tous les ajouter simultanément
2. Marc, notre administrateur global Azure AD, modifie l’application ServiceNow pour activer les demandes de libre-service
3. Julie, notre professionnelle de l’information, se connecte au portail myapps ; elle clique sur le bouton « Ajouter d’autres applications » et voit que ServiceNow figure parmi les applications recommandées. Elle revient ensuite au portail myapps et voit l’application ServiceNow.

[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]