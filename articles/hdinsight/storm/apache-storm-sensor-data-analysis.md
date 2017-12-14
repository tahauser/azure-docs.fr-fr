---
title: "Analyse des données de capteur avec Apache Storm et HBase | Microsoft Docs"
description: "Découvrez comment vous connecter à Apache Storm à l’aide d’un réseau virtuel. Utilisez Storm avec HBase pour traiter des données de capteur à partir d'un hub d'événements et les visualiser avec D3.js."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/19/2017
ms.author: larryfr
ms.openlocfilehash: 8c8cda26f2b9b564dee330e4883ec12f39feb652
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analyser les données de capteur avec Apache Storm, Event Hub, et HBase dans HDInsight (Hadoop)

Découvrez comment utiliser Apache Storm dans HDInsight pour traiter les données de capteur à partir d’Azure Event Hub. Les données sont ensuite stockées dans Apache HBase sur HDInsight et visualisées à l’aide de D3.js.

Le modèle Azure Resource Manager utilisé dans ce document montre comment créer plusieurs ressources Azure dans un groupe de ressources. Le modèle crée un réseau virtuel Azure, deux clusters HDInsight (Storm et HBase) et une application web Azure. Une implémentation node.js de tableau de bord web en temps réel est automatiquement déployée sur l’application web.

> [!NOTE]
> Les informations et l’exemple contenus dans ce document nécessitent la version 3.6 de HDInsight.
>
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="architecture"></a>Architecture

