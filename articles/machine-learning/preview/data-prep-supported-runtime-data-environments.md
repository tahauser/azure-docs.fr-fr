---
title: Combinaisons d’environnements d’exécution et de données prises en charge pour les préparations des données Azure Machine Learning | Microsoft Docs
description: Ce document fournit une liste complète des combinaisons prises en charge pour les différents runtimes et sources de données pour les préparations des données Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: bdd1c51c915787d9e9522f6691ae0ff06d546484
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2018
---
# <a name="supported-matrix-for-this-release"></a>Matrice de prise en charge pour cette version 
Quand votre code charge des données à l’aide de Sources de données Azure Machine Learning ou obtient un cadre de données Pandas ou Spark avec les Préparations des données Azure Machine Learning, les combinaisons suivantes d’emplacements de données et d’environnements de calcul d’expérimentation sont prises en charge :

|     |Fichiers locaux  |Stockage d'objets blob Azure  |Base de données SQL Server***  |
|---------|---------|---------|---------|---------|
|Python local ;    |     Prise en charge    |Non pris en charge         | Non pris en charge        |         |
|Python Docker (machine virtuelle Linux)     |Pris en charge dans les fichiers projet uniquement*         | Non pris en charge        |        Non pris en charge |         |
|PySpark Docker (machine virtuelle Linux)     |Pris en charge dans les fichiers projet uniquement*     |Prise en charge         | Pris en charge \**        |         |
|Azure Data Science Virtual Machine Python     |Pris en charge dans les fichiers projet uniquement*         |Non pris en charge         |Non pris en charge         |         |
|Azure Data Science Virtual Machine PySPark     | Pris en charge dans les fichiers projet uniquement*        |Non pris en charge         |Non pris en charge         |         |
|Azure HDInsight PySpark     | Non pris en charge        |Prise en charge         |Pris en charge \**         |         |
|Azure HDInsight Python     | Non pris en charge        | Non pris en charge        | Non pris en charge        |         |

À l’heure actuelle, Azure Data Lake Store n’est pris en charge pour aucune cible de calcul.

*Quand des chemins de fichiers locaux sont utilisés, les fichiers dans le projet sont copiés dans l’environnement de calcul, puis lus à cet endroit. Les fichiers en dehors du projet ne sont pas copiés et les chemins ne seront plus résolus dans l’environnement de calcul. Envisagez d’utiliser la Substitution de source de données afin que votre code utilise un fichier local lorsqu’il s’exécute en local. Puis, basculez vers un objet blob Stockage Azure pour une configuration d’exécution différente. Vous pouvez également utiliser la prise en charge de l’échantillonnage sur les sources de données pour gérer des exécutions sur des données volumineuses uniquement dans certaines configurations d’exécution.

\** Utilise le pilote SQL Server JDBC Maven 6.2.1. Vous devez vérifier que ce package (ou un package compatible) est inclus dans votre fichier spark_dependencies.yml pour l’environnement de calcul.

\*** Prend en charge Azure SQL Database ou SQL Server à condition que la base de données soit accessible à partir de l’environnement de calcul. 
