---
title: "Sources de données prises en charge disponibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit la liste complète des sources de données prises en charge disponibles pour la préparation des données Azure Machine Learning."
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
ms.openlocfilehash: 7b42080ea4bf9a9e49f2695ab8746d9ead7348bd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Sources de données prises en charge disponibles pour la préparation des données Azure Machine Learning 
Cet article présente les sources de données actuellement prises en charge pour la préparation des données Azure Machine Learning.

Les sources de données prises en charge pour cette version sont répertoriées ci-dessous.

## <a name="types"></a>Types 

### <a name="sql-server"></a>SQL Server
Lisez à partir du serveur SQL local ou de la base de données SQL Azure.

#### <a name="options"></a>Options
- Adresse du serveur
- Approbation du serveur (même lorsque le certificat sur le serveur n’est pas valide. À utiliser avec précaution)
- Type d’authentification (Windows, Server)
- User Name
- Mot de passe
- Base de données à laquelle se connecter
- Requête SQL

#### <a name="notes"></a>Notes
- Les colonnes sql_variant ne sont pas prises en charge
- La colonne Heure est convertie en datetime par l’ajout de l’heure depuis la base de données à la date 1970/1/1
- Lors de l’exécution sur un cluster Spark, toutes les colonnes associées aux données (date, datetime, datetime2, datetimeoffset) évaluent des valeurs incorrectes pour les dates antérieures à 1583
- Les valeurs des colonnes décimales peuvent manquer de précision en raison d’une conversion en décimales

### <a name="directory-vs-file"></a>Répertoire et fichier
Choisissez un fichier unique et lisez-le dans la préparation des données. Le type de fichier est analysé afin de déterminer les paramètres par défaut pour la connexion de fichiers affichée dans l’écran suivant.

Choisissez un répertoire ou un ensemble de fichiers contenus dans un répertoire (le sélecteur de fichiers permet d’effectuer plusieurs sélections). Quelle que soit l’approche utilisée, les fichiers sont lus en tant que flux de données unique, puis ajoutés les uns aux autres, les en-têtes étant supprimés si nécessaire.

Types de fichier pris en charge :
- Délimité (csv, tsv, txt, etc.)
- Largeur fixe
- Texte brut
- Fichier JSON

### <a name="csv-file"></a>Fichier CSV
Lisez un fichier de valeurs séparées par des virgules à partir du stockage.

#### <a name="options"></a>Options
- Séparateur
- Commentaire
- headers
- Symbole décimal
- Encodage de fichier
- Lignes à ignorer

### <a name="tsv-file"></a>Fichier TSV
Lisez un fichier de valeurs séparées par des tabulations à partir du stockage.

#### <a name="options"></a>Options
- Commentaire
- headers
- Encodage de fichier
- Lignes à ignorer

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Lisez un fichier Excel, une feuille à la fois, en spécifiant le nom ou le numéro de la feuille.

#### <a name="options"></a>Options
- Nom ou numéro de la feuille
- headers
- Lignes à ignorer

### <a name="json-file"></a>Fichier JSON
Lire un fichier JSON à partir du stockage. Le fichier est « aplati » lors de la lecture.

#### <a name="options"></a>Options
- Aucun

### <a name="parquet"></a>Parquet
Lisez un jeu de données Parquet, fichier ou dossier unique.

Le format Parquet peut prendre différentes formes dans le stockage. Pour les jeux de données plus petits, un fichier .parquet unique est parfois utilisé. Différentes bibliothèques Python prennent en charge la lecture de fichiers .parquet uniques ou leur écriture. Pour le moment, la préparation des données Azure Machine Learning s’appuie sur la bibliothèque Python PyArrow pour la lecture de données Parquet lors de l’utilisation interactive locale. Elle prend en charge les fichiers .parquet uniques (à condition qu’ils soient écrits comme tels, et non dans le cadre d’un jeu de données plus grand), ainsi que les jeux de données Parquet.

Un jeu de données Parquet est une collection de plusieurs fichiers .parquet représentant chacun une partition plus petite d’un plus grand jeu de données. Les jeux de données sont généralement contenus dans un dossier, et constituent le format de sortie Parquet par défaut pour les plateformes telles que Spark et Hive.

>[!NOTE]
>Lors de la lecture de données Parquet qui se trouvent dans un dossier contenant plusieurs fichiers .parquet, il est plus sûr de sélectionner le répertoire pour la lecture et l’option **Parquet data set** (Jeu de données Parquet). Ainsi, PyArrow lira la totalité du dossier plutôt que chaque fichier. Cela garantit la prise en charge de la lecture de méthodes de stockage Parquet sur disque plus compliquées (par exemple le partitionnement de dossier).

L’exécution avec montée en puissance repose sur les capacités de lecture Parquet de Spark, et prend en charge les fichiers individuels et les dossiers, comme en mode interactif local.

#### <a name="options"></a>Options
- Jeu de données Parquet. Cette option détermine si la préparation des données Azure Machine Learning développe un répertoire donné et tente de lire chaque fichier individuellement (mode non sélectionné) ou si elle traite ce répertoire comme l’ensemble du jeu de données (mode sélectionné). Avec le mode sélectionné, PyArrow choisit la meilleure façon d’interpréter les fichiers.


## <a name="locations"></a>Emplacements
### <a name="local"></a>Local
Emplacement de stockage réseau mappé ou disque dur local.

### <a name="sql-server"></a>SQL Server
Serveur SQL local, ou base de données SQL Azure.

### <a name="azure-blob-storage"></a>Stockage d'objets blob Azure
Stockage Blob Azure qui nécessite un abonnement Azure.

