---
title: "Fonctionnalités de sécurité Azure liées à la gestion des identités | Microsoft Docs"
description: " Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure liées à la gestion des identités. Les solutions de gestion des identités et accès de Microsoft aident les services informatiques à protéger l’accès aux applications et ressources dans le centre de données d’entreprise comme dans le cloud, en activant des niveaux supplémentaires de validation telles que l’authentification multifacteur et les stratégies d’accès conditionnel. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 921d7eef90a86216dc13804d3dc4dd216247fb87
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-identity-management-security-overview"></a>Vue d’ensemble de la sécurité et de la gestion des identités Azure
Les solutions de gestion des identités et accès de Microsoft aident les services informatiques à protéger l’accès aux applications et ressources dans le centre de données d’entreprise comme dans le cloud, en activant des niveaux supplémentaires de validation telles que l’authentification multifacteur et les stratégies d’accès conditionnel. En surveillant les activités suspectes via les fonctions avancées de reporting, d’audit et d’alertes de sécurité, vous êtes en mesure de limiter les problèmes de sécurité potentiels. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) fournit une authentification unique à des milliers d’applications cloud (SaaS) et assure un accès aux applications web que vous exécutez en local.

Azure Active Directory (AD) présente de nombreux avantages en termes de sécurité :

* Création et gestion d’une identité unique pour chaque utilisateur de l’entreprise hybride, ce qui favorise la synchronisation des utilisateurs, des groupes et des appareils
* Fourniture d’un accès par authentification unique à vos applications, y compris à des milliers d’applications SaaS pré-intégrées
* Activation de la sécurité d’accès aux applications en appliquant l’authentification multi-facteur basée sur des règles aussi bien pour les applications locales que pour les applications cloud
* Fourniture d’un accès à distance sécurisé aux applications web locales via le proxy d’application Azure AD

Cet article vise à fournir une vue d’ensemble des principales fonctionnalités de sécurité Azure liées à la gestion des identités. Il contient également des liens vers des articles fournissant des informations complémentaires sur chaque fonctionnalité.  

Cet article se concentre sur les principales fonctionnalités de gestion d’identité Azure, à savoir :

* Authentification unique
* Proxy inversé
* Authentification multifacteur
* Surveillance de la sécurité, alertes et rapports Machine Learning
* Gestion des identités et des accès des consommateurs
* Inscription des appareils
* Privileged Identity Management
* Identity Protection
* Gestion des identités hybrides

## <a name="single-sign-on"></a>Authentification unique
Avec l’authentification unique (SSO), vous pouvez accéder à toutes les applications et à toutes les ressources dont vous avez besoin pour travailler, en vous connectant une seule fois avec un seul compte utilisateur. Une fois connecté, vous pouvez accéder à toutes les applications dont vous avez besoin sans devoir vous authentifier à nouveau (par exemple, taper un mot de passe).

De nombreuses entreprises s’appuient sur des applications SaaS comme Office 365, Box et Salesforce pour accroître la productivité des utilisateurs finaux. Historiquement, le personnel informatique devait créer et mettre à jour chaque compte d’utilisateur dans chaque application SaaS et les utilisateurs devaient mémoriser un mot de passe pour chaque application SaaS.

Azure AD étend les environnements Active Directory local dans le cloud, ce qui permet aux utilisateurs d’utiliser leur compte professionnel principal, non seulement pour se connecter à leurs appareils liés au domaine et aux ressources de l’entreprise, mais aussi à toutes les applications SaaS et web nécessaires à leur travail.

Non seulement les utilisateurs n’ont plus besoin de gérer plusieurs noms d’utilisateur et mots de passe, mais l’accès aux applications peut être automatiquement activé ou désactivé en fonction des groupes de l’organisation et de leur statut d’employé. Azure AD ajoute des contrôles de sécurité et de gouvernance de l’accès qui vous permettent de gérer de manière centralisée l’accès des utilisateurs sur les différentes applications SaaS.

En savoir plus :

* [Présentation de l’authentification unique](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../active-directory/active-directory-appssoaccess-whatis.md)
* [Intégrer l’authentification unique Azure Active Directory aux applications SaaS](../active-directory/active-directory-enterprise-apps-manage-sso.md)

