---
title: Niveaux tarifaires dans Azure Database pour MySQL | Microsoft Docs
description: Niveaux tarifaires dans Azure Database pour MySQL
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: ae7e57e9b40f5194c15525a48843060bbccaa956
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-mysql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>Options et performances d’Azure Database pour MySQL : comprendre ce qui est disponible dans chaque niveau tarifaire
Quand vous créez un serveur Azure Database pour MySQL, vous choisissez entre trois options principales pour configurer les ressources allouées pour ce serveur. Ces choix ont un impact sur les performances et la mise à l’échelle du serveur.
- Niveau tarifaire
- Unités de calcul
- Stockage (Go)

Pour chaque niveau tarifaire, vous choisissez dans une plage de niveaux de performances (unités de calcul) en fonction de vos besoins en matière de charges de travail. Des niveaux de performances plus élevés offrent des ressources supplémentaires pour votre serveur, conçu pour délivrer un débit plus élevé. Vous pouvez changer le niveau de performances du serveur au sein d’un niveau tarifaire, pratiquement sans interruption des applications.

> [!IMPORTANT]
> Le service étant en préversion publique, il n’existe pas de contrat de niveau de service (SLA) garanti.

Dans un serveur Azure Database pour MySQL, vous pouvez avoir une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. 

## <a name="choose-a-pricing-tier"></a>Sélectionnez un niveau tarifaire
En phase de préversion, le service Azure Database pour MySQL offre deux niveaux tarifaires : De base et Standard. Le niveau Premium n’est pas encore disponible, mais il le sera bientôt. 

Le tableau suivant fournit des exemples de niveaux tarifaires adaptés à différentes charges de travail d’application.

| Niveau tarifaire | Charges de travail cibles |
| :----------- | :----------------|
| De base | Idéal pour les petites charges de travail qui requièrent une capacité de calcul et de stockage évolutive sans garantie d’E/S par seconde. Exemple : serveurs utilisés pour le développement ou le test ou pour des applications à petite échelle rarement utilisées. |
| Standard | Le meilleur choix pour les applications cloud nécessitant une garantie d’E/S par seconde avec un débit élevé. Exemples : applications web ou applications d’analyse. |
| Premium | Idéal pour les charges de travail nécessitant une latence faible pour les transactions et les E/S. Assure la meilleure prise en charge possible d’un grand nombre d’utilisateurs simultanés. S’applique aux bases de données qui prennent en charge les applications critiques.<br />Le niveau tarifaire Premium n’est pas disponible en préversion. |

Pour choisir un niveau tarifaire, commencez par déterminer si votre charge de travail a besoin d’une garantie d’E/S par seconde. Si c’est le cas, utilisez le niveau tarifaire Standard.

| **Caractéristiques des niveaux tarifaires** | **De base** | **Standard** |
| :------------------------ | :-------- | :----------- |
| Nombre maximal d’unités de calcul | 100 | 800 | 
| Volume total de stockage maximal | 1 To | 1 To | 
| Garantie d’E/S par seconde de stockage | N/A  | Oui | 
| E/S par seconde de stockage maximales | N/A  | 3 000 | 
| Période de rétention de sauvegarde de bases de données | 7 jours | 35 jours | 

Pendant la phase de préversion, vous ne pouvez pas changer le niveau tarifaire une fois que le serveur est créé. Ultérieurement, il sera possible de faire passer un serveur d’un niveau tarifaire à l’autre.

