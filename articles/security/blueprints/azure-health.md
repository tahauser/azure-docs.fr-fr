---
title: Plan d’analytique Azure Health
description: Instructions pour le déploiement d’un plan d’analytique de santé HIPAA/HITRUST
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 26566e0a-0a54-49f4-a91d-48e20b7cef71
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: simorjay
ms.openlocfilehash: 700378d23f869427fb50b9dee5bcf8448ac73404
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-security-and-compliance-blueprint---hipaahitrust-health-data-and-ai"></a>Plan de sécurité et de conformité Azure - AI et données de santé HIPAA/HITRUST

## <a name="overview"></a>Vue d'ensemble

**Le Plan de sécurité et de conformité Azure - AI et données de santé HIPAA/HITRUST propose un déploiement clé en main d’une solution PaaS Azure afin d’illustrer comment ingérer, stocker, analyser et interagir de manière sécurisée avec des données de santé tout en respectant les exigences de l’industrie en matière de conformité. Le plan aide à accélérer l’adoption et l’utilisation du cloud pour les clients avec des données réglementées.**

Le Plan de sécurité et de conformité Azure - AI et données de santé HIPAA/HITRUST fournit des outils et des conseils pour aider à déployer un environnement PaaS (Platform-as-a-Service) sécurisé et compatible avec HIPAA (Health Insurance Portability and Accountability Act) et HITRUST (Health Information Trust Alliance) pour l’ingestion, le stockage, l’analyse et l’interaction avec des dossiers médicaux personnels et non-personnels dans un environnement cloud sécurisé à plusieurs niveaux, déployé en tant que solution de bout en bout. Il présente une architecture de référence commune et est conçu pour simplifier l’adoption de Microsoft Azure. Cette architecture fournie est un exemple de solution qui répond aux besoins des entreprises souhaitant une approche basée sur le cloud visant à réduire la charge et les coûts liés aux déploiements.

![](images/components.png)

La solution est conçue afin de consommer un exemple de jeu de données mis en forme à l’aide de FHIR (Fast Healthcare Interoperability Resources), une norme internationale d’échange d’informations de soins de santé par voie électronique, et de le stocker de manière sécurisée. Les clients peuvent ensuite utiliser Azure Machine Learning pour tirer parti de puissants outils de décisionnel et d’analytique afin de passer en revue les prédictions effectuées sur les exemples de données. En guise d’exemple du genre d’expérience pouvant être facilitée par Azure Machine Learning, le plan inclut un exemple de jeu de données, des scripts et des outils pour la prédiction de la durée du séjour d’un patient dans un hôpital. 

Ce plan est destiné à servir de base modulaire. Il pourra être adapté par les clients en fonction de leurs besoins spécifiques, notamment en vue du développement de nouvelles expériences Azure Machine Learning pour résoudre des scénarios de cas d’usage à la fois clinique et opérationnel. Il est conçu pour être sécurisé et conforme lors du déploiement. Toutefois, il incombe aux clients de configurer correctement les rôles et d’implémenter les éventuelles modifications. Notez les points suivants :

-   Ce plan constitue une base de référence ayant pour but d’aider les clients à utiliser Microsoft Azure dans un environnement HITRUST et HIPAA.

-   Bien qu’il ait été conçu pour être aligné avec HIPAA et HITRUST (par le biais de l’infrastructure CSF [Common Security Framework]), il ne doit pas être considéré comme conforme avant d’avoir été certifié par un auditeur externe conformément aux exigences HIPAA et HITRUST.

-   Il incombe aux clients d’effectuer des contrôles de conformité et de sécurité appropriés de toute solution générée à l’aide de cette architecture de base.

## <a name="deploying-the-automation"></a>Déploiement de l’automatisation

- Pour déployer la solution, suivez les instructions fournies dans le guide de déploiement. 

