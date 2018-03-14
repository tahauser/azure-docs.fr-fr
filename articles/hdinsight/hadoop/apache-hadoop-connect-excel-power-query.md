---
title: "Connexion d’Excel à Hadoop à l’aide de Power Query - Azure HDInsight | Microsoft Docs"
description: "Découvrez comment tirer profit des outils décisionnels et utiliser Power Query pour Excel afin d’accéder aux données stockées dans Hadoop sur HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 21dfaa9b4d2bb59cb2e84006ad58fcb6ce3f81b4
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Connexion d’Excel à Hadoop à l’aide de Power Query
Une fonctionnalité clé de la solution de données volumineuses (« big data ») de Microsoft est l’intégration d’outils décisionnels (BI) Microsoft à des clusters Hadoop dans Azure HDInsight. Un exemple important de cette intégration est la possibilité de connecter Excel au compte Azure Storage contenant les données associées à votre cluster Hadoop en utilisant le complément Microsoft Power Query pour Excel. Cet article vous explique comment configurer et utiliser Power Query pour interroger des données associées à un cluster Hadoop géré avec HDInsight.

### <a name="prerequisites"></a>Prérequis
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un cluster HDInsight**. Pour en configurer un, consultez la section [Prise en main d’Azure HDInsight][hdinsight-get-started].
* **Une station de travail** fonctionnant sous Windows 7, Windows Server 2008 R2 ou une version ultérieure.
* **Office 2016, Office 2013 Professionnel Plus, Office 365 ProPlus, Excel 2013 autonome ou Office 2010 Professionnel Plus**.

## <a name="install-power-query"></a>Installation de Power Query
Power Query permet d'importer des données produites ou générées par un travail Hadoop s'exécutant sur un cluster HDInsight.

Dans Excel 2016, Power Query a été intégré dans le ruban de données sous la section Obtenir et transformer. Pour les versions Excel plus anciennes, téléchargez Microsoft Power Query pour Excel depuis le [Centre de téléchargement Microsoft][powerquery-download] et installez-le.

## <a name="import-hdinsight-data-into-excel"></a>Importation de données HDInsight dans Excel
Le complément Power Query pour Excel facilite l’importation de données depuis votre cluster HDInsight dans Excel, où des outils décisionnels tels que PowerPivot et Power Map peuvent être utilisés pour inspecter, analyser et présenter les données.

**Pour importer des données à partir d’un cluster HDInsight**

1. Ouvrez Excel.
2. Créez un classeur vide.
3. Effectuez les étapes suivantes, selon la version d’Excel :

    - Excel 2016

        - Cliquez sur le menu **Données**, cliquez sur **Obtenir des données** à partir du ruban **Obtenir et transformer des données**, cliquez sur **Depuis Azure**, puis cliquez sur **Depuis Azure HDInsight(HDFS)**.

        ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

    - Excel 2013/2010

        - Cliquez dans le menu **Power Query**, cliquez sur **À partir d'Azure**, puis sur **Depuis Microsoft Azure HDInsight**.
   
        ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
        **Remarque :** si vous ne voyez pas le menu **Power Query**, cliquez sur **Fichier** > **Options** > **Compléments** et sélectionnez **Compléments COM** dans la zone déroulante **Gestionnaire** en bas de la page. Sélectionnez le bouton **OK** et vérifiez que la case correspondant au complément Power Query pour Excel a été cochée.
       
        **Remarque :** Power Query vous permet également d'importer des données à partir de HDFS en cliquant sur **À partir d'autres sources**.
4. Pour le champ **Nom du compte**, saisissez le nom du compte de stockage d’objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il peut s’agir du [compte de stockage par défaut](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou d’un compte de stockage lié.  Le format est *https://&lt;StorageAccountName>.blob.core.windows.net/*.
5. Pour le champ **Clé du compte**, saisissez la clé du compte de stockage d’objets blob, puis cliquez sur **Enregistrer**. (Entrez les informations sur le compte uniquement la première fois que vous accédez à ce Store.)
6. Dans le volet **Navigateur** situé à gauche de l’Éditeur de requête, double-cliquez sur le nom du conteneur de stockage d’objets Blob. Par défaut, le nom du conteneur est identique à celui du cluster.
7. Localisez **HiveSampleData.txt** dans la colonne **Nom** (le chemin du dossier est **../hive/warehouse/hivesampletable/**), puis cliquez sur **Binaire** à gauche de HiveSampleData.txt. HiveSampleData.txt est fourni avec tout le cluster. Si vous le souhaitez, vous pouvez utiliser votre propre fichier.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Le cas échéant, vous pouvez renommer le nom des colonnes. Lorsque vous êtes prêt, cliquez sur **Fermer et charger**.  Les données ont été chargées dans votre classeur :
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>étapes suivantes
Dans cet article, vous avez appris à utiliser Power Query pour extraire des données de HDInsight et les importer dans Excel. De la même façon, vous pouvez extraire des données de HDInsight et les importer dans une base de données SQL Azure. Il est également possible de télécharger des données dans HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Visualiser des données Hive à l’aide de Microsoft Power BI dans Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Hive avec Data Lake Tools pour Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](../hdinsight-for-vscode.md).
* [Charger des données dans HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
