---
title: Se connecter à Azure Cosmos DB à l’aide d’outils d’analyse décisionnelle | Microsoft Docs
description: Découvrez comment utiliser le pilote ODBC Azure Cosmos DB pour créer des tables et des vues afin d’afficher les données normalisées dans BI et dans un logiciel d’analyse de données.
keywords: odbc, pilote odbc
services: cosmos-db
author: mimig1
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 9967f4e5-4b71-4cd7-8324-221a8c789e6b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 03/22/2018
ms.author: mimig
ms.openlocfilehash: 445acafeef67027712826f644afaa1784569b30d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Se connecter à Azure Cosmos DB à l’aide d’outils d’analyse décisionnelle avec le pilote ODBC

Le pilote ODBC Azure Cosmos DB vous permet de vous connecter à Azure Cosmos DB à l’aide d’outils d’analyse décisionnelle comme SQL Server Integration Services, Power BI Desktop et Tableau, pour analyser et créer une représentation visuelle de vos données Azure Cosmos DB dans ces solutions.

Le pilote ODBC Azure Cosmos DB est conforme à ODBC 3.8 et prend en charge la syntaxe ANSI SQL-92. Le pilote offre de puissantes fonctionnalités pour vous aider à renormaliser les données dans Azure Cosmos DB. Grâce à ce pilote, vous pouvez représenter les données dans Azure Cosmos DB sous forme de tables et de vues. Il vous permet d’effectuer des opérations SQL dans des tables et des vues, notamment des regroupements par requêtes, des insertions, des mises à jour et des suppressions.

## <a name="why-do-i-need-to-normalize-my-data"></a>Pourquoi dois-je normaliser mes données ?
Azure Cosmos DB étant une base de données sans schéma, elle offre un développement rapide d’applications en permettant à ces applications d’itérer leur modèle de données à la volée sans schéma strict. Une même base de données Azure Cosmos DB peut contenir des documents JSON de différentes structures. C’est une solution idéale pour le développement rapide d’applications, mais si vous souhaitez analyser et créer des rapports de vos données à l’aide d’outils d’analyse de données et décisionnels, les données doivent souvent être aplaties et respecter un schéma spécifique.

C’est là qu’intervient le pilote ODBC. Grâce au pilote ODBC, vous pouvez à présent renormaliser les données d’Azure Cosmos DB dans des tables et des vues adaptées à vos besoins d’analyse de données et de création de rapports. Les schémas renormalisés n’ont aucun impact sur les données sous-jacentes et il n’est pas obligatoire pour les développeurs de les respecter ; ils permettent de tirer parti d’outils compatibles ODBC pour accéder aux données. Désormais, votre base de données Azure Cosmos DB ne sera pas uniquement l’un des outils favoris de votre équipe de développement. Vos analystes de données vont l’adorer eux aussi.

Familiarisons-nous à présent avec le pilote ODBC.

## <a id="install"></a>Étape 1 : installer le pilote ODBC Azure Cosmos DB

1. Téléchargez les pilotes correspondant à votre environnement :

    | Programme d’installation | Systèmes d’exploitation pris en charge| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/documentdb-odbc-64x64) pour Windows 64 bits| Versions 64 bits de Windows 8.1 ou version ultérieure, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/documentdb-odbc-32x64) pour 32 bits sur Windows 64 bits| Versions 64 bits de Windows 8.1 ou version ultérieure, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 et Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/documentdb-odbc-32x32) pour Windows 32 bits|Versions 32 bits de Windows 8.1 ou version ultérieure, Windows 8, Windows 7, Windows XP et Windows Vista.|

    Exécutez le fichier msi localement pour lancer l’**Assistant d’installation du pilote ODBC Microsoft Azure Cosmos DB**. 
2. Terminez l’assistant d’installation en utilisant l’entrée par défaut pour installer le pilote ODBC.
3. Ouvrez l’application **Administrateur de sources de données ODBC** sur votre ordinateur ; pour cela, tapez **Sources de données ODBC** dans la fenêtre de recherche Windows. 
    Vous pouvez confirmer l’installation du pilote en cliquant dans l’onglet **Pilotes** pour vérifier que le **pilote ODBC Microsoft Azure Cosmos DB** est répertorié.

    ![Administrateur de sources de données ODBC Azure Cosmos DB](./media/odbc-driver/odbc-driver.png)

