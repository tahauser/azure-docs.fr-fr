---
title: "Limitations des bases de données Azure pour PostgreSQL"
description: "Cet article décrit les limitations dans Azure Database pour PostgreSQL, telles que le nombre de connexions et les options du moteur de stockage."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ba05308039e9743dd207333476e61a45c0ca166a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Limitations des bases de données Azure pour PostgreSQL
Le service de base de données Azure pour PostgreSQL est en préversion publique. Les sections suivantes décrivent les limites fonctionnelles et les limites de capacités du service de base de données.

## <a name="pricing-tier-maximums"></a>Valeurs maximales des niveaux tarifaires
Azure Database pour PostgreSQL a plusieurs niveaux tarifaires que vous pouvez choisir pour créer un serveur. Pour plus d’informations, consultez [Niveaux tarifaires dans Azure Database pour PostgreSQL](concepts-pricing-tiers.md).  

Chaque niveau tarifaire comporte un nombre maximal de connexions et d’unités de calcul ainsi qu’un espace maximal de stockage, comme suit : 

|Niveau de tarification| Génération de calcul| vCore(s)| Nombre maximal de connexions |
|---|---|---|---|
|De base| Gen 4| 1| 50 |
|De base| Gen 4| 2| 100 |
|De base| Gen 5| 1| 50 |
|De base| Gen 5| 2| 100 |
|Usage général| Gen 4| 2| 150|
|Usage général| Gen 4| 4| 250|
|Usage général| Gen 4| 8| 480|
|Usage général| Gen 4| 16| 950|
|Usage général| Gen 4| 32| 1 500|
|Usage général| Gen 5| 2| 150|
|Usage général| Gen 5| 4| 250|
|Usage général| Gen 5| 8| 480|
|Usage général| Gen 5| 16| 950|
|Usage général| Gen 5| 32| 1 500|
|Mémoire optimisée| Gen 5| 2| 150|
|Mémoire optimisée| Gen 5| 4| 250|
|Mémoire optimisée| Gen 5| 8| 480|
|Mémoire optimisée| Gen 5| 16| 950|
|Mémoire optimisée| Gen 5| 32| 1900|

Lorsque la limite du nombre de connexions est dépassée, vous pouvez recevoir l’erreur suivante :
> FATAL:  sorry, too many clients already

Le système Azure a besoin de cinq connexions pour effectuer le monitoring du serveur Azure Database pour PostgreSQL. 

## <a name="functional-limitations"></a>Limitations fonctionnelles
### <a name="scale-operations"></a>Opérations de mise à l’échelle
1.  La mise à l’échelle dynamique des serveurs dans les différents niveaux tarifaires n’est pas prise en charge pour le moment. Autrement dit, cela concerne le basculement entre les niveaux De base, Usage général et Mémoire optimisée.
2.  La diminution de la taille de stockage du serveur n’est pas prise en charge pour le moment.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment.

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

### <a name="point-in-time-restore-pitr"></a>Point-in-time-restore (PITR)
1.  Lorsque vous utilisez la fonctionnalité PITR, le nouveau serveur est créé avec la même configuration que le serveur sur lequel il est basé.
2.  La restauration d’un serveur supprimé n’est pas prise en charge.

## <a name="next-steps"></a>Étapes suivantes
- Comprendre [les éléments disponibles dans chaque niveau tarifaire](concepts-pricing-tiers.md)
- Découvrir [les versions prises en charge de la base de données PostgreSQL](concepts-supported-versions.md)
- Consulter le [guide pratique : sauvegarder et restaurer un serveur dans Azure Database pour PostgreSQL à l’aide du portail Azure](howto-restore-server-portal.md)
