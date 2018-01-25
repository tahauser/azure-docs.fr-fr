---
title: "Comment profiler des données dans des sources de données"
description: "Article de procédure mettant en avant l’inclusion de profils de données au niveau des tables et des colonnes lors de l’inscription des sources de données dans Azure Data Catalog et expliquant comment utiliser des profils de données pour comprendre les sources de données."
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: spelluru
ms.openlocfilehash: 80181b729ffa6d6cbc2d17fe8a5ae8ee4e3d41ab
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="data-profile-data-sources"></a>Profilage de données dans des sources de données
## <a name="introduction"></a>Introduction
**Microsoft Azure Data Catalog** est un service cloud entièrement géré qui sert de système d'inscription et de détection des sources de données d'entreprise. En d'autres termes, **Microsoft Azure Data Catalog** vise essentiellement à aider les utilisateurs à détecter, comprendre et utiliser des sources de données et permet aux organisations de mieux exploiter leurs données. Lorsqu’une source de données est inscrite dans **Azure Data Catalog**, ses métadonnées sont copiées et indexées par le service. Mais ce n’est pas tout.

La fonctionnalité **Profilage des données** dans **Azure Data Catalog** examine les données à partir des sources de données prises en charge dans votre catalogue et collecte des statistiques et des informations relatives à ces données. Vous pouvez inclure très facilement un profil de vos ressources de données. Lorsque vous enregistrez une ressource de données, sélectionnez **Inclure le profil de données** dans l’outil d’inscription de sources de données.

## <a name="what-is-data-profiling"></a>Qu’est-ce que le profilage de données ?
Le profilage des données consiste à examiner les données dans la source de données en cours d’inscription et à collecter des statistiques et des informations sur ces données. Lors de la découverte de sources de données, ces statistiques peuvent vous aider à déterminer dans quelle mesure les données peuvent vous aider à résoudre vos problème métier.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Les sources de données suivantes prennent en charge le profilage des données :

* Vues et tables SQL Server (y compris la base de données SQL Azure et Azure SQL Data Warehouse)
* Tables et vues Oracle
* Tables et vues Teradata
* Tables Hive

L’inclusion de profils de données lors de l’inscription de ressources de données permet à l’utilisateur de répondre à certaines questions sur les sources de données, notamment :

* Ces données peuvent-elles m’aider à résoudre mon problème métier ?
* Les données sont-elles conformes à des normes ou modèles spécifiques ?
* La source de données comporte-t-elle des anomalies et, si oui, lesquelles ?
* Quelles sont les difficultés que je risque de rencontrer en intégrant ces données dans mon application ?

> [!NOTE]
> Vous pouvez également ajouter de la documentation à une ressource pour décrire dans quelle mesure les données peuvent être intégrées à une application. Voir [Comment documenter des sources de données](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Comment inclure un profil de données lors de l’inscription d’une source de données
Vous pouvez inclure très facilement un profil de votre source de données. Lorsque vous procédez à l’inscription d’une source de données dans le panneau **Objets à inscrire** de l’outil d’inscription de sources de données, sélectionnez l’option **Inclure le profil de données**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Pour en savoir plus sur l’inscription des sources de données, consultez les articles [Inscription de sources de données](data-catalog-how-to-register.md) et [Prise en main d’Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrage sur des ressources de données comprenant des profils de données
Pour découvrir des ressources de données qui incluent un profil de données, vous pouvez inclure l’élément `has:tableDataProfiles` ou `has:columnsDataProfiles` dans l’un de vos termes de recherche.

> [!NOTE]
> La sélection de l’option **Inclure le profil de données** dans l’outil d’enregistrement de la source de données inclut les informations de profil au niveau de la colonne et de la table. Toutefois, l’API Data Catalog autorise l’enregistrement des ressources de données avec un seul jeu d’informations de profil.
>
>

## <a name="viewing-data-profile-information"></a>Affichage des informations de profil de données
Dès lors que vous obtenez une source de données appropriée associée à un profil, vous pouvez afficher les détails du profil de données. Pour afficher le profil de données, sélectionnez une ressource de données et choisissez **Profil de données** dans la fenêtre du portail Data Catalog.

![](media/data-catalog-data-profile/data-catalog-view.png)

Un profil de données dans **Azure Data Catalog** affiche les informations de profil au niveau de la table et au niveau de la colonne :

### <a name="object-data-profile"></a>Profil de données au niveau objet
* Nombre de lignes
* Taille de la table
* Date de dernière mise à jour de l’objet

### <a name="column-data-profile"></a>Profil de données au niveau colonne
* Type de données de colonne
* Nombre de valeurs distinctes
* Nombre de lignes contenant des valeurs NULL
* Valeurs minimale, maximale, moyenne et d’écart type des colonnes

## <a name="summary"></a>Résumé
Le profilage des données fournit des statistiques et des informations sur les ressources de données inscrites afin de vous aider à déterminer en quoi les données peuvent vous aider à résoudre vos problèmes métier. Outre l’annotation et la documentation de sources de données, les profils de données peuvent permettre aux utilisateurs de mieux comprendre vos données.

## <a name="see-also"></a>Voir aussi
* [Inscription de sources de données](data-catalog-how-to-register.md)
* [Prise en main d’Azure Data Catalog](data-catalog-get-started.md)
