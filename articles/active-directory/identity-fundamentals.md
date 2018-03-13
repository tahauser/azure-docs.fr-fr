---
title: "Principes de base de la gestion des identités Azure"
description: "Les identités informatiques sont désormais le moyen le plus efficace de garder le contrôle et une bonne visibilité sur quand et comment les utilisateurs accèdent aux données et aux applications d’entreprise."
keywords: 
author: jeffgilb
manager: mtillman
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: a7eb669aac109ae8cde6d75129004d322c270651
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="fundamentals-of-azure-identity-management"></a>Principes de base de la gestion des identités Azure

Dans un contexte où les ressources digitales d’entreprise sont de plus en plus souvent stockées en dehors du réseau d’entreprise, que ce soit sur le cloud ou sur des appareils, il devient nécessaire de développer les meilleures solutions d’identité informatique et de gestion d’accès possibles. Les identités informatiques sont désormais le moyen le plus efficace de garder le contrôle et une bonne visibilité sur quand et comment les utilisateurs accèdent aux données et aux applications d’entreprise.

Microsoft sécurise les identités dans le cloud depuis plus de dix ans et désormais, avec [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), ces mêmes systèmes de protection vous sont proposés. Avec Azure AD, les administrateurs d’entreprise peuvent facilement responsabiliser les utilisateurs et les administrateurs et leur garantir une sécurité et une gouvernance sans égales.

