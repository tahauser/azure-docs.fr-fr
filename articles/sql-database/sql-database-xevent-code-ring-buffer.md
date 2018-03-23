---
title: Code de la mémoire tampon en anneau XEvent pour SQL Database | Microsoft Docs
description: Fournit un exemple de code Transact-SQL simple et rapide en utilisant la cible de la mémoire tampon en anneau, dans Azure SQL Database.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/03/2017
ms.author: genemi
ms.openlocfilehash: f553933d5a1435a96421939af8522daf53db1ff3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Code cible de la mémoire tampon en anneau pour les événements étendus dans SQL Database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Vous pouvez utiliser un exemple de code complet pour capturer et signaler le plus simplement et rapidement possible les informations liées à un événement étendu pendant un test. La cible la plus simple pour les données d’événement étendu est la [cible de la mémoire tampon en anneau](http://msdn.microsoft.com/library/ff878182.aspx).

Cette rubrique présente un exemple de code Transact-SQL qui :

1. Crée une table contenant des données pour la démonstration.
2. Crée une session pour un événement étendu existant, à savoir **sqlserver.sql_statement_starting**.
   
   * L’événement est limité aux instructions SQL qui contiennent une chaîne Update particulière : **statement LIKE '%UPDATE tabEmployee%'**.
   * Choisit d’envoyer la sortie de l’événement vers une cible de type mémoire tampon en anneau, à savoir **package0.ring_buffer**.
3. Démarre la session d’événement.
4. Émet un ensemble d’instructions SQL UPDATE simples.
5. Émet une instruction SQL SELECT pour récupérer la sortie d’événement de la mémoire tampon en anneau.
   
   * **sys.dm_xe_database_session_targets** et d’autres vues de gestion dynamique (DMV) sont incluses.
6. Arrête la session d’événement.
7. Supprime la cible de la mémoire tampon en anneau pour libérer ses ressources.
8. Supprime la session d’événement et la table de démonstration.

## <a name="prerequisites"></a>Prérequis


* Un compte et un abonnement Azure. Vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Une base de données dans laquelle vous pouvez créer une table.
  
  * Vous pouvez aussi [créer une base de données de démonstration **AdventureWorksLT**](sql-database-get-started.md) en quelques minutes.
* SQL Server Management Studio (ssms.exe), dans l’idéal, la version de sa dernière mise à jour mensuelle. 
  Vous pouvez télécharger la dernière version de ssms.exe :
  
  * À partir de la rubrique [Télécharger SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [En utilisant un lien direct vers le téléchargement.](http://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Exemple de code

Après quelques modifications mineures, vous pouvez exécuter l’exemple suivant de code de mémoire tampon en anneau sur Azure SQL Database ou Microsoft SQL Server. La différence se limite à la présence du nœud « _database » dans le nom de certaines vues de gestion dynamique (DMV) utilisées dans la clause FROM à l’étape 5. Par exemple : 

* sys.dm_xe**_database**_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;

## <a name="ring-buffer-contents"></a>Contenu de la mémoire tampon en anneau

Nous avons utilisé ssms.exe pour exécuter l’exemple de code.

Pour afficher les résultats, nous avons cliqué sur la cellule sous l’en-tête de colonne **target_data_XML**.

Puis, dans le volet de résultats, nous avons cliqué sur la cellule sous l’en-tête de colonne **target_data_XML**. Ce clic a créé un autre onglet Fichier dans ssms.exe pour afficher (au format XML) le contenu de la cellule de résultat.

La sortie est présentée dans le bloc suivant. Elle semble longue, mais ne comprend que deux éléments **<event>** .

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Libérer les ressources détenues par votre mémoire tampon en anneau

Quand vous n’avez plus besoin de votre mémoire tampon en anneau, vous pouvez la supprimer et libérer ses ressources à l’aide d’une instruction **ALTER** , comme suit :

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


La définition de votre session d’événement est mise à jour, mais pas supprimée. Vous pouvez ajouter ultérieurement une autre instance de la mémoire tampon en anneau à votre session d’événement :

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Plus d’informations

La rubrique principale sur les événements étendus dans Base de données SQL Azure est :

* La rubrique [Considérations relatives aux événements étendus dans Azure SQL Database](sql-database-xevent-db-diff-from-svr.md) décrit les différences à prendre en compte entre les événements étendus dans Azure SQL Database et ceux dans Microsoft SQL Server.

Vous trouverez d’autres rubriques d’exemples de code pour les événements étendus en suivant le lien ci-dessous. Toutefois, vous devez vérifier régulièrement les exemples pour voir s’ils ciblent Microsoft SQL Server ou la base de données SQL Azure. Vous pouvez ensuite déterminer si vous devez apporter quelques modifications mineures avant d’exécuter un exemple.

* Exemple de code pour Azure SQL Database : [Code cible du fichier d’événements pour les événements étendus dans SQL Database](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
