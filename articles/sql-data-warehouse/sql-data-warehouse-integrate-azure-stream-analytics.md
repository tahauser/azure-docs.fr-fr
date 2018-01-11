---
title: Utiliser Azure Stream Analytics avec SQL Data Warehouse | Documents Microsoft
description: "Conseils sur l’utilisation d’Azure Stream Analytics avec Azure SQL Data Warehouse pour le développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 8aeb2247-20c5-4a29-b327-30a8ce09dfdc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: c5c0450cba541a9346f023057345c5fc9b147903
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Utiliser Azure Stream Analytics avec SQL Data Warehouse
Azure Stream Analytics est un service entièrement géré permettant de traiter des événements avec une latence faible, une haute disponibilité et de façon évolutive via des données de diffusion dans le cloud. Vous pouvez découvrir les principes de base en lisant [Présentation d’Azure Stream Analytics][Introduction to Azure Stream Analytics]. Vous pouvez ensuite apprendre à créer une solution de bout en bout avec Stream Analytics en suivant le didacticiel [Bien démarrer avec Azure Stream Analytics][Get started using Azure Stream Analytics].

Dans cet article, vous allez apprendre à utiliser votre base de données Azure SQL Data Warehouse à la façon d’un récepteur de sortie pour vos travaux Stream Analytics.

## <a name="prerequisites"></a>Composants requis
Exécutez d’abord les étapes suivantes dans le didacticiel [Bien démarrer avec Azure Stream Analytics][Get started using Azure Stream Analytics].  

1. Création d’une entrée de hub d’événements
2. Configuration et démarrage de l’application de génération d’événements
3. Configuration d’un travail Stream Analytics
4. Spécification d’une entrée de travail et d’une requête

Ensuite, créez une base de données SQL Data Warehouse.

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Spécifier la sortie du travail : base de données Azure SQL Data Warehouse
### <a name="step-1"></a>Étape 1
En haut de la page de votre travail Stream Analytics, cliquez sur **SORTIE** puis sur **AJOUTER UNE SORTIE**.

### <a name="step-2"></a>Étape 2
Sélectionnez Base de données SQL, puis cliquez sur suivant.

![][add-output]

### <a name="step-3"></a>Étape 3
Entrez les valeurs ci-dessous dans la page suivante :

* *Alias de sortie*: entrez un nom convivial pour cette sortie de travail.
* *Abonnement*:
  * Si votre base de données SQL Data Warehouse se trouve dans le même abonnement que celui de la tâche Stream Analytics, sélectionnez Utiliser la base de données SQL de l’abonnement actuel.
  * Si votre base de données se trouve dans un autre abonnement, sélectionnez Utiliser la base de données SQL d’un autre abonnement.
* *Base de données*: spécifiez le nom d’une base de données de destination.
* *Nom du serveur*: spécifiez le nom du serveur pour la base de données que vous venez d’indiquer. Vous pouvez obtenir cette information dans le portail Azure.

![][server-name]

* *Nom d’utilisateur*: tapez le nom d’utilisateur d’un compte disposant d’autorisations en écriture sur la base de données.
* *Mot de passe*: indiquez le mot de passe du compte d’utilisateur spécifié.
* *Table*: spécifiez le nom de la table cible dans la base de données.

![][add-database]

### <a name="step-4"></a>Étape 4
Cliquez sur la coche pour ajouter cette sortie du travail et pour vérifier que Stream Analytics peut se connecter à la base de données.

![][test-connection]

Dès que la connexion à la base de données est établie, une notification s’affiche en bas du portail. Vous pouvez tester la connexion à la base de données en cliquant sur Tester la connexion au bas de l’écran.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble de l’intégration, consultez [Vue d’ensemble sur l’intégration de SQL Data Warehouse][SQL Data Warehouse integration overview].

Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/
