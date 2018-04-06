---
title: Bonnes pratiques pour Azure Operational Security | Microsoft Docs
description: Cet article fournit un ensemble de bonnes pratiques pour Azure Operational Security.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: bb7e0df046ecc2ffcd3fa59ac53edf36095933c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-operational-security-best-practices"></a>Bonnes pratiques pour Azure Operational Security
Azure Operational Security renvoie aux services, contrôles et fonctionnalités auxquels les utilisateurs ont accès pour protéger leurs données, leurs applications et d’autres ressources dans Microsoft Azure. Azure Operational Security repose sur une infrastructure qui intègre les connaissances acquises via différentes fonctionnalités spécifiques de Microsoft, y compris Microsoft Security Development Lifecycle (SDL), le programme Microsoft Security Response Center et une connaissance approfondie des cybermenaces.

Dans cet article, nous abordons différentes bonnes pratiques relatives à la sécurité pour les bases de données Azure. Ces bonnes pratiques sont issues de notre expérience dans le domaine de la sécurité des bases de données Azure, mais également de celle des clients comme vous.

Pour chaque bonne pratique, nous détaillons les éléments suivants :
-   Nature de la bonne pratique
-   Raison pour laquelle activer cette bonne pratique
-   Conséquence possible en cas de non-utilisation de la bonne pratique
- Comment apprendre à utiliser la bonne pratique

Cet article repose sur un consensus, ainsi que sur les fonctionnalités et ensembles de fonctions de la plateforme Azure disponibles au moment de la rédaction du présent document. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

Les bonnes pratiques pour Azure Operational Security qui sont abordées dans le cadre de cet article sont les suivantes :

-   Surveiller, gérer et protéger l’infrastructure cloud
-   Gérer l’identité et implémenter l’authentification unique (SSO)
-   Suivre les demandes, analyser les tendances d’utilisation et diagnostiquer les problèmes
-   Surveiller les services avec une solution de surveillance centralisée
-   Prévenir, détecter et traiter les menaces
-   Surveiller le réseau de bout en bout selon un scénario
-   Sécuriser le déploiement à l’aide d’outils DevOps éprouvés

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Surveiller, gérer et protéger l’infrastructure cloud
Le service des opérations informatiques est chargé de gérer l’infrastructure du centre de données, les applications et les données, y compris la stabilité et la sécurité de ces systèmes. Toutefois, l’obtention d’informations de sécurité dans des environnements informatiques de plus en plus complexes requiert souvent de la part des organisations qu’elles bricolent et rassemblent les données de plusieurs systèmes de sécurité et de gestion.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud.

La [solution Security and Audit d’OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) permet au service informatique de surveiller activement toutes les ressources, ce qui peut aider à réduire l’impact des incidents de sécurité. Cette solution possède des domaines de sécurité qui peuvent être utilisés pour la surveillance des ressources.

Pour plus d’informations sur OMS, lisez l’article [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

Pour vous aider à empêcher, détecter et répondre aux menaces, [la solution Security and Audit d’Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) collecte et traite les données concernant vos ressources, ce qui inclut :

-   Journaux des événements de sécurité
-   Suivi d’événements pour les événements Windows (ETW)
-   Événements d’audit AppLocker
-   Journal du pare-feu Windows
-   Événements Advanced Threat Analytics
-   Résultats de l’évaluation de la base de référence
-   Résultats de l’analyse anti-programme malveillant
-   Résultats de l’évaluation des correctifs/mises à jour
-   Flux de données Syslog explicitement activés sur l’agent


## <a name="manage-identity-and-implement-single-sign-on"></a>Gérer l’identité et implémenter l’authentification unique
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) est le service Microsoft de gestion des annuaires et des identités, basé sur le cloud, mutualisé.

