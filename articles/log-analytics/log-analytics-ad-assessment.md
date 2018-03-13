---
title: Optimisation de votre environnement Active Directory avec Azure Log Analytics | Microsoft Docs
description: "La solution Active Directory Health Check permet d’évaluer les risques et l’intégrité de vos environnements à intervalles réguliers."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f026c605b84c5f2b6420e975a06d7c02227efbd9
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-log-analytics"></a>Optimisez votre environnement Active Directory avec la solution Active Directory Health Check dans Log Analytics

![Symbole AD Health Check](./media/log-analytics-ad-assessment/ad-assessment-symbol.png)

La solution Active Directory Health Check permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers. Cet article vous aide à installer et utiliser la solution pour vous permettre de prendre les mesures correctives en vue de régler des problèmes potentiels.

Cette solution fournit une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont classées en quatre domaines pour vous aider à rapidement mesurer les risques et prendre les mesures appropriées.

Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi un problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Une fois la solution ajoutée et le contrôle terminé, le résumé des informations de domaines s’affiche sur le tableau de bord **AD Health Check** de l’infrastructure de votre environnement. Les sections suivantes expliquent comment utiliser les informations du tableau de bord **AD Health Check**, dans lequel vous pouvez afficher et exécuter les actions recommandées pour votre infrastructure de serveur Active Directory.  

![image de la vignette AD Health Check](./media/log-analytics-ad-assessment/ad-healthcheck-summary-tile.png)

