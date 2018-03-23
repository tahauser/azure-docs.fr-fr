---
title: Concevoir sa première base de données SQL Azure - C# | Microsoft Docs
description: Apprenez à concevoir votre première base de données SQL Azure et vous y connecter à l’aide d’un programme C# utilisant ADO.NET.
services: sql-database
author: MightyPen
manager: craigg-msft
ms.reviewer: CarlRabeler
ms.service: sql-database
ms.custom: develop databases, mvc, devcenter
ms.topic: tutorial
ms.date: 03/15/2018
ms.openlocfilehash: 3b6f260983e3c826bf558f0fe6d1a0fa6ae6b3af
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="design-an-azure-sql-database-and-connect-with-cx23-and-adonet"></a>Concevoir une base de données SQL Azure et se connecter avec C&#x23; et ADO.NET

Azure SQL Database est une solution DBaaS relationnelle gérée dans Microsoft Cloud (Azure). Ce didacticiel vous montre comment utiliser le portail Azure et ADO.NET avec Visual Studio pour : 

> [!div class="checklist"]
> * Créer une base de données dans le portail Azure
> * Configurer une règle de pare-feu au niveau du serveur dans le portail Azure
> * Se connecter à la base de données avec ADO.NET et Visual Studio
> * Créer des tables avec ADO.NET
> * Insérer, mettre à jour et supprimer des données avec ADO.NET 
> * Interroger les données avec ADO.NET

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis


Une installation de [Visual Studio Community 2017, Visual Studio Professional 2017 ou Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

<!-- The following included .md, sql-database-tutorial-portal-create-firewall-connection-1.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-tutorial-portal-create-firewall-connection-1](../../includes/sql-database-tutorial-portal-create-firewall-connection-1.md)]


<!-- The following included .md, sql-database-csharp-adonet-create-query-2.md, is long.
And it starts with a ## H2.
-->

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à exécuter des tâches de base de données classiques telles que la création d’une base de données et de tables, le chargement et l’interrogation de données, ainsi que la restauration de la base de données à un point antérieur dans le temps. Vous avez appris à effectuer les actions suivantes :
> [!div class="checklist"]
> * Créer une base de données
> * Configurer une règle de pare-feu
> * Se connecter à la base de données avec [Visual Studio et C#](sql-database-connect-query-dotnet-visual-studio.md)
> * créez des tables
> * Insérer, mettre à jour et supprimer des données
> * Données de requête

Passez au didacticiel suivant pour en savoir plus sur la migration de vos données.

> [!div class="nextstepaction"]
>[Migrer votre base de données SQL Server vers Azure SQL Database](sql-database-migrate-your-sql-server-database.md)

