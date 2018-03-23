---
title: Gérer des bases de données SQL Azure à l’aide d’Azure Automation | Microsoft Docs
description: Découvrez comment le service Azure Automation peut être utilisé pour gérer les bases de données SQL Azure à grande échelle.
services: sql-database, automation
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/03/2017
ms.author: carlrab
ms.openlocfilehash: 0174b2b1dd5942e17ea60c2dce624c87fd1289c8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Gestion des bases de données SQL Azure à l'aide d'Azure Automation
Ce guide vous présente le service Azure Automation et la manière de l'utiliser pour gérer plus simplement vos bases de données SQL Azure.

## <a name="what-is-azure-automation"></a>Qu'est-ce qu'Azure Automation ?
[Azure Automation](https://azure.microsoft.com/services/automation/) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus. Azure Automation permet d'automatiser les tâches fastidieuses, manuelles, propices aux erreurs et répétitives, afin d'augmenter la fiabilité, l'efficacité et le retour sur investissement pour votre organisation.

Azure Automation fournit un moteur d'exécution de workflow hautement fiable et hautement disponible, qui s'adapte à vos besoins au fur et à mesure que votre entreprise se développe. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et dégagez du temps pour votre personnel informatique/DevOps afin de lui permettre de se concentrer sur des tâches générant une valeur ajoutée pour l'entreprise, en configurant Azure Automation pour exécuter automatiquement vos tâches de gestion de cloud.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Comment Azure Automation peut-il aider à gérer des bases de données SQL Azure ?
Le service Azure SQL Database peut être géré dans Azure Automation à l’aide des [applets de commande PowerShell pour Azure SQL Database](https://docs.microsoft.com/powershell/servicemanagement/azure.sqldatabase/v1.6.1/azure.sqldatabase/) disponibles dans les [outils Azure PowerShell](/powershell/azure/overview). Azure Automation dispose dès le départ de ces applets de commande PowerShell pour Azure SQL Database, de sorte que vous pouvez effectuer toutes vos tâches de gestion de bases de données SQL au sein du service. Dans Azure Automation, vous pouvez également associer ces applets de commande à des applets de commande d'autres services Azure, afin d'automatiser des tâches complexes entre des services Azure et des systèmes tiers.

Azure Automation a également la possibilité de communiquer directement avec les serveurs SQL en exécutant des commandes SQL à l'aide de PowerShell.

La [galerie de runbooks d’Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contient de nombreux runbooks d’équipe produit et de communauté pour commencer à automatiser la gestion des bases de données SQL Azure, d’autres services Azure et de systèmes tiers. La galerie de Runbooks inclut :

* [Exécuter des requêtes SQL avec une base de données SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Mise à l’échelle verticale (haut ou bas) d’une base de données SQL Azure selon un programme planifié](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Tronquer une table SQL si la base de données est proche de la taille maximale](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Indexer les tables dans une base de données SQL Azure si elles sont très fragmentées](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les bases d'Azure Automation et la manière de l'utiliser pour gérer les bases de données SQL Azure, suivez ces liens pour en savoir plus sur Azure Automation.

* [Vue d’ensemble de Microsoft Azure Automation](../automation/automation-intro.md)
* [Mon premier Runbook](../automation/automation-first-runbook-graphical.md)
* [Plan d’apprentissage pour Azure Automation](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [Azure Automation : votre agent SQL dans le cloud](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

