---
title: Configuration de stratégies Hive dans HDInsight joint à un domaine - Azure | Microsoft Docs
description: Découvrir...
services: hdinsight
documentationcenter: ''
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 919a0a45c4c8c94b19ed1d602ed20df0afabd09a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>Configurer les stratégies Hive dans HDInsight joint à un domaine
Découvrez comment configurer des stratégies Apache Ranger pour Hive. Dans cet article, vous créez deux stratégies Ranger pour restreindre l’accès à hivesampletable. hivesampletable dispose de clusters HDInsight. Après avoir configuré les stratégies, vous utilisez Excel et le pilote ODBC pour vous connecter à des tables Hive dans HDInsight.

## <a name="prerequisites"></a>Prérequis

* Un cluster HDInsight joint à un domaine. Consultez [Configuration de cluster HDInsight joints à un domaine](apache-domain-joined-configure.md).
* Une station de travail avec Office 2016, Office 2013 ProPlus, Office 365 Pro Plus, l’édition autonome d’Excel 2013 ou Office Professionnel Plus 2010.

## <a name="connect-to-apache-ranger-admin-ui"></a>Connexion à l’interface utilisateur Apache Ranger
**Pour vous connecter à l’interface utilisateur Ranger**

1. Dans un navigateur, connectez-vous à l’interface utilisateur Ranger. L’URL est https://&lt;ClusterName>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > Ranger utilise des informations d’identification différentes de celles du cluster Hadoop. Pour empêcher les navigateurs d’utiliser les informations d’identification Hadoop mises en cache, utilisez une nouvelle fenêtre de navigation InPrivate pour vous connecter à l’interface utilisateur administrateur Ranger.
   >
   >
