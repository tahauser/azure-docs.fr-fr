---
title: "Utiliser Power BI avec SQL Data Warehouse | Microsoft Docs"
description: "Conseils relatifs à l’utilisation de Power BI avec Azure SQL Data Warehouse pour le développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: b12bee87-2268-40c2-81bf-ab27588b32e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: 4ea9a2ff0c95a73b348d3b48e9e62957d5cce31c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="use-power-bi-with-sql-data-warehouse"></a>Utiliser Power BI avec SQL Data Warehouse
À l’instar de la Base de données SQL Azure, la fonction de connexion directe de SQL Data Warehouse permet à l’utilisateur de tirer parti d’une capacité de transfert logique performante, ainsi que des fonctionnalités d’analyse de Power BI.  Grâce à la connexion directe, les requêtes sont renvoyées vers votre solution Azure SQL Data Warehouse en temps réel lorsque vous explorez les données.  Combinée à la puissance de SQL Data Warehouse, cette fonction permet aux utilisateurs de créer des rapports dynamiques en quelques minutes pour plusieurs téraoctets de données.  En outre, l’introduction du bouton Open in Power BI permet aux utilisateurs de connecter directement Power BI à leur solution SQL Data Warehouse sans collecter d’informations à partir d’autres sections d’Azure.

Lorsque vous utilisez la connexion directe, notez les points suivants :

* Spécifiez le nom de serveur complet lors de la connexion (voir ci-dessous pour plus de détails).
* Vérifiez que les règles de pare-feu relatives à la base de données sont configurées sur « Autoriser l’accès aux services Azure ».
* Chaque action, telle que la sélection d’une colonne ou l’ajout d’un filtre, interroge directement l’entrepôt de données.
* Les vignettes sont actualisées environ toutes les 15 minutes (il n’est donc pas nécessaire de planifier l’actualisation).
* Les Questions et réponses ne sont pas disponibles pour les jeux de données de connexion directe.
* Les modifications de schéma ne sont pas récupérées automatiquement.
* Toutes les requêtes de connexion directe expireront après 2 minutes.

Ces restrictions et remarques sont susceptibles de changer, car nous améliorons continuellement les expériences. Les étapes de connexion sont détaillées ci-dessous.  

## <a name="using-the-open-in-power-bi-button"></a>Utilisation du bouton « Open in Power BI »
La méthode la plus simple pour basculer entre SQL Data Warehouse et Power BI consiste à utiliser le bouton Open in Power BI. Ce bouton vous permet de commencer à créer des tableaux de bord en toute transparence dans Power BI.  

1. Pour démarrer, accédez à votre instance de SQL Data Warehouse dans le portail Azure.
2. Cliquez sur le bouton Open in Power BI.
3. Si nous ne sommes pas en mesure de vous connecter directement, ou que vous ne disposez pas d’un compte Power BI, vous devrez vous connecter.  
4. Vous serez alors dirigé vers la page de connexion SQL Data Warehouse, préremplie avec les informations de votre instance SQL Data Warehouse.
5. Après avoir entré vos informations d’identification, vous serez complètement connecté à votre instance SQL Data Warehouse.

## <a name="connecting-through-the-power-bi-portal"></a>Connexion par le biais du portail Power BI
Outre l’utilisation du bouton Open in Power BI, les utilisateurs peuvent se connecter à leur instance SQL Data Warehouse au moyen du portail Power BI.

1. En bas du volet de navigation, cliquez sur « Obtenir des données ».
2. Sélectionnez « Bases de données ».
3. Une fois sur la page des bases de données, sélectionnez « Entrepôt de données SQL Azure », puis cliquez sur « Se connecter ».
4. Entrez les informations de connexion nécessaires.  Le nom de serveur et le nom de base de données sont spécifiés dans le portail Azure.
5. Vous serez redirigé vers la page d’accueil de Power BI et, une fois que vous serez connecté, une nouvelle entrée sous « Jeu de données » s’affichera avec le nom de votre instance.  
6. Vous pouvez cliquer sur le nouveau jeu de données pour explorer toutes les tables et les affichages dans votre base de données. La sélection d’une colonne renverra une requête à la source, ce qui entraînera la création dynamique de votre élément visuel. Ces éléments visuels peuvent être enregistrés dans un nouveau rapport, puis ré-épinglés dans votre tableau de bord.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
