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
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: 094189e08002ce8d4a2f4f92a8c112eaf18ebe13
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="the-wingtip-tickets-saas-application"></a>Application SaaS Wingtip Tickets

La même application *Wingtip Tickets* est implémentée dans chacun des trois exemples. L’application est une simple application SaaS de réservation et de liste d’événements ciblant les lieux de petite taille (cinémas, clubs, etc.). Chaque lieu correspond à un locataire de l’application et dispose de ses propres données : détails sur le lieu, listes d’événements, clients, commandes de tickets, etc.  L’application, ainsi que les scripts de gestion et les didacticiels, présentent un scénario SaaS de bout en bout. Celui-ci inclut le provisionnement des locataires, le monitoring et la gestion des performances, la gestion des schémas et les rapports et analytiques entre locataires.

## <a name="three-saas-application-patterns"></a>3 modèles d’application SaaS

Trois versions de l’application sont disponibles, chacune explorant un modèle différent d’architecture mutualisée de base de données sur Azure SQL Database.  La première utilise une application à locataire unique avec une base de données mono-locataire isolée. La deuxième utilise une application multi-locataire avec une base de données par locataire. La troisième utilise une application multi-locataire avec des bases de données multi-locataires partitionnées.

![Trois modèles d’architecture mutualisée][image-three-tenancy-patterns]

 Chaque exemple inclut des scripts et didacticiels qui explorent plusieurs modèles de conception et de gestion que vous pouvez utiliser dans votre propre application.  Chaque exemple se déploie en moins de cinq minutes.  Les trois exemples peuvent être déployés côte à côte. Vous pouvez alors comparer les différences de conception et de gestion.

## <a name="standalone-application-pattern"></a>Modèle d’application autonome

Le modèle d’application autonome utilise une application à locataire unique avec une base de données mono-locataire pour chaque locataire. Chaque application du locataire est déployée dans un groupe de ressources Azure distinct. Ce dernier peut se trouver dans l’abonnement du fournisseur de services ou dans celui du locataire qui est alors géré par le fournisseur au nom du locataire. Ce modèle fournit l’isolation des locataires la plus grande, mais il est généralement le plus onéreux, car il n’existe aucune possibilité de partager des ressources entre plusieurs locataires.

Consultez les [didacticiels][docs-tutorials-for-wingtip-sa] et le code sur GitHub  [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Modèle avec une base de données par locataire

Le modèle de base de données par locataire est efficace pour les fournisseurs de services qui sont concernés par l’isolation des locataires et souhaitent exécuter un service centralisé qui permet une utilisation rentable de ressources partagées. Une base de données est créée pour chaque locataire ou lieu, et toutes les bases de données sont gérées centralement. Les bases de données peuvent être hébergées dans des pools élastiques pour fournir une gestion des performances rentable et facile, qui tire parti des modèles de charge de travail imprévisibles des locataires. Une base de données de catalogue contient le mappage entre les locataires et leurs bases de données. Ce mappage est géré à l’aide des fonctionnalités de gestion de carte de partitions de la [bibliothèque cliente de base de données élastique](sql-database-elastic-database-client-library.md), ce qui garantit une gestion efficace des connexions à l’application.

Consultez les [didacticiels][docs-tutorials-for-wingtip-dpt] et le code sur GitHub  [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Modèle de base de données multi-locataire partitionnée

Les bases de données multi-locataires sont efficaces pour les fournisseurs de services qui veulent un coût inférieur par locataire et n’ont besoin d’une forte isolation des locataires. Ce modèle permet la compression de gros volumes de locataires dans une seule base de données, et ainsi de réduire le coût par locataire. Une échelle presque infinie est possible en partitionnant les locataires entre plusieurs bases de données.  Une base de données de catalogue mappe à nouveau les locataires aux bases de données.  

Ce modèle autorise également un modèle hybride dans lequel vous pouvez optimiser les coûts avec plusieurs locataires dans une base de données, ou optimiser l’isolation avec un seul locataire dans sa propre base de données. Le choix peut être effectué locataire par locataire, soit lorsque le locataire est provisionné, soit ultérieurement, sans aucun impact sur l’application.

Consultez les [didacticiels][docs-tutorials-for-wingtip-mt] et le code sur GitHub  [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Étapes suivantes

#### <a name="conceptual-descriptions"></a>Descriptions conceptuelles

- Une explication plus détaillée des modèles d’architecture mutualisée d’application est disponible à l’adresse [Modèles de location de base de données SaaS multi-locataire][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Didacticiels et code

- Application autonome :
    - [Didacticiels pour une application autonome][docs-tutorials-for-wingtip-sa].
    - [Code pour une application autonome, sur Github][github-code-for-wingtip-sa].

- Base de données par locataire :
    - [Didacticiels pour les bases de données par locataire][docs-tutorials-for-wingtip-dpt].
    - [Code pour la base de données par locataire, sur GitHub][github-code-for-wingtip-dpt].

- Multi-locataire partitionné :
    - [Didacticiels pour un multi-locataire partitionné][docs-tutorials-for-wingtip-mt].
    - [Code pour un multi-locataire partitionné, sur GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Trois modèles d’architecture mutualisée."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

