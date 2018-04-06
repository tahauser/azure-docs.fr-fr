---
title: Présentation d’Azure SQL Database Managed Instance | Microsoft Docs
description: Cette rubrique décrit Azure SQL Database Managed Instance et explique son fonctionnement ainsi que ses différences par rapport à une base de données unique dans Azure SQL Database.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/22/2018
ms.author: bonova
ms.openlocfilehash: 2d07d58114a4d89f40a4ea9e388c58f58494766c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-a-managed-instance-preview"></a>Présentation de l’option Managed Instance (préversion)

Azure SQL Database Managed Instance (préversion) est une nouvelle fonctionnalité d’Azure SQL Database, presque 100 % compatible avec SQL Server localement (Édition Entreprise), qui fournit une implémentation de [réseau virtuel (VNet)](../virtual-network/virtual-networks-overview.md) native qui traite les problèmes de sécurité courants, ainsi qu’un [modèle d’entreprise](https://azure.microsoft.com/pricing/details/sql-database/) favorable aux clients SQL Server locaux. Managed Instance permet aux clients SQL Server existants d’effectuer une migration « lift-and-shift » de leurs applications locales vers le cloud en apportant des modifications minimales aux applications et bases de données. En même temps, Managed Instance conserve toutes les fonctionnalités PaaS (correctifs automatiques et mises à jour de versions, sauvegarde, haute disponibilité), ce qui réduit considérablement le temps de gestion et le coût total de possession.

> [!IMPORTANT]
> Pour obtenir une liste de régions dans lesquelles Managed Instance est actuellement disponible, consultez [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
Le diagramme suivant présente les principales fonctionnalités de Managed Instance :

![fonctionnalités clés](./media/sql-database-managed-instance/key-features.png)

L’option Managed Instance est pressentie comme la plateforme préférée dans les scénarios suivants : 

- Clients SQL Server localaux/IaaS cherchant à effecuter une migration de leurs applications vers un service entièrement géré avec peu de modifications de conception.
- Éditeurs de logiciels indépendants (ISV) s’appuyant sur des bases de données SQL qui veulent permettre à leurs clients de migrer vers le cloud afin de bénéficier d’un avantage concurrentiel considérable ou d’atteindre le marché mondial. 

En disponibilité générale, Managed Instance a pour but d’offrir une compatibilité de la surface d’exposition proche de 100 % avec la dernière version de SQL Server locale par le biais d’un plan de mise en production intermédiaire. 

Le tableau suivant indique les principales différences entre SQL IaaS, Azure SQL Database et SQL Database Managed Instance, ainsi que leurs principaux scénarios d’usage envisagés :

| | Scénario d’usage | 
| --- | --- | 
|SQL Database Managed Instance |Aux clients cherchant à effectuer une migration d’un grand nombre d’applications locales ou IaaS, automatiquement générées ou fournies par un éditeur de logiciels indépendant, avec un effort de migration aussi faible que possible, proposez Managed Instance. À l’aide du [service de migration des données](/sql/dma/dma-overview) entièrement automatisé dans Azure, les clients peuvent effectuer une migration « lift-and-shift » de leur SQL Server local vers l’option Managed Instance qui offre une compatibilité avec SQL Server local et une isolation totale des instances des clients avec une prise en charge de réseau virtuel native.  Avec Software Assurance, vous pouvez échanger leurs licences existantes avec des tarifs réduits sur SQL Database Managed Instance en utilisant [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  SQL Database Managed Instance est la meilleure destination de migration dans le cloud pour les instances SQL Server qui nécessitent une sécurité élevée et une surface de programmabilité riche. |
|Azure SQL Database (unique ou pool) |**Pools élastiques** : aux clients qui développent de nouvelles applications multilocataires SaaS ou qui transforment intentionnellement leurs applications locales existantes en applications multilocataires SaaS, proposez des pools élastiques. Les avantages de ce modèle sont : <br><ul><li>Conversion du modèle d’entreprise de la vente de licences à la vente d’abonnements à des services (pour les éditeurs de logiciels indépendants)</li></ul><ul><li>Isolation simple et renforcée des locataires</li></ul><ul><li>Modèle de programmation simplifié et centré sur la base de données</li></ul><ul><li>Possibilité d’augmenter la taille des instances sans limite maximale</li></ul>**Bases de données uniques** : aux clients qui développent de nouvelles applications autres que des applications multilocataires SaaS, dont la charge de travail est stable et prévisible, proposez des bases de données uniques. Les avantages de ce modèle sont :<ul><li>Modèle de programmation simplifié et centré sur la base de données</li></ul>  <ul><li>Performances prévisibles pour chaque base de données</li></ul>|
|Machine virtuelle IaaS SQL|Aux clients qui ont besoin de personnaliser le système d’exploitation ou le serveur de base de données, ainsi qu’aux clients qui ont des exigences spécifiques liées à l’exécution d’applications tierces à côté de SQL Server (sur la même machine virtuelle), proposez des machines virtuelles/IaaS SQL en tant que solution optimale.|
|||

<!---![positioning](./media/sql-database-managed-instance/positioning.png)--->

## <a name="how-to-programmatically-identify-a-managed-instance"></a>Comment identifier par programmation une option Managed Instance

Le tableau suivant montre plusieurs propriétés, accessibles par le biais de Transact SQL, que vous pouvez utiliser pour détecter que votre application fonctionne avec Managed Instance et récupérer des propriétés importantes.

|Propriété|Valeur|Commentaire|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Cette valeur est identique à celle indiquée dans SQL Database.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Cette valeur est identique à celle indiquée dans SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Cette valeur identifie Managed Instance de façon unique.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nom DNS d’instance complet au format suivant :<instanceName>.<dnsPrefix>.database.windows.net, où <instanceName> est le nom fourni par le client, tandis que <dnsPrefix> est une partie générée automatiquement du nom garantissant l’unicité des noms DNS globaux (par exemple, « wcus17662feb9ce98 »)|Exemple : my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Principales fonctionnalités de Managed Instance 

> [!IMPORTANT]
> Une instance Managed Instance s’exécute avec toutes les fonctionnalités de la version SQL Server la plus récente, notamment les opérations en ligne, les corrections de plan automatiques et autres améliorations des performances d’entreprise. 

| **Avantages PaaS** | **Continuité de l’activité** |
| --- | --- |
|Aucun achat ni gestion de matériel <br>Aucun temps de gestion à dédier à l’infrastructure sous-jacente <br>Provisionnement et mise à l’échelle du service rapides <br>Application automatisée de correctifs et de mises à niveau de version <br>Intégration à d’autres services de données PaaS |Contrat SLA à 99,99 % de durée de fonctionnement  <br>Haute disponibilité intégrée <br>Données protégées par des sauvegardes automatisées <br>Période de rétention de sauvegarde configurable par le client (7 jours en préversion publique) <br>Sauvegardes lancées par l’utilisateur <br>Fonctionnalité de limite de restauration dans le temps d’une base de données |
|**Sécurité et conformité** | **Gestion**|
|Environnement isolé (intégration de réseau virtuel, service de locataire unique, calcul et stockage dédiés) <br>Chiffrement des données en transit <br>Prise en charge de l’authentification unique Azure AD <br>Conformité aux mêmes normes qu’une base de données Azure SQL <br>Audit SQL <br>Détection de menaces |API Azure Resource Manager pour automatiser le provisionnement et la mise à l’échelle des services <br>Fonctionnalités du portail Azure pour le provisionnement et la mise à l’échelle manuels des services <br>Service de migration des données 

![Authentification unique](./media/sql-database-managed-instance/sso.png) 

## <a name="managed-instance-service-tier"></a>Niveau de service de Managed Instance

Managed Instance est initialement disponible dans un seul niveau de service (usage général), conçu pour les applications dont les besoins de disponibilité et de latence d’E/S sont standard.

La liste suivante décrit les principales caractéristiques du niveau de service Usage général : 

- Concevoir pour la majorité des applications métier dont les besoins de performances et de haute disponibilité sont standard 
- Stockage Azure Premium à hautes performances (8 To) 
- 100 bases de données par instance 

Dans ce niveau, vous pouvez sélectionner le stockage et la capacité de calcul indépendamment. 

Le diagramme suivant illustre le calcul actif et les nœuds redondants de ce niveau de service.
 
![Niveau de service Usage général](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

Voici les principales fonctionnalités du niveau de service Usage général :

|Fonctionnalité | Description|
|---|---|
| Nombre de vCores* | 8, 16, 24|
| Version/Build de SQL Server | SQL Server (version la plus récente disponible) |
| Taille de stockage minimale | 32 Go |
| Taille de stockage maximale | 8 To |
| Espace de stockage maximal par base de données | 4 To |
| IOPS de stockage attendues | De 500 à 7 500 IOPS par fichier de données (dépend du fichier de données) Consultez [Stockage Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes). |
| Nombre de fichiers de données (ROWS) par base de données | Multiple | 
| Nombre de fichiers journaux (LOG) par base de données | 1 | 
| Sauvegardes automatisées gérées | OUI |
| Haute disponibilité | Selon le stockage étendu et [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Analyse et métriques des instances et bases de données intégrées | OUI |
| Mise à jour corrective automatique des logiciels | OUI |
| Réseau virtuel - Déploiement Azure Resource Manager | OUI |
| Réseau virtuel - Modèle de déploiement classique | Non  |
| Prise en charge du portail | OUI|
|||

\* Un vCore représente l’UC logique offerte avec un choix à opérer entre plusieurs générations de matériel. Les UC logiques de 4e génération sont basées sur des processeurs Intel E5-2673 v3 (Haswell) de 2,4 GHz, et celles de 5e génération sur des processeurs Intel E5-2673 v4 (Broadwell) de 2,3 GHz.  

## <a name="advanced-security-and-compliance"></a>Sécurité et conformité avancées 

### <a name="managed-instance-security-isolation"></a>Isolation de la sécurité Managed Instance 

Managed Instance offre une isolation de la sécurité supplémentaire par rapport aux autres locataires dans le cloud Azure. L’isolation de la sécurité inclut : 

- Implémentation d’un réseau virtuel natif et sa connexion à votre environnement local à l’aide d’Azure ExpressRoute ou d’une passerelle VPN 
- Exposition du point de terminaison SQL uniquement par le biais d’une adresse IP privée, ce qui permet de sécuriser la connexion à partir de réseaux Azure privés ou hybrides
- Locataire unique avec infrastructure sous-jacente dédiée (calcul, stockage)

Le diagramme suivant présente la conception de l’isolation : 

![haute disponibilité](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>Audit de sécurité et de conformité 

[L’audit Managed Instance](sql-database-managed-instance-auditing.md) suit les événements de base de données et les écrit dans un journal d’audit dans votre compte de stockage Azure. L’audit permet de respecter une conformité réglementaire, de comprendre l’activité de la base de données et de découvrir des discordances et des anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité. 

### <a name="data-encryption-in-motion"></a>Chiffrement des données en mouvement 

Managed Instance sécurise vos données par le biais d’un chiffrement des données en mouvement à l’aide du protocole TLS.

En plus du protocole TLS, SQL Database Managed Instance offre une protection des données sensibles en vol, au repos et pendant le traitement des requêtes avec [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted est une nouveauté qui offre une protection inégalée des données contre les failles de sécurité impliquant le vol de données critiques. Par exemple, avec Always Encrypted, les numéros de carte de crédit sont toujours chiffrés dans la base de données, même pendant le traitement des requêtes, ce qui permet le déchiffrement au point d’utilisation par les applications ou le personnel autorisés qui doivent traiter ces données. 

### <a name="dynamic-data-masking"></a>Masquage des données dynamiques 

Le [masquage des données dynamiques](/sql/relational-databases/security/dynamic-data-masking) de SQL Database limite l’exposition des données sensibles en les masquant aux utilisateurs sans privilège. Le masquage des données dynamique contribue à empêcher tout accès non autorisé aux données sensibles en vous permettant d’indiquer la quantité de données sensibles à révéler avec un impact minimal sur la couche Application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées. 

### <a name="row-level-security"></a>Sécurité au niveau des lignes 

La [sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security) vous permet de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête (par exemple, appartenance à un groupe ou contexte d’exécution). La sécurité au niveau des lignes (RLS) simplifie la conception et le codage de la sécurité dans votre application. Elle vous permet d’implémenter des restrictions sur l’accès aux lignes de données. Par exemple, en s’assurant que les employés ne peuvent accéder qu’aux lignes de données utiles à leur service, ou en limitant l’accès aux données aux seules données pertinentes. 

### <a name="threat-detection"></a>Détection de menaces 

[Managed Instance Threat Detection](sql-database-managed-instance-threat-detection.md) complète [l’audit Managed Instance](sql-database-managed-instance-auditing.md) en fournissant une couche supplémentaire d’informations de sécurité intégrée au service, lequel vise à détecter les tentatives inhabituelles ou potentiellement dangereuses d’accès à des bases de données ou d’exploitation des failles de sécurité de celles-ci. Vous êtes alerté en cas d’activités suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes Threat Detection peuvent être consultées à partir d’[Azure Security Center](https://azure.microsoft.com/services/security-center/). Elles fournissent des détails sur les activités suspectes et recommandent l’action à entreprendre pour analyser et prévenir la menace.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Intégration d’Azure Active Directory et authentification multifacteur 

SQL Database vous permet de gérer de manière centralisée les identités d’utilisateur de base de données et d’autres services Microsoft avec l’[intégration d’Azure Active Directory](sql-database-aad-authentication.md). Cette fonctionnalité simplifie la gestion des autorisations et améliore la sécurité. Azure Active Directory prend en charge l’[authentification multifacteur](sql-database-ssms-mfa-authentication-configure.md) (MFA) pour augmenter la sécurité des données et des applications, ainsi qu’un processus d’authentification unique. 

### <a name="authentication"></a>Authentification 
L’authentification de base de données SQL fait référence au processus de validation de l’identité des utilisateurs lorsqu’ils se connectent à la base de données. Une base de données SQL prend en charge deux types d’authentification :  

- L’authentification SQL, qui utilise un nom d’utilisateur et un mot de passe.
- L’authentification Azure Active Directory, qui utilise des identités gérées par Azure Active Directory et qui est prise en charge pour des domaines gérés et intégrés.  

### <a name="authorization"></a>Authorization

Le terme autorisation fait référence aux actions qu’un utilisateur peut exécuter au sein d’Azure SQL Database. Celles-ci sont contrôlées par les appartenances aux rôles et les autorisations au niveau objet de la base de données de votre compte d’utilisateur. Managed Instance a les mêmes fonctionnalités d’autorisation que SQL Server 2017. 

## <a name="database-migration"></a>Migration de base de données 

Managed Instance cible des scénarios d’utilisateur impliquant une migration de base de données en masse depuis des implémentations locales ou IaaS.  Managed Instance prend en charge plusieurs options de migration de base de données : 

### <a name="data-migration-service"></a>Service de migration des données

Azure Database Migration Service est un service entièrement géré conçu pour permettre des migrations transparentes de plusieurs sources de base de données vers des plateformes de données Azure avec un temps d’arrêt minime.   Ce service simplifie les tâches nécessaires pour déplacer des bases de données SQL Server tierces existantes vers Azure. Les options de déploiement incluent Azure SQL Database, Managed Instance et SQL Server dans une machine virtuelle Azure en préversion publique. Consultez [Comment migrer votre base de données locale vers Managed Instance à l’aide de DMS](https://aka.ms/migratetoMIusingDMS).  

### <a name="backup-and-restore"></a>Sauvegarde et restauration  

L’approche de la migration s’appuie sur les sauvegardes SQL dans Stockage Blob Azure. Les sauvegardes stockées dans Azure Storage Blob peuvent être directement restaurées dans Managed Instance. 

## <a name="sql-features-supported"></a>Fonctionnalités SQL prises en charge 

Managed Instance vise à assurer une compatibilité de la surface d’exposition proche de 100 % avec SQL Server local par étapes jusqu’à la disponibilité générale du service. Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
 
Managed Instance prend en charge la compatibilité descendante avec les bases de données SQL 2008.  La migration directe à partir de serveurs de base de données SQL 2005 est prise en charge, le niveau de compatibilité des bases de données SQL 2005 qui ont migré est mis à jour vers SQL 2008. 
 
Le diagramme suivant illustre la compatibilité de la surface d’exposition dans Managed Instance :  

![migration](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Principales différences entre SQL Server local et Managed Instance 

Managed Instance tire parti du fait d’être toujours à jour dans le cloud, ce qui signifie que certaines fonctionnalités de SQL Server local peuvent quant à elles être obsolètes, supprimées ou remplacées par d’autres.  Dans certains cas, les outils ont besoin de reconnaître qu’une fonctionnalité particulière agit de façon légèrement différente ou que le service n’est pas exécuté dans un environnement que vous ne contrôlez pas entièrement : 

- La haute disponibilité est intégrée et préconfigurée. Les fonctionnalités de haute disponibilité Always On ne sont pas exposées de la même manière que sur des implémentations SQL IaaS. 
- Les sauvegardes sont automatisées et la restauration dans le temps limitée. Le client peut lancer des sauvegardes `copy-only` qui n’interfèrent pas avec la chaîne de sauvegarde automatique. 
- Managed Instance ne permet pas de spécifier des chemins physiques complets si bien que tous les scénarios correspondants doivent être pris en charge différemment : RESTORE DB ne prend pas en charge WITH MOVE, CREATE DB n’autorise pas de chemins physiques, BULK INSERT fonctionne avec les objets blob Azure uniquement, etc. 
- Managed Instance prend en charge l’[authentification Azure AD](sql-database-aad-authentication.md) en tant qu’alternative cloud à l’authentification Windows. 
- Managed Instance gère automatiquement le groupe de fichiers et les fichiers XTP des bases de données contenant des objets OLTP In-Memory.
 
### <a name="managed-instance-administration-features"></a>Fonctionnalités administratives de Managed Instance  

Managed Instance permet aux administrateurs système de se concentrer sur ce qui importe le plus à l’entreprise. De nombreuses activités d’administrateur système/de base de données ne sont pas nécessaires ou sont très simples. Par exemple, l’installation et la mise à jour corrective d’un système d’exploitation/système de gestion de base de données relationnelle, le redimensionnement et la configuration d’une instance dynamique, les sauvegardes, la réplication de base de données (notamment des bases de données système), ainsi que la configuration des flux de données de surveillance de l’intégrité et des performances.  

> [!IMPORTANT]
> Pour obtenir la liste des fonctionnalités prises en charge, partiellement prises en charge et non prises en charge, consultez [Fonctionnalités de SQL Database](sql-database-features.md). Pour obtenir la liste des différences de T-SQL entre Managed Instance et SQL Server, consultez [Différences de T-SQL entre Managed Instance et SQL Server](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>Étapes suivantes

- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
- Pour un tutoriel qui crée une option Managed Instance et restaure une base de données à partir d’un fichier de sauvegarde, consultez [Créer une option Managed Instance](sql-database-managed-instance-tutorial-portal.md).
- Pour un tutoriel utilisant le service Azure Database Migration Service (DMS) pour la migration, consultez [Migrer SQL vers Azure SQL Database Managed Instance](../dms/tutorial-sql-server-to-managed-instance.md).
