---
title: "Bienvenue dans l’application Wingtips - Azure SQL Database | Microsoft Docs"
description: "Obtenez des informations sur les modèles de client de base de données, ainsi que l’exemple d’application Wingtips SaaS pour Azure SQL Database dans l’environnement cloud."
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Bienvenue dans l’exemple d’application client Azure SQL Database Wingtip Tickets SaaS

Bienvenue dans l’exemple d’application client Azure SQL Database Wingtip Tickets SaaS et ses didacticiels. Le terme client de base de données fait référence au mode d’isolation des données que votre application fournit à vos clients, qui paient pour être hébergés dans votre application. Pour simplifier les choses pour l’instant, soit chaque client dispose d’une base de données entière pour lui seul soit il partage une base de données avec d’autres clients.

## <a name="wingtip-tickets-app"></a>L’application Wingtip Tickets

L’exemple d’application Wingtip Tickets illustre les effets des différents modèles de client de base de données pour la conception et la gestion des applications SaaS mutualisées. Les didacticiels qui l’accompagnent décrivent directement ces mêmes effets. Wingtip Tickets repose sur Azure SQL Database.

Wingtip Tickets est conçu pour gérer les différents scénarios de conception et de gestion qui sont utilisés par de vrais clients SaaS. Les modèles d’utilisation qui en émergent sont pris en compte pour Wingtip Tickets.

Vous pouvez installer l’application Wingtip Tickets dans votre propre abonnement Azure en moins de cinq minutes. L’installation inclut des exemples de données pour plusieurs clients. Vous pouvez installer l’application et les scripts de gestion pour tous les modèles, car les installations ne peuvent pas interagir ou interférer entre elles.

#### <a name="code-in-github"></a>Le code dans Github

Le code de l’application et les scripts de gestion sont disponibles sur GitHub :

- Modèle **Application autonome** : [référentiel WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
- Modèle **Une base de données par client** : [référentiel WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
- Modèle **Base de données mutualisée partitionnée** : [référentiel WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB).

La même base de code pour l’application Wingtip Tickets est réutilisée pour tous les modèles répertoriés précédemment. Vous pouvez utiliser le code à partir de Github pour démarrer vos propres projets SaaS.



## <a name="major-database-tenancy-models"></a>Principaux modèles de base de données principale

Wingtip Tickets est une application SaaS répertoriant des événements et gérant la vente de tickets. Wingtip fournit des services qui sont nécessaires aux lieux de vente. Tous les éléments suivants s’appliquent à chaque emplacement :

- Paie pour être hébergé dans votre application.
- Est un *client* dans Wingtip.
- Accueille des événements. Les événements suivants sont impliqués :
    - Le prix des tickets.
    - La vente des tickets.
    - Les clients qui achètent des tickets.

L’application, ainsi que les scripts de gestion et les didacticiels, présentent un scénario complet de SaaS. Ce scénario inclut les activités suivantes :

- Configuration de clients.
- Surveillance et gestion des performances.
- Gestion des schémas.
- Création de rapports et d’analyses entre clients.

Toutes ces activités sont fournies à l’échelle nécessaire.



## <a name="code-samples-for-each-tenancy-model"></a>Exemples de code pour chaque modèle de client

Un ensemble de modèles d’application sont mis en évidence. Toutefois, les autres implémentations peuvent mélanger les éléments de deux modèles ou plus.

#### <a name="standalone-app-model"></a>Modèle d’application autonome

![Modèle d’application autonome][standalone-app-model-62s]

Ce modèle utilise une application à client unique. Par conséquent, ce modèle ne nécessite qu’une seule base de données, et stocke des données pour un seul client. Le client bénéficie d’une isolation totale par rapport aux autres clients dans la base de données.

Vous pouvez utiliser ce modèle lorsque vous vendez des instances de votre application à de nombreux clients, pour que chaque client opère sur son propre espace. Il est ainsi le seul client. Alors que la base de données stocke des données pour un seul client, elle stocke les données de nombreux clients du client.

#### <a name="database-per-tenant"></a>Base de données par client

![Modèle de base de données par client][database-per-tenant-model-35d]

Ce modèle possède plusieurs clients dans l’instance de l’application. Pour chaque nouveau client, une autre base de données est allouée pour une utilisation uniquement par le nouveau client.

Ce modèle offre une isolation de la base de données complète pour chaque client. Le service Azure SQL Database est assez sophistiqué pour rendre ce modèle plausible.

- [Présentation d’un exemple d’application SaaS mutualisée SQL Database][saas-dbpertenant-wingtip-app-overview-15d] - contient plus d’informations sur ce modèle.

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>Bases de données mutualisées partitionnées, le modèle hybride

![Bases de données mutualisées partitionnées, le modèle hybride][sharded-multitenantdb-model-hybrid-79m]

Ce modèle possède plusieurs clients dans l’instance de l’application. Ce modèle possède également plusieurs clients dans tout ou partie de ses bases de données. Ce modèle est adapté pour offrir différents niveaux de service afin que les clients puissent payer plus s’ils souhaitent bénéficier d’une isolation complète de la base de données.

Le schéma de chaque base de données inclut un identificateur de client. L’identificateur de client est présent même dans les bases de données qui ne stockent qu’un seul client.

- [Présentation d’un exemple d’application SaaS mutualisée SQL Database][saas-multitenantdb-get-started-deploy-89i]



## <a name="tutorials-for-each-tenancy-model"></a>Didacticiels pour chaque modèle de client

Chaque modèle de client est documenté par ce qui suit :

- Un ensemble d’articles de didacticiel.
- Un code source stocké dans un référentiel Github dédié au modèle :
    - Le code de l’application Wingtip Tickets.
    - Le code de script pour les scénarios de gestion.

#### <a name="tutorials-for-management-scenarios"></a>Didacticiels pour les scénarios de gestion

Des articles de didacticiel pour chaque modèle couvrant les scénarios de gestion suivants :

- Approvisionnement client.
- Surveillance et gestion des performances.
- Gestion des schémas.
- Création de rapports et d’analyses entre clients.
- Restauration d’un client à un point antérieur dans le temps.
- Récupération d’urgence.



## <a name="next-steps"></a>Étapes suivantes

- [Présentation d’un exemple d’application SaaS mutualisée SQL Database][saas-dbpertenant-wingtip-app-overview-15d] - contient plus d’informations sur ce modèle.

- [Modèles de client de base de données SaaS mutualisée][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "Modèle d’application autonome"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "Modèle de base de données par client"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "Bases de données mutualisées partitionnées, le modèle hybride"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


