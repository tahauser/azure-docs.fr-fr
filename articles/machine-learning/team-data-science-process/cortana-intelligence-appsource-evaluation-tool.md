---
title: Outil d’évaluation de la solution Cortana Intelligence | Microsoft Docs
description: Vous êtes partenaire Microsoft. À ce titre, nous vous fournissons ici toutes les étapes à suivre pour publier votre solution Cortana Intelligence sur AppSource.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams
ms.openlocfilehash: f678cb9819a2c03f97bcca5a2092160212779328
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Outil d’évaluation de la solution Cortana Intelligence
## <a name="overview"></a>Vue d'ensemble
Vous pouvez utiliser l’outil d’évaluation de la solution Cortana Intelligence pour évaluer la conformité de vos solutions d’analyse avancées aux meilleures pratiques recommandées par Microsoft. Microsoft est ravi de travailler avec ses partenaires (éditeurs de logiciels indépendants et intégrateurs de systèmes) pour fournir des solutions de haute qualité à ses clients et revendeurs, ainsi qu’à des fins d’implémentation. Ce guide explique comment utiliser l’outil d’évaluation de la solution avec votre solution et décrit les meilleures pratiques à appliquer pour les vérifications.

## <a name="getting-started"></a>Prise en main
Veuillez [télécharger](https://aka.ms/aa-evaluation-tool-download) et installer l’outil d’évaluation de solution Cortana Intelligence.

Configuration requise :
- Windows 10 : [site officiel pour Windows 10](https://www.microsoft.com/en-us/windows)
- Azure PowerShell : [Installation et configuration d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0)

## <a name="identifying-your-app"></a>Identification de votre application
Une fois l’installation terminée, ouvrez l’outil et commencez votre première évaluation.

![Outil d’évaluation ouvert](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

Fournissez les informations d’identification relatives à votre solution.

![Connexion à un abonnement Azure](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Connectez-vous à votre abonnement Azure et indiquez le groupe de ressources qui contient votre application.

![Sélection des ressources](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

Une fois le groupe de ressources chargé, sélectionnez les ressources incluses dans votre solution et identifiez l’accessibilité de toutes les ressources de données en tant que :
- Ingestion
- Consommation
- Interne

Ces informations nous permettent de mieux comprendre comment votre solution utilise les différents composants et de nous assurer que les composants utilisateur sont conformes aux meilleures pratiques.

### <a name="ingestion"></a>Ingestion
Dans ce cas, l’ingestion désigne toutes les sources de données utilisées pour intégrer des données extérieures à la solution ou utilisées par n’importe quel service extérieur à la solution pour lui transmettre des données.

### <a name="consumption"></a>Consommation
Dans ce cas, la consommation fait référence à tous les jeux de données utilisés pour transmettre des données aux utilisateurs finaux, de façon directe ou indirecte. Par exemple : 
- Jeux de données utilisés dans une requête directe à partir de PowerBI
- Jeux de données interrogés dans une application web

>[!NOTE]
Si une ressource spécifique est utilisée pour l’ingestion et la consommation, choisissez **Consommation**.

### <a name="internal"></a>Interne
Choisissez cette option pour toutes les ressources de données utilisées uniquement pour le traitement d’applications internes.

Vous serez ensuite invité à fournir des informations d’identification valides pour toutes les bases de données spécifiées à l’étape précédente :

![Configuration préalable aux tests](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>Cas de test de la solution
L’outil effectue une série de tests automatisés sur votre solution.

![Configuration de l’exécution des tests](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

Une fois les tests terminés, vous êtes invité à expliquer ou justifier pourquoi votre solution n’est pas conforme à l’exigence.

![Indication d’une justification professionnelle](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

Par exemple, si votre solution publie dans Azure SQL DW, les tests d’évaluation impliquent également une publication dans Azure Analysis Services de votre part. 

Votre solution peut utiliser des machines virtuelles IaaS exécutant SQL Server Analysis Services au lieu d’Azure Analysis Services. Il s’agirait d’une raison valable d’échec du test.
## <a name="packaging-your-evaluation-results"></a>Empaquetage de vos résultats d’évaluation
Une fois les cas de test effectués, votre package d’évaluation est exporté vers un fichier zip. Vous êtes alors invité à fournir des commentaires sur l’outil d’évaluation. 

Vous devez d’abord partager ce fichier zip des résultats de test avec Microsoft pour que votre solution soit évaluée puis obtienne une approbation d’ajout à AppSource.

![Notation de l’outil d’évaluation](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

La section ci-dessus de cet article traite diverses fonctionnalités de l’outil. Intéressons-nous maintenant aux types de meilleures pratiques qu’il évalue.

## <a name="security-evaluation-considerations"></a>Considérations sur l’évaluation de la sécurité
### <a name="databases-should-use-azure-active-directory-authentication"></a>Les bases de données doivent utiliser l’authentification Azure Active Directory
Toutes les ressources Azure SQL ou Azure SQL DW de la solution doivent être activées avec l’authentification Azure Active Directory (AAD). AAD vous permet de gérer l’ensemble de vos identités et rôles à partir d’un emplacement unique.

| Pour plus d’informations sur l’un des sujets suivants : | Lisez l’article : |
| --- | --- |
| AAD avec SQL Database et SQL Data Warehouse | [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database ou de SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) |
| Configuration et gestion d’AAD | [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) |
| Authentification des applications web Azure | [Authentification et autorisation dans Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) |
| Configuration des applications web avec AAD | [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](https://docs.microsoft.com/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>Les jeux de données accessibles aux utilisateurs finaux doivent prendre en charge le contrôle d’accès en fonction du rôle
Lorsque vous exécutez l’outil d’évaluation, vous êtes invité à spécifier toutes les ressources de création de rapports ou de publication. Il est supposé que ces ressources sont destinées à un accès par les utilisateurs finaux et non par les développeurs. Un contrôle d’accès en fonction du rôle (RBAC) doit être assigné à ces ressources pour garantir que les utilisateurs finaux peuvent accéder uniquement aux données autorisées.

Plus précisément, toutes les ressources Azure suivantes peuvent être configurées avec RBAC et sont considérées comme acceptables :
- Sécurisation HDInsight : voir [Introduction aux fonctions de sécurité Hadoop sur la base de clusters HDInsight joints à un domaine](https://docs.microsoft.com/azure/hdinsight/hdinsight-domain-joined-introduction)
- Azure SQL : voir [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database ou de SQL Data Warehouse]( https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
- Azure Analysis Services : voir [Manage database roles and users for Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users) (Gérer les utilisateurs et rôles de base de données pour Azure Analysis Services)
- Azure SQL Data Warehouse (sachez que, dans la mesure où SQL DW ne prend pas en charge RBAC, cette solution n’est pas recommandée pour un accès direct de l’utilisateur final).

Si vous utilisez un autre type de ressource qui prend en charge RBAC, vous devez le spécifier dans la justification du cas de test.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store doit utiliser le chiffrement au repos
Azure Data Lake Store (ADLS) prend en charge le chiffrement au repos par défaut avec des clés de chiffrement gérées par ADLS. Vous pouvez également configurer le chiffrement à l’aide de Key Vault.

Pour plus d’informations sur la spécification des paramètres de chiffrement ADLS, voir [Créer un compte Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account).

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Azure SQL et Azure SQL Data Warehouse doivent utiliser le chiffrement
Azure SQL et Azure SQL DW prennent en charge le chiffrement TDE (Transparent Data Encryption), qui assure le chiffrement et le déchiffrement en temps réel des fichiers journaux et de données.

| Pour plus d’informations sur l’un des sujets suivants : | Lisez l’article : |
| --- | --- |
| Chiffrement transparent des données (TDE) | [Chiffrement transparent des données](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL Data Warehouse avec TDE | [Prise en main du chiffrement transparent des données (TDE)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| Configuration d’Azure SQL avec TDE | [Transparent Data Encryption avec Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Configuration d’Azure SQL avec Always Encrypted | [Key Vault : SQL Database avec Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

Outre le chiffrement TDE, Azure SQL prend en charge la nouvelle technologie de chiffrement de données Always Encrypted, qui garantit que les données sont chiffrées non seulement au repos et pendant le déplacement entre client et serveur, mais aussi quand elles sont en cours d’utilisation, lors de l’exécution de commandes sur le serveur.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>Toutes les machines virtuelles doivent être déployées à partir de la Place de marché Microsoft Azure
Pour que nous puissions assurer un niveau de sécurité cohérent à l’échelle d’AppSource, toutes les machines virtuelles déployées dans le cadre d’une solution Cortana Intelligence doivent être certifiées et publiées sur la Place de marché.

Pour rechercher la liste actuelle des images Place de marché Microsoft Azure, consultez [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute).

Pour plus d’informations sur la publication d’une image de machine virtuelle pour la Place de marché Microsoft Azure, consultez [Guide pour la création d’une image de machine virtuelle pour la Place de marché Microsoft Azure](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).

## <a name="scalability-evaluation-considerations"></a>Considérations sur l’évaluation de l’extensibilité
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Les solutions Cortana Intelligence doivent inclure une plateforme Big Data évolutive.
Les solutions Cortana Intelligence doivent assurer une mise à l’échelle pour de très grandes tailles de données. Dans Azure, cela signifie qu’elles doivent inclure l’une des deux plateformes de données dont l’échelle se mesure en pétaoctets :
- Azure Data Lake Store
- Azure SQL Data Warehouse

Si votre solution ne requiert pas la prise en charge de ces tailles de données ou si vous utilisez une plateforme de données alternative, veuillez le préciser dans la justification du cas de test.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Les solutions Cortana Intelligence doivent inclure des environnements de données d’ingestion dédiés.
D’une manière générale, il est préférable que les solutions Cortana Intelligence n’insèrent pas de données directement dans des sources de données relationnelles. Au lieu de cela, les données brutes doivent être stockées dans un environnement non structuré, avec des insertions/mises à jour idempotentes dans tous les magasins relationnels avec Azure Data Factory.

Pour plus d’informations sur la copie des données avec Azure Data Factory, voir [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de Visual Studio](https://docs.microsoft.com/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio).

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Microsoft Azure SQL Data Warehouse doit utiliser PolyBase pour l’ingestion de données
Azure SQL DW prend en charge PolyBase, pour une ingestion de données parallèle et hautement évolutive. PolyBase vous permet d’utiliser Azure SQL DW pour émettre des requêtes concernant des jeux de données externes stockés dans le Stockage Blob Azure ou dans Azure Data Lake Store. Cette approche offre de meilleures performances que d’autres méthodes de mise à jour en bloc.

Pour obtenir des instructions sur la prise en main de PolyBase et Azure SQL DW, consultez [Charger des données avec PolyBase dans SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase).

Pour plus d’informations sur les meilleures pratiques avec PolyBase et Azure SQL DW, consultez [Guide d’utilisation de PolyBase dans SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide).

## <a name="availability-evaluation-considerations"></a>Considérations sur l’évaluation de la disponibilité

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>Les jeux de données accessibles aux utilisateurs finaux doivent prendre en charge un nombre important d’utilisateurs simultanés
Lorsque vous exécutez l’outil d’évaluation, vous êtes invité à spécifier toutes les ressources de création de rapports ou de publication. Il est supposé que ces ressources sont destinées à un accès par les utilisateurs finaux et non par les développeurs. Ces ressources doivent prendre en charge un nombre d’utilisateurs simultanés moyen à élevé.

Plus précisément, Azure SQL Data Warehouse ne doit pas être la seule source de données disponible aux utilisateurs finaux. Si Azure SQL DW est fourni en tant que ressource pour les utilisateurs avancés, Azure Analysis Services doit être mis à la disposition des utilisateurs standard.

Pour plus d’informations sur les limites d’accès simultané d’Azure SQL DW, consultez [Gestion de la concurrence et des charges de travail dans SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency).

Pour plus d’informations sur Azure Analysis Services, consultez [Qu’est-ce qu’Azure Analysis Services ?](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview).

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Les ressources Azure SQL doivent disposer d’un réplica en lecture seule pour le basculement
Les bases de données Azure SQL prennent en charge la géoréplication sur une instance secondaire. Cette instance peut ensuite être utilisée comme une instance de basculement pour fournir des applications à haute disponibilité.

Pour plus d’informations sur la géoréplication pour les bases de données Azure SQL, consultez [Vue d’ensemble : groupes de basculement et géo-réplication active](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).

Pour obtenir des instructions sur la façon de configurer la géoréplication pour Azure SQL, consultez [Configurer la géoréplication active pour Azure SQL Database avec Transact-SQL](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-transact-sql).

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Les sauvegardes géoredondantes doivent être activées sur Azure SQL Data Warehouse
Azure SQL DW prend en charge les sauvegardes quotidiennes sur stockage géoredondant. Cette géoréplication vous garantit de pouvoir restaurer l’entrepôt de données, même lorsque vous ne pouvez pas accéder aux captures instantanées stockées dans votre région primaire. Cette fonctionnalité est activée par défaut et ne doit pas être désactivée pour les solutions Cortana Intelligence.

Pour plus d’informations sur les sauvegardes Azure SQL DW et la restauration, consultez [Sauvegardes SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-backups).

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>Les machines virtuelles doivent être configurées avec des groupes à haute disponibilité
Les machines virtuelles Azure doivent être configurées dans des groupes à haute disponibilité afin de réduire l’impact des événements de maintenance planifiés et non planifiés.

Pour plus d’informations sur la disponibilité des machines virtuelles Azure, consultez [Gestion de la disponibilité des machines virtuelles Windows dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).

## <a name="other-evaluation-considerations"></a>Autres considérations sur l’évaluation
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Les applications Cortana Intelligence doivent utiliser un outil centralisé pour l’orchestration des données
L’utilisation d’un outil unique pour la gestion et la planification de la transformation et du déplacement des données garantit la cohérence des données critiques. Cette méthode fournit une logique claire vis-à-vis de la logique de nouvelle tentative, de la gestion des dépendances, des alertes/de la journalisation, etc. Nous recommandons l’utilisation [d’Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-introduction) pour l’orchestration des données dans Azure.

Si vous utilisez un ou plusieurs outils autres qu’Azure Data Factory pour l’orchestration des données, décrivez-les.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Les modèles Microsoft Azure Machine Learning doivent être reformés avec Azure Data Factory
Microsoft Azure Machine Learning (AzureML) fournit des outils simples d’utilisation pour la création et le déploiement de pipelines d’apprentissage automatique et de modélisation prédictive. Toutefois, il est important que les déploiements de production de ces modèles AzureML ne soient pas basés sur un seul jeu de données fixe, mais s’adaptent à la dynamique de déplacement des phénomènes du monde réel.

Pour plus d’informations sur la création de services web de reformation, voir [Reformation des modèles Machine Learning par programme](https://docs.microsoft.com/azure/machine-learning/machine-learning-retrain-models-programmatically).

Pour plus d’informations sur l’automatisation du processus de formation de modèle à l’aide d’Azure Data Factory, consultez [Mettre à jour les modèles Azure Machine Learning à l’aide de l’activité des ressources de mise à jour](https://docs.microsoft.com/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity).

## <a name="existing-documentation"></a>Documentation existante
[Microsoft Azure Certified permet de développer votre activité dans le cloud](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Microsoft Azure Certified pour Cortana Intelligence](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)

