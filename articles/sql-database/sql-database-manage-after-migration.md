---
title: "Gérer après la migration - Azure SQL Database | Microsoft Docs"
description: "Découvrez comment gérer votre base de données après la migration vers Azure SQL Database."
services: sql-database
documentationcenter: 
author: joesackmsft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 10/14/2016
ms.author: Joe.Sack
ms.suite: sql
ms.prod_service: sql-database
ms.component: migration
ms.openlocfilehash: e562c33cabc7d39d1f6a911c21343f85da205c0b
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="how-should-i-manage-my-azure-sql-database-after-migration"></a>Comment gérer ma base de données après la migration vers Azure SQL Database ?

*Forum aux questions sur la gestion de vos investissements relatifs à Azure SQL Database*

Vous venez tout juste de faire migrer des bases de données SQL Server vers Azure SQL Database, ou vous comptez peut-être le faire très bientôt. Et après ? Comme SQL Database est une offre *PaaS (Platform as a Service)*, Microsoft gère plusieurs choses à votre place. Mais, concrètement, en quoi cela change-t-il les pratiques de votre société concernant les domaines clés tels que la sécurité, la continuité d’activité, la maintenance de base de données, l’optimisation des performances, la surveillance, etc. ? 

L’objectif de cet article est d’organiser de manière succincte les ressources et l’aide dont vous avez besoin pour gérer vos investissements relatifs à SQL Database. Les principaux aspects traités dans cet article sont la continuité d’activité, la sécurité, la maintenance et la surveillance de base de données, le niveau de performance et le mouvement des données. Nous allons aborder les domaines clés qui diffèrent entre SQL Server et SQL Database. Nous allons également décrire les bonnes pratiques qui vous aideront à optimiser les avantages et réduire les risques. 

## <a name="manage-business-continuity-after-migration"></a>Gérer la continuité d’activité après la migration

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Comment créer et gérer les sauvegardes dans SQL Database ? 
SQL Database sauvegarde automatiquement les bases de données à votre place et vous permet de les restaurer à tout moment durant la période de rétention. La période de rétention est de 35 jours pour les bases de données Standard et Premium, et de 7 jours pour les bases de données De base. De plus, la fonctionnalité de rétention à long terme vous permet de stocker les fichiers de sauvegarde pour une période plus longue (jusqu’à 10 ans), et de les restaurer à tout moment à partir de ces sauvegardes. Ce n’est pas tout, les sauvegardes de base de données sont géorépliquées pour garantir leur géorestauration dans n’importe quelle partie du globe, en cas d’urgence ou de catastrophe à l’échelle régionale. Consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-a-regional-catastrophe"></a>Comment assurer la continuité d’activité en cas d’urgence au niveau du centre de données ou en cas de catastrophe régionale ? 

Les sauvegardes de base de données sont géorépliquées pour garantir leur géorestauration dans n’importe quelle partie du globe, en cas d’urgence ou de catastrophe à l’échelle régionale. Consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md). De plus, SQL Database permet de gérer de manière active les bases de données secondaires géorépliquées dans une autre région. Si vous les configurez dans un groupe de basculement automatique, vous avez la garantie que les bases de données vont basculer automatiquement vers les bases de données secondaires en cas d’urgence. Si aucun groupe de basculement automatique n’est configuré, votre application doit effectuer une surveillance active et déclencher le basculement vers les bases de données secondaires en cas d’urgence. 
### <a name="sql-server-provided-me-readable-secondary-replicas-can-i-access-the-secondaries-on-sql-database"></a>SQL Server m’a fourni des réplicas secondaires accessibles en lecture. Puis-je accéder à ces réplicas secondaires sur SQL Database ? 

