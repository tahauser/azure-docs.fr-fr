---
title: "Sorties Stream Analytics : options de stockage et d’analyse | Microsoft Docs"
description: "Découvrez les options de sorties de données Stream Analytics, notamment Power BI pour les résultats de l’analyse."
keywords: "transformation de données, résultats d’analyse, options de stockage de données"
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: 
author: SnehaGunda
manager: kfile
editor: cgronlun
ms.assetid: ba6697ac-e90f-4be3-bafd-5cfcf4bd8f1f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/18/2017
ms.author: sngun
ms.openlocfilehash: 6df9a3fafea97638d63c0dc4601c5ced357c410d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Sorties Stream Analytics : options de stockage, d’analyse
Lorsque vous créez une tâche Stream Analytics, songez à la façon dont les données obtenues devront être consommées. Comment allez-vous afficher les résultats du travail Stream Analytics et où allez-vous les stocker ?

Pour permettre un éventail de modèles d’application, Azure Stream Analytics propose différentes options pour stocker et afficher les résultats de l’analyse. Cela vous permet d’afficher plus facilement la sortie des tâches et de rendre plus flexibles leur consommation et leur stockage pour l’entreposage de données et d’autres utilisations. Toute sortie configurée dans la tâche doit exister avant le démarrage de la tâche et avant le début du transit des événements. Par exemple, si vous utilisez le stockage d’objets blob en tant que sortie, le travail ne crée aucun compte de stockage automatiquement. Créez un compte de stockage avant le début du travail Stream Analytics.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics prend en charge [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Ce type de stockage vous permet de stocker des données de toute taille, de tout type et de toute vitesse d’ingestion en vue d’une analyse opérationnelle et exploratoire. De plus, Stream Analytics doit être autorisé à accéder à Data Lake Store. Des informations détaillées sur les autorisations et l’inscription à Data Lake Store (si nécessaire) sont fournies l’article sur les [sorties Data Lake](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autoriser un Azure Data Lake Store
Lorsque Data Lake Storage est sélectionné en tant que sortie dans le portail Azure, vous êtes invité à autoriser une connexion à un Data Lake Store existant.  

![Autoriser Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Remplissez ensuite les propriétés de la sortie Data Lake Store comme indiqué ci-dessous :

![Autoriser Data Lake Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

Le tableau ci-dessous répertorie les noms et les descriptions des propriétés nécessaires à la création d’une sortie Data Lake Store.

<table>
<tbody>
<tr>
<td><B>NOM DE LA PROPRIÉTÉ</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Nom convivial utilisé dans les requêtes pour diriger la sortie de la requête vers Data Lake Store.</td>
</tr>
<tr>
<td>Nom du compte</td>
<td>Nom du compte de stockage Data Lake Storage où vous envoyez votre sortie. Vous voyez s’afficher la liste déroulante des comptes Data Lake Store auxquels ont accès les utilisateurs connectés au portail.</td>
</tr>
<tr>
<td>Modèle de préfixe de chemin d’accès</td>
<td>La procédure d’affection de noms respecte la convention suivante : <BR>{Modèle de préfixe de chemin d’accès}/Code_hachage_schéma_Numéro_Guid.extension <BR> <BR>Exemples de fichier de sortie :<BR>Myoutput/20170901/00/45434_gguid_1.csv <BR>Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR>En outre, voici les situations dans lesquelles un fichier est créé :<BR>1. Modification du schéma de sortie <BR>2. Redémarrage externe ou interne d’un travail<BR><BR>En outre, si le modèle de chemin d’accès au fichier ne contient aucun caractère « / » de fin, le dernier modèle du chemin d’accès est traité en tant que préfixe de nom de fichier.<BR><BR>Exemple :<BR>Pour le modèle de chemin d’accès « folder1/logs/HH », le fichier généré ressemblerait à ce qui suit : folder1/logs/02_134343_gguid_1.csv.</td>
</tr>
<tr>
<td>Format de la date [<I>facultatif</I>]</td>
<td>Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple : JJ/MM/AAAA</td>
</tr>
<tr>
<td>Format de l’heure [<I>facultatif</I>]</td>
<td>Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est HH.</td>
</tr>
<tr>
<td>Format de sérialisation de l’événement</td>
<td>Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Encodage</td>
<td>Si vous utilisez le format CSV ou JSON, un encodage doit être spécifié. UTF-8 est le seul format de codage actuellement pris en charge.</td>
</tr>
<tr>
<td>Délimiteur</td>
<td>Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicable uniquement pour la sérialisation JSON. L’expression « Séparé par une ligne » indique que la sortie sera mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme « Tableau » indique que la sortie sera mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser du code JSON séparé par des lignes, car il ne requiert aucun traitement spécial pendant que le fichier de sortie est écrit.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renouveler une autorisation Data Lake Store
Vous devez authentifier de nouveau votre compte Data Lake Store si son mot de passe a été modifié depuis la création ou la dernière authentification de votre travail.

![Autoriser Data Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>Base de données SQL
[base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) comme sortie pour les données relationnelles ou pour les applications qui dépendent de contenus hébergés dans une base de données relationnelle. Les travaux Stream Analytics écrivent les données dans une table existante d’une base de données Azure SQL Database.  Notez que le schéma de table doit correspondre exactement aux champs et aux types de sortie de votre travail. [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) peut également être spécifié en tant que sortie via l’option de sortie SQL Database (il s’agit d’une fonctionnalité d’évaluation). Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de base de données SQL.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette base de données. |
| Base de données |Nom de la base de données où vous envoyez votre sortie |
| Nom du serveur |Nom du serveur de base de données SQL |
| Nom d’utilisateur |Nom de l’utilisateur qui a accès en écriture à la base de données. |
| Mot de passe |Mot de passe de connexion à la base de données |
| Table |Nom de la table dans laquelle la sortie sera écrite. Le nom de la table est sensible à la casse et le schéma de cette table doit correspondre exactement au nombre de champs et aux types générés par la sortie de votre travail. |

> [!NOTE]
> L’offre de base de données SQL Azure est actuellement prise en charge pour une sortie de tâche dans Stream Analytics. Toutefois, une machine virtuelle de Azure exécutant SQL Server avec une base de données associée n’est pas pris en charge. Cela est susceptible de changer dans des futures versions.
> 
> 

## <a name="blob-storage"></a>Stockage d'objets blob
Le stockage d’objets blob offre une solution peu coûteuse et évolutive pour stocker de grandes quantités de données non structurées dans le cloud.  Pour une présentation du stockage d’objets blob Azure et de son utilisation, consultez [Utilisation du stockage d’objets blob](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie d’objets blob.

<table>
<tbody>
<tr>
<td>Nom de la propriété</td>
<td>Description</td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage d’objets blob.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Nom du compte de stockage où vous envoyez votre sortie.</td>
</tr>
<tr>
<td>Clé du compte de stockage</td>
<td>Clé secrète associée au compte de stockage.</td>
</tr>
<tr>
<td>Conteneur de stockage</td>
<td>Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous téléchargez un objet blob dans le service d'objets Blob, vous devez spécifier un conteneur pour cet objet blob.</td>
</tr>
<tr>
<td>Séquence d’octets préfixe du chemin d’accès [facultatif]</td>
<td>Modèle de chemin d’accès au fichier utilisé pour écrire vos blobs dans le conteneur spécifié. <BR> Dans le modèle de chemin d’accès, vous pouvez choisir d’utiliser une ou plusieurs instances de l’une des 2 variables suivantes pour spécifier la fréquence d’écriture des blobs : <BR> {date}, {time} <BR> Exemple 1 : cluster1/logs/{date}/{time} <BR> Exemple 2 : cluster1/logs/{date} <BR> <BR> La procédure d’affection de noms respecte la convention suivante : <BR> {Modèle de préfixe de chemin d’accès}/Code_hachage_schéma_Numéro_Guid.extension <BR> <BR> Exemples de fichier de sortie : <BR> Myoutput/20170901/00/45434_gguid_1.csv <BR> Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR> En outre, voici les situations dans lesquelles un fichier est créé : <BR> 1. Le nombre maximal de blocs autorisé du fichier actuel (50 000, actuellement) est dépassé <BR> 2. Modification du schéma de sortie <BR> 3. Redémarrage externe ou interne d’un travail  </td>
</tr>
<tr>
<td>Format de la date [facultatif]</td>
<td>Si le jeton de la date est utilisé dans le chemin d’accès du préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés. Exemple : JJ/MM/AAAA</td>
</tr>
<tr>
<td>Format de l’heure [facultatif]</td>
<td>Si le jeton de l’heure est utilisé dans le chemin d’accès du préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisés. Actuellement, la seule valeur possible est HH.</td>
</tr>
<tr>
<td>Format de sérialisation de l’événement</td>
<td>Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Encodage</td>
<td>Si vous utilisez le format CSV ou JSON, un encodage doit être spécifié. UTF-8 est le seul format de codage actuellement pris en charge.</td>
</tr>
<tr>
<td>Délimiteur</td>
<td>Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicable uniquement pour la sérialisation JSON. L’expression « Séparé par une ligne » indique que la sortie sera mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme « Tableau » indique que la sortie sera mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser du code JSON séparé par des lignes, car il ne requiert aucun traitement spécial pendant que le fichier de sortie est écrit.</td>
</tr>
</tbody>
</table>

Lorsque vous utilisez le stockage d’objets blob en tant que sortie, un fichier est créé dans l’objet blob dans les cas suivants :

* si le nombre maximal de blocs inclus dans le fichier est supérieur au maximum autorisé (remarque : vous pouvez atteindre ce maximum sans atteindre la taille maximale autorisée pour les blobs. Par exemple, si le taux de sortie est élevé, vous voyez apparaître davantage d’octets par bloc, et la taille de fichier est supérieure. S’il est faible, chaque bloc inclut moins de données et cette taille est inférieure.) ;  
* si le schéma est modifié dans la sortie, et que le format de cette dernière requiert un schéma fixe (CSV et Avro) ;  
* si un travail est redémarré en externe, ou en cas de redémarrage interne d’un travail ;  
* si la requête est entièrement partitionnée (un fichier est créé pour chaque partition de sortie) ;  
* si un fichier ou un conteneur du compte de stockage est supprimé par l’utilisateur ;  
* si la sortie fait l’objet d’une segmentation temporelle via le modèle de préfixe de chemin d’accès, un nouvel objet blob est utilisé lorsque la requête passe à l’heure suivante.

## <a name="event-hub"></a>Event Hub
[Event Hubs](https://azure.microsoft.com/services/event-hubs/) est un service de réception d’événements de publication/d’abonnement hautement évolutif. Il peut collecter des millions d’événements par seconde. Un hub d’événements peut être utilisé en tant que sortie lorsque la sortie d’un travail Stream Analytics correspond à l’entrée d’un autre travail de diffusion.

Quelques paramètres sont requis pour configurer les flux de données Event Hub en tant que sortie.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cet Event Hub. |
| Espace de noms Service Bus |Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un Event Hub, vous avez également créé un espace de noms Service Bus |
| Event Hub |Nom de votre sortie Event Hub |
| Nom de la stratégie Event Hub |Stratégie d’accès partagé, qui peut être créée dans l’onglet Configuration du hub d’événements. Chaque stratégie d’accès partagé présente un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie Event Hub |Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Colonne de clé de partition [facultatif] |Cette colonne contient la clé de partition pour la sortie du hub d’événements. |
| Format de sérialisation de l’événement |Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
| Encodage |Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format |Applicable uniquement pour la sérialisation JSON. L’expression « Séparé par une ligne » indique que la sortie sera mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme « Tableau » indique que la sortie sera mise en forme en tant que tableau d’objets JSON. Ce tableau se ferme uniquement lorsque le travail s’arrête ou que Stream Analytics est passé à la période suivante. En règle générale, il est préférable d’utiliser du code JSON séparé par des lignes, car il ne requiert aucun traitement spécial pendant que le fichier de sortie est écrit. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) peut être utilisé comme sortie d’une tâche Stream Analytics pour fournir une expérience de visualisation riche des résultats d’analyse. Cette fonctionnalité peut être utilisée pour les tableaux de bord opérationnels, la génération de rapports et la création de rapports pilotés par des métriques.

### <a name="authorize-a-power-bi-account"></a>Autorisation d’un compte Power BI
1. Lorsque Power BI est sélectionné en tant que sortie dans le portail Azure, vous êtes invité à autoriser un utilisateur Power BI existant ou à créer un compte Power BI.  
   
   ![Autoriser un utilisateur de Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Créez un compte si vous n’en avez pas déjà un, puis cliquez sur Autoriser maintenant.  Un écran semblable à celui-ci s’affiche.  
   
   ![Compte Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. Dans cette étape, indiquez le compte professionnel ou scolaire servant à autoriser la sortie Power BI. Si vous n’êtes pas déjà inscrit à Power BI, choisissez S’inscrire maintenant. Le compte professionnel ou scolaire que vous utilisez pour Power BI peut être différent du compte d’abonnement Azure avec lequel vous êtes actuellement connecté.

### <a name="configure-the-power-bi-output-properties"></a>Configuration des propriétés de sortie Power BI
Une fois le compte Power BI authentifié, vous pouvez configurer les propriétés pour votre sortie Power BI. Le tableau ci-dessous répertorie les noms de propriétés et leur description pour configurer votre sortie Power BI.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette sortie Power BI. |
| Espace de travail de groupe |Pour permettre le partage de données avec d’autres utilisateurs de Power BI, vous pouvez sélectionner des groupes à l’intérieur de votre compte Power BI ou choisir « Mon espace de travail » si vous ne souhaitez pas écrire dans un groupe.  La mise à jour d’un groupe existant nécessite le renouvellement de l’authentification Power BI. |
| Nom du jeu de données |Fournissez un nom de jeu de données qui doit être utilisé pour la sortie Power BI |
| Nom de la table |Fournissez un nom de table sous le jeu de données de la sortie Power BI. Actuellement, la sortie Power BI des tâches Stream Analytics peut avoir une table uniquement dans un jeu de données. |

Pour une vue d’ensemble de la configuration d’un tableau de bord et d’une sortie Power BI, voir l’article [Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Ne créez pas explicitement le jeu de données et la table dans le tableau de bord Power BI. Le jeu de données et la table seront automatiquement remplis au démarrage du travail et lorsque celui-ci commence à extraire des sorties dans Power BI. Notez que si la requête de travail ne génère aucun résultat, le jeu de données et la table ne seront pas créés. De même, si Power BI dispose déjà d’un jeu de données et d’une table portant le même nom que celui saisi dans le travail Stream Analytics, alors ces données sont écrasées.
> 
> 

### <a name="schema-creation"></a>Création d’un schéma
Azure Stream Analytics crée un jeu de données et une table Power BI au nom de l’utilisateur s’il n’en existe pas encore. Dans tous les autres cas, la table est mise à jour de façon à inclure les nouvelles valeurs. Actuellement, il existe une limitation : une seule table peut exister dans un jeu de données.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Conversion de types de données Stream Analytics vers Power BI
Azure Stream Analytics met à jour le modèle de données dynamiquement lors de l’exécution si le schéma de sortie est modifié. L’intégralité des modifications de nom de colonne, modifications de type de colonne et ajouts ou suppressions de colonnes sont suivis.

Ce tableau décrit les conversions de types de données des [types de données Steam Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) vers les [types Entity Data Model (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) de Power Bi si un jeu de données et une table POWER BI n’existent pas.


De Stream Analytics | Vers Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | Chaîne
Datetime | DateTime
float | Double
Tableau d’enregistrements | Type chaîne, valeur constante « IRecord » ou « IArray »

### <a name="schema-update"></a>Mise à jour d’un schéma
Steam Analytics déduit le schéma de modèle de données sur la base du premier ensemble d’événements de la sortie. Plus tard, si nécessaire, le schéma de modèle de données est mis à jour pour prendre en compte les événements entrants qui ne correspondent pas au schéma d’origine.

La requête `SELECT *` doit être évitée pour empêcher la mise à jour dynamique du schéma entre les lignes. En plus de l’impact potentiel sur les performances, le temps nécessaire pour la création des résultats devient impossible à déterminer. Les champs exacts qui doivent être présentés dans le tableau de bord Power BI doivent être sélectionnés. En outre, les valeurs de données doivent être compatibles avec le type de données choisi.


Précédent/Actuel | Int64 | Chaîne | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | Chaîne | Chaîne | Double
Double | Double | Chaîne | Chaîne | Double
Chaîne | Chaîne | Chaîne | Chaîne |  | Chaîne | 
DateTime | Chaîne | Chaîne |  DateTime | Chaîne


### <a name="renew-power-bi-authorization"></a>Renouvellement de l’autorisation Power BI
Vous devez authentifier de nouveau votre compte Power BI si son mot de passe a été modifié depuis la création ou la dernière authentification de votre travail. Par ailleurs, si le logiciel Multi-Factor Authentication (MFA) est configuré sur votre client Azure Active Directory (AAD), vous devrez renouveler l’autorisation Power BI toutes les deux semaines. Un symptôme de ce problème est l’absence de sortie de la tâche et une « erreur d’authentification de l’utilisateur » dans les journaux des opérations :

  ![Erreur de jeton d’actualisation Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Pour résoudre ce problème, arrêtez votre tâche en cours d'exécution et accédez à votre sortie Power BI.  Cliquez sur le lien « Renouveler l’autorisation », puis redémarrez votre tâche depuis l’heure du dernier arrêt pour éviter de perdre des données.

  ![Renouvellement de l’autorisation par Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Stockage de tables
[stockage de tables Azure](../storage/common/storage-introduction.md) offre un stockage hautement disponible et massivement évolutif, afin qu’une application puisse être mise à l’échelle automatiquement pour répondre à la demande des utilisateurs. Le stockage de tables correspond au magasin de clés/attributs NoSQL de Microsoft, qui peut être utilisé pour les données structurées, en présentant moins de contraintes au niveau du schéma. Le stockage des données sur les tables Azure permet d’assurer leur persistance et une récupération efficace.

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de table.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers ce stockage de tables. |
| Compte de stockage |Nom du compte de stockage où vous envoyez votre sortie. |
| Clé du compte de stockage |Clé d’accès associée au compte de stockage. |
| Nom de la table |Nom de la table. La table est créée, si elle n’existe pas. |
| Partition Key |Nom de la colonne de sortie contenant la clé de partition. La clé de partition est un identificateur unique pour la partition dans une table donnée qui constitue la première partie de la clé primaire d’une entité. C’est une valeur de chaîne qui peut atteindre 1 Ko. |
| Row Key |Nom de la colonne de sortie contenant la clé de ligne. La clé de ligne est un identificateur unique pour une entité dans une partition donnée. Elle constitue la deuxième partie de la clé primaire d’une entité. La clé de ligne est une valeur de chaîne qui peut atteindre 1 Ko. |
| Taille du lot |Nombre d’enregistrements d’une opération par lot. En général, la valeur par défaut est suffisante pour bon nombre des tâches. Pour plus d’informations sur la modification de ce paramètre, voir [Spécifications des opérations par lot des tables](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx). |
 
## <a name="service-bus-queues"></a>Files d'attente Service Bus
[Files d'attente Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) permettent de livrer des messages selon le principe du premier entré, premier sorti (FIFO) à un ou plusieurs destinataires concurrents. En général, les messages sont reçus et traités par les destinataires dans l’ordre dans lequel ils ont été ajoutés à la file d’attente ; chaque message est reçu et traité par un seul consommateur de message uniquement.

La table ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de file d’attente.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette file d’attente Service Bus. |
| Espace de noms Service Bus |Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. |
| Nom de la file d’attente |Nom de la file d’attente Service Bus. |
| Nom de la stratégie de file d’attente |Lorsque vous créez une file d’attente, vous pouvez également créer des stratégies d’accès partagé sur l’onglet Configurer de la file d’attente. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de file d’attente |Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Format de sérialisation de l’événement |Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
| Encodage |Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |
| Format |Applicable uniquement pour le type JSON. L’expression « Séparé par une ligne » indique que la sortie sera mise en forme de sorte que tous les objets JSON soient séparés par une nouvelle ligne. Le terme « Tableau » indique que la sortie sera mise en forme en tant que tableau d’objets JSON. |

## <a name="service-bus-topics"></a>Rubriques de Service Bus
Les files d'attente Service Bus offrent une communication de type un-à-un entre l'expéditeur et le destinataire, alors que les [rubriques Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrent une communication de type un-à-plusieurs.

Le tableau ci-dessous répertorie les noms de propriétés et leur description pour la création d’une sortie de table.

| Nom de la propriété | Description |
| --- | --- |
| Alias de sortie |Nom convivial utilisé dans les requêtes pour diriger la sortie de requête vers cette rubrique Service Bus. |
| Espace de noms Service Bus |Un espace de noms Service Bus est un conteneur pour un jeu d’entités de messagerie. En créant un Event Hub, vous avez également créé un espace de noms Service Bus |
| Nom de la rubrique |Les rubriques sont des entités de messagerie qui sont similaires aux files d’attente et aux hubs d’événements. Il est conçu pour recevoir des flux d’événements à partir d’appareils et de services divers. Quand une rubrique est créée, elle reçoit également un nom. Les messages envoyés à une rubrique ne sont disponibles que si un abonnement est créé. Assurez-vous donc que la rubrique comprend un ou plusieurs abonnements. |
| Nom de la stratégie de rubrique |Lorsque vous créez une rubrique, vous pouvez également créer des stratégies d’accès partagé sur l’onglet Configurer de la rubrique. Chaque stratégie d’accès partagé présente un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| Clé de la stratégie de rubrique |Clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Service Bus |
| Format de sérialisation de l’événement |Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
 | Encodage |Si vous utilisez le format CSV ou JSON, un encodage doit être spécifié. UTF-8 est le seul format d’encodage actuellement pris en charge |
| Délimiteur |Applicable uniquement pour la sérialisation CSV. Stream Analytics prend en charge un certain nombre de délimiteurs communs pour sérialiser des données dans un format CSV. Valeurs prises en charge : virgule, point-virgule, espace, tabulation et barre verticale. |

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) est un service de base de données multimodèle distribué à l’échelle mondiale, qui offre une mise à l’échelle élastique et sans limite dans le monde entier, des requêtes enrichies et une indexation automatique sur les modèles de données indépendants des schémas, la garantie d’une latence faible et des contrats de niveau de service complets de haut niveau.

La liste ci-dessous présente les noms et les descriptions des propriétés pour la création d’une sortie Azure Cosmos DB.

* **Alias de sortie** : alias faisant référence à cette sortie dans votre requête Stream Analytics.  
* **Nom du compte** : nom ou URI du point de terminaison du compte Cosmos DB.  
* **Clé du compte** : clé d’accès partagé du compte Cosmos DB.  
* **Base de données** : nom de la base de données Cosmos DB.  
* **Modèle de nom de collection** : modèle ou nom de collection des collections à utiliser. Le format de nom de collection peut être construit à l’aide du jeton facultatif {partition}, où les partitions commencent à 0. Voici des exemples d’entrées valides :  
  1\) MyCollection : il doit exister une collection nommée « MyCollection ».  
  2\) MyCollection{partition} : vous devez créer les collections « MyCollection0 », « MyCollection1 », « MyCollection2 », etc.  
* **Clé de partition** : facultative. Nécessaire uniquement si vous utilisez un jeton de partition dans votre modèle de nom de collection. Nom du champ dans les événements de sortie utilisé pour spécifier la clé de partitionnement de sortie sur les collections. Pour une sortie de collection unique, une colonne de sortie arbitraire peut être utilisée (par exemple, PartitionId).  
* **ID de document** : facultatif. Nom du champ dans les événements de sortie utilisé pour spécifier la clé primaire sur laquelle sont basées les opérations d’insertion ou de mise à jour.  

## <a name="azure-functions-in-preview"></a>Azure Functions (préversion)
Azure Functions est un service de calcul sans serveur qui vous permet d’exécuter du code à la demande sans explicitement configurer ou gérer l’infrastructure. Grâce à ce service, vous pouvez implémenter le code qui est déclenché par les événements qui se produisent dans Azure ou des services tiers.  Comme Azure Functions peut répondre à des déclencheurs, il constitue l’outil de sortie logique pour Azure Stream Analytics. Cet adaptateur de sortie permet aux utilisateurs de connecter Stream Analytics à Azure Functions et d’exécuter un script ou un fragment de code en réaction à différents événements.

Azure Stream Analytics appelle Azure Functions via des déclencheurs HTTP. Le nouvel adaptateur de sortie Azure Stream Analytics est disponible, avec les propriétés configurables suivantes :

| Nom de la propriété | Description |
| --- | --- |
| Function App |Nom de votre application Azure Function App |
| Fonction |Nom de la fonction dans votre application Azure Function App |
| Taille de lot maximale |Cette propriété peut être utilisée pour définir la taille maximale de chaque lot de sortie qui est envoyé à votre application Azure Functions. Par défaut, cette valeur est définie sur 256 Ko. |
| Nombre maximal de lots  |Comme son nom l’indique, cette propriété vous permet de spécifier le nombre maximal d’événements dans chaque lot envoyé à Azure Functions. Par défaut, cette valeur est de 100. |
| Clé |Si vous souhaitez utiliser une fonction Azure à partir d’un autre abonnement, vous pouvez le faire en fournissant la clé permettant d’accéder à votre fonction. |

Lorsque le logiciel Azure Stream Analytics reçoit une erreur 413 (qui indique que l’entité de demande HTTP est trop volumineuse) de la part d’une fonction Azure, il réduit la taille des lots envoyés à Azure Functions. Dans le code de votre fonction Azure, utilisez cette exception pour vous assurer qu’Azure Stream Analytics n’envoie pas de lots trop volumineux. Vérifiez également que les valeurs de taille et de nombre de lots maximum utilisées dans la fonction correspondent à celles qui ont été saisies dans le portail Stream Analytics. 

De plus, si aucun événement n’est signalé dans le temps imparti, aucune sortie n’est générée. De ce fait, la fonction computeResult n’est pas appelée. Ce comportement est cohérent avec les fonctions d’agrégation fenêtrées intégrées.


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>étapes suivantes
Stream Analytics, un service géré d’analyse de diffusion en continu des données à partir de l’Internet des objets vous a été présenté. Pour en savoir plus sur ce service, consultez les rubriques suivantes :

* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