## <a id="connect"></a>Étape 2 : se connecter à votre base de données Azure Cosmos DB

1. Après l’[installation du pilote ODBC Azure Cosmos DB](#install), dans la fenêtre **Administrateur de sources de données ODBC**, cliquez sur **Ajouter**. Vous pouvez créer un DSN utilisateur ou système. Dans cet exemple, vous allez créer un DSN utilisateur.
2. Dans la fenêtre **Créer une nouvelle source de données**, sélectionnez **Microsoft Azure Cosmos DB ODBC Driver (Pilote ODBC Microsoft Azure Cosmos DB)**, puis cliquez sur **Terminer**.
3. Dans la fenêtre **Azure Cosmos DB ODBC Driver SDN Setup (Configuration DSN du pilote ODBC Azure Cosmos DB)**, répondez aux questions suivantes : 

    ![Fenêtre de configuration DSN du pilote ODBC Azure Cosmos DB](./media/odbc-driver/odbc-driver-dsn-setup.png)
    - **Nom de source de données** : le nom convivial de votre DSN ODBC. Ce nom étant spécifique à votre compte Azure Cosmos DB, choisissez-le de manière appropriée si vous possédez plusieurs comptes.
    - **Description** : courte description de la source de données.
    - **Hôte** : URI de votre compte Azure Cosmos DB. Vous pouvez récupérer cette information sur la page des clés Azure Cosmos DB du portail Azure, comme illustré dans la capture d’écran suivante. 
    - **Clé d’accès** : clé primaire ou secondaire, en lecture-écriture ou en lecture seule, affichée sur la page des clés Azure Cosmos DB du portail Azure, comme illustré dans la capture d’écran suivante. Nous vous recommandons d'utiliser la clé en lecture seule si le DSN sert au traitement des données en lecture seule et à la création de rapports.
    ![Page des clés Azure Cosmos DB](./media/odbc-driver/odbc-driver-keys.png)
    - **Chiffrer la clé d’accès pour** : sélectionnez l’option optimale en fonction des utilisateurs de cet ordinateur. 
4. Cliquez sur le bouton **Test** pour vérifier que vous pouvez vous connecter à votre compte Azure Cosmos DB. 
5. Cliquez sur **Options avancées** et définissez les valeurs suivantes :
    - **Cohérence des requêtes** : sélectionnez le [niveau de cohérence](consistency-levels.md) de vos opérations. La valeur par défaut est Session.
    - **Nombre de tentatives**: entrez le nombre de tentatives d’une opération si la demande initiale n’aboutit pas en raison d’une limitation de service.
    - **Fichier de schéma** : plusieurs options vous sont proposées ici.
        - Par défaut, si vous ne modifiez pas cette entrée (vide), le pilote analyse les données de la première page de toutes les collections afin de déterminer le schéma de chaque collection. Cette opération est appelée Mappage de la collection. Si aucun fichier de schéma n’est défini, le pilote doit effectuer l’analyse pour chaque session de pilote, ce qui peut allonger le délai de démarrage d’une application avec le DSN. Nous vous recommandons de toujours associer un fichier de schéma à un DSN.
        - Si vous disposez déjà d’un fichier de schéma (peut-être un fichier que vous avez créé à l’aide de [l’Éditeur de schéma](#schema-editor)), cliquez sur **Parcourir**, recherchez votre fichier, cliquez sur **Enregistrer**, puis sur **OK**.
        - Si vous souhaitez créer un nouveau schéma, cliquez sur **OK**, puis sur **Éditeur de schéma** dans la fenêtre principale. Accédez ensuite à l’[Éditeur de schéma](#schema-editor) pour plus d’informations. Après la création du nouveau fichier de schéma, pensez à revenir à la fenêtre **Options avancées** pour l’inclure.

6. Une fois que vous avez terminé et fermé la fenêtre de **configuration DSN du pilote ODBC Azure Cosmos DB**, le DSN du nouvel utilisateur est ajouté à l’onglet DSN utilisateur.

    ![Nouveau DSN ODBC Azure Cosmos DB dans l’onglet DSN utilisateur](./media/odbc-driver/odbc-driver-user-dsn.png)

## <a id="#collection-mapping"></a>Étape 3 : Créer une définition de schéma à l’aide de la méthode de mappage de la collection

Il existe deux types de méthodes d’échantillonnage que vous pouvez utiliser : **mappage de la collection** ou **délimiteurs de la table**. Une session d’échantillonnage peut utiliser les deux méthodes d’échantillonnage, mais chaque collection peut uniquement utiliser une méthode d’échantillonnage spécifique. Les étapes ci-dessous créent un schéma pour les données d’une ou plusieurs collections à l’aide de la méthode de mappage de la collection. Cette méthode d’échantillonnage récupère les données dans la page d’une collection pour déterminer la structure des données. Elle transpose une collection dans une table du côté ODBC. Cette méthode d’échantillonnage est rapide et efficace lorsque les données d’une collection sont homogènes. Si une collection contient des données hétérogènes, nous vous recommandons d’utiliser la [méthode de mappage par délimiteurs de table](#table-mapping) car elle fournit une méthode d’échantillonnage plus robuste pour déterminer les structures des données de la collection. 

1. Après avoir terminé les étapes 1 à 4 de la rubrique [Se connecter à votre base de données Azure Cosmos DB](#connect), cliquez sur **Éditeur de schéma** dans la fenêtre de **configuration DSN du pilote ODBC Azure Cosmos DB**.

    ![Bouton Éditeur de schéma dans la fenêtre de configuration du DSN du pilote ODBC Azure Cosmos DB](./media/odbc-driver/odbc-driver-schema-editor.png)
2. Dans la fenêtre **Éditeur de schéma**, cliquez sur **Créer**.
    La fenêtre **Générer le schéma** affiche toutes les collections du compte Azure Cosmos DB. 
3. Sélectionnez une ou plusieurs collections à échantillonner, puis cliquez sur **Échantillonner**. 
4. Dans l’onglet **Mode Création**, la base de données, le schéma et la table sont représentés. Dans la vue de la table, l’analyse affiche l’ensemble des propriétés associées aux noms de colonne (Nom SQL, Nom de la Source, etc.).
    Pour chaque colonne, vous pouvez modifier le nom de la colonne SQL, le type SQL, la longueur SQL (le cas échéant), l’échelle (le cas échéant), la précision (le cas échéant) et la valeur Nullable.
    - Vous pouvez définir **Masquer la colonne** sur **true** si vous souhaitez exclure cette colonne des résultats de la requête. Les colonnes marquées Masquer la colonne = true ne sont pas retournées pour la sélection et la projection, bien qu’elles fassent toujours partie du schéma. Par exemple, vous pouvez masquer toutes les propriétés système Azure Cosmos DB requises commençant par « _ ».
    - La colonne **id** est le seul champ qui ne peut pas être masqué car elle sert de clé primaire dans le schéma normalisé. 
5. Une fois que vous avez terminé la définition du schéma, cliquez sur **Fichier** | **Enregistrer**, accédez au répertoire d’enregistrement du schéma, puis cliquez sur **Enregistrer**.

    Si vous souhaitez, par la suite, utiliser ce schéma avec un DSN, ouvrez la fenêtre de configuration du DSN du pilote ODBC Azure Cosmos DB (par le biais de l’Administrateur de sources de données ODBC), cliquez sur Options avancées, puis, dans la boîte de dialogue Fichier de schéma, accédez au schéma enregistré. L’enregistrement d’un fichier de schéma dans une source de données existante modifie la connexion de DSN afin de définir l’étendue des données et de la structure définie par le schéma.

## <a id="table-mapping"></a>Étape 4 : Créer une définition de schéma à l’aide de la méthode de mappage des délimiteurs de table

Il existe deux types de méthodes d’échantillonnage que vous pouvez utiliser : **mappage de la collection** ou **délimiteurs de la table**. Une session d’échantillonnage peut utiliser les deux méthodes d’échantillonnage, mais chaque collection peut uniquement utiliser une méthode d’échantillonnage spécifique. 

Les étapes suivantes créent un schéma pour les données d’une ou plusieurs collections à l’aide de la méthode des **délimiteurs de table**. Nous vous recommandons d’utiliser cette méthode d’échantillonnage lorsque vos collections contiennent des données hétérogènes. Vous pouvez utiliser cette méthode pour définir l’étendue de l’échantillonnage sur un ensemble d’attributs et ses valeurs correspondantes. Par exemple, si un document contient une propriété « Type », vous pouvez étendre l’échantillonnage aux valeurs de cette propriété. Le résultat final de l’échantillonnage serait un ensemble de tables pour chacune des valeurs du type que vous avez spécifié. Par exemple, Type = Voiture produira une table Voiture tandis que Type = Avion produira une table Avion.

1. Après avoir terminé les étapes 1 à 4 de la rubrique [Se connecter à votre base de données Azure Cosmos DB](#connect), cliquez sur **Éditeur de schéma** dans la fenêtre de configuration DSN du pilote ODBC Azure Cosmos DB.
2. Dans la fenêtre **Éditeur de schéma**, cliquez sur **Créer**.
    La fenêtre **Générer le schéma** affiche toutes les collections du compte Azure Cosmos DB. 
3. Sélectionnez une collection dans l’onglet **Exemple de vue**, dans la colonne **Définition de mappage** de la collection, puis cliquez sur **Modifier**. Puis, dans la fenêtre **Définition de mappage**, sélectionnez la méthode **Délimiteurs de table**. Faites ensuite ce qui suit :

    a. Dans le champ **Attributs**, tapez le nom d’une propriété de délimiteur. Il s’agit d’une propriété de votre document que vous souhaitez étendre à l’échantillonnage, par exemple Ville. Appuyez ensuite sur Entrée. 

    b. Si vous souhaitez uniquement étendre l’échantillonnage certaines valeurs de l’attribut que vous venez d’entrer, sélectionnez l’attribut dans la zone de sélection, entrez une valeur dans la zone **Valeur**, par exemple Seattle, puis appuyez sur Entrée. Vous pouvez continuer à ajouter d’autres valeurs pour les attributs. Assurez-vous simplement que l’attribut approprié est sélectionné lorsque vous entrez des valeurs.

    Par exemple, si vous incluez une valeur **Attributs** de type Ville et que vous souhaitez limiter votre table pour inclure uniquement les lignes dont la valeur Ville est New York et Dubaï, vous devez entrer Ville dans la zone Attributs et New York puis Dubaï dans la zone **Valeurs**.
4. Cliquez sur **OK**. 
5. Après avoir mappé les définitions des collections que vous souhaitez échantillonner, dans la fenêtre **Éditeur de schéma**, cliquez sur **Échantillonner**.
     Pour chaque colonne, vous pouvez modifier le nom de la colonne SQL, le type SQL, la longueur SQL (le cas échéant), l’échelle (le cas échéant), la précision (le cas échéant) et la valeur Nullable.
    - Vous pouvez définir **Masquer la colonne** sur **true** si vous souhaitez exclure cette colonne des résultats de la requête. Les colonnes marquées Masquer la colonne = true ne sont pas retournées pour la sélection et la projection, bien qu’elles fassent toujours partie du schéma. Par exemple, vous pouvez masquer toutes les propriétés système Azure Cosmos DB requises commençant par `_`.
    - La colonne **id** est le seul champ qui ne peut pas être masqué car elle sert de clé primaire dans le schéma normalisé. 
6. Une fois que vous avez terminé la définition du schéma, cliquez sur **Fichier** | **Enregistrer**, accédez au répertoire d’enregistrement du schéma, puis cliquez sur **Enregistrer**.
7. Dans la fenêtre de **configuration du DSN du pilote ODBC Azure Cosmos DB**, cliquez sur **Options avancées**. Puis, dans la fenêtre **Fichier de schéma**, accédez au fichier de schéma enregistré et cliquez sur **OK**. Cliquez à nouveau sur **OK** pour enregistrer le DSN. Cette opération enregistre dans le DSN le schéma que vous avez créé. 

## <a name="optional-set-up-linked-server-connection"></a>(Facultatif) Configurer une connexion à un serveur lié

Vous pouvez interroger Azure Cosmos DB à partir de SQL Server Management Studio (SSMS) en configurant une connexion à un serveur lié.

1. Créez une source de données système, nommée par exemple `SDS Name`, en suivant les instructions de [l’étape 2](#connect).
2. [Installez SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).
3. Dans l’éditeur de requête SSMS, créez un objet de serveur lié `DEMOCOSMOS` pour la source de données avec les commandes suivantes. Remplacez `DEMOCOSMOS` par le nom de votre serveur lié, et `SDS Name` par le nom de votre source de données système.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Pour voir le nom du nouveau serveur lié, actualisez la liste Serveurs liés.

![Serveur lié dans SSMS](./media/odbc-driver/odbc-driver-linked-server-ssms.png)

### <a name="query-linked-database"></a>Interroger une base de données liée

Pour interroger la base de données liée, entrez une requête SSMS. Dans cet exemple, il s’agit d’une requête SELECT dans la table de la collection nommée `customers` :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Exécutez la requête. Le résultat devrait se présenter ainsi :

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Le serveur Cosmos DB lié ne prend pas en charge les noms en quatre parties. Un message d’erreur de ce type est retourné :

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Facultatif) Création de vues
Vous pouvez définir et créer des vues dans le cadre du processus d’échantillonnage. Ces vues sont équivalentes aux vues SQL. Elles sont en lecture seule et affichent les sélections et les projections SQL Azure Cosmos DB définies. 

Pour créer une vue de vos données, dans la fenêtre **Éditeur de schéma**, dans la colonne **View Definitions** (Définitions de la vue), cliquez sur **Add** (Ajouter) sur la ligne de la collection à échantillonner. Puis, dans la fenêtre **View Definitions** (Définitions de la vue), procédez comme suit :
1. Cliquez sur **New** (Nouveau), entrez un nom pour la vue, par exemple, EmployeesfromSeattleView, puis cliquez **OK**.
2. Dans la fenêtre **Modifier l’affichage**, entrez une requête Azure Cosmos DB. Utilisez obligatoirement une requête SQL Azure Cosmos DB, par exemple `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Gender, c.Manager FROM c WHERE c.City = “Seattle”`, puis cliquez sur **OK**.

Vous pouvez créer autant de vues que vous le souhaitez. Une fois que vous avez terminé la définition des vues, vous pouvez échantillonner les données. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Étape 5 : Affichage de vos données dans des outils décisionnels comme Power BI Desktop

Vous pouvez utiliser votre nouveau DSN pour connecter DocumentADB avec n’importe quel outil compatible ODBC. Cette étape vous indique simplement comment vous connecter à Power BI Desktop et créer une visualisation Power BI.

1. Ouvrez Power BI Desktop.
2. Cliquez sur **Get Data** (Obtenir les données).
3. Dans la fenêtre **Get Data** (Obtenir les données), cliquez sur **Other** (Autre)  | **ODBC** | **Connect** (Se connecter).
4. Dans la fenêtre **From ODBC** (Depuis ODBC), sélectionnez le nom de source de données que vous avez créé, puis cliquez sur **OK**. Vous pouvez laisser les entrées **Options avancées** vides.
5. Dans la fenêtre **Accéder à une source de données à l’aide d’un pilote ODBC**, sélectionnez **Par défaut ou Personnalisé** , puis cliquez sur **Connecter**. Vous n’avez pas besoin d’inclure les **propriétés de la chaîne d’informations d’identification**.
6. Dans la fenêtre du **navigateur**, dans le volet gauche, développez la base de données, le schéma, puis sélectionnez la table. Le volet des résultats inclut les données en utilisant le schéma que vous avez créé.
7. Pour visualiser les données dans Power BI Desktop, cochez la case en regard du nom de la table, puis cliquez sur **Charger**.
8. Dans Power BI Desktop, à l’extrême gauche, sélectionnez l’onglet Données ![Onglet Données de Power BI Desktop](./media/odbc-driver/odbc-driver-data-tab.png) pour confirmer que vos données ont été importées.
9. Vous pouvez désormais créer des éléments visuels à l’aide de Power BI en cliquant sur l’onglet Rapport ![onglet Rapport dans Power BI Desktop](./media/odbc-driver/odbc-driver-report-tab.png), sur **Nouvel élément visuel**, puis en personnalisation votre mosaïque. Pour plus d’informations sur la création de visualisations dans Power BI Desktop, consultez [Types de visualisation dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/).

## <a name="troubleshooting"></a>Résolution de problèmes

Si l’erreur suivante s’affiche, vérifiez que les valeurs **Hôte** et **Clé d’accès** que vous avez copiées sur le portail Azure à l’[étape 2](#connect) sont correctes, puis réessayez. Utilisez les boutons de copie à droite des valeurs **Hôte** et **Clé d’accès** sur le portail Azure pour copier les valeurs correctes.

    [HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}`

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Cosmos DB, consultez la page [Bienvenue dans Azure Cosmos DB](introduction.md).
