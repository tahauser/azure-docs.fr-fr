---
title: "Guide des opérations et de planification du Centre de sécurité Azure | Microsoft Docs"
description: "Ce document vous aide à planifier au préalable l’utilisation du Centre de sécurité Azure et les considérations concernant les opérations quotidiennes."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f984e4a2-ac97-40bf-b281-2f7f473494c4
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2018
ms.author: yurid
ms.openlocfilehash: 8a44542091ea4dddc214a4954766c63dacd3e2a2
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="azure-security-center-planning-and-operations-guide"></a>Guide des opérations et de planification du Centre de sécurité Azure
Ce guide s’adresse aux informaticiens professionnels, aux architectes informatiques, aux analystes de la sécurité des informations et aux administrateurs de cloud dont les entreprises prévoient d’utiliser le Centre de sécurité Azure.

    
## <a name="planning-guide"></a>Guide de planification
Ce guide couvre un ensemble d’étapes et de tâches que vous pouvez suivre pour optimiser votre utilisation du Centre de sécurité en fonction des critères de sécurité et du modèle de gestion de cloud de votre entreprise. Pour tirer pleinement parti du Centre de sécurité, il est important de comprendre comment les différents utilisateurs ou équipes de votre entreprise utilisent ce service afin de répondre aux besoins de sécurisation du développement et des opérations, de surveillance, de gouvernance et de réponse aux incidents. Les principaux aspects à prendre en compte lorsque vous prévoyez d’utiliser le Centre de sécurité sont les suivants :

* Rôles de sécurité et contrôles d’accès
* Stratégies et recommandations de sécurité
* Collecte et stockage des données
* Ressources non Azure continues
* Surveillance continue de la sécurité
* Réponse aux incidents

Dans la section suivante, vous allez apprendre à planifier chacun de ces aspects et à appliquer ces recommandations en fonction de vos besoins.


> [!NOTE]
> Pour obtenir des réponses utiles lors des phases de conception et de planification, consultez le [Forum aux questions (FAQ) du Centre de sécurité Azure](security-center-faq.md) .
> 

## <a name="security-roles-and-access-controls"></a>Rôles de sécurité et contrôles d’accès
Selon la taille et la structure de votre entreprise, plusieurs personnes et équipes peuvent utiliser le Centre de sécurité pour différentes tâches liées à la sécurité. Le diagramme suivant vous présente un exemple de personnes fictives avec leurs responsabilités et rôles respectifs en termes de sécurité :

