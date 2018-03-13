---
title: "Planification de la capacité de cluster dans Azure HDInsight | Microsoft Docs"
description: "Découvrez comment spécifier un cluster HDInsight pour la capacité et les performances."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: 70451c5a69a1a268aef0eb957cb92791b92eb221
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planification de la capacité pour les clusters HDInsight

Avant de déployer un cluster HDInsight, planifiez la capacité de cluster souhaitée en déterminant les performances et la mise à l’échelle requises. Cette planification aide à optimiser les coûts et accroît la facilité d’utilisation. Certaines décisions en matière de capacité du cluster ne peuvent pas être changées après le déploiement. Si les paramètres de performances changent, vous pouvez démonter et recréer un cluster sans perdre les données stockées.

Les principales questions à se poser pour la planification de la capacité sont les suivantes :

* Dans quelle région géographique devez-vous déployer votre cluster ?
* De quelle quantité de stockage avez-vous besoin ?
* Quel type de cluster devez-vous déployer ?
* Quels taille et type de machine virtuelle vos nœuds de cluster doivent-ils utiliser ?
* Combien de nœuds worker votre cluster doit-il avoir ?

## <a name="choose-an-azure-region"></a>Choisir une région Azure

La région Azure détermine où votre cluster est physiquement mis en service. Pour réduire la latence de lecture et d’écriture, le cluster doit être proche de vos données.