Oui, la fonctionnalité de « géoréplication active » permet de créer des réplicas secondaires accessibles en lecture. 

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premise-to-sql-database"></a>En quoi mon plan de récupération d’urgence local diffère de celui de SQL Database ? 
Les implémentations de SQL Server vous obligeaient à gérer activement les sauvegardes à l’aide de fonctionnalités telles que le clustering de basculement, la mise en miroir de bases de données, la réplication, la copie des journaux de transaction ou de simples sauvegardes BACPAC. Toutefois, sur SQL Database, les sauvegardes sont entièrement gérées par Microsoft. Vous avez uniquement des plans de sauvegarde et des plans de récupération d’urgence, configurables et opérationnels en quelques clics seulement via le portail Azure (ou en quelques commandes PowerShell). ‌
### <a name="in-the-event-of-disaster-how-do-i-recover-my-databases"></a>En cas d’urgence, comment récupérer mes bases de données ? 
SQL Database vous permet de restaurer automatiquement vos bases de données à n’importe quel point dans le temps au cours des 35 derniers jours. Vous pouvez recourir à cette option si vous perdez des données ou si vous êtes confronté à une situation d’urgence liée à l’application. 

En cas de situation d’urgence au niveau régional, si les bases de données secondaires géorépliquées sont configurées, vous pouvez effectuer une récupération à partir de vos bases de données secondaires géorépliquées dans une autre région. Pour accéder en temps réel à vos applications, vous pouvez effectuer un basculement manuel vers les bases de données secondaires géorépliquées de l’autre région. Si vous avez configuré un groupe de basculement automatique, ce basculement vers les bases de données secondaires géorépliquées se produit automatiquement en cas d’urgence. Si vous n’avez pas configuré de bases de données secondaires géorépliquées, vous pouvez toujours récupérer vos bases de données à partir des fichiers de sauvegarde répliqués automatiquement (fonctionnalité intégrée, aucune configuration nécessaire), moyennant une durée de récupération plus longue (durée maximale d’interruption admissible de 12 heures) et une perte de données limitée à une heure au maximum. 

### <a name="are-the-failovers-to-secondary-transparent-how-does-my-application-handle-database-failovers"></a>Est-ce que les basculements vers les bases de données secondaires sont transparents ? Comment mon application gère-t-elle les basculements de bases de données ? 
Si vous avez configuré des groupes de basculement automatique, le basculement vers le site secondaire est transparent. Toutefois, si ce n’est pas le cas, votre application doit intégrer la logique nécessaire pour surveiller la disponibilité du site principal et basculer manuellement vers le site secondaire. 
 
## <a name="manage-security-after-migration"></a>Gérer la sécurité après la migration

### <a name="how-can-i-restrict-access-to-my-sql-database"></a>Comment restreindre l’accès à mon service SQL Database ? 
 
Il existe quelques méthodes pour verrouiller l’accès à vos bases de données SQL. 
1. Limitez le passage par Internet. ExpressRoute vous permet de disposer d’un accès dédié par la fibre au réseau Azure pour éviter que vos données ne passent par Internet. Vous pouvez également configurer une connectivité interrégion à l’aide d’ExpressRoute. Les liens suivants décrivent ExpressRoute de manière plus détaillée : 
 - [Présentation d’ExpressRoute](../expressroute/expressroute-introduction.md)
 - [Configuration requise](../expressroute/expressroute-prerequisites.md) 
 - [Flux de travail](../expressroute/expressroute-workflows.md) 
 
2. Sélectionnez les ressources qui se connectent à SQL Database : 

   Par défaut, votre service SQL Database est configuré pour « Autoriser tous les services Azure », ce qui signifie que toutes les machines virtuelles Azure peuvent essayer de se connecter à la base de données.  L’authentification de toutes les connexions est néanmoins obligatoire. Si vous ne souhaitez pas que votre base de données soit accessible à l’ensemble des adresses IP Azure, vous pouvez désactiver la fonctionnalité « Autoriser tous les services Azure » et utiliser les [points de terminaison de service de réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md) pour restreindre l’accès entrant de la base de données uniquement aux ressources Azure présentes dans un sous-réseau virtuel Azure spécifique. 

   ![Points de terminaison de service de réseau virtuel](./media/sql-database-manage-after-migration/vnet-service-endpoints.png) 

   Vous pouvez également provisionner des [adresses IP réservées](../virtual-network/virtual-networks-reserved-public-ip.md) pour vos machines virtuelles, et mettre en liste verte ces adresses IP de machines virtuelles spécifiques dans les paramètres de pare-feu du serveur. (Consultez la capture d’écran ci-dessous qui illustre un exemple dans le portail Azure.) En affectant des adresses IP réservées, vous évitez de devoir mettre à jour les règles du pare-feu en cas de changement des adresses IP. 

