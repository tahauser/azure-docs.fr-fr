---
title: "Destinations et sorties de données prises en charge avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit la liste complète des destinations et sorties de données prises en charge disponibles pour la préparation des données Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 5451a5f525da845f97141a97241efb53ec42ff58
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="supported-data-exports-for-this-preview"></a>Exportations de données prises en charge pour cette version d’évaluation 
Il est possible d’exporter vers différents formats. Vous pouvez utiliser ces formats pour conserver les résultats intermédiaires de la préparation des données avant leur intégration au reste du flux de travail Machine Learning.

## <a name="types"></a>Types 
### <a name="csv-file"></a>Fichier CSV 
Écrivez un fichier de valeurs séparées par des virgules (CSV) dans le stockage.

#### <a name="options"></a>Options
- Fins de ligne
- Remplacer les valeurs NULL par
- Remplacer les erreurs par 
- Séparateur


### <a name="parquet"></a>Parquet 
Écrivez un jeu de données dans le stockage au format Parquet.

Le format Parquet peut prendre différentes formes dans le stockage. Pour les jeux de données plus petits, un fichier .parquet unique est parfois utilisé. Différentes bibliothèques Python prennent en charge la lecture et l’écriture sur un fichier .parquet unique. 

Pour le moment, Azure Machine Learning Workbench s’appuie sur la bibliothèque Python PyArrow pour l’écriture de données Parquet lors de l’utilisation interactive locale. Cela signifie que le fichier .parquet unique est actuellement le seul format de sortie Parquet pris en charge lors d’une utilisation interactive locale.

Au cours des exécutions évolutives (sur Spark), Azure Machine Learning Workbench s’appuie sur les fonctionnalités de lecture et écriture Parquet de Spark. Le format de sortie par défaut de Spark pour Parquet (actuellement le seul pris en charge) relève d’une structure semblable à un jeu de données Hive. Cela signifie qu’un dossier contient de nombreux fichiers .parquet étant chacun une partition d’un plus grand jeu de données. 

#### <a name="caveats"></a>Mises en garde 
Le format Parquet est relativement récent et son implémentation comporte certaines incohérences entre les différentes bibliothèques. Par exemple, Spark impose des restrictions quant à la validité des caractères dans les noms de colonnes lors de l’écriture dans Parquet, ce qui n’est pas le cas de PyArrow. Un nom de colonne ne peut pas contenir les caractères suivants : 
- .
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- Pour garantir la compatibilité avec Spark, à chaque fois que vous écrivez des données dans Parquet, les occurrences de ces caractères dans les noms de colonnes sont remplacées par un trait de soulignement ( _ ).
>- Pour assurer la cohérence lors des exécutions locales et de montée en puissance, les noms de colonnes des données écrites dans Parquet par le biais de l’application, Python ou Spark sont expurgés pour garantir la compatibilité avec Spark. Pour garantir la validité des noms de colonnes attendus lors de l’écriture de caractères Parquet dans Spark, supprimez le jeu non valide des colonnes avant l’écriture.



## <a name="locations"></a>Emplacements 
### <a name="local"></a>Local 
Emplacement de stockage réseau mappé ou disque dur local

### <a name="azure-blob-storage"></a>Stockage d'objets blob Azure
Le stockage Blob Azure nécessite un abonnement Azure.