HDInsight est disponible dans de nombreuses régions Azure. Pour rechercher la région la plus proche, consultez l’entrée *HDInsight pour Linux* sous *Données et analyses* dans [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Choisir la taille et l’emplacement de stockage

### <a name="location-of-default-storage"></a>Emplacement du stockage par défaut

Le stockage par défaut (soit un compte de stockage Azure, soit Azure Data Lake Store) doit être au même emplacement que votre cluster. Le stockage Azure est disponible à tous les emplacements. Data Lake Store est disponible dans certaines régions. Consultez la disponibilité actuelle de Data Lake Store sous *Stockage* dans [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Emplacement des données existantes

Si vous avez déjà avez un compte de stockage ou un Data Lake Store contenant vos données, et que vous souhaitez utiliser ce stockage comme espace de stockage par défaut pour votre cluster, vous devez déployer votre cluster au même emplacement.

### <a name="storage-size"></a>Taille de stockage

Après avoir déployé un cluster HDInsight, vous pouvez joindre d’autres comptes de stockage Azure ou accéder à d’autres Data Lake Stores. Tous vos comptes de stockage doivent résider au même emplacement que votre cluster. Un Data Lake Store peut être à un emplacement différent, mais cela risque d’entraîner une latence des données en lecture/écriture.

Le stockage Azure a certaines [limites de capacité](../azure-subscription-service-limits.md#storage-limits), tandis que Data Lake Store est quasiment illimité.

Un cluster peut accéder à une combinaison de différents comptes de stockage. Voici quelques exemples classiques :

* Quand la quantité de données est susceptible de dépasser la capacité de stockage d’un conteneur de stockage d’objets blob.
* Quand le taux d’accès au conteneur d’objets blob peut dépasser le seuil où la limitation se produit.
* Quand vous souhaitez rendre des données que vous avez déjà chargées vers un conteneur d’objets blob accessible au cluster.
* Quand vous souhaitez isoler différentes parties du stockage pour des raisons de sécurité ou pour simplifier l’administration.

Pour un cluster à 48 nœuds, nous recommandons d’avoir de quatre à huit comptes de stockage. Bien que le stockage total actuel puisse être suffisant, chaque compte de stockage fournit une bande passante réseau supplémentaire pour les nœuds de calcul. Quand vous avez plusieurs comptes de stockage, utilisez un nom aléatoire pour chaque compte de stockage, sans préfixe. L’objectif de l’attribution aléatoire des noms est de réduire le risque de goulot d’étranglement de stockage (limitation) ou les défaillances en mode commun parmi tous les comptes. Pour de meilleures performances, utilisez un seul conteneur par compte de stockage.

## <a name="choose-a-cluster-type"></a>Choisir un type de cluster

Le type de cluster détermine la charge de travail que votre cluster HDInsight est configuré pour exécuter, telle que Hadoop, Storm, Kafka ou Spark. Pour obtenir une description détaillée des types de clusters disponibles, consultez [Présentation d’Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Chaque type de cluster a une topologie de déploiement spécifique qui inclut des exigences en matière de taille et de quantité de nœuds.

## <a name="choose-the-vm-size-and-type"></a>Choisir la taille et le type de machine virtuelle

Chaque type de cluster a un ensemble de types de nœuds, et chaque type de nœud a des options spécifiques pour la taille et le type de machine virtuelle.

Pour déterminer la taille de cluster optimale pour votre application, vous pouvez évaluer la capacité de cluster et augmenter la taille comme indiqué. Vous pouvez par exemple utiliser une charge de travail simulée ou une *requête canary*. Avec la charge simulée, vous exécutez vos charges de travail attendues sur différentes tailles de cluster, en augmentant progressivement la taille jusqu’à obtenir les performances souhaitées. Vous pouvez insérer une requête canary régulièrement parmi les autres requêtes de production pour montrer si le cluster a suffisamment de ressources.

La taille et le type de machine virtuelle sont déterminés par la puissance de traitement de l’UC, la taille de la RAM et la latence du réseau :

* UC : la taille de machine virtuelle détermine le nombre de cœurs. Plus il y a de cœurs, plus chaque nœud peut atteindre un degré élevé de calcul parallèle. En outre, certains types de machines virtuelles ont des cœurs plus rapides.

* RAM : la taille de machine virtuelle détermine également la quantité de RAM disponible sur la machine virtuelle. Pour les charges de travail qui stockent des données en mémoire pour le traitement, au lieu de lire à partir du disque, vérifiez que vos nœuds worker disposent de suffisamment de mémoire pour stocker les données.

* Réseau : pour la plupart des types de clusters, les données traitées par le cluster ne sont pas sur le disque local, mais dans un service de stockage externe tel que Data Lake Store ou Stockage Azure. Prenez en compte le débit et la bande passante réseau entre la machine virtuelle du nœud et le service de stockage. La bande passante accessible à une machine virtuelle augmente généralement avec la taille. Pour plus d’informations, consultez [Tailles des machines virtuelles Linux dans Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Choix de l’échelle du cluster

L’échelle d’un cluster est déterminée par la quantité de ses nœuds de machine virtuelle. Pour tous les types de clusters, il existe certains types de nœuds qui ont une échelle spécifique et d’autres qui prennent en charge la montée en puissance parallèle. Par exemple, un cluster peut nécessiter exactement trois nœuds ZooKeeper ou deux nœuds Head. Les nœuds worker qui effectuent le traitement des données de manière distribuée peuvent bénéficier de la montée en puissance parallèle, en ajoutant des nœuds worker supplémentaires.

En fonction de votre type de cluster, l’augmentation du nombre de nœuds worker ajoute de la capacité de calcul supplémentaire (par exemple, davantage de cœurs), mais peut également ajouter à la quantité totale de mémoire requise pour que l’ensemble du cluster prenne en charge le stockage en mémoire des données en cours de traitement. Comme avec le choix de la taille et du type de machine virtuelle, la sélection de l’échelle de cluster appropriée est généralement effectuée de manière empirique, à l’aide de charges de travail simulées ou de requêtes canary.

Vous pouvez faire monter en puissance votre cluster afin de répondre aux pics de charge, puis annuler la montée en puissance quand les nœuds supplémentaires ne sont plus nécessaires. Pour plus d’informations, consultez la rubrique [Mettre à l’échelle les clusters HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Cycle de vie du cluster

Vous êtes facturé pour la durée de vie d’un cluster. Si vous avez besoin que votre cluster ne soit opérationnel qu’à des horaires spécifiques, vous pouvez [créer des clusters à la demande à l’aide d’Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Vous pouvez également créer des scripts PowerShell qui approvisionnent et suppriment votre cluster, puis planifier ces scripts à l’aide de [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> Quand un cluster est supprimé, son metastore Hive par défaut est également supprimé. Pour rendre persistant le metastore pour la recréation de cluster suivante, utilisez un magasin de métadonnées externe tel qu’Azure Database ou Oozie.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Isoler les erreurs de travaux de cluster

Parfois, des erreurs peuvent se produire à cause de l’exécution en parallèle de plusieurs composants de mappage et de réduction sur un cluster à plusieurs nœuds. Pour aider à isoler le problème, essayez d’effectuer des tests distribués en exécutant simultanément plusieurs travaux sur un cluster à nœud unique, puis développez cette approche pour exécuter plusieurs travaux simultanément sur des clusters qui contiennent plusieurs nœuds. Pour créer un cluster HDInsight à nœud unique dans Azure, utilisez l’option *avancée*.

Vous pouvez également installer un environnement de développement à nœud unique sur votre ordinateur local et y tester la solution. Hortonworks fournit un environnement de développement local à nœud unique pour les solutions Hadoop qui est utile pour le développement initial, la preuve de concept et les tests. Pour plus d’informations, consultez [Hortonworks Sandbox](http://hortonworks.com/products/hortonworks-sandbox/).

Pour identifier le problème sur un cluster local à nœud unique, vous pouvez réexécuter les travaux ayant échoué et ajuster les données d’entrée, ou utiliser des jeux de données plus petits. Le mode d’exécution de ces travaux dépend de la plateforme et du type d’application.

## <a name="quotas"></a>Quotas

Après avoir déterminé la taille, l’échelle et le type de la machine virtuelle de votre cluster cible, vérifiez les limites de capacité de quota actuelles de votre abonnement. Quand vous atteignez une limite de quota, vous risquez de ne pas pouvoir déployer de nouveaux clusters ou de faire monter en puissance des clusters existants en ajoutant des nœuds worker. La limite de quota courante la plus souvent atteinte est le quota de cœurs de processeur qui existe aux niveaux de l’abonnement, de la région et de la série de machine virtuelle. Par exemple, votre abonnement peut avoir une limite totale de 200 cœurs, avec une limite de 30 cœurs dans votre région et une limite de 30 cœurs sur les instances de machine virtuelle. Vous pouvez [contacter le support technique pour demander une augmentation du quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Toutefois, il existe certaines limites de quota fixes. Par exemple, un abonnement Azure peut avoir au maximum 10 000 cœurs. Pour plus d’informations sur ces limites, consultez [Abonnement Azure et limites, quotas et contraintes du service](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka et bien plus encore](hdinsight-hadoop-provision-linux-clusters.md) : découvrez comment installer et configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka, Hive interactif, HBase, R Server ou Storm.
* [Surveiller les performances du cluster](hdinsight-key-scenarios-to-monitor.md) : découvrez les principaux scénarios susceptibles d’affecter les capacités de votre cluster HDInsight.
