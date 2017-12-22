---
title: "Audit dans Azure SQL Data Warehouse | Microsoft Docs"
description: "Prise en main de l’audit dans Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 0e6af148-b218-4b43-bb5f-907917d20330
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 08/21/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: f851c82ebeaa647f663d499a4d327c3479e36121
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Audit dans Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Audit](sql-data-warehouse-auditing-overview.md)
> * [Détection de menaces](sql-data-warehouse-security-threat-detection.md)
> 
> 

La fonction d’audit de SQL Data Warehouse vous permet d’enregistrer les événements survenus dans votre base de données dans un journal d’audit au sein de votre compte Microsoft Azure Storage. L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données, et à découvrir des discordances et des anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité. Cette fonction s’intègre également dans Microsoft Power BI, afin de faciliter la création d’analyses et de rapports approfondis.

Les outils d'audit permettent et facilitent le respect des normes liées à la conformité, mais ne garantissent pas cette dernière. Pour plus d’informations sur les programmes Azure prenant en charge la conformité aux normes, voir le <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Centre de confidentialité Azure</a>.

* [Principes fondamentaux de l’audit de base de données]
* [Configuration de l'audit pour votre base de données]
* [Analyse des journaux et des rapports d’audit]

## <a id="subheading-1"></a>Principes fondamentaux de l’audit de base de données Azure SQL Data Warehouse
Éléments rendus possibles par l’audit de bases de données SQL Data Warehouse :

* **La rétention** d’une piste d’audit d’événements sélectionnés. Vous pouvez définir des catégories d’actions de base de données à auditer.
* **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
* **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Vous pouvez configurer l’audit pour les catégories d’événements suivantes :

**SQL brut** et **SQL paramétré** pour lesquels les journaux d’audit collectés sont classés comme  

* **accès aux données ;**
* **modifications de schéma (DDL) ;**
* **modifications de données (DML) ;**
* **comptes, rôles et autorisations (DCL) ;**
* **Procédure stockée**, **Connexion** et **Gestion des transactions**.

Pour chaque catégorie d’événements, les audits des opérations **Succès** et **Échec** sont configurées séparément.

Pour plus d’informations sur les activités et les événements audités, consultez <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">Informations de référence sur le format des journaux d’audit (téléchargement d’un fichier doc)</a>.

Les journaux d'audit sont stockés dans votre compte de stockage Azure. Vous pouvez définir une période de rétention des journaux d'audit.

Une stratégie d’audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut. Une stratégie d’audit de serveur par défaut s’applique à toutes les bases de données d’un serveur sur lequel aucune stratégie d’audit de base de données de substitution spécifique n’est définie.

Avant de configurer l’audit, vérifiez que vous utilisez bien un [« Client de niveau inférieur »](sql-data-warehouse-auditing-downlevel-clients.md).

## <a id="subheading-2"></a>Configuration de l’audit pour votre base de données
1. Lancez le <a href="https://portal.azure.com" target="_blank">portail Azure</a>.
2. Accédez au panneau **Paramètres** de l’instance SQL Data Warehouse que vous voulez auditer. Dans le panneau **Paramètres**, sélectionnez **Audit et détection des menaces**.
   
    ![][1]
3. Ensuite, activez la fonction d’audit en cliquant sur le bouton **ACTIVÉ** .
   
    ![][3]
4. Dans le panneau de configuration de l’audit, sélectionnez **DÉTAILS DU STOCKAGE** pour ouvrir le panneau Stockage des journaux d’audit. Sélectionnez le compte de stockage Azure dans lequel les journaux seront enregistrés, ainsi que la période de rétention. 
>[!TIP]
>Utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport préconfigurés.
   
    ![][4]
5. Cliquez sur le bouton **OK** pour enregistrer la configuration des détails du stockage.
6. Sous **JOURNALISATION PAR ÉVÈNEMENT**, cliquez sur **SUCCÈS** et sur **ÉCHEC**pour enregistrer tous les événements, ou choisissez des catégories d’événements individuelles.
7. Si vous configurez l’audit pour une base de données, vous pouvez être amené à modifier la chaîne de connexion de votre client pour garantir que l’audit des données est correctement capturé. Consultez la rubrique [Modifier le nom de domaine complet du serveur dans la chaîne de connexion](sql-data-warehouse-auditing-downlevel-clients.md) , qui traite des connexions de client de niveau inférieur.
8. Cliquez sur **OK**.

## <a id="subheading-3"></a>Analyse des journaux et des rapports d’audit
Les journaux d’audit sont agrégés dans une collection de tables de stockage avec un préfixe **SQLDBAuditLogs** au sein du compte de stockage Azure que vous avez choisi lors de la configuration. Vous pouvez afficher les fichiers journaux à l'aide d'un outil tel que l'<a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Explorateur de stockage Azure</a>.

Un modèle de rapport de tableau de bord préconfiguré est disponible sous forme de <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">feuille de calcul Excel téléchargeable</a> afin de vous aider à analyser rapidement les données de journal. Pour utiliser le modèle sur vos journaux d'audit, il vous faut Excel 2013 ou une version ultérieure et Power Query, téléchargeable <a href="http://www.microsoft.com/download/details.aspx?id=39379">ici</a>.

Le modèle contient des données d'exemple fictives. Vous pouvez configurer Power Query de façon à ce qu'il importe votre journal d'audit directement à partir de votre compte de stockage Azure.

## <a id="subheading-4"></a>Régénération des clés de stockage
Dans un environnement de production, vous allez probablement actualiser périodiquement vos clés de stockage. Au moment d’actualiser vos clés, vous devez réenregistrer la stratégie. Pour ce faire, procédez comme suit :

1. Dans le panneau de configuration de l’audit (décrit plus haut dans la section de configuration de l’audit), faites passer la **clé d’accès du stockage** de *Principale* à *Secondaire*, puis choisissez **ENREGISTRER**.

   ![][4]
2. Revenez au volet de configuration du stockage, puis **régénérez** la *clé d’accès primaire*.
3. Revenez au panneau de configuration de l’audit, faites passer la **clé d’accès du stockage** de *Secondaire* à *Principale*, puis cliquez sur **ENREGISTRER**.
4. Retournez dans l'interface utilisateur de stockage, puis **régénérez** la *clé d'accès secondaire* (en vue du prochain cycle d'actualisation des clés).

## <a id="subheading-5"></a>Automation (PowerShell / API REST)
Vous pouvez aussi configurer l’audit dans Azure SQL Data Warehouse en utilisant les outils d’automation suivants :

* **Applets de commande PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

<!--Anchors-->
[Principes fondamentaux de l’audit de base de données]: #subheading-1
[Configuration de l'audit pour votre base de données]: #subheading-2
[Analyse des journaux et des rapports d’audit]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


<!--Link references-->
[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy
[107]: /powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy