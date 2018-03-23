---
title: Corriger une erreur de connexion SQL, erreur temporaire | Microsoft Docs
description: Découvrez comment diagnostiquer, résoudre et empêcher une erreur de connexion SQL ou une erreur temporaire dans Base de données SQL Azure.
keywords: connexion SQL,chaîne de connexion,problèmes de connectivité,erreur temporaire,erreur de connexion
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 11/29/2017
ms.author: daleche
ms.openlocfilehash: f6b5f825d7f8111075fe37b5dc29d174928d913e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Diagnostiquer, résoudre et empêcher les erreurs de connexion SQL et les erreurs temporaires de Base de données SQL
Cet article décrit comment empêcher, résoudre, diagnostiquer et limiter les erreurs de connexion et les erreurs temporaires que votre application cliente rencontre lorsqu’elle interagit avec Base de données SQL Azure. Découvrez comment configurer une logique de nouvelle tentative, générer la chaîne de connexion et ajuster les autres paramètres de connexion.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Erreurs temporaires
Une erreur temporaire s’explique par une cause sous-jacente qui se résout d’elle-même en peu de temps. Les erreurs temporaires surviennent de temps en temps lorsque le système Azure réaffecte rapidement des ressources matérielles pour mieux équilibrer les différentes charges de travail. La plupart de ces événements de reconfiguration se terminent en moins de 60 secondes. Durant cette reconfiguration, vous pouvez rencontrer des problèmes de connectivité avec SQL Database. Les applications qui se connectent à SQL Database doivent être conçues de sorte à s’attendre à de telles erreurs temporaires. Pour les gérer, implémentez une logique de nouvelle tentative dans leur code au lieu de les exposer aux utilisateurs comme des erreurs d’application.

