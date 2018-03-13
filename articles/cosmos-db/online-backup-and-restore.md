---
title: Sauvegarde et restauration en ligne avec Azure Cosmos DB | Microsoft Docs
description: "Découvrez comment effectuer des sauvegardes et des restaurations automatiques sur une base de données Azure Cosmos DB."
keywords: sauvegarde et restauration, sauvegarde en ligne
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: f88bdd6ffb70ccd2aa48453747964c4afb5bea46
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Sauvegarde et restauration en ligne automatiques avec Azure Cosmos DB
Azure Cosmos DB sauvegarde automatiquement toutes vos données à intervalles réguliers. Les sauvegardes automatiques sont effectuées sans affecter les performances ou la disponibilité de vos opérations de base de données. Toutes vos sauvegardes sont stockées séparément dans un autre service de stockage, et ces sauvegardes sont répliquées globalement pour garantir la résilience contre les sinistres régionaux. Les sauvegardes automatiques sont destinées aux scénarios où vous supprimez accidentellement votre conteneur Cosmos DB et où vous avez besoin ultérieurement d’une solution de récupération de données ou de récupération d’urgence.  

Cet article commence par un récapitulatif sur la redondance et la disponibilité des données dans Cosmos DB et traite ensuite des sauvegardes. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Récapitulatif de la haute disponibilité avec Cosmos DB
Cosmos DB est conçu pour être [globalement distribué](distribute-data-globally.md) : il vous permet de mettre à l’échelle le débit dans plusieurs régions Azure, ainsi que le basculement indiqué par la stratégie et les API multihébergement transparentes. Azure Cosmos DB propose un [contrat SLA avec une disponibilité à 99,99 %](https://azure.microsoft.com/support/legal/sla/cosmos-db) pour tous les comptes à une ou plusieurs régions avec cohérence souple, ainsi qu’une disponibilité de lecture à 99,999 % pour tous les comptes de base de données à plusieurs régions. Toutes les écritures effectuées dans Azure Cosmos DB sont validées durablement sur des disques locaux par un quorum de réplicas au sein d’un centre de données local, avant d’accuser réception au client. Notez que la haute disponibilité de Cosmos DB s’appuie sur le stockage local et ne dépend d’aucune technologie de stockage externe. En outre, si votre compte de base de données est associé à plusieurs régions Azure, vos écritures sont également répliquées entre les autres régions. Pour mettre à l’échelle votre débit, et accéder aux données à une latence faible, vous pouvez avoir autant de régions lues associées à votre compte de base de données que vous le souhaitez. Dans chaque région de lecture, les données (répliquées) sont rendues persistantes durablement sur un jeu de réplicas.  

Comme illustré dans le schéma suivant, un seul conteneur Cosmos DB est [partitionné horizontalement](partition-data.md). Une « partition » est indiquée par un cercle dans le diagramme suivant, et chaque partition est hautement disponible via un jeu de réplicas. Il s’agit de la distribution locale au sein d’une seule région Azure (indiquée par l’axe des abscisses). En outre, chaque partition (avec son jeu de réplicas correspondant) est ensuite globalement distribuée dans plusieurs régions liées à votre compte de base de données (par exemple, dans cette illustration, les trois régions : États-Unis de l’Est, États-Unis de l’Ouest et Centre de l’Inde). Le « jeu de partitions » est une entité globalement distribuée comprenant plusieurs copies de vos données dans chaque région (indiquée par l’axe des ordonnées). Vous pouvez affecter la priorité aux régions associées à votre compte de base de données ; Cosmos DB basculera en toute transparence à la région suivante en cas de sinistre. Vous pouvez également simuler le basculement manuellement pour tester la disponibilité de bout en bout de votre application.  

L’image suivante illustre le degré élevé de redondance avec Cosmos DB.

![Degré élevé de redondance avec Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Degré élevé de redondance avec Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Sauvegardes complètes, automatiques, en ligne
Oups, j’ai supprimé mon conteneur ou ma base de données ! Avec Cosmos DB, vos données, ainsi que les sauvegardes de vos données sont rendues hautement redondantes et résilientes aux sinistres régionaux. Actuellement, ces sauvegardes automatisées sont effectuées environ toutes les quatre heures et les deux sauvegardes les plus récentes sont stockées en permanence. Si les données sont accidentellement supprimées ou endommagées, contactez le [support technique Azure](https://azure.microsoft.com/support/options/) dans les huit heures. 

Les sauvegardes sont effectuées sans affecter les performances ou la disponibilité de vos opérations de base de données. Cosmos DB effectue la sauvegarde en arrière-plan sans consommer les RU approvisionnées ni affecter les performances et sans affecter la disponibilité de votre base de données. 

Contrairement à vos données qui sont stockées dans Cosmos DB, les sauvegardes automatiques sont stockées dans le service Stockage Blob Azure. Pour garantir un chargement efficace et à faible latence, la capture instantanée de votre sauvegarde est chargée vers une instance de stockage Blob Azure dans la même région que la région d’écriture en cours de votre compte de base de données Cosmos DB. Pour assurer la résilience contre les sinistres régionaux, chaque instantané de vos données de sauvegarde dans le stockage blob Azure est à nouveau répliqué via le stockage géoredondant (GRS) vers une autre région. Le schéma suivant montre que l’ensemble du conteneur Cosmos DB (avec les trois partitions principales dans la région États-Unis de l’Ouest, dans cet exemple) est sauvegardé dans un compte Stockage Blob Azure distant dans la région États-Unis de l’Ouest, puis répliqué par GRS dans la région États-Unis de l’Est. 

L’image suivante illustre les sauvegardes complètes périodiques de toutes les entités Cosmos DB dans Stockage Azure GRS.

![Sauvegardes complètes périodiques de toutes les entités Cosmos DB dans Stockage Azure GRS](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Période de rétention des sauvegardes
Comme décrit ci-dessus, Azure Cosmos DB prend des captures instantanées de vos données toutes les quatre heures au niveau de la partition. À chaque instant, seuls les deux dernières captures instantanées sont conservées. Toutefois, si la collection/base de données est supprimée, nous conservons les captures instantanées existantes pour toutes les partitions supprimées au sein de la collection/base de données donnée pendant 30 jours.

Si vous souhaitez conserver vos propres instantanés, vous pouvez utiliser l’exportation à l’option d’exportation vers un fichier JSON de l’[outil de migration de données](import-data.md#export-to-json-file) d’Azure Cosmos DB pour planifier des sauvegardes supplémentaires.

## <a name="restoring-a-database-from-an-online-backup"></a>Restauration d’une base de données depuis une sauvegarde en ligne
En cas de suppression accidentelle de vos données, vous pouvez [émettre un ticket de support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [appeler le support technique Azure](https://azure.microsoft.com/support/options/) pour restaurer les données à partir de la dernière sauvegarde automatique. Si vous devez restaurer votre base de données en raison d’un problème d’altération des données (y compris les cas où les documents au sein d’une collection sont supprimés), consultez [Gestion de l’altération des données](#handling-data-corruption) lorsque vous devez prendre des mesures supplémentaires pour empêcher les données altérées d’écraser les sauvegardes existantes. Pour obtenir une capture instantanée spécifique de votre sauvegarde à restaurer, Cosmos DB requiert que les données soient accessibles pendant la durée du cycle de sauvegarde de cette capture instantanée.

## <a name="handling-data-corruption"></a>Gestion de l’altération des données
Azure Cosmos DB conserve les deux dernières sauvegardes de chaque partition dans le compte de base de données. Ce modèle fonctionne bien lorsque un conteneur (collection de documents, graphique, table) ou une base de données est accidentellement supprimé car une des dernières versions peut être restaurée. Toutefois, dans le cas où les utilisateurs présentent un problème d’altération des données, Azure Cosmos DB peut ne pas être informé de l’altération des données et il est possible que l’altération supprime les sauvegardes existantes. Dès que l’altération est supprimée, l’utilisation doit supprimer le conteneur altéré (collection/graphe/table) afin que les sauvegardes soient protégées contre l’écrasement des données altérées.

## <a name="next-steps"></a>Étapes suivantes

Pour répliquer votre base de données dans plusieurs centres de données, consultez [Azure Cosmos DB, un service de base de données mondialement distribué sur Azure](distribute-data-globally.md). 

Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

