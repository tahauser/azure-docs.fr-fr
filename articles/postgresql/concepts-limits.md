---
title: "Limitations dans une base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Décrit les limitations des bases de données Azure pour PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: 6dbed1a834d74047178a9f996683d65520047e66
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limitations des bases de données Azure pour PostgreSQL
Le service de base de données Azure pour PostgreSQL est en préversion publique. Les sections suivantes décrivent les limites fonctionnelles et les limites de capacités du service de base de données.

## <a name="service-tier-maximums"></a>Maximums de niveau de service
La base de données Azure pour PostgreSQL a plusieurs niveaux de service que vous pouvez choisir pour créer un serveur. Pour plus d’informations, consultez la page [Comprendre les éléments disponibles dans chaque niveau de service](concepts-service-tiers.md).  

Chaque niveau de service comporte un nombre maximal de connexions, d’unités de calcul et de stockage dans la préversion du service : 

| | |
| :------------------------- | :---------------- |
| **Nombre maximal de connexions**        |                   |
| 50 unités de calcul de base     | 55 connexions    |
| 100 unités de calcul de base    | 105 connexions   |
| 100 unités de calcul standard | 150 connexions   |
| 200 unités de calcul standard | 250 connexions   |
| 400 unités de calcul standard | 480 connexions   |
| 800 unités de calcul standard | 950 connexions   |
| **Nombre maximal d’unités de calcul**      |                   |
| Niveau de service De base         | 100 unités de calcul |
| Niveau de service Standard      | 800 unités de calcul |
| **Volume maximal de stockage**            |                   |
| Niveau de service De base         | 1 To              |
| Niveau de service Standard      | 1 To              |

Le système Azure a besoin de cinq connexions pour effectuer le monitoring du serveur Azure Database pour PostgreSQL. Au-delà du nombre maximal de connexions, vous risquez de recevoir l’erreur suivante :
> FATAL:  sorry, too many clients already


## <a name="preview-functional-limitations"></a>Limitations fonctionnelles de la préversion
### <a name="scale-operations"></a>Opérations de mise à l’échelle
1.  La mise à l’échelle dynamique de serveurs sur différents niveaux de service n’est pas prise en charge pour le moment. Autrement dit, il n’est pas possible de basculer entre les niveaux de service De base et Standard.
2.  L’augmentation dynamique de la demande de stockage sur un serveur créé au préalable n’est pas prise en charge pour le moment.
3.  La diminution de la taille de stockage du serveur n’est pas prise en charge.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment.

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs créés au préalable entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

### <a name="point-in-time-restore"></a>Restauration dans le temps
1.  La restauration à un autre niveau de service et/ou à une autre taille d’unités de calcul et de stockage n’est pas autorisée.
2.  La restauration d’un serveur supprimé n’est pas prise en charge.

## <a name="next-steps"></a>étapes suivantes
- Comprendre [les éléments disponibles dans chaque niveau tarifaire](concepts-service-tiers.md)
- Comprendre [les versions prises en charge de la base de données PostgreSQL](concepts-supported-versions.md)
- Consulter le [guide pratique : sauvegarder et restaurer un serveur dans une base de données Azure pour PostgreSQL à l’aide du Portail Azure](howto-restore-server-portal.md)
