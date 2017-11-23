---
title: "Détection avancée des menaces Azure | Microsoft Docs"
description: "En savoir plus sur Identity Protection et ses fonctionnalités."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: d2fab26d8ff9f006cfed82685a738b791d0b0624
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="azure-advanced-threat-detection"></a>Détection avancée des menaces Azure
## <a name="introduction"></a>Introduction

### <a name="overview"></a>Vue d'ensemble

Microsoft a développé une série de livres blancs, de présentations de sécurité, de meilleures pratiques et de listes de vérification afin d’aider les clients d’Azure à comprendre les différentes fonctionnalités liées à la sécurité disponibles dans la plateforme Azure et autour de celle-ci. Les rubriques sont aussi précises que variées et sont mises à jour régulièrement. Le présent document fait partie de cette série, comme décrit dans la section Résumé ci-après.

### <a name="azure-platform"></a>Plateforme Azure

Azure est une plateforme de services de cloud public qui prend en charge une large sélection de systèmes d’exploitation, de langages de programmation, d’infrastructures, d’outils, de bases de données et d’appareils.
Elle prend en charge les langages de programmation suivants :
-   Exécuter des conteneurs Linux avec l’intégration Docker.
-   Créer des applications avec JavaScript, Python, .NET, PHP, Java et Node.js
-   Créer des serveurs principaux pour les appareils iOS, Android et Windows.

Les services de cloud public Azure prennent en charge les technologies auxquelles des millions de développeurs et de professionnels de l’informatique font déjà confiance.

Lorsque vous migrez vers un cloud public avec une organisation, cette organisation est chargée de protéger vos données et d’assurer la sécurité et la gouvernance qui encadrent le système.

L’infrastructure d’Azure est conçue de l’installation vers les applications pour héberger des millions de clients simultanément, et constitue une base fiable permettant de répondre aux besoins des entreprises en matière de sécurité. Azure propose un large choix d’options pour configurer et personnaliser la sécurité afin de satisfaire les besoins de vos déploiements d’applications. Ce document vous aide à répondre à ces exigences.

### <a name="abstract"></a>Résumé

Microsoft Azure intègre une fonctionnalité de détection des menaces avancée à travers certains services, comme Azure Active Directory, Azure Operations Management Suite (OMS) et Azure Security Center. Cet ensemble de fonctionnalités et de services de sécurité fournit un moyen simple et rapide de comprendre ce qui se passe dans vos déploiements Azure.

Ce livre blanc vous guide dans les « approches Microsoft Azure » en matière de diagnostic de vulnérabilité des menaces et d’analyse des risques associés aux activités malveillantes dirigées contre les serveurs et autres ressources Azure. Il vous aide à identifier les méthodes d’identification et de gestion des vulnérabilités, qui reposent sur des solutions optimisées par la plateforme Azure et par les services et technologies de sécurité côté client.

Ce livre blanc se concentre sur la technologie de plateforme Azure et les contrôles orientés client, et n’essaie pas de répondre aux considérations relatives aux SLA, modèles de tarification et pratique DevOps.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

![Azure Active Directory Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) est une fonctionnalité de l’édition [Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) offrant une vue d’ensemble des événements à risque et des vulnérabilités potentielles qui affectent les identités de votre organisation. Microsoft sécurise les identités dans le cloud depuis plus de dix ans. Avec Azure AD Identity Protection, Microsoft propose met ces mêmes systèmes de protection à disposition des clients d’entreprise. Identity Protection tire parti des fonctionnalités existantes de détection des anomalies d’Azure AD (disponibles via les [rapports d’activités anormales d’Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)) et introduit de nouveaux types d’événements à risque capables de détecter les anomalies en temps réel.

Identity Protection s’appuie sur des algorithmes d’apprentissage automatique adaptatif et des règles heuristiques pour détecter les anomalies et les événements à risque susceptibles d’indiquer qu’une identité a été compromise. À l’aide de ces données, Identity Protection génère des rapports et des alertes qui vous permettent d’analyser ces événements à risque et de prendre les mesures de correction ou d’atténuation qui s’imposent.

Mais Azure Active Directory Identity Protection est plus qu’un outil de surveillance et de création de rapports. En fonction des événements à risque, Identity Protection calcule le niveau de risque des utilisateurs pour chaque utilisateur, ce qui vous permet de configurer des stratégies basées sur les risques pour protéger automatiquement les identités de votre organisation.

Ces stratégies basées sur les risques, en plus des autres [contrôles d’accès conditionnel](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) fournis par Azure Active Directory et [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), peuvent automatiquement bloquer l’accès ou appliquer des mesures de correction adaptatives qui incluent la réinitialisation de mot de passe et la mise en œuvre l’authentification multifacteur.

### <a name="identity-protections-capabilities"></a>Fonctionnalités du service Identity Protection

