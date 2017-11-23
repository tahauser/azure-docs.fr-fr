---
title: "Échantillonner des données dans des tables Hive Azure HDInsight | Microsoft Docs"
description: "Sous-échantillonnage de données dans des tables Hive Azure HDInsight (Hadoop)"
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: d765c2adc8a3aa77d903490875c7f8ad622ef4d2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2017
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Échantillonner des données dans des tables Hive Azure HDInsight
Cet article décrit la procédure de sous-échantillonnage des données stockées dans des tables Hive Azure HDInsight à l'aide de requêtes Hive pour les réduire à une taille plus gérable à des fins d’analyse. Nous y abordons trois méthodes d’échantillonnage communément utilisées :

* Échantillonnage aléatoire uniforme
* Échantillonnage aléatoire par groupe
* Échantillonnage stratifié

Le **menu** ci-après pointe vers des rubriques qui expliquent comment échantillonner des données dans différents environnements de stockage.

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Pourquoi échantillonner vos données ?**
Si vous prévoyez d’analyser un jeu de données volumineux, il est généralement recommandé de sous-échantillonner les données afin de réduire leur taille sous une forme plus facilement exploitable, mais toujours représentative. Cette opération d’échantillonnage facilite la compréhension et l’exploration des données, ainsi que la conception de fonctionnalités. Son rôle dans le processus TDSP (Team Data Science Process) consiste à permettre le prototypage rapide des fonctions de traitement des données et des modèles d’apprentissage automatique.

Cette tâche d’échantillonnage est une étape du [processus TDSP (Team Data Science Process)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Envoi de requêtes Hive
Les requêtes Hive peuvent être envoyées à partir de console de ligne de commande Hadoop, sur le nœud principal du cluster Hadoop. Pour effectuer cette opération, connectez-vous au nœud principal du cluster Hadoop, ouvrez la console de ligne de commande Hadoop, puis soumettez les requêtes Hive à cet emplacement. Pour plus d’informations sur la soumission de requêtes Hive dans la console de ligne de commande Hadoop, voir [Envoi de requêtes Hive](move-hive-tables.md#submit).

## <a name="uniform"></a> Échantillonnage aléatoire uniforme
Le terme « échantillonnage aléatoire uniforme » signifie que chaque ligne du jeu de données a la même chance d’être échantillonnée que les autres. Vous pouvez l’implémenter en ajoutant un champ supplémentaire rand() au jeu de données dans la requête « select » interne et dans la requête « select » externe conditionnant ce champ aléatoire.

Voici un exemple de requête :

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Dans cet exemple, la chaîne `<sample rate, 0-1>` spécifie la proportion d’enregistrements que les utilisateurs veulent échantillonner.

## <a name="group"></a> Échantillonnage aléatoire par groupe
Lorsque vous échantillonnez des données catégorielles, vous pouvez choisir d’inclure ou d’exclure toutes les instances d’une valeur spécifique de la valeur catégorielle. Ce type d’échantillonnage est appelé « échantillonnage par groupe ». Par exemple, si vous disposez d’une valeur catégorielle « *État* », qui présente les valeurs, comme NY, MA, CA, NJ et PA, vous voulez que les enregistrements de chaque État soient toujours regroupés, qu’ils soient ou non échantillonnés.

Voici un exemple de requête effectuant un échantillonnage par groupe :

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Échantillonnage stratifié
L’échantillonnage aléatoire est stratifié par rapport à une variable catégorielle lorsque les échantillons obtenus comportent des valeurs de cette catégorie qui existent dans la même proportion que dans la population parente. En considérant le même exemple que ci-dessus, supposons que vos données comportent les observations suivantes par État : NJ présente 100 observations, NY 60 observations et WA 300 observations. Si vous spécifiez un taux d’échantillonnage stratifié de 0,5, l’échantillon obtenu pour NJ, NY et WA sera respectivement d’environ 50, 30 et 150 observations.

Voici un exemple de requête :

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Pour plus d’informations sur les méthodes d’échantillonnage plus élaborées qui sont disponibles dans Hive, consultez la page consacrée aux [méthodes d’échantillonnage dans le manuel du langage](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