[Azure AD](https://azure.microsoft.com/services/active-directory/) inclut également une suite complète de fonctionnalités de [gestion d’identité](https://docs.microsoft.com/azure/security/security-identity-management-overview), comme [l’authentification multifacteur](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), l’inscription d’appareil, la gestion de mot de passe libre-service, la gestion de groupes libre-service, la gestion des comptes privilégiés, le contrôle d’accès en fonction du rôle, la surveillance de l’utilisation de l’application, ainsi que la création d’audits complets, la surveillance de la sécurité et la création d’alertes.

Les fonctions suivantes permettent d’améliorer la sécurité des applications informatiques, de simplifier les processus informatiques, de réduire les coûts et de garantir le respect des objectifs de conformité aux normes du secteur :

-   Gestion des identités et des accès pour le cloud
-   Simplifier l’accès utilisateur sur n’importe quelle application de cloud
-   Protégez les données et les applications sensibles
-   Offrir le libre-service à vos employés
-   Intégrer à Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Gestion des identités et des accès pour le cloud
Azure Active Directory (Azure AD) est une [solution cloud complète pour la gestion des identités et des accès](https://www.microsoft.com/cloud-platform/identity-management), qui vous offre un ensemble performant de fonctionnalités de gestion des utilisateurs et des groupes. Il permet de sécuriser l’accès aux applications locales et cloud, dont des services web Microsoft, tels qu’Office 365 et de nombreuses applications SaaS (software as a service) tierces.
Pour en savoir plus sur l’activation de la protection d’identité dans Azure AD, consultez [Activer Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Simplifier l’accès utilisateur sur n’importe quelle application de cloud
[Activez l’authentification unique](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) afin de simplifier l’accès des utilisateurs à des milliers d’applications de cloud depuis des appareils Windows, Mac, Android et iOS. Les utilisateurs peuvent lancer des applications de cloud depuis un panneau d’accès web personnalisé ou d’une application mobile à l’aide des informations d’identification de leur entreprise. Utilisez le module Proxy d’application d’Azure AD pour dépasser les limites des applications SaaS et publier des applications web locales pour leur fournir un accès distant hautement sécurisé et une fonction d’authentification unique.

### <a name="protect-sensitive-data-and-applications"></a>Protégez les données et les applications sensibles
Activez [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) pour empêcher les accès non autorisés aux applications locales et cloud en fournissant un niveau supplémentaire d’authentification. Protégez votre entreprise et réduisez les menaces potentielles à l’aide d’alertes et d’une surveillance de la sécurité, ainsi que de rapports basés sur un apprentissage automatique qui identifient les comportements d’accès incohérents.

### <a name="enable-self-service-for-your-employees"></a>Offrir le libre-service à vos employés
Déléguez des tâches importantes, telles que la réinitialisation des mots de passe ainsi que la création et la gestion de groupes, à vos employés. [Activez la réinitialisation et la modification du mot de passe en libre-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), ainsi que la gestion de groupes en libre-service avec Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Intégrer à Azure Active Directory
Étendez [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) et d’autres annuaires locaux vers Azure AD pour permettre une authentification unique sur toutes les applications informatiques. Vous pouvez synchroniser automatiquement les attributs utilisateurs à votre annuaire de cloud depuis tous les types d’annuaires locaux.

Pour en savoir plus sur l’intégration d’Azure Active Directory et sur la façon de l’activer, consultez l’article [Intégrer des annuaires locaux à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Suivre les demandes, analyser les tendances d’utilisation et diagnostiquer les problèmes
[L’analyse du stockage Azure](https://docs.microsoft.com/azure/storage/storage-analytics) effectue la journalisation et fournit les données d’indicateurs de performance d’un compte de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d'utilisation et diagnostiquer les problèmes liés à votre compte de stockage

Les métriques de Storage Analytics sont activées par défaut pour les nouveaux comptes de stockage. Vous pouvez activer la journalisation et configurer les métriques et la journalisation dans le portail Azure. Pour plus d’informations, consultez [Surveillance d’un compte de stockage dans le portail Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations Set Service Properties pour activer Storage Analytics individuellement pour chaque service.

Pour obtenir un guide détaillé concernant l'utilisation de Storage Analytics et d'autres outils permettant d'analyser, de diagnostiquer et de résoudre les problèmes d'Azure Storage, consultez [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Pour en savoir plus sur l’intégration d’Azure Active Directory et sur la façon de l’activer, consultez l’article [Enabling and Configuring Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN) (Activation et configuration de Storage Analytics).

## <a name="monitoring-services"></a>Surveillance des services
Les applications cloud sont complexes, et se composent de nombreux éléments mobiles. L’analyse fournit des données visant à garantir que votre application reste opérationnelle et soit exécutée en toute intégrité. Elle vous permet également de parer à des problèmes potentiels ou de résoudre des problèmes déjà survenus. En outre, vous pouvez utiliser les données d’analyse pour obtenir des informations détaillées sur votre application. Ces connaissances peuvent vous aider à améliorer les performances de l’application ou sa facilité de gestion, ou à automatiser des actions qui exigeraient normalement une intervention manuelle.

### <a name="monitor-azure-resources"></a>Surveiller les ressources Azure
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) est le service de plateforme qui fournit une source unique de surveillance des ressources Azure. Avec Azure Monitor, vous pouvez visualiser, interroger, acheminer, archiver et agir sur les mesures et journaux provenant des ressources dans Azure. Vous pouvez travailler avec ces données à l’aide du panneau du portail Monitor, les [applets de commande PowerShell Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), [l’interface de ligne de commande multiplateforme](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) ou les [API REST d’Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Activer la mise à l’échelle automatique avec Azure Monitor
Activez la [mise à l’échelle automatique Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) uniquement aux groupes de machines virtuelles identiques, services cloud, plans App Service et environnements App Service.

### <a name="manage-roles-permissions-and-security"></a>Gérer la sécurité et les autorisations des rôles
De nombreuses équipes ont besoin de [réglementer l’accès aux données et aux paramètres de surveillance](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) strictement. Par exemple, si des membres de votre équipe travaillent exclusivement sur l’analyse (ingénieurs du support technique, ingénieurs devops) ou si vous utilisez un fournisseur de services gérés, vous souhaiterez leur accorder l’accès à l’analyse des données tout en limitant leur capacité à créer, modifier ou supprimer des ressources.

Ce document montre comment appliquer un rôle RBAC de surveillance intégré à un utilisateur dans Azure ou créer vos propres rôles personnalisés pour un utilisateur qui a rapidement besoin d’autorisations limitées pour la surveillance. Il évoque ensuite les considérations de sécurité pour vos ressources liées à Azure Monitor et comment vous pouvez restreindre l’accès aux données contenues.

## <a name="prevent-detect-and-respond-to-threats"></a>Prévenir, détecter et traiter les menaces
La détection des menaces d’Azure Security Center fonctionne en collectant automatiquement les informations de sécurité à partir des ressources Azure, du réseau et des solutions de partenaires connectées. Elle analyse ces informations, souvent issues de plusieurs sources, pour identifier les menaces. Les alertes de sécurité, ainsi que les recommandations sur la façon de répondre à la menace, sont hiérarchisées dans Azure Security Center.

-   [Configurez une stratégie de sécurité](https://docs.microsoft.com/azure/security-center/security-center-policies) pour votre abonnement Azure.
-   Suivez les [recommandations d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) pour protéger vos ressources Azure.
-   Passez en revue et gérez vos [alertes de sécurité](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) actuelles.

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

Le Centre de sécurité propose des fonctions de prévention, de détection et de réponse aux menaces conviviales et efficaces, intégrées dans Azure. Ses fonctionnalités principales sont les suivantes :

-   Comprendre l’état de la sécurité dans le cloud
-   Contrôler la sécurité dans le cloud
-   Déployer des solutions de sécurité dans le cloud intégrées en toute simplicité
-   Détecter les menaces et réagir rapidement

### <a name="understand-cloud-security-state"></a>Comprendre l’état de la sécurité dans le cloud
Utilisez Azure Security Center pour obtenir un aperçu central de l’état de sécurité de toutes vos ressources Azure. Vérifiez d’un coup d’œil que les contrôles de sécurité appropriés sont en place et configurés correctement et identifiez rapidement les ressources nécessitant votre attention.

### <a name="take-control-of-cloud-security"></a>Contrôler la sécurité dans le cloud
Définissez pour vos abonnements Azure des [stratégies de sécurité](https://docs.microsoft.com/azure/security-center/security-center-policies) adaptées aux besoins de votre entreprise en matière de sécurité dans le cloud, ainsi qu’au type d’application ou à la sensibilité des données contenues dans chaque abonnement. Utilisez des recommandations en fonction des stratégies pour guider les propriétaires de ressources dans le processus d’implémentation des contrôles requis (et éviter ainsi les approximations en matière de sécurité dans le cloud).

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Déployer des solutions de sécurité dans le cloud intégrées en toute simplicité
[Activez des solutions de sécurité](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) proposées par Microsoft et ses partenaires, notamment les pare-feu et logiciels anti-programme malveillant les plus performants du secteur. Déployez vos solutions de sécurité grâce à un approvisionnement simplifié (même les modifications relatives au réseau sont configurées pour vous). Les événements de sécurité émis par des solutions partenaires sont collectés automatiquement pour l’analyse et la création d’alertes.

### <a name="detect-threats-and-respond-fast"></a>Détecter les menaces et réagir rapidement
Anticipez les menaces existantes et futures dans le cloud avec une approche intégrée et pilotée par l’analytique. En associant le savoir-faire et les [renseignements de Microsoft en matière de menaces](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) à l’échelle mondiale à des informations sur des événements liés à la sécurité cloud au sein de vos déploiements Azure, Security Center vous permet de détecter les menaces réelles au plus tôt, et réduit le nombre de faux-positifs. Les alertes de sécurité dans le cloud fournissent des insights sur la campagne d’attaque, notamment les événements associés et les ressources affectées, et suggèrent des moyens de remédier aux problèmes afin de récupérer rapidement.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Surveiller le réseau de bout en bout selon un scénario
Les clients créent un réseau de bout en bout dans Azure en orchestrant et composant diverses ressources réseau telles qu’un réseau virtuel, ExpressRoute, Application Gateway, des équilibrages de charge, etc. La surveillance est disponible sur chacune des ressources réseau.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un scénario réseau dans, vers et depuis Azure. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des informations sur votre réseau dans Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatisez la surveillance réseau à distance avec la capture de paquets
Surveillez et diagnostiquez les problèmes réseau sans vous connecter à vos machines virtuelles à l’aide de Network Watcher. Déclenchez la [capture de paquets](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) en définissant des alertes et bénéficiez d’un accès à des informations en temps réel sur le niveau de performance au niveau du paquet. Quand vous identifiez un problème, vous pouvez l’examiner en détail pour effectuer de meilleurs diagnostics.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Obtenez des insights sur votre trafic réseau à l’aide des journaux de flux
Acquérez une meilleure compréhension du modèle de trafic de votre réseau à l’aide des [journaux de flux de groupes de sécurité réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Les informations fournies par les journaux de flux vous aident à recueillir des données sur la conformité, l’audit et la surveillance de votre profil de sécurité réseau.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnostiquez les problèmes de connectivité d’un VPN
Network Watcher vous permet de [diagnostiquer les problèmes courants liés aux connexions et à la passerelle VPN](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Vous pouvez ainsi identifier le problème, mais également utiliser les journaux détaillés créés pour un examen plus poussé du problème.

Pour plus d’informations sur la façon de configurer Network Watcher et de l’activer, consultez l’article [Configure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) (Configurer Network Watcher).

## <a name="secure-deployment-using-proven-devops-tools"></a>Sécuriser le déploiement à l’aide d’outils DevOps éprouvés
Voici certaines pratiques Azure DevOps dans cet espace Microsoft Cloud, qui permettent aux entreprises et aux équipes d’être productives et efficaces.

-   **IaC (Infrastructure as Code) :** IaC est un ensemble de techniques et pratiques grâce auquel les professionnels de l’informatique peuvent supprimer la charge associée à la génération et à la gestion quotidiennes d’une infrastructure modulaire. Il permet aux professionnels de l’informatique de créer et de gérer leur environnement serveur moderne d’une façon similaire à celle dont les développeurs de logiciels génèrent et gèrent le code de l’application. Dans le cas d’Azure, [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) vous permet d’approvisionner vos applications à l’aide d’un modèle déclaratif. Dans un modèle unique, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Le même modèle vous permet de déployer plusieurs fois votre application à chaque phase du cycle de vie de l’application.
-   **Intégration et déploiement continus :** vous pouvez configurer vos projets d’équipe Visual Studio Team Services (VSTS) afin de les [générer et déployer automatiquement](https://www.visualstudio.com/docs/build/overview) sur des applications web Azure ou des services cloud. VSTS déploie automatiquement les fichiers binaires après avoir effectué une génération sur Azure au terme de chaque archivage de code. Le processus de génération du package décrit ici est équivalent à la commande Package de Visual Studio et les étapes de la publication sont les mêmes que pour la commande Publier dans Visual Studio.
-   **Release Management :** Visual Studio [Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) est une excellente solution pour automatiser le déploiement en plusieurs étapes et gérer le processus de mise en production. Créez des pipelines de déploiement gérés et continus pour des publications rapides, faciles et fréquentes. Grâce à Release Management, vous pouvez automatiser une très grande partie du processus de mise en production et avoir des workflows d’approbation prédéfinis. Déployez localement et dans le cloud, étendez et personnalisez en fonction de vos besoins.
-   **Surveillance du niveau de performance des applications :** détectez et résolvez les problèmes, et améliorez en permanence vos applications. Diagnostiquer rapidement les problèmes dans votre application en ligne. Comprendre comment vos utilisateurs l’utilisent. La configuration consiste simplement à ajouter du code JS et une entrée webconfig ; les résultats apparaissent au bout de quelques minutes dans le portail avec tous les détails. [Application Insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) aide les entreprises à détecter et à traiter les problèmes plus rapidement.
-   **Test de charge et mise à l’échelle automatique :** vous pouvez détecter les problèmes de performances dans l’application, afin d’améliorer la qualité du déploiement et vous assurer que l’application est toujours fonctionnelle ou disponible pour répondre aux besoins de l’entreprise. Assurez-vous que votre application peut gérer le trafic de votre prochaine campagne de marketing ou de lancement. Commencez à exécuter des [tests de charge](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) informatiques en un minimum de temps avec VSTS.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez-en plus sur [Azure Operational Security](https://docs.microsoft.com/azure/security/azure-operational-security).
- Pour approfondir vos connaissances, consultez [Operations Management Suite | Security & Compliance](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Prise en main de la solution Security and Audit Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