Mais Azure Active Directory Identity Protection est bien plus qu’un outil de surveillance et de création de rapports. Pour protéger les identités de votre organisation, vous pouvez configurer des stratégies qui répondent automatiquement aux problèmes détectés lorsqu’un niveau de risque spécifié est atteint. Outre les autres contrôles d’accès conditionnel fournis par Azure Active Directory et EMS, ces stratégies peuvent automatiquement bloquer ou déclencher des mesures de correction adaptatives qui incluent des réinitialisations de mot de passe et la mise en œuvre de l’authentification multifacteur.

Exemples de méthodes qu’utilise Azure Identity Protection pour vous aider à sécuriser vos comptes et identités :

[Détection des événements et des comptes à risque :](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Détection de six types d’événements à risque à l’aide de l’apprentissage automatique et des règles heuristiques
-   Calcul du niveau de risque des utilisateurs
-   Recommandations personnalisées visant à améliorer la posture de sécurité globale en mettant en évidence les vulnérabilités

[Examen des événements à risque :](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Envoi de notifications pour les événements à risque
-   Examen des événements à risque à l’aide d’informations contextuelles et pertinentes
-   Workflows de base pour le suivi des investigations
-   Accès rapide à des mesures de correction telles que la réinitialisation de mot de passe

[Stratégies d’accès conditionnel en fonction des risques :](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Stratégie pour atténuer les connexions à risque en bloquant les connexions ou en imposant des demandes d’authentification multifacteur.
-   Stratégie pour bloquer ou sécuriser les comptes d’utilisateurs à risque
-   Stratégie pour exiger que les utilisateurs s’inscrivent à l’authentification multifacteur

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

Grâce à la [Gestion des identités privilégiées Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

vous pouvez gérer, contrôler et surveiller l’accès au sein de votre organisation. Cela inclut l’accès aux ressources dans Azure AD et d’autres services en ligne Microsoft comme Office 365 ou Microsoft Intune.

Azure AD Privileged Identity Management vous aide à :

-   Recevoir une alerte et un rapport sur les administrateurs Azure AD et bénéficier d’un accès administratif « juste à temps » aux services Microsoft Online Services comme Office 365 et Intune

-   Obtenir des rapports sur l'historique des accès administrateur et sur les modifications apportées aux affectations de l'administrateur

-   Recevoir des alertes sur l'accès à un rôle privilégié

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) est une solution de gestion informatique de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud. La solution OMS étant implémentée sous la forme d’un service informatique, elle peut être opérationnelle rapidement, avec un investissement minimal dans des services d’infrastructure. Les nouvelles fonctionnalités de sécurité sont fournies automatiquement, ce qui vous permet d’économiser sur les coûts de mise à niveau et de maintenance.

En plus de fournir de précieux services de manière autonome, OMS peut intégrer des composants System Center tels que [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/), afin d’étendre dans le cloud vos investissements existants en matière de gestion de la sécurité. System Center et OMS peuvent fonctionner simultanément pour fournir une expérience de gestion hybride.

### <a name="holistic-security-and-compliance-posture"></a>Approche globale de la sécurité et de la conformité

Le [tableau de bord de sécurité et d’audit d’OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) offre une vue détaillée de la sécurité informatique de votre organisation. Elle fournit des requêtes de recherche intégrées pour détecter les problèmes importants qui requièrent votre attention. Le tableau de bord Sécurité et audit est l’écran d’accueil pour tout ce qui se rapporte à la sécurité dans OMS. Il fournit un aperçu global de l’état de sécurité de vos ordinateurs. Il vous permet également de voir tous les événements des dernières 24 heures, des 7 derniers jours ou de n’importe quel intervalle de temps personnalisé.

Les tableaux de bord d’OMS vous permettent de comprendre rapidement et facilement les conditions de sécurité globales de n’importe quel environnement, dans le contexte des opérations informatiques, notamment : évaluation des mises à jour logicielles, évaluation des logiciel anti-programmes malveillants et référentiels de configuration. En outre, les données des journaux de sécurité sont facilement accessibles afin de rationaliser les processus d’audit de sécurité et de conformité.

Le tableau de bord de la solution de sécurité et d’audit d’OMS est organisé en quatre catégories principales :

![Tableau de bord de la solution de sécurité et d’audit d’OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **Domaines de sécurité** : dans cette section, vous pourrez explorer plus en détail les enregistrements de sécurité au fil du temps, accéder à l’évaluation des programmes malveillants, l’évaluation des mises à jour, la sécurité du réseau, les informations d’identité et d’accès, les ordinateurs présentant des événements de sécurité, et accéder rapidement au tableau de bord du Centre de sécurité Azure.

-   **Problèmes notables** : cette option vous permet d’identifier rapidement le nombre de problèmes actifs et leur gravité.

-   **Détections (préversion)** : cette option vous permet d’identifier les modèles d’attaque en visualisant les alertes de sécurité au fur et à mesure qu’elles affectent vos ressources.

-   **Informations sur les menaces** : cette option vous permet d’identifier les modèles d’attaque en visualisant le nombre total de serveurs présentant un trafic IP sortant malveillant, le type de menace malveillante et une carte indiquant l’origine de ces adresses IP.

-   **Requêtes de sécurité communes** : cette option vous fournit une liste des requêtes de sécurité les plus courantes que vous pouvez utiliser pour surveiller votre environnement. Cliquez sur l’une de ces requêtes pour ouvrir le panneau Recherche affichant les résultats de cette requête.

### <a name="insight-and-analytics"></a>Insight & Analytics
Au cœur de [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) se trouve le référentiel OMS, qui est hébergé dans le cloud Azure.

![Insight & Analytics](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Les données sont collectées dans le référentiel à partir de sources connectées en configurant des sources de données et en ajoutant des solutions à votre abonnement.

![abonnement](./media/azure-threat-detection/azure-threat-detection-fig5.png)

Les sources de données et les solutions créeront chacune différents types d'enregistrements avec leur propre jeu de propriétés, mais elles peuvent toujours être analysées ensemble dans des requêtes vers le référentiel. Vous pouvez ainsi utiliser les mêmes outils et méthodes pour travailler avec différents types de données collectées par différentes sources.


La plupart de vos interactions avec Log Analytics s’effectuent via le portail OMS qui s’exécute dans un navigateur et vous fournit un accès aux paramètres de configuration et à plusieurs outils pour analyser et agir sur les données collectées. À partir du portail, vous pouvez utiliser des [recherches de journaux](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) pour créer des requêtes et analyser les données collectées, des [tableaux de bord](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards) personnalisables avec des vues graphiques de vos recherches les plus précieuses, et des [solutions](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) intégrant des fonctionnalités et des outils d’analyse supplémentaires.

![outils d’analyse](./media/azure-threat-detection/azure-threat-detection-fig6.png)

Les solutions permettent d’ajouter des fonctionnalités à Log Analytics. Elles s'exécutent principalement dans le cloud et fournissent une analyse des données collectées dans le référentiel OMS. Elles peuvent également définir de nouveaux types d'enregistrements à collecter qui peuvent être analysés avec des recherches de journaux ou via une interface utilisateur supplémentaire fournie par la solution dans le tableau de bord OMS.
La solution Sécurité et Audit est un exemple de ces types de solutions.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automatisation et contrôle : alerte sur les anomalies de configuration de la sécurité

Azure Automation automatise les processus administratifs grâce à des runbooks basés sur PowerShell et exécutés dans le cloud Azure. Les Runbooks peuvent également être exécutés sur un serveur de votre centre de données local pour gérer des ressources locales. Azure Automation assure la gestion de la configuration avec PowerShell DSC (Desired State Configuration).

![Azure Automation](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Vous pouvez créer et gérer les ressources DSC hébergées dans Azure et les appliquer à des systèmes cloud et locaux pour définir et appliquer automatiquement leur configuration ou pour obtenir des rapports sur les anomalies afin de garantir des configurations de sécurité conformes à la stratégie définie.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center vous permet de protéger vos ressources Azure. Il fournit des fonctions intégrées de surveillance de la sécurité et de gestion des stratégies sur vos abonnements Azure. Au sein du service, vous pouvez définir des stratégies non seulement sur vos abonnements Azure, mais également sur les [groupes de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), pour une granularité accrue.

![Azure Security Center](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Les chercheurs en sécurité de Microsoft sont constamment à l’affût des nouvelles menaces. Ils ont accès à un vaste jeu de télémétrie acquis grâce à la présence globale de Microsoft sur le cloud et localement. Cette collection diverse et étendue de jeux de données permet à Microsoft de détecter de nouveaux modèles et de nouvelles tendances d’attaques dans ses produits locaux destinés au consommateur et aux entreprises, ainsi que dans ses services en ligne.

Azure Security Center peut donc rapidement mettre à jour ses algorithmes de détection, puisque les pirates sont à l’origine d’attaques innovantes de plus en plus sophistiquées. Cette approche permet de faire face à des menaces en pleine mutation.

![Centre de sécurité](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

La détection des menaces d’Azure Security Center fonctionne en collectant automatiquement les informations de sécurité à partir de vos ressources Azure, du réseau et des solutions de partenaires connectées.  Elle analyse ces informations, en corrélant les données issues de plusieurs sources, pour identifier les menaces.
Les alertes de sécurité, ainsi que les recommandations sur la façon de répondre à la menace, sont hiérarchisées dans Azure Security Center.

Azure Security Center emploie des analyses de sécurité avancées allant bien au-delà des approches simplement basées sur la signature. Les innovations en matière de Big Data et de technologies [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) sont mises à profit pour évaluer des événements dans toute la structure du cloud, et permettent ainsi de détecter des menaces qui seraient impossibles à identifier à l’aide de méthodes manuelles et de prédire l’évolution des attaques. Ces analyses de sécurité comprennent les éléments suivants :

### <a name="threat-intelligence"></a>Informations sur les menaces

Microsoft dispose d’une multitude d’informations en matière de menaces à l’échelle mondiale.
La télémétrie provient de plusieurs sources, telles qu’Azure, Office 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DCU) et Microsoft Security Response Center (MSRC).

![Informations sur les menaces](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Les chercheurs reçoivent également les informations sur les menaces partagées par les principaux fournisseurs de services cloud et s’abonnent aux flux d’informations sur les menaces provenant de tiers. Azure Security Center peut utiliser ces informations pour vous alerter en cas de menaces provenant d’éléments malveillants connus. Voici quelques exemples :

-   **Utilisation de la puissance de Machine Learning** : Azure Security Center a accès à une grande quantité de données sur l’activité réseau cloud, qui peuvent être utilisées pour détecter les menaces ciblant vos déploiements Azure. Par exemple :

-   **Détection des attaques par force brute** : Machine Learning permet de créer un schéma historique des tentatives d’accès à distance, qui facilite la détection des attaques par force brute sur les ports SQL, RDP et SSH.

-   **Détection des attaques DDoS sortantes et des botnets** : les attaques ciblant les ressources cloud visent généralement à utiliser la puissance de calcul de ces ressources pour exécuter d’autres attaques.

-   **Nouveaux serveurs d’analyse comportementale et machines virtuelles** : dès lors qu’un serveur ou une machine virtuelle est attaqué(e), les pirates utilisent diverses techniques pour exécuter du code malveillant sur le système ciblé, en évitant la détection, en assurant la persistance des attaques et en contournant les contrôles de sécurité.

-   **Détection des menaces sur Azure SQL Database** : la détection des menaces pour Azure SQL Database identifie les activités de base de données anormales en indiquant les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des bases de données.

### <a name="behavioral-analytics"></a>Analyse comportementale

L’analyse comportementale est une technique qui analyse et compare les données à une collection de modèles connus. Toutefois, ces modèles ne sont pas de simples signatures. Ils sont déterminés par le biais d’algorithmes d’apprentissage automatique appliqués aux ensembles de données massifs.

![Analyse comportementale](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


Ils sont également déterminés à travers une analyse minutieuse des comportements malveillants par des experts. Azure Security Center peut utiliser l’analyse comportementale pour identifier les ressources compromises en se basant sur l’analyse des journaux de la machine virtuelle, des journaux du périphérique réseau virtuel, des journaux Service Fabric, des vidages sur incident et d’autres sources.

En outre, il existe une corrélation avec les autres signaux pour rechercher les preuves d’une campagne généralisée. Ce rapprochement permet d’identifier les événements qui sont cohérents avec les indicateurs de compromission établis.

Voici quelques exemples :
-   **Exécution de processus suspect** : les pirates utilisent diverses techniques pour exécuter des logiciels malveillants en l’absence de détection. Par exemple, un pirate peut donner au programme malveillant le même nom que celui utilisé pour des fichiers système légitimes, mais placer ces fichiers à un autre emplacement, utiliser un nom très similaire à un fichier anodin ou masquer la véritable extension du fichier. Azure Security Center modélise les comportements et surveille les exécutions du processus pour détecter les valeurs aberrantes telles que celles-ci.

-   **Programmes malveillants masqués et tentatives d’exploitation** : des programmes malveillants sophistiqués peuvent échapper aux produits anti-programme malveillant traditionnels en choisissant de ne jamais écrire sur le disque ou en chiffrant des composants logiciels stockés sur le disque. Toutefois, ces logiciels malveillants peuvent être détectés à l’aide de l’analyse de mémoire, car le programme malveillant laisse des traces en mémoire pour pouvoir fonctionner. Lorsque le logiciel se bloque, un vidage sur incident capture une partie de la mémoire au moment de l’incident. En analysant la mémoire dans le vidage sur incident, Azure Security Center peut détecter les techniques utilisées pour exploiter la vulnérabilité des logiciels, accéder aux données confidentielles et persister subrepticement au sein d’un ordinateur compromis sans affecter les performances de votre machine.

-   **Mouvement latéral et reconnaissance interne** : afin de persister dans un réseau compromis et de localiser/récolter des données précieuses, les attaquants tentent souvent de se déplacer latéralement à partir de l’ordinateur compromis vers d’autres ordinateurs au sein du même réseau. Azure Security Center surveille les activités de processus et de connexion afin de détecter les tentatives de développement du pirate au sein du réseau, telles que l’exécution de commande à distance, la détection de réseau et l’énumération de compte.

-   **Scripts PowerShell malveillants** : les pirates peuvent utiliser PowerShell pour exécuter du code malveillant sur des machines virtuelles cibles à des fins diverses. Azure Security Center inspecte l’activité PowerShell à la recherche d’activité suspecte.

-   **Attaques sortantes** : les pirates ciblent souvent les ressources cloud en vue d’utiliser ces ressources pour lancer d’autres attaques. Les machines virtuelles compromises peuvent, par exemple, servir à lancer des attaques par force brute contre d’autres machines virtuelles, envoyer du courrier indésirable, ou analyser les ports ouverts et autres appareils sur Internet. En appliquant l’apprentissage automatique au trafic réseau, Azure Security Center peut détecter lorsque les communications réseau sortantes dépassent la norme. Dans le cas du courrier indésirable, Azure Security Center met également en corrélation le trafic de messagerie inhabituel avec l’intelligence issue d’Office 365 pour déterminer si le courrier est susceptible d’être mal intentionné ou est le résultat d’une campagne de courrier électronique légitime.

### <a name="anomaly-detection"></a>Détection des anomalies

Azure Security Center utilise également la détection des anomalies pour identifier les menaces. Contrairement à l’analyse comportementale (qui dépend des modèles connus dérivés de grands jeux de données), la détection des anomalies est plus « personnalisée » et se concentre sur les lignes de base propres à vos déploiements. L’apprentissage automatique est appliqué pour déterminer l’activité normale de vos déploiements et les règles sont générées pour définir les conditions de valeurs aberrantes pouvant signaler un événement de sécurité. Voici un exemple :

-   **Attaques par force brute RDP/SSH entrantes** : vos déploiements peuvent comporter des machines virtuelles occupées par un nombre plus ou moins important de connexions quotidiennes. Azure Security Center peut déterminer l’activité de connexion de base de ces machines virtuelles et utiliser l’apprentissage automatique pour définir ce qui correspond à des activités normales. Une alerte peut être générée en cas d’écart par rapport à la référence définie pour les caractéristiques de connexion. Là encore, l’apprentissage automatique détermine ce qui est significatif.

### <a name="continuous-threat-intelligence-monitoring"></a>Analyse continue des informations sur les menaces

Azure Security Center s’appuie sur des équipes de recherche de sécurité et de sciences des données implantées dans le monde entier, qui surveillent en continu les évolutions en matière de menaces. Cela inclut les initiatives suivantes :

-   **Analyse des informations sur les menaces** : les informations sur les menaces incluent des mécanismes, des indicateurs, des implications et des conseils pratiques sur les menaces, nouvelles ou existantes. Ces informations sont partagées avec la communauté dédiée à la sécurité, et Microsoft surveille en permanence le flux des informations sur les menaces provenant de sources internes et externes.

-   **Partage de signal** : les informations fournies par les équipes de sécurité sur le portefeuille complet de services cloud et locaux, de serveurs, et d’appareils de point de terminaison client de Microsoft sont partagées et analysées.

-   **Spécialistes de la sécurité Microsoft** : engagement continu avec les équipes Microsoft travaillant dans des domaines de la sécurité spécialisés, tels que la forensique et la détection d’attaques web.

-   **Réglage de la détection** : des algorithmes sont exécutés sur les jeux de données client réels, et les chercheurs en sécurité collaborent avec les clients pour valider les résultats. Les vrais et les faux positifs sont utilisés pour affiner les algorithmes d’apprentissage automatique.

Ces efforts combinés aboutissent à des détections nouvelles et améliorées, dont vous pouvez bénéficier instantanément sans aucune action de votre part.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Fonctionnalités de détection de menaces avancées - Autres services Azure

### <a name="virtual-machine-microsoft-antimalware"></a>Machine virtuelle : Microsoft Antimalware

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) est une solution d’agent unique pour les applications et les environnements client, conçue pour s’exécuter en arrière-plan sans intervention humaine. Vous pouvez déployer la protection en fonction des besoins de vos charges de travail d’application, avec une configuration de base sécurisée par défaut ou une configuration personnalisée avancée, y compris pour la surveillance anti-programmes malveillants. Azure Antimalware est une option de sécurité dédiée aux machines virtuelles Azure qui est automatiquement installée sur toutes les machines virtuelles PaaS Azure.

**Fonctionnalités d’Azure permettant de déployer et activer Microsoft Antimalware pour vos applications**

#### <a name="microsoft-antimalware-core-features"></a>Principales fonctionnalités de Microsoft Antimalware

-   **Protection en temps réel** : surveille l’activité dans Cloud Services et sur les machines virtuelles pour détecter et bloquer l’exécution de logiciels malveillants.

-   **Analyse planifiée** : effectue périodiquement une analyse ciblée pour détecter les logiciels malveillants, y compris les programmes en cours d’exécution.

-   **Correction de logiciels malveillants** : prend automatiquement des mesures sur les programmes malveillants détectés, notamment la suppression ou la mise en quarantaine des fichiers malveillants et le nettoyage des entrées de registre malveillantes.

-   **Mises à jour de signatures** : installe automatiquement les dernières signatures de protection (définitions de virus) pour garantir la mise à jour de la protection selon une fréquence prédéfinie.

-   **Mises à jour du moteur Antimalware** : met automatiquement à jour le moteur Microsoft Antimalware.

-   **Mises à jour de la plateforme Antimalware** : met automatiquement à jour la plateforme Microsoft Antimalware.

-   **Protection active** : signale les métadonnées de télémétrie relatives aux menaces détectées et aux ressources suspectes à Microsoft Azure afin d’assurer une réaction rapide au paysage de menaces en constante évolution, et d’activer la remise de signatures synchrones en temps réel par le biais du système MAPS (système de protection active Microsoft).

-   **Exemples de création de rapport** : crée et fournit des exemples de rapports au service Microsoft Antimalware pour contribuer à améliorer le service et permettre la résolution des problèmes.

-   **Exclusions** : permet aux administrateurs d’applications et de services de configurer certains fichiers, processus et lecteurs pour les exclure de la protection et de l’analyse, entre autres pour des raisons de performances.

-   **Collecte d’événements Antimalware** : enregistre l’intégrité du service Antimalware, les activités suspectes et les mesures de correction prises dans le journal des événements du système d’exploitation et les rassemble dans le compte de stockage Azure du client.

### <a name="azure-sql-database-threat-detection"></a>Azure SQL Database Threat Detection

[Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) est une nouvelle fonctionnalité de sécurité intelligence intégrée dans le service Azure SQL Database. Conçue pour identifier, profiler et détecter 24 heures sur 24 les activités anormales sur la base de données, la fonction Azure SQL Database Threat Detection identifie les menaces potentielles pour la base de données.

Les responsables de la sécurité ou autres administrateurs désignés peuvent obtenir une notification immédiate concernant les activités suspectes qui interviennent sur la base de données. Chaque notification contient des détails sur l’activité suspecte et fournit des recommandations pour vous aider à étudier et corriger la menace.

Azure SQL Database Threat Detection détecte actuellement les vulnérabilités potentielles et les attaque par injection SQL, ainsi que les schémas d’accès anormaux à la base de données.

À réception d’une notification par e-mail transmise par le système de détection des menaces, les utilisateurs peuvent rechercher et consulter les enregistrements d’audit appropriés à l’aide du lien contenu dans le message, qui ouvre une visionneuse d’audit et/ou un modèle Excel d’audit préconfiguré contenant les enregistrements d’audit pertinents identifiés au moment de l’événement suspecte, en utilisant les éléments suivants :
-   Stockage d’audit du serveur/de la base de données, retraçant les activités anormales sur la base de données

-   Table de stockage d’audit pertinente ayant été utilisée au moment de l’événement pour écrire le journal d’audit

-   Enregistrements d’audit dans l’heure suivant l’événement.

-   Enregistrements d’audit associés à un ID d’événement similaire au moment de l’événement (facultatif pour certains détecteurs)

Les fonction SQL Database Threat Detector utilisent les méthodes de détection suivantes :

-   **Détection déterministe** : détecte les séquences suspectes (à partir de règles) dans les requêtes client SQL qui correspondent à des attaques connues. Cette méthodologie offre une haute capacité de détection et génère peu de faux positifs ; elle présente toutefois une couverture assez limitée puisqu’elle relève de la catégorie des « détections atomiques ».

-   **Détection comportementale** : détecte les activités anormales, autrement dit les comportements anormaux de la base de données qui n’ont pas été observés au cours des 30 derniers jours.  Dans un client SQL, une activité anormale peut, par exemple, se traduire par un pic d’échecs de connexion/requête, par un volume important de données extraites, par des requêtes canoniques inhabituelles ou encore par l’utilisation d’adresses IP inconnues pour accéder à la base de données

### <a name="application-gateway-web-application-firewall"></a>Pare-feu d’applications web sur Application Gateway

Le [pare-feu d’applications web](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) est une fonction de [passerelle Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) qui offre une protection pour les applications web qui utilisent la passerelle d’application pour les fonctions de [contrôle de remise d’application](https://kemptechnologies.com/in/application-delivery-controllers) standard. Le pare-feu d’applications web procède en les protégeant contre la plupart des [10 plus courantes vulnérabilités web de l’OWASP](https://www.owasp.org/index.php/Top_10_2010-Main)

![Pare-feu d’applications web sur Application Gateway](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   Protection contre les injections de code SQL

-   Protection de l’exécution de script de site à site

-   Protection contre les attaques web courante comme l’injection de commande, les dissimulations de requêtes HTTP, la séparation de réponse HTTP et les attaques RFI (Remote File Inclusion)

-   Protection contre les violations de protocole HTTP

-   Protection contre les anomalies de protocole HTTP comme un agent-utilisateur hôte manquant et les en-têtes Accept

-   Protection contre les robots, les crawlers et les scanneurs

-   Détection des erreurs de configuration d’application courantes (par exemple, Apache, IIS, etc.)

La configuration WAF au niveau d’Application Gateway vous offre plusieurs avantages :

-   Protection de votre application web contre les vulnérabilités et les attaques web sans modification du code principal.

-   Protection simultanée de plusieurs applications web derrière une passerelle d’application. La passerelle d’application peut héberger jusqu’à 20 sites web derrière une passerelle unique, protégeant ainsi tous ces sites contre les attaques web.

-   Surveillance de votre application web contre les attaques à l’aide de rapports en temps réel générées par les journaux WAF de la passerelle d’application.

-   Certains contrôles de conformité nécessitent que tous les points de terminaison qui accèdent à Internet soient protégés par une solution WAF. En utilisant une passerelle d’application avec WAF activé, vous pouvez remplir ces exigences de conformité.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>API Anomaly Detection intégrée à Azure Machine Learning

Anomaly Detection est une API intégrée à Azure Machine Learning qui permet de détecter les différents types de séquences anormales dans vos données de série chronologique. L’API attribue un score d’anomalie à chaque point de données de la série chronologique, qui peut être utilisé pour générer des alertes, en établissant une surveillance via les tableaux de bord ou en créant une connexion avec vos systèmes de gestion de tickets.

[L’API Anomaly Detection](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) peut détecter les types suivants de schémas anormaux dans les données de séries chronologiques :

-   **Pics et creux** : par exemple, quand vous analysez le nombre d’échecs de connexion pour un service ou le nombre de validations dans un site de commerce électronique, les pics ou creux d’activité inhabituels peuvent indiquer des attaques de sécurité ou des interruptions de service.

-   **Tendances positives et négatives :** lorsque vous surveillez l’utilisation de la mémoire, par exemple, une réduction de taille de la mémoire disponible est signe d’une fuite de mémoire potentielle ; lorsque vous analysez la longueur de la file d’attente d’un service, une tendance à la hausse permanente peut indiquer un problème logiciel sous-jacent.

-   **Changements de niveau et changements dans la plage de valeurs dynamiques :** par exemple, il peut être intéressant d’analyser les changements des niveaux de latence d’un service après une mise à niveau du service, ou encore une réduction des niveaux d’exceptions après une mise à niveau.

L’API de Machine Learning offre les avantages suivants :

-   **Détection flexible et fiable :** les modèles de détection d’anomalies permettent aux utilisateurs de configurer les paramètres de sensibilité et de détecter des anomalies entre les jeux de données saisonnières et non saisonnières. Les utilisateurs peuvent ajuster le modèle de détection d’anomalies pour rendre l’API de détection plus ou moins sensible en fonction de leurs besoins. Cela permet de détecter les anomalies plus ou moins visibles au niveau des données, avec et sans schémas saisonniers.

-   **Détection évolutive et rapide :** l’approche d’analyse traditionnelle, qui repose sur les seuils actuels définis à partir des connaissances d’experts dans le domaine, est coûteuse et ne peut pas être déployée sur des millions de jeux de données qui évoluent de façon dynamique. Les modèles de détection d’anomalies proposés dans cette API s’appuient sur un apprentissage et sont paramétrés automatiquement à partir des données historiques et en temps réel.

-   **Détection proactive et exploitable :** il est possible d’appliquer une détection des lents changements de tendance et de niveau afin d’anticiper les anomalies. Les équipes peuvent utiliser les signaux anormaux rapidement détectés pour étudier et agir sur les aspects problématiques.  En outre, il est possible de développer des modèles d’analyse des causes ainsi que des outils d’alerte sur ce service d’API de détection des anomalies.

L’API de détection des anomalies est une solution efficace pour un large éventail de scénarios, notamment la surveillance de l’intégrité du service et des indicateurs de performance clés, l’IoT, l’analyse des performances et l’analyse du trafic réseau. Voici quelques scénarios courants où cette API peut se révéler utile :
- Les services informatiques ont besoin d’outils pour suivre les événements, les codes d’erreur, les journaux d’utilisation et les performances (processeur, mémoire, etc.) en temps voulu.

-   Les sites de commerce en ligne ont besoin d’effectuer un suivi des activités du client, des pages consultées, du nombre de clics, etc.

-   Les entreprises de services publics, quant à elles, ont besoin d’effectuer un suivi de la consommation d’eau, de gaz, d’électricité et d’autres ressources.

-   Les services de gestion d’installations/bâtiments doivent surveiller la température, l’humidité, le trafic, etc.

-   Les entreprises IoT/fabricants veulent utiliser les données de capteur dans des séries chronologiques pour analyser le flux de travail, la qualité, etc.

-   Les fournisseurs de services, tels que les centres d’appel, doivent analyser les tendances de demande de service, le volume d’incidents, la durée de la file d’attente, etc.

-   Les groupes d’analyse marketing doivent surveiller en temps réel les changements anormaux des KPI commerciaux (par exemple, le volume des ventes, les sentiments du client, les prix).

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) est un composant essentiel de la pile de sécurité de Microsoft Cloud. Cette solution complète peut aider votre organisation lorsque vous cherchez à tirer pleinement parti des promesses des applications cloud, tout en vous laissant le contrôle grâce à une meilleure visibilité dans l’activité. Elle vous permet également de renforcer la protection des données critiques dans les applications cloud.

Grâce à des outils qui vous permettent de détecter le Shadow IT, d’évaluer les risques, d’appliquer des stratégies, d’examiner les activités et d’arrêter les menaces, votre organisation est en mesure de migrer en toute sécurité vers le cloud tout en conservant le contrôle sur ses données critiques.

<table style="width:100%">
 <tr>
   <td>Découvrez</td>
   <td>Découvrez le Shadow IT grâce à Cloud App Security. Bénéficiez d’une meilleure visibilité grâce à une détection des applications, des activités, des utilisateurs, des données et des fichiers dans votre environnement cloud. Détectez les applications tierces connectées à votre cloud.</td>
 </tr>
 <tr>
   <td>Examiner</td>
   <td>Examinez vos applications cloud à l’aide d’outils d’investigation cloud conçus pour explorer en profondeur les applications à risque, les utilisateurs spécifiques et les fichiers de votre réseau. Recherchez des modèles parmi les données collectées à partir de votre cloud. Générez des rapports pour surveiller votre cloud.</td>

 </tr>
 <tr>
   <td>Contrôle</td>
   <td>Limitez les risques en définissant des stratégies et des alertes de manière à accentuer le contrôle sur le trafic réseau cloud. Utilisez Cloud App Security pour migrer vos utilisateurs vers d’autres solutions cloud sécurisées et approuvées.</td>

 </tr>
 <tr>
   <td>Protéger</td>
   <td>Utilisez Cloud App Security pour approuver ou interdire des applications, appliquer des mesures de prévention des fuites de données, contrôler les autorisations et le partage, et générer des alertes et des rapports personnalisés.</td>

 </tr>
 <tr>
   <td>Contrôle</td>
   <td>Limitez les risques en définissant des stratégies et des alertes de manière à accentuer le contrôle sur le trafic réseau cloud. Utilisez Cloud App Security pour migrer vos utilisateurs vers d’autres solutions cloud sécurisées et approuvées.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

Cloud App Security intègre la visibilité à votre cloud :

-   Utilisation de Cloud Discovery pour mapper et identifier votre environnement cloud et les applications cloud utilisées par votre organisation.


-   en approuvant et en interdisant les applications dans votre cloud ;



-   en utilisant des connecteurs faciles à déployer qui tirent parti des API de fournisseurs pour améliorer la visibilité et la gouvernance des applications auxquelles vous vous connectez ;

-   en vous aidant à préserver le contrôle grâce à la définition et à l’optimisation de stratégies.

Cloud App Security exécute une analyse complexe sur les données recueillies auprès de ces sources. Cette solution vous alerte immédiatement en cas d’activités anormales et vous procure une visibilité totale sur votre environnement cloud. Vous pouvez configurer une stratégie dans Cloud App Security et l’utiliser pour protéger tous les éléments de votre environnement cloud.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Fonctionnalités ATD tierces accessibles via la Place de marché Azure

### <a name="web-application-firewall"></a>Pare-feu d’applications web

Le pare-feu d’applications web inspecte le trafic web entrant et bloque les injections SQL, les attaques XSS, les téléchargements de programmes malveillants, les attaques DDoS, ainsi que les autres attaques ciblées sur vos applications web. Ce type de pare-feu inspecte également les réponses des serveurs Web principaux pour prévention de perte de données (DLP). Le moteur de contrôle d’accès intégré permet aux administrateurs de créer des stratégies de contrôle d’accès granulaire pour l’authentification, l’autorisation et la traçabilité (AAA), qui offre aux organisations une authentification renforcée et un meilleur contrôle des utilisateurs.

**Points forts :**
-   Détecte et bloque les injections SQL, les attaques XSS, les téléchargements de logiciels malveillants, les attaques DDoS d’application ou toute autre attaque dirigée contre votre application.

-   Authentification et contrôle d’accès.

-   Analyse le trafic sortant pour détecter les données sensibles, avec la possibilité de masquer ou bloquer la fuite d’informations.

-   Accélère la distribution de contenus d’application web, à l’aide de fonctionnalités telles que la mise en cache, la compression et d’autres optimisations du trafic.

Voici quelques exemples de pare-feu d’applications web disponibles sur la Place de marché Azure :

[Barracuda Web Application Firewall, Brocade Virtual Web Application Firewall (Brocade vWAF), Imperva SecureSphere et The ThreatSTOP IP Firewall.](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>Étapes suivantes

- [Fonctionnalités de détection d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Les fonctionnalités avancées de détection d’Azure Security Center permettent d’identifier les menaces actives ciblant vos ressources Microsoft Azure et fournissent les informations nécessaires pour y répondre rapidement.

- [Azure SQL Database Threat Detection](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Azure SQL Database Threat Detection a permis d’apaiser les inquiétudes concernant les menaces potentielles qui pèsent sur les bases de données.
