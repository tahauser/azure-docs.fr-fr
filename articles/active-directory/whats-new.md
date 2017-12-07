---
title: "Nouveautés Notes de publication pour Azure Active Directory | Microsoft Docs"
description: "Découvrez les nouveautés d’Azure Active Directory (Azure AD), notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités dépréciées et les modifications à venir."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1538e1c26cfe658c7f42ccdd57d8bf5aca0b1fb
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Nouveautés d’Azure Active Directory




> Restez informé des nouveautés d’Azure Active Directory en vous abonnant à ce [flux](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us) dans votre lecteur de flux RSS favori.



Nous améliorons Azure Active Directory de manière continue. Pour vous permettre de rester à jour avec les développements les plus récents, cette rubrique fournit des informations sur les éléments suivants :

-   Versions les plus récentes 
-   Problèmes connus 
-   Résolution des bogues 
-   Fonctionnalités déconseillées 
-   Modifications planifiées 

Consultez cette page régulièrement, car nous la mettons à jour chaque mois.

## <a name="november-2017"></a>Novembre 2017
 
### <a name="retiring-acs"></a>Retrait d’ACS



**Type :** modification planifiée  
**Catégorie de service :** ACS  
**Fonctionnalité de produit :** Access Control Service 


Microsoft Azure Active Directory Access Control (également appelé Access Control Service ou ACS) sera retiré fin 2018.  Des informations supplémentaires, notamment un planning détaillé et des instructions générales de migration, seront fournies dans les prochaines semaines. En attendant, veuillez laisser des commentaires sur cette page pour toute question concernant ACS, et un membre de notre équipe vous aidera à obtenir des réponses.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Restreindre l’accès du navigateur à Intune Managed Browser 


**Type :** modification planifiée  
**Catégorie de service :** accès conditionnel  
**Fonctionnalité produit :** sécurité et protection de l’identité




Avec ce comportement, vous pourrez restreindre l’accès du navigateur à Office 365 et à d’autres applications cloud connectées à Azure AD qui utilisent Intune Managed Browser en tant qu’application approuvée. 

Cette modification vous permet de configurer la condition suivante pour l’accès conditionnel basé sur l’application :

**Applications clientes :** navigateur

**Quel est l’impact de la modification ?**

Aujourd’hui, l’accès est bloqué lors de l’utilisation de cette condition. Quand la préversion de ce comportement sera disponible, tout accès nécessitera l’utilisation de l’application Managed Browser. 

Vous trouverez des détails supplémentaires sur cette fonctionnalité et d’autres dans les notes de publication et les blogs à venir. 

