---
title: Optimisation de votre environnement SQL Server avec Azure Log Analytics | Microsoft Docs
description: "Avec Azure Log Analytics, la solution SQL Health Check permet d’évaluer les risques et l’intégrité de vos environnements à intervalles réguliers."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5da04e9479ebd6cec886a8c5ca38d040aec2758d
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-log-analytics"></a>Optimisez votre environnement SQL avec la solution SQL Server Health Check dans Log Analytics

![Symbole SQL Health Check](./media/log-analytics-sql-assessment/sql-assessment-symbol.png)

La solution SQL Health Check permet d’évaluer les risques et l’intégrité de vos environnements de serveurs à intervalles réguliers. Cet article vous aidera à installer la solution pour vous permettre de prendre les mesures correctives pour régler des problèmes potentiels.

Cette solution fournit une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont classées en six domaines pour vous aider à rapidement mesurer les risques et prendre les mesures correctives appropriées.

Les recommandations proposées sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi un problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Une fois la solution ajoutée et l’évaluation terminée, le récapitulatif des informations des domaines concernés s’affiche sur le tableau de bord **SQL Health Check** de l’infrastructure de votre environnement. Les sections suivantes expliquent comment utiliser les informations du tableau de bord **SQL Health Check**, dans lequel vous pouvez afficher et exécuter les actions recommandées pour votre infrastructure SQL Server.

![image de la vignette SQL Health Check](./media/log-analytics-sql-assessment/sql-healthcheck-summary-tile.png)

