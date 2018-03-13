---
title: Sauvegarde et restauration dans Azure Database pour PostgreSQL
description: Apprenez-en davantage sur la restauration et les sauvegardes automatiques de votre serveur Azure Database pour PostgreSQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0f7ec38d2c271ebaa15e681a71eb32be7151921f
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="backup-and-restore-in-azure-database-for-postgresql"></a>Sauvegarde et restauration dans Azure Database pour PostgreSQL

Azure Database pour PostgreSQL crée automatiquement des sauvegardes de serveur et les conserve dans un stockage géoredondant ou redondant localement configuré par l’utilisateur. Les sauvegardes peuvent être utilisées pour restaurer votre serveur à un point dans le temps. La sauvegarde et la restauration sont une partie essentielle de toute stratégie de continuité d’activité, dans la mesure où elles protègent vos données des corruptions et des suppressions accidentelles.

## <a name="backups"></a>Sauvegardes

Azure Database pour PostgreSQL accepte les sauvegardes complètes, différentielles et du journal des transactions. Celles-ci vous permettent de restaurer un serveur à n’importe quel point dans le temps au sein de votre période de rétention de sauvegarde configurée. La période de rétention de sauvegarde par défaut est de sept jours. Vous pouvez éventuellement la configurer sur 35 jours maximum. Toutes les sauvegardes sont chiffrées à l’aide du chiffrement AES 256 bits.

### <a name="backup-frequency"></a>Fréquence de sauvegarde

En règle générale, les sauvegardes complètes se produisent une fois par semaine, les sauvegardes différentielles ont lieu deux fois par jour, et les sauvegardes du journal des transactions se déroulent toutes les cinq minutes. La première sauvegarde complète est planifiée immédiatement après la création d’un serveur. La sauvegarde initiale peut prendre plus de temps sur un plus grand serveur restauré. Le point dans le temps le plus ancien vers lequel un nouveau serveur peut être restauré est l’heure à laquelle la sauvegarde complète initiale est complétée.

### <a name="backup-redundancy-options"></a>Options de redondance de sauvegarde

Azure Database pour PostgreSQL offre la possibilité de choisir entre le stockage de sauvegarde géoredondant ou localement redondant dans les niveaux Usage général et À mémoire optimisée. Lorsque les sauvegardes sont conservées dans le stockage de sauvegarde géoredondant, elles ne sont pas uniquement conservées dans la région d’hébergement de votre serveur, mais sont également répliquées dans un [centre de données jumelé](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). Cela permet de bénéficier d’une meilleure protection et de la possibilité de restaurer votre serveur dans une région différente en cas de sinistre. Le niveau De base propose uniquement un stockage de sauvegarde redondant localement.

> [!IMPORTANT]
> La configuration du stockage géoredondant ou redondant localement pour la sauvegarde est uniquement possible lors de la création du serveur. Une fois que le serveur est approvisionné, vous ne pouvez pas modifier l’option de redondance du stockage de sauvegarde.

### <a name="backup-storage-cost"></a>Coût du stockage de sauvegarde

Azure Database pour PostgreSQL fournit jusqu’à 100 % du stockage de serveur approvisionné pour le stockage de sauvegarde sans coût supplémentaire. En règle générale, cela est adapté pour une rétention de sauvegarde de sept jours. Tous les stockages de sauvegarde supplémentaires utilisés sont facturés en Go par mois.

Par exemple, si vous avez approvisionné un serveur de 250 Go, vous bénéficiez de 250 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Tout stockage au-dessus de 250 Go est facturé.

## <a name="restore"></a>Restore

Dans Azure Database pour PostgreSQL, l’exécution d’une restauration crée un serveur à partir de sauvegardes de serveur d’origine.

Deux types de restauration sont disponibles :

- La **restauration à un point dans le temps** est disponible avec l’option de redondance de sauvegarde et crée un serveur dans la même région que votre serveur d’origine.
- La **géorestauration** est disponible uniquement si vous avez configuré votre serveur pour le stockage géoredondant ; elle vous permet de restaurer votre serveur dans une autre région.

Le délai estimé de récupération dépend de plusieurs facteurs, notamment du nombre total de bases de données à récupérer dans la même région au même moment, de la taille des bases de données, de la taille du journal des transactions et de la bande passante réseau. Le délai de récupération est généralement inférieur à 12 heures.

> [!IMPORTANT]
> Si vous supprimez le serveur, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées. Vous ne pouvez pas restaurer un serveur supprimé.

### <a name="point-in-time-restore"></a>Limite de restauration dans le temps

Quelle que soit l’option de redondance de sauvegarde, vous pouvez effectuer une restauration à n’importe quel point dans le temps au sein de votre période de rétention de sauvegarde. Un serveur est créé dans la même région Azure que le serveur d’origine. Il est créé avec la configuration du serveur d’origine pour le niveau de tarification, la génération du calcul, le nombre de vCores, la taille de stockage, la période de rétention de sauvegarde et l’option de redondance de sauvegarde.

La restauration à un point dans le temps est utile dans plusieurs scénarios. Par exemple, lorsqu’un utilisateur supprime accidentellement des données, dépose une base de données ou une table importante ou si une application remplace accidentellement des données correctes par des données erronées en raison d’un défaut de l’application.

Vous devez peut-être attendre la prochaine sauvegarde du journal des transactions avant de pouvoir restaurer à un point dans le temps dans les cinq dernières minutes.

### <a name="geo-restore"></a>Géo-restauration

Vous pouvez restaurer un serveur dans une autre région Azure où le service est disponible si vous avez configuré votre serveur pour les sauvegardes géoredondantes. La géorestauration constitue l’option de récupération par défaut lorsque votre serveur est indisponible en raison d’un incident dans la région où il est hébergé. Si un incident à grande échelle dans une région entraîne l’indisponibilité de votre application de base de données, vous pouvez restaurer un serveur à partir des sauvegardes géoredondantes sur un serveur situé dans n’importe quelle autre région. Il peut y avoir un délai entre le moment où une sauvegarde est effectuée et celui où elle est répliquée dans une autre région. Ce délai peut atteindre une heure. En cas d’incident, il peut donc y avoir jusqu’à une heure de pertes de données.

### <a name="perform-post-restore-tasks"></a>Effectuer des tâches de post-restauration

Après une restauration à l’aide d’un de ces mécanismes de récupération, vous devez effectuer les tâches suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Si le nouveau serveur est destiné à remplacer le serveur d’origine, redirigez les clients et les applications clientes vers le nouveau serveur
- Vérifiez que les règles de pare-feu appropriées au niveau du serveur sont en place pour permettre aux utilisateurs de se connecter
- Assurez-vous que les connexions et les autorisations appropriées au niveau de la base de données sont en place
- Configurer les alertes, selon les besoins

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la continuité d’activité, consultez la  [vue d’ensemble de la continuité d’activité](concepts-business-continuity.md).
- Pour effectuer une restauration à un point dans le temps à l’aide du portail Azure, consultez  [Restauration d’une base de données à un point dans le temps à l’aide du portail Azure](howto-restore-server-portal.md).
- Pour effectuer une restauration à un point dans le temps à l’aide de l’interface de ligne de commande Azure, consultez  [Restaurer une base de données à un point dans le temps à l’aide de l’interface CLI](howto-restore-server-cli.md).