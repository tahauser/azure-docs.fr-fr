---
title: Optimisation de votre environnement System Center Operations Manager avec Azure Log Analytics | Microsoft Docs
description: "La solution System Center Operations Manager Health Check permet d’évaluer les risques et l’intégrité de vos environnements à intervalles réguliers."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86484ca2bc7dc14035f48b8f7b1514a4fc471b74
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>Optimisation de votre environnement avec la solution System Center Operations Manager Health Check (préversion)

![Symbole de System Center Operations Manager Health Check](./media/log-analytics-scom-assessment/scom-assessment-symbol.png)

La solution System Center Operations Manager Health Check permet d’évaluer les risques et l’intégrité de votre groupe de gestion System Center Operations Manager à intervalles réguliers. Cet article vous aide à installer, à configurer et à utiliser la solution pour vous permettre de prendre les mesures correctives en vue de régler des problèmes potentiels.

Cette solution fournit une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont classées en quatre domaines pour vous aider à rapidement mesurer les risques et prendre les mesures correctives appropriées.

Les recommandations proposées sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi un problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Une fois la solution ajoutée et l’évaluation effectuée, le résumé des informations de domaines s’affiche sur le tableau de bord **System Center Operations Manager Health Check** de votre infrastructure. Les sections suivantes expliquent comment utiliser les informations du tableau de bord **System Center Operations Manager Health Check**, dans lequel vous pouvez afficher et exécuter les actions recommandées pour votre environnement Operations Manager.

