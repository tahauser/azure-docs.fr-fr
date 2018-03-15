---
title: "Se connecter à Azure Databricks à partir d’Excel, de Python ou de R | Microsoft Docs"
description: "Découvrez comment utiliser le pilote Simba pour connecter Azure Databricks à Excel, Python ou R."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: nitinme
ms.openlocfilehash: 9daa7d30036d0a0f98d079e03a69c29d11e49664
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-azure-databricks-from-excel-python-or-r"></a>Se connecter à Azure Databricks à partir d’Excel, de Python ou de R

Dans cet article, vous allez apprendre à utiliser le pilote ODBC Databricks pour connecter Azure Databricks avec le langage R, Python ou Microsoft Excel. Une fois que vous avez établi la connexion, vous pouvez accéder aux données dans Azure Databricks à partir des clients Excel, Python ou R. Vous pouvez également utiliser les clients pour analyser les données de façon plus précise. 

## <a name="prerequisites"></a>configuration requise

* Vous devez disposer d’un espace de travail Azure Databricks, d’un cluster Spark et d’exemples de données associés à votre cluster. Si vous ne disposez pas de ces éléments, suivez le démarrage rapide [Exécuter une tâche Spark sur Azure Databricks à l’aide du portail Azure](quickstart-create-databricks-workspace-portal.md).

