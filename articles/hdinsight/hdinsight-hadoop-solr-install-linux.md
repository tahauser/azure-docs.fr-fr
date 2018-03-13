---
title: "Utiliser une action de script pour installer Solr sur HDInsight basé sur Linux - Azure | Documents Microsoft"
description: "Découvrez comment installer Solr sur des clusters Hadoop HDInsight basés sur Linux à l’aide des actions de script."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cc93ed5c-a358-456a-91a4-f179185c0e98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: larryfr
ms.openlocfilehash: f642a1f8060f566ec95b23995d0f82191b0c5315
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Installation et utilisation de Solr sur des clusters HDInsight Hadoop

Découvrez comment installer Solr sur Azure HDInsight en utilisant une action de script. Solr est une puissante plateforme de recherche qui fournit des fonctionnalités de recherche au niveau de l'entreprise pour les données gérées par Hadoop.

> [!IMPORTANT]
    > Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!IMPORTANT]
> L’exemple de script utilisé dans ce document installe un cluster Solr 4.9 avec une configuration spécifique. Si vous souhaitez configurer le cluster Solr avec d’autres collections, partitions, schémas, réplicas, etc., vous devez modifier le script et les fichiers binaires Solr.

## <a name="whatis"></a>Présentation de Solr

[Apache Solr](http://lucene.apache.org/solr/features.html) est une plateforme de recherche d’entreprises qui permet d’effectuer de puissantes opérations de recherche en texte intégral sur des données. Alors que Hadoop permet de stocker et de gérer de grandes quantités de données, Apache Solr fournit les fonctionnalités de recherche nécessaires pour les récupérer rapidement.

> [!WARNING]
> Les composants fournis avec le cluster HDInsight sont entièrement pris en charge par Microsoft.
>
> Les composants personnalisés, tels que Solr, bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Il se peut que le support Microsoft ne soit pas en mesure de résoudre les problèmes avec des composants personnalisés. Vous devrez peut-être contacter les communautés open source pour obtenir de l’aide. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org). Par exemple : [Hadoop](http://hadoop.apache.org/).

## <a name="what-the-script-does"></a>Ce que fait le script

Ce script effectue les modifications suivantes au cluster HDInsight :

* Installe Solr 4.9 dans `/usr/hdp/current/solr`
* Crée un nouvel utilisateur, **solrusr**, qui est utilisé pour exécuter le service Solr
* Définit **solruser** comme propriétaire de `/usr/hdp/current/solr`
* Ajoute une configuration [Upstart](http://upstart.ubuntu.com/) qui démarre Solr automatiquement.

## <a name="install"></a>Installer Solr à l’aide d’actions de script

Un exemple de script d’installation de Solr sur un cluster HDInsight est disponible à l’emplacement suivant :

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Pour créer un cluster sur lequel Solr est installé, utilisez les étapes décrites dans le document [Créer des clusters HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md). Pendant le processus de création, procédez comme suit pour installer Solr :

1. Dans la section __Résumé du cluster__ sélectionnez__Paramètres avancés__, puis __Actions de script__. Utilisez les informations suivantes pour remplir le formulaire :

   * **NAME**: saisissez un nom convivial pour l’action de script.
   * **URI du script** : https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
   * **HEAD**: cochez cette option.
   * **WORKER** : cochez cette option
   * **ZOOKEEPER** : cochez cette option pour installer le nœud ZooKeeper
   * **PARAMETERS**: laissez ce champ vide.

2. En bas de la section **Actions de script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, cliquez sur **Suivant** pour revenir au __résumé du cluster__

3. Sur la page __résumé du cluster__, sélectionnez __Créer__ pour créer le cluster.

## <a name="usesolr"></a>Utilisation de Solr dans HDInsight

> [!IMPORTANT]
> Les étapes de cette section présentent les fonctionnalités de base de Solr. Pour plus d’informations sur l’utilisation de Solr, consultez le [site Apache Solr](http://lucene.apache.org/solr/).

### <a name="index-data"></a>Données d'index

Procédez comme suit pour ajouter des exemples de données vers Solr, puis procédez à une requête :

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

     > [!IMPORTANT]
     > Les étapes suivantes de ce document utilisent un tunnel SSL pour se connecter à l’interface utilisateur Solr. Pour utiliser ces étapes, vous devez établir un tunnel SSL, puis configurer votre navigateur pour l’utiliser.
     >
     > Pour plus d’informations, consultez le document [Utilisation du tunnel SSH avec HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Pour obtenir des exemples de données Solr, utilisez les commandes suivantes :

    ```bash
    cd /usr/hdp/current/solr/example/exampledocs
    java -jar post.jar solr.xml monitor.xml
    ```

    Voici les résultats qui sont retournés à la console :

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    L’utilitaire `post.jar` ajoute les documents **solr.xml** et **monitor.xml** à l’index.
  
3. Pour interroger l’API REST de Solr, utilisez la commande suivante :

    ```bash
    curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"
    ```

    Cette commande recherche **collection1** pour tous les documents correspondant à  **\*:\***  (encodé sous la forme \*%3A\* dans la chaîne de requête). Le document JSON suivant est un exemple de réponse :

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }

### <a name="using-the-solr-dashboard"></a>Utilisation du tableau de bord Solr

Le tableau de bord Solr est une interface utilisateur web qui permet de travailler avec le mode série sur Solr via votre navigateur web. Le tableau de bord Solr n’est pas exposé directement sur Internet à partir de votre cluster HDInsight. Vous pouvez utiliser un tunnel SSH pour y accéder. Pour plus d’informations sur l’utilisation du tunnel SSH, consultez le document [Utilisation du tunnel SSH avec HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

Une fois que vous avez établi un tunnel SSH, procédez comme suit pour utiliser le tableau de bord Solr :

1. Déterminez le nom d’hôte du nœud principal primaire :

   1. Utilisez SSH pour vous connecter au nœud principal du cluster. Par exemple : `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.

       Pour plus d’informations sur l’utilisation de SSH, consultez le document [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

   2. Utilisez la commande suivante pour obtenir le nom d’hôte complet :

        ```bash
        hostname -f
        ```

        Cette commande retourne une valeur semblable au nom d’hôte suivant :

            hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

        Enregistrez la valeur retournée, car elle est utilisée ultérieurement.

2. Dans votre navigateur, connectez-vous à **http://HOSTNAME:8983/solr/#/**, où **HOSTNAME** correspond au nom que vous avez déterminé aux étapes précédentes.

    La requête est routée via le tunnel SSH jusqu’à l’interface utilisateur web Solr sur votre cluster. Le page est similaire à l’image suivante :

    ![Image du tableau de bord Solr.](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

3. Dans le volet de gauche, utilisez la liste déroulante **Sélecteur de base** pour sélectionner **collection1**. Plusieurs entrées doivent apparaître sous **collection1**.

4. Dans les entrées sous **collection1**, sélectionnez **Requête**. Utilisez les valeurs suivantes pour remplir la page de recherche :

   * Dans la zone de texte **q**, entrez **\*:**\*. La requête renvoie tous les documents indexés dans Solr. Si vous souhaitez rechercher une chaîne spécifique dans les documents, vous pouvez la saisir à cet emplacement.
   * Sélectionnez le format de sortie dans la zone de texte **wt** . La valeur par défaut est **json**.

     Enfin, sélectionnez le bouton **Exécuter la requête** au bas de la page de recherche.

     ![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

     Le résultat renvoie les deux documents que vous avez précédemment ajoutés à l’index. Le résultat ressemble au document JSON suivant :

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }

### <a name="starting-and-stopping-solr"></a>Démarrage et arrêt de Solr

Pour arrêter ou démarrer Solr manuellement, utilisez les commandes suivantes :

```bash
sudo stop solr
sudo start solr
```

## <a name="backup-indexed-data"></a>Sauvegarde de données indexées

Pour sauvegarder les données de Solr sur le stockage par défaut pour votre cluster, utilisez la procédure suivante :

1. Connectez-vous au cluster à l’aide de SSH, puis utilisez la commande suivante pour obtenir le nom d’hôte du nœud principal :

    ```bash
    hostname -f
    ```

2. Utilisez la commande suivante pour créer une capture instantanée des données indexées. Remplacez **HOSTNAME** par le nom retourné par la commande précédente :

    ```bash
    curl http://HOSTNAME:8983/solr/replication?command=backup
    ```

    La réponse ressemble au XML suivant :

        <?xml version="1.0" encoding="UTF-8"?>
        <response>
          <lst name="responseHeader">
            <int name="status">0</int>
            <int name="QTime">9</int>
          </lst>
          <str name="status">OK</str>
        </response>

3. Remplacez les répertoires par `/usr/hdp/current/solr/example/solr`. Il y a ici un sous-répertoire pour chaque collection. Chaque répertoire de la collection contient un répertoire `data` qui contient la capture instantanée de la collection.

4. Pour créer une archive compressée du dossier de capture d’instantané, utilisez la commande suivante :

    ```bash
    tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855
    ```

    Remplacez les valeurs `snapshot.20150806185338855` portant le nom de la capture d’instantané pour votre collection.

    Cette commande crée une archive nommée **snapshot.20150806185338855.tgz**, dans laquelle se trouve le contenu du répertoire **snapshot.20150806185338855**.

5. Vous pouvez ensuite stocker l’archive vers le stockage principal du cluster à l’aide de la commande suivante :

    ```bash
    hdfs dfs -put snapshot.20150806185338855.tgz /example/data
    ```

Pour plus d’informations sur l’utilisation de sauvegardes et de restaurations Solr, consultez [https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups).

## <a name="next-steps"></a>Étapes suivantes

* [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques à l’aide de Hadoop et peut être utilisé avec Azure HDInsight.

* [Installer Hue sur les clusters HDInsight](hdinsight-hadoop-hue-linux.md). Utilisez la personnalisation de clusters pour installer Hue sur des clusters HDInsight Hadoop. Hue est un ensemble d’applications web permettant d’interagir avec un cluster Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