Si votre programme client utilise ADO.NET, votre programme est informé de l’erreur temporaire par la levée d’une exception **SqlException**. Comparez la propriété **Number** à la liste des erreurs temporaires mentionnées au début de l’article [Codes d’erreur SQL pour les applications clientes SQL Database](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Connexion et commande
Retentez la connexion SQL ou rétablissez-la, en fonction de ce qui suit :

* **Une erreur temporaire survient pendant une tentative de connexion** : retentez la connexion après un délai de quelques secondes.
* **Une erreur temporaire se produit pendant une commande de requête SQL** : ne retentez pas cette dernière immédiatement. Établissez plutôt la connexion après un certain délai. Retentez ensuite la commande.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logique de nouvelle tentative pour les erreurs temporaires
Les programmes clients qui rencontrent occasionnellement une erreur temporaire sont plus solides lorsqu’ils contiennent une logique de nouvelle tentative.

Si votre programme communique avec SQL Database par le biais d’un intergiciel (middleware) tiers, demandez au fournisseur s’il contient une logique de nouvelle tentative pour les erreurs temporaires.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principes de nouvelle tentative
* Si l’erreur est temporaire, réessayez d’ouvrir une connexion.
* Ne retentez pas directement une instruction SQL SELECT qui a échoué avec une erreur temporaire.
  * Au lieu de cela, établir une nouvelle connexion et relancer l’instruction SELECT.
* Si une instruction SQL UPDATE échoue avec une erreur temporaire, établissez une nouvelle connexion avant de retenter l’instruction UPDATE.
  * La logique de nouvelle tentative doit s’assurer que la transaction de base de données est complètement terminée ou que la transaction entière a été annulée.

### <a name="other-considerations-for-retry"></a>Autres considérations
* Un fichier de commandes qui démarre automatiquement après les heures de travail et qui s’achève avant le matin peut se permettre d’attendre très longtemps entre deux tentatives.
* Un programme d’interface utilisateur doit prendre en compte la tendance qu’ont les hommes à l’abandon en cas d’attente trop longue.
  * La solution ne consiste pas à retenter l’opération à chaque seconde, car une telle stratégie pourrait submerger le système de requêtes.

### <a name="interval-increase-between-retries"></a>Augmentation de l’intervalle entre les tentatives
Nous vous recommandons de patienter 5 secondes avant votre première nouvelle tentative. Si vous effectuez une nouvelle tentative avant 5 secondes, vous risquez de submerger le service cloud. Pour chaque nouvelle tentative suivante, le délai doit augmenter de manière exponentielle, sans dépasser 60 secondes.

Pour en savoir plus sur la période de blocage des clients qui utilisent ADO.NET, consultez [Regroupement de connexions SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Vous pouvez également définir le nombre maximal de tentatives avant l’arrêt automatique du programme.

### <a name="code-samples-with-retry-logic"></a>Exemples de code avec la logique de nouvelle tentative
Des exemples de code avec logique de nouvelle tentative sont disponibles aux emplacements suivants :

- [Connexion résiliente à SQL avec ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Connexion résiliente à SQL avec PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Tester votre logique de nouvelle tentative
Pour tester la logique de nouvelle tentative, vous devez simuler ou provoquer une erreur pouvant être corrigée alors que votre programme est en cours d’exécution.

#### <a name="test-by-disconnecting-from-the-network"></a>Test par le biais de la déconnexion du réseau
Pour tester votre logique de nouvelle tentative, vous pouvez déconnecter votre ordinateur client du réseau pendant l’exécution du programme. L’erreur est :

* **SqlException.Number** = 11001
* Message : « Hôte inconnu »

Dans le cadre de la première nouvelle tentative, votre programme peut corriger les fautes d’orthographe et tenter de se connecter.

Pour concrétiser ce test, débranchez votre ordinateur du réseau avant lancer votre programme. Votre programme reconnaît alors un paramètre d’exécution qui fait en sorte que le programme :

* Ajoute temporairement 11001 à sa liste d’erreurs à considérer comme provisoire.
* Tente sa première connexion comme d’habitude.
* Une fois l’erreur détectée, supprime 11001 de sa liste.
* Affiche un message indiquant à l’utilisateur de connecter l’ordinateur au réseau.
   * Suspend toute exécution à l’aide de la méthode **Console.ReadLine** ou d’une boîte de dialogue contenant un bouton OK. L’utilisateur appuie sur la touche Entrée après la connexion de l’ordinateur au réseau.
* Essaie de nouveau de se connecter, et attend la réussite.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Teste en fournissant un nom de base de données mal orthographié au moment de la connexion
Votre programme peut délibérément mal orthographier le nom d’utilisateur avant la première tentative de connexion. L’erreur est :

* **SqlException.Number** = 18456
* Message : « Échec de la connexion pour l’utilisateur 'WRONG_MyUserName' »

Dans le cadre de la première nouvelle tentative, votre programme peut corriger les fautes d’orthographe et tenter de se connecter.

Pour mettre ce test en pratique, votre programme reconnaît un paramètre d’exécution, qui fait en sorte que le programme :

* Ajoute temporairement 18456 à sa liste d’erreurs à prendre en compte comme provisoire.
* Ajoute volontairement ’WRONG_’ au nom d’utilisateur.
* Une fois l’erreur détectée, supprime 18456 de la liste.
* Supprime ’WRONG_’ du nom d’utilisateur.
* Essaie de nouveau de se connecter, et attend la réussite.


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Paramètres de connexion .NET Sql pour les nouvelles tentatives de connexion
Si votre programme client se connecte à SQL Database à l’aide de la classe .NET Framework **System.Data.SqlClient.SqlConnection**, utilisez .NET 4.6.1 ou une version ultérieure (ou .NET Core) afin de pouvoir utiliser la fonctionnalité de nouvelle tentative de connexion. Pour plus d’informations sur la fonctionnalité, consultez [cette page web](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Lorsque vous générez la [chaîne de connexion](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) pour votre objet **SqlConnection**, coordonnez les valeurs entre les paramètres suivants :

* **ConnectRetryCount** :&nbsp;&nbsp;La valeur par défaut est 1. La plage s’étend de 0 à 255.
* **ConnectRetryInterval** :&nbsp;&nbsp;La valeur par défaut est 1 seconde. La plage s’étend de 1 à 60.
* **ConnectionTimeout** :&nbsp;&nbsp;La valeur par défaut est 15 secondes. La plage s’étend de 0 à 2 147 483 647.

Plus précisément, les valeurs que vous choisissez doivent vérifier la formule suivante :

Délai d’expiration de connexion = ConnectRetryCount × ConnectionRetryInterval

Par exemple, si le nombre est égal à 3 et que l’intervalle s’élève à 10 secondes, un délai d’expiration de 29 secondes seulement ne laisse pas suffisamment de temps au système pour sa 3e et dernière tentative de connexion, 29 étant inférieur à 3 × 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Connexion et commande
Les paramètres **ConnectRetryCount** et **ConnectRetryInterval** permettent à votre objet **SqlConnection** de recommencer l’opération de connexion sans notification à votre programme ou renvoi du contrôle à celui-ci. Les nouvelles tentatives peuvent se produire dans les situations suivantes :

* Appel de méthode mySqlConnection.Open
* Appel de méthode mySqlConnection.Execute

Il existe une subtilité. Si une erreur temporaire se produit pendant l’exécution de votre *requête*, votre objet **SqlConnection** ne retente pas l’opération de connexion. Il ne relance certainement pas votre requête. Toutefois, **SqlConnection** vérifie très rapidement la connexion avant d’envoyer votre requête pour exécution. Si la vérification rapide détecte un problème de connexion, **SqlConnection** réessaye l’opération de connexion. Si la nouvelle tentative réussit, votre requête est envoyée pour exécution.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Le paramètre ConnectRetryCount doit-il être combiné avec la logique de nouvelle tentative d’application ?
Supposons que votre application possède une logique de nouvelle tentative personnalisée robuste. Elle peut réessayer l’opération de connexion quatre fois. Si vous ajoutez **ConnectRetryInterval** et **ConnectRetryCount** = 3 à votre chaîne de connexion, vous augmentez le nombre de nouvelles tentatives à 4 * 3, soit 12 nouvelles tentatives. Vous ne souhaitez peut-être pas un si grand nombre de nouvelles tentatives.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Connexions à SQL Database
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Connexion : chaîne de connexion
La chaîne de connexion nécessaire pour se connecter à SQL Database est légèrement différente de la chaîne utilisée pour se connecter à SQL Server. Il est possible de copier la chaîne de connexion de votre base de données à partir du [portail Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Connexion : adresse IP
Vous devez configurer le serveur de base de données SQL pour accepter les communications à partir de l’adresse IP de l’ordinateur qui héberge votre programme client. Pour définir cette configuration, modifiez les paramètres du pare-feu via le [portail Azure](https://portal.azure.com/).

Si vous oubliez de configurer l’adresse IP, votre programme échoue en envoyant un message d’erreur pratique indiquant l’adresse IP nécessaire.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Pour plus d’informations, consultez [Configurer les paramètres du pare-feu sur SQL Database](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Connexion : ports
En règle générale, vous devez simplement vous assurer que le port 1433 est ouvert pour la communication sortante sur l’ordinateur qui héberge le programme client.

Par exemple, lorsque votre programme client est hébergé sur un ordinateur Windows, vous pouvez utiliser le pare-feu Windows sur l’hôte pour ouvrir le port 1433.

1. Ouvrez le Panneau de configuration.

2. Sélectionnez **Tous les éléments du Panneau de configuration** > **Pare-feu Windows** > **Paramètres avancés** > **Règles de trafic sortant**   >  **Actions** > **Nouvelle règle**.

Si votre programme client est hébergé sur une machine virtuelle Azure, lisez [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

Pour obtenir des informations générales sur la configuration des ports et des adresses IP, consultez [Pare-feu Azure SQL Database](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Connexion : ADO.NET 4.6.1
Si votre programme utilise des classes ADO.NET comme **System.Data.SqlClient.SqlConnection** pour se connecter à SQL Database, nous vous recommandons d’utiliser .NET Framework version 4.6.1 ou ultérieure.

ADO.NET 4.6.1 :

* Pour SQL Database, l’ouverture d’une connexion à l’aide de la méthode **SqlConnection.Open** en améliore la fiabilité. La méthode **Open** intègre désormais de meilleurs mécanismes de nouvelle tentative en réponse à des défaillances temporaires, pour certaines erreurs se produisant avant le délai d’expiration de la connexion.
* Le regroupement de connexions est pris en charge, ce qui contribue à garantir que l’objet de connexion qu’il donne à votre programme fonctionne.

Lorsque vous utilisez un objet de connexion à partir d’un pool de connexions, nous conseillons de faire en sorte que votre programme interrompe temporairement la connexion lorsque vous ne l’utilisez pas immédiatement. Il n’est pas coûteux de rouvrir une connexion, ce qui n’est pas le cas de la création d’une connexion.

Si vous utilisez ADO.NET 4.0 ou version antérieure, nous vous recommandons d’effectuer une mise à niveau vers la dernière version d’ADO.NET. Depuis novembre 2015, [ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx)est disponible au téléchargement.

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostics
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostic : vérifier si les utilitaires peuvent se connecter
Si votre programme ne parvient pas à se connecter à SQL Database, une option de diagnostic consiste à essayer de se connecter avec un programme utilitaire. Dans l’idéal, l’utilitaire se connecte à l’aide de la bibliothèque que votre programme utilise.

Sur un ordinateur Windows, vous pouvez essayer ces utilitaires :

* SQL Server Management Studio (ssms.exe), qui se connecte à l’aide d’ADO.NET
* sqlcmd.exe, qui se connecte en utilisant [ODBC](http://msdn.microsoft.com/library/jj730308.aspx)

Une fois votre programme connecté, faites un test avec une courte requête SQL SELECT.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostic : vérifier les ports ouverts
Si vous pensez que les tentatives de connexion échouent en raison de problèmes de port, vous pouvez exécuter un utilitaire sur votre ordinateur pour obtenir des rapports sur les configurations de port.

Sur Linux, les utilitaires suivants peuvent vous être utiles :

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * Modifiez la valeur de l’exemple pour refléter votre adresse IP.

Sur Windows, l’utilitaire [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) peut s’avérer utile. Voici un exemple d’exécution qui a demandé l’état d’un port sur un serveur SQL Database et qui a été exécuté sur un ordinateur portable :

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostic : consignation des erreurs dans un journal
Un problème intermittent est parfois mieux diagnostiqué par la détection d’une tendance générale observée sur plusieurs jours ou semaines.

Votre client peut aider à consigner toutes les erreurs qu’il rencontre un diagnostic. Vous pouvez mettre en corrélation les entrées de journal avec des informations sur les erreurs de base consignées en interne par SQL Database lui-même.

Enterprise Library 6 (EntLib60) offre des classes .NET gérées afin de faciliter la journalisation. Pour plus d’informations, consultez [5 - Un jeu d’enfants : utilisation du bloc d’application de journalisation](http://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostics : examinez les journaux d’erreur système
Voici quelques instructions Transact-SQL SELECT qui permettent d’interroger les journaux d’erreur et d’autres informations.

| Interrogation de journaux | Description |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |La vue [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) propose des informations sur les événements individuels, notamment ceux qui peuvent causer des erreurs temporaires ou des problèmes de connectivité.<br/><br/>Dans l’idéal, vous pouvez mettre en corrélation les valeurs **start_time** ou **end_time** avec les informations indiquant quand votre programme client a rencontré des problèmes.<br/><br/>Vous devez vous connecter à la base de données *MASTER* pour exécuter cette requête. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |La vue [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) agrège des nombres de différents types d’événements, pour permettre des diagnostics supplémentaires.<br/><br/>Vous devez vous connecter à la base de données *MASTER* pour exécuter cette requête. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostics : Rechercher les événements liés aux problèmes dans le journal de base de données SQL
Vous pouvez rechercher des entrées sur les problèmes survenus dans le journal de SQL Database. Essayez l’instruction Transact-SQL SELECT qui suit dans la base de données *MASTER* :

```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>quelques-unes d’entre elles ont renvoyé des lignes de sys.fn_xe_telemetry_blob_target_read_file
L’exemple suivant montre à quoi peut ressembler une ligne retournée. Les valeurs null indiquées ne sont en général pas nulles dans d’autres lignes.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
Enterprise Library 6 (EntLib60) est une infrastructure de classes .NET qui vous permet d’implémenter des clients de cloud fiables, et notamment le service SQL Database. Pour rechercher des rubriques dédiées à chaque zone dans laquelle EntLib60 peut être utile, consultez [Enterprise Library 6 - avril 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

La logique de nouvelle tentative pour la gestion des erreurs temporaires est un domaine où EntLib60 peut être utile. Pour plus d’informations, consultez [4 - La persévérance, le secret de la réussite : utilisation du bloc applicatif de gestion des erreurs temporaires](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Le code source pour EntLib60 est publiquement disponible par téléchargement depuis le [Centre de téléchargement](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft ne prévoit pas d’apporter des mises à jour de maintenance ou de fonctionnalité supplémentaires à EntLib.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classes EntLib60 pour les erreurs temporaires et les nouvelles tentatives
Les classes EntLib60 suivantes sont particulièrement utiles pour la logique de nouvelle tentative. Toutes ces classes se trouvent dans ou sous l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

Dans l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** :

* **RetryPolicy**
  
  * **ExecuteAction**
* **ExponentialBackoff**
* **SqlDatabaseTransientErrorDetectionStrategy**
* **ReliableSqlConnection**
  
  * **ExecuteCommand**

Dans l’espace de noms **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy**
* **NeverTransientErrorDetectionStrategy**

Voici quelques liens vers des informations sur EntLib60 :

* Livre électronique : [Guide du développeur de Microsoft Enterprise Library, 2e édition](http://www.microsoft.com/download/details.aspx?id=41145).
* Meilleures pratiques : [Conseils généraux sur les nouvelles tentatives](../best-practices-retry-general.md) comprend une excellente présentation approfondie de la logique de nouvelle tentative.
* Téléchargement NuGet de [Bibliothèque d’entreprise - Bloc applicatif de gestion des erreurs 6.0 temporaires de Microsoft](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60 : le bloc de journalisation
* Le bloc de journalisation est une solution très flexible et configurable qui vous permet de :
  
  * Créer et stocker des messages du journal dans de nombreux emplacements.
  * Classer et filtrer les messages.
  * Recueillir des informations contextuelles utiles pour le débogage et le suivi, ainsi que pour les exigences d’audit et de journalisation en général.
* Le bloc de journalisation extrait les fonctionnalités issues de la destination de journalisation de façon que le code d’application soit cohérent, quels que soient l’emplacement et le type du magasin de journalisation cible.

Pour plus d’informations, consultez [5 - Un jeu d’enfants : utilisation du bloc d’application de journalisation](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Code source de la méthode EntLib60 IsTransient
Ensuite, à partir de la classe **SqlDatabaseTransientErrorDetectionStrategy**, le code source C# pour la méthode **IsTransient**. Le code source clarifie les erreurs considérées comme temporaires qui peuvent faire l’objet de nouvelles tentatives depuis avril 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la résolution d’autres problèmes de connexion courants à SQL Database, consultez [Résolution des problèmes de connexion à Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).
* [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)
* [Regroupement de connexions SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*Nouvelle tentative* est une bibliothèque de nouvelle tentative sous licence Apache 2.0 à usage général écrite en langage Python,](https://pypi.python.org/pypi/retrying) pour simplifier la tâche consistant à ajouter des comportements de nouvelle tentative dans toutes les situations.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

