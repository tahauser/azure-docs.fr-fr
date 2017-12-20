---
title: "Se connecter à Azure SQL Data Warehouse sqlcmd | Microsoft Docs"
description: "Utilisez l’utilitaire de ligne de commande [sqlcmd][sqlcmd] pour établir la connexion et interroger un entrepôt de données Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: 6e2b69e5-4806-4e91-9ea1-e2b63bf28c46
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 10/31/2016
ms.author: anvang;barbkess
ms.openlocfilehash: 5a3fe1046c3417070ba8ff5bd18a0485e2152eff
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Se connecter à SQL Data Warehouse avec sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Utilisez l’utilitaire de ligne de commande [sqlcmd][sqlcmd] pour établir la connexion et interroger un entrepôt de données Azure SQL Data Warehouse.  

## <a name="1-connect"></a>1. Connecter
Pour commencer à utiliser [sqlcmd][sqlcmd], ouvrez l’invite de commandes et entrez **sqlcmd** suivi de la chaîne de connexion de votre base de données SQL Data Warehouse. La chaîne de connexion requiert les paramètres suivants :

* **Serveur (-S) :** nom du serveur, sous la forme `<`Nom_serveur`>`.database.windows.net
* **Base de données (-d) :** nom de la base de données.
* **Activer les identificateurs marqués (-I) :** les identificateurs marqués doivent être activés pour permettre la connexion à une instance SQL Data Warehouse.

Pour utiliser l’authentification SQL Server, vous devez ajouter les paramètres de nom d’utilisateur/mot de passe :

* **Utilisateur (-U) :** utilisateur du serveur sous la forme `<`utilisateur`>`
* **Mot de passe (-P) :** mot de passe associé à l’utilisateur.

Par exemple, votre chaîne de connexion peut ressembler à ceci :

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Pour utiliser l’authentification Azure Active Directory intégrée, vous devez ajouter les paramètres Azure Active Directory :

* **Authentification Azure Active Directory (-G) :** utilisez Azure Active Directory pour l’authentification

Par exemple, votre chaîne de connexion peut ressembler à ceci :

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Vous devez [activer l’authentification Azure Active Directory](sql-data-warehouse-authentication.md) pour vous authentifier à l’aide d’Active Directory.
> 
> 

## <a name="2-query"></a>2. Requête
Une fois la connexion établie, vous pouvez envoyer des instructions Transact-SQL prises en charge à l’instance.  Dans cet exemple, les requêtes sont soumises en mode interactif.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Les exemples ci-après vous indiquent comment exécuter vos requêtes en mode batch à l’aide de l’option -Q ou en redirigeant votre SQL vers sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Étapes suivantes
Consultez la [documentation sqlcmd][sqlcmd] pour en savoir plus sur les options disponibles dans sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