![Mosaïque de la solution System Center Operations Manager](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Tableau de bord System Center Operations Manager Health Check](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution

La solution fonctionne avec Microsoft System Operations Manager 2012 Service Pack (SP) 1 et 2012 R2.

Utilisez les informations suivantes pour installer et configurer la solution.

 - Avant de pouvoir utiliser une solution Health Check dans Log Analytics, vous devez avoir installé cette solution. Installez la solution depuis [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview).

 - Après avoir ajouté la solution à l’espace de travail, la mosaïque **System Center Operations Manager Health Check** sur le tableau de bord affiche un message de configuration supplémentaire requis. Cliquez sur la mosaïque et suivez les étapes de configuration mentionnées dans la page

 ![Mosaïque du tableau de bord System Center Operations Manager](./media/log-analytics-scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> La configuration de System Center Operations Manager peut être effectuée via un script en suivant les étapes présentées dans la page de configuration de la solution dans Log Analytics.

 Pour configurer l’évaluation via la console Operations Manager, effectuez les étapes ci-dessous dans l’ordre suivant :
1. [Définir le compte d’identification pour System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. [Configurer la règle System Center Operations Manager Health Check](#configure-the-assessment-rule)

## <a name="system-center-operations-manager-assessment-data-collection-details"></a>Détails de l’évaluation de la collecte de données System Center Operations Manager

L’évaluation de System Center Operations Manager collecte des données à partir des sources suivantes :

* Registre
* Windows Management Instrumentation (WMI)
* Journal des événements
* Données de fichier
* Directement à partir d’Operations Manager à l’aide de PowerShell et de requêtes SQL, à partir d’un serveur d’administration que vous avez spécifié.  

Les données sont collectées sur le serveur d’administration et transmises à Log Analytics tous les sept jours.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Comptes d’identification Operations Manager pour Log Analytics

Log Analytics utilise les packs d’administration pour les charges de travail afin de fournir des services à valeur ajoutée. Chaque charge de travail nécessite des privilèges spécifiques de la charge de travail pour exécuter les packs d’administration dans un contexte de sécurité différent, comme un compte d’utilisateur de domaine. Configurez un compte d’identification Operations Manager avec élévation des informations d’identification. Pour plus d’informations, consultez [Guide pratique pour créer un compte d’identification](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) dans la documentation Operations Manager.

Utilisez les informations suivantes pour définir le compte d'identification Operations Manager pour System Center Operations Manager Health Check.

### <a name="set-the-run-as-account"></a>Configuration du compte d’identification

Le compte d’identification doit respecter les conditions suivantes avant de continuer :

* Un compte d’utilisateur de domaine qui est membre du groupe Administrateurs locaux sur tous les serveurs prenant en charge n’importe quel rôle Operations Manager : serveur d’administration, SQL Server qui héberge les opérations, entrepôt de données et base de données ACS, création de rapports, console Web et serveur de passerelle.
* Rôle Administrateur Operations Manager pour le groupe de gestion en cours d’évaluation
* Si le compte ne dispose pas de droits d’administrateur système SQL, exécutez le [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) pour accorder des autorisations granulaires au compte sur chaque instance SQL Server qui héberge une ou toutes les bases de données Operations Manager.

1. Dans la console Operations Manager, sélectionnez le bouton de navigation **Administration**.
2. Sous **Configuration d’identification**, cliquez sur **Comptes**.
3. Dans l’Assistant **Créer un compte d’identification**, sur la page **Introduction**, cliquez sur **suivant**.
4. Sur la page **Propriétés générales**, sélectionnez **Windows** dans la liste **Type de compte d’identification :** .
5. Entrez un nom d’affichage dans la zone de texte **Nom d’affichage**, saisissez éventuellement une description dans la zone **Description**, puis cliquez sur **Suivant**.
6. Sur la page **Sécurité de la distribution**, sélectionnez **Plus sécurisé**.
7. Cliquez sur **Créer**.  

Maintenant que le compte d’identification est créé, il doit cibler les serveurs d’administration dans le groupe d’administration et être associé à un profil d’identification prédéfini afin que les flux de travail s’exécutent à l’aide des informations d’identification.  

1. Sous **Configuration d’identification**, **Comptes**, dans le volet des résultats, double-cliquez sur le compte que vous avez créé précédemment.
2. Dans l’onglet **Distribution**, cliquez sur **Ajouter** pour **Ordinateurs sélectionnés** et ajoutez le serveur d’administration auquel distribuer le compte.  Cliquez deux fois sur **OK** pour enregistrer vos modifications.
3. Sous **Configuration d’identification**, cliquez sur **Profils**.
4. Recherchez *Profil d’évaluation SCOM*.
5. Le nom du profil doit être : *Microsoft System Center Advisor SCOM Assessment Run As Profile*.
6. Avec le bouton droit, mettez à jour ses propriétés et ajoutez le nouveau compte d’identification créé précédemment.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Script SQL octroyant des autorisations granulaires au compte d’identification

Exécutez le script SQL suivant pour accorder les autorisations requises au compte d’identification sur l’instance SQL Server utilisée par Operations Manager qui héberge les opérations, l’entrepôt de données et la base de données ACS.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Configurer la règle de contrôle d’intégrité

Le pack de gestion de la solution System Center Operations Manager Health Check inclut une règle nommée *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule*. Cette règle est responsable de l’exécution du contrôle d’intégrité. Pour activer la règle et configurer la fréquence, utilisez les procédures ci-dessous.

Par défaut, Microsoft System Center Advisor SCOM Assessment Run Assessment Rule est désactivée. Pour exécuter le contrôle d’intégrité, vous devez activer la règle sur un serveur de gestion. Utilisez les étapes suivantes.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>Activation de la règle pour un serveur de gestion spécifique

1. Dans l’espace de travail **Création** de la console Operations Manager, recherchez la règle *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* dans le panneau **Règles**.
2. Dans les résultats de la recherche, sélectionnez le résultat qui inclut le texte *Type : serveur de gestion*.
3. Cliquez sur la règle avec le bouton droit, puis cliquez sur **Substitutions** > **Pour un objet spécifique de la classe : serveur de gestion**.
4.  Dans la liste de serveurs de gestion disponibles, sélectionnez le serveur de gestion où la règle doit être exécutée.  Il doit s’agir du même serveur d’administration que vous avez configuré précédemment pour y associer le compte d’identification.
5.  Veillez à modifier la valeur de substitution sur **True** pour la valeur du paramètre **Activé**.<br><br> ![paramètre de substitution](./media/log-analytics-scom-assessment/rule.png)

    Toujours dans cette fenêtre, configurez la fréquence de l’exécution à l’aide de la procédure suivante.

#### <a name="configure-the-run-frequency"></a>Configuration de la fréquence d’exécution

L’évaluation est configurée pour s’exécuter toutes les 10 080 minutes (ou sept jours) par défaut. Vous pouvez substituer la valeur par la valeur minimale de 1 440 minutes (ou un jour). La valeur représente l’intervalle de temps minimal requis entre les exécutions d’évaluation successives. Pour substituer l’intervalle, utilisez les étapes ci-dessous.

1. Dans l’espace de travail **Création** de la console Operations Manager, recherchez la règle *Microsoft System Center Advisor SCOM Assessment Run Assessment Rule* dans la section **Règles**.
2. Dans les résultats de la recherche, sélectionnez le résultat qui inclut le texte *Type : serveur de gestion*.
3. Cliquez sur la règle avec le bouton droit, puis cliquez sur **Remplacer la règle** > **Pour tous les objets de la classe : serveur de gestion**.
4. Modifiez la valeur du paramètre **Intervalle** sur la valeur de l’intervalle souhaité. Dans l’exemple ci-dessous, la valeur est définie sur 1 440 minutes (1 jour).<br><br> ![paramètre d’intervalle](./media/log-analytics-scom-assessment/interval.png)<br>  

    Si la valeur est inférieure à 1440 minutes, la règle s’exécute selon un intervalle d’une journée. Dans cet exemple, la règle ignore la valeur de l’intervalle et s’exécute à la fréquence d’un jour.


## <a name="understanding-how-recommendations-are-prioritized"></a>Hiérarchisation des recommandations

Une valeur de pondération déterminant l'importance relative de la recommandation est attribuée à chaque recommandation. Seules les 10 recommandations les plus importantes sont affichées.

### <a name="how-weights-are-calculated"></a>Calcul des pondérations

Les pondérations sont des agrégations de valeurs basées sur trois facteurs clés :

- La *probabilité* qu'une anomalie identifiée cause des problèmes. Une plus grande probabilité attribue un score global supérieur à la recommandation.
- L’*impact* de l’anomalie sur votre organisation si elle devait causer des problèmes. Un plus grand impact attribue un score global supérieur à la recommandation.
- L’*effort* requis pour implémenter la recommandation. Un plus grand effort attribue un score global inférieur à la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage du score total disponible pour chaque domaine. Par exemple, si une recommandation dans le domaine Disponibilité et continuité d’activité a un score de 5 %, l’implémentation de cette recommandation augmente votre score Disponibilité et continuité d’activité global de 5 %.

### <a name="focus-areas"></a>Domaines

**Disponibilité et continuité d’activité** : ce domaine présente les recommandations relatives à la disponibilité du service, la résilience de votre infrastructure et la protection de votre activité.

**Performances et évolutivité** : ce domaine présente les recommandations pour aider votre organisation à développer son infrastructure informatique, s’assurer que votre environnement informatique répond aux besoins de performances actuels et est en mesure de répondre à l’évolution des besoins d’infrastructure.

**Mise à niveau, migration et déploiement** : ce domaine présente les recommandations pour vous aider à mettre à niveau, migrer et déployer SQL Server dans votre infrastructure existante.

**Opérations et surveillance** : ce domaine présente des recommandations relatives à la rationalisation de vos opérations informatiques, la mise en place une maintenance préventive et l’optimisation des performances.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Faut-il viser un score de 100 % dans chaque domaine ?

Pas nécessairement. Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Toutefois, chaque infrastructure de serveur étant différente, certaines recommandations peuvent être plus ou moins adaptées à votre système. Par exemple, il se peut que certaines recommandations de sécurité soient moins appropriées si vos ordinateurs virtuels ne sont pas connectés à Internet. Certaines recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent des rapports et des données ad hoc de faible priorité. Les problèmes importants pour une entreprise bien établie peuvent l'être moins pour une start-up. Nous vous conseillons donc d'identifier tout d'abord vos domaines prioritaires, puis d'observer l'évolution de vos résultats au fil du temps.

Chaque recommandation inclut une justification de son importance. Utilisez cette explication pour évaluer si la mise en œuvre de la recommandation est importante pour vous, en fonction de la nature de vos services informatiques et des besoins de votre organisation.

## <a name="use-health-check-focus-area-recommendations"></a>Utilisation des recommandations des domaines Health Check

Avant de pouvoir utiliser une solution de contrôle d’intégrité dans Log Analytics, vous devez avoir installé cette solution. Pour plus d’informations sur l’installation des solutions, consultez la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Une fois le pack installé, vous pouvez afficher un résumé des recommandations à l’aide de la mosaïque System Center Operations Manager Health Check de la page Vue d’ensemble dans le portail OMS.

Consultez le résumé des évaluations de conformité pour votre infrastructure, puis explorez les recommandations.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Pour afficher les recommandations relatives à un domaine et prendre des mesures correctives
1. Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://portal.azure.com).
2. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
3. Dans le volet des abonnements Log Analytics, sélectionnez un espace de travail, puis cliquez sur la vignette **Portail OMS**.  
4. Dans la page **Vue d’ensemble**, cliquez sur la mosaïque **System Center Operations Manager Health Check**.
5. Dans la page **System Center Operations Manager Health Check**, passez en revue les informations de résumé dans l’un des panneaux relatifs à un domaine, puis cliquez sur l’un d’entre eux pour afficher les recommandations afférentes.
6. Les pages relatives au domaine répertorient les recommandations prioritaires pour votre environnement. Cliquez sur une recommandation sous **Objets affectés** pour en afficher les détails et comprendre pourquoi elle apparaît.<br><br> ![domaine](./media/log-analytics-scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. Vous pouvez effectuer les actions correctives suggérées dans **Actions suggérées**. Une fois l'élément traité, les évaluations ultérieures indiqueront que des mesures ont été prises et votre score de conformité augmentera. Les éléments corrigés apparaissent comme **objets passés**.

## <a name="ignore-recommendations"></a>Ignorer les recommandations

Si vous souhaitez ignorer des recommandations, vous pouvez créer un fichier texte que Log Analytics utilise pour empêcher les recommandations d'apparaître dans les résultats de votre évaluation.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Pour identifier les recommandations que vous voulez ignorer
1. Dans le portail Azure, dans la page des espaces de travail Log Analytics, cliquez sur la vignette **Rechercher dans les journaux** pour votre espace de travail sélectionné.
2. Utilisez la requête suivante pour répertorier les recommandations qui ont échoué pour les ordinateurs de votre environnement.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), remplacez la requête ci-dessus par la requête ci-dessous.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Voici une capture d’écran montrant la requête Recherche de journal : <br><br> ![recherche de journal](./media/log-analytics-scom-assessment/scom-log-search.png)<br>

3. Choisissez les recommandations que vous souhaitez ignorer. Vous utiliserez les valeurs RecommendationId dans la procédure suivante.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Pour créer et utiliser un fichier texte IgnoreRecommendations.txt

1. Créez un fichier nommé IgnoreRecommendations.txt.
2. Collez ou saisissez chaque valeur RecommendationId de chaque recommandation que Log Analytics doit ignorer sur une ligne distincte, puis enregistrez et fermez le fichier.
3. Placez le fichier dans le dossier suivant sur chaque ordinateur sur lequel Log Analytics doit ignorer les recommandations.
4. Sur le serveur de gestion Operations Manager : *lecteur_système*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Pour vérifier que les recommandations sont ignorées

1. Une fois la prochaine évaluation planifiée exécutée, par défaut tous les sept jours, les recommandations spécifiées sont marquées comme Ignorées et n’apparaissent pas dans le tableau de bord de contrôle d’intégrité.
2. Vous pouvez utiliser les requêtes Recherche de journal suivantes pour répertorier toutes les recommandations ignorées.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Si vous avez mis à niveau votre espace de travail vers le [nouveau langage de requête dans Log Analytics](log-analytics-log-search-upgrade.md), remplacez la requête ci-dessus par la requête ci-dessous.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Si vous décidez ultérieurement d’afficher les recommandations ignorées, supprimez tous les fichiers IgnoreRecommendations.txt, ou supprimez les valeurs RecommendationID de ces fichiers.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>FAQ sur la solution System Center Operations Manager Health Check

*J’ai ajouté la solution Health Check à mon espace de travail Log Analytics. Mais je ne vois pas les recommandations. Pourquoi ?* Après avoir ajouté la solution, utilisez la procédure suivante pour afficher les recommandations du tableau de bord Log Analytics.  

- [Définir le compte d’identification pour System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [Configurer la règle System Center Operations Manager Health Check](#configure-the-health-check-rule)


*Est-il possible de configurer la fréquence d’exécution du contrôle ?* Oui. Consultez [Configuration de la fréquence d’exécution](#configure-the-run-frequency).

*Si un autre serveur est découvert après l’ajout de la solution System Center Operations Manager Assessment, ce serveur sera-t-il évalué ?* Oui, après la découverte, il sera évalué ; par défaut, tous les sept jours.

*Quel est le nom du processus qui effectue la collecte de données ?* AdvisorAssessment.exe

*Où est exécuté le processus AdvisorAssessment.exe ?* AdvisorAssessment.exe s’exécute sous le processus HealthService du serveur d’administration où le contrôle d’intégrité est activé. À l’aide de ce processus, la découverte de l’ensemble de votre environnement s’effectue par le biais de la collecte de données distante.

*Combien de temps la collecte de données prend-elle ?* La collecte de données sur le serveur prend environ une heure. Elle peut prendre plus de temps dans les environnements comportant plusieurs bases de données ou instances d’Operations Manager.

*Que se passe-t-il si je définis l’intervalle d’évaluation sur une valeur inférieure à 1 440 minutes ?* L’évaluation est préconfigurée pour s’exécuter au maximum une fois par jour. Si vous remplacez la valeur de l’intervalle par une valeur inférieure à 1 440 minutes, l’évaluation utilise alors la valeur de 1 440 minutes en tant que valeur d’intervalle.

*Comment savoir s’il existe des échecs liés aux conditions préalables ?* Si le contrôle d’intégrité s’est exécuté et qu’aucun résultat ne s’affiche, il est probable que certaines des conditions préalables pour le contrôle ont échoué. Vous pouvez exécuter les requêtes : `Operation Solution=SCOMAssessment` et `SCOMAssessmentRecommendation FocusArea=Prerequisites` dans la Recherche de journal pour afficher les conditions préalables qui ont échoué.

*Il y a un message `Failed to connect to the SQL Instance (….).` dans les échecs liés aux conditions préalables. Quel est le problème ?* AdvisorAssessment.exe, le processus qui collecte les données, s’exécute sous le processus HealthService du serveur de gestion. Dans le cadre du contrôle d’intégrité, le processus tente de se connecter au serveur SQL Server où se trouve la base de données Operations Manager. Cette erreur peut se produire lorsque les règles du pare-feu bloquent la connexion à l’instance SQL Server.

*Quels types de données sont collectés ?* Les types de données suivants sont collectés : - Données WMI - Données de registre - Données EventLog - Données Operations Manager via Windows PowerShell, Requêtes SQL et Collecteur d’informations sur le fichier.

*Pourquoi faut-il configurer un compte d'identification ?* Avec Operations Manager, plusieurs requêtes SQL sont exécutées. Pour permettre leur exécution, vous devez utiliser un compte d’identification avec les autorisations nécessaires. En outre, pour les requêtes WMI, des informations d'identification d'administrateur local sont requises.

*Pourquoi afficher uniquement les 10 premières recommandations ?* Au lieu de vous communiquer une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez afficher la liste détaillée de toutes les recommandations, utilisez Recherche de journal.

*Est-il possible d'ignorer une recommandation ?* Oui, consultez [Ignorer les recommandations](#Ignore-recommendations).


## <a name="next-steps"></a>Étapes suivantes

- [Rechercher dans les journaux](log-analytics-log-searches.md) pour savoir comment analyser les données et recommandations détaillées de System Center Operations Manager Health Check.