## <a name="understand-the-price"></a>Comprendre les tarifs
Lorsque vous créez un serveur Azure Database pour MySQL à l’intérieur du [portail Azure](https://portal.azure.com/#create/Microsoft.MySQLServer), sélectionnez la page **Niveau tarifaire** pour afficher le coût mensuel en fonction des options que vous avez sélectionnées. Si vous n’avez pas d’abonnement Azure, utilisez la calculatrice de prix Azure pour obtenir un prix estimé. Pour personnaliser les options, visitez le site web [Calculatrice de prix d’Azure](https://azure.microsoft.com/pricing/calculator/), cliquez sur **Ajouter des produits à votre estimation**, développez la catégorie **Bases de données**, puis choisissez **Base de données Azure pour MySQL**.

## <a name="choose-a-performance-level-compute-units"></a>Choisir un niveau de performances (unités de calcul)
Une fois que vous avez déterminé le niveau tarifaire de votre serveur Azure Database pour MySQL, vous êtes prêt à déterminer le niveau de performances en sélectionnant le nombre d’unités de calcul nécessaires. Un bon point de départ est 200 ou 400 unités de calcul pour les applications qui ont besoin d’accès concurrentiels en nombre plus élevé pour leurs charges de travail web ou d’analyse, puis d’ajuster par palier au fil des besoins. 

Les unités de calcul sont une mesure du débit de traitement du processeur, dont la disponibilité est garantie pour un serveur Azure Database pour MySQL. Une unité de calcul est une mesure mélangée de ressources processeur et mémoire.  Pour plus d’informations, voir [Présentation des unités de calcul](concepts-compute-unit-and-storage.md)

### <a name="basic-pricing-tier-performance-levels"></a>Niveaux de performances du niveau tarifaire De base :

| **Niveau de performances** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Nombre maximal d’unités de calcul | 50 | 100 |
| Taille du stockage inclus | 50 Go | 50 Go |
| Taille maximale de stockage du serveur\* | 1 To | 1 To |

### <a name="standard-pricing-tier-performance-levels"></a>Niveaux de performances du niveau tarifaire Standard :

| **Niveau de performances** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Nombre maximal d’unités de calcul | 100 | 200 | 400 | 800 |
| Taille du stockage inclus et E/S par seconde approvisionnées | 125 GO,<br/> 375 E/S PAR SECONDE | 125 GO,<br/> 375 E/S PAR SECONDE | 125 GO,<br/> 375 E/S PAR SECONDE | 125 GO,<br/> 375 E/S PAR SECONDE |
| Taille maximale de stockage du serveur\* | 1 To | 1 To | 1 To | 1 To |
| Nombre maximal d’E/S par seconde du serveur approvisionnées | 3 000 E/S PAR SECONDE | 3 000 E/S PAR SECONDE | 3 000 E/S PAR SECONDE | 3 000 E/S PAR SECONDE |
| Nombre maximal d’E/S par seconde du serveur approvisionnées par Go | 3 E/S par seconde fixes par Go | 3 E/S par seconde fixes par Go | 3 E/S par seconde fixes par Go | 3 E/S par seconde fixes par Go |

\*La taille maximale de stockage du serveur fait référence à la taille maximale de stockage approvisionnée pour votre serveur.

## <a name="storage"></a>Stockage 
La configuration du stockage définit la quantité de stockage disponible pour un serveur Azure Database pour MySQL. Le stockage utilisé par le service inclut les fichiers de base de données, les journaux de transaction et les journaux du serveur MySQL. Lors de la sélection de la configuration du stockage, prenez en compte la taille du stockage nécessaire pour héberger vos bases de données et les besoins en performances (E/S par seconde).

Un minimum de capacité de stockage est inclus avec chaque niveau tarifaire, indiqué dans le tableau précédent par « Taille du stockage inclus ». Une capacité de stockage supplémentaire peut être ajoutée lors de la création du serveur, par incréments de 125 Go, jusqu’au stockage maximal autorisé. La capacité de stockage supplémentaire peut être configurée indépendamment de la configuration des unités de calcul. Le prix change en fonction de la quantité de stockage sélectionnée.

La configuration des E/S par seconde dans chaque niveau de performances est relative au niveau tarifaire et à la taille de stockage choisis. Le niveau De base n’offre pas de garantie d’E/S par seconde. Dans le niveau tarifaire Standard, les E/S par seconde augmentent proportionnellement à la taille maximale de stockage, selon un ratio fixe de 3:1. Le stockage de 125 Go inclus garantit 375 E/S par seconde provisionnées, chaque E/S pouvant atteindre 256 Ko. Vous pouvez choisir un stockage supplémentaire jusqu’à un maximum de 1 To, avec une garantie de 3 000 E/S par seconde approvisionnés.

Surveillez le graphe des métriques dans le portail Azure ou lancez des commandes Azure CLI pour mesurer la consommation de stockage et les E/S par seconde. Les métriques pertinentes à surveiller sont Limite de stockage, Pourcentage de stockage, Stockage utilisé et Pourcentage d’E/S.

>[!IMPORTANT]
> Pendant la phase de préversion, vous choisissez la quantité de stockage au moment de la création du serveur. Le changement de taille du stockage sur un serveur existant n’est pas encore pris en charge. 

## <a name="scaling-a-server-up-or-down"></a>Augmentation ou diminution de la puissance d’un serveur
Vous choisissez initialement le niveau tarifaire et le niveau de performances quand vous créez votre serveur Azure Database pour MySQL. Ultérieurement, vous pouvez augmenter ou diminuer dynamiquement les unités de calcul, dans la plage du même niveau tarifaire. Dans le portail Azure, faites glisser le curseur des unités de calcul dans la page Niveau tarifaire du serveur, ou créez un script en suivant cet exemple : [Surveiller et mettre à l’échelle un serveur Azure Database pour MySQL avec Azure CLI](scripts/sample-scale-server.md)

La mise à l’échelle des unités de calcul se fait indépendamment de la taille maximale de stockage que vous avez choisie.

En réalité, un changement du niveau de performances d’un serveur crée une copie du serveur d’origine avec le nouveau niveau de performances, puis bascule les connexions vers ce serveur copié. Aucune donnée n’est perdue au cours de ce processus. Pendant le bref instant où le système bascule vers la copie du serveur, les connexions à la base de données sont désactivées, et certaines transactions en cours peuvent être annulées. Cette fenêtre de désactivation varie, mais dure moins de 4 secondes en moyenne, et ne dépasse pas 30 secondes dans plus de 99 % des cas. Cette durée peut se révéler supérieure, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.

La durée de la totalité du processus de mise à l’échelle dépend de la taille et du niveau tarifaire du serveur avant et après le changement. Par exemple, le changement des unités de calcul au sein du niveau tarifaire Standard ne demande normalement que quelques minutes. Les nouvelles propriétés du serveur ne sont appliquées qu’une fois les modifications terminées.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les unités de calcul, consultez [Présentation des unités de calcul](concepts-compute-unit-and-storage.md)
- Découvrez comment [Surveiller et mettre à l’échelle un serveur Azure Database pour MySQL à l’aide d’Azure CLI](scripts/sample-scale-server.md)