![contrôleur](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Le Centre de sécurité permet à ces personnes d’assumer ces différentes responsabilités. Par exemple : 

**Jeff (propriétaire de la charge de travail)**

* Gérer une charge de travail de cloud et ses ressources connexes
* Chargé de l’implémentation et de la maintenance des protections conformément à la stratégie de sécurité de l’entreprise

**Ellen (directrice informatique/responsable de la sécurité des systèmes d’information)**

* Responsable de tous les aspects liés à la sécurité de l’entreprise
* Souhaite comprendre la position de l’entreprise en matière de sécurité à travers les charges de travail de cloud
* Doit être informée des attaques et des risques majeurs

**David (sécurité informatique)**

* Définit les stratégies de sécurité de l’entreprise afin de garantir que les protections appropriées sont en place
* Surveille la conformité aux stratégies
* Génère les rapports pour la direction ou les auditeurs

**Judy (opérations de sécurité)**

* Surveille et répond aux alertes de sécurité 24h/24, 7j/7.
* Rend des comptes au propriétaire de la charge de travail de cloud ou à l’analyste de la sécurité informatique

**Sam (analyste de sécurité)**

* Analyse les attaques
* Collabore avec le propriétaire de la charge de travail de cloud pour mettre en œuvre les solutions 

Azure Security Center utilise le [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) qui fournit des [rôles intégrés](../active-directory/role-based-access-built-in-roles.md) susceptibles d’être attribués à des utilisateurs, des groupes et des services dans Azure. Lorsqu’un utilisateur ouvre le Centre de sécurité, il voit uniquement les informations relatives aux ressources auxquelles il a accès. Autrement dit, l’utilisateur se voit attribuer le rôle de propriétaire, collaborateur ou lecteur, pour l’abonnement ou le groupe de ressources auquel appartiennent les ressources. Outre ces rôles, il existe deux rôles propres à Security Center :

- **Lecteur de sécurité** : un utilisateur qui appartient à ce rôle est en mesure d’afficher uniquement les configurations de Security Center, ce qui inclut les recommandations, les alertes, la stratégie et l’intégrité, mais il ne peut pas apporter de modifications.
- **Administrateur de la sécurité** : il a les mêmes droits que le lecteur de sécurité, mais il peut également mettre à jour la stratégie de sécurité, et rejeter des alertes et des recommandations.

Les rôles de Security Center décrits ci-dessus n’ont pas accès à d’autres zones de service d’Azure (par exemple, stockage, web et mobile, ou Internet des objets).  

> [!NOTE]
> Un utilisateur doit être au moins propriétaire ou collaborateur du groupe de ressources ou de l’abonnement pour voir Azure Security Center. 
> 
> 

En considérant les personnes décrites dans le diagramme précédent, voici le RBAC qui serait nécessaire :

**Jeff (propriétaire de la charge de travail)**

* Propriétaire/collaborateur du groupe de ressources

**David (sécurité informatique)**

* Propriétaire/collaborateur de l’abonnement ou Administrateur de la sécurité

**Judy (opérations de sécurité)**

* Lecteur de l’abonnement ou lecteur de sécurité, autorisé à consulter les alertes
* Propriétaire/collaborateur de l’abonnement ou Administrateur de la sécurité, requis pour clore les alertes

**Sam (analyste de sécurité)**

* Lecteur de l’abonnement, autorisé à consulter les alertes
* Propriétaire/collaborateur de l’abonnement, requis pour clore les alertes
* Accès à l’espace de travail potentiellement requis

Autres informations importantes à prendre en compte :

* Seuls les collaborateurs/propriétaires d’abonnement et les administrateurs de sécurité peuvent modifier une stratégie de sécurité.
* Seuls les propriétaires et collaborateurs d’abonnement et de groupe de ressources peuvent appliquer les recommandations de sécurité pour une ressource.

Lorsque vous planifiez le contrôle d’accès à l’aide de RBAC dans le Centre de sécurité, veillez à identifier qui dans votre entreprise utilisera le Centre de sécurité, ainsi que les types de tâches qu’ils effectueront, puis configurez RBAC en conséquence.

> [!NOTE]
> Nous vous recommandons d’attribuer le rôle le moins permissif permettant aux utilisateurs d’effectuer leurs tâches. Par exemple, les utilisateurs qui n’ont besoin que de consulter des informations sur l’état de sécurité des ressources sans effectuer aucune action, telles que l’application des recommandations ou la modification des stratégies, doivent recevoir le rôle de lecteur.
> 
> 

## <a name="security-policies-and-recommendations"></a>Stratégies et recommandations de sécurité
Une stratégie de sécurité définit la configuration souhaitée de vos charges de travail, tout en garantissant leur conformité aux exigences de sécurité réglementaires. Dans Security Center, vous pouvez définir les stratégies de vos abonnements Azure, qui peuvent être adaptées au type de charge de travail ou à la sensibilité des données.

Les stratégies Security Center comprennent les composants suivant :
- [Collecte des données](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) : paramètres de l’approvisionnement d’agent et de la collecte de données.
- [Stratégie de sécurité](https://docs.microsoft.com/azure/security-center/security-center-policies) : [stratégie Azure](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) qui détermine les contrôles surveillés et recommandés par Security Center. Vous pouvez aussi utiliser une stratégie Azure pour créer des définitions, définir des stratégies supplémentaires et assigner des stratégies à des groupes d’administration.
- [Notifications par e-mail](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) : paramètres des contacts de sécurité et des notifications.
- [Niveau de tarification](https://docs.microsoft.com/azure/security-center/security-center-pricing) : sélection de tarification gratuite ou standard, qui détermine les fonctionnalités Security Center disponibles pour les ressources visées (à spécifier pour les abonnements, les groupes de ressources et les espaces de travail).

> [!NOTE]
> Spécifier un contact de sécurité permettra à Azure de joindre la personne adéquate de votre organisation si un incident survient. Pour plus d’informations sur la mise en œuvre de cette recommandation, consultez [Fournir les détails du contact de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) .

### <a name="security-policies-definitions-and-recommendations"></a>Définitions et recommandations de stratégies de sécurité
Security Center crée automatiquement une stratégie de sécurité par défaut, pour chacun de vos abonnements Azure. Vous pouvez modifier la stratégie dans Security Center ou utiliser la stratégie Azure pour créer des définitions, définir des stratégies supplémentaires et assigner des stratégies à des groupes d’administrations (qui peuvent représenter toute l’organisation, une branche commerciale de cette dernière, etc), et surveiller la conformité des stratégies à ces étendues.

Avant de configurer des stratégies de sécurité, examinez chacune des [recommandations de sécurité](https://docs.microsoft.com/azure/security-center/security-center-recommendations)et déterminer si ces stratégies conviennent à vos différents abonnements et groupes de ressources. Il est également important de comprendre l’action à mener concernant les recommandations de sécurité et de connaître la personne de votre organisation chargée de surveiller les nouvelles recommandations et de prendre les mesures nécessaires.

## <a name="data-collection-and-storage"></a>Collecte et stockage des données
Azure Security Center utilise Microsoft Monitoring Agent (le même agent que celui utilisé par Operations Management Suite et le service Log Analytics) pour collecter les données relatives à la sécurité sur vos machines virtuelles. [Les données collectées](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) à partir de cet agent seront stockées dans vos espaces de travail Log Analytics.

### <a name="agent"></a>Agent

Une fois l’approvisionnement automatique activé dans la stratégie de sécurité, Microsoft Monitoring Agent (pour [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) ou [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) est installé sur toutes les machines virtuelles Azure prises en charge et sur toutes les autres qui sont créées. Si Microsoft Monitoring Agent est déjà installé sur la machine virtuelle ou sur l’ordinateur, Azure Security Center utilise cet agent. Les processus de l’agent sont conçus pour être non invasifs et ont un impact minimal sur les performances de la machine Virtuelle.

Microsoft Monitoring Agent pour Windows requiert le port TCP 443. Consultez l’[article sur la résolution des problèmes](security-center-troubleshooting-guide.md) pour plus d’informations.

Si, à un moment donné, vous souhaitez désactiver la collecte de données, vous pouvez le faire dans la stratégie de sécurité. Toutefois, étant donné que Microsoft Monitoring Agent peut être utilisé par d’autres services de surveillance et de gestion Azure, cet agent n’est pas désinstallé automatiquement lorsque vous désactivez la collecte de données dans Security Center. Vous pouvez le désinstaller manuellement si nécessaire.

> [!NOTE]
> Pour obtenir la liste des machines virtuelles prises en charge, consultez le [forum aux questions (FAQ) sur Azure Security Center](security-center-faq.md).
> 

### <a name="workspace"></a>Espace de travail

Un espace de travail est une ressource Azure servant de conteneur à données. Vous ou d’autres membres de votre organisation pouvez utiliser plusieurs espaces de travail pour gérer différents ensembles de données provenant de tout ou partie de votre infrastructure informatique.

Les données collectées à partir de Microsoft Monitoring Agent (pour le compte d’Azure Security Center) seront stockées dans un espace de travail Log Analytics existant associé à votre abonnement Azure ou dans un nouvel espace de travail, en tenant compte de la zone géographique de la machine virtuelle. 

Dans le portail Azure, vous pouvez parcourir le contenu pour afficher la liste de vos espaces de travail Log Analytics, y compris ceux créés par Azure Security Center. Un groupe de ressources associées sera créé pour les nouveaux espaces de travail. Les deux respecteront la convention d’affectation de noms suivante : 

* Espace de travail : *DefaultWorkspace-[ID d’abonnement]-[zone géographique]*
* Groupe de ressources : *DefaultResouceGroup-[zone géographique]*

Pour les espaces de travail créés par Azure Security Center, les données sont conservées pendant 30 jours. Pour les espaces de travail existants, la rétention dépend du niveau tarifaire de l’espace de travail. Si vous le souhaitez, vous pouvez aussi utiliser un espace de travail existant.

> [!NOTE]
> Microsoft s’engage fermement à protéger la confidentialité et la sécurité de ces données. Microsoft adhère aux recommandations strictes de conformité et de sécurité, du codage jusqu'à l'exploitation d'un service. Pour plus d’informations sur la gestion des données et la confidentialité, lisez [Sécurité des données d’Azure Security Center](security-center-data-security.md).
> 

## <a name="onboarding-non-azure-resources"></a>Intégration de ressources non Azure

Security Center peut surveiller l’état de sécurité de vos ordinateurs autres qu’Azure, mais vous devez d’abord intégrer ces ressources. Pour en savoir plus sur l’intégration de ressources non Azure, consultez [Intégration dans Azure Security Center Standard pour une sécurité renforcée](https://docs.microsoft.com/azure/security-center/security-center-onboarding#onboard-non-azure-computers).

## <a name="ongoing-security-monitoring"></a>Surveillance continue de la sécurité
Après la configuration initiale et la mise en œuvre des recommandations du Centre de sécurité, l’étape suivante détermine les processus opérationnels de ce dernier.

La vue d’ensemble sur Security Center offre une vision unifiée de la sécurité sur toutes les ressources Azure et non-Azure que vous avez connectées. L’exemple ci-dessous montre un environnement dans lequel plusieurs problèmes sont à régler :

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig11.png)

> [!NOTE]
> Le Centre de sécurité n’interfère pas avec vos procédures de fonctionnement normales. Il surveille passivement vos déploiements et fournit des recommandations en fonction des stratégies de sécurité activées.

Lorsque vous choisissez pour la première fois d’utiliser Security Center pour votre environnement Azure, veillez à passer en revue toutes les recommandations, ce que vous pouvez faire dans la mosaïque **Recommandations** ou par ressource (**Compute**, **Mise en réseau**, **Stockage et données** et **Application**).

Une fois toutes les recommandations traitées, la section **Prévention** doit être verte pour toutes les ressources concernées. À ce stade, la surveillance continue devient plus facile puisque vous n’effectuerez des actions qu’en fonction des modifications apportées dans les mosaïques de recommandations et d’intégrité de la sécurité des ressources.

La section **Détection** est plus réactive. Ces alertes concernent les problèmes qui surviennent maintenant ou qui existent déjà et ont été détectés par les contrôles d’Azure Security Center et par des systèmes tiers. La mosaïque Alertes de sécurité affiche des graphiques à barres qui indiquent le nombre d’alertes de détection de menaces enregistrées chaque jour, ainsi que leur répartition au sein des catégories de gravité (faible, moyenne, élevée). Pour plus d’informations sur les alertes de sécurité, voir [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md).

Prévoyez de consulter l’option [Informations sur les menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) dans le cadre de vos opérations de sécurité quotidiennes. Vous pouvez y identifier les menaces de sécurité qui pèsent sur votre environnement. Par exemple, pour déterminer si un ordinateur fait parti d’un botnet.

### <a name="monitoring-for-new-or-changed-resources"></a>Surveillance des ressources nouvelles ou modifiées
La plupart des environnements Azure sont dynamiques, avec des nouvelles ressources ajoutées ou retirées régulièrement, des configurations ou des modifications, etc. Le Centre de sécurité vous offre une visibilité sur l’état de la sécurité de ces nouvelles ressources.

Lorsque vous ajoutez de nouvelles ressources (machines virtuelles, bases de données SQL) dans votre environnement Azure, le Centre de sécurité les détecte automatiquement et commence à surveiller leur sécurité. Cela inclut également les rôles Web et les rôles de travail PaaS. Si la collecte de données est activée dans la [stratégie de sécurité](security-center-policies.md), des fonctionnalités de surveillance supplémentaires sont activées automatiquement pour vos machines virtuelles.

![Principaux aspects](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Pour les machines virtuelles, cliquez sur **Compute**, sous la section **Prévention**. Les problèmes liés à l’activation des données ou aux recommandations associées s’affichent dans l’onglet **Présentation** et dans la section **Recommandations relatives à la surveillance**.
2. Consultez les **recommandations** pour découvrir les éventuels risques de sécurité identifiés pour la nouvelle ressource.
3. Il est très courant, lors de l’ajout de nouvelles machines virtuelles à votre environnement, que seul le système d’exploitation soit installé initialement. Le propriétaire de la ressource peut avoir besoin de temps pour déployer d’autres applications qui seront utilisées par ces machines virtuelles.  L’idéal est de connaître le but de cette charge de travail. Va-t-il s’agir d’un serveur d’applications ? Selon la nature de cette nouvelle charge de travail, vous pouvez activer la **stratégie de sécurité**appropriée, ce qui constitue la troisième étape de cette charge de travail.
4. Lorsque de nouvelles ressources sont ajoutées à votre environnement Azure, il est possible que les nouvelles alertes s’affichent dans la mosaïque **Alertes de sécurité** . Vérifiez toujours si des nouvelles alertes s’affichent dans cette mosaïque et prenez des mesures en fonction des recommandations du Centre de sécurité.

Vous devez également surveiller régulièrement l’état des ressources existantes pour identifier les modifications de configuration qui sont à l’origine de risques de sécurité, d’alertes de sécurité et de dérive par rapport aux lignes de base recommandées. Commencez dans le tableau de bord du Centre de sécurité. Là, consultez régulièrement trois zones principales.

![Opérations](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. Le panneau **Prévention** vous permet d’accéder rapidement à vos principales ressources. Utilisez cette option pour surveiller les éléments Compute, Mise en réseau, Stockage et donnée, et Applications.
2. Le panneau **Recommandations** vous permet de consulter les recommandations du Centre de sécurité. La surveillance continue peut signaler que vous n’avez aucune recommandation aujourd’hui, ce qui est normal puisque vous les avez toutes traitées pendant la configuration initiale du Centre de sécurité. Voilà pourquoi cette section peut ne pas afficher de nouvelles informations tous les jours et ne doit être consultée qu’en cas de besoin.
3. La section **Détection** peut changer très fréquemment ou très rarement. Passez toujours en revue les alertes de sécurité et prenez des mesures en fonction des recommandations du Centre de sécurité.

### <a name="hardening-access-and-applications"></a>Renforcement des applications et de l’accès

Dans le cadre de vos opérations de sécurité, vous devez entreprendre des mesures préventives pour restreindre l’accès aux machines virtuelles, et contrôler les applications exécutées sur ces dernières. En verrouillant le trafic entrant de vos machines virtuelles, vous réduisez l’exposition aux attaques et fournissez en même temps un accès simple pour se connecter aux machines virtuelles si nécessaire. Utilisez la fonctionnalité d’accès [Juste à temps à la machine virtuelle](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) pour renforcer l’accès à vos machines virtuelles. 

Vous pouvez utiliser les [contrôles d’application adaptative](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) pour vous aider à contrôler les applications et à décider lesquelles peuvent s’exécuter sur vos machines virtuelles situées dans Azure. Cela vous permet notamment de renforcer vos machines virtuelles contre les programmes malveillants. Security Center utilise le machine learning pour analyser les processus en cours d’exécution sur la machine virtuelle et exploite ces informations pour vous aider à appliquer les règles de mise en liste verte.


## <a name="incident-response"></a>Réponse aux incidents
Le Centre de sécurité détecte les menaces et vous les signale dès qu’elles se produisent. Les organisations doivent surveiller les nouvelles alertes de sécurité et réagir soit en examinant l’attaque plus en détail,soit en la contrant. Pour plus d’informations sur le fonctionnement de la détection des menaces par le Centre de sécurité, voir [Fonctionnalités de détection d’Azure Security Center](security-center-detection-capabilities.md).

Même si cet article ne vise pas à vous aider à créer votre propre plan de réponse aux incidents, nous allons utiliser le cycle de vie Microsoft Azure Security Response in the Cloud comme base pour les étapes de réponse aux incidents. Les étapes sont présentées dans le diagramme suivant :

![Activité suspecte](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Vous pouvez utiliser le document [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guide de gestion des incidents de sécurité informatique) du NIST (National Institute of Standards and Technology) comme référence pour vous aider à créer votre plan.
> 

Vous pouvez utiliser les alertes du Centre de sécurité lors des étapes suivantes :

* **Détecter**: identifier une activité suspecte dans une ou plusieurs ressources. 
* **Évaluer**: procéder à l’évaluation initiale pour obtenir plus d’informations sur l’activité suspecte.
* **Diagnostiquer**: suivre les étapes techniques de résolution pour remédier au problème.

Chaque alerte de sécurité fournit des informations permettant de mieux comprendre la nature de l’attaque et propose des solutions possibles. Certaines alertes fournissent également des liens vers des informations plus détaillées ou d’autres sources d’information dans Azure. Vous pouvez utiliser les informations fournies pour d’autres recherches et pour commencer l’atténuation. Vous pouvez également rechercher des données relatives à la sécurité qui sont stockées dans votre espace de travail.

L’exemple suivant montre une activité RDP suspecte :

![Activité suspecte](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Cette page affiche des informations sur l’heure de l’attaque, le nom d’hôte source, la machine virtuelle cible, et détaille une procédure recommandée. Parfois, aucune information ne sera disponible concernant la source de l’attaque. Pour plus d’informations sur ce type de comportement, voir [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) (Informations sur la source manquantes dans les alertes Azure Security Center).

Depuis cette page, vous pouvez aussi lancer un [examen](https://docs.microsoft.com/azure/security-center/security-center-investigation) pour mieux comprendre la chronologie de l’attaque et comment elle s’est produite, les systèmes potentiellement compromis, les informations d’identification utilisées, et pour voir une représentation graphique de l’ensemble de la chaîne d’attaque.

Une fois le système compromis identifié, vous pouvez exécuter les [playbooks](https://docs.microsoft.com/azure/security-center/security-center-playbooks) de sécurité créés précédemment. Un playbook de sécurité est un ensemble de procédures exécutable depuis Security Center lorsqu’un playbook spécifique est déclenché depuis une alerte sélectionnée.

La vidéo [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) (Comment tirer profit d’Azure Security Center et de Microsoft Operations Management Suite pour répondre aux incidents) présente des démonstrations pouvant vous aider à comprendre comment Azure Security Center peut être utilisé dans chacune de ces étapes.

> [!NOTE]
> Consultez l’article [Utilisation d’Azure Security Center pour la réponse aux incidents](security-center-incident-response.md) pour plus d’informations sur l’utilisation des fonctionnalités d’Azure Security Center afin de vous aider lors du processus de réponse aux incidents. 
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez vu comment planifier l’adoption du Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans le Centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Surveillance des solutions de partenaire avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaire.
* [FAQ d’Azure Security Center](security-center-faq.md) : découvrez les réponses aux questions les plus souvent posées à propos de l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : accédez à des billets de blog sur la sécurité et la conformité Azure.

