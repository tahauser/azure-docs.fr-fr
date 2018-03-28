---
title: Bien démarrer avec l’audit de bases de données SQL Azure | Microsoft Docs
description: Utilisez la fonctionnalité d’audit d’Azure SQL Database pour effectuer le suivi des événements de base de données dans un journal d’audit.
services: sql-database
author: giladm
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 06/07/2017
ms.author: giladm
ms.openlocfilehash: 0339dc2ecc93fb9ec260ede9d3f2e9b7b1419b08
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-sql-database-auditing"></a>Bien démarrer avec l’audit de bases de données SQL
L’audit de bases de données SQL Azure suit les événements de base de données et les écrit dans un journal d’audit dans votre compte de stockage Azure. Par ailleurs, l’audit :

* peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données ainsi qu’à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité ;

* permet et facilite le respect de normes de conformité, même s’il ne garantit pas cette conformité. Pour plus d’informations sur les programmes Azure prenant en charge la conformité aux normes, voir le [Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Vue d’ensemble de l’audit be bases de données SQL Azure
Vous pouvez utiliser l’audit de bases de données SQL pour :


* **La rétention** d’une piste d’audit d’événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
* **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
* **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Vous pouvez configurer l’audit pour différents types de catégories d’événements, comme expliqué dans la section [Configuration de l’audit de votre base de données](#subheading-2).

> [!IMPORTANT]
> Les journaux d’audit sont écrits dans des **Blobs d’ajout** dans un stockage Blob Azure avec votre abonnement Azure.
>
> * **Stockage Premium** n’est actuellement  **pas pris en charge** par l’ajout d’objets blob.
> * Le **stockage dans un réseau virtuel** n’est actuellement **pas pris en charge**.

## <a id="subheading-8"></a>Définir une stratégie d’audit au niveau du serveur ou au niveau de la base de données

Une stratégie d’audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut :

* Une stratégie de serveur s’applique aux bases de données existantes et à celles qui sont nouvellement créées sur le serveur.

* Si *l’audit d’objets blob serveur est activé*, il *s’applique toujours à la base de données*. La base de données est auditée, quels que soient les paramètres d’audit de la base de données.

* L’activation de l’audit d’objets blob dans la base de données, en plus de son activation sur le serveur, ne remplace *pas* et ne modifie pas non plus les paramètres de l’audit d’objets blob serveur. Les deux audits coexistent. En d’autres termes, la base de données est auditée deux fois en parallèle (une fois par la stratégie du serveur et une autre fois par la stratégie de la base de données).

   > [!NOTE]
   > Vous devez éviter d’activer à la fois l’audit d’objets blob du serveur et l’audit d’objets blob de la base de données, sauf si :
    > * Vous voulez utiliser un autre *compte de stockage* ou une autre *période de rétention* pour une base de données spécifique.
    > * Vous souhaitez auditer des types ou des catégories d’événements pour une base de données qui sont différents de ceux qui sont audités pour les autres bases de données du serveur. Par exemple, il est possible que des insertions de table doivent être auditées uniquement pour une base de données spécifique.
   >
   > Sinon, nous vous recommandons d’activer uniquement l’audit d’objets blob au niveau du serveur et de laisser l’audit au niveau de la base de données désactivé pour toutes les bases de données.


## <a id="subheading-2"></a>Configuration de l’audit pour votre base de données
La section suivante décrit la configuration de l’audit à l’aide du portail Azure.

1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez au panneau **Paramètres** de la base de données/du serveur SQL que vous voulez auditer. Dans le panneau **Paramètres**, sélectionnez **Audit et détection des menaces**.

    <a id="auditing-screenshot"></a>![Volet de navigation][1]
3. Si vous préférez définir une stratégie d’audit de serveur, vous pouvez sélectionner le lien **Afficher les paramètres du serveur** dans le panneau d’audit de la base de données. Vous pouvez alors afficher ou modifier les paramètres d’audit du serveur. Les stratégies d’audit de serveur s’appliquent aux bases de données existantes et à celles qui sont nouvellement créées sur le serveur.

    ![Volet de navigation][2]
4. Si vous préférez activer l’audit d’objets blob au niveau de la base de données : pour **Audit**, sélectionnez **ACTIVÉ** et pour **Type d’audit**, sélectionnez **Objet blob**.

    Si l’audit d’objets blob du serveur est activé, l’audit configuré pour la base de données coexiste avec celui-ci.

    ![Volet de navigation][3]
5. Pour ouvrir le panneau **Stockage des journaux d’audit**, sélectionnez **Détails du stockage**. Sélectionnez le compte de stockage Azure dans lequel les journaux seront enregistrés, puis sélectionnez la période de rétention. Les anciens journaux seront supprimés. Cliquez ensuite sur **OK**.
   >[!TIP]
   >Pour tirer le meilleur parti des modèles des rapports d’audit, utilisez le même compte de stockage pour toutes les bases de données auditées.

    <a id="storage-screenshot"></a>![Volet de navigation][4]
6. Si vous souhaitez personnaliser les événements audités, vous pouvez le faire avec PowerShell ou l’API REST.
7. Une fois que vous avez configuré vos paramètres d’audit, vous pouvez activer la nouvelle fonctionnalité de détection des menaces et configurer les adresses e-mail de réception des alertes de sécurité. La détection des menaces vous permet de recevoir des alertes proactives sur des activités anormales de la base de données qui peuvent indiquer des menaces de sécurité potentielles. Pour plus d’informations, consultez [Bien démarrer avec la détection des menaces](sql-database-threat-detection-get-started.md).
8. Cliquez sur **Enregistrer**.





## <a id="subheading-3"></a>Analyse des journaux et des rapports d’audit
Les journaux d’audit sont agrégés dans le compte de stockage Azure choisi lors de la configuration. Vous pouvez explorer les journaux d’audit avec un outil comme [l’Explorateur de stockage Azure](http://storageexplorer.com/).

Les journaux d’audit d’objets blob sont enregistrés sous la forme d’une collection de fichiers d’objets blob dans un conteneur nommé **sqldbauditlogs**.

Pour plus d’informations sur la hiérarchie du dossier de stockage, sur les conventions de nommage et sur le format des journaux, consultez le [document de référence sur le format des journaux d’audit d’objets blob](https://go.microsoft.com/fwlink/?linkid=829599).

Plusieurs méthodes vous permettent d’afficher des journaux d’audit d’objets blob :

* Utilisez le [portail Azure](https://portal.azure.com).  Ouvrez la base de données appropriée. En haut du panneau **Audit et détection des menaces** de la base de données, cliquez sur **Afficher les journaux d’audit**.

    ![Volet de navigation][7]

    Un panneau **Enregistrements d’audit** s’ouvre, dans lequel s’affichent les journaux.

    - Vous pouvez afficher des dates spécifiques en cliquant sur **Filtrer** en haut du panneau **Enregistrements d’audit**.
    - Vous pouvez basculer entre les enregistrements d’audit qui ont été créés par un audit de stratégie de base de données ou de stratégie de serveur.

       ![Volet de navigation][8]

* Utilisez la fonction système **sys.fn_get_audit_file** (T-SQL) pour retourner les données du journal d’audit sous un format tabulaire. Pour plus d’informations sur l’utilisation de cette fonction, consultez la [documentation sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Utilisez **Fusionner les fichiers d’audit** dans SQL Server Management Studio (à partir de SSMS 17) :
    1. Dans le menu SSMS, sélectionnez **Fichier** > **Ouvrir** > **Fusionner les fichiers d’audit**.

        ![Volet de navigation][9]
    2. La boîte de dialogue **Ajouter des fichiers d’audit** s’ouvre. Sélectionnez l’une des options **Ajouter** pour choisir de fusionner les fichiers d’audit à partir d’un disque local ou de les importer à partir du stockage Azure. Vous devrez fournir vos informations de stockage Azure et la clé de compte.

    3. Une fois que tous les fichiers à fusionner ont été ajoutés, cliquez sur **OK** pour terminer l’opération de fusion.

    4. Le fichier fusionné s’ouvre dans SSMS, où vous pouvez l’afficher et l’analyser, ainsi que l’exporter vers un fichier XEL ou CSV, ou une table.

* Utilisez l’[application de synchronisation](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) que nous avons créée. Elle s’exécute dans Azure et utilise les API publiques OMS (Operations Management Suite) Log Analytics pour effectuer un push des journaux d’audit SQL vers OMS. L’application de synchronisation effectue un push des journaux d’audit SQL vers OMS Log Analytics pour les utiliser dans le tableau de bord OMS Log Analytics.

* Utilisez Power BI. Vous pouvez afficher et analyser les données du journal d’audit dans Power BI. Explorez [Power BI et accédez à un modèle téléchargeable](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Téléchargez les fichiers journaux à partir de votre conteneur Azure Storage Blob via le portail ou avec un outil comme [l’Explorateur de stockage Azure](http://storageexplorer.com/).
    * Une fois que vous avez téléchargé localement un fichier journal, vous pouvez double-cliquer sur le fichier pour ouvrir, afficher et analyser les journaux dans SSMS.
    * Vous pouvez également télécharger plusieurs fichiers simultanément avec l’Explorateur de stockage Azure. Cliquez avec le bouton droit sur un sous-dossier, puis sélectionnez **Enregistrer en tant que** pour enregistrer dans un dossier local.

* Autres méthodes :
   * Après avoir téléchargé plusieurs fichiers (ou un sous-dossier contenant des fichiers journaux), vous pouvez les fusionner localement en suivant les instructions relatives à l’option Fusionner les fichiers d’audit dans SSMS décrites précédemment.

   * Affichez des journaux d’audit d’objets blob par programmation :

     * Utilisez la bibliothèque C# [Lecteur des événements étendus](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/).
     * [Interrogez des fichiers d’événements étendus](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) avec PowerShell.




## <a id="subheading-5"></a>Pratiques de production
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Audit des bases de données géorépliquées</a>
Avec les bases de données géorépliquées, lorsque vous activez l’audit dans la base de données primaire, la même stratégie d’audit est appliquée à la base de données secondaire. Il est également possible de configurer l’audit dans la base de données secondaire en activant l’audit dans le **serveur secondaire**, indépendamment de la base de données primaire.

* Au niveau du serveur (**recommandé**) : Activez l’audit dans le **serveur principal** et le **serveur secondaire**. Les bases de données primaire et secondaire sont auditées, indépendamment l’une de l’autre, en fonction de leur stratégie de niveau serveur respective.

* Au niveau de la base de données : L’audit pour les bases de données secondaires peut uniquement être configuré à partir des paramètres d’audit de la base de données primaire.
   * L’audit Objet blob doit être activé sur la *base de données primaire elle-même*, et non pas sur le serveur.
   * Une fois que l’audit d’objets blob est activé sur la base de données primaire, il est également activé sur la base de données secondaire.

     >[!IMPORTANT]
     >Avec l’audit au niveau de la base de données, les paramètres de stockage de la base de données secondaire sont identiques à ceux de la base de données primaire, ce qui entraîne un trafic entre régions. Il est conseillé d’activer uniquement l’audit d’objets blob au niveau du serveur et de laisser l’audit au niveau de la base de données désactivé pour toutes les bases de données.
<br>

### <a id="subheading-6">Régénération des clés de stockage</a>
Dans un environnement de production, vous allez probablement actualiser périodiquement vos clés de stockage. Lors de l’actualisation de vos clés, vous devez réenregistrer la stratégie d’audit. Pour ce faire, procédez comme suit :

1. Ouvrez le panneau **Détails du stockage**. Dans la zone **Clé d’accès de stockage**, sélectionnez **Secondaire**, puis cliquez sur **OK**. Cliquez ensuite sur **Enregistrer** en haut du panneau de configuration de l’audit.

    ![Volet de navigation][5]
2. Accédez au panneau de configuration du stockage, puis régénérez la clé d’accès primaire.

    ![Volet de navigation][6]
3. Revenez au panneau de configuration de l’audit, remplacez la clé d’accès de stockage secondaire par la clé primaire, puis cliquez sur **OK**. Cliquez ensuite sur **Enregistrer** en haut du panneau de configuration de l’audit.
4. Revenez au panneau de configuration du stockage, puis régénérez la clé d’accès secondaire (en préparation du cycle suivant d’actualisation des clés).

## <a name="manage-sql-database-auditing-using-azure-powershell"></a>Gérer l’audit de base de données SQL avec Azure PowerShell


* **Applets de commande PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditing][101]
   * [Get-AzureRMSqlServerAuditing][102]
   * [Set-AzureRMSqlDatabaseAuditing][105]
   * [Set-AzureRMSqlServerAuditing][106]

   Pour obtenir un exemple de script, consultez [Configurer l’audit et la détection des menaces avec PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="manage-sql-database-auditing-using-rest-api"></a>Gérer l’audit de base de données SQL à l’aide de l’API REST

* **API REST - Audit d’objets blob** :

   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/library/azure/mt771862.aspx)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
