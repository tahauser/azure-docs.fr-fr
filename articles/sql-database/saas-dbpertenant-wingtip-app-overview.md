---
title: "Exemple d’application mutualisée Azure SQL Database - SaaS Wingtip | Microsoft Docs"
description: "Apprendre à l’aide d’un exemple d’application mutualisée utilisant l’exemple Azure SQL Database - SaaS Wingtip"
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 2871d2b1208013808958e8a5b0c62fce31af86ec
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Présentation d’une application SaaS mutualisée qui utilise le modèle de base de données par locataire avec SQL Database

L’application *Wingtip SaaS* est un exemple d’application mutualisée. L’application utilise une base de données par locataire, le modèle d’application SaaS, pour traiter plusieurs locataires. L’application présente les fonctionnalités d’Azure SQL Database qui activent des scénarios SaaS, à l’aide de plusieurs modèles de gestion et de conception SaaS. Pour que vous soyez rapidement opérationnel, l’application SaaS Wingtip se déploie en moins de cinq minutes.

Le code source de l’application et les scripts de gestion sont disponibles dans le référentiel GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Avant de commencer, consultez les [conseils généraux](saas-tenancy-wingtip-app-guidance-tips.md) pour télécharger et débloquer les scripts de gestion Wingtip Tickets.

## <a name="application-architecture"></a>Architecture de l'application

L’application SaaS Wingtip utilise le modèle de base de données par client, ainsi que des pools élastiques SQL pour optimiser l’efficacité. Pour l’approvisionnement et le mappage des clients à leurs données, une base de données catalogue est utilisée. L’application SaaS Wingtip centrale utilise un pool avec trois exemples de clients, plus la base de données catalogue. Suivre un grand nombre de didacticiels SaaS Wingtip entraîne l’ajout de modules complémentaires au déploiement initial, en raison de l’introduction de bases de données analytiques, de la gestion des schémas des bases de données, etc.


![Architecture de SaaS Wingtip](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


Lorsque vous suivez les didacticiels et utilisez l’application, il est important que vous vous concentriez sur les modèles SaaS liés à la couche données. En d’autres termes, concentrez-vous sur le niveau de données et n’analysez pas trop l’application en elle-même. La compréhension de l’implémentation de ces modèles SaaS est essentielle pour implémenter ces modèles dans vos applications, tout en prenant en compte toutes les modifications nécessaires pour vos besoins métier spécifiques.

## <a name="sql-database-wingtip-saas-tutorials"></a>Didacticiels de l’application SaaS Wingtip de SQL Database

Après avoir déployé l’application, explorez les didacticiels suivants qui se basent sur le déploiement initial. Ces didacticiels explorent des modèles SaaS courants qui tirent parti des fonctionnalités intégrées de SQL Database, de SQL Data Warehouse et d’autres services Azure. Les didacticiels incluent des scripts PowerShell, avec des explications détaillées qui simplifient considérablement la compréhension et l’implémentation des mêmes modèles de gestion SaaS dans vos applications.


| Didacticiel | Description |
|:--|:--|
| [Instructions et conseils pour l’exemple d’application de base de données SQL Azure mutualisée SaaS](saas-tenancy-wingtip-app-guidance-tips.md) | **COMMENCEZ ICI** Téléchargez et exécutez les scripts PowerShell permettant de préparer des parties de l’application. |
|[Déployer et explorer l’application SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)|  Déployez et explorez l’application SaaS Wingtip dans votre abonnement Azure. |
|[Approvisionner des clients et les inscrire dans le catalogue](saas-dbpertenant-provision-and-catalog.md)| Découvrez comment l’application se connecte aux clients à l’aide d’une base de données catalogue, et comment le catalogue mappe les clients à leurs données. |
|[Surveiller et gérer les performances](saas-dbpertenant-performance-monitoring.md)| Découvrez comment utiliser les fonctionnalités de surveillance de SQL Database, et définir des alertes qui se déclenchent en cas de dépassement des seuils de performances. |
|[Surveiller avec Log Analytics (OMS)](saas-dbpertenant-log-analytics.md) | Apprenez à utiliser [Log Analytics](../log-analytics/log-analytics-overview.md) pour surveiller de grandes quantités de ressources dans plusieurs pools. |
|[Restaurer un client unique](saas-dbpertenant-restore-single-tenant.md)| Découvrez comment restaurer une base de données client à un point antérieur dans le temps. Les étapes permettant de restaurer une base de données parallèle, en laissant la base de données client en ligne, sont également incluses. |
|[Gérer le schéma de base de données client](saas-tenancy-schema-management.md)| Découvrez comment mettre à jour un schéma et des données de référence sur toutes les bases de données de locataire. |
|[Exécuter des requêtes distribuées entre locataires](saas-tenancy-cross-tenant-reporting.md) | Créez une base de données analytique ad hoc, puis exécutez des requêtes distribuées en temps réel sur tous les clients.  |
|[Exécuter l’analytique sur les données client extraites](saas-tenancy-tenant-analytics.md) | Extrayez les données client dans une base de données analytique ou un entrepôt de données pour les requêtes analytiques en mode hors connexion. |


## <a name="next-steps"></a>Étapes suivantes

- [Conseils généraux et astuces relatifs au déploiement et à l’utilisation de l’exemple d’application SaaS Wingtip Tickets](saas-tenancy-wingtip-app-guidance-tips.md)

- [Déployer l’application SaaS Wingtip](saas-dbpertenant-get-started-deploy.md)