3. Évitez d’exposer le port 1433 en dehors d’Azure

   Exécutez SSMS dans Azure via [Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Cela vous dispense d’ouvrir des connexions sortantes pour le port 1433. Comme l’adresse IP est statique, la base de données peut être ouverte uniquement pour RemoteApp. MFA (Multi-Factor Authentication) et l’accès multiutilisateur sont pris en charge. 

### <a name="what-authentication-methods-are-offered-in-sql-database"></a>Quelles sont les méthodes d’authentification proposées dans SQL Database ?

Les principales méthodes d’authentification proposées dans SQL Database et SQL Data Warehouse sont l’authentification Azure Active Directory et l’authentification SQL. Azure Active Directory (AD) est un service centralisé de gestion des identités et des accès. SQL est l’un des nombreux services Azure intégrés à Azure AD. Un service géré centralisé présente l’avantage suivant : les informations d’identification de l’utilisateur sont partagées entre tous les services Azure utilisés pour simplifier l’authentification. Cela permet également à SQL Database et SQL Data Warehouse de proposer une authentification multifacteur et des comptes d’utilisateur Invité dans un domaine Azure AD. 

Si vous disposez déjà d’Active Directory localement, vous pouvez fédérer l’annuaire avec Azure Active Directory pour étendre votre annuaire à Azure. 


|||
|---|---|
| Si vous…|Azure SQL Database / Azure SQL Data Warehouse|
| Préférez ne pas utiliser Azure Active Directory (AD) dans Azure|Utilisez l’[authentification SQL](sql-database-security-overview.md)|
| Avez utilisé AD avec SQL Server localement|[Fédérez AD avec Azure AD](../active-directory/connect/active-directory-aadconnect.md), puis utilisez l’authentification basée sur Azure AD Authentication. Ainsi, vous pouvez utiliser l’authentification unique.|
| Devez appliquer l’authentification multifacteur via MFA (Multi-Factor Authentication)|Imposez MFA en tant que stratégie via l’[accès conditionnel Microsoft](sql-database-conditional-access.md), et utilisez l’[authentification universelle Azure AD avec prise en charge de MFA](sql-database-ssms-mfa-authentication.md).|
| Avez des comptes Invité issus de comptes Microsoft (live.com, outlook.com) ou d’autres domaines (gmail.com)|Utilisez l’[authentification universelle Azure AD](sql-database-ssms-mfa-authentication.md) de SQL Database/Data Warehouse, qui tire profit d’[Azure AD B2B Collaboration](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
| Êtes connecté à Windows avec des informations d’identification Azure AD provenant d’un domaine fédéré|Utilisez l’[authentification intégrée Azure AD](sql-database-aad-authentication-configure.md).|
| Êtes connecté à Windows avec des informations d’identification d’un domaine non fédéré avec Azure|Utilisez l’[authentification par mot de passe Azure AD](sql-database-aad-authentication-configure.md).|
| Avez des services de niveau intermédiaire qui doivent se connecter à Azure SQL Database ou Data Warehouse|Utilisez l’[authentification par jeton Azure AD](sql-database-aad-authentication-configure.md).
|||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-databases-from-the-application-side"></a>Comment limiter l’accès aux données sensibles de mes bases de données du côté de l’application ? 

Pour empêcher les utilisateurs non autorisés d’afficher les données sensibles, vous disposez de certaines options dans SQL Database : 

- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) est une forme de chiffrement côté client qui permet de chiffrer les colonnes sensibles de votre base de données (elles sont en texte chiffré pour les administrateurs de base de données et les utilisateurs non autorisés). La clé de chiffrement Always Encrypted est stockée côté client, pour permettre uniquement aux clients autorisés de déchiffrer les colonnes sensibles. Always Encrypted prend en charge les comparaisons d’égalité, ce qui permet aux administrateurs de base de données de continuer à interroger les colonnes chiffrées quand ils exécutent des commandes SQL. Vous pouvez utiliser Always Encrypted avec diverses options de magasin de clés, par exemple [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), le magasin de certificats Windows et les modules de sécurité matériels locaux.
- [Dynamic Data Masking](sql-database-dynamic-data-masking-get-started.md) est une fonctionnalité de masquage de données qui limite l’exposition des données sensibles en les masquant aux utilisateurs qui n’ont aucun privilège sur la couche Application. Vous définissez une règle de masquage qui permet de créer un modèle de masquage (par exemple, pour afficher uniquement les 4 derniers chiffres d’une carte d’identité nationale et masquer le reste avec des X) et d’identifier les utilisateurs qui doivent être exclus de la règle de masquage.
- La [sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security) est une fonctionnalité qui vous permet de contrôler l’accès aux lignes d’une table de base de données en fonction de l’utilisateur qui exécute la requête (par exemple, en fonction de son appartenance à un groupe ou un contexte d’exécution). La restriction d’accès s’effectue sur la couche Base de données au lieu de la couche Application, ce qui simplifie votre logique d’application. 

### <a name="what-encryption-options-do-i-have-in-sql-database-and-what-actors-does-the-encryption-protect-from"></a>Quelles sont les options de chiffrement dont je dispose dans SQL Database, et de quoi protège le chiffrement ?
Il existe trois technologies de chiffrement principales dans SQL Database : 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) (technologie mentionnée dans la question ci-dessus) : chiffre les colonnes sensibles de la table, de bout en bout, depuis les clients non autorisés jusqu’au disque physique. Les administrateurs du serveur et des données ne peuvent pas voir les données sensibles, car les clés de chiffrement sont stockées sur le client. 
- [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) : il s’agit d’un chiffrement au repos, qui chiffre au niveau de la base de données et protège les fichiers de données, fichiers journaux et sauvegardes associées contre le vol de supports physiques. TDE est activé par défaut pour toutes les bases de données créées.
 
  Le diagramme suivant montre une vue d’ensemble des choix possibles de technologies de chiffrement.

   ![Vue d’ensemble du chiffrement](./media/sql-database-manage-after-migration/overview-encryption.png)