2. Connectez-vous avec le nom d’utilisateur et le mot de passe du domaine de l’administrateur du cluster :

    ![Page d’accueil Ranger joint à un domaine HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Actuellement, Ranger fonctionne uniquement avec Yarn et Hive.

## <a name="create-domain-users"></a>Création d’utilisateurs du domaine
Pour plus d’informations sur la création de hiveruser1 et hiveuser2, voir [Créer un cluster HDInsight joint à un domaine](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster). Dans ce didacticiel, vous utilisez les deux comptes utilisateur.

## <a name="create-ranger-policies"></a>Création de stratégies Ranger
Dans cette section, vous créez deux stratégies Ranger pour accéder à hivesampletable. Vous accordez une autorisation select sur différents ensembles de colonnes. Les deux utilisateurs ont été créés en suivant les instructions de l’article [Créer un cluster HDInsight joint à un domaine](apache-domain-joined-configure-using-azure-adds.md#create-a-domain-joined-hdinsight-cluster). Dans la section suivante, vous allez tester les deux stratégies dans Excel.

**Pour créer des stratégies Ranger**

1. Ouvrez l’interface utilisateur Ranger. Consultez [Connexion à l’interface utilisateur Apache Ranger](#connect-to-apache-ranager-admin-ui).
2. Cliquez sur **&lt;ClusterName>_hive** sous **Hive**. Deux stratégies préconfigurées doivent s’afficher.
3. Cliquez sur **Ajouter une nouvelle stratégie**, puis entrez les valeurs suivantes :

   * Nom de la stratégie : read-hivesampletable-all
   * Base de données Hive : par défaut
   * table : hivesampletable
   * Colonne Hive : *
   * Sélectionner un utilisateur : hiveuser1
   * Autorisations : select

     ![Configuration de stratégie Hive pour Ranger joint à un domaine HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Si un utilisateur de domaine n’est pas renseigné dans Sélectionner un utilisateur, attendez quelques instants que Ranger se synchronise avec AAD.
     >
     >
4. Cliquez sur **Ajouter** pour enregistrer la stratégie.
5. Répétez les deux dernières étapes pour créer une autre stratégie avec les propriétés suivantes :

   * Nom de la stratégie : read-hivesampletable-devicemake
   * Base de données Hive : par défaut
   * table : hivesampletable
   * Colonne Hive : clientid, devicemake
   * Sélectionner un utilisateur : hiveuser2
   * Autorisations : select

## <a name="create-hive-odbc-data-source"></a>Création d’une source de données ODBC Hive
Vous trouverez les instructions dans [Création d’une source de données ODBC Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Propriété  |Description |
 | --- | --- |
 | Data Source Name | Donnez un nom à votre source de données |
 | Host | Entrez &lt;HDInsightClusterName>.azurehdinsight.net. Par exemple, myHDICluster.azurehdinsight.net |
 | Port | Utilisez **443**. (ce port est passé de 563 à 443). |
 | Base de données | Utilisez **Default**. |
 | Hive Server Type | Sélectionnez **Hive Server 2**. |
 | Mechanism | Sélectionnez **Azure HDInsight Service**. |
 | HTTP Path | Laissez cette valeur vide. |
 | User Name | Entrez hiveuser1@contoso158.onmicrosoft.com. Mettez à jour le nom de domaine s’il est différent. |
 | Mot de passe | Entrez le mot de passe pour hiveuser1. |

Veillez à cliquer sur **Test** avant d’enregistrer la source de données.

## <a name="import-data-into-excel-from-hdinsight"></a>Importation de données dans Microsoft Excel à partir de HDInsight
Dans la dernière section, vous avez configuré deux stratégies.  hiveuser1 a l’autorisation select sur toutes les colonnes et hiveuser2 a l’autorisation select sur deux colonnes. Dans cette section, vous représentez les deux utilisateurs pour importer des données dans Excel.

1. Ouvrez un nouveau classeur ou un classeur existant dans Excel.
2. À partir de l’onglet **Données**, cliquez sur **À partir d’autres sources de données**, puis sur **Provenance : Assistant Connexion de données** pour lancer **l’Assistant Connexion de données**.

    ![Assistant Ouvrir la connexion de données][img-hdi-simbahiveodbc.excel.dataconnection]
3. Sélectionnez **DSN ODBC** comme source de données, puis cliquez sur **Suivant**.
4. Dans Sources de données ODBC, sélectionnez le nom de la source de données que vous avez créée à l’étape précédente, puis cliquez sur **Suivant**.
5. Saisissez à nouveau le mot de passe pour le cluster dans l’Assistant, puis cliquez sur **OK**. Attendez l'ouverture de la boîte de dialogue **Sélection d'une base de données et d'une table** . Cette opération peut prendre quelques secondes.
6. Sélectionnez **hivesampletable**, puis cliquez sur **Suivant**.
7. Cliquez sur **Terminer**.
8. Dans la boîte de dialogue **Importation de données** , vous pouvez modifier ou spécifier la requête. Pour cela, cliquez sur **Propriétés**. Cette opération peut prendre quelques secondes.
9. Cliquez sur l’onglet **Définition**. Le texte de commande est le suivant :

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Selon les stratégies Ranger que vous avez définies, hiveuser1 a l’autorisation select sur toutes les colonnes.  Par conséquent, cette requête fonctionne avec les informations d’identification de hiveuser1, mais cette requête ne fonctionne pas avec les informations d’identification de hiveuser2.

   ![Propriétés de connexion][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Cliquez sur **OK** pour fermer la boîte de dialogue Propriétés de connexion.
11. Cliquez sur **OK** pour fermer la boîte de dialogue **Importation de données**.  
12. Entrez à nouveau le mot de passe pour hiveuser1 et cliquez sur **OK**. Patientez quelques secondes pour que les données soient importées dans Excel. Une fois terminé, 11 colonnes de données doivent s’afficher.

Pour tester la deuxième stratégie (read-hivesampletable-devicemake) que vous avez créée dans la dernière section

1. Ajoutez une nouvelle feuille dans Excel.
2. Suivez la procédure précédente pour importer les données.  La seule modification à effectuer consiste à utiliser les informations d’identification de hiveuser2 au lieu de hiveuser1. Cette opération échoue, car hiveuser2 n’est autorisé à afficher que deux colonnes. Vous devez obtenir l’erreur suivante :

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Suivez la même procédure pour importer les données. Cette fois, utilisez les informations d’identification de hiveuser2 et modifiez également l’instruction select de :

        SELECT * FROM "HIVE"."default"."hivesampletable"

    to:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Une fois terminé, deux colonnes de données importées doivent s’afficher.

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un cluster HDInsight joint à un domaine, consultez [Configuration de clusters HDInsight joints à un domaine](apache-domain-joined-configure.md).
* Pour gérer un cluster HDInsight joint à un domaine, consultez [Gestion de clusters HDInsight joints à un domaine](apache-domain-joined-manage.md).
* Pour exécuter des requêtes Hive en utilisant SSH sur des clusters HDInsight joints au domaine, voir [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Pour connecter Hive à l’aide de Hive JDBC, consultez [Se connecter à Hive sur Azure HDInsight à l’aide du pilote Hive JDBC](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Pour connecter Excel à Hadoop à l’aide de Hive ODBC, consultez [Connexion d’Excel à Hadoop à l’aide du pilote Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Pour connecter Excel à Hadoop à l’aide de Power Query, consultez [Connexion d’Excel à Hadoop à l’aide de Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
