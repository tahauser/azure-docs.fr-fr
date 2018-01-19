---
title: "Limitations dans une base de données Azure pour MySQL | Microsoft Docs"
description: "Décrit les limitations de la préversion des bases de données Azure pour MySQL."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/11/2018
ms.openlocfilehash: f0f9a10f987f19d8ae77a07038cffe23446856fd
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitations dans Azure Database pour MySQL
Le service de base de données Azure pour MySQL est en préversion publique. Les sections suivantes abordent la capacité, la prise en charge du moteur de stockage, la prise en charge des privilèges, la prise en charge des instructions de manipulation des données et les limites fonctionnelles du service de base de données. Vous pouvez aussi consulter les [limitations générales](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) qui sont applicables au moteur de base de données MySQL.

## <a name="service-tier-maximums"></a>Valeurs maximales des niveaux de service
Azure Database pour MySQL vous permet de choisir entre plusieurs niveaux de service lorsque vous créez un serveur. Pour plus d’informations, consultez la page [Comprendre les éléments disponibles dans chaque niveau de service](concepts-service-tiers.md).  

Chaque niveau de service dans la préversion comporte un nombre maximal de connexions et d’unités de calcul, ainsi qu’un espace maximal de stockage, comme suit : 

|                            |                   |
| :------------------------- | :---------------- |
| **Nombre maximal de connexions**        |                   |
| 50 unités de calcul de base     | 50 connexions    |
| 100 unités de calcul de base    | 100 connexions   |
| 100 unités de calcul standard | 200 connexions   |
| 200 unités de calcul standard | 400 connexions   |
| 400 unités de calcul standard | 800 connexions   |
| 800 unités de calcul standard | 1 600 connexions  |
| **Nombre maximal d’unités de calcul**      |                   |
| Niveau de service De base         | 100 unités de calcul |
| Niveau de service Standard      | 800 unités de calcul |
| **Volume maximal de stockage**            |                   |
| Niveau de service De base         | 1 To              |
| Niveau de service Standard      | 1 To              |

Au-delà du nombre maximal de connexions, vous risquez de recevoir l’erreur suivante :
> ERROR 1040 (08004): Too many connections

## <a name="storage-engine-support"></a>Prise en charge du moteur de stockage

### <a name="supported"></a>Prise en charge
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Non pris en charge
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Prise en charge des privilèges

### <a name="unsupported"></a>Non pris en charge
- Les paramètres de serveur du rôle Administrateur de bases de données peuvent dégrader les performances du serveur ou nier les propriétés ACID du système de gestion de base de données (SGBD). Par conséquent, pour maintenir l’intégrité de notre service et du contrat SLA au niveau du produit, nous ne permettons pas aux clients d’accéder au rôle Administrateur de bases de données. Le compte d’utilisateur par défaut, qui est créé en même temps qu’une instance de base de données, permet aux clients d’exécuter la plupart des instructions DDL et DML dans l’instance de base de données gérée. 
- De la même façon, les [privilèges de super utilisateur](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) sont eux aussi limités.

## <a name="data-manipulation-statement-support"></a>Prise en charge des instructions de manipulation des données

### <a name="supported"></a>Prise en charge
- LOAD DATA INFILE - Pris en charge, mais doit spécifier le paramètre [LOCAL] qui est dirigé vers un chemin UNC (stockage Azure monté via XSMB).

### <a name="unsupported"></a>Non pris en charge
- SELECT ... INTO OUTFILE

## <a name="preview-functional-limitations"></a>Limitations fonctionnelles de la préversion

### <a name="scale-operations"></a>Opérations de mise à l’échelle
- La mise à l’échelle dynamique de serveurs sur différents niveaux de service n’est pas prise en charge pour le moment. Autrement dit, il n’est pas possible de basculer entre les niveaux de service De base et Standard.
- L’augmentation dynamique de la demande de stockage sur un serveur créé au préalable n’est pas prise en charge pour le moment.
- La diminution de la taille de stockage du serveur n’est pas prise en charge.

### <a name="server-version-upgrades"></a>Mises à niveau de la version du serveur
- La migration automatique entre les versions principales du moteur de base de données n’est pas prise en charge pour le moment.

### <a name="point-in-time-restore"></a>Restauration dans le temps
- La restauration à un autre niveau de service et/ou à une autre taille d’unités de calcul et de stockage n’est pas autorisée.
- La restauration d’un serveur supprimé n’est pas prise en charge.

## <a name="functional-limitations"></a>Limitations fonctionnelles

### <a name="subscription-management"></a>Gestion des abonnements
- Le déplacement dynamique de serveurs créés au préalable entre les groupes de ressources et d’abonnements n’est pas pris en charge pour le moment.

## <a name="current-known-issues"></a>Problèmes connus
- L’instance de serveur MySQL affiche la mauvaise version de serveur une fois la connexion établie. Pour obtenir la bonne version de l’instance de serveur, utilisez la commande select version(); dans l’invite MySQL.

## <a name="next-steps"></a>étapes suivantes
- [Éléments disponibles dans chaque niveau de service](concepts-service-tiers.md)
- [Versions de base de données MySQL prises en charge](concepts-supported-versions.md)
