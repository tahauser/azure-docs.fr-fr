---
title: Vue d’ensemble du service de base de données relationnelle d’Azure Database pour MySQL
description: Vue d’ensemble du service de base de données relationnelle d’Azure Database pour MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.custom: mvc
ms.openlocfilehash: 3b7ae08446e672c218e7278cff4f77739286fead
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-azure-database-for-mysql"></a>Qu’est-ce qu’Azure Database pour MySQL ?
Azure Database pour MySQL est un service de base de données relationnelle dans le cloud Microsoft qui repose sur le moteur de base de données [MySQL Community Edition](https://www.mysql.com/products/community/). Azure Database pour MySQL offre :

- Une haute disponibilité intégrée sans coût supplémentaire ;
- Des performances prévisibles, grâce aux tarifs du paiement à l’utilisation ;
- Une mise à l’échelle à la volée en quelques secondes ;
- La protection des données sensibles au repos et en mouvement ;
- Des sauvegardes automatiques et une restauration à un point dans le temps jusqu’à 35 jours ;
- Une sécurité et une conformité de classe Entreprise.

Ces fonctionnalités ne demandent pratiquement aucune administration, et toutes sont fournies sans coût supplémentaire. Elles vous permettent de vous concentrer sur le développement rapide de vos applications et d’accélérer leur mise sur le marché, plutôt que de consacrer du temps et des ressources à gérer des machines virtuelles et une infrastructure. Par ailleurs, vous pouvez continuer à développer votre application avec les outils open source et la plateforme de votre choix pour travailler avec la rapidité et l’efficacité exigées par votre activité, et ce sans avoir à acquérir de nouvelles compétences.

![Diagramme conceptuel d’Azure Database pour MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Cet article présente les principaux concepts et fonctionnalités d’Azure Database pour MySQL qui ont trait aux performances, à l’extensibilité et à la facilité de gestion. Il contient également des liens pour en explorer les détails. Consultez ces démarrages rapides pour bien commencer :
- [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Création d’un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Pour accéder à des exemples Azure CLI, consultez :
- [Exemples de CLI Azure pour Azure Database pour MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustez les performances et la mise à l’échelle en quelques secondes
Le service Azure Database pour MySQL offre plusieurs niveaux de service : De base, Usage général et À mémoire optimisée. Chaque niveau offre différentes performances et fonctionnalités pour prendre en charge des charges de travail de base de données plus ou moins denses. Vous pouvez créer votre première application sur une petite base de données pour un faible coût mensuel, puis adapter l’échelle aux besoins de votre solution. L’évolutivité dynamique permet de répondre en toute transparence à l’évolution rapide des besoins en ressources de votre base de données. Vous payez uniquement pour les ressources dont vous avez besoin et seulement quand vous en avez besoin. Pour plus d’informations, consultez [Niveaux tarifaires](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Surveillance et alerte
Comment savoir quand augmenter ou diminuer la taille des instances ? Vous utilisez les fonctionnalités intégrées de surveillance et d’alerte de performances, combinées avec les évaluations de performance basées sur des vCores. Ces outils vous permettent d’évaluer rapidement l’impact des mises à l’échelle des vCores (montées ou descentes en charge) en fonction de vos besoins en performances actuels ou pour un projet. Pour plus d’informations, consultez [Alertes](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Votre application et votre activité ne s’arrêtent jamais
Avec un temps de disponibilité de 99,99 %, l’excellent contrat de niveau de service (SLA) d’Azure, soutenu par un réseau mondial de centres de données gérés par Microsoft, permet d’exécuter votre application 24 heures sur 24, 7 jours sur 7. Avec chaque serveur Azure Database pour MySQL, vous tirez parti de la sécurité intégrée, d’une tolérance en cas de panne et de la protection des données que vous seriez de toute manière contraint d’acheter ou de concevoir, de créer et de gérer. Avec Azure Database pour MySQM, vous pouvez utiliser la limite de restauration dans le temps pour récupérer un serveur à un état antérieur, jusqu’à 35 jours.

## <a name="secure-your-data"></a>Sécurisez vos données
Les services de base de données Azure ont une tradition de sécurité des données qu’Azure Database pour MySQL respecte, avec des fonctionnalités qui limitent l’accès, protègent les données au repos et en mouvement et vous aident à surveiller l’activité. Visitez le [Centre de confidentialité Azure](https://www.microsoft.com/en-us/trustcenter/security) pour plus d’informations sur la sécurité de la plateforme Azure.

Le service Base de données Azure pour MySQL utilise le chiffrement de stockage pour les données au repos. Les données, notamment les sauvegardes, sont chiffrées sur le disque (à l’exception des fichiers temporaires créés par le moteur pendant l’exécution de requêtes). Le service utilise le chiffrement AES 256 bits qui est inclus dans le chiffrement de stockage Azure, et les clés sont gérées par le système. Le chiffrement de stockage est toujours activé et ne peut pas être désactivé.

Par défaut, le service Base de données Azure pour MySQL est configuré afin de requérir la [sécurité de connexion SSL](./concepts-ssl-connection-security.md) pour les données en mouvement sur le réseau. L’application de connexions SSL entre votre serveur de base de données et vos applications clientes vous protège contre les « attaques de l’intercepteur » en chiffrant le flux de données entre le serveur et votre application.  Vous avez la possibilité de désactiver le recours obligatoire au protocole SSL pour la connexion à votre service de base de données si votre application cliente ne prend pas en charge la connectivité SSL.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez lu la présentation d’Azure Database pour MySQL et répondu à la question « Qu’est-ce qu’Azure Database pour MySQL ? », vous êtes prêt à :
- Consultez la page de tarification pour des comparaisons de coûts et des calculatrices. [Tarification](https://azure.microsoft.com/pricing/details/mysql/)
- Commencez par créer votre premier serveur. [Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Générez votre première application à l’aide de votre langage préféré : [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