### <a name="how-should-i-manage-encryption-keys-in-the-cloud"></a>Comment dois-je gérer les clés de chiffrement dans le cloud ? 
Il existe des options relatives à la gestion des clés pour Always Encrypted (chiffrement côté client) et Transparent Data Encryption (chiffrement au repos). Il est recommandé de changer régulièrement les clés de chiffrement à une fréquence calquée sur les règlements internes et les exigences de conformité.

- **Always Encrypted** : il existe une [hiérarchie à deux clés](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) dans Always Encrypted. Une colonne de données sensibles est chiffrée par une clé de chiffrement de colonne AES 256, qui est à son tour chiffrée par une clé principale de colonne. Les pilotes clients fournis pour Always Encrypted n’ont pas de limites concernant la longueur des clés principales de colonne.

  La valeur chiffrée de la clé de chiffrement de colonne est stockée dans la base de données, et la clé principale de colonne est stockée dans un magasin de clés approuvé, par exemple le magasin de certificats Windows, Azure Key Vault ou un module de sécurité matériel. 
  
  Vous pouvez changer [la clé de chiffrement de colonne et la clé principale de colonne](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell). La rotation de la clé de chiffrement de colonne peut prendre beaucoup de temps selon la taille des tables qui contiennent les colonnes chiffrées. Vous devez donc planifier soigneusement les rotations des clés de chiffrement de colonne. La rotation des clés principales de colonne, en revanche, n’interfère pas avec les performances des bases de données. Vous pouvez l’effectuer avec des rôles distincts.

  Le diagramme suivant montre les options relatives au magasin de clés pour les clés principales de colonne dans Always Encrypted 

   ![Fournisseurs de magasins de clés principales de colonne Always Encrypted](./media/sql-database-manage-after-migration/always-encrypted.png)