## <a name="reverse-proxy"></a>Proxy inversé
Le proxy d’application Azure AD vous permet de publier des applications en local, telles que des sites [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US), des [applications web Outlook](https://technet.microsoft.com/library/jj657718.aspx) et des applications [IIS](http://www.iis.net/) à l’intérieur de votre réseau privé et offre un accès sécurisé aux utilisateurs en dehors de votre réseau. Le proxy d’application assure l’accès à distance et l’authentification unique (SSO) pour de nombreux types d’applications web locales, avec les milliers d’applications SaaS prises en charge par Azure AD. Les employés peuvent se connecter à vos applications depuis leur domicile sur leurs propres appareils et s’authentifier par le biais de ce proxy cloud.

En savoir plus :

* [Activation du proxy d’application Azure AD](../active-directory/active-directory-application-proxy-enable.md)
* [Publier des applications avec le Proxy d’application Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Authentification unique avec le proxy d’application](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
* [Utilisation de l’accès conditionnel](../active-directory/application-proxy-enable-remote-access-sharepoint.md)

## <a name="multi-factor-authentication"></a>Authentification multifacteur
Azure Multi-Factor Authentication (MFA) est une méthode d’authentification qui nécessite l’utilisation de plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Elle fournit une authentification forte par le biais de diverses options de vérification : appel téléphonique, SMS, notification par application mobile ou code de vérification et jetons OAuth tiers.

En savoir plus :

* [Authentification multifacteur](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Présentation d'Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure Multi-Factor Authentication : fonctionnement](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Surveillance de la sécurité, alertes et rapports Machine Learning
Vous pouvez protéger votre entreprise grâce à la surveillance de la sécurité, aux alertes et aux rapports Machine Learning qui identifient les comportements d’accès incohérents. Vous pouvez utiliser les rapports d'accès et d'utilisation Azure Active Directory pour obtenir une visibilité complète sur l'intégrité et la sécurité du répertoire de votre société. Grâce à ces informations, un administrateur de répertoire est capable de déterminer plus précisément les risques de sécurité potentiels et donc de les atténuer au maximum.

Dans le portail Azure, les rapports sont classés de la façon suivante :

* Rapports d’anomalies : contiennent les événements de connexion qui peuvent nous sembler anormaux. Notre objectif est de vous faire part de ces activités et de vous permettre de déterminer si un événement est suspect.
* Rapports d’application intégrée : fournissent des indications sur l’utilisation des applications du cloud au sein de votre société. Azure Active Directory permet d’intégrer des milliers d'applications du cloud.
* Rapports d’erreurs : indiquent les erreurs qui peuvent survenir lors de la configuration de comptes sur des applications externes.
* Rapports spécifiques à l’utilisateur : affichent les données d’activité relatives aux appareils/connexions d’un utilisateur spécifique.
* Journaux d’activité : contiennent un enregistrement de tous les événements audités durant les dernières 24 heures, les derniers 7 jours ou les derniers 30 jours, des modifications d’activité de groupes, et des activités d’enregistrement et de réinitialisation de mot de passe.

En savoir plus :

* [Affichage de vos rapports d’accès et d’utilisation](../active-directory/active-directory-view-access-usage-reports.md)
* [Prise en main de la création de rapports Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
* [Guide Azure Active Directory Reporting Guide](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Gestion des identités et des accès des consommateurs
Pour les applications utilisées par les consommateurs, Azure Active Directory B2C offre un service de gestion de l’identité global et hautement disponible, qui s’adapte à des centaines de millions d’identités. Le service peut être intégré sur l’ensemble des plateformes web et mobiles. Vos consommateurs peuvent se connecter à toutes vos applications via une expérience personnalisable en utilisant leurs comptes de réseaux sociaux existants ou en créant des comptes avec de nouvelles informations d’identification.

Dans le passé, les développeurs d'applications qui souhaitaient inscrire et connecter les consommateurs à leurs applications écrivaient leur propre code. Et ils auraient utilisé des systèmes ou des bases de données locaux pour stocker les noms d'utilisateur et les mots de passe. Azure Active Directory B2C offre à votre organisation un meilleur moyen d’intégrer la gestion des identités des consommateurs dans vos applications à l’aide d’une plateforme sécurisée basée sur des normes et d’un ensemble complet de stratégies extensibles.

Lorsque vous utilisez Azure Active Directory B2C, vos consommateurs peuvent s’inscrire auprès de vos applications à l’aide de leurs comptes sociaux existants (Facebook, Google, Amazon, LinkedIn) ou en créant des informations d’identification (adresse de messagerie et mot de passe, ou nom d’utilisateur et mot de passe).

En savoir plus :

* [Qu’est-ce qu’Azure Active Directory B2C ?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C en version préliminaire : Inscription et connexion de consommateurs à vos applications](../active-directory-b2c/active-directory-b2c-overview.md)
* [Version préliminaire d’Azure Active Directory B2C : types d’applications](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Enregistrement de l’appareil
Azure AD Device Registration est la base des scénarios d’ [accès conditionnel](../active-directory/active-directory-conditional-access-device-registration-overview.md) en fonction de l’appareil. Lors de l’inscription d’un appareil, Azure Active Directory Device Registration fournit une identité à l’appareil qui sera utilisée pour authentifier l’appareil lors de la connexion de l’utilisateur. L’appareil authentifié et les attributs de l’appareil peuvent alors être utilisés pour appliquer des stratégies d’accès conditionnel pour les applications qui sont hébergées sur le cloud et localement.

Quand ils sont associés à une solution de gestion des appareils mobiles comme Intune, les attributs de l’appareil dans Azure Active Directory sont mis à jour avec des informations supplémentaires sur l’appareil. Cela vous permet de créer des règles d’accès conditionnel qui imposent que l’accès à partir des appareils réponde à vos normes de sécurité et de conformité.

En savoir plus :

* [Prise en main du service Azure Active Directory Device Registration](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Le service Azure Active Directory (AD) Privileged Identity Management vous permet de gérer, de contrôler et de surveiller vos identités privilégiées et l’accès aux ressources dans Azure AD et dans d’autres services en ligne Microsoft tels qu’Office 365 ou Microsoft Intune.

Les utilisateurs doivent parfois effectuer des opérations privilégiées dans des ressources Azure ou Office 365, ou dans d'autres applications SaaS. Cela signifie souvent que les entreprises doivent leur donner un accès privilégié permanent à Azure AD. C’est un risque de sécurité croissant pour les ressources hébergées dans le cloud, car les entreprises ne peuvent pas suffisamment surveiller ce que ces utilisateurs font avec leurs privilèges d'administrateur. En outre, si un compte d’utilisateur disposant d’un accès privilégié est compromis, cette seule faille peut affecter sa sécurité globale sur le cloud. Azure AD Privileged Identity Management contribue à minimiser ce risque.

Grâce à Azure AD Privileged Identity Management, vous pouvez :

* Identifier les utilisateurs qui ont un rôle d’administrateur dans Azure AD
* Activer à la demande un accès administrateur « juste à temps » aux services Microsoft Online Services comme Office 365 et Intune
* Obtenir des rapports sur l'historique des accès administrateur et sur les modifications apportées aux affectations de l'administrateur
* Recevoir des alertes sur l'accès à un rôle privilégié

En savoir plus :

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Rôles dans Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Azure AD Privileged Identity Management : comment ajouter ou supprimer un rôle d’utilisateur](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity Protection
Azure AD Identity Protection est un service de sécurité offrant une vue consolidée des événements à risque et des vulnérabilités potentielles qui affectent les identités de votre organisation. Identity Protection tire parti des fonctionnalités existantes de détection des anomalies d’Azure Active Directory (disponibles via les rapports d’activités anormales d’Azure AD) et introduit de nouveaux types d’événements à risque capables de détecter les anomalies en temps réel.

En savoir plus :

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9 : Azure AD and Identity Show: Identity Protection Preview (Émission sur Azure AD et l’identité : présentation d’Identity Protection)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Gestion des identités hybrides
L’approche d’identité de Microsoft s’étend aussi bien au cloud qu’à l’environnement local pour créer une identité d’utilisateur unique pour l’authentification et l’autorisation d’accès à toutes les ressources, indépendamment de l’emplacement.

En savoir plus :

* [Livre blanc sur les identités hybrides](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog de l’équipe Active Directory](https://blogs.technet.microsoft.com/ad/)
