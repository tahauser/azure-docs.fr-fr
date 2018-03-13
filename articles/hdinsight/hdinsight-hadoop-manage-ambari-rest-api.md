---
title: "Surveiller et gérer Hadoop avec l’API REST Ambari - Azure HDInsight | Documents Microsoft"
description: "Découvrez comment utiliser Ambari pour surveiller et gérer les clusters Hadoop dans Azure HDInsight. Dans ce document, vous allez apprendre à utiliser l'API REST d'Ambari incluse avec les clusters HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/22/2018
ms.author: larryfr
ms.openlocfilehash: 44066a3b4d5bebc67b089bebc2f3c11d33c77000
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gérer des clusters HDInsight à l'aide de l'API REST d'Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Découvrez comment utiliser l’API REST Ambari pour gérer et surveiller les clusters Hadoop dans Azure HDInsight.

Apache Ambari simplifie la gestion et la surveillance d'un cluster Hadoop en fournissant une interface utilisateur web et une API REST faciles à utiliser. Ambari est inclus dans les clusters HDInsight qui utilisent le système d’exploitation Linux. Vous pouvez utiliser Ambari pour surveiller le cluster et apporter des modifications de configuration.

## <a id="whatis"></a>Présentation d'Ambari

[Apache Ambari](http://ambari.apache.org) fournit l’interface utilisateur web qui permet de gérer et de surveiller les clusters Hadoop. Les développeurs peuvent intégrer ces fonctionnalités dans leurs applications à l’aide des [API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari est fourni par défaut avec les clusters HDInsight Linux.

## <a name="how-to-use-the-ambari-rest-api"></a>Comment utiliser l’API REST Ambari

> [!IMPORTANT]
> Les informations et exemples décrits dans ce document nécessitent un cluster HDInsight utilisant Linux comme système d’exploitation. Pour plus d'informations, consultez [Prise en main de HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

Les exemples contenus dans ce document s’appliquent au Bourne shell (Bash) et à PowerShell. Les exemples Bash ont été testés avec GNU Bash 4.3.11, mais doivent fonctionner avec d’autres interpréteurs de commandes Unix. Les exemples PowerShell ont été testés avec PowerShell 5.0, mais devraient fonctionner avec PowerShell 3.0 ou version ultérieure.

Si vous utilisez le __Bourne shell__ (Bash), les éléments suivants doivent être installés :

* [cURL](http://curl.haxx.se/) : cURL est un utilitaire utilisable pour travailler en ligne de commande avec les API REST. Dans ce document, il est utilisé pour communiquer avec l'API REST d'Ambari.

Que vous utilisiez Bash ou PowerShell, vous devez également avoir installé [jq](https://stedolan.github.io/jq/). Jq est un utilitaire permettant de travailler avec des documents JSON. Il est utilisé dans **tous** les exemples de Bash et dans l’**un** des exemples de PowerShell.

### <a name="base-uri-for-ambari-rest-api"></a>URI de base pour l’API Rest Ambari

L’URI de base pour l’API REST sur les clusters HDInsight est https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, où **CLUSTERNAME** est le nom de votre Storm sur le cluster HDInsight.

> [!IMPORTANT]
> Bien que le nom du cluster dans la partie du nom de domaine complet (FQDN) de l’URI (CLUSTERNAME.azurehdinsight.net) ne respecte pas la casse, les autres occurrences dans l’URI respectent la casse. Par exemple, si le nom de votre cluster est `MyCluster`, les éléments suivants sont des URI valides :
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> Les URI suivantes renvoient une erreur, car la deuxième occurrence du nom n’utilise pas la casse correcte.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Authentification

Une connexion à Ambari sur HDInsight requiert HTTPS. Utilisez le nom du compte Administrateur (la valeur par défaut est **admin**) et le mot de passe fournis lors de la création du cluster.

## <a name="examples-authentication-and-parsing-json"></a>Exemples : Authentification et analyse de JSON

Les exemples suivants montrent comment effectuer une demande GET sur l’API REST Ambari de base :

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Les exemples de Bash dans ce document partent des hypothèses suivantes :
>
> * Le nom de connexion du cluster est la valeur par défaut de `admin`.
> * `$CLUSTERNAME` contient le nom du cluster. Vous pouvez définir cette valeur à l’aide de `set CLUSTERNAME='clustername'`.
> * Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion au cluster (admin).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> Les exemples de PowerShell dans ce document partent des hypothèses suivantes :
>
> * `$creds` est un objet d’informations d’identification qui contient l’identifiant et le mot de passe de l’administrateur pour l’accès au cluster. Vous pouvez définir cette valeur à l’aide de `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` et en fournissant les informations d’identification lorsque vous y êtes invité.
> * `$clusterName` est une chaîne qui contient le nom du cluster. Vous pouvez définir cette valeur à l’aide de `$clusterName="clustername"`.

Les deux exemples renvoient un document JSON qui commence avec des informations similaires à ce qui suit :

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>Analyse des données JSON

L’exemple suivant utilise `jq` pour analyser le document de réponse JSON et afficher uniquement les informations `health_report` à partir des résultats.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

PowerShell 3.0 et versions ultérieures fournissent l’applet de commande `ConvertFrom-Json` qui convertit le document JSON en un objet plus facile à utiliser avec PowerShell. L’exemple suivant utilise `ConvertFrom-Json` pour afficher uniquement les informations `health_report` à partir des résultats.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Alors que la plupart des exemples de ce document utilisent `ConvertFrom-Json` pour afficher les éléments du document de réponse, l’exemple [Mise à jour de la configuration Ambari](#example-update-ambari-configuration) utilise jq. Jq est utilisé dans cet exemple pour construire un nouveau modèle à partir du document de réponse JSON.

Pour obtenir une référence complète de l'API REST, consultez la page [Référence V1 de l'API d'Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Exemple : Obtenir le nom de domaine complet de nœuds de cluster

Lorsque vous travaillez avec HDInsight, vous pouvez avoir besoin de connaître le nom de domaine complet (FQDN) d'un nœud de cluster. Vous pouvez facilement récupérer le nom de domaine complet des différents nœuds du cluster à l’aide des exemples suivants :

* **Tous les nœuds**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **Nœuds principaux**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Nœuds de travail**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Nœuds Zookeeper**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Exemple : Obtenir l’adresse IP interne de nœuds de cluster

> [!IMPORTANT]
> Les adresses IP renvoyées par les exemples de cette section ne sont pas directement accessibles sur Internet. Elles sont uniquement accessibles au sein du réseau virtuel Azure qui contient le cluster HDInsight.
>
> Pour plus d’informations sur l’utilisation de HDInsight et des réseaux virtuels, consultez [Étendre les fonctionnalités HDInsight à l’aide d’un réseau virtuel Azure personnalisé](hdinsight-extend-hadoop-virtual-network.md).

Pour rechercher l’adresse IP, vous devez connaître le nom de domaine complet interne des nœuds de cluster. Lorsque cela est fait, vous pouvez ensuite obtenir l’adresse IP de l’hôte. Les exemples suivants interrogent d’abord Ambari pour connaître le nom de domaine complet de tous les nœuds de l’hôte, puis l’adresse IP de chaque hôte.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> Les exemples précédents vous invitent à entrer le mot de passe. Comme cet exemple exécute la commande `curl` plusieurs fois, le mot de passe `$PASSWORD` est fourni pour éviter plusieurs invites.

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Exemple : Obtenir le stockage par défaut

Lorsque vous créez un cluster HDInsight, vous devez utiliser un compte de stockage Azure ou Data Lake Store comme stockage par défaut du cluster. Vous pouvez utiliser Ambari pour récupérer ces informations après avoir créé le cluster. Par exemple, si vous voulez lire/écrire des données sur le conteneur en dehors de HDInsight.

Les exemples suivants récupèrent la configuration de stockage par défaut du cluster :

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> Ces exemples renvoient la première configuration appliquée au serveur (`service_config_version=1`), qui contient ces informations. Si vous récupérez une valeur qui a été modifiée après la création du cluster, vous devrez peut-être lister les versions de configuration et récupérer la plus récente.

La valeur de retour est similaire à l’un des exemples suivants :

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - Cette valeur indique que le cluster utilise un compte de stockage Azure pour le stockage par défaut. La valeur `ACCOUNTNAME` est le nom du compte de stockage. La partie `CONTAINER` est le nom du conteneur d’objets blob dans le compte de stockage. Le conteneur est la racine du stockage compatible HDFS du cluster.

* `adl://home` - Cette valeur indique que le cluster utilise Azure Data Lake Store pour le stockage par défaut.

    Pour rechercher le nom du compte Data Lake Store, utilisez les exemples suivants :

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    La valeur de retour est similaire à `ACCOUNTNAME.azuredatalakestore.net`, où `ACCOUNTNAME` est le nom du compte Data Lake Store.

    Pour rechercher le répertoire dans Data Lake Store qui contient le stockage du cluster, utilisez les exemples suivants :

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    La valeur de retour est similaire à `/clusters/CLUSTERNAME/`. Cette valeur est un chemin d’accès au sein du compte Data Lake Store. Ce chemin d’accès est la racine du système de fichiers compatible HDFS du cluster. 

> [!NOTE]
> L’applet de commande `Get-AzureRmHDInsightCluster` fournie par [Azure PowerShell](/powershell/azure/overview) retourne également les informations de stockage du cluster.


## <a name="example-get-configuration"></a>Exemple : obtenir la configuration

1. Récupérez les configurations disponibles pour votre cluster.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    Cet exemple renvoie un document JSON avec la configuration actuelle (identifiée par la valeur *tag* ) pour les composants installés sur le cluster. L’exemple suivant est un extrait des données renvoyées à partir d’un type de cluster Spark.
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. Récupérez la configuration du composant qui vous intéresse. Dans l’exemple suivant, remplacez `INITIAL` par la valeur de balise retournée à partir de la requête précédente.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Cet exemple renvoie un document JSON qui contient la configuration actuelle pour le composant `core-site`.

## <a name="example-update-configuration"></a>Exemple : mettre à jour la configuration

1. Obtenir la configuration actuelle, qu’Ambari enregistre comme « configuration souhaitée » :

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    Cet exemple renvoie un document JSON avec la configuration actuelle (identifiée par la valeur *tag* ) pour les composants installés sur le cluster. L’exemple suivant est un extrait des données renvoyées à partir d’un type de cluster Spark.
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    Dans cette liste, vous devez copier le nom du composant (par exemple, **spark\_thrift\_sparkconf** et la valeur **tag**).

2. Récupérez la configuration du composant et de la balise à l’aide des commandes suivantes :
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > Remplacez **spark-thrift-sparkconf** et **INITIAL** par le composant et la balise dont vous souhaitez récupérer la configuration.
   
    Jq est utilisé pour convertir les données récupérées à partir de HDInsight dans un nouveau modèle de configuration. Plus précisément, ces exemples permettent d’effectuer les actions suivantes :
   
    * Il crée une valeur unique qui contient la chaîne « version » et la date, qui est stockée dans `newtag`.

    * Il crée un document racine pour la nouvelle configuration souhaitée.

    * Il récupère le contenu du tableau `.items[]` et l’ajoute à l’élément **desired_config**.

    * Il supprime les éléments `href`, `version` et `Config`, car ils ne sont pas nécessaires à l’envoi d’une nouvelle configuration.

    * Ajoute un élément `tag` avec une valeur de `version#################`. La partie numérique est basée sur la date actuelle. Chaque configuration doit avoir une balise unique.
     
    Enfin, les données sont enregistrées dans le document `newconfig.json`. La structure du document doit se présenter comme suit :
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. Ouvrez le document `newconfig.json` et modifiez/ajoutez des valeurs dans l’objet `properties`. L’exemple suivant modifie la valeur de `"spark.yarn.am.memory"` de `"1g"` en `"3g"`. Il ajoute également `"spark.kryoserializer.buffer.max"` avec une valeur de `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Enregistrez le fichier une fois que vous avez apporté des modifications.

4. Utilisez les commandes suivantes pour envoyer la configuration mise à jour à Ambari.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Ces commandes envoient le contenu du fichier **newconfig.json** au cluster en tant que nouvelle configuration souhaitée. La demande renvoie un document JSON. L’élément **versionTag** de ce document doit correspondre à la version que vous avez envoyée et l’objet **configs** contient les modifications de configuration que vous avez demandées.

### <a name="example-restart-a-service-component"></a>Exemple : redémarrer un composant de service

À ce stade, si vous regardez l’interface web d’Ambari, le service Spark indique qu’il doit être redémarré pour que la nouvelle configuration prenne effet. Procédez comme suit pour redémarrer le service.

1. Utilisez ce qui suit pour activer le mode de maintenance du service Spark :

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Ces commandes envoient un document JSON au serveur qui active le mode de maintenance. Vous pouvez vérifier que le service est désormais en mode maintenance à l’aide de la demande suivante :
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    La valeur de retour est `ON`.

2. Ensuite, utilisez ce qui suit pour désactiver le service :

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    La réponse ressemble à ce qui suit :
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > La valeur `href` retournée par cet URI à l’aide de l’adresse IP interne du nœud du cluster. Pour l’utiliser depuis l’extérieur du cluster, remplacez la partie « 10.0.0.18:8080 » par le nom de domaine complet du cluster. 
    
    Les commandes suivantes récupèrent l’état de la demande :

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Une réponse de `COMPLETED` indique que la demande est terminée.

3. Une fois la demande précédente terminée, utilisez ce qui suit pour démarrer le service.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    Le service utilise désormais la nouvelle configuration.

4. Enfin, utilisez les éléments suivants pour désactiver le mode de maintenance.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une référence complète de l'API REST, consultez la page [Référence V1 de l'API d'Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