- **TDE (Transparent Data Encryption)** : il existe une hiérarchie à deux clés dans TDE. Les données de chaque base de données utilisateur sont chiffrées par une clé de chiffrement de base de données AES-256 symétrique (spécifique à la base de données), qui est à son tour chiffrée par une clé principale RSA 2048 asymétrique (spécifique au serveur). 

  Par défaut, la clé principale liée à Transparent Data Encryption est gérée par le service SQL Database pour des raisons pratiques. Si votre organisation souhaite contrôler la clé principale, il est possible d’utiliser [Azure Key Vault](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql) comme magasin de clés. 

  Avec Azure Key Vault, votre organisation contrôle le provisionnement, la rotation et les autorisations spécifiques aux clés. [La rotation ou le changement de type d’une clé principale TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) demande peu de temps, car il suffit de rechiffrer la clé de chiffrement de base de données. 

  Dans les organisations qui appliquent une séparation des rôles entre la gestion de la sécurité et celle des données, l’administrateur de la sécurité peut provisionner le matériel de clé pour la clé principale TDE dans Azure Key Vault, et fournir à l’administrateur de base de données un identificateur de clé Azure Key Vault pour le chiffrement au repos sur un serveur. 

## <a name="monitoring-and-compliance-after-migration"></a>Surveillance et conformité après la migration

### <a name="how-do-i-monitor-database-activities-in-sql-database"></a>Comment surveiller les activités de base de données dans SQL Database ?
Il existe quelques fonctionnalités de surveillance intégrées dans SQL Database. Elles permettent d’effectuer un suivi des événements de sécurité et d’autres événements liés à la base de données :
- L’[audit SQL](sql-database-auditing.md) vous permet de collecter les journaux d’audit des événements de base de données dans votre propre compte de stockage Azure.
- La [détection des menaces SQL](sql-database-threat-detection.md) vous permet de détecter les activités suspectes qui indiquent une tentative malveillante d’accès, de violation ou d’exploitation non autorisée des données de la base de données. La détection des menaces de SQL Database exécute plusieurs ensembles d’algorithmes qui détectent les vulnérabilités potentielles et les attaques par injection de code SQL, ainsi que les modèles anormaux d’accès aux bases de données (par exemple, les accès à partir d’un emplacement inhabituel ou par un principal inconnu). Les responsables sécurité ou les administrateurs désignés reçoivent une notification par e-mail si une menace est détectée sur la base de données. Chaque notification fournit des détails sur l’activité suspecte, ainsi que des recommandations sur l’analyse et l’atténuation de la menace. 
- L’[évaluation des vulnérabilités SQL](sql-vulnerability-assessment.md) est un service d’analyse et de rapport de base de données qui vous permet de surveiller l’état de sécurité de vos bases de données à grande échelle, et d’identifier les risques de sécurité et les dérives par rapport à la base de référence que vous avez définie pour la sécurité. Après chaque analyse, une liste personnalisée d’étapes à entreprendre et des scripts de correction sont proposés, ainsi qu’un rapport d’évaluation qui peut vous aider à répondre aux exigences de conformité. 
- [L’application de synchronisation de la sécurité OMS pour SQL](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) utilise les API publiques OMS (Operations Management Suite) afin d’effectuer l’envoi (push) des journaux d’audit SQL vers OMS pour Log Analytics et définir des alertes de détection personnalisées, notamment : 
 - La vignette et le tableau de bord d’audit SQL Database qui fournissent un rapport clair et cohérent de vos activités de base de données. 
 - SQL Log Analytics, qui permet d’analyser votre activité de base de données et d’examiner les incohérences et anomalies pouvant indiquer des violations de sécurité.
 - Les règles spécifiques aux alertes avancées sur les événements observés qui déclenchent des alertes par e-mail, Webhook et Runbook Azure Automation (par exemple, en cas de changement de mot de passe, d’activité hors des heures habituelles ou d’utilisation de commandes SQL spécifiques).