Azure AD Premium est une solution de gestion des identités et des accès dans le cloud dotée de puissantes fonctions de protection. Elle offre une même identité sécurisée pour toutes les applications, une protection des identités (renforcée par le [graphique de sécurité d’analyse décisionnelle Microsoft](https://www.microsoft.com/en-us/security/intelligence)) et Privileged Identity Management. Azure AD Premium n’est pas un outil de surveillance et de création de rapports de plus. Il peut protéger les identités de vos utilisateurs en temps réel et vous permet de créer des stratégies d’accès adaptatives basées sur les risques afin de préserver les données de votre entreprise.

Regardez cette vidéo pour un aperçu rapide de la protection et de la gestion des identités Azure AD :
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft vous fournit non seulement une identité qui vous permet d’aller où bon vous semble, mais également un ensemble d’outils permettant d’automatiser, de sécuriser et de gérer l’informatique au sein de votre entreprise. Même après l’émergence du cloud computing, il existe bel et bien encore une demande pour gérer et contrôler les tâches informatiques, par exemple les appels au support technique pour réinitialiser les mots de passe des utilisateurs, la gestion des groupes d’utilisateurs et les demandes d’application. Les choses se compliquent d’ailleurs puisque les employés apportent désormais leurs appareils personnels sur le lieu de travail et utilisent des applications SaaS facilement accessibles. À ce titre, garder le contrôle sur leurs applications dans les centres de données de l’entreprise et sur les plateformes de cloud public représente un défi de taille.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Se connecter à Active Directory en local avec Azure AD et Office 365
Les entreprises qui ont investi de manière conséquente dans Active Directory en local peuvent étendre ces investissements au cloud. Pour cela, elles doivent intégrer leurs répertoires en local à Azure AD dans une [gestion des identités hybrides](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Ce faisant, elles augmentent la productivité des utilisateurs en leur fournissant une identité commune pour accéder aux ressources, où qu’ils se trouvent. Les utilisateurs et les entreprises peuvent ensuite utiliser l’authentification unique (SSO) pour accéder à la fois aux ressources en local et aux services dans le cloud comme Office 365.

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) est le seul outil dont vous avez besoin pour parvenir à cette intégration. Azure AD Connect propose différentes fonctionnalités pour prendre en charge vos besoins de synchronisation d’identité. Il remplace les versions antérieures des outils d’intégration d’identité comme DirSync et Azure AD Sync. Avec Azure AD Connect, la gestion des identités et la synchronisation entre les ressources en local et Azure AD sont possibles grâce à :

- Synchronisation : ce composant est chargé de créer des utilisateurs, des groupes et autres objets. Il permet également de s’assurer que les informations d’identité relatives aux utilisateurs et aux groupes dans votre environnement local correspondent à celles qui se trouvent dans le cloud. L’écriture différée de mot de passe peut également être activée pour préserver la synchronisation des répertoires en local lorsqu’un utilisateur met à jour son mot de passe dans Azure AD.
- AD FS : Fédération est une fonctionnalité facultative fournie par Azure AD Connect, qui peut être utilisée pour configurer un environnement hybride à l’aide d’une infrastructure AD FS locale. Fédération permet aux organisations de faire face à des déploiements complexes, par exemple l’authentification unique, l’application de la stratégie de connexion AD ou l’authentification multifacteur à l’aide de cartes à puce ou d’une solution tierce.
- Analyse d’intégrité : [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) peut assurer une surveillance robuste et offrir l’accès à un emplacement central dans le portail Azure pour afficher ces activités.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>Augmenter la productivité et réduire les coûts du support technique avec des expériences en libre-service et d’authentification unique

Les employés sont plus productifs lorsqu’ils disposent d’un seul nom d’utilisateur et d’un seul mot de passe à mémoriser et qu’ils bénéficient d’une expérience cohérente sur chaque appareil. Ils gagnent également du temps lorsqu’ils peuvent réaliser des tâches en libre-service comme [réinitialiser un mot de passe oublié](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) ou demander l’accès à une application sans attendre l’aide du support technique.

Azure AD [étend au cloud l’annuaire Active Directory en local](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Cela permet aux utilisateurs d’utiliser leur compte professionnel principal non seulement pour se connecter aux appareils appartenant au domaine et aux ressources de l’entreprise, mais également à toutes les applications SaaS et web nécessaires à leur travail. De plus, les utilisateurs n’ont plus besoin de gérer plusieurs combinaisons de nom d’utilisateur et mot de passe. L’accès aux applications peut être automatiquement mis en service (ou au contraire retiré) en fonction de leur appartenance aux groupes de l’entreprise et de leur statut en tant qu’employé. Vous pouvez en outre contrôler l’accès aux applications de la galerie ou aux applications en local que vous avez développées et publiées via le [proxy d’application Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started).

## <a name="manage-and-control-access-to-corporate-resources"></a>Gérer et contrôler l’accès aux ressources de l’entreprise
Les solutions de gestion des identités et des accès de Microsoft aident les services informatiques à protéger l’accès aux applications et aux ressources dans le centre de données de l’entreprise, mais aussi dans le cloud. Elles activent des niveaux supplémentaires de validation comme [l’authentification multifacteur](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) et les [stratégies d’accès conditionnel](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). En surveillant les activités suspectes via les fonctions avancées de reporting, d’audit et d’alertes de sécurité, vous êtes en mesure de limiter les problèmes de sécurité potentiels.

Les stratégies d’accès conditionnel dans Azure AD Premium vous offrent, en tant qu’administrateur de l’entreprise, la possibilité de créer des règles d’accès basées sur la stratégie pour n’importe quelle application connectée à Azure AD (applications SaaS, applications personnalisées s’exécutant dans le cloud ou applications web en local). Azure AD évalue ces stratégies en temps réel, puis les applique à chaque fois qu’un utilisateur tente d’accéder à une application. Les stratégies de protection de l’identité Azure vous permettent d’agir immédiatement si la moindre activité suspecte est détectée. Ces actions peuvent inclure le blocage de l’accès pour les utilisateurs à risque élevé, l’application de l’authentification multifacteur, et la réinitialisation des mots de passe utilisateur si les informations d’identification semblent avoir été compromises.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started), qui fait partie de l’offre Azure Active Directory Premium P2, vous permet de découvrir, de restreindre et de surveiller les comptes administratifs et leur accès aux ressources dans Azure Active Directory et autres services en ligne Microsoft. Il vous permet également de gérer l’accès administratif à la demande, pour la durée de votre choix.

Privileged Identity Management peut appliquer des droits d’administrateur à la demande afin que les administrateurs puissent demander une authentification multifacteur ou encore un renforcement temporaire de leurs droits pendant un laps de temps bien défini.

## <a name="benefits-of-azure-identity"></a>Avantages de la gestion des identités Azure

Avec la gestion des identités Azure, vous pouvez :

-   créer et gérer une identité unique pour chaque utilisateur de votre entreprise en préservant la synchronisation des utilisateurs, des groupes et des appareils avec [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) ;

-   faciliter l’accès à vos applications par le biais de l’authentification unique, y compris à des milliers d’applications SaaS pré-intégrées, ou fournir un accès à distance sécurisé aux applications SaaS en local à l’aide du [proxy d’application Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started) ;

-   sécuriser l’accès aux applications en appliquant [l’authentification multifacteur](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) basée sur des règles aussi bien aux applications en local qu’aux applications dans le cloud ;

-   améliorer la productivité des utilisateurs avec la [réinitialisation des mots de passe en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) et les demandes d’accès aux applications et de groupe à l’aide du [portail MyApps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help) ;

-   tirer parti de la [haute disponibilité et fiabilité](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications) d’une solution d’entreprise de gestion des identités et des accès basée sur le cloud de niveau international.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les solutions d’identité Azure](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)