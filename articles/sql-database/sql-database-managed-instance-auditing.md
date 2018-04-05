---
title: Audit d’Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrez comment prendre en main l’audit d’Azure SQL Database Managed Instance à l’aide de T-SQL
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/19/2018
ms.author: giladm
ms.openlocfilehash: 3d5a4ad3f4046dfdfe6eb3f7ddd931ccb240b1a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Prendre en main l’audit d’Azure SQL Database Managed Instance

L’audit [d’Azure SQL Database Managed Instance](sql-database-managed-instance.md) suit les événements de base de données et les écrit dans un journal d’audit dans votre compte de stockage Azure. Par ailleurs, l’audit :
- peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données ainsi qu’à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité ;
- permet et facilite le respect de normes de conformité, même s’il ne garantit pas cette conformité. Pour plus d’informations sur les programmes Azure prenant en charge la conformité aux normes, voir le [Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Configurer l’audit de votre serveur

La section suivante décrit la configuration de l’audit à l’aide de votre instance Managed Instance.
1. Accédez au [portail Azure](https://portal.azure.com).
2. Les étapes suivantes permettent de créer un **conteneur** Stockage Azure où sont stockés les journaux d’audit.

   - Accédez au stockage Azure où vous souhaitez stocker vos journaux d’audit.

     > [!IMPORTANT]
     > Utilisez un compte de stockage dans la même région que le serveur Managed Instance afin d’éviter des lectures/écritures entre régions.

   - Dans le compte de stockage, accédez à **Vue d’ensemble**, puis cliquez sur **Objets blob**.

     ![Volet de navigation][1]

   - Dans le menu supérieur, cliquez sur **+ Conteneur** pour créer un conteneur.

     ![Volet de navigation][2]

   - Indiquez un **Nom** pour le conteneur, définissez le niveau d’accès Public sur **Privé**, puis cliquez sur **OK**.

     ![Volet de navigation][3]

   - Dans la liste des conteneurs, cliquez sur le conteneur que vous venez de créer, puis sur **Propriétés du conteneur**.

     ![Volet de navigation][4]

   - Copiez l’URL du conteneur en cliquant sur l’icône Copier, puis enregistrez-la (par exemple, dans le Bloc-notes) pour l’utiliser ultérieurement. L’URL du conteneur doit être au format `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Volet de navigation][5]

3. Les étapes suivantes permettent de générer un **jeton SAP** Stockage Azure utilisé pour accorder au compte de stockage des droits d’accès sur l’audit de Managed Instance.

   - Accédez au compte Stockage Azure dans lequel vous avez créé le conteneur à l’étape précédente.

   - Dans le menu Paramètres de stockage, cliquez sur **Signature d’accès partagé**.

     ![Volet de navigation][6]

   - Configurez la signature d’accès partagé comme suit :
     - **Services autorisés** : objet blob
     - **Date de début** : pour éviter tout problème lié au fuseau horaire, il est recommandé d’utiliser la date de la veille.
     - **Date de fin** : choisissez la date à laquelle ce jeton SAP arrive à expiration. 

       > [!NOTE]
       > À l’expiration, renouvelez le jeton afin d’éviter les échecs d’audit.

     - Cliquez sur **Générer une signature d’accès partagé**.

       ![Volet de navigation][7]

   - Après que vous avez cliqué sur Générer une signature d’accès partagé, le jeton SAP s’affiche en bas de la fenêtre. Copiez le jeton en cliquant sur l’icône Copier, puis enregistrez-le (par exemple, dans le Bloc-notes) pour l’utiliser ultérieurement.

     > [!IMPORTANT]
     > Supprimez le point d’interrogation (« ? ») au début du jeton.

     ![Volet de navigation][8]

4. Connectez-vous à votre instance Managed Instance via SQL Server Management Studio (SSMS).

5. Exécutez l’instruction T-SQL suivante pour **créer des informations d’identification** à l’aide de l’URL du conteneur et du jeton SAP que vous avez créés dans les étapes précédentes :

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Exécutez l’instruction T-SQL suivante pour créer un audit de serveur (choisissez votre propre nom d’audit et utilisez l’URL du conteneur que vous avez créée à l’étape précédente) :

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Si aucune valeur n’est spécifiée, la valeur par défaut du paramètre `RETENTION_DAYS` est 0 (rétention illimitée).

    Pour toute information supplémentaire :
    - [Audit des différences entre Managed Instance, Azure SQL DB et SQL Server](#subheading-3)
    - [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Créez une spécification de l’audit du serveur ou une spécification de l’audit de la base de données comme vous le feriez pour SQL Server :
    - [CREATE SERVER AUDIT SPECIFICATION (Transact-SQL)](https://docs.microsoft.com/ sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [CREATE DATABASE AUDIT SPECIFICATION (Transact-SQL)](https://docs.microsoft.com/ sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Activez l’audit du serveur que vous avez créé à l’étape 6 :

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Analyser les journaux d’audit
Plusieurs méthodes vous permettent d’afficher des journaux d’audit d’objets blob.

- Utilisez la fonction système `sys.fn_get_audit_file` pour retourner les données du journal d’audit dans un format tabulaire. Pour plus d’informations sur l’utilisation de cette fonction, consultez la [documentation sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Pour obtenir la liste complète des méthodes de consommation du journal d’audit, reportez-vous à l’article [Bien démarrer avec l’audit de bases de données SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> La méthode permettant d’afficher des enregistrements d’audit à partir du portail Azure (volet « Enregistrements d’audit ») n’est pas disponible pour le moment pour Managed Instance.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Audit des différences entre Managed Instance, Azure SQL DB et SQL Server

Les principales différences entre SQL Audit dans Managed Instance, Azure SQL Database et SQL Server en local sont :

- Dans Managed Instance, SQL Audit fonctionne au niveau du serveur et stocke les fichiers journaux `.xel` dans le compte de stockage Blob Azure.
- Dans Azure SQL Database, SQL Audit fonctionne au niveau de la base de données.
- Dans SQL Server (en local ou dans les machines virtuelles), SQL Audit fonctionne au niveau du serveur, mais stocke les événements dans les journaux des événements du système de fichiers/Windows.

L’audit XEvent sur Managed Instance prend en charge les cibles de Stockage Blob Azure. Les journaux de fichiers et de Windows ne sont **pas pris en charge**.

Les principales différences de syntaxe `CREATE AUDIT` pour l’audit du Stockage Blob Azures sont :
- Une nouvelle syntaxe `TO URL` est fournie et vous permet de spécifier l’URL du conteneur du Stockage Blob Azure où les fichiers `.xel` sont placés.
- La syntaxe `TO FILE` **n’est pas prise en charge**, car Managed Instance ne peut pas accéder aux partages de fichiers Windows.
- L’option d’arrêt n’est **pas prise en charge**.
- La valeur 0 du paramètre `queue_delay` n’est **pas prise en charge**.


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir la liste complète des méthodes de consommation du journal d’audit, reportez-vous à l’article [Bien démarrer avec l’audit de bases de données SQL](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- Pour plus d’informations sur les programmes Azure prenant en charge la conformité aux normes, voir le [Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/compliance/).


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