- [Azure Security Center](../security-center/security-center-intro.md) offre une gestion centralisée de la sécurité pour les charges de travail qui s’exécutent dans Azure, localement et dans d’autres clouds. Vous pouvez voir si les fonctionnalités de protection essentielles de SQL Database, telles que l’audit et Transparent Data Encryption, sont configurées pour toutes les ressources. Si nécessaire, vous pouvez créer des stratégies basées sur vos propres exigences. 

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Est-ce que SQL Database est conforme aux exigences réglementaires et comment cela peut-il répondre aux exigences de conformité de mon organisation ? 
Azure SQL Database est conforme à un certain nombre d’exigences réglementaires. Pour voir quels sont les derniers points de conformité respectés, visitez [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings) et recherchez les points de conformité importants pour votre organisation. Ainsi, vous pourrez déterminer si Azure SQL Database fait partie des services Azure conformes. Il est important de noter que même si Azure SQL Database peut être certifié en tant que service conforme, il contribue à la conformité du service de votre organisation sans représenter pour autant une garantie automatique. 

## <a name="database-maintenance-and-monitoring-after-migration"></a>Maintenance et surveillance de base de données après la migration

### <a name="how-do-i-monitor-growth-in-data-size-and-resource-utilization"></a>Comment surveiller la croissance de la taille des données et l’utilisation des ressources ?

- Vous pouvez afficher les métriques relatives à la taille de votre base de données et à l’utilisation des ressources dans le graphique de « surveillance » du portail Azure. 

  ![Graphique de surveillance](./media/sql-database-manage-after-migration/monitoring-chart.png)