![image du tableau de bord AD Health Check](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>configuration requise

* La solution Active Directory Health Check nécessite l’installation d’une version prise en charge du .NET Framework (version 4.5.2 ou ultérieure) sur chaque ordinateur sur lequel est installé Microsoft Monitoring Agent (MMA).  L’agent MMA est utilisé par System Center 2016 - Operations Manager, Operations Manager 2012 R2 et le service Log Analytics.
* La solution prend en charge les contrôleurs de domaine exécutant Windows Server 2008 et 2008 R2, Windows Server 2012 et 2012 R2 et Windows Server 2016.
* Un espace de travail Log Analytics pour ajouter la solution Active Directory Health Check à partir de la Place de marché Azure dans le portail Azure.  Aucune configuration supplémentaire n’est requise.

  > [!NOTE]
  > Une fois que vous avez ajouté la solution, le fichier AdvisorAssessment.exe est ajouté aux serveurs comportant des agents. Les données de configuration sont lues puis envoyées au service Log Analytics dans le cloud pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données.
  >
  >

Pour que vous puissiez contrôler l’intégrité des contrôleurs de domaine qui sont membres du domaine à évaluer, ces derniers ont besoin d’un agent et d’une connectivité à Log Analytics faisant appel à l’une des méthodes prises en charge suivantes :

1. Installez [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) si le contrôleur de domaine n’est pas déjà surveillé par System Center 2016 - Operations Manager ou Operations Manager 2012 R2.
2. S’il est surveillé avec System Center 2016 - Operations Manager ou Operations Manager 2012 R2 et que le groupe d’administration n’est pas intégré au service Log Analytics, le contrôleur de domaine peut être hébergé de manière multiple avec Log Analytics pour collecter des données et les transférer au service, et toujours être surveillé par Operations Manager.  
3. Sinon, si votre groupe d’administration Operations Manager est intégré au service, vous devez ajouter des contrôleurs de domaine pour la collecte de données par le service en suivant la procédure décrite dans [Ajout d’ordinateurs gérés par des agents](log-analytics-om-agents.md#connecting-operations-manager-to-oms) après avoir activé la solution dans votre espace de travail.  

L’agent sur votre contrôleur de domaine qui est rattaché à un groupe d’administration Operations Manager collecte les données et les transfère au serveur d’administration qui lui a été assigné, puis ce dernier les envoie directement au service Log Analytics.  Les données ne sont pas écrites dans les bases de données Operations Manager.  

## <a name="active-directory-health-check-data-collection-details"></a>Détails de la collecte de données Active Directory Health Check

Active Directory Health Check collecte les données provenant des sources suivantes à l’aide des agents que vous avez activés :

- Registre
- LDAP
- .NET Framework
- Journal des événements
- Interfaces ADSI (Active Directory Service Interface)
- Windows PowerShell
- Données de fichier
- Windows Management Instrumentation (WMI)
- API de l’outil DCDIAG
- API de service de réplication de fichiers (NTFRS)
- Code C# personnalisé

Les données sont collectées sur le contrôleur de domaine et transmises à Log Analytics tous les sept jours.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Hiérarchisation des recommandations
Une valeur de pondération déterminant l'importance relative de la recommandation est attribuée à chaque recommandation. Seules les 10 recommandations les plus importantes sont affichées.

### <a name="how-weights-are-calculated"></a>Calcul des pondérations
Les pondérations sont des agrégations de valeurs basées sur trois facteurs clés :

* La *probabilité* qu’une anomalie identifiée cause des problèmes. Une plus grande probabilité attribue un score global supérieur à la recommandation.
* L’*impact* de l’anomalie sur votre organisation si elle devait causer des problèmes. Un plus grand impact attribue un score global supérieur à la recommandation.
* L’*effort* requis pour implémenter la recommandation. Un plus grand effort attribue un score global inférieur à la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage du score total disponible pour chaque domaine. Par exemple, si une recommandation dans le domaine de la sécurité et de la conformité a un score de 5 %, l’implémentation de cette recommandation augmente votre score global de sécurité et conformité de 5 %.

### <a name="focus-areas"></a>Domaines
**Sécurité et conformité** : ce domaine présente les recommandations relatives aux menaces de sécurité potentielles et violations de stratégies d’entreprise, ainsi qu’aux exigences techniques, juridiques et réglementaires.

**Disponibilité et continuité d’activité** : ce domaine présente les recommandations relatives à la disponibilité du service, la résilience de votre infrastructure et la protection de votre activité.

**Performances et évolutivité** : ce domaine présente les recommandations pour aider votre organisation à développer son infrastructure informatique, s’assurer que votre environnement informatique répond aux besoins de performances actuels et est en mesure de répondre à l’évolution des besoins d’infrastructure.

**Mise à niveau, migration et déploiement** : ce domaine présente des recommandations pour vous aider à mettre à niveau, migrer et déployer Active Directory dans votre infrastructure existante.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Faut-il viser un score de 100 % dans chaque domaine ?
Pas nécessairement. Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Toutefois, chaque infrastructure de serveur étant différente, certaines recommandations peuvent être plus ou moins adaptées à votre système. Par exemple, il se peut que certaines recommandations de sécurité soient moins appropriées si vos ordinateurs virtuels ne sont pas connectés à Internet. Certaines recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent des rapports et des données ad hoc de faible priorité. Les problèmes importants pour une entreprise bien établie peuvent l'être moins pour une start-up. Nous vous conseillons donc d'identifier tout d'abord vos domaines prioritaires, puis d'observer l'évolution de vos résultats au fil du temps.

Chaque recommandation inclut une justification de son importance. Servez-vous de cette explication pour évaluer si la mise en œuvre de la recommandation est importante pour vous, en fonction de la nature de vos services informatiques et des besoins de votre organisation.

## <a name="use-health-check-focus-area-recommendations"></a>Utilisation des recommandations des domaines Health Check
Une fois le pack installé, vous pouvez afficher un résumé des recommandations à l’aide de la vignette Health Check de la page de solution dans le portail Azure.

Consultez le résumé des évaluations de conformité pour votre infrastructure, puis explorez les recommandations.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Pour afficher les recommandations relatives à un domaine et prendre des mesures correctives
3. Cliquez sur la vignette **Vue d’ensemble** de votre espace de travail Log Analytics dans le portail Azure.
4. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Active Directory Health Check**.
5. Dans la page **Health Check**, passez en revue les informations récapitulatives dans l’un des panneaux du domaine concerné, puis cliquez sur l’un d’entre eux pour afficher les recommandations correspondantes.
6. Les pages relatives au domaine répertorient les recommandations prioritaires pour votre environnement. Cliquez sur une recommandation sous **Objets affectés** pour en afficher les détails et comprendre pourquoi elle apparaît.<br><br> ![image des recommandations Health Check](./media/log-analytics-ad-assessment/ad-healthcheck-dashboard-02.png)
7. Vous pouvez effectuer les actions correctives suggérées dans **Actions suggérées**. Une fois l’élément traité, les évaluations ultérieures indiquent que des mesures ont été prises et votre score de conformité augmente. Les éléments corrigés apparaissent comme **objets passés**.

## <a name="ignore-recommendations"></a>Ignorer les recommandations
Si vous souhaitez ignorer des recommandations, vous pouvez créer un fichier texte que Log Analytics utilisera pour empêcher les recommandations d’apparaître dans les résultats de votre évaluation.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Pour identifier les recommandations que vous ignorerez
1. Dans le portail Azure, dans la page des espaces de travail Log Analytics, cliquez sur la vignette **Rechercher dans les journaux** pour votre espace de travail sélectionné.
2. Utilisez la requête suivante pour répertorier les recommandations qui ont échoué pour les ordinateurs de votre environnement.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Voici une capture d’écran montrant la requête Recherche de journal : <br><br> ![recommandations ayant échoué](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

3. Choisissez les recommandations que vous souhaitez ignorer. Vous utiliserez les valeurs RecommendationId dans la procédure suivante.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Pour créer et utiliser un fichier texte IgnoreRecommendations.txt
1. Créez un fichier nommé IgnoreRecommendations.txt.
2. Collez ou saisissez chaque valeur RecommendationId de chaque recommandation que Log Analytics doit ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3. Placez le fichier dans le dossier suivant sur chaque ordinateur sur lequel Log Analytics doit ignorer les recommandations.
   * Sur les ordinateurs avec Microsoft Monitoring Agent (connectés directement ou avec Operations Manager) : *lecteur_système*:\Program Files\Microsoft Monitoring Agent\Agent
   * Sur le serveur d’administration Operations Manager 2012 R2 : *lecteur_système*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Sur le serveur d’administration Operations Manager 2016 : *lecteur_système*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Pour vérifier que les recommandations sont ignorées
Une fois le prochain contrôle d’intégrité planifié exécuté, par défaut tous les sept jours, les recommandations spécifiées sont marquées comme *Ignorées* et n’apparaissent pas dans le tableau de bord.

1. Vous pouvez utiliser les requêtes Recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Si vous décidez ultérieurement d’afficher les recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou supprimez les valeurs RecommendationID de ces fichiers.

## <a name="ad-health-check-solutions-faq"></a>Questions fréquentes (FAQ) sur la solution AD Health Check
*Quelle est la fréquence d’exécution d’un contrôle d’intégrité ?*

* Le contrôle s’exécute tous les sept jours.

*Est-il possible de configurer la fréquence d’exécution du contrôle d’intégrité ?*

* Pas pour l'instant.

*Si un autre serveur est découvert après l’ajout d’une solution de contrôle d’intégrité, ce serveur sera-t-il contrôlé ?*

* Oui, une fois découverts, les nouveaux serveurs sont contrôlés tous les sept jours.

*Si un serveur est désactivé, est-il retiré du processus de contrôle d’intégrité ?*

* Si un serveur n'envoie pas de données pendant 3 semaines, il est supprimé.

*Quel est le nom du processus qui effectue la collecte de données ?*

* AdvisorAssessment.exe

*Combien de temps la collecte de données prend-elle ?*

* La collecte de données sur le serveur prend environ 1 heure. Cela peut prendre plus de temps sur les serveurs dotés d'un grand nombre de serveurs Active Directory.

*Est-il possible de configurer les périodes de collecte de données ?*

* Pas pour l'instant.

*Pourquoi afficher uniquement les 10 premières recommandations ?*

* Au lieu d'exploiter une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez afficher la liste détaillée de toutes les recommandations, utilisez Recherche de journal.

*Est-il possible d'ignorer une recommandation ?*

* Oui, consultez la section [Ignorer les recommandations](#ignore-recommendations) ci-dessus.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez [Recherches dans les journaux dans Log Analytics](log-analytics-log-searches.md) pour savoir comment analyser les données et recommandations détaillées d’AD Health Check.