![Diagramme d'architecture](./media/apache-storm-sensor-data-analysis/devicesarchitecture.png)

Cet exemple est constitué des composants suivants :

* **Azure Event Hubs**: contient les données collectées à partir des capteurs.
* **Storm sur HDInsight**: fournit le traitement en temps réel des données à partir d’Event Hub.
* **HBase sur HDInsight**: fournit un magasin de données NoSQL persistant pour les données une fois celles-ci traitées par Storm.
* **Service Azure Virtual Network**: permet des communications sécurisées entre Storm sur HDInsight et les clusters HBase sur HDInsight.
  
  > [!NOTE]
  > Un réseau virtuel est requis lors de l’utilisation de l’API cliente Java HBase. Il n’est pas exposé via la passerelle publique pour les clusters HBase. L’installation de clusters HBase et Storm dans le même réseau virtuel permet au cluster Storm (ou à tout autre système sur le réseau virtuel) d’accéder directement à HBase à l’aide de l’API cliente.

* **Site web de tableau de bord**: un exemple de tableau de bord qui suit des données en temps réel.
  
  * Le site web est implémenté dans Node.js.
  * [Socket.io](http://socket.io/) est utilisé pour la communication en temps réel entre la topologie Storm et le site web.
    
    > [!NOTE]
    > L’utilisation de Socket.io pour la communication est un détail d’implémentation. Vous pouvez utiliser toute infrastructure de communications, telles que WebSockets brut ou SignalR.

  * [D3.js](http://d3js.org/) est utilisé pour représenter par un graphique les données envoyées au site web.

> [!IMPORTANT]
> Deux clusters sont nécessaires, car il n’existe aucune méthode prise en charge pour créer un cluster HDInsight pour Storm et HBase.

La topologie lit les données à partir d’Event Hub à l’aide de la classe `org.apache.storm.eventhubs.spout.EventHubSpout` et écrit des données dans HBase à l’aide la classe `org.apache.storm.hbase.bolt.HBaseBolt`. La communication avec le site web s'effectue à l'aide de [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Le diagramme suivant décrit la disposition de la topologie :

![diagramme de topologie](./media/apache-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Ce diagramme est un affichage très simplifié de la topologie. Une instance de chaque composant est créée pour chaque partition de votre hub d’événements. Ces instances sont réparties entre les nœuds du cluster et les données sont acheminées entre eux comme suit :
> 
> * Les données du spout vers l'analyseur disposent d’un équilibrage de charges.
> * Les données de l’analyseur vers le tableau de bord et HBase sont regroupées par ID de périphérique afin que les messages issus du même périphérique passent toujours par le même composant.

### <a name="topology-components"></a>Composants de la topologie

* **Spout EventHub** : spout fourni dans le cadre de la version 0.10.0 et ultérieure d’Apache Storm.
  
  > [!NOTE]
  > Le spout Event Hub utilisé dans cet exemple requiert une version 3.5 ou 3.6 de cluster Storm sur HDInsight.

* **ParserBolt.java**: les données émises par le spout sont JSON brut, et parfois, plusieurs événements sont émis à la fois. Ce bolt lit les données émises par le spout et analyse le message JSON. Le bolt émet ensuite les données sous forme de tuple qui contient plusieurs champs.
* **DashboardBolt.java** : ce composant montre comment utiliser la bibliothèque client Socket.io pour Java pour envoyer des données en temps réel au tableau de bord web.
* **no-hbase.yaml** : la définition de la topologie utilisée lors de l’exécution en mode local. Les composants de HBase ne sont pas utilisés.
* **no-hbase.yaml** : la définition de la topologie utilisée lors de l’exécution de cette dernière sur la cluster. Les composants de HBase sont utilisés.
* **dev.Properties** : les informations de configuration relatives aux composants du spot Event Hub, du bolt HBase et du tableau de bord.

## <a name="prepare-your-environment"></a>Préparation de votre environnement

Avant d’utiliser cet exemple, vous devez préparer votre environnement de développement. Vous devez également créer un hub d’événements Azure, qui est utilisé par cet exemple.

Les éléments suivants doivent être installés dans votre environnement de développement :

* [Node.js](http://nodejs.org/) : utilisé pour afficher un aperçu du tableau de bord web localement sur votre environnement de développement.
* [Java et JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): utilisés pour développer la topologie Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): utilisé pour générer et compiler le projet.
* [Git](http://git-scm.com/): utilisé pour télécharger le projet à partir de GitHub.
* Client **SSH** : utilisé pour se connecter aux clusters HDInsight basés sous Linux. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

Pour créer un hub d’événements, utilisez les étapes répertoriées dans le document [Créer un hub d’événements](../../event-hubs/event-hubs-create.md).

> [!IMPORTANT]
> Enregistrez le nom du hub d’événements, l’espace de noms et la clé pour RootManageSharedAccessKey. Ces informations sont utilisées pour configurer la topologie Storm.

Vous n’avez pas besoin d’un cluster HDInsight. Les étapes décrites dans ce document fournissent un modèle Azure Resource Manager qui crée les ressources requises par cet exemple. Le modèle crée les ressources suivantes :
 
* un réseau virtuel Azure ;
* un cluster Storm sur HDInsight (basé sur Linux, 2 nœuds Worker) ;
* un cluster HBase sur HDInsight (basé sur Linux, 2 nœuds Worker) ;
* une application web Azure qui héberge le tableau de bord web.

## <a name="download-and-configure-the-project"></a>Téléchargement et configuration du projet

Utilisez la commande suivante pour télécharger le projet à partir de GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Une fois la commande terminée, vous obtenez la structure de répertoire suivante :

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Ce document ne détaille pas le code inclus dans cet exemple. Toutefois, le code est entièrement commenté.

Pour configurer le projet afin de lire à partir du hub d’événements, ouvrez le fichier `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` et ajoutez les informations de votre hub d’événements aux lignes suivantes :

```bash
eventhub.policy.key: the_key_for_RootManageSharedAccessKey
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>Compilation et test local

> [!IMPORTANT]
> L’utilisation locale de la topologie nécessite un environnement de développement Storm en cours. Pour plus d’informations, consultez la page [Configurer un environnement de développement Storm](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) à l’adresse Apache.org.

Avant de tester, vous devez lancer le tableau de bord pour afficher la sortie de la topologie et de générer les données à stocker dans le hub d'événements.

> [!IMPORTANT]
> Le composant HBase de cette topologie n’est pas actif lors d’un test local. L’API Java du cluster HBase n’est pas accessible depuis l’extérieur du réseau virtuel Azure qui contient les clusters.

### <a name="start-the-web-application"></a>Démarrage de l’application Web

1. Ouvrez une invite de commandes et passez au répertoire `hdinsight-eventhub-example/dashboard`. Utilisez la commande suivante pour installer les dépendances requises par l’application web :
   
    ```bash
    npm install
    ```

2. Utilisez la commande suivante pour démarrer l’application Web.
   
    ```bash
    node server.js
    ```
   
    Un message similaire au texte suivant s’affiche :
   
        Server listening at port 3000

3. Ouvrez un navigateur web et entrez `http://localhost:3000/` en tant qu’adresse. Une page similaire à celle ci-dessous s’affiche :
   
    ![tableau de bord web](./media/apache-storm-sensor-data-analysis/emptydashboard.png)
   
    Laissez l’invite de commandes ouverte. Après le test, utilisez Ctrl-C pour arrêter le serveur Web.

### <a name="generate-data"></a>Générer des données

> [!NOTE]
> Les étapes de cette section utilisent Node.js de sorte à pouvoir être utilisées sur n’importe quelle plate-forme. Pour d'autres exemples de langue, consultez le répertoire`SendEvents`.

1. Ouvrez une nouvelle invite, un nouvel interpréteur de commandes ou un nouveau terminal et passez au répertoire `hdinsight-eventhub-example/SendEvents/nodejs`. Utilisez la commande suivante pour installer les dépendances requises par l’application :

    ```bash
    npm install
    ```

2. Ouvrez le fichier `app.js` dans un éditeur de texte et ajoutez les informations du hub d'événements obtenues précédemment :
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'Your-eventhub-namespace';
    // Event Hub Name
    var hubname ='Your-eventhub-name';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'RootManageSharedAccessKey';
    var my_key = 'Your-Key';
    ```
   
   > [!NOTE]
   > Cet exemple suppose que vous utilisez `RootManageSharedAccessKey` pour accéder à votre hub d’événements.

3. Utilisez la commande suivante pour insérer de nouvelles entrées dans le hub d'événements :
   
    ```bash
    node app.js
    ```
   
    Vous pouvez voir plusieurs lignes de sortie contenant les données envoyées au concentrateur d’événements :
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="build-and-start-the-topology"></a>Création et démarrage de la topologie

1. Ouvrez une nouvelle invite de commandes et passez au répertoire `hdinsight-eventhub-example/TemperatureMonitor`. Pour générer et empaqueter la topologie, utilisez la commande suivante : 

    ```bash
    mvn clean package
    ```

2. Utilisez la commande suivante pour démarrer la topologie en mode local :

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local` démarre la topologie en mode local.
    * `--filter`utilise le fichier `dev.properties` pour remplir les paramètres dans la définition de la topologie.
    * `resources/no-hbase.yaml` utilise la définition de la topologie `no-hbase.yaml`.
 
   Une fois démarrée, la topologie lit les entrées à partir du concentrateur d’événements et les envoie au tableau de bord s’exécutant sur votre ordinateur local. Vous devriez voir des lignes apparaître dans le tableau de bord web, comme illustré dans l’image suivante :
   
    ![tableau de bord avec données](./media/apache-storm-sensor-data-analysis/datadashboard.png)

2. Lorsque le tableau de bord est en cours d’exécution, utilisez la commande `node app.js` des étapes précédentes pour envoyer de nouvelles données aux Event Hubs. Les valeurs de température étant générées au hasard, le graphique doit être mis à jour pour afficher les modifications importantes de température.
   
   > [!NOTE]
   > Vous devez être dans le répertoire **hdinsight-eventhub-example/SendEvents/Nodejs** lorsque vous utilisez la commande `node app.js`.

3. Après avoir vérifié que le tableau de bord est mis à jour, arrêtez la topologie en tapant Ctrl + C. Vous pouvez également utiliser Ctrl+C pour arrêter le serveur web local.

## <a name="create-a-storm-and-hbase-cluster"></a>Créer un cluster Storm et HBase

Les étapes décrites dans cette section utilisent un [modèle Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md) pour créer un réseau virtuel Azure et des clusters Storm et HBase sur le réseau virtuel. Le modèle crée également une application web Azure et déploie une copie du tableau de bord dans celle-ci.

> [!NOTE]
> Un réseau virtuel est utilisé afin que la topologie en cours d’exécution sur le cluster Storm puisse communiquer directement avec le cluster HBase à l’aide de l’API Java HBase.

Le modèle Resource Manager utilisé dans ce document se trouve dans un conteneur d’objets blob public, disponible à l’adresse **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**.

1. Cliquez sur le bouton suivant pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/apache-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir de la section **Déploiement personnalisé**, entrez les valeurs suivantes :
   
    ![Paramètres HDInsight](./media/apache-storm-sensor-data-analysis/parameters.png)
   
   * **Nom de cluster de base** : cette valeur sera utilisée comme nom de base pour les clusters Storm et HBase. Par exemple, si vous entrez **abc**, cela crée un cluster Storm nommé **storm-abc** et un cluster HBase nommé **hbase-abc**.
   * **Nom d’utilisateur de connexion au cluster** : nom d’utilisateur Admin pour les clusters Storm et HBase.
   * **Mot de passe de connexion au cluster** : mot de passe d’utilisateur Admin pour les clusters Storm et HBase.
   * **Nom d’utilisateur SSH** : utilisateur SSH permettant de créer des clusters Storm et HBase.
   * **Mot de passe SSH** : mot de passe de l’utilisateur SSH pour les clusters Storm et HBase.
   * **Emplacement** : région dans laquelle seront créés les clusters.
     
     Cliquez sur **OK** pour enregistrer les paramètres.

3. Utilisez la section **Concepts de base** pour créer un groupe de ressources ou sélectionnez un groupe existant.
4. Dans le menu déroulant **Emplacement du groupe de ressources**, sélectionnez le même emplacement que celui que vous avez sélectionné pour le paramètre **Emplacement** dans la section **Paramètres**.
5. Lisez les conditions générales, puis cochez la case **J’accepte les conditions générales mentionnées ci-dessus**.
6. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. La création des clusters prend environ 20 minutes.

Une fois les ressources créées, les informations relatives au groupe de ressources s’affichent.

![Groupe de ressources pour le réseau virtuel et les clusters](./media/apache-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Les noms des clusters HDInsight sont **storm-BASENAME** et **hbase-BASENAME**, où BASENAME est le nom que vous avez fourni pour le modèle. Vous utilisez ces noms dans une étape ultérieure, lors de la connexion aux clusters. Notez également que le nom du site du tableau de bord est **basename-dashboard**. Cette valeur sera utilisée ultérieurement dans ce document.

## <a name="configure-the-dashboard-bolt"></a>Configurer le bolt de tableau de bord

Pour envoyer des données au tableau de bord déployé en tant qu’application web, vous devez modifier la ligne suivante dans le fichier `dev.properties` :

```yaml
dashboard.uri: http://localhost:3000
```

Modifiez `http://localhost:3000` en `http://BASENAME-dashboard.azurewebsites.net` et enregistrez le fichier. Remplacez **BASENAME** par le nom de base que vous avez fourni à l’étape précédente. Vous pouvez également utiliser le groupe de ressources créé précédemment pour sélectionner le tableau de bord et afficher l’URL.

## <a name="create-the-hbase-table"></a>Créer une table HBase

Pour stocker des données dans HBase, nous devons tout d’abord créer une table. Créez au préalable les ressources sur lesquelles Storm doit écrire, car la création de ressources dans une topologie Storm peut faire que plusieurs instances essaient de créer la même ressource. Créez les ressources en dehors de la topologie et utilisez Storm pour les opérations de lecture/écriture et d’analyse.

1. Utilisez le protocole SSH pour vous connecter au cluster HBase à l’aide de l’utilisateur et du mot de passe SSH fournis pour le modèle lors de la création du cluster. Par exemple, si vous vous connectez à l’aide de la commande `ssh` , vous devez utiliser la syntaxe suivante :
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    Remplacez `sshuser` par le nom d’utilisateur SSH que vous avez utilisé lors de la création du cluster. Remplacez `clustername` par le nom du cluster HBase.

2. À partir de la session SSH, démarrez l’interpréteur de commandes HBase.
   
    ```bash
    hbase shell
    ```
   
    Une fois l’interpréteur de commandes chargé, une invite `hbase(main):001:0>` s’affiche.

3. À partir de l'environnement de ligne de commande HBase, entrez la commande suivante pour créer une table dans laquelle seront stockées les données du capteur :
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. Vérifiez que la table a été créée à l’aide de la commande suivante :
   
    ```hbase
    scan 'SensorData'
    ```
   
    Cela renvoie des informations similaires à l’exemple suivant, indiquant que la table comporte 0 ligne.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Entrez `exit` pour quitter le shell HBase :

## <a name="configure-the-hbase-bolt"></a>Configuration du bolt HBase

Pour écrire dans HBase à partir du cluster Storm , vous devez fournir le bolt HBase avec les détails de configuration de votre cluster HBase.

1. Utilisez un des exemples suivants pour récupérer le quorum Zookeeper pour votre cluster HBase :

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > Remplacez `your_HDInsight_cluster_name` par le nom de votre cluster HDInsight : Pour plus d’informations sur l’installation de l’utilitaire `jq`, consultez [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
    >
    > Lorsque vous y êtes invité, entrez le mot de passe de connexion administrateur HDInsight.

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > Remplacez `votre_HDInsight_cluster_name par le nom de votre cluster HDInsight. Lorsque vous y êtes invité, entrez le mot de passe de connexion administrateur HDInsight.
    >
    > Cet exemple nécessite Azure PowerShell. Pour plus d’informations sur l’utilisation d’Azure PowerShell, voir [Prise en main d’Azure PowerShell](https://docs.microsoft.com/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6)

    Les informations renvoyées par ces exemples sont semblables au texte suivant :

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Ces informations sont utilisées par Storm pour communiquer avec le cluster HBase.

2. Modifiez le fichier `dev.properties` et ajoutez les informations du quorum Zookeeper à la ligne suivante :

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Génération, mise en package et déploiement de la solution vers HDInsight

Dans votre environnement de développement, suivez les étapes ci-dessous pour déployer la topologie Storm vers le cluster Storm.

1. Depuis le répertoire `TemperatureMonitor`, utilisez la commande suivante pour exécuter une nouvelle version et créer un package JAR à partir de votre projet :
   
        mvn clean package
   
    Cette commande crée un fichier nommé `TemperatureMonitor-1.0-SNAPSHOT.jar in the `répertoire cible de votre projet.

2. Utilisez scp pour télécharger les fichiers `TemperatureMonitor-1.0-SNAPSHOT.jar` et `dev.properties` sur votre cluster Storm. Dans l’exemple suivant, remplacez `sshuser` par l’utilisateur SSH fourni lors de la création du cluster et `clustername` par le nom de votre cluster Storm. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > Le téléchargement des fichiers peut prendre plusieurs minutes.

    Pour en savoir plus sur l’utilisation des commandes `scp` et `ssh` avec HDInsight, voir [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Une fois le fichier téléchargé, connectez-vous au cluster Storm via SSH.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Remplacez `sshuser` par le nom d’utilisateur SSH. Remplacez `clustername` par le nom de cluster Storm.

4. Dans la session SSH, utilisez la commande suivante pour démarrer la topologie :
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote` envoie la topologie au service Nimbus, qui la distribue aux nœuds superviseurs du cluster.
    * `--filter`utilise le fichier `dev.properties` pour remplir les paramètres dans la définition de la topologie.
    * `-R /with-hbase.yaml`utilise la topologie `with-hbase.yaml` incluse dans le package.

5. Une fois la topologie lancée, ouvrez un navigateur vers le site Web que vous avez publié sur Azure, puis utiliser la commande `node app.js` pour envoyer des données au concentrateur d'événements. Vous devez mettre à jour le tableau de bord Web pour afficher les informations.
   
    ![dashboard](./media/apache-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Afficher les données HBase

Utilisez les étapes suivantes pour vous connecter à HBase et vérifier que les données ont été écrites dans la table :

1. Utilisez SSH pour vous connecter au cluster HBase.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Remplacez `sshuser` par le nom d’utilisateur SSH. Remplacez `clustername` par le nom du cluster HBase.

2. À partir de la session SSH, démarrez l’interpréteur de commandes HBase.
   
    ```bash
    hbase shell
    ```
   
    Une fois l’interpréteur de commandes chargé, une invite `hbase(main):001:0>` s’affiche.

3. Affichez les lignes de la table :
   
    ```hbase
    scan 'SensorData'
    ```
   
    Cette commande renvoie des informations similaires au texte suivant, indiquant que la table comporte des données.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > Cette opération d’analyse renvoie 10 lignes au maximum pour la table.

## <a name="delete-your-clusters"></a>Supprimer vos clusters

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Pour supprimer simultanément les clusters, le stockage et l’application web, supprimez le groupe de ressources qui les contient.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples de topologies Storm avec HDInsight, consultez les [exemples de topologies pour Storm sur HDInsight](apache-storm-example-topology.md)

Pour plus d'informations sur Apache Storm, consultez le [Apache Storm](https://storm.incubator.apache.org/) site.

Pour plus d'informations sur HBase avec HDInsight, veuillez consulter la [Présentation de HBase avec HDInsight](../hbase/apache-hbase-overview.md).

Pour plus d’informations sur Socket.io, consultez le site [socket.io](http://socket.io/) .

Pour plus d'informations sur D3.js, consultez la page [D3.js : documents pilotés par les données](http://d3js.org/).

Pour plus d'informations sur la création de topologies en Java, consultez [Développement de topologies Java pour Apache Storm sur HDInsight](apache-storm-develop-java-topology.md).

Pour plus d'informations sur la création de topologies en .NET, consultez [Développement de topologies C# pour Apache Storm sur HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
