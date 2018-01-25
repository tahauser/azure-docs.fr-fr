---
title: Gestion des applications avec Azure Active Directory | Microsoft Docs
description: "Cet article présente les avantages de l’intégration d’Azure Active Directory avec vos applications locales, cloud et SaaS."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 95b96f10-2d5c-4b78-8af8-d3657a24140f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: e825e6be79efad5dfb385f96901a2b5682027963
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="managing-applications-with-azure-active-directory"></a>Gestion des applications avec Azure Active Directory
Au-delà du workflow ou du contenu réels, les entreprises ont deux exigences de base pour toutes les applications :

1. Pour augmenter la productivité, les applications doivent être faciles d’accès
2. Pour activer la sécurité et la gouvernance, l’entreprise a besoin de contrôler et de superviser les utilisateurs qui peuvent accéder à chaque application et ceux qui le font effectivement

Dans le vocabulaire des applications cloud, cela est possible en utilisant l’identité pour contrôler «*QUI est autorisé à faire QUOI*».

Dans la terminologie de l’informatique :

* *Qui* est connu comme *l’identité* : la gestion des utilisateurs et des groupes
* *Quoi* est connu comme la *gestion de l’accès* : la gestion de l’accès aux ressources protégées

Ensemble, les deux composants sont désignés comme la *Gestion de l’identité et de l’accès (IAM)*, que le groupe [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) définit comme « *la discipline de sécurité qui permet aux bonnes personnes d’accéder aux bonnes ressources au bon moment et pour les bonnes raisons* ».

Très bien, alors, quel est le problème ? Si IAM n’est *pas gérée* à un seul emplacement avec une solution intégrée :

* Les administrateurs d’identité doivent créer et mettre à jour des comptes d’utilisateurs individuellement dans toutes les applications séparément, activité redondante et chronophage.
* Les utilisateurs sont tenus de mémoriser différentes informations d’identifications pour accéder aux applications dont ils ont besoin. Par conséquent, les utilisateurs ont tendance à écrire leurs mots de passe ou à utiliser d’autres solutions de gestion des mots de passe, ce qui crée d’autres risques de sécurité des données.
* Les activités redondantes et chronophages réduisent la durée pendant laquelle les utilisateurs et les administrateurs travaillent sur des activités d’entreprise qui augmentent vos résultats.

Alors, de manière générale, qu’est-ce qui empêche les entreprises d’adopter des solutions IAM intégrées ?

* La plupart des solutions techniques reposent sur des plateformes logicielles qui doivent être déployées et adaptées par chaque entreprise pour ses propres applications.
* Les applications cloud sont souvent adoptées à une fréquence plus élevée que la capacité des services informatiques à intégrer des solutions IAM existantes.
* Les outils de sécurité et de surveillance nécessitent une personnalisation et une intégration accrues pour parvenir à des scénarios de bout en bout complets.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory intégré à des applications
Azure Active Directory est une solution Microsoft de type IDaaS (Identity as a Service) qui :