[![](./images/deploy.png)](https://aka.ms/healthblueprintdeploy)

Pour obtenir une présentation rapide du fonctionnement de cette solution, regardez cette [vidéo](https://aka.ms/healthblueprintvideo) qui explique son déploiement.

- Vous trouverez un Forum aux questions [ici](https://aka.ms/healthblueprintfaq).

-   **Diagramme architectural.** Le diagramme illustre l’architecture de référence utilisée pour le plan et l’exemple de scénario de cas d’usage.

-   **Modèles de déploiement**. Dans ce déploiement, des [modèles Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) sont utilisés pour déployer automatiquement les composants de l’architecture dans Microsoft Azure, en spécifiant des paramètres de configuration pendant l’installation.

-   **[Scripts de déploiement automatisé](https://aka.ms/healthblueprintdeploy)**. Ces scripts aident à déployer la solution. Les scripts sont constitués des éléments suivants :


-   Un script de configuration de l’installation des modules et de configuration de [l’administrateur général](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) est utilisé pour installer et vérifier que les modules PowerShell et rôles d’administrateur général nécessaires sont configurés correctement. 
-   Un script d’installation PowerShell est utilisé pour déployer la solution. Il est fourni par le biais d’un fichier .zip qui contient des fonctions de démonstration prédéfinies.

## <a name="solution-components"></a>Composants de la solution


L’architecture de base est constituée des éléments suivants :

-   **[Modèle de menaces](https://aka.ms/healththreatmodel)** Un modèle de menaces complet est fourni au format tm7 pour une utilisation avec [Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168). Il présente les composants de la solution, les flux de données entre eux, ainsi que les délimitations d’approbation. Le modèle peut aider les clients à comprendre les points de risque potentiel dans l’infrastructure du système lors du développement de composants d’apprentissage automatique ou autres modifications.

-   **[Matrice d’implémentation client](https://aka.ms/healthcrmblueprint)** Un classeur Microsoft Excel répertorie les exigences HITRUST pertinentes, et explique de quelle manière Microsoft et le client doivent satisfaire chacune d’entre elles.

-   **[Contrôle d’intégrité.](https://aka.ms/healthreviewpaper)** La solution a été contrôlée par Coalfire systems, Inc. Le document relatif au contrôle et aux instructions Health Compliance (HIPAA, and HITRUST) fournit l’évaluation de la solution réalisée par l’auditeur\', ainsi que des suggestions pour transformer le plan en déploiement prêt pour la production.

# <a name="architectural-diagram"></a>Diagramme architectural


![](images/refarch.png)

## <a name="roles"></a>contrôleur


Le plan définit deux rôles pour les utilisateurs administratifs (opérateurs) et trois rôles pour les utilisateurs membres de la direction et chargés du soin des patients. Un sixième rôle est défini afin de permettre à un auditeur d’évaluer la conformité avec les réglementations HIPAA ou autres. Le contrôle d’accès en fonction du rôle (RBAC) Azure autorise une gestion de l’accès très précise pour chaque utilisateur de la solution par le biais de rôles intégrés et personnalisés. Pour plus d’informations sur RBAC, les rôles et les autorisations, consultez [Bien démarrer avec le contrôle d’accès en fonction du rôle dans le portail Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) et [Rôles intégrés pour le contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

### <a name="site-administrator"></a>Administrateur de site


L’administrateur du site est responsable de l’abonnement Azure du client. Il contrôle le déploiement global, mais n’a pas accès aux dossiers des patients.

-   Attributions de rôles par défaut : [Propriétaire](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#owner)

-   Attributions de rôles personnalisées : N/A

-   Étendue : Abonnement

### <a name="database-analyst"></a>Analyste de base de données

L’analyste de base de données gère la base de données et l’instance de SQL Server.
Il n’a pas accès aux dossiers des patients.

-   Attributions de rôles intégrées : [Contributeur de SQL DB](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#sql-db-contributor), [Contributeur de SQL Server](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#sql-server-contributor)

-   Attributions de rôles personnalisées : N/A

-   Étendue : Groupe de ressources

 ### <a name="data-scientist"></a>Scientifique des données


Le scientifique des données assure le fonctionnement du service Azure Machine Learning. Il peut importer, exporter et gérer les données, et exécuter des rapports. Il a accès aux données des patients, mais ne dispose pas des privilèges d’administrateur.

-   Attributions de rôles intégrées : [Contributeur de comptes de stockage](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#storage-account-contributor)

-   Attributions de rôles personnalisées : N/A

-   Étendue : Groupe de ressources

### <a name="chief-medical-information-officer-cmio"></a>Agent principal des renseignements médicaux


L’agent principal des renseignements médicaux rapproche les services informatiques/technologiques et les professionnels de santé d’un établissement de santé. Son travail implique généralement l’utilisation d’analytiques pour déterminer si les ressources sont allouées de façon appropriée au sein d’une organisation.

-   Attributions de rôles intégrées : Aucune

### <a name="care-line-manager"></a>Responsable hiérarchique des soins


Le responsable hiérarchique des soins est directement impliqué dans les soins fournis aux patients.
Ce rôle nécessite de surveiller l’état de santé de chaque patient, et de s’assurer que le personnel est disponible pour répondre aux besoins spécifiques des patients. Le responsable hiérarchique des soins est responsable de l’ajout et de la mise à jour des dossiers des patients.

-   Attributions de rôles intégrées : Aucune

-   Attributions de rôles personnalisées : dispose des privilèges nécessaires pour exécuter HealthcareDemo.ps1 afin de procéder aux admissions et aux sorties des patients.

-   Étendue : Groupe de ressources

### <a name="auditor"></a>Auditeur


L’auditeur évalue la conformité de la solution. Ils n’ont aucun accès direct au réseau.

-   Attributions de rôles intégrées : [Lecteur](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#reader)

-   Attributions de rôles personnalisées : N/A

-   Étendue : Abonnement

## <a name="example-use-case"></a>Exemple de cas d’usage


L’exemple de cas d’usage inclus avec ce plan illustre comment vous pouvez l’utiliser pour activer l’apprentissage automatique et l’analytique sur des données de santé dans le cloud. Contosoclinic est un petit hôpital situé aux États-Unis. Les administrateurs du réseau de l’hôpital souhaitent utiliser Azure Machine Learning pour mieux prédire la durée de séjour d’un patient au moment de l’admission, afin d’accroître l’efficacité de la charge de travail opérationnelle et d’améliorer la qualité des soins fournis.

### <a name="predicting-length-of-stay"></a>Prédiction de durée du séjour


L’exemple de scénario d’usage utilise Azure Machine Learning pour prédire la durée du séjour d’un nouveau patient en comparant les détails médicaux pris au moment de son admission aux données historiques agrégées à partir de patients précédents.
Le plan comprend un jeu étendu de dossiers médicaux anonymes afin d’illustrer les fonctionnalités de formation et de prédiction de la solution. Dans un déploiement de production, les clients utiliseraient leurs propres dossiers pour la formation de la solution, afin de refléter de manière plus précise les caractéristiques uniques de leur environnement, équipements et patients.

### <a name="users-and-roles"></a>Utilisateurs et rôles


**Administrateur de site -- Alex**

*Adresse e-mail : Alex\_AdminSite*

Le travail d’Alex consiste à évaluer les technologies susceptibles de réduire la charge liée à la gestion d’un réseau local et de réduire les coûts de gestion. Alex évalue Azure depuis quelque temps, mais il a des difficultés à configurer les services dont il a besoin pour répondre aux exigences de conformité HiTrust relatives au stockage des données des patients dans le cloud. Alex a sélectionné l’AI Azure Health pour déployer une solution de santé conforme et répondant aux exigences des clients pour HiTrust.

**Scientifique des données -- Marie**

*Adresse e-mail : Marie\_ScientifiqueDonnées*

Marie est responsable de l’utilisation et de la création de modèles qui analysent les dossiers médicaux afin d’obtenir des informations détaillées sur les soins médicaux. Elle utilise SQL et le langage de programmation statistique R pour créer ses modèles.

**Analyste de base de données -- Delphine**

*Adresse e-mail : Delphine\_AnalysteBD*

Delphine est le contact principal pour tout ce qui concerne le serveur Microsoft SQL Server qui stocke toutes les données relatives aux patients pour Contosoclinic. Delphine est une administratrice SQL Server expérimentée qui s’est récemment familiarisée avec Azure SQL Database.

**Agent principal des renseignements médicaux -- Caroline**

Caroline collabore avec Chris le responsable hiérarchique des soins et Marie la scientifique des données afin d’identifier les facteurs qui ont un impact sur la durée de séjour des patients.
Caroline utilise les prédictions fournies par la solution de durée de séjour (DDS) pour déterminer si les ressources sont allouées de manière adéquate dans le réseau de l’hôpital. Elle utilise par exemple le tableau de bord fournie dans cette solution.

**Responsable hiérarchique des soins -- Chris**

*Adresse e-mail : Chris\_ResponsableHiérarchiqueSoins*

En tant que personne responsable de la gestion de l’admission et de la sortie des patients à Contosoclinic, Chris utilise les prédictions générées par la solution DDS pour s’assurer que le personnel adéquat est disponible pour fournir des soins aux patients pendant qu’ils sont à l’hôpital.

**Auditeur -- Henri**

*Adresse e-mail : Henri\_Auditeur*

Henri est un auditeur certifié ayant une expérience de l’audit pour ISO, SOC et HiTrust. Il a été embauché pour contrôler le réseau de Contosoclinc. Il peut vérifier la matrice de responsabilités client fournie avec la solution, afin de s’assurer que le plan et la solution DDS peuvent être utilisés pour stocker, traiter et afficher des données personnelles sensibles.


# <a name="design-configuration"></a>Configuration de la conception


Cette section détaille les mesures de sécurité et les configurations par défaut intégrées au plan pour les éléments suivants :

- **INGESTION** des sources de données brutes, notamment la source de données FHIR
- **STOCKAGE** des informations personnelles
- **ANALYSE** et prédiction des résultats
- **INTERACTION** avec les résultats et prédictions
- Gestion des **IDENTITÉS** de la solution
- Fonctionnalités de **SÉCURITÉ**


## <a name="identity"></a>IDENTITÉ 

### <a name="azure-active-directory-and-role-based-access-control-rbac"></a>Azure Active Directory et contrôle d’accès en fonction du rôle (RBAC)


**Authentication :**

-   [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) est le service cloud et multilocataire de gestion des annuaires et des identités proposé par Microsoft\'. Tous les utilisateurs de la solution ont été créés dans Azure Active Directory, y compris ceux qui accèdent à la base de données SQL.



-   L’authentification auprès de l’application est effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

-   [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités pouvant affecter les identités de votre organisation, et configure les réponses automatiques aux actions suspectes détectées qui sont liées aux identités de votre organisation. Il examine aussi les incidents suspects et prend les mesures nécessaires pour les éliminer.

-   Le [contrôle d’accès en fonction du rôle (RBAC) Azure](/azure/active-directory/role-based-access-control-configure) permet une gestion précise de l’accès pour Azure. L’accès à l’abonnement est limité à l’administrateur des abonnements, et l’accès à Azure Key Vault est limité à l’administrateur de site. Les mots de passe forts (12 caractères au minimum avec au moins une lettre en minuscule/majuscule, un chiffre et un caractère spécial) sont obligatoires.

-   L’authentification multifacteur est prise en charge quand le commutateur -enableMFA est activé pendant le déploiement.

-   Les mots de passe expirent après 60 jours quand le commutateur -enableADDomainPasswordPolicy est activé pendant le déploiement.

**Rôles :**

-   La solution utilise des [rôles intégrés](/azure/active-directory/role-based-access-built-in-roles) pour gérer l’accès aux ressources.

-   Par défaut, des rôles intégrés spécifiques sont attribués à tous les utilisateurs.

### <a name="azure-key-vault"></a>Azure Key Vault

-   Les données stockées dans Key Vault sont les suivantes :

    -   Clé Application Insights
    -   Clé d’accès au stockage des données sur les patients
    -   Chaîne de connexion des patients
    -   Nom de la table des données sur les patients
    -   Point de terminaison de service web Azure ML
    -   Clé API du service Azure ML

-   Les stratégies d’accès avancées sont configurées en fonction des besoins.
-   Les stratégies d’accès à Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
-   Toutes les clés et tous les secrets dans Key Vault ont des dates d’expiration.
-   Toutes les clés dans Key Vault sont protégées par HSM \[Type de clé = clé RSA 2048 bits protégée par HSM\].
-   Des autorisations minimales requises sont accordées à tous les utilisateurs et à toutes les identités à l’aide du contrôle d’accès en fonction rôle (RBAC).
-   Les applications ne partagent pas un coffre de clés, sauf si elles s’approuvent mutuellement et qu’elles ont besoin d’accéder aux mêmes secrets au moment de l’exécution.
-   Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
-   Les opérations de chiffrement autorisées pour les clés sont limitées à celles qui sont nécessaires.

## <a name="ingest"></a>INGESTION 

### <a name="azure-functions"></a>Azure Functions
La solution a été conçue pour utiliser [Azure Functions](/azure/azure-functions/) pour traiter les exemples de données de longueur de séjour utilisées dans la démonstration d’analytique. Trois capacités dans les fonctions ont été créées.

**1. Importation en bloc de données de renseignements médicaux protégés (PHI)**

Quand vous utilisez le script de démonstration .\\HealthcareDemo.ps1 avec le commutateur **BulkPatientAdmission** comme indiqué dans **Déploiement et exécution de la démo**, il exécute le pipeline de traitement suivant :
1. **Stockage Blob Azure** : l’exemple de fichier .csv de données sur les patients est chargé dans le stockage.
2. **Event Grid** : l’événement publie les données vers la fonction Azure (importation en bloc - événements d’objet blob).
3. **Fonction Azure** : effectue le traitement et stocke les données dans le stockage SQL à l’aide de la fonction sécurisée - événement(type; url blob).
4. **Base de données SQL** : le magasin de base de données pour les données sur les patients utilise des balises pour la classification, et le processus ML est déclenché pour effectuer l’expérience d’apprentissage.

![](images/dataflow.png)

En outre, la fonction Azure a été conçue pour lire et protéger les données sensibles désignées dans l’exemple de jeu de données à l’aide des balises suivantes :
- dataProfile => “ePHI”
- owner => \<Site Admin UPN\>
- environment => “Pilot”
- department => “Global Ecosystem" Le balisage a été appliqué à l’exemple de jeu de données où les noms des patients ont été identifiés comme du texte clair.

**2. Admission des nouveaux patients**

Quand vous utilisez le script de démonstration .\\HealthcareDemo.ps1 avec le commutateur **BulkPatientadmission** comme indiqué dans **Déploiement et exécution de la démo**, il exécute le pipeline de traitement suivant : ![](images/securetransact.png)
**1. Fonction Azure** déclenchée, et la fonction demande un [jeton du porteur](/rest/api/) à Azure Active directory.

**2. Key Vault** est sollicité afin d’obtenir un secret qui est associé au jeton demandé.

**3. Les rôles Azure valident la demande et autorisent la demande d’accès à Key Vault.

**4. Key Vault** retourne le secret, dans le cas présent la chaîne de connexion à la base de données SQL.

**5. La fonction Azure** utilise la chaîne de connexion pour se connecter de façon sécurisée à la base de données SQL, et poursuit le traitement afin de stocker les données ePHI.

Pour effectuer le stockage des données, un schéma d’API commun a été implémenté conformément à la norme Fast Healthcare Interoperability Resources (FHIR). Les éléments d’échange FHIR suivants ont été fournis à la fonction :

-   Le [schéma de patient](https://www.hl7.org/fhir/patient.html) fournit des informations sur un patient.

-   Le [schéma d’observation](https://www.hl7.org/fhir/observation.html) couvre l’élément central des soins de santé. Il sert à prendre en charge le diagnostic, à surveiller la progression, à déterminer les lignes de base et les modèles, et même à capturer des caractéristiques démographiques. 

-   Le [schéma de rencontre](https://www.hl7.org/fhir/encounter.html) couvre les différents types de rencontre, par exemple ambulatoire, urgence, soins à domicile, patient admis en interne et rencontres virtuelles.

-   Le [schéma de la condition](https://www.hl7.org/fhir/condition.html) couvre des informations détaillées sur une condition, un problème, un diagnostic ou autre événement, situation ou concept clinique ayant atteint un niveau nécessitant une attention particulière.  



### <a name="event-grid"></a>Event Grid


Le solution prend en charge Event Grid, un service unique permettant de gérer le routage de tous les événements de n’importe quelle source vers n’importe quelle destination et assurant :

-   [La sécurité et l’authentification.](/azure/event-grid/security-authentication)

-   [Le contrôle d’accès en fonction du rôle](/azure/event-grid/security-authentication#management-access-control) pour diverses opérations de gestion telles que l’énumération des abonnements aux événements, la création de nouveaux abonnements et la génération de clés.

-   Audit

## <a name="store"></a>STOCKAGE 

### <a name="sql-database-and-server"></a>Serveur et base de données SQL 


-   Le [chiffrement transparent des données (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) fournit un chiffrement et un déchiffrement en temps réel des données stockées dans Azure SQL Database, à l’aide d’une clé stockée dans Azure Key Vault.

-   [Évaluation des vulnérabilités SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) est un outil simple à configurer, qui vous permet de découvrir, suivre et corriger des vulnérabilités de base de données potentielles.

-   [Détection de menaces pour les bases de données SQL](/azure/sql-database/sql-database-threat-detection) activée.

-   [Audit de bases de données SQL](/azure/sql-database/sql-database-auditing) activé.

-   [Journalisation des métriques et diagnostics des bases de données SQL](/azure/sql-database/sql-database-metrics-diag-logging) activée.

-   Les [règles de pare-feu au niveau des serveurs et des bases de données](/azure/sql-database/sql-database-firewall-configure) ont été renforcées.

-   Des [colonnes Always Encrypted](/azure/sql-database/sql-database-always-encrypted-azure-key-vault) sont utilisées pour protéger le prénom, le deuxième prénom et le nom de famille du patient.
    En outre, le chiffrement des colonnes de base de données utilise Azure Active Directory (AD) pour authentifier l’application auprès d’Azure SQL Database.

-   L’accès à la base de données SQL et à SQL Server est configuré selon le principe du moindre privilège.

-   Seules les adresses IP requises sont autorisées à traverser le pare-feu SQL.

### <a name="storage-accounts"></a>Comptes de stockage


-   [Les données en mouvement sont transférées uniquement à l’aide de TLS/SSL](/azure/storage/common/storage-require-secure-transfer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json).

-   L’accès anonyme n’est pas autorisé pour les conteneurs.

-   Des règles d’alerte sont configurées pour effectuer le suivi des activités anonymes.

-   Le protocole HTTPS est nécessaire pour accéder aux ressources du compte de stockage.

-   Les données de demande d’authentification sont consignées dans les journaux et surveillées.

-   Les données dans le stockage d’objets blob sont chiffrées au repos.

## <a name="analyze"></a>ANALYSE

### <a name="machine-learning"></a>Machine Learning


-   [La journalisation est activée](/azure/machine-learning/studio/web-services-logging) pour les services web de Machine Learning.
- L’utilisation de [Machine Learning](/azure/machine-learning/preview/experimentation-service-configuration) Workbench nécessite le développement d’expériences, ce qui permet de réaliser des prédictions d’après un jeu de solution. L’[intégration de Workbench](/azure/machine-learning/preview/using-git-ml-project) peut aider à simplifier la gestion des expériences.

## <a name="security"></a>SÉCURITÉ

### <a name="azure-security-center"></a>Azure Security Center
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) offre un aperçu central de l’état de sécurité de toutes vos ressources Azure. Vous pouvez vérifier d’un coup d’œil que les contrôles de sécurité appropriés sont en place et configurés correctement, et vous pouvez identifier rapidement les ressources nécessitant votre attention. 

- [Azure Advisor](/azure/advisor/advisor-overview) est un conseiller personnalisé basé dans le cloud qui décrit les bonnes pratiques à suivre pour optimiser vos déploiements Azure. Il analyse votre télémétrie de configuration et d’utilisation des ressources, puis recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la haute disponibilité et la sécurité de vos ressources Azure.

### <a name="application-insights"></a>Application Insights
- [Application Insights](/azure/application-insights/app-insights-overview) est un service extensible de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes. Utilisez-le pour analyser votre application web en direct. Il détecte les anomalies de performances. Il intègre de puissants outils d’analyse pour vous aider à diagnostiquer les problèmes et à comprendre ce que font les utilisateurs avec votre application. Il a été conçu pour vous permettre d’améliorer continuellement les performances et la convivialité.

### <a name="azure-alerts"></a>Alertes Azure
- [Les alertes offrent un moyen de surveiller les services Azure et vous permettent de configurer des conditions sur les données. Elles fournissent également des notifications quand une condition d’alerte correspond aux données de surveillance.

### <a name="operations-management-suite-oms"></a>Operations Management Suite (OMS)
[Operations Management Suite (également appelé OMS)](/azure/operations-management-suite/operations-management-suite-overview) est une collection de services de gestion.

-   L’espace de travail est activé pour Security Center

-   L’espace de travail est activé pour la surveillance des charges de travail

-   La surveillance des charges de travail est activée pour :

    -   Identité et accès

    -   Sécurité et audit

    -   Analytique de base de données SQL Azure

    -   Solution [Azure WebApp Analytics](/azure/log-analytics/log-analytics-azure-web-apps-analytics)

    -   Key Vault Analytics

    -   Suivi des modifications

-   [Application Insights Connector (préversion)](/azure/log-analytics/log-analytics-app-insights-connector) est activé

-   [Activity Log Analytics](/azure/log-analytics/log-analytics-activity) est activé
