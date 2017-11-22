---
title: "Gestion des identités et des accès : bonnes pratiques de sécurité Azure | Microsoft Docs"
description: "Cet article détaille un ensemble de meilleures pratiques en matière de gestion des identités et du contrôle d’accès à l’aide de fonctionnalités Azure intégrées."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2017
ms.author: yurid
ms.openlocfilehash: d80fdd5a2e4339823c05368d76de333f3314d4ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Meilleures pratiques en matière de sécurité du contrôle d’accès et de la gestion des identités Azure
Beaucoup considèrent l’identité comme la nouvelle couche de sécurité assumant ce rôle du point classique du réseau. Les efforts en matière de gestion de la sécurité et les investissements ont changé de cible principale, car les périmètres réseau sont de plus en plus poreux et la défense du périmètre n’est plus aussi efficace qu’elle ne l’était avant l’explosion des appareils [BYOD](http://aka.ms/byodcg) et des applications cloud.

Dans cet article, nous allons étudier une collection de meilleures pratiques en matière de sécurité du contrôle d’accès et de la gestion des identités Azure. Ces meilleures pratiques sont issues de notre expérience avec [Azure AD](../active-directory/active-directory-whatis.md), mais également de celle des clients, comme vous.

Pour chaque meilleure pratique, nous allons détailler les éléments suivants :

* Nature de la meilleure pratique
* Raison pour laquelle activer cette bonne pratique
* Conséquence possible en cas de non-utilisation de la meilleure pratique
* Alternatives possibles à la meilleure pratique
* Comment apprendre à utiliser la meilleure pratique

Cet article repose sur un consensus, ainsi que sur les fonctionnalités et ensembles de fonctions de la plateforme Azure disponibles lors de l’écriture. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

Dans cet article, nous allons étudier les points suivants :

* Centralisation de la gestion de votre identité
* Activation de l’authentification unique (SSO)
* Déploiement de la gestion des mots de passe
* Application de l’authentification multifacteur (MFA) pour les utilisateurs
* Utilisation du contrôle d’accès en fonction du rôle (RBAC)
* Contrôle des emplacements de création des ressources à l’aide du gestionnaire de ressources
* Aide aux développeurs pour tirer parti des fonctionnalités d’identité pour les applications SaaS
* Surveillance active des activités suspectes

## <a name="centralize-your-identity-management"></a>Centralisation de la gestion de votre identité
Une étape importante vers la sécurisation de votre identité consiste à s’assurer que l’informatique peut gérer des comptes de manière centralisée en fonction de l’endroit de création d’un compte. Alors que la plupart des entreprises informatiques disposent de leur annuaire de compte principal sur site, le nombre de déploiements de clouds hybrides est en hausse et il est important de comprendre comment intégrer des annuaires locaux et de cloud et offrir une expérience transparente pour l’utilisateur final.

Pour mettre en œuvre ce scénario d’[identité hybride](../active-directory/active-directory-hybrid-identity-design-considerations-overview.md), nous vous recommandons deux options :

* Synchronisation de votre annuaire local avec votre annuaire de cloud à l’aide d’Azure AD Connect
* Fédération de votre identité en local avec votre annuaire de cloud à l’aide des [Services ADFS](https://msdn.microsoft.com/library/bb897402.aspx)

Les organisations qui ne parviennent pas à intégrer leur identité locale à leur identité de cloud vont être confrontées à une surcharge administrative en termes de gestion des comptes qui augmente la probabilité d’erreurs et de failles de sécurité.

Pour plus d’informations sur la synchronisation Azure AD, consultez l’article [Intégration de vos identités locales à Azure Active Directory](../active-directory/active-directory-aadconnect.md).

## <a name="enable-single-sign-on-sso"></a>Activation de l’authentification unique (SSO)
La gestion de multiples annuaires pose un problème d’administration, non seulement pour l’informatique, mais également pour les utilisateurs finaux qui auront à mémoriser plusieurs mots de passe. L’utilisation de l’[authentification unique](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) offre à vos utilisateurs la possibilité d’utiliser le même jeu d’informations d’identification pour s’authentifier et accéder aux ressources dont ils ont besoin, que cette ressource soit située en local ou dans le cloud.

L’authentification unique permet aux utilisateurs d’accéder à leurs [applications SaaS](../active-directory/active-directory-appssoaccess-whatis.md) avec leur compte professionnel dans Azure AD. Ceci s’applique non seulement aux applications SaaS de Microsoft, mais également à d’autres applications, telles que [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) et [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). Votre application peut être configurée pour utiliser Azure AD en tant que fournisseur [d’identité SAML](../active-directory/fundamentals-identity.md). Pour contrôler la sécurité, Azure AD n'émettra pas de jeton permettant de se connecter à l'application avant que l'accès n'ait été octroyé par Azure AD. Les utilisateurs peuvent accorder un accès direct ou via un groupe dont ils sont membres.

> [!NOTE]
> la décision d’utiliser l’authentification unique aura un impact sur l’intégration de votre annuaire local à votre annuaire de cloud. Si vous souhaitez utiliser l’authentification unique, vous devez utiliser la fédération, étant donné que la synchronisation d’annuaires proposera la [même expérience d’authentification](../active-directory/active-directory-aadconnect.md).
>
>

Les organisations qui n’appliquent pas l’authentification unique pour leurs utilisateurs et leurs applications sont davantage exposées à des scénarios, dans lesquels les utilisateurs ont plusieurs mots de passe, ce qui augmente directement la probabilité de réutilisation des mots de passe ou l’utilisation de mots de passe peu sécurisés par les utilisateurs.

Pour en savoir plus sur l’authentification unique Azure AD, lisez l’article [Gestion AD FS et personnalisation avec Azure AD Connect](../active-directory/active-directory-aadconnect-federation-management.md).

## <a name="deploy-password-management"></a>Déploiement de la gestion des mots de passe
Si vous avez plusieurs locataires ou si vous souhaitez permettre aux utilisateurs de [réinitialiser leur mot de passe](../active-directory/active-directory-passwords-update-your-own-password.md), il est important d’utiliser des stratégies de sécurité appropriées afin d’éviter les abus. Dans Azure, vous pouvez tirer parti de la fonctionnalité de réinitialisation de mot de passe en libre-service et personnaliser les options de sécurité pour répondre aux besoins de votre entreprise.

Il est particulièrement important d’obtenir des commentaires à partir de ces utilisateurs et d’apprendre de leurs expériences lorsqu’ils tentent d’effectuer ces étapes. En fonction de ces expériences, élaborez un plan pour atténuer les problèmes potentiels qui peuvent se produire lors du déploiement pour un groupe de plus grande taille. Il est également recommandé d’utiliser le [Rapport d’activité de l’enregistrement de la réinitialisation de mot de passe](../active-directory/active-directory-passwords-get-insights.md) pour surveiller l’inscription des utilisateurs.

Les organisations souhaitant éviter les appels d’aide à la modification des mots de passe, mais permettant aux utilisateurs de réinitialiser eux-mêmes leur mot de passe s’exposent à un plus grand volume d’appels au service d’assistance en raison de problèmes de ce type. Dans les organisations disposant de plusieurs clients, il est impératif d’implémenter ce type de fonctionnalité et de permettre aux utilisateurs de réinitialiser leur mot de passe dans les limites de sécurité établies dans la stratégie de sécurité.

Pour en savoir plus sur la réinitialisation des mots de passe, lisez l’article [Déploiement de la gestion des mots de passe et formation des utilisateurs](../active-directory/active-directory-passwords-best-practices.md).

## <a name="enforce-multi-factor-authentication-mfa-for-users"></a>Application de l’authentification multifacteur (MFA) pour les utilisateurs
Pour les organisations devant se conformer aux normes du secteur, telles que [PCI DSS version 3.2](http://blog.pcisecuritystandards.org/preparing-for-pci-dss-32), l’authentification multifacteur est une fonctionnalité essentielle pour authentifier les utilisateurs. Au-delà de la conformité aux normes du secteur, la mise en œuvre de MFA pour authentifier les utilisateurs permet également aux organisations de limiter les vols d’informations d’identification, tels que les attaques [Pass-the-Hash (PtH)](http://aka.ms/PtHPaper).

En activant Azure MFA pour vos utilisateurs, vous ajoutez une deuxième couche de sécurité aux connexions et transactions des utilisateurs. Dans ce cas, une transaction peut accéder à un document situé sur un serveur de fichiers ou sur votre site SharePoint Online. Azure MFA permet également au département informatique de réduire le risque d’accès aux données de l’organisation par un compte compromis.

Par exemple : mettre en œuvre Azure Multi-Factor Authentication pour vos utilisateurs et la configurer de manière à utiliser un appel téléphonique ou un SMS à des fins de vérification. Si les informations d’identification sont compromises, la personne malveillante ne pourra accéder à aucune ressource, dans la mesure où elle n’a pas accès au téléphone de l’utilisateur. Les organisations qui n’ajoutent pas de couche supplémentaire de protection d’identité sont plus sensibles au vol d’informations d’identification, susceptible de compromettre des données.

Les organisations souhaitant conserver le contrôle complet de l’authentification en local peuvent recourir au [serveur Microsoft Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), ou « MFA local ». Grâce à cette méthode, vous pourrez toujours appliquer l’authentification multi-facteur, tout en conservant le serveur MFA en local.

Pour en savoir plus sur Azure Multi-Factor Authentication, voir [Prise en main avec Azure Multi-Factor Authentication dans le cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Utilisation du contrôle d’accès en fonction du rôle (RBAC)
Restreindre l’accès en fonction des principes du [besoin de connaître](https://en.wikipedia.org/wiki/Need_to_know) et du [privilège minimum](https://en.wikipedia.org/wiki/Principle_of_least_privilege) est impératif pour les organisations désireuses d’appliquer des stratégies de sécurité pour l’accès aux données. La fonction de contrôle d’accès en fonction du rôle (RBAC) d’Azure peut être utilisée pour affecter des autorisations aux utilisateurs, groupes et des applications, à une certaine étendue. L’étendue d’une attribution de rôle peut être une seule ressource, un groupe de ressources ou un abonnement.

Vous pouvez tirer parti des rôles [RBAC intégrés](../active-directory/role-based-access-built-in-roles.md) dans Azure pour affecter des privilèges aux utilisateurs. Envisagez l’utilisation du rôle *Contributeur de comptes de stockage* pour les opérateurs de cloud qui ont besoin de gérer des comptes de stockage, et du rôle *Contributeur de comptes de stockage classiques* pour ceux qui gèrent des comptes de stockage classiques. Pour les opérateurs de cloud qui doivent gérer des machines virtuelles et des comptes de stockage, vous pouvez recourir au rôle *Contributeur de machines virtuelles*.

Les organisations qui n’appliquent aucun contrôle d’accès aux données via des fonctionnalités telles que RBAC risquent d’octroyer plus de privilèges que nécessaire à leurs utilisateurs. Le fait d’autoriser des utilisateurs à accéder à certains types de données (par exemple, des données HBI), auxquelles ils n’avaient pas accès au départ, peut conduire à la compromission de celles-ci.

Vous pouvez en savoir plus sur la fonction RBAC d’Azure en lisant l’article [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

## <a name="control-locations-where-resources-are-created-using-resource-manager"></a>Contrôle des emplacements de création des ressources à l’aide du gestionnaire de ressources
Le fait de permettre aux opérateurs de cloud d’effectuer des tâches tout en les empêchant de briser les conventions qui sont nécessaires à la gestion des ressources de votre organisation est très important. Les organisations qui veulent contrôler les emplacements où les ressources sont créées doivent coder en dur ces emplacements.

Pour ce faire, les organisations peuvent créer des stratégies de sécurité dotées de définitions décrivant les actions ou les ressources spécifiquement refusées. Vous affectez ces définitions de stratégies selon l'étendue souhaitée, au niveau de l'abonnement, du groupe de ressources ou d'une ressource individuelle.

> [!NOTE]
> Cela n’est pas la même chose que la fonction RBAC. Cette dernière est exploitée pour authentifier les utilisateurs ayant le privilège de créer ces ressources.
>
>

Exploitez [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pour créer des stratégies personnalisées également pour les scénarios où l’entreprise souhaite autoriser des opérations uniquement lorsque le centre de coûts approprié est associé ; dans le cas contraire, la demande est refusée.

Les organisations qui ne contrôlent pas comment les ressources sont créées sont plus sensibles aux utilisateurs susceptibles d’abuser du service en créant plus de ressources que nécessaire. Le renforcement du processus de création des ressources est une étape importante de sécurisation d’un scénario à plusieurs locataires.

Pour en savoir plus sur la création de stratégies avec Azure Resource Manager, lisez l’article [Qu’est-ce qu’Azure Policy ?](../azure-policy/azure-policy-introduction.md).

## <a name="guide-developers-to-leverage-identity-capabilities-for-saas-apps"></a>Aide aux développeurs pour tirer parti des fonctionnalités d’identité pour les applications SaaS
L’identité de l’utilisateur est exploitée dans de nombreux scénarios lorsque les utilisateurs accèdent aux [applications SaaS](https://azure.microsoft.com/marketplace/active-directory/all/) qui peuvent être intégrées à l’annuaire local ou de cloud. Tout d’abord, nous conseillons aux développeurs d’utiliser une méthode sécurisée pour développer ces applications, telles que [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). Azure AD simplifie l’authentification pour les développeurs en fournissant l’identité en tant que service, avec la prise en charge des protocoles standard tels que [OAuth 2.0](http://oauth.net/2/) et [OpenID Connect](http://openid.net/connect/), ainsi que des bibliothèques open source pour différentes plateformes.

Veillez à enregistrer toute application qui sous-traite l’authentification à Azure AD. Cette procédure est obligatoire. Cette obligation vient du fait qu’Azure AD a besoin de coordonner la communication avec l’application lors de la gestion de l’authentification unique ou de l’échange de jetons. La session utilisateur expire lorsque la durée de vie du jeton émis par Azure AD arrive à expiration. Évaluez toujours si votre application doit utiliser cette durée ou si vous pouvez réduire ce temps. La réduction de la durée de vie peut agir comme une mesure de sécurité qui oblige les utilisateurs à se déconnecter en fonction d’une période d’inactivité.

Les organisations qui n’appliquent pas le contrôle d’identité pour accéder aux applications et n’indiquent pas aux développeurs comment intégrer de manière sécurisée des applications à leur système de gestion des identités peuvent être plus vulnérables face au vol d’informations d’identification, tel que [l’authentification et la gestion de session faibles décrites dans Open Web Application Security Project (OWASP) Top 10](https://www.owasp.org/index.php/OWASP_Top_Ten_Cheat_Sheet).

Pour en savoir plus sur les scénarios d’authentification pour les applications SaaS, lisez l’article [Scénarios d’authentification pour Azure AD](../active-directory/active-directory-authentication-scenarios.md).

## <a name="actively-monitor-for-suspicious-activities"></a>Surveillance active des activités suspectes
Selon le [rapport 2016 sur les compromissions de données de Verizon](http://www.verizonenterprise.com/verizon-insights-lab/dbir/2016/), la compromission des informations d’identification est toujours en hausse et devient une des activités les plus rentables pour les cybercriminels. Pour cette raison, il est important de disposer d’un système de surveillance des identités pouvant détecter rapidement un comportement suspect et déclencher une alerte pour un examen approfondi. Azure AD dispose de deux fonctions principales qui peuvent aider les organisations à surveiller leurs identités : les [rapports d’anomalies](../active-directory/active-directory-view-access-usage-reports.md) Azure AD Premium et la fonctionnalité de [protection d’identité](../active-directory/active-directory-identityprotection.md) Azure AD.

Veillez à utiliser les rapports d’anomalies pour identifier les tentatives de connexion [sans être suivi](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md), les attaques en [force brute](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) contre un compte particulier, les tentatives de connexion depuis plusieurs emplacements, ainsi que la connexion à partir [d’appareils infectés](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md) et d’adresses IP suspectes. N’oubliez pas qu’il s’agit de rapports. En d’autres termes, vous devez disposer de processus et de procédures permettant aux administrateurs informatiques d’exécuter ces rapports de manière quotidienne ou à la demande (généralement dans un scénario de réponse aux incidents).

En revanche, la protection d’identité Azure AD est un système de surveillance actif qui signale les risques en cours sur son propre tableau de bord. En outre, vous recevrez également des notifications de synthèse quotidiennes par e-mail. Nous vous recommandons d’ajuster le niveau de risque en fonction des besoins de votre entreprise. Le niveau de risque d’un événement à risque est une indication (Élevé, Moyen ou Faible) de la gravité de l’événement à risque. Le niveau de risque permet aux utilisateurs du service Identity Protection de hiérarchiser les mesures qu’ils doivent prendre afin de réduire le risque pour leur organisation.

Les organisations qui ne surveillent pas activement leurs systèmes d’identité risquent de compromettre les informations d’identification des utilisateurs. Si elles n’ont pas connaissance des activités suspectes se déroulant avec ces informations d’identification, elles ne seront pas en mesure de limiter ce type de menace.
Pour en savoir plus sur la protection d’identité Azure, lisez l’article [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md) (Protection de l’identité Azure Active Directory).
