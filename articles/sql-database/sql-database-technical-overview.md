---
title: Qu’est-ce que le service Azure SQL Database ? | Microsoft Docs
description: 'Introduction à la base de données SQL : détails techniques et fonctionnalités du système de gestion des bases de données relationnelles Microsoft dans le cloud.'
keywords: introduction à sql, intro à sql, qu’est-ce qu’une base de données sql
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.topic: overview
ms.date: 03/07/2018
ms.author: carlrab
ms.openlocfilehash: 7a06ed8433ebcf728c7b090f5e984d4e3ebeb846
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="what-is-the-azure-sql-database-service"></a>Qu’est-ce que le service Azure SQL Database ? 

SQL Database est un service administré de bases de données relationnelles à usage général de Microsoft Azure qui prend en charge des structures telles que les données relationnelles, JSON, les données spatiales et XML. SQL Database propose des [bases de données managées uniques SQL](sql-database-servers-databases.md), des bases de données managées SQL dans un [pool élastique](sql-database-elastic-pool.md), et des instances managées SQL, appelées [Azure SQL Database Managed Instance](sql-database-managed-instance.md) (en préversion publique). Cette solution offre des [performances évolutives dynamiques](sql-database-service-tiers.md) et fournit des options telles que les [index columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) pour la génération de rapport et l’analyse extrême, et [OLTP en mémoire](sql-database-in-memory.md) pour le traitement transactionnel extrême. Microsoft gère toutes les applications de correctifs et mises à jour de la base de code SQL en toute transparence et élimine toute la gestion de l’infrastructure sous-jacente. 

SQL Database partage sa base de code avec le [moteur de base de données Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). Avec la stratégie orientée cloud de Microsoft, les dernières fonctionnalités de SQL Server sont tout d’abord publiées dans SQL Database, puis dans SQL Server. Cette approche vous procure les fonctionnalités SQL Server les plus récentes sans frais d’application de correctifs ou de mise à niveau, et ces fonctionnalités ont déjà été testées sur des millions de bases de données. Pour plus d’informations sur les nouvelles fonctionnalités annoncées, voir :