* Téléchargez le pilote ODBC Databricks à partir de la [page de téléchargement du pilote Databricks](https://databricks.com/spark/odbc-driver-download). Installer la version 32 bits ou 64 bits en fonction de l’application à partir de laquelle vous souhaitez vous connecter à Azure Databricks. Par exemple, pour vous connecter à partir d’Excel, installez la version 32 bits du pilote. Pour vous connecter à partir de R et Python, installez la version 64 bits du pilote.

* Configurez un jeton d’accès personnel dans Databricks. Pour obtenir des instructions, consultez [Generate token](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management) (Générer un jeton).

## <a name="set-up-a-dsn"></a>Configurer un nom de source de données

Un nom de source de données contient les informations relatives à une source de données spécifique. Un pilote ODBC a besoin de ce nom de source de données pour se connecter à une source de données. Dans cette section, vous configurez un nom de source de données qui peut être utilisé avec le pilote ODBC Databricks pour se connecter à Azure Databricks à partir de clients comme Microsoft Excel, Python ou R.

1. Dans l’espace de travail Azure Databricks, accédez au cluster Databricks.

    ![Ouvrir le cluster Databricks](./media/connect-databricks-excel-python-r/open-databricks-cluster.png "Ouvrir le cluster Databricks")

2. Dans l’onglet **Configuration**, cliquez sur l’onglet **JDBC/ODBC** (JDBC/ODBC) et copiez les valeurs de **Server Hostname** (Nom d’hôte du serveur) et **HTTP Path** (Chemin d’accès HTTP). Vous avez besoin de ces valeurs pour effectuer les étapes de cet article.

    ![Obtenir la configuration Databricks](./media/connect-databricks-excel-python-r/get-databricks-jdbc-configuration.png "Obtenir la configuration Databricks")

3. Sur votre ordinateur, lancez l’application **Sources de données ODBC** (32 bits ou 64 bits) en fonction de l’application. Pour vous connecter à partir d’Excel, utilisez la version 32 bits. Pour vous connecter à partir de R et Python, utilisez la version 64 bits.

    ![Lancer ODBC](./media/connect-databricks-excel-python-r/launch-odbc-app.png "Lancer l’application ODBC")

4. Dans l’onglet **Nom DSN de l’utilisateur**, cliquez sur **Ajouter**. Dans la boîte de dialogue **Créer une nouvelle source de données**, sélectionnez le **Pilote ODBC Spark Simba**, puis cliquez sur **Terminer**.

    ![Lancer ODBC](./media/connect-databricks-excel-python-r/add-new-user-dsn.png "Lancer l’application ODBC")

5. Dans la boîte de dialogue **Simba Spark ODBC Driver** (Pilote ODBC Spark Simba), fournissez les valeurs suivantes :

    ![Configurer le nom de source de données](./media/connect-databricks-excel-python-r/odbc-dsn-setup.png "Configurer le nom de source de données")

    Le tableau suivant fournit des informations sur les valeurs à spécifier dans la boîte de dialogue.
    
    |Champ  | Valeur  |
    |---------|---------|
    |**Nom de source de données**     | Indiquez un nom pour la source de données.        |
    |**Host(s)** (Hôte(s))     | Indiquez la valeur que vous avez copiée à partir de l’espace de travail Databricks pour *Server hostname* (Nom d’hôte du serveur).        |
    |**Port**     | Entrez *443*.        |
    |**Authentification** > **Mécanisme**     | Sélectionnez *Nom d’utilisateur et mot de passe*.        |
    |**Nom d'utilisateur**     | Entrez un *jeton*.        |
    |**Mot de passe**     | Entrez la valeur de jeton que vous avez copiée à partir de l’espace de travail Databricks. |
    
    Effectuez les étapes supplémentaires suivantes dans la boîte de dialogue de configuration du nom de source de données.
    
    * Cliquez sur **HTTP Options** (Options HHTP). Dans la boîte de dialogue qui s’ouvre, collez la valeur de *HTTP Path* (Chemin d’accès HTTP) que vous avez copiée à partir de l’espace de travail Databricks. Cliquez sur **OK**.
    * Cliquez sur **SSL Options** (Options SSL). Dans la boîte de dialogue qui s’ouvre, cochez la case **Activer SSL**. Cliquez sur **OK**.
    * Cliquez sur **Tester** pour tester la connexion à Azure Databricks. Cliquez sur **OK** pour enregistrer la configuration.
    * Dans la boîte de dialogue **Administrateur de sources de données ODBC**, cliquez sur **OK**.

Vous avez maintenant configuré votre nom de source de données. Dans les sections suivantes, vous utilisez ce nom de source de données pour vous connecter à Azure Databricks à partir d’Excel, de Python ou de R.

## <a name="connect-from-microsoft-excel"></a>Se connecter à partir de Microsoft Excel

Dans cette section, vous extrayez des données d’Azure Databricks dans Microsoft Excel à l’aide du nom de source de données que vous avez créé précédemment. Avant de commencer, assurez-vous que Microsoft Excel est installé sur votre ordinateur. Vous pouvez utiliser une version d’évaluation de Microsoft Excel à partir du [lien de la version d’évaluation de Microsoft Excel](https://products.office.com/excel).

1. Ouvrez un classeur vide dans Microsoft Excel. À partir du ruban **Données**, cliquez sur **Obtenir les données**. Cliquez sur **À partir d’autres sources**, puis sur **À partir d’ODBC**.

    ![Lancer ODBC à partir d’Excel](./media/connect-databricks-excel-python-r/launch-odbc-from-excel.png "Lancer ODBC à partir d’Excel")

2. Dans la boîte de dialogue **À partir d’ODBC**, sélectionnez le nom de source de données que vous avez créé précédemment, puis cliquez sur **OK**.

    ![Sélectionner le nom de source de données](./media/connect-databricks-excel-python-r/excel-select-dsn.png "Sélectionner le nom de source de données")

3. Si vous êtes invité à indiquer des informations d’identification, entrez un **jeton** pour le nom d’utilisateur. Pour le mot de passe, fournissez la valeur de jeton que vous avez récupérée à partir de l’espace de travail Databricks.

    ![Fournir des informations d’identification pour Databricks](./media/connect-databricks-excel-python-r/excel-databricks-token.png "Sélectionner le nom de source de données")

4. Dans la fenêtre de navigateur, sélectionnez le tableau dans Databricks que vous souhaitez charger dans Excel, puis cliquez sur **Charger**. 

    ![Charger des données dans Excel](./media/connect-databricks-excel-python-r/excel-load-data.png "Charger des données dans Excel")

Une fois que les données se trouvent dans le classeur Excel, vous pouvez effectuer des opérations d’analyse sur celles-ci.

## <a name="connect-from-r"></a>Se connecter à partir de R

Dans cette section, vous utilisez un environnement de développement intégré de langage R pour référencer des données dans Azure Databricks. Avant de commencer, vous devez avoir les composants suivants installés sur l’ordinateur.

* Un environnement de développement intégré pour le langage R. Cet article utilise RStudio pour le Bureau. Vous pouvez l’installer à partir du [téléchargement RStudio](https://www.rstudio.com/products/rstudio/download/).
* Si vous utilisez RStudio pour le Bureau comme environnement de développement intégré, installez également Microsoft R Client depuis [http://aka.ms/rclient/](http://aka.ms/rclient/). 

Ouvrez RStudio et procédez comme suit :

- Référencez le package `RODBC`. Cela vous permet de vous connecter à Azure Databricks à l’aide du nom de source de données que vous avez créé précédemment.
- Établissez une connexion à l’aide du nom de source de données.
- Exécutez une requête SQL sur les données dans Azure Databricks. Dans l’extrait de code suivant, *radio_sample_data* est un tableau qui existe déjà dans Azure Databricks.
- Effectuez certaines opérations sur la requête pour vérifier la sortie. 

L’extrait de code suivant effectue ces tâches :

    # reference the 'RODBC' package
    require(RODBC)
    
    # establish a connection using the DSN you created earlier
    conn <- odbcConnect("<ENTER DSN NAME HERE>")
    
    # run a SQL query using the connection you created
    res <- sqlQuery(conn, "SELECT * FROM radio_sample_data")
    
    # print out the column names in the query output
    names(res) 
        
    # print out the number of rows in the query output
    nrow (res)

## <a name="connect-from-python"></a>Se connecter à partir de Python

Dans cette section, vous utilisez un environnement de développement intégré Python (comme IDLE) pour référencer des données disponibles dans Azure Databricks. Avant de commencer, respectez les conditions préalables suivantes :

* Installez Python à partir [d’ici](https://www.python.org/downloads/). L’installation de Python à partir de ce lien installe également IDLE.

* À partir d’une invite de commandes sur l’ordinateur, installez le package `pyodbc`. Exécutez la commande suivante :

      pip install pyodbc

Ouvrez IDLE et procédez comme suit :

- Importez le package `pyodbc`. Cela vous permet de vous connecter à Azure Databricks à l’aide du nom de source de données que vous avez créé précédemment.
- Établissez une connexion à l’aide du nom de source de données que vous avez créé précédemment.
-  Exécutez une requête SQL à l’aide de la connexion que vous avez créée. Dans l’extrait de code suivant, *radio_sample_data* est un tableau qui existe déjà dans Azure Databricks.
- Effectuez des opérations sur la requête pour vérifier la sortie.

L’extrait de code suivant effectue ces tâches :

```python
# import the `pyodbc` package:
import pyodbc

# establish a connection using the DSN you created earlier
conn = pyodbc.connect("DSN=<ENTER DSN NAME HERE>", autocommit = True)

# run a SQL query using the connection you created
cursor = conn.cursor()
cursor.execute("SELECT * FROM radio_sample_data")

# print the rows retrieved by the query.
for row in cursor.fetchall():
    print(row)

```

## <a name="next-steps"></a>étapes suivantes

* Pour en savoir plus sur les sources à partir desquelles vous pouvez importer des données dans Azure Databricks, consultez [Spark Data Sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#) (Sources de données Spark)