* active IAM en tant que service cloud ; 
* assure la centralisation de la gestion de l’accès, l’authentification unique et le compte-rendu ; 
* prend en charge la gestion intégrée de l’accès à des [milliers d’applications](https://azure.microsoft.com/marketplace/active-directory/) dans la galerie d’applications, notamment à Salesforce, Google Apps, Box, Concur et bien d’autres encore. 

Avec Azure Active Directory, toutes les applications que vous publiez pour vos partenaires et clients (professionnels et particuliers) présentent des fonctionnalités identiques de gestion de l’identité et de l’accès.<br> Vous êtes ainsi en mesure de réduire de manière significative vos coûts opérationnels.

Que se passe-t-il si vous devez implémenter une application qui ne figure pas encore dans la galerie d’applications ? Bien que l’opération soit un peu plus longue que la configuration de l’authentification unique pour les applications de la galerie d’applications, Azure AD vous propose un assistant qui vous aide à effectuer la configuration.

La valeur d’Azure AD va bien au-delà des applications cloud. La solution s’utilise également avec des applications locales, pour lesquelles elle procure un accès sécurisé à distance. Avec l’accès sécurisé à distance, vous pouvez éliminer le recours aux VPN et à d’autres implémentations traditionnelles de gestion de l’accès à distance.

En centralisant la gestion de l’accès et en procurant l’authentification unique pour l’ensemble de vos applications, Azure AD constitue la solution adaptée aux problèmes principaux de sécurité des données et de productivité.

* Les utilisateurs peuvent accéder à plusieurs applications avec une seule connexion, ce qui leur laisse plus de temps pour générer des revenus et pour effectuer des activités commerciales.
* Les administrateurs d’identité peuvent gérer l’accès aux applications depuis un seul emplacement.

Les avantages pour l’utilisateur et pour votre société sont évidents. Examinons de plus près les avantages pour un administrateur d’identité et pour l’entreprise.

## <a name="integrated-application-benefits"></a>Avantages des applications intégrées
Le processus d’authentification unique comporte deux étapes :

* L’authentification, le processus de validation de l’identité de l’utilisateur.
* L’autorisation, la décision d’activer ou de bloquer l’accès à une ressource avec une stratégie d’accès.

Lorsque vous utilisez Azure AD pour gérer des applications et activer l’authentification unique :

* L’authentification est effectuée sur l’installation locale de l’utilisateur (par exemple, AD) ou sur le compte Azure AD.
* L’autorisation s’exécute sur la stratégie Azure AD d’affectation et de protection, garantissant ainsi une expérience utilisateur cohérente, et vous permettant d’ajouter des affectations, des emplacements et des conditions MFA sur toute application, indépendamment de ses fonctionnalités internes.

Il est important de comprendre que la manière dont l’autorisation est mise en œuvre sur l’application cible varie en fonction de la façon dont l’application a été intégrée avec Azure AD.

* **Applications préintégrées par le fournisseur de services** À l’instar d’Office 365 et d’Azure, ce sont des applications directement intégrées sur Azure AD et dépendantes pour l’intégralité de leurs fonctionnalités de gestion de l’identité et de l’accès. L’accès à ces applications est activé via les informations de répertoire et l’émission de jetons.
* **Applications préintégrées par Microsoft et applications personnalisées** Ce sont des applications cloud indépendantes qui s’appuient sur un répertoire d’applications internes et peuvent fonctionner indépendamment d’Azure AD. L’accès à ces applications est activé par l’émission d’informations d’identification spécifiques à une application et mappées sur un compte d’application. Selon les fonctionnalités de l’application, les informations d’identification peuvent être un jeton de fédération ou le nom d’utilisateur et le mot de passe d’un compte précédemment configuré dans l’application.
* **Applications locales** Les applications publiées via le proxy d’application Azure AD permettant principalement l’accès aux applications locales. Ces applications reposent sur un répertoire local central comme Windows Server Active Directory. L’accès à ces applications est activé en déclenchant le proxy pour fournir le contenu de l’application à l’utilisateur final tout en respectant l’exigence d’ouverture de session locale.

Par exemple, si un utilisateur rejoint votre entreprise, vous devez lui créer un compte dans Azure AD pour les opérations principales d’ouverture de session. Si cet utilisateur doit accéder à une application gérée comme Salesforce, vous devez également créer un compte pour cet utilisateur dans Salesforce et associer ce compte au compte Azure pour que l’authentification unique fonctionne. Lorsque l’utilisateur quitte votre entreprise, il est recommandé de supprimer le compte Azure AD et tous les comptes équivalents dans les magasins IAM des applications auxquels l’utilisateur avait accès.

## <a name="access-detection"></a>Détection de l’accès
Dans les entreprises modernes, les services informatiques n’ont souvent pas connaissance de toutes les applications cloud qui sont utilisées. En association avec Cloud App Discovery, Azure AD vous fournit une solution permettant de détecter ces applications.

## <a name="account-management"></a>Account management
Historiquement, la gestion des comptes dans les différentes applications est un processus manuel effectué par le service informatique ou le personnel de support technique dans l’entreprise. Azure AD a entièrement automatisé la gestion des comptes dans toutes les applications intégrées de fournisseur de services, ainsi que les applications préintégrées par Microsoft prenant en charge l’approvisionnement automatique des utilisateurs ou SAML JIT.

## <a name="automated-user-provisioning"></a>Approvisionnement automatique des utilisateurs
Certaines applications fournissent des interfaces d’automatisation pour la création et la suppression (ou la désactivation) de comptes. Si un fournisseur offre une interface de ce type, elle est exploitée par Azure AD. Ainsi, vos coûts d’exploitation sont réduits, car les tâches d’administration sont effectuées automatiquement, et la sécurité de votre environnement est améliorée, car le risque d’accès non autorisé est atténué.

## <a name="access-management"></a>gestion de l’accès
Grâce à Azure AD, vous pouvez gérer l’accès aux applications à l’aide d’affectations individuelles ou pilotées par des règles. Vous pouvez également déléguer la gestion de l’accès aux bonnes personnes dans l’entreprise, assurant ainsi une meilleure supervision et réduisant la charge du support technique.

## <a name="on-premises-applications"></a>Applications locales
Le proxy d’application intégré vous permet de publier vos applications locales auprès de vos utilisateurs, assurant ainsi une expérience d’accès cohérente avec les applications cloud modernes, tout en offrant les fonctionnalités de surveillance, de création de rapports et de sécurité d’Azure AD.

## <a name="reporting-and-monitoring"></a>Création de rapports et surveillance
Azure AD fournit des fonctionnalités préintégrées de création de rapports et de surveillance qui vous permettent de savoir qui a accès aux applications et à quel moment ces utilisateurs s’en servent réellement.

## <a name="related-capabilities"></a>Fonctionnalités associées
Avec Azure AD, vous pouvez sécuriser vos applications avec des stratégies d’accès précises et la MFA préintégrée. Pour en savoir plus sur Azure MFA, consultez [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Prise en main
Pour commencer à intégrer des applications avec Azure AD, consultez le [Guide de prise en main de l’intégration d’Azure Active Directory avec les applications](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Voir aussi
[Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