- **[Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=databases)** : l’endroit où trouver les nouveautés et les futures fonctionnalités. 
- **[Blog de Microsoft Azure SQL Database](https://azure.microsoft.com/blog/topics/database)** : l’endroit où les membres de l’équipe produit SQL Server publient des billets de blog sur les nouveautés et les fonctionnalités de SQL Database. 

> [!IMPORTANT]
> Pour comprendre les différences entre les fonctionnalités de SQL Database et de SQL Server, consultez l’article sur les [fonctionnalités SQL](sql-database-features.md). 

SQL Database offre des performances prévisibles à plusieurs niveaux de service qui permettent une évolutivité dynamique avec aucun temps mort, une optimisation intelligente intégrée, une évolutivité et une disponibilité globales, ainsi que des options avancées de sécurité, et tout cela sans (presque) aucune tâche d’administration. Ces fonctionnalités vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à la gestion des machines virtuelles et de leur infrastructure. Le service SQL Database est actuellement présent dans 38 centres de données du monde entier, et de plus en plus de centres de données passent en ligne régulièrement, ce qui vous permet d’exécuter votre base de données dans un centre de données proche de chez vous.

> [!IMPORTANT]
> SQL Database Managed Instance est actuellement en préversion et est uniquement disponible à un seul niveau de service. Pour plus d’informations, consultez [SQL Database Managed Instance](sql-database-managed-instance.md).
>

## <a name="scalable-performance-and-pools"></a>Pools et performances évolutives

Avec SQL Database, chaque base de données est isolée de l’autre et portable, chacune avec son propre [niveau de service](sql-database-service-tiers.md) et donc sa garantie de performances. SQL Database fournit plusieurs niveaux de performances pour des besoins différents et permet de regrouper les bases de données pour optimiser l’utilisation des ressources et économiser de l’argent.

Avec SQL Database Managed Instance, chaque instance est isolée des autres instances avec des ressources garanties. Pour plus d’informations, consultez [SQL Database Managed Instance](sql-database-managed-instance.md) 

### <a name="adjust-performance-and-scale-without-downtime"></a>Ajustez les performances et la mise à l'échelle sans interruption de service

SQL Database offre trois niveaux de service pour prendre en charge des charges de travail de base de données plus ou moins lourdes : De base, Standard et Premium. Vous pouvez créer votre première application sur une seule petite base de données pour un coût mensuel modique et modifier le niveau de service manuellement ou automatiquement à tout moment pour répondre aux besoins de votre solution. Vous pouvez ajuster les performances sans perturber le fonctionnement de votre application, ni l’expérience de vos clients. L’évolutivité dynamique permet à votre base de données de répondre en toute transparence aux besoins en ressources qui évoluent sans cesse et de payer uniquement les ressources dont vous avez besoin, lorsque vous en avez besoin.

   ![mise à l’échelle](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

SQL Database Managed Instance est en préversion et un seul niveau de service est disponible. Pour plus d’informations, consultez [SQL Database Managed Instance](sql-database-managed-instance.md)

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Pools élastiques pour optimiser l’utilisation des ressources

Pour de nombreuses entreprises et applications, la possibilité de créer des bases de données uniques et d’augmenter ou de ralentir les performances à la demande se révèle suffisante, surtout si les modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d'utilisation imprévisibles, il peut être difficile de gérer les coûts et votre modèle commercial. [Les pools élastiques](sql-database-elastic-pool.md) sont conçus pour résoudre ce problème. Le concept est simple. Vous allouez des ressources de performance à un pool plutôt qu’à une base de données individuelle et vous payez pour les ressources de performance d’un pool plutôt que pour celles d’une base de données unique. 

   ![pools élastiques](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Les pools élastiques vous permettent de ne pas avoir à ajuster les performances de la base de données en fonction des besoins en ressources. Les bases de données regroupées consomment les ressources de performance du pool élastique en fonction des besoins. Les bases de données regroupées consomment mais ne dépassent pas les limites du pool. Vos coûts restent prévisibles, même si l’utilisation de la base de données individuelle ne l’est pas. Par ailleurs, vous pouvez [ajouter et supprimer des bases de données du pool](sql-database-elastic-pool-manage-portal.md), mettre à l’échelle votre application en passant de quelques bases de données à des milliers, le tout dans les limites d’un budget que vous contrôlez. Vous pouvez également contrôler le nombre minimal et maximal de ressources disponibles pour les bases de données dans le pool. Cela vous permet de garantir qu’aucune base de données du pool n’utilise toutes les ressources du pool et que chaque base de données dispose d’un nombre minimal de ressources. Pour en savoir plus sur les modèles de conception pour les applications SaaS avec des pools élastiques, voir [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Modèles de conception pour les applications SaaS mutualisées avec SQL Database).

> [!IMPORTANT]
> SQL Database Managed Instance ne prend pas en charge les pools élastiques.

### <a name="blend-single-databases-with-pooled-databases"></a>Fusionner les bases de données uniques avec les bases de données regroupées

Quelle que soit la méthode utilisée (bases de données uniques ou pools élastiques), rien n’est irréversible. Vous pouvez fusionner des bases de données uniques avec des pools élastiques et modifier les niveaux de service des bases de données uniques et des pools élastiques rapidement et en toute simplicité, pour adapter le système à votre situation. Grâce à la puissance et à la portée d’Azure, vous pouvez mélanger les autres services Azure avec SQL Database, afin de pouvoir répondre aux besoins uniques de vos applications en matière de conception, réduire les coûts, optimiser l’efficacité des ressources et créer de nouvelles opportunités professionnelles.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Fonctionnalités étendues de surveillance et d’alerte

Mais comment comparer les performances relatives des bases de données uniques et des pools élastiques ? Comment faire la part des choses entre les différents niveaux de service selon une mise à l'échelle verticale ? Vous utilisez les outils [d’alerte](sql-database-insights-alerts-portal.md) et [de surveillance des performances intégrés](sql-database-performance.md) en plus des évaluations de performances basées sur [les unités de transaction de base de données (DTU) pour les bases de données uniques et les DTU élastiques (eDTU) pour les pools élastiques](sql-database-what-is-a-dtu.md). Ces outils vous permettent d’évaluer rapidement l’impact des mises à l’échelle (montées ou descentes en charge) en fonction de vos besoins en performances actuels ou pour un projet. Voir [Options et performances de la base de données SQL : comprendre ce qui est disponible dans chaque niveau de service](sql-database-service-tiers.md) pour plus d’informations.

En outre, SQL Database peut [émettre des mesures et des journaux de diagnostic](sql-database-metrics-diag-logging.md) pour faciliter la surveillance. Vous pouvez configurer SQL Database pour stocker l’utilisation des ressources, les employés et les sessions, ainsi que la connectivité dans une de ces ressources Azure :

- **Stockage Azure** : pour archiver des quantités importantes de données de télémétrie à un petit prix
- **Hub d’événements Azure** : pour intégrer des données de télémétrie SQL Database à votre solution de surveillance personnalisée ou à vos pipelines très actifs
- **Azure Log Analytics** : pour une solution de surveillance intégrée avec des fonctionnalités de génération de rapports, d’alerte et d’atténuation

    ![architecture](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Fonctionnalités de disponibilité

Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service [(SLA)](http://azure.microsoft.com/support/legal/sla/)d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. En outre, SQL Database fournit des fonctionnalités intégrées de [continuité d’activité et d’évolutivité globale](sql-database-business-continuity.md), notamment :

- **[Sauvegardes automatiques](sql-database-automated-backups.md)** : SQL Database effectue automatiquement des sauvegardes complètes, différentielles et de fichier journal.
- **[Restaurations à un point dans le temps](sql-database-recovery-using-backups.md)** : SQL Database prend en charge la récupération à n’importe quel point dans le temps au cours de la période de rétention de sauvegarde automatique.
- **[Géo-réplication active](sql-database-geo-replication-overview.md)** : SQL Database permet de configurer jusqu’à quatre bases de données secondaires accessibles en lecture, que ce soit dans les mêmes centres de données ou dans des centres de données éparpillés dans le monde.  Par exemple, si vous avez une application SaaS avec une base de données de catalogue qui a un volume élevé de transactions simultanées en lecture seule, utilisez la géo-réplication active pour permettre une mise à l’échelle de lecture globale et supprimer les goulots d’étranglement sur le serveur principal qui sont dus à des charges de travail en lecture. 
- **[Groupes de basculement](sql-database-geo-replication-overview.md)** : SQL Database permet d’activer la haute disponibilité et l’équilibrage de charge à une échelle globale, y compris la géo-réplication transparente et le basculement de grands ensembles de bases de données et de pools élastiques. Les groupes de basculement et la géo-réplication active permettent la création d’applications SaaS distribuées dans le monde entier avec une administration minimale qui laisse toutes les tâches complexes de surveillance, de routage et d’orchestration de basculement aux bons soins de SQL Database.
- **[Bases de données redondantes dans une zone](sql-database-high-availability.md)** : SQL Database vous permet de configurer des bases de données Premium ou des pools élastiques Premium sur plusieurs zones de disponibilité. Les bases de données Premium et les pools élastiques Premium ont plusieurs réplicas redondants pour une haute disponibilité. Placer ces réplicas dans plusieurs zones de disponibilité procure donc une résilience supérieure, et donne la possibilité d’effectuer une récupération automatique des échecs de mise à l’échelle du centre de données sans perte de données. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. 

## <a name="built-in-intelligence"></a>Intelligence intégrée

Avec SQL Database, vous obtenez une intelligence intégrée qui permet de réduire considérablement les coûts d’exécution et de gestion des bases de données, et d’optimiser les performances et la sécurité de votre application. Via l’exécution de millions de charges de travail client 24 heures sur 24, SQL Database collecte et traite une quantité vertigineuse de données de télémétrie, tout en respectant également pleinement la confidentialité des clients en arrière-plan. Différents algorithmes évaluent en permanence les données de télémétrie afin que le service puisse apprendre et s’adapter à votre application. En fonction de cette analyse, le service propose des solutions pour améliorer les performances en tenant compte de votre charge de travail spécifique. 

### <a name="automatic-performance-monitoring-and-tuning"></a>Surveillance et réglage des performances automatiques

SQL Database fournit des informations détaillées sur les requêtes que vous devez surveiller. SQL Database en apprend plus sur les modèles de votre base de données et vous permet d’adapter le schéma de base de données à votre charge de travail. SQL Database propose des [recommandations pour le réglage des performances](sql-database-advisor.md), où vous pouvez passer en revue les actions de réglage et les appliquer. 

Toutefois, la surveillance permanente de la base de données est une tâche difficile et fastidieuse, en particulier lors du traitement de plusieurs bases de données. [Intelligent Insights](sql-database-intelligent-insights.md) effectue cette tâche pour vous en surveillant automatiquement les performances de SQL Database à l’échelle et vous informe des problèmes de dégradation des performances, identifie la cause du problème et fournit des recommandations pour l’amélioration des performances lorsque cela est possible.

La gestion efficace d’un très grand nombre de bases de données peut s’avérer impossible même avec tous les rapports et outils disponibles avec SQL Database et Azure. Au lieu de surveiller et de régler votre base de données manuellement, vous pouvez envisager de déléguer certaines actions de surveillance et de réglage à SQL Database grâce au [réglage automatique](sql-database-automatic-tuning.md). SQL Database applique automatiquement les recommandations, puis teste et vérifie chacune de ses actions de réglage afin de garantir l’amélioration continue des performances. De cette manière, SQL Database s’adapte automatiquement à votre charge de travail de façon contrôlée et sécurisée. Le réglage automatique signifie que les performances de votre base de données sont soigneusement surveillées et comparées avant et après chaque action de réglage et, si les performances ne s’améliorent pas, l’action de réglage est annulée.

Aujourd’hui, plusieurs de nos partenaires exécutant des [applications SaaS multi-locataires](sql-database-design-patterns-multi-tenancy-saas-applications.md) sur SQL Database font confiance au réglage des performances pour garantir que leurs applications ont toujours des performances stables et prévisibles. Pour eux, cette fonctionnalité réduit considérablement le risque de voir survenir un incident lié aux performances au milieu de la nuit. En outre, étant donné qu’une partie de leurs clients utilisent également SQL Server, ils se servent des mêmes recommandations d’indexation fournies par SQL Database pour aider leurs clients exécutant SQL Server.

Deux aspects de réglage automatique sont [disponibles dans SQL Database](sql-database-automatic-tuning.md) :

- **Gestion automatique des index** : identifie les index qui doivent être ajoutés à votre base de données et ceux qui doivent être supprimés.
- **Correction du plan automatique** : identifie les plans problématiques et résout les problèmes de performances du plan SQL (déployé prochainement, déjà disponible dans SQL Server 2017).

### <a name="adaptive-query-processing"></a>Traitement adaptatif des requêtes

Nous ajoutons également à SQL Database la famille des fonctionnalités de [traitement adaptatif des requêtes](/sql/relational-databases/performance/adaptive-query-processing), y compris l’exécution entrelacée pour les fonctions TVF à instructions multiples, les commentaires d’allocation de mémoire en mode traitement par lots et les jointures adaptatives en mode de traitement par lots. Chacune de ces fonctionnalités de traitement adaptatif des requêtes applique des techniques similaires d’apprentissage et d’adaptation, ce qui contribue à résoudre les problèmes de performances liés aux soucis d’optimisation de requête historiquement intraitables.

### <a name="intelligent-threat-detection"></a>Détection intelligente des menaces

 La [détection des menaces SQL](sql-database-threat-detection.md) tire parti de l’[audit SQL Database](sql-database-auditing.md) pour surveiller en permanence les bases de données SQL Azure et repérer les tentatives potentiellement dangereuses d’accès aux données sensibles. La détection des menaces SQL fournit une nouvelle couche de sécurité qui permet aux clients de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales. Les utilisateurs reçoivent une alerte en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes générées par la détection des menaces SQL fournissent des détails sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace. Les utilisateurs peuvent analyser les événements suspects pour déterminer s’ils sont le résultat d’une tentative d’accès, d’une violation ou d’une exploitation des données dans la base de données. La détection des menaces vous permet de réagir facilement aux menaces potentielles visant la base de données sans devenir un expert en sécurité ou gérer des systèmes avancés de surveillance de la sécurité.

## <a name="advanced-security-and-compliance"></a>Sécurité et conformité avancées

SQL Database fournit toute une gamme de [fonctionnalités intégrées de sécurité et conformité](sql-database-security-overview.md) pour que votre application réponde aux différentes exigences de conformité et de sécurité. 

### <a name="auditing-for-compliance-and-security"></a>Audit de sécurité et de conformité

L’[audit SQL Database](sql-database-auditing.md) suit les événements de base de données et les écrit dans un journal d’audit dans votre compte de stockage Azure. L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

### <a name="data-encryption-at-rest"></a>Chiffrement des données au repos

La fonctionnalité [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) de SQL Database protège le système contre toute menace d’activité malveillante, en effectuant un chiffrement et un déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions au repos, sans qu’il soit nécessaire de modifier l’application. Depuis mai 2017, toutes les nouvelles bases de données SQL Azure sont automatiquement protégées à l’aide de la fonctionnalité TDE. Il s’agit de la technologie de chiffrement au repos éprouvée de SQL, qui est requise par de nombreuses normes de conformité comme protection contre le vol d’un support de stockage. Les clients peuvent gérer les clés de chiffrement TDE et d’autres secrets de manière sécurisée et conforme à l’aide d’Azure Key Vault.

### <a name="data-encryption-in-motion"></a>Chiffrement des données en mouvement

SQL Database est le seul système de base de données à offrir une protection des données sensibles à la volée, au repos et pendant le traitement des requêtes avec [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted est une nouveauté qui offre une protection inégalée des données contre les failles de sécurité impliquant le vol de données critiques. Par exemple, avec Always Encrypted, les numéros de carte de crédit des clients sont toujours chiffrés dans la base de données, même pendant le traitement des requêtes, ce qui permet le déchiffrement au point d’utilisation par les applications ou le personnel autorisés qui doivent traiter ces données.

### <a name="dynamic-data-masking"></a>Masquage des données dynamiques

Le [masquage des données dynamiques de SQL Database](sql-database-dynamic-data-masking-get-started.md) limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège. Le masquage des données dynamiques empêche tout accès non autorisé aux données sensibles en permettant aux clients d’indiquer la quantité de données sensibles à faire apparaître, avec un impact minimal sur la couche application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées.

### <a name="row-level-security"></a>Sécurité au niveau des lignes

La [sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permet aux clients de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête (par exemple, appartenance à un groupe ou contexte d’exécution). La sécurité au niveau des lignes (RLS) simplifie la conception et le codage de la sécurité dans votre application. Elle vous permet d’implémenter des restrictions sur l’accès aux lignes de données. Par exemple, en s’assurant que les employés ne peuvent accéder qu’aux lignes de données utiles à leur service, ou en limitant l’accès aux données d’un client aux données relatives à son entreprise uniquement.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Intégration d’Azure Active Directory et authentification multifacteur

SQL Database vous permet de gérer de manière centralisée les identités d’utilisateur de base de données et d’autres services Microsoft avec l’[intégration d’Azure Active Directory](sql-database-aad-authentication.md). Cette fonctionnalité simplifie la gestion des autorisations et améliore la sécurité. Azure Active Directory prend en charge l’[authentification multifacteur](sql-database-ssms-mfa-authentication.md) (MFA) pour augmenter la sécurité des données et des applications, ainsi qu’un processus d’authentification unique.

### <a name="compliance-certification"></a>Certification de conformité

SQL Database participe à des audits réguliers et a été certifié par rapport à plusieurs normes de conformité. Pour en savoir plus, accédez au [Centre de confidentialité Microsoft Azure](https://azure.microsoft.com/support/trust-center/), qui inclut la liste la plus à jour des [certifications de conformité de la base de données SQL](https://azure.microsoft.com/support/trust-center/services/).

## <a name="easy-to-use-tools"></a>Outils simples d’utilisation

SQL Database simplifie la création et la gestion des applications et vous fait gagner en productivité. SQL Database vous aide à vous concentrer sur ce que vous maîtrisez le mieux, à savoir le développement de formidables applications. Dans SQL Database, vous pouvez gérer et développer des applications à l’aide des outils et des compétences dont vous disposez déjà.

- **[Portail Azure](https://portal.azure.com/)** : application web dédiée à la gestion de tous les services Azure 
- **[SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** : application client téléchargeable gratuitement dédiée à la gestion des infrastructures SQL, allant de SQL Server à SQL Database
- **[SQL Server Data Tools dans Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** : application client téléchargeable gratuitement, dédiée au développement de bases de données relationnelles SQL Server, de bases de données SQL Azure, de packages Integration Services, de modèles de données Analysis Services et de rapports Reporting Services.
- **[Visual Studio Code](https://code.visualstudio.com/docs)** : éditeur de code open source téléchargeable gratuitement pour Linux, macOS et Windows qui prend en charge les extensions, y compris l’[extension mssql](https://aka.ms/mssql-marketplace) pour l’exécution de requêtes dans Microsoft SQL Server, Azure SQL Database et SQL Data Warehouse.

SQL Database prend en charge la génération d’applications avec Python, Java, Node.js, PHP, Ruby et .NET sur MacOS, Linux et Windows. SQL Database prend en charge les mêmes [bibliothèques de connexions](sql-database-libraries.md) que SQL Server.

## <a name="engage-with-the-sql-server-engineering-team"></a>Collaborer avec l’équipe d’ingénierie de SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server) : posez vos questions sur l’administration de bases de données
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server) : posez vos questions sur le développement
- [Forums MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver) : posez vos questions techniques
- [Feedback](http://aka.ms/sqlfeedback) : signalez des bogues et demandez une fonctionnalité
- [Reddit](https://www.reddit.com/r/SQLServer/) : échangez au sujet de SQL Server

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [page de tarification](https://azure.microsoft.com/pricing/details/sql-database/) pour obtenir un comparatif des coûts entre une base de données unique et des pools élastiques et accéder à des calculateurs.

- Consultez ces démarrages rapides pour bien commencer :

  - [Créer une base de données SQL dans le portail Azure](sql-database-get-started-portal.md)  
  - [Créer une base de données SQL avec Azure CLI](sql-database-get-started-cli.md)
  - [Créer une base de données SQL à l’aide de PowerShell](sql-database-get-started-powershell.md)

- Pour accéder à des exemples Azure CLI et PowerShell, consultez :
  - [Exemples d’interface de ligne de commande Azure pour SQL Database](sql-database-cli-samples.md)
  - [Exemples Azure PowerShell pour SQL Database](sql-database-powershell-samples.md)