![image du tableau de bord SQL Health Check](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Prérequis

* La solution SQL Health Check nécessite l’installation d’une version prise en charge de .NET Framework 4 sur chaque ordinateur sur lequel est installé Microsoft Monitoring Agent (MMA).  L’agent MMA est utilisé par System Center 2016 - Operations Manager, Operations Manager 2012 R2 et le service Log Analytics.  
* La solution prend en charge SQL Server 2012, 2014 et 2016.
* Espace de travail Log Analytics pour ajouter la solution SQL Health Check à partir de la Place de marché Azure dans le portail Azure.  Pour installer la solution,vous devez être administrateur ou collaborateur dans l’abonnement Azure.

  > [!NOTE]
  > Une fois que vous avez ajouté la solution, le fichier AdvisorAssessment.exe est ajouté aux serveurs comportant des agents. Les données de configuration sont lues puis envoyées au service Log Analytics dans le cloud pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données.
  >
  >

Pour que vous puissiez contrôler l’intégrité de vos serveurs SQL Server, ces derniers ont besoin d’un agent et d’une connectivité à Log Analytics faisant appel à l’une des méthodes prises en charge suivantes :

1. Installez [Microsoft Monitoring Agent (MMA)](log-analytics-windows-agent.md) si le serveur n’est pas déjà surveillé par System Center 2016 - Operations Manager ou Operations Manager 2012 R2.
2. S’il est surveillé avec System Center 2016 - Operations Manager ou Operations Manager 2012 R2 et que le groupe d’administration n’est pas intégré au service Log Analytics, le serveur peut être multirésident avec Log Analytics pour collecter des données et les transférer au service, et quand même être surveillé par Operations Manager.  
3. Sinon, si votre groupe d’administration Operations Manager est intégré au service, vous devez ajouter des contrôleurs de domaine pour la collecte de données par le service en suivant la procédure décrite dans [Ajout d’ordinateurs gérés par des agents](log-analytics-om-agents.md#connecting-operations-manager-to-oms) après avoir activé la solution dans votre espace de travail.  

L’agent sur votre serveur SQL Server qui est rattaché à un groupe d’administration Operations Manager collecte les données et les transfère au serveur d’administration qui lui a été assigné, puis ce dernier les envoie directement au service Log Analytics.  Les données ne sont pas écrites dans les bases de données Operations Manager.  

Si le serveur SQL Server est surveillé par Operations Manager, vous devez configurer un compte d’identification Operations Manager. Consultez la rubrique [Comptes d’identification Operations Manager pour Log Analytics](#operations-manager-run-as-accounts-for-log-analytics) ci-dessous pour plus d’informations.

## <a name="sql-health-check-data-collection-details"></a>Détails de la collecte des données SQL Health Check
SQL Health Check collecte les données provenant des sources suivantes à l’aide de l’agent que vous avez activé :

* Windows Management Instrumentation (WMI)
* Registre
* Compteurs de performances
* Résultats de la vue de gestion dynamique SQL Server

Les données sont collectées sur le serveur SQL Server et transmises à Log Analytics tous les sept jours.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Comptes d’identification Operations Manager pour Log Analytics
Log Analytics utilise le groupe d’administration et l’agent d’Operations Manager pour collecter et envoyer des données au service Log Analytics. Log Analytics utilise les packs d’administration pour les charges de travail afin de fournir des services à valeur ajoutée. Chaque charge de travail nécessite des privilèges spécifiques de la charge de travail pour exécuter les packs d’administration dans un contexte de sécurité différent, comme un compte d’utilisateur de domaine. Vous devez fournir les informations d’identification en configurant un compte d’identification Operations Manager.

Utilisez les informations suivantes afin de définir le compte d’identification Operations Manager pour SQL Health Check.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Définir le compte d’identification pour SQL Health Check
 Si vous utilisez déjà le pack d’administration de SQL Server, vous devez utiliser cette configuration d’identification.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Configuration du compte d’identification SQL dans la console Operations
> [!NOTE]
> Par défaut, les flux de travail dans le pack d’administration s’exécutent dans le contexte de sécurité du compte système local. Si vous utilisez Microsoft Monitoring Agent connecté directement au service plutôt que le rattachement direct à un groupe d’administration Operations Manager, ignorez les étapes 1 à 5 ci-dessous et exécutez l’exemple T-SQL ou PowerShell, en spécifiant NT AUTHORITY\SYSTEM comme nom d’utilisateur.
>
>

1. Dans Operations Manager, ouvrez la console Operations, puis cliquez sur **Administration**.
2. Sous **Configuration d’identification**, cliquez sur **Profils**, puis ouvrez **Profil d’identification de l’évaluation SQL OMS**.
3. Dans la page **Comptes d’identification**, cliquez sur **Ajouter**.
4. Sélectionnez un compte d'identification Windows qui contient les informations d'identification nécessaires pour SQL Server ou cliquez sur **Nouveau** pour en créer un.

   > [!NOTE]
   > Le type de compte d’identification doit être Windows. Le compte d’identification doit également faire partie du groupe Administrateurs local sur tous les serveurs Windows hébergeant des instances de SQL Server.
   >
   >
5. Cliquez sur **Enregistrer**.
6. Modifiez, puis exécutez l’exemple T-SQL suivant sur chaque instance de SQL Server afin d’accorder les autorisations minimales requises pour que le compte d’identification puisse effectuer le contrôle d’intégrité. Toutefois, vous n’avez pas besoin de faire cela si un compte d’identification fait déjà partie du rôle de serveur sysadmin sur les instances de SQL Server.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Configuration du compte d’identification SQL à l’aide de Windows Powershell
Ouvrez une fenêtre PowerShell et exécutez le script suivant après l’avoir mis à jour avec vos informations :

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Hiérarchisation des recommandations
Une valeur de pondération déterminant l'importance relative de la recommandation est attribuée à chaque recommandation. Seules les dix recommandations les plus importantes sont affichées.

### <a name="how-weights-are-calculated"></a>Calcul des pondérations
Les pondérations sont des agrégations de valeurs basées sur trois facteurs clés :

* La *probabilité* qu'une anomalie identifiée cause des problèmes. Une plus grande probabilité attribue un score global supérieur à la recommandation.
* L’*impact* de l’anomalie sur votre organisation si elle devait causer des problèmes. Un plus grand impact attribue un score global supérieur à la recommandation.
* L’*effort* requis pour implémenter la recommandation. Un plus grand effort attribue un score global inférieur à la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage du score total disponible pour chaque domaine. Par exemple, si une recommandation dans le domaine de la sécurité et de la conformité a un score de 5 %, l'implémentation de cette recommandation augmentera votre score global de sécurité et conformité de 5 %.

### <a name="focus-areas"></a>Domaines
**Sécurité et conformité** : ce domaine présente les recommandations relatives aux menaces de sécurité potentielles et violations de stratégies d’entreprise, ainsi qu’aux exigences techniques, juridiques et réglementaires.

**Disponibilité et continuité d’activité** : ce domaine présente les recommandations relatives à la disponibilité du service, la résilience de votre infrastructure et la protection de votre activité.

**Performances et évolutivité** : ce domaine présente les recommandations pour aider votre organisation à développer son infrastructure informatique, s’assurer que votre environnement informatique répond aux besoins de performances actuels et est en mesure de répondre à l’évolution des besoins d’infrastructure.

**Mise à niveau, migration et déploiement** : ce domaine présente les recommandations pour vous aider à mettre à niveau, migrer et déployer SQL Server dans votre infrastructure existante.

**Opérations et surveillance** : ce domaine présente des recommandations relatives à la rationalisation de vos opérations informatiques, la mise en place une maintenance préventive et l’optimisation des performances.

**Gestion des modifications et configuration** : ce domaine présente des recommandations pour vous aider à protéger vos opérations quotidiennes, vous assurer que les modifications n’affectent pas votre infrastructure, établir des procédures de contrôle des modifications, et effectuer le suivi et l’audit des configurations système.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Faut-il viser un score de 100 % dans chaque domaine ?
Pas nécessairement. Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Toutefois, chaque infrastructure de serveur étant différente, certaines recommandations peuvent être plus ou moins adaptées à votre système. Par exemple, il se peut que certaines recommandations de sécurité soient moins appropriées si vos ordinateurs virtuels ne sont pas connectés à Internet. Certaines recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent des rapports et des données ad hoc de faible priorité. Les problèmes importants pour une entreprise bien établie peuvent l'être moins pour une start-up. Nous vous conseillons donc d'identifier tout d'abord vos domaines prioritaires, puis d'observer l'évolution de vos résultats au fil du temps.

Chaque recommandation inclut une justification de son importance. Servez-vous de cette explication pour évaluer si la mise en œuvre de la recommandation est importante pour vous, en fonction de la nature de vos services informatiques et des besoins de votre organisation.

## <a name="use-health-check-focus-area-recommendations"></a>Utilisation des recommandations des domaines Health Check
Avant de pouvoir utiliser une solution d’évaluation dans Log Analytics, vous devez avoir installé cette solution.  Une fois le pack installé, vous pouvez afficher un résumé des recommandations à l’aide de la vignette SQL Health Check de la page de solution dans le portail Azure.

Consultez le résumé des évaluations de conformité pour votre infrastructure, puis explorez les recommandations.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Pour afficher les recommandations relatives à un domaine et prendre des mesures correctives
1. Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).
2. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
3. Dans le volet des abonnements Log Analytics, sélectionnez un espace de travail, puis cliquez sur la vignette **Vue d’ensemble**.  
4. Dans la page **Vue d’ensemble**, cliquez sur la vignette **SQL Health Check**.
5. Dans la page **Health Check**, passez en revue les informations récapitulatives dans l’un des panneaux du domaine concerné, puis cliquez sur l’un d’entre eux pour afficher les recommandations correspondantes.
6. Les pages relatives au domaine répertorient les recommandations prioritaires pour votre environnement. Cliquez sur une recommandation sous **Objets affectés** pour en afficher les détails et comprendre pourquoi elle apparaît.<br><br> ![image des recommandations SQL Health Check](./media/log-analytics-sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. Vous pouvez effectuer les actions correctives suggérées dans **Actions suggérées**. Une fois l'élément traité, les évaluations ultérieures indiqueront que des mesures ont été prises et votre score de conformité augmentera. Les éléments corrigés apparaissent comme **objets passés**.

## <a name="ignore-recommendations"></a>Ignorer les recommandations
Si vous souhaitez ignorer des recommandations, vous pouvez créer un fichier texte que Log Analytics utilisera pour empêcher les recommandations d’apparaître dans les résultats de votre évaluation.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Pour identifier les recommandations que vous ignorerez
1. Dans le portail Azure, dans la page des espaces de travail Log Analytics, cliquez sur la vignette **Rechercher dans les journaux** pour votre espace de travail sélectionné.
2. Utilisez la requête suivante pour répertorier les recommandations qui ont échoué pour les ordinateurs de votre environnement.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), remplacez la requête ci-dessus par la requête ci-dessous.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Voici une capture d’écran montrant la requête Recherche de journal : <br><br> ![recommandations ayant échoué](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Choisissez les recommandations que vous souhaitez ignorer. Vous utiliserez les valeurs RecommendationId dans la procédure suivante.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Pour créer et utiliser un fichier texte IgnoreRecommendations.txt
1. Créez un fichier nommé IgnoreRecommendations.txt.
2. Collez ou saisissez chaque valeur RecommendationId de chaque recommandation que Log Analytics doit ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3. Placez le fichier dans le dossier suivant sur chaque ordinateur sur lequel Log Analytics doit ignorer les recommandations.
   * Sur les ordinateurs avec Microsoft Monitoring Agent (connectés directement ou avec Operations Manager) : *lecteur_système*:\Program Files\Microsoft Monitoring Agent\Agent
   * Sur le serveur d’administration Operations Manager : *lecteur_système*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Sur le serveur d’administration Operations Manager 2016 : *lecteur_système*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Pour vérifier que les recommandations sont ignorées
1. Une fois les prochaines évaluations planifiées exécutées, par défaut tous les 7 jours, les recommandations spécifiées sont marquées Ignored (ignorées) et n'apparaissent pas dans le tableau de bord d'évaluation.
2. Vous pouvez utiliser les requêtes Recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), remplacez la requête ci-dessus par la requête ci-dessous.
    >
    > `SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Si vous décidez ultérieurement d’afficher les recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou supprimez les valeurs RecommendationID de ces fichiers.

## <a name="sql-health-check-solution-faq"></a>Questions fréquentes (FAQ) sur la solution SQL Health Check
*Quelle est la fréquence d’exécution d’un contrôle d’intégrité ?*

* Le contrôle s’exécute tous les sept jours.

*Est-il possible de configurer la fréquence d’exécution du contrôle ?*

* Pas pour l'instant.

*Si un autre serveur est découvert après l’ajout de la solution SQL Health Check, ce serveur sera-t-il contrôlé ?*

* Oui, une fois découverts, les nouveaux serveurs sont contrôlés tous les sept jours.

*Si un serveur est désactivé, est-il retiré du processus de contrôle d’intégrité ?*

* Si un serveur n'envoie pas de données pendant 3 semaines, il est supprimé.

*Quel est le nom du processus qui effectue la collecte de données ?*

* AdvisorAssessment.exe

*Combien de temps la collecte de données prend-elle ?*

* La collecte de données sur le serveur prend environ 1 heure. Cela peut prendre plus de temps sur les serveurs dotés d'un grand nombre d'instances SQL ou de bases de données.

*Quels types de données sont collectés ?*

* Les types de données suivants sont collectés :
  * WMI
  * Registre
  * Compteurs de performances
  * Vues de gestion dynamique (DMV) SQL

*Est-il possible de configurer les périodes de collecte de données ?*

* Pas pour l'instant.

*Pourquoi faut-il configurer un compte d'identification ?*

* Pour SQL Server, un petit nombre de requêtes SQL est exécuté. Pour permettre cette exécution, vous devez utiliser un compte d'identification avec les autorisations AFFICHER L'ÉTAT DU SERVEUR.  En outre, pour les requêtes WMI, des informations d'identification d'administrateur local sont requises.

*Pourquoi afficher uniquement les 10 premières recommandations ?*

* Au lieu d'exploiter une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez afficher la liste détaillée des recommandations, utilisez la recherche dans les journaux de Log Analytics.

*Est-il possible d'ignorer une recommandation ?*

* Oui, consultez la section [Ignorer les recommandations](#ignore-recommendations) ci-dessus.

## <a name="next-steps"></a>Étapes suivantes
* [Rechercher dans les journaux](log-analytics-log-searches.md) pour savoir comment analyser les données et recommandations détaillées de SQL Health Check.