- Pour obtenir un meilleur insight et explorer les requêtes au niveau du détail, vous pouvez utiliser l’outil « Query Performance Insight » disponible sur le portail Azure. Cela nécessite l’activation du « Magasin des requêtes » pour votre base de données.

  ![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

- Vous pouvez également afficher les métriques à l’aide des DMV (vues de gestion dynamique) via [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) et [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database). 

### <a name="how-often-do-i-need-to-run-consistency-checks-like-dbcccheckdb"></a>À quelle fréquence dois-je exécuter les vérifications de cohérence telles que DBCC_CHECKDB ?
DBCC_CHECKDB permet de vérifier l’intégrité logique et physique de tous les objets de la base de données. Vous n’avez plus besoin d’effectuer ces vérifications, car elles sont gérées par Microsoft sur Azure. Pour plus d’informations, consultez [Intégrité des données dans Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="monitor-performance-and-resource-utilization-after-migration"></a>Surveiller le niveau de performance et l’utilisation des ressources après la migration

### <a name="how-do-i-monitor-performance-and-resource-utilization-in-azure-sql-database"></a>Comment surveiller le niveau de performance et l’utilisation des ressources dans Azure SQL Database ?
Vous pouvez surveiller le niveau de performance et l’utilisation des ressources dans Azure SQL Database à l’aide des méthodes suivantes :

- **Portail Azure** : le portail Azure montre l’utilisation d’une base de données unique quand vous la sélectionnez et que vous cliquez sur le graphique du volet Vue d’ensemble. Vous pouvez modifier le graphique pour afficher plusieurs métriques, notamment le pourcentage d’UC, le pourcentage de DTU, le pourcentage d’E/S de données, le pourcentage de sessions et le pourcentage de taille de base de données. 
  ![utilisation des ressources](./media/sql-database-manage-after-migration/resource-utilization.png)

  À partir de ce graphique, vous pouvez également configurer des alertes par ressource. Ces alertes vous permettent de réagir à certaines situations spécifiques aux ressources par l’envoi d’un e-mail, d’écrire sur un point de terminaison HTTPS/HTTP ou d’effectuer une action. Consultez [Analyse des performances d’une base de données dans Azure SQL Database](sql-database-single-database-monitor.md) pour obtenir des instructions détaillées.

- **Vues** : vous pouvez interroger la vue de gestion dynamique [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) pour retourner l’historique des statistiques de consommation des ressources de la dernière heure, et la vue de catalogue système [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) pour retourner l’historique des 14 derniers jours. 

- **Query Performance Insight** : [Query Performance Insight](sql-database-query-performance.md) vous permet de voir l’historique des requêtes les plus consommatrices de ressources et des requêtes les plus longues pour une base de données spécifique. Cette fonctionnalité nécessite l’activation du [Magasin des requêtes](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) pour la base de données.

- **Azure SQL Analytics (préversion) dans Log Analytics** : [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md) vous permet de collecter et de visualiser les métriques de performance clés SQL Azure. Il peut prendre en charge jusqu’à 150 000 bases de données Azure SQL Database et 5 000 pools élastiques SQL par espace de travail. Vous pouvez l’utiliser à des fins de surveillance et pour recevoir des notifications. Vous pouvez surveiller les métriques d’Azure SQL Database et des pools élastiques de plusieurs abonnements Azure. De plus, vous pouvez utiliser les pools élastiques pour identifier les problèmes à chaque couche d’une pile d’applications. 

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-performance-level"></a>Comment vérifier si j’utilise les niveaux de performance et de service appropriés ?
Surveillez les vues de gestion dynamique [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) et [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) pour comprendre comment sont consommées l’UC, les E/S (entrées/sorties) et la mémoire. Vous pouvez également utiliser l’outil [Query Performance Insight](sql-database-query-performance.md) de SQL Database pour afficher la consommation des ressources. Si le pourcentage d’utilisation des ressources disponibles est régulièrement élevé, passez à un niveau de performance supérieur au sein du niveau de service existant, ou passez à un niveau de service supérieur. A l’inverse, si le pourcentage d’utilisation des ressources disponibles est régulièrement faible, passez à un niveau de performance ou un niveau de service moins élevé.

### <a name="i-am-seeing-performance-issues-how-does-my-azure-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Je constate des problèmes de performances. En quoi ma méthodologie de résolution des problèmes dans Azure SQL Database diffère-t-elle de celle de SQL Server ?
De nombreux aspects de votre méthodologie de résolution des problèmes de performances restent les mêmes dans Azure SQL Database. Toutefois, il existe quelques différences. Par exemple, si vous constatez une détérioration des performances globales, surveillez les vues de gestion dynamique [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) et [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) pour comprendre comment sont consommées l’UC, les E/S (entrées/sorties) et la mémoire. Vous devrez peut-être changer le niveau de performance et/ou le niveau de service en fonction des demandes de charge de travail.
Pour obtenir un ensemble complet de recommandations sur les problèmes d’optimisation du niveau de performance, consultez [Optimisation des performances dans Azure SQL Database](sql-database-performance-guidance.md). 

### <a name="do-i-need-to-maintain-indexes-and-statistics"></a>Dois-je assurer la maintenance des index et des statistiques ?
Azure SQL Database n’assure pas la maintenance des index et des statistiques de manière automatique dans le cadre du service. Vous êtes responsable de la planification de la maintenance des index et des statistiques. L’article suivant, Méthodes Azure Automation, décrit en détail plusieurs options pour la planification des tâches de maintenance sur Azure SQL Database.

## <a name="data-movement-after-migration"></a>Mouvement des données après la migration

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-azure-sql-database"></a>Comment exporter et importer des données en tant que fichiers BACPAC à partir d’Azure SQL Database ? 

- **Export** : vous pouvez exporter votre base de données Azure SQL Database en tant que fichier BACPAC à partir du portail Azure.

  ![Exporter en tant que fichier BACPAC](./media/sql-database-export/database-export.png)

- **Import** : vous pouvez importer un fichier BACPAC dans une base de données via le portail Azure.

  ![Importer un fichier BACPAC](./media/sql-database-import/import.png)

### <a name="how-do-i-synchronize-data-between-azure-sql-database-sql-server-2016--2012"></a>Comment synchroniser des données entre Azure SQL Database et SQL Server 2016 / 2012 ?
La fonctionnalité [Data Sync](sql-database-sync-data.md) vous permet de synchroniser les données de façon bidirectionnelle entre plusieurs bases de données SQL Server locales et Azure SQL Database. Toutefois, comme cette opération est basée sur un déclencheur, la cohérence finale est garantie (sans perte de données) mais pas la cohérence des transactions. 

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [Azure SQL Database](sql-database-technical-overview.md).
