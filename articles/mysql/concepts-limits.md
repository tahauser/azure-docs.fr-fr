---
title: Limitations dans Azure Database pour MySQL
description: Cet article décrit les limitations dans Azure Database pour MySQL, telles que le nombre de connexions et les options du moteur de stockage.
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: 2fa69182b4238cfd19fcc9571e4327512e9528c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitations dans Azure Database pour MySQL
Les sections suivantes abordent la capacité, la prise en charge du moteur de stockage, la prise en charge des privilèges, la prise en charge des instructions de manipulation des données et les limites fonctionnelles du service de base de données. Vous pouvez aussi consulter les [limitations générales](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) qui sont applicables au moteur de base de données MySQL.

## <a name="service-tier-maximums"></a>Valeurs maximales des niveaux de service
Azure Database pour MySQL vous permet de choisir entre plusieurs niveaux de service lorsque vous créez un serveur. Pour plus d’informations, consultez [Niveaux tarifaires dans Azure Database pour MySQL](concepts-pricing-tiers.md).  

Chaque niveau de service offre un nombre maximal de connexions et d’unités compute, ainsi qu’un maximum d’espace de stockage, comme suit : 

|**Niveau tarifaire**| **Génération de calcul**|**vCore(s)**| **Nombre maximal de connexions**|
|---|---|---|---|
|De base| Gen 4| 1| 50|
|De base| Gen 4| 2| 100|
|De base| Gen 5| 1| 50|
|De base| Gen 5| 2| 100|
|Usage général| Gen 4| 2| 300|
|Usage général| Gen 4| 4| 625|
|Usage général| Gen 4| 8| 1250|
|Usage général| Gen 4| 16| 2 500|
|Usage général| Gen 4| 32| 5 000|
|Usage général| Gen 5| 2| 300|
|Usage général| Gen 5| 4| 625|
|Usage général| Gen 5| 8| 1250|
|Usage général| Gen 5| 16| 2 500|
|Usage général| Gen 5| 32| 5 000|
|Mémoire optimisée| Gen 5| 2| 600|
|Mémoire optimisée| Gen 5| 4| 1250|
|Mémoire optimisée| Gen 5| 8| 2 500|
|Mémoire optimisée| Gen 5| 16| 5 000|

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
- Rôle d’administrateur de bases de données : plusieurs paramètres de server peuvent dégrader les performances du serveur ou nier les propriétés ACID du système de gestion de base de données (SGBD). Par conséquent, pour préserver l’intégrité du service et le contrat SLA au niveau du produit, ce service n’expose pas le rôle d’administrateur de bases de données. Le compte d’utilisateur par défaut, qui est créé en même temps qu’une instance de base de données, permet à l’utilisateur d’exécuter la plupart des instructions DDL et DML dans l’instance de base de données gérée. 
- Privilèges de super utilisateur : de la même façon, les [privilèges de super utilisateur](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) sont eux aussi limités.

## <a name="data-manipulation-statement-support"></a>Prise en charge des instructions de manipulation des données

### <a name="supported"></a>Prise en charge
- LOAD DATA INFILE - Pris en charge, mais doit spécifier le paramètre [LOCAL] qui est dirigé vers un chemin UNC (stockage Azure monté via XSMB).

### <a name="unsupported"></a>Non pris en charge
- SELECT ... INTO OUTFILE

## <a name="functional-limitations"></a>Limitations fonctionnelles

### <a name="scale-operations"></a>Opérations de mise à l’échelle
- La mise à l’échelle dynamique des serveurs dans les différents niveaux tarifaires n’est pas prise en charge pour le moment. Autrement dit, le basculement entre les niveaux de prix De base, Usage général et Mémoire optimisée.
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

## <a name="next-steps"></a>Étapes suivantes
- [Éléments disponibles dans chaque niveau de service](concepts-pricing-tiers.md)
- [Versions de base de données MySQL prises en charge](concepts-supported-versions.md)