Pour plus d’informations, consultez [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nouvelles applications clientes approuvées pour l’accès conditionnel basé sur les applications Azure AD

 
**Type :** modification planifiée  
**Catégorie de service :** accès conditionnel  
**Fonctionnalité produit :** sécurité et protection de l’identité




Les applications suivantes vont être ajoutées à la liste des [applications clientes approuvées](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) :

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Pour plus d'informations, consultez les pages suivantes :

- [Spécification d’application cliente approuvée](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Accès conditionnel basé sur les applications Azure Active Directory](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Prise en charge des conditions d’utilisation en plusieurs langues



**Type :** nouvelle fonctionnalité    
**Catégorie de service :** conditions d’utilisation  
**Fonctionnalité de produit :** gouvernance/conformité





Les administrateurs peuvent maintenant créer des conditions d’utilisation qui contiennent plusieurs documents PDF. Vous pouvez baliser ces documents PDF avec une langue correspondante. Pour les utilisateurs concernés, le fichier PDF avec la langue correspondante est affiché en fonction de leurs préférences. S’il n’existe aucune correspondance, la langue par défaut est affichée.


---
 

### <a name="realtime-password-writeback-client-status"></a>État en temps réel du client de réécriture du mot de passe



**Type :** nouvelle fonctionnalité  
**Catégorie de service :** réinitialisation du mot de passe libre-service  
**Fonctionnalité de produit :** authentification utilisateur


 

Vous pouvez maintenant examiner l’état de votre client de réécriture du mot de passe local. Cette option est disponible dans la section **Intégration locale** de la page **[Réinitialisation du mot de passe](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**. 

En cas de problème avec la connexion à votre client de réécriture local, un message d’erreur s’affiche avec :

- Des informations sur la raison pour laquelle vous ne pouvez pas vous connecter à votre client de réécriture local 
- Un lien vers la documentation qui vous aidera à résoudre le problème 


Pour plus d’informations, consultez [Intégration locale](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Accès conditionnel basé sur les applications Azure AD 



 
**Type :** nouvelle fonctionnalité  
**Catégorie de service :** Azure AD  
**Fonctionnalité produit :** sécurité et protection de l’identité





Vous pouvez désormais restreindre l’accès à Office 365 et à d’autres applications cloud connectées à Azure AD aux [applications clientes approuvées](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) qui prennent en charge les stratégies Intune App Protection à l’aide de [l’accès conditionnel basé sur les applications Azure AD](active-directory-conditional-access-mam.md). Des stratégies de protection des applications Intune sont utilisées pour configurer et protéger les données d’entreprise sur ces applications clientes.

En combinant des stratégies d’accès conditionnel [basées sur les applications](active-directory-conditional-access-mam.md) et [basées sur les appareils](active-directory-conditional-access-policy-connected-applications.md), vous bénéficiez de la flexibilité nécessaire pour protéger les données sur les appareils d’entreprise et personnels.

Les conditions et contrôles suivants sont désormais disponibles pour une utilisation avec l’accès conditionnel basé sur les applications :

**Condition de plateforme prise en charge**

- iOS
- Android

**Condition d’applications clientes**

- Applications mobiles et clients de bureau

**Contrôle d’accès**

- Demander une application cliente approuvée


Pour plus d’informations, consultez [Accès conditionnel basé sur les applications dans Azure Active Directory](active-directory-conditional-access-mam.md).

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Gestion des appareils Azure AD dans le portail Azure



**Type :** nouvelle fonctionnalité  
**Catégorie de service :** inscription et gestion d’appareil  
**Fonctionnalité produit :** sécurité et protection de l’identité

 



À partir de maintenant, vous trouverez tous vos appareils connectés à Azure AD et les activités liées aux appareils à un seul emplacement. Il existe une nouvelle expérience d’administration pour gérer toutes les identités d’appareils et les paramètres dans le portail Azure. Dans cette version, vous pouvez :

- Afficher tous vos appareils qui sont disponibles pour l’accès conditionnel dans Azure AD.

- Afficher les propriétés, notamment vos appareils hybrides joints à Azure AD.

- Rechercher des clés BitLocker pour vos appareils joints à Azure AD, gérer votre appareil avec Intune, et bien plus encore.

- Gérer les paramètres associés aux appareils Azure AD.


Pour plus d’informations, consultez [Gestion des appareils avec le portail Azure](device-management-azure-portal.md).



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>Prise en charge de macOS comme plateforme d’appareil pour l’accès conditionnel Azure AD 



**Type :** nouvelle fonctionnalité    
**Catégorie de service :** accès conditionnel  
**Fonctionnalité produit :** sécurité et protection de l’identité 
 

Vous pouvez maintenant inclure (ou exclure) macOS comme condition de plateforme d’appareil dans votre stratégie d’accès conditionnel Azure AD. Avec l’ajout de macOS aux plateformes d’appareils prises en charge, vous pouvez :

- **Inscrire et gérer des appareils macOS par le biais d’Intune**. Comme pour d’autres plateformes telles qu’iOS et Android, une application de portail d’entreprise est disponible pour macOS afin d’effectuer des inscriptions unifiées. La nouvelle application de portail d’entreprise pour macOS vous permet d’inscrire un appareil auprès d’Intune et auprès d’Azure AD.
 
- **Garantir que les appareils macOS sont conformes aux stratégies de conformité de votre organisation définies dans Intune**. Dans le portail Intune sur Azure, vous pouvez maintenant configurer des stratégies de conformité pour les appareils macOS. 
  
- **Restreindre l’accès aux applications dans Azure AD uniquement aux appareils macOS conformes**. Lors de la création de stratégies d’accès conditionnel, vous pouvez choisir macOS comme option de plateforme d’appareils distincte. Cette option vous permet de créer des stratégies d’accès conditionnel propres à macOS pour l’ensemble d’applications cible dans Azure.

Pour plus d'informations, consultez les pages suivantes :

- [Créer une stratégie de conformité d’appareil pour les appareils macOS avec Intune](https://aka.ms/macoscompliancepolicy)
- [Accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>Extension NPS pour Azure MFA 


**Type :** nouvelle fonctionnalité    
**Catégorie de service :** authentification multifacteur  
**Fonctionnalité de produit :** authentification utilisateur




L’extension de serveur NPS (Network Policy Server) pour Azure MFA permet d’ajouter des fonctionnalités de MFA basées sur le cloud à votre infrastructure d’authentification à l’aide de vos serveurs existants. Avec l’extension NPS, vous pouvez ajouter des vérifications basées sur des appels téléphoniques, des SMS ou des applications mobiles à votre flux d’authentification existant sans avoir à installer, configurer et gérer les nouveaux serveurs. 

Cette extension a été créée pour les organisations qui souhaitent protéger des connexions VPN sans déployer le serveur Azure MFA. L’extension NPS joue le rôle d’adaptateur entre RADIUS et Azure MFA sur le cloud pour fournir un second facteur d’authentification pour les utilisateurs fédérés ou synchronisés.


Pour plus d’informations, consultez [Intégrer votre infrastructure NPS existante à Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Restaurer ou supprimer définitivement les utilisateurs supprimés


**Type :** nouvelle fonctionnalité    
**Catégorie de service :** gestion des utilisateurs  
**Fonctionnalité de produit :** annuaire 



Dans le centre d’administration Azure AD, vous pouvez désormais :

- Restaurer un utilisateur supprimé. 
- Supprimer définitivement un utilisateur. 


**Pour essayer :**

1. Dans le centre d’administration Azure AD, sélectionnez [**Tous les utilisateurs**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) dans la section **Gérer**. 

2. Dans la liste **Afficher**, sélectionnez **Utilisateurs supprimés récemment**. 

4. Sélectionnez un ou plusieurs utilisateurs supprimés récemment, puis restaurez-les ou supprimez-les définitivement.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nouvelles applications clientes approuvées pour l’accès conditionnel basé sur les applications Azure AD

 
**Type :** fonctionnalité modifiée  
**Catégorie de service :** accès conditionnel  
**Fonctionnalité produit :** sécurité et protection de l’identité


Les applications suivantes ont été ajoutées à la liste des [applications clientes approuvées](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) :

- Planificateur Microsoft

- Microsoft Azure Information Protection 


Pour plus d'informations, consultez les pages suivantes :

- [Spécification d’application cliente approuvée](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Accès conditionnel basé sur les applications Azure Active Directory](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>Capacité à appliquer un opérateur « OU » à des contrôles dans une stratégie d’accès conditionnel 


**Type :** fonctionnalité modifiée    
**Catégorie de service :** accès conditionnel  
**Fonctionnalité produit :** sécurité et protection de l’identité

 
La capacité à appliquer un opérateur « OU » (Demander un des contrôles sélectionnés) à des contrôles d’accès conditionnel a été publiée. Cette fonctionnalité vous permet de créer des stratégies avec un opérateur **OU** entre des contrôles d’accès. Par exemple, vous pouvez utiliser cette fonctionnalité pour créer une stratégie qui oblige l’utilisateur à se connecter à l’aide de l’authentification multifacteur **OU** à utiliser un appareil conforme.

Pour plus d’informations, consultez [Contrôles dans l’accès conditionnel Azure Active Directory](active-directory-conditional-access-controls.md).

 
---

### <a name="aggregation-of-realtime-risk-events"></a>Agrégation des événements à risque en temps réel


**Type :** fonctionnalité modifiée    
**Catégorie de service :** protection des identités  
**Fonctionnalité produit :** sécurité et protection de l’identité


Pour améliorer votre expérience d’administration, dans Azure AD Identity Protection, tous les événements à risque en temps réel qui proviennent de la même adresse IP durant une journée donnée sont maintenant agrégés pour chaque type d’événement à risque. Ce changement permet de limiter le volume d’événements à risque indiqués sans modification de la sécurité utilisateur.

La détection en temps réel sous-jacente fonctionne chaque fois que l’utilisateur se connecte. Si vous avez une stratégie de sécurité des risques de connexion configurée pour MFA ou pour bloquer l’accès, elle est toujours déclenchée lors de chaque connexion présentant un risque.

 
---
 




## <a name="october-2017"></a>Octobre 2017


### <a name="deprecating-azure-ad-reports"></a>Dépréciation des rapports Azure AD


**Type :** modification planifiée  
**Catégorie de service :** création de rapports  
**Fonctionnalité produit :** gestion du cycle de vie des identités  



Le portail Azure vous offre :

- Une nouvelle console d’administration Azure Active Directory 
- De nouvelles API pour les rapports sur l’activité et la sécurité
 
En raison de ces nouvelles fonctionnalités, les API des rapports sous le point de terminaison **/reports** seront supprimées le 10 décembre 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Détection automatique du champ de connexion


**Type :** corrigé   
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** authentification unique  



Azure Active Directory prend en charge la détection automatique de champ de connexion pour les applications qui affichent un champ de nom d’utilisateur et de mot de passe HTML.  Ces étapes sont documentées sous [Comment capturer automatiquement les champs de connexion d’une application](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Vous pouvez rechercher cette fonctionnalité en ajoutant une application *ne provenant pas de la galerie* sur la page **Applications d’entreprise** du [portail Azure](http://aad.portal.azure.com). En outre, vous pouvez configurer le mode **Authentification unique** pour cette nouvelle application sur **Authentification unique par mot de passe**, en entrant une URL web et en enregistrant la page.
 
En raison d’un problème de service, cette fonctionnalité a été temporairement désactivée pour une période donnée. Le problème a été résolu et la détection automatique de champ de connexion est à nouveau disponible.

---

### <a name="new-mfa-features"></a>Nouvelles fonctionnalités MFA


**Type :** nouvelle fonctionnalité  
**Catégorie de service :** authentification multifacteur  
**Fonctionnalité produit :** sécurité et protection de l’identité  



La fonctionnalité MFA est essentielle pour protéger votre organisation. Afin de rendre les informations d’identification plus adaptables et l’expérience plus agréable, les fonctionnalités suivantes ont été ajoutées : 

- Intégration des résultats de la demande d’accès multifacteur directement dans le rapport de connexion Azure AD, notamment l’accès par programme aux résultats de l’authentification multifacteur

- Intégration plus avancée de la configuration de MFA dans l’expérience de configuration Azure AD dans le portail Azure

Avec cette préversion publique, la gestion et la création de rapports MFA font partie intégrante de l’expérience de configuration de base d’Azure AD. L’agrégation de ces deux fonctionnalités vous permet de gérer la fonctionnalité de portail de gestion MFA dans l’expérience Azure AD.

Pour en savoir plus, consultez [Référence pour la génération de rapports d’authentification multifacteur dans le portail Azure](active-directory-reporting-activity-sign-ins-mfa.md). 


---

### <a name="introducing-terms-of-use"></a>Présentation des conditions d’utilisation



**Type :** nouvelle fonctionnalité  
**Catégorie de service :** conditions d’utilisation  
**Fonctionnalité de produit :** gouvernance  



La fonctionnalité Conditions d’utilisation d’Azure AD vous offre une méthode simple pour présenter des informations aux utilisateurs finaux. Cela permet de garantir qu’ils se voient présenter les clauses d’exclusion de responsabilité nécessaires au respect des conditions légales ou de conformité.

Vous pouvez utiliser la fonctionnalité Conditions d’utilisation d’Azure AD dans les scénarios suivants :

- Conditions d’utilisation générales pour tous les utilisateurs de votre organisation. 

- Conditions d’utilisation spécifiques en fonction des attributs d’un utilisateur (par exemple, docteurs/infirmières ou employés nationaux/internationaux dans des groupes dynamiques). 

- Conditions d’utilisation spécifiques pour l’accès aux applications à fort impact commercial, comme Salesforce.

Pour plus d’informations, consultez [Conditions d’utilisation d’Azure Active Directory](active-directory-tou.md).


---

### <a name="enhancements-to-privileged-identity-management"></a>Améliorations apportées à Privileged Identity Management


**Type :** nouvelle fonctionnalité  
**Catégorie de service :** PIM  
**Fonctionnalité produit :** Privileged Identity Management  


Avec Azure Active Directory Privileged Identity Management (PIM), vous pouvez maintenant gérer, contrôler et surveiller l’accès aux ressources Azure (préversion) au sein de votre organisation aux éléments suivants :

- Abonnements
- Groupes de ressources
- Machines virtuelles 

Toutes les ressources dans le portail Azure tirant parti de la fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) d’Azure peuvent utiliser toutes les capacités de gestion de la sécurité et des cycles de vie offertes par Azure AD PIM.

Pour plus d’informations, consultez [PIM pour les ressources Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---

### <a name="introducing-access-reviews"></a>Présentation des révisions d’accès


**Type :** nouvelle fonctionnalité  
**Catégorie de service :** révisions d’accès  
**Fonctionnalité de produit :** gouvernance  



Les révisions d’accès (préversion) permettent aux organisations de gérer efficacement les appartenances à des groupes et les accès aux applications d’entreprise : 

- Vous pouvez le recertifier l’accès utilisateur invité à l’aide des révisions d’accès de leur accès aux applications et appartenances à des groupes. Les informations fournies par les révisions d’accès permettent aux réviseurs de décider efficacement si des utilisateurs invités doivent bénéficier d’un accès ininterrompu.

- Vous pouvez recertifier l’accès d’employés à des applications et l’appartenance à des groupes à l’aide de révisions d’accès.

Vous pouvez collecter les contrôles de révision d’accès dans des programmes importants pour votre organisation afin de suivre les révisions de conformité ou les applications sensibles aux risques.

Pour plus d’informations, consultez [Révisions d’accès Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>Masquage des applications tierces à partir de My Apps et du lanceur d’applications Office 365



**Type :** nouvelle fonctionnalité  
**Catégorie de service :** My Apps  
**Fonctionnalité de produit :** authentification unique  



Vous pouvez désormais mieux gérer les applications qui s’affichent sur vos portails d’utilisateur avec une nouvelle propriété permettant de **masquer l’application**. Le masquage des applications est utile dans les cas où des vignettes d’applications s’affichent pour des services principaux ou des vignettes en double, et finissent par encombrer les lanceurs d’application de l’utilisateur. Le bouton bascule se trouve dans la section des propriétés de l’application tierce et est étiqueté **Visible pour l’utilisateur ?**. Vous pouvez également masquer une application par programme par le biais de PowerShell. 

Pour plus d’informations, consultez [Masquer une application tierce de l’expérience utilisateur dans Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**Qu’est-ce qui est disponible ?**

 Dans le cadre de la transition vers la nouvelle console d’administration, deux API pour récupérer les journaux d’activité Azure AD sont disponibles. Le nouvel ensemble d’API fournit une fonctionnalité enrichie de filtrage et de tri en plus de fournir des activités d’audit et de connexion plus avancées. Les données précédemment disponibles via les rapports de sécurité sont désormais accessibles via l’API d’événements à risque Identity Protection dans Microsoft Graph.


## <a name="september-2017"></a>Septembre 2017

### <a name="hotfix-for-microsoft-identity-manager"></a>Correctif logiciel pour Microsoft Identity Manager


**Type :** fonctionnalité modifiée  
**Catégorie de service :** Microsoft Identity Manager  
**Fonctionnalité produit :** gestion du cycle de vie des identités  



Un package correctif cumulatif (build 4.4.1642.0) est disponible depuis le 25 septembre 2017 pour Microsoft Identity Manager (MIM) 2016 Service Pack 1 (SP1). Ce package cumulatif :

- Résout les problèmes et ajoute des améliorations
- Est une mise à jour cumulative qui remplace toutes les mises à jour de MIM 2016 SP1 jusqu’à la version 4.4.1459.0 pour Microsoft Identity Manager 2016. 
- Requiert que vous disposiez de **Microsoft Identity Manager 2016 build 4.4.1302.0.** 

Pour plus d’informations, consultez [Package correctif cumulatif (build 4.4.1642.0) disponible pour Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---
