---
title: Meilleures pratiques pour sécuriser l’accès des administrateurs dans Azure AD | Microsoft Docs
description: Assurez-vous que l’accès des administrateurs et les comptes administrateur de votre organisation sont sécurisés. Pour les architectes de systèmes et les professionnels de l’informatique qui configurent Azure AD, Azure et Microsoft Online Services.
services: active-directory
keywords: ''
author: curtand
ms.author: curtand
ms.date: 03/09/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: martincoetzer, MarkMorow
ms.openlocfilehash: 98665ab215c98ea60273ce3aae2757cf20817a90
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Sécurisation de l’accès privilégié pour les déploiements hybrides et cloud dans Azure AD

La sécurité de la plupart ou de toutes les ressources professionnelles des organisations actuelles dépend de l’intégrité des comptes privilégiés qui administrent et gèrent les systèmes informatiques. Les personnes malveillantes, comme les pirates informatiques, ciblent souvent des comptes administrateur et d’autres moyens d’accès privilégié pour tenter d’accéder rapidement à des données sensibles et à des systèmes à l’aide d’attaques d’informations d’identification. Pour les services cloud, la prévention et la réponse sont de la responsabilité conjointe du fournisseur de services cloud et du client. Pour plus d’informations sur les dernières menaces sur les points de terminaison et le cloud, consultez le [rapport de renseignements de sécurité Microsoft](https://www.microsoft.com/security/sir/default.aspx). Cet article peut vous aider à créer une feuille de route afin de combler les lacunes de vos plans actuels, des conseils sont fournis ici.

> [!NOTE] 
> Microsoft s’engage aux plus hauts niveaux de confiance, de transparence, de conformité aux normes et aux réglementations. Découvrez comment l’équipe de réponse aux incidents mondiale de Microsoft permet d’atténuer les effets des attaques contre les services cloud, et comment la sécurité est intégrée dans les produits professionnels et services cloud Microsoft dans le [Centre de gestion de la confidentialité Microsoft - Sécurité](https://www.microsoft.com/en-us/trustcenter/security) et les objectifs de conformité Microsoft dans le [Centre de gestion de la confidentialité Microsoft - Conformité](https://www.microsoft.com/en-us/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Pour la plupart des organisations, la sécurité des ressources professionnelles dépend de l’intégrité des comptes privilégiés qui administrent et gèrent les systèmes informatiques. Les cybercriminels se concentrent sur l’accès privilégié aux systèmes d’infrastructure (comme Active Directory et Azure Active Directory) pour accéder aux données sensibles d’une organisation. 

Les approches traditionnelles qui ciblent la sécurisation des points d’entrée et de sortie d’un réseau comme périmètre de sécurité principal sont moins efficaces du fait l’utilisation croissante d’applications SaaS et d’appareils personnels sur Internet. Le remplacement naturel du périmètre de sécurité réseau dans une entreprise actuelle complexe réside dans les contrôles d’authentification et d’autorisation dans la couche d’identité d’une organisation. 

Les comptes administrateur privilégiés sont effectivement contrôlés par ce nouveau « périmètre de sécurité ». Il est essentiel de protéger l’accès privilégié, qu’il s’agisse d’un environnement local, cloud ou hybride de services hébergés en local et sur le cloud. La protection de l’accès des administrateurs contre des adversaires déterminés vous oblige à adopter une approche réfléchie et complète pour isoler les systèmes de votre organisation contre les risques. 

La sécurisation de l’accès privilégié nécessite le changement de
* Processus, pratiques d’administration et gestion des connaissances
* Composants techniques telles que les défenses d’hôte, les protections de compte et la gestion des identités

Ce document se concentre principalement sur la création d’une feuille de route pour sécuriser les identités et l’accès qui sont gérés ou signalés dans Azure AD, Microsoft Azure, Office 365 et d’autres services cloud. Pour les organisations disposant de comptes administrateur locaux, consultez les conseils relatifs à l’accès privilégié local et hybride géré depuis Active Directory dans [Sécurisation de l’accès privilégié](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> Les conseils de cet article font principalement référence aux fonctionnalités d’Azure Active Directory incluses dans les plans Azure Active Directory Premium P1 et P2. Azure Active Directory Premium P2 est inclus dans la suite EMS E5 et la suite Microsoft 365 E5. Ces conseils supposent que votre organisation dispose déjà de licences Azure AD Premium P2 pour vos utilisateurs. Si vous ne disposez pas de ces licences, certains conseils peuvent ne pas s’appliquer à votre organisation. Dans cet article, le terme administrateur général du terme (ou administrateur global) est également synonyme de « administrateur d’entreprise » ou « administrateur client ».

## <a name="develop-a-roadmap"></a>Créer une feuille de route 

Microsoft vous recommande de créer et de suivre une feuille de route pour sécuriser l’accès privilégié contre les cybercriminels. Vous pouvez toujours ajuster votre feuille de route pour prendre en compte vos fonctionnalités existantes et des exigences spécifiques au sein de votre organisation. Chaque étape de la feuille de route doit indiquer le coût et la difficulté pour les adversaires d’attaquer un accès privilégié pour vos ressources locales, cloud et hybrides. Microsoft recommande les quatre étapes de feuille de route suivantes : cette feuille de route recommandée planifie les implémentations les plus efficaces et rapides en premier, sur la base de l’expérience d’incidents de cyber-attaque et d’implémentation de réponse de Microsoft. Les délais de cette feuille de route sont approximatifs.

![Étapes de la feuille de route avec délais](./media/admin-roles-best-practices/roadmap-timeline.png)

* Étape 1 (24-48 heures) : éléments critiques que nous vous recommandons de traiter immédiatement

* Étape 2 (2-4 semaines) : atténuer les techniques d’attaque les plus fréquemment utilisées

* Étape 3 (1-3 mois) : gagner en visibilité et disposer d’un contrôle total sur l’activité administrative

* Étape 4 (six mois et plus) : continuer à créer des défenses pour renforcer votre plateforme de sécurité

Ce cadre de feuille de route est conçu pour optimiser l’utilisation de technologies Microsoft que vous avez peut-être déjà déployées. Vous pouvez également tirer parti des principales technologies de sécurité actuelles et à venir et intégrer des outils de sécurité d’autres fournisseurs que vous avez déjà déployés ou que vous envisagez de déployer. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Étape 1 : éléments critiques que nous vous recommandons de traiter immédiatement

![Étape 1](./media/admin-roles-best-practices/stage-one.png)

L’étape 1 de la feuille de route cible les tâches critiques rapides et faciles à implémenter. Nous vous recommandons de les traiter immédiatement au cours des premières 24 à 48 heures pour garantir un niveau de base d’accès privilégié sécurisé. Cette étape de la feuille de route d’accès privilégié sécurisé comprend les actions suivantes :

### <a name="general-preparation"></a>Préparation générale

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Activer Azure AD Privileged Identity Management

Si vous n’avez pas déjà activé Azure AD Privileged Identity Management (PIM), faites-le dans votre client de production. Après avoir activé Privileged Identity Management, vous recevez des notifications par courrier électronique de changements de rôles d’accès privilégié. Ces notifications vous informent lorsque des utilisateurs supplémentaires sont ajoutés aux rôles disposant de privilèges élevés dans votre annuaire.

Azure AD Privileged Identity Management est inclus dans Azure AD Premium P2 ou EMS E5. Ces solutions vous aident à protéger l’accès aux applications et aux ressources de l’environnement local et dans le cloud. Si vous ne disposez pas d’Azure AD Premium P2 ou d’EMS E5 et que vous souhaitez évaluer plus de fonctionnalités que celles référencées dans cette feuille de route, inscrivez-vous à l’[essai gratuit de 90 jours d’Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Utilisez ces versions d’évaluation de licence pour essayer Azure AD Privileged Identity Management et Azure AD Identity Protection, pour surveiller l’activité à l’aide des rapports de sécurité avancés d’Azure AD, des audits et des alertes.

Après avoir activé Azure AD Privileged Identity Management :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en utilisant un compte d’administrateur général de votre client de production.

2. Pour sélectionner le client sur lequel vous souhaitez utiliser Privileged Identity Management, sélectionnez votre nom d’utilisateur dans le coin supérieur droit du portail Azure.

3. Sélectionnez **Tous les services** et filtrez la liste pour rechercher **Azure AD Privileged Identity Management**.

4. Ouvrez Privileged Identity Management à partir de la liste **Tous les services** et épinglez-le à votre tableau de bord.

La première personne à utiliser Azure AD Privileged Identity Management dans votre client se voit automatiquement attribuer les rôles **Administrateur de sécurité** et **Administrateur de rôle privilégié** dans le client. Seuls les administrateurs de rôle privilégié peuvent gérer les attributions de rôles d’annuaire Azure AD d’utilisateurs. En outre, après l’ajout d’Azure AD Privileged Identity Management, l’Assistant de sécurité s’affiche et vous guide tout au long du processus de découverte et d’attribution. Vous pouvez quitter l’Assistant sans apporter de modifications supplémentaires pour l’instant. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Identifier et classer les comptes dans des rôles à privilèges élevés 

Après avoir activé Azure AD Privileged Identity Management, vous voyez les utilisateurs ayant les rôles d’annuaire Administrateur général, Administrateur de rôle privilégié, Administrateur Exchange Online et Administrateur SharePoint Online. Si vous ne disposez pas d’Azure AD PIM dans votre client, vous pouvez utiliser l’[API PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0). Commencez par le rôle d’administrateur général car ce rôle est générique : un utilisateur à qui ce rôle d’administrateur est affecté a les mêmes autorisations sur tous les services cloud auxquels votre organisation s’est abonnée, peu importe que vous attribuiez ce rôle dans le portail Office 365, dans le portail Azure ou à l’aide du module Azure AD pour Microsoft PowerShell. 

Supprimez les comptes qui ne sont plus nécessaires dans ces rôles et classez les autres comptes qui sont affectés à des rôles d’administrateur :

* Affectés individuellement à des utilisateurs administratifs, et pouvant également servir à des fins non administratives (par exemple, messagerie personnelle)
* Affectés individuellement à des utilisateurs administratifs et dédiés à des fins administratives uniquement
* Partagés entre plusieurs utilisateurs
* Pour les scénarios d’accès d’urgence de secours
* Pour les scripts automatisés
* Pour les utilisateurs externes

#### <a name="define-at-least-two-emergency-access-accounts"></a>Définir au moins deux comptes d’accès d’urgence 

Veillez à ne pas vous retrouver dans une situation dans laquelle vous pourriez être verrouillé par inadvertance en dehors de l’administration de votre client Azure AD en raison de l’impossibilité de se connecter ou d’activer un compte utilisateur existant en tant qu’administrateur. Par exemple, si l’organisation est fédérée vers un fournisseur d’identité local, ce fournisseur d’identité peut être indisponible et les utilisateurs ne peuvent donc pas se connecter en local. Vous pouvez pallier l’impact d’un défaut d’accès administratif en stockant plusieurs comptes d’accès d’urgence dans votre locataire.

Les comptes d’accès d’urgence aident les organisations à restreindre l’accès privilégié dans un environnement Azure Active Directory existant. Ces comptes sont hautement privilégiés et ne sont pas affectés à des individus spécifiques. Les comptes d’accès d’urgence sont limités à des cas d’urgence ou à des scénarios « de secours » où il est impossible d’utiliser des comptes d’administration normaux. Les organisations doivent vérifier l’objectif de contrôle et de réduction de l’utilisation du compte d’urgence à la stricte durée pendant laquelle celui-ci est nécessaire. 

Évaluez les comptes qui sont affectés ou éligibles pour le rôle d’administrateur général. Si vous ne voyez pas de comptes cloud uniquement à l’aide du domaine *. onmicrosoft.com (conçu pour l’accès d’urgence de secours), créez-les. Pour plus d’informations, consultez [Managing emergency access administrative accounts in Azure AD](active-directory-admin-manage-emergency-access-accounts.md) (Gestion des comptes d’administration de l’accès d’urgence dans Azure AD).

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Activer l’authentification multifacteur et inscrire tous les autres comptes administrateur non fédérés utilisateur unique à privilèges élevés 

Exigez Azure multi-Factor Authentication (MFA) lors de la connexion de tous les utilisateurs individuels auxquels sont affectés un ou plusieurs rôles d’administrateur Azure AD : Administrateur général, Administrateur de rôle privilégié, Administrateur Exchange Online et Administrateur SharePoint Online. Suivez le guide pour activer [l’authentification multifacteur (MFA) pour vos comptes Administrateur](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) et vérifier que tous ces utilisateurs sont inscrits sur [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Vous trouverez plus d’informations dans les étapes 2 et 3 du guide [Protect access to data and services in Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) (Protéger l’accès aux données et services dans Office 365). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Étape 2 : atténuer les techniques d’attaque les plus fréquemment utilisées

![Étape 2](./media/admin-roles-best-practices/stage-two.png)

L’étape 2 de la feuille de route vise à atténuer les techniques d’attaque les plus fréquemment utilisées de vol d’informations d’identification et d’abus et est conçue pour être implémentée en 2 à 4 semaines environ. Cette étape de la feuille de route d’accès privilégié sécurisé comprend les actions suivantes.

### <a name="general-preparation"></a>Préparation générale

#### <a name="conduct-a-inventory-of-services-owners-and-admins"></a>Inventorier les services, propriétaires et administrateurs

Avec l’augmentation du BYOD (apportez votre propre appareil), des stratégies de travail à domicile et la croissance de la connectivité sans fil dans les entreprises, il est essentiel que vous surveilliez qui se connecte à votre réseau. Un audit de sécurité efficace révèle souvent des appareils, applications et programmes exécutés sur votre réseau qui ne sont pas pris en charge par le service informatique, et sont donc potentiellement non sécurisés. Pour plus d’informations, consultez [Présentation de la gestion et surveillance de la sécurité Azure](../security/security-management-and-monitoring-overview.md). Veillez à inclure toutes les tâches suivantes dans votre processus d’inventaire. 

* Identifiez les utilisateurs disposant de rôles d’administrateur et les services où ils peuvent gérer.
* Utilisez Azure AD PIM pour connaître les utilisateurs de votre organisation disposant d’un accès administrateur à Azure AD, y compris les rôles autres que ceux répertoriés à l’étape 1.
* En plus des rôles définis dans Azure AD, Office 365 inclut un ensemble de rôles d’administrateur que vous pouvez affecter aux utilisateurs de votre organisation. Chaque rôle d’administrateur correspond à des fonctions d’entreprise courantes et fournit aux personnes de votre organisation des autorisations pour effectuer des tâches spécifiques dans le centre d’administration Office 365. Utilisez le centre d’administration Office pour connaître les utilisateurs de votre organisation disposant d’un accès administrateur à Office 365, y compris via des rôles non gérés dans Azure AD. Pour plus d’informations, consultez [À propos des rôles d’administrateur Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) et [Security best practices for Office 365](https://support.office.com/article/Security-best-practices-for-Office-365-9295e396-e53d-49b9-ae9b-0b5828cdedc3) (Meilleures pratiques de sécurité pour Office 365).
* Inventoriez dans d’autres services dont votre organisation dépend, tels qu’Azure, Intune ou Dynamics 365.
* Assurez-vous que vos comptes administrateur (comptes utilisés à des fins administratives, et pas seulement les comptes courants des utilisateurs) utilisent des adresses de messagerie qui leur sont associées et qu’ils sont inscrits pour Azure MFA ou utilisent MFA en local.
* Demandez aux utilisateurs leur justification d’entreprise d’accès administrateur.
* Supprimez l’accès administrateur pour les personnes et les services n’en ayant pas besoin.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Identifier les comptes Microsoft dans des rôles d’administrateur à basculer vers des comptes professionnels ou scolaires 

Parfois, l’administrateur général d’une organisation réutilise les informations d’identification de compte Microsoft existantes lorsqu’il a commencé à utiliser Azure AD. Ces comptes Microsoft doivent être remplacés par des comptes cloud ou synchronisés individuels. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Vérifier les comptes d’utilisateur distincts et le transfert de messagerie pour les comptes administrateur général 

Les comptes administrateur général ne doivent pas inclure des adresses de messagerie personnelle, car les comptes de messagerie personnelle sont régulièrement hameçonnés par les cybercriminels. Pour séparer les risques liés à Internet (attaques d’hameçonnage, navigation web involontaire) des privilèges administrateur, créez un compte dédié pour chaque utilisateur avec des privilèges administratifs. 

Si vous ne l’avez pas déjà fait, créez des comptes distincts pour les utilisateurs pour effectuer des tâches administratives générales, pour vous assurer qu’ils n’ouvrent pas de messages électroniques ou qu’ils n’exécutent pas de programmes associés à leurs comptes administrateur par inadvertance. Veillez à ce que la messagerie de ces comptes soit transférée vers une boîte aux lettres professionnelle.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Vérifier que les mots de passe des comptes administrateur ont été récemment modifiés

Assurez-vous que tous les utilisateurs se sont connectés à leur compte administrateur et modifié leur mot de passe au moins une fois au cours des 90 derniers jours. Vérifiez également les mots de passe de comptes partagés, pour lesquels plusieurs utilisateurs connaissent le mot de passe, ont été modifiés récemment.

#### <a name="turn-on-password-synchronization"></a>Activer la synchronisation de mot de passe

La synchronisation de mot de passe est une fonctionnalité permettant de synchroniser les codes de hachage des mots de passe utilisateur à partir d’une instance Active Directory locale vers une instance Azure AD basée sur le cloud. Même que si vous décidez d’utiliser la fédération avec les services de fédération Active Directory (AD FS) ou d’autres fournisseurs d’identité, vous pouvez éventuellement configurer la synchronisation de mot de passe comme sauvegarde au cas où votre infrastructure locale, serveurs AD ou ADFS par exemple, échouerait ou deviendrait temporairement indisponible. Cela permet aux utilisateurs de se connecter au service à l’aide du mot de passe qu’ils utilisent pour se connecter à leur instance AD locale. Cela permet également à la protection d’identité de détecter les informations d’identification compromises en comparant ces codes de hachage de mot de passe avec des mots de passe connus pour être compromis, si un utilisateur a utilisé les mêmes adresse de messagerie et mot de passe sur d’autres services non connectés à Azure AD.  Pour plus d’informations, consultez [Implémenter la synchronisation de hachage du mot de passe avec la synchronisation Azure AD Connect](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Exiger l’authentification multifacteur (MFA) pour les utilisateurs dans tous les rôles privilégiés, ainsi que pour les utilisateurs exposés

Azure AD recommande que vous exigiez une authentification multifacteur (MFA) pour tous vos utilisateurs, notamment les administrateurs et tous les autres utilisateurs qui pourraient poser des problèmes significatifs si leur compte était compromis (par exemple, les responsables financiers). Cela réduit le risque d'attaque en raison d'un mot de passe compromis.

Activer :

* [MFA pour les comptes hautement exposés](../multi-factor-authentication/multi-factor-authentication-security-best-practices.md) tels que les comptes des directeurs généraux d’une organisation 
* [MFA pour chaque compte administrateur associé à un utilisateur individuel](../multi-factor-authentication/multi-factor-authentication-get-started-user-states.md) pour les autres applications SaaS connectées 
* MFA pour tous les administrateurs d’applications SaaS Microsoft, notamment les administrateurs dans des rôles gérés dans Exchange Online et le portail Office

Si vous utilisez Windows Hello Entreprise, l’exigence relative à MFA peut être remplie à l’aide de l’expérience de connexion Windows Hello. Pour plus d’informations, consultez [Windows Hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport). 

#### <a name="configure-identity-protection"></a>Configurer la protection des identités 

Azure AD Identity Protection est un outil de surveillance et de rapports basé sur un algorithme que vous pouvez utiliser pour détecter les vulnérabilités potentielles qui affectent les identités de votre organisation. Vous pouvez configurer des réponses automatiques aux activités suspectes détectées et prendre les mesures appropriées pour les résoudre. Pour plus d’informations, consultez [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Obtenir votre Office 365 Secure Score (si vous utilisez Office 365)

Secure Score recherche les services Office 365 que vous utilisez (comme OneDrive, SharePoint et Exchange), puis examine vos paramètres et activités et les compare à une référence établie par Microsoft. Vous obtenez un score indiquant votre alignement sur les meilleures pratiques de sécurité. Toute personne disposant d’autorisations d’administration (rôle Administrateur général ou personnalisé) pour un abonnement Office 365 Business Premium ou Enterprise peut accéder à Secure Score à l’adresse [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Passer en revue l’aide Office 365 en matière de sécurité et de conformité (si vous utilisez Office 365)

Le [plan de sécurité et de conformité](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) offre une vue d’ensemble de l’approche selon laquelle un client Office 365 doit configurer Office 365 et tirer parti d’autres fonctionnalités d’EMS. Passez ensuite en revue les étapes 3 à 6 de la procédure [Protect access to data and services in Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) (Protéger l’accès aux données et services dans Office 365) et le guide de procédure [Monitor security and compliance in Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6) (Surveiller la sécurité et la conformité dans Office 365).


#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Configurer la surveillance de l’activité Office 365 (si vous utilisez Office 365)

Vous pouvez contrôler comment les personnes de votre organisation utilisent les services Office 365, vous permettant ainsi d’identifier les utilisateurs disposant d’un compte administrateur et n’ayant peut-être pas besoin d’un accès Office 365 du fait qu’ils ne se connectent pas à ces portails. Pour plus d’informations, consultez [Rapports d’activité dans le Centre d’administration Office 365](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263).

#### <a name="establish-incidentemergency-response-plan-owners"></a>Définir des propriétaires de plan de réponse d’incident/d’urgence

Une réponse efficace aux incidents est une tâche complexe. La définition d’une capacité de réponse aux incidents efficace nécessite donc une planification et des ressources significatives. Il est essentiel que vous surveilliez en permanence les cyber-attaques et que vous établissiez des procédures pour hiérarchiser la gestion des incidents. Des méthodes efficaces de collecte, d’analyse et de rapports de données sont essentielles pour développer des relations, pour communiquer avec d’autres groupes internes et pour planifier les propriétaires. Pour plus d’informations, consultez [Centre de réponse aux problèmes de sécurité Microsoft](https://technet.microsoft.com/security/dn440717). 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Sécuriser les comptes d’administration privilégiés locaux (si ce n’est pas déjà fait)

Si votre locataire Azure Active Directory est synchronisé avec Active Directory local, puis suivez les conseils de la [Feuille de route de l’accès privilégié sécurisé](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access) : étape 1. Cela inclut la création de comptes administrateur distincts pour les utilisateurs devant effectuer des tâches administratives locales, déployer des stations de travail à accès privilégié pour les administrateurs d’Active Directory et créer des mots de passe administrateur local uniques pour les stations de travail et les serveurs.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Étapes supplémentaires pour les organisations gérant l’accès à Azure

#### <a name="complete-an-inventory-of-subscriptions"></a>Inventorier les abonnements

Utilisez le portail Enterprise et le portail Azure pour identifier les abonnements de votre organisation qui hébergent des applications de production. 

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Supprimer les comptes Microsoft des rôles Administrateur

Les comptes Microsoft d’autres programmes, tels que Xbox Live et Outlook, ne doivent pas être utilisés en tant que comptes administrateur pour les abonnements d’organisation. Supprimez l’état d’administration de tous les comptes Microsoft et remplacez par des comptes professionnels ou scolaires Active Directory (par exemple, chris@contoso.com).

#### <a name="monitor-azure-activity"></a>Surveiller l’activité Azure

Le Journal d’activité Azure fournit un historique des événements au niveau de l’abonnement dans Azure. Il fournit des informations indiquant qui a créé, mis à jour et supprimé quelles ressources et quand ces événements se sont produits. Pour plus d’informations, consultez [Audit et réception de notifications relatives à des actions importantes dans votre abonnement Azure](../monitoring-and-diagnostics/monitor-quick-audit-notify-action-in-subscription.md).


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Étapes supplémentaires pour les organisations gérant l’accès à d’autres applications cloud via Azure AD 

#### <a name="configure-conditional-access-policies"></a>Configurer des stratégies d’accès conditionnel

Préparez des stratégies d’accès conditionnel pour les applications locales et hébergées sur le cloud. Si vous disposez d’appareils rattachés à l’espace de travail des utilisateurs, obtenez plus d’informations dans [Configuration d’un accès conditionnel en local à l’aide du service Azure Active Directory Device Registration](active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Étape 3 : gagner en visibilité et disposer d’un contrôle total sur l’activité administrative

![Étape 3](./media/admin-roles-best-practices/stage-three.png)

L’étape 3 s’appuie sur les atténuations de l’étape 2 et est conçue pour être implémentée dans un délai de 1 à 3 mois environ. Cette étape de la feuille de route d’accès privilégié sécurisé comprend les composants suivants.

### <a name="general-preparation"></a>Préparation générale

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Effectuer une vérification de l’accès des utilisateurs de rôles d’administrateur

De plus en plus d’utilisateurs d’entreprise disposent d’un accès privilégié par le biais de services cloud, ce qui peut conduire à une plateforme non managée croissante. Il s’agit des utilisateurs devenant des administrateurs généraux pour Office 365, des administrateurs d’abonnements Azure et des utilisateurs disposant d’un accès Administrateur aux machines virtuelles ou par le biais d’applications SaaS. Au lieu de cela, dans les organisations, tous les employés, et plus particulièrement les administrateurs, doivent gérer les transactions quotidiennes en tant qu’utilisateurs sans privilège et ne disposer que des droits d’administrateur nécessaires. Le nombre d’utilisateurs dans des rôles d’administrateur pouvant avoir augmenté depuis l’adoption initiale, passez en revue l’accès afin d’identifier et de vérifier chaque utilisateur éligible pour activer des privilèges d’administrateur. 

Effectuez les actions suivantes :

* Déterminez quels utilisateurs sont des administrateurs Azure AD, accordez un accès administrateur à la demande juste-à-temps, ainsi que les contrôles de sécurité en fonction du rôle.
* Affectez un autre rôle aux utilisateurs ne disposant d’aucune justification précise à l’accès administrateur (si aucun rôle n’est éligible, supprimez-les).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Poursuivre le lancement d’une authentification renforcée pour tous les utilisateurs 

Exigez une authentification forte et moderne, telle qu’Azure MFA ou Windows Hello, des responsables de la suite C, des directeurs de niveau supérieur, du personnel informatique et de sécurité critique et d’autres utilisateurs hautement exposés. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Utiliser des stations de travail dédiées pour l’administration d’Azure AD

Les personnes malveillantes peuvent tenter de cibler des comptes privilégiés pour accéder aux données et systèmes d’une organisation dans le but de nuire à l’intégrité et l’authenticité des données, via un code malveillant modifiant la logique du programme ou qui usurpe l’administrateur saisissant des informations d’identification. Les stations de travail d’accès privilégié (PAW) fournissent un système d’exploitation dédié aux tâches sensibles et protégé contre les attaques Internet et les vecteurs de menaces. La séparation de ces tâches et comptes sensibles et de ces stations de travail et appareils utilisés au quotidien fournit une protection très élevée contre les attaques par hameçonnage, les vulnérabilités du système d’exploitation et des applications, différentes attaques par emprunt d’identité et les vols d’informations d’identification tels que les enregistreurs de frappe et les attaques de type « Pass the hash » et « Pass the ticket ». En déployant des stations de travail à accès privilégié, vous pouvez réduire le risque de saisie d’informations d’identification administrateur par les administrateurs si ce n’est sur un environnement de bureau qui a été renforcé. Pour plus d’informations, consultez [Stations de travail d’accès privilégié](https://docs.microsoft.com/en-us/windows-server/identity/securing-privileged-access/privileged-access-workstations).

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Passer en revue les recommandations relatives à la gestion des incidents du National Institute of Standards and Technology 

Le National Institute of Standards and Technology (NIST) fournit des instructions pour la gestion des incidents, et plus particulièrement pour l’analyse des données relatives aux incidents et détermination de la réponse adéquate à chaque incident. Pour plus d’informations, consultez le document [(NIST) Computer Security Incident Handling Guide (SP 800-61, Revision 2)](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guide de la gestion des incidents de sécurité informatique du NIST (SP 800-61, Révision 2)).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Implémenter Privileged Identity Management (PIM) pour JIT sur des rôles administratifs supplémentaires

Pour Azure Active Directory, utilisez la fonctionnalité [Gestion des identités privilégiées Azure Active Directory](active-directory-privileged-identity-management-configure.md). L’activation à durée limitée de rôles privilégiés vous permet de :

* Activer des privilèges administratifs pour effectuer une tâche spécifique
* Appliquer MFA pendant le processus d’activation
* Utiliser des alertes pour informer les administrateurs de modifications hors-bande
* Permettre aux utilisateurs de conserver certains privilèges sur une durée préconfigurée
* Permettre aux administrateurs de sécurité de détecter toutes les identités privilégiées, d’afficher des rapports d’audit et de créer des révisions d’accès afin d’identifier chaque utilisateur éligible à l’activation de privilèges administratifs

Si vous utilisez déjà Azure AD Privileged Identity Management, ajustez la plage de temps des privilèges à durée limitée si nécessaire (par exemple, les fenêtres de maintenance).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Déterminer l’exposition à des protocoles de connexion basés sur un mot de passe (si vous utilisez Exchange Online)

Auparavant, les protocoles supposaient que les combinaisons de nom d’utilisateur/mot de passe étaient intégrées dans les appareils, comptes de messagerie, téléphones, etc. De nos jours, en raison du risque de cyber-attaques dans le cloud, nous vous recommandons d’identifier chaque utilisateur qui, si ses informations d’identification étaient compromises, pourrait être source de problèmes catastrophiques pour l’organisation, et de ne pas l’autoriser à se connecter à sa messagerie via son nom d’utilisateur/mot de passe en implémentant des exigences d’authentification forte et un accès conditionnel. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Effectuer une évaluation de révision des rôles pour les rôles Office 365 (si vous utilisez Office 365)

Évaluez si tous les utilisateurs administrateurs se trouvent dans les rôles appropriés (supprimez et réaffectez en fonction de cette évaluation).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Passer en revue l’approche de gestion des incidents de sécurité utilisée dans Office 365 et comparer avec votre organisation

Vous pouvez télécharger ce rapport depuis [Security Incident Management in Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302) (Gestion des incidents de sécurité dans Microsoft Office 365).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Continuer à sécuriser les comptes d’administration privilégiés locaux

Si votre Azure Active Directory est connecté à Active Directory local, suivez les conseils de la [Feuille de route de l’accès privilégié sécurisé](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access) : étape 2. Cela inclut le déploiement de stations de travail à accès privilégié pour tous les administrateurs, la nécessité d’une MFA, l’utilisation d’un nombre suffisant d’administrateurs pour la maintenance du contrôleur de domaine, la réduction de la zone d’attaque de domaines, le déploiement d’ATA pour la détection des attaques.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Étapes supplémentaires pour les organisations gérant l’accès à Azure

#### <a name="establish-integrated-monitoring"></a>Définir une surveillance intégrée

[Azure Security Center](../security-center/security-center-intro.md) fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventorier vos comptes privilégiés dans des machines virtuelles hébergées

Dans la plupart des cas, vous n’avez pas besoin d’accorder aux utilisateurs des autorisations illimitées à tous vos abonnements ou ressources Azure. Vous pouvez utiliser des rôles d’administrateur Azure AD pour séparer les droits au sein de votre organisation et n’accorder que l’accès dont les utilisateurs ont besoin pour effectuer des tâches spécifiques. Par exemple, vous pouvez utiliser des rôles d’administrateur Azure AD pour permettre à un administrateur de ne gérer que les machines virtuelles dans un abonnement, tandis qu’un autre pourra gérer les bases de données SQL au sein du même abonnement. Pour plus d’informations, consultez [Bien démarrer avec le contrôle d’accès en fonction du rôle dans le portail Azure](role-based-access-control-what-is.md).

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Implémenter PIM pour les rôles d’administrateur Azure AD

Utilisez Privileged Identity Management avec des rôles d’administrateur Azure AD pour gérer, contrôler et surveiller l’accès aux ressources Azure. PIM protège les comptes privilégiés contre les cyber-attaques en réduisant le temps d’exposition des privilèges et en augmentant votre visibilité sur leur utilisation grâce à des rapports et des alertes. Pour plus d’informations, consultez [Gérer l’accès RBAC aux ressources Azure avec Privileged Identity Management](pim-azure-resource.md).

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Utiliser des intégrations des journaux Azure pour envoyer les journaux Azure pertinents à vos systèmes SIEM 

L’intégration des journaux Azure permet d’intégrer des journaux bruts de vos ressources Azure dans les systèmes SIEM (Security Information and Event Management) existants de votre organisation. L’[intégration des journaux Azure](../security/security-azure-log-integration-overview.md) collecte les événements Windows à partir des journaux de l’Observateur d’événements et des ressources Azure des journaux d’activité Azure, des alertes Azure Security Center et des journaux de diagnostic Azure. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Étapes supplémentaires pour les organisations gérant l’accès à d’autres applications cloud via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Implémenter l’approvisionnement de l’utilisateur pour des applications connectées

Azure AD vous permet d’automatiser la création, la maintenance et la suppression d’identités utilisateur dans les applications cloud (SaaS) comme Dropbox, Salesforce, ServiceNow, etc. Pour plus d’informations, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure AD](active-directory-saas-app-provisioning.md).

#### <a name="integrate-information-protection"></a>Intégrer la protection des informations

MCAS vous permet de rechercher des fichiers et de définir des stratégies basées sur les étiquettes de classification Azure Information Protection, offrant ainsi une meilleure visibilité et un meilleur contrôle de vos données dans le cloud. Analysez et classez des fichiers dans le cloud et apposez des étiquettes de protection des informations Azure. Pour plus d’informations, consultez [Azure Information Protection integration](https://docs.microsoft.com/cloud-app-security/azip-integration) (Intégration d’Azure Information Protection).

#### <a name="configure-conditional-access"></a>Configurer un accès conditionnel

Configurez un accès conditionnel en fonction du groupe, de l’emplacement et du niveau de confidentialité des [applications SaaS](https://azure.microsoft.com/overview/what-is-saas/) et des applications connectées à Azure AD. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Surveiller l’activité dans les applications cloud connectées

Pour assurer également la protection de l’accès des utilisateurs dans des applications connectées, nous vous recommandons d’utiliser [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). Cela permet de sécuriser l’accès d’entreprise aux applications cloud, en plus de sécuriser vos comptes administrateur, en vous permettant de :

* Étendre la visibilité et le contrôle aux applications cloud
* Créer des stratégies d’accès, des activités et le partage de données
* Identifier automatiquement les activités à risque, les comportements anormaux et les menaces
* Empêcher la fuite de données
* Réduire les risques et prévention des menaces et application de stratégies automatisées

L’agent Cloud App Security SIEM s’intègre à Cloud App Security avec votre serveur SIEM pour permettre la surveillance centralisée des alertes et des activités d’Office 365. Il s’exécute sur votre serveur et transmet les alertes et les activités de Cloud App Security au serveur SIEM. Pour plus d’informations, consultez [SIEM integration](https://docs.microsoft.com/cloud-app-security/siem) (Intégration de SIEM).

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Étape 4 : continuer à créer des défenses pour une sécurité plus proactive


![Étape 4](./media/admin-roles-best-practices/stage-four.png)

L’étape 4 de la feuille de route s’appuie sur la visibilité de l’étape 3 et est conçue pour être implémentée dans un délai de six mois et plus. L’établissement d’une feuille de route vous permet de développer des protections de l’accès privilégié contre d’éventuelles attaques qui sont actuellement connus et présentes aujourd’hui. Malheureusement, les menaces de sécurité évoluent et changent en permanence, nous vous donc conseillons de considérer la sécurité comme un processus continu orienté sur le coût et la réduction du taux de réussite des adversaires ciblant votre environnement.

La protection de l’accès privilégié est une première étape essentielle pour définir des garanties de sécurité des ressources d’entreprise d’une organisation actuelle, mais elle ne constitue pas à elle seule un programme de sécurité complet qui doit comprendre des éléments tels qu’une stratégie, des opérations, la sécurité des informations, des serveurs, des applications, des ordinateurs, des appareils, une infrastructure cloud et d’autres composants offrant des garanties de sécurité. 

Outre la gestion de vos comptes d’accès privilégié, nous vous recommandons de vérifier régulièrement les éléments suivants :

* Assurez-vous que les administrateurs effectuent leurs tâches quotidiennes en tant qu’utilisateurs sans privilèges.
* N’accordez que l’accès privilégié nécessaire, et supprimez-le ensuite (juste-à-temps).
* Conservez et passez en revue l’activité d’audit relative aux comptes privilégiés.

Pour plus d’informations sur la création d’une feuille de route de sécurité complète, consultez les [ressources d’architecture informatique cloud de Microsoft](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Pour plus d’informations sur l’aide que peuvent apporter les services Microsoft sur ces différents points, contactez votre représentant Microsoft ou consultez [Build critical cyber defenses to protect your enterprise](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx) (Développer des cyber-défenses critiques pour protéger votre entreprise).

Cette dernière étape de la feuille de route d’accès privilégié sécurisé comprend les composants suivants.

### <a name="general-preparation"></a>Préparation générale

#### <a name="review-admin-roles-in-azure-active-directory"></a>Passer en revue les rôles d’administrateur dans Azure Active Directory 

Déterminez si les rôles d’administrateur Azure AD intégrés actuels sont toujours à jour et assurez-vous que les utilisateurs ne se trouvent que dans les rôles et les délégations dont ils ont besoin pour les autorisations correspondantes. À l’aide d’Azure AD, vous pouvez affecter des administrateurs distincts à différentes fonctions. Pour plus d’informations, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Passer en revue les utilisateurs administrateurs d’appareils joints Azure AD

Pour plus d’informations, consultez [Comment configurer des appareils hybrides joints à Azure Active Directory](device-management-hybrid-azuread-joined-devices-setup.md).

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Passer en revue les membres de [rôles d’administrateur Office 365 intégrés](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Si vous utilisez Office 365.
‎
#### <a name="validate-incident-response-plan"></a>Valider le plan de réponse aux incidents

Pour améliorer votre plan, Microsoft vous recommande de vérifier régulièrement que votre plan fonctionne comme prévu :

* Parcourir votre feuille de route pour voir ce qui n’a pas fonctionné
* Sur la base de l’analyse post mortem, vérifier les meilleures pratiques actuelles ou en définir de nouvelles
* Vérifier que votre plan de réponse aux incidents mis à jour et les meilleures pratiques sont distribuées dans votre organisation


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Étapes supplémentaires pour les organisations gérant l’accès à Azure 

Déterminez si vous devez [transférer la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>« Secours » : que faire en cas d’urgence

![Urgence](./media/admin-roles-best-practices/emergency.jpeg)

1. Fournissez aux principaux responsables et de la sécurité les informations pertinentes sur l’incident.

2. Passez en revue votre manuel d’attaque. 

3. Accédez à votre combinaison nom d’utilisateur/mot de passe de compte « de secours » pour vous connecter à Azure AD. 

4. Obtenez de l’aide de Microsoft en [ouvrant une demande de support Azure](../azure-supportability/how-to-create-azure-support-request.md).

5. Examinez les [rapports de connexion Azure AD](active-directory-reporting-azure-portal.md). Il peut y avoir un décalage entre l’occurrence d’un événement et le moment où il est inclus dans le rapport.

6. Pour les environnements hybrides, s’ils sont fédérés et que votre serveur AD FS n’est pas disponible, vous devrez peut-être basculer temporairement de l’authentification fédérée vers l’utilisation de la synchronisation du code de hachage du mot de passe. Cela permet de passer de la fédération de domaine à l’authentification managée jusqu’à ce que le serveur AD FS soit disponible.

7. Surveillez les courriers électroniques des comptes privilégiés.

8. Veillez à effectuer des sauvegardes des journaux concernés en vue d’un éventuel examen légal et plus approfondi.

Pour plus d’informations sur la façon dont Microsoft Office 365 gère les incidents de sécurité, consultez [Security Incident Management in Microsoft Office 365](http://aka.ms/Office365SIM) (Gestion des incidents de sécurité dans Microsoft Office 365).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>FAQ : questions fréquentes que nous recevons sur la protection de l’accès privilégié  


**Q :** que faire si je n’ai pas encore implémenté de composants d’accès sécurisé ?

**Réponse :** définissez au minimum deux comptes de secours, affectez la MFA à vos comptes administrateur privilégiés et séparez les comptes utilisateurs des comptes administrateur général.


**Q :** après une violation, quel est le problème à traiter en premier ?

**Réponse :** vérifiez que vous avez demandé l’authentification la plus stricte aux personnes hautement exposées.


**Q :** que se passe-t-il si notre administrateurs privilégiés ont été désactivés ?

**Réponse :** créez un compte administrateur général toujours à jour.


**Q :** que se passe-t-il s’il ne reste qu’un seul administrateur général et qu’il est inaccessible ? 

**Réponse :** utilisez un de vos comptes de secours pour obtenir un accès privilégié immédiat.


**Q :** comment protéger les administrateurs au sein de mon organisation ?

**Réponse :** les administrateurs doivent toujours effectuer leurs tâches quotidiennes en tant qu’utilisateurs « non privilégiés » standard.
 

**Q :** quelles sont les meilleures pratiques pour créer des comptes administrateur dans Azure AD ?

**Réponse :** réservez l’accès privilégié pour des tâches administratives spécifiques.


**Q :** quels outils permettent de réduire l’accès administrateur permanent ?

**Réponse :** Privileged Identity Management (PIM) et les rôles d’administrateur Azure AD.


**Q :** quelle est la position de Microsoft sur la synchronisation des comptes administrateur avec Azure AD ?

**Réponse :** les comptes administrateur de niveau 0 (incluant des comptes, groupes et d’autres ressources disposant d’un contrôle administratif direct ou indirect sur la forêt AD, domaines ou contrôleurs de domaine et toutes les ressources) ne sont utilisés que pour les comptes AD locaux et ne sont généralement pas synchronisés pour Azure AD pour le cloud. 


**Q :** comment empêcher les administrateurs d’accorder un accès administrateur aléatoire dans le portail ?

**Réponse :** utilisez des comptes non privilégiés pour tous les utilisateurs et la plupart des administrateurs. Commencez par définir l’empreinte de l’organisation afin de déterminer les comptes administrateur qui doivent être privilégiés. Surveillez également les nouveaux utilisateurs administratifs créés.


## <a name="next-steps"></a>Étapes suivantes

* [Centre de gestion de la confidentialité Microsoft - Sécurité produit](https://www.microsoft.com/en-us/trustcenter/security) : fonctionnalités de sécurité des produits et services cloud de Microsoft

* [Centre de gestion de la confidentialité Microsoft - Conformité](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) : ensemble complet des offres de conformité de Microsoft pour les services cloud

* [Conseils sur la réalisation d’une évaluation des risques](https://www.microsoft.com/en-us/trustcenter/guidance/risk-assessment): gérez les exigences de sécurité et de conformité pour les services cloud de Microsoft

### <a name="other-ms-online-services"></a>Autres services en ligne MS 

* [Microsoft Intune Security](https://www.microsoft.com/en-us/trustcenter/security/intune-security) : Intune fournit des fonctionnalités de gestion des appareils mobiles, de gestion des applications mobiles et de gestion des ordinateurs à partir du cloud.

* [Sécurité de Microsoft Dynamics 365](https://www.microsoft.com/en-us/trustcenter/security/dynamics365-security) : Dynamics 365 est la solution cloud de Microsoft qui centralise les fonctions de gestion de la relation client (CRM) et de planification des ressources d’entreprise (ERP).

 
