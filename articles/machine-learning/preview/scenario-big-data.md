---
title: "Prévision de charges de travail de serveur sur des téraoctets de données - Azure | Microsoft Docs"
description: "Découvrez comment effectuer l’apprentissage d’un modèle Machine Learning sur du Big Data avec Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: c8e023d68ec2c7e40675f985d3e13b0714cec8ea
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>Prévision de charges de travail de serveur sur des téraoctets de données

Cet article montre comment les scientifiques de données peuvent utiliser Azure Machine Learning Workbench pour développer des solutions devant utiliser du Big Data. Vous pouvez commencer par l’extrait d’un ensemble de données volumineuses, itérer la préparation des données, ajouter l’ingénierie et Machine Learning, puis étendre le processus à tout l’ensemble de données volumineuses. 

Vous apprendrez ce qu’il faut savoir sur les capacités clés de Machine Learning Workbench suivantes :
* Changement facile entre des cibles de calcul. Vous pouvez configurer différentes cibles de calcul et les utiliser pour expérimenter. Dans cet exemple, vous utiliserez une machine virtuelle de science des données Ubuntu et un cluster Azure HDInsight en tant que cibles de calcul. Vous pouvez aussi configurer les cibles de calcul en fonction de la disponibilité des ressources. Nous expliquerons en particulier comment, après la montée en puissance parallèle du cluster Spark avec plus de nœuds Worker, vous pouvez utiliser les ressources via Machine Learning Workbench pour accélérer les expérimentations.
* Suivi de l’historique des exécutions. Vous pouvez utiliser Machine Learning Workbench pour suivre les performances des modèles Machine Learning et autres mesures d’intérêt.
* Opérationnalisation du modèle Machine Learning. Vous pouvez utiliser les outils intégrés à Machine Learning Workbench pour déployer le modèle Machine Learning formé en tant que service web sur Azure Container Service. Vous pouvez aussi utiliser le service web pour obtenir des prédictions de mini-lots via des appels d’API REST. 
* Prise en charge de plusieurs téraoctets de données.

> [!NOTE]
> Pour des exemples de code et autres ressources relatives à ce cas, consultez [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

La prévision de la charge de travail sur des serveurs est un besoin métier commun aux entreprises technologiques qui gèrent leur propre infrastructure. Pour réduire les coûts d’infrastructure, les services qui s’exécutent sur des serveurs sous-exploités doivent être regroupés afin de les exécuter sur un plus petit nombre de machines. Ceux qui s’exécutent sur des serveurs sur-exploités doivent disposer de plus de machines. 

Dans ce scénario, vous vous concentrez sur la prédiction de la charge de travail pour chaque machine (ou serveur). Vous utilisez notamment les données de session sur chaque serveur pour prédire la classe de la charge de travail du serveur à l’avenir. Vous classifiez la charge de chaque serveur en trois classes, à savoir faible, moyenne et haute, à l’aide de Random Forest Classifier dans [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Dans cet exemple, les techniques de Machine Learning et le flux de travail peuvent facilement être étendus à d’autres problèmes similaires. 


## <a name="prerequisites"></a>configuration requise

Cet exemple nécessite les prérequis suivants :

* Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).
* Une copie installée [d’Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Pour installer le programme et créer un espace de travail, consultez le [guide de démarrage rapide relatif à l’installation](./quickstart-installation.md). Si vous avez plusieurs abonnements, vous pouvez [définir l’abonnement souhaité comme abonnement actif actuel](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* Windows 10 (les instructions de cet exemple s’appliquent en général avec les systèmes macOS).
* Une image DSVM (Data Science Virtual Machine) pour Linux (Ubuntu), de préférence dans la région États-Unis de l’Est où les données se trouvent. Vous pouvez approvisionner une machine virtuelle de science des données Ubuntu en suivant ces [instructions](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Vous pouvez aussi consulter ce [guide de démarrage rapide](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Nous recommandons d’utiliser une machine virtuelle avec au moins 8 cœurs et 32 Go de mémoire. 

Suivez les [instruction](known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present) pour activer l’accès à sudoer sans mot de passe sur la machine virtuelle pour AML Workbench.  Vous pouvez choisir d’utiliser [l’authentification basée sur la clé SSH pour la création et l’utilisation de la machine virtuelle dans AML Workbench](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). Dans cet exemple, nous utilisons le mot de passe pour accéder à la machine virtuelle.  Enregistrez le tableau suivant qui contient les informations sur la machine virtuelle de science des données pour les étapes ultérieures :

 Nom du champ| Valeur |  
 |------------|------|
Adresse IP de la machine virtuelle de science des données | xxx|
 Nom d'utilisateur  | xxx|
 Mot de passe   | xxx|


 Vous pouvez choisir d’utiliser une machine virtuelle avec le [moteur Docker](https://docs.docker.com/engine/) installé.

* Un cluster HDInsight Spark avec Hortonworks Data Platform version 3.6 et Spark version 2.1.x, de préférence dans la région États-Unis de l’Est où les données se trouvent. Consultez [Créer un cluster Apache Spark dans Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters) pour savoir comment créer des clusters HDInsight. Nous recommandons d’utiliser un cluster à trois Workers avec 16 cœurs et 112 Go de mémoire par Worker. Vous pouvez également sélectionner simplement le type de machine virtuelle « `D12 V2` » pour le nœud principal et « `D14 V2` » pour le nœud Worker. Le déploiement du cluster dure environ 20 minutes. Vous avez besoin du nom, du nom d’utilisateur SSH et du mot de passe du cluster pour exécuter cet exemple. Enregistrez le tableau suivant qui contient les informations sur le cluster Azure HDInsight pour les étapes ultérieures :

 Nom du champ| Valeur |  
 |------------|------|
 Nom du cluster| xxx|
 Nom d'utilisateur  | xxx (sshuser par défaut)|
 Mot de passe   | xxx|


* Un compte de stockage Azure. Vous pouvez suivre ces [instructions](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) pour en créer un. Créez également deux conteneurs d’objets blob privés nommés « `fullmodel` » et « `onemonthmodel` » dans ce compte de stockage. Le compte de stockage est utilisé pour enregistrer les résultats de calcul intermédiaires et les modèles Machine Learning. Vous avez besoin du nom et de la clé d’accès du compte de stockage pour exécuter cet exemple. Enregistrez le tableau suivant qui contient les informations sur le compte de stockage Azure pour les étapes ultérieures :

 Nom du champ| Valeur |  
 |------------|------|
 Nom du compte de stockage| xxx|
 Clé d’accès  | xxx|


La machine virtuelle de science des données Ubuntu et le cluster Azure HDInsight créés dans la liste de prérequis sont des cibles de calcul. Les cibles de calcul correspondent à la ressource de calcul dans le contexte de Machine Learning Workbench, qui peut être autre que l’ordinateur sur lequel est exécuté Machine Learning Workbench.   

## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Machine Learning Workbench.
2.  Dans la page **Projets**, sélectionnez le signe **+**, puis **Nouveau projet**.
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet.
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez **Prédiction de charge de travail sur des téraoctets de données** et sélectionnez le modèle.
5.  Sélectionnez **Créer**.

Vous pouvez créer un projet Workbench avec un référentiel Git créé au préalable en suivant ces [instructions](./tutorial-classifying-iris-part-1.md).  
Exécutez `git status` pour examiner l’état des fichiers de suivi de version.

## <a name="data-description"></a>Description des données

Les données utilisées dans cet exemple sont des données de charge de travail d’un serveur synthétisé. Elles sont hébergées dans un compte Stockage Blob Azure, accessible publiquement dans la région États-Unis de l’Est. Vous trouverez les informations propres au compte de stockage dans le champ `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) au format « wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path> ». Vous pouvez utiliser les données directement à partir du stockage Blob Azure. Si le stockage est utilisé simultanément par un grand nombre d’utilisateurs, vous pouvez utiliser [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) pour télécharger les données dans votre propre stockage, afin de bénéficier d’une meilleure expérience d’expérimentation. 

La taille totale des données est d’environ 1 To. Chaque fichier a une taille d’environ 1 à 3 Go et est au format de fichier CSV sans en-tête. Chaque ligne de données représente la charge d’une transaction sur un serveur particulier. Les informations détaillées du schéma de données sont les suivantes :

Numéro de colonne | Nom du champ| type | Description |  
|------------|------|-------------|---------------|
1  | `SessionStart` | DateTime |    Heure de début de session
2  |`SessionEnd`    | DateTime | Heure de fin de session
3 |`ConcurrentConnectionCounts` | Entier  | Nombre de connexions simultanées
4 | `MbytesTransferred` | Double | Données normalisées transférées en mégaoctets
5. | `ServiceGrade` | Entier  |  Niveau de service pour la session
6. | `HTTP1` | Entier |  La session utilise HTTP1 ou HTTP2
7 |`ServerType` | Entier    |Type de serveur
8 |`SubService_1_Load` | Double |   Charge du sous-service 1
9. | `SubService_2_Load` | Double |  Charge du sous-service 2
10 | `SubService_3_Load` | Double |     Charge du sous-service 3
11 |`SubService_4_Load` | Double |  Charge du sous-service 4
12 | `SubService_5_Load`| Double |      Charge du sous-service 5
13. |`SecureBytes_Load`  | Double | Charge sécurisée en octets
14 |`TotalLoad` | Double | Charge totale sur le serveur
15 |`ClientIP` | Chaîne|    Adresse IP du client
16 |`ServerIP` | Chaîne|    Adresse IP du serveur



Remarque : les types de données prévus sont répertoriés dans le tableau précédent. En raison de valeurs manquantes ou de problèmes de données modifiées, rien ne garantit que les types de données obtenus sont ceux prévus. Le traitement des données doit en tenir compte. 


## <a name="scenario-structure"></a>Structure du scénario

Les fichiers utilisés dans cet exemple sont structurés comme suit.

| Nom de fichier | type | Description |
|-----------|------|-------------|
| `Code` | Dossier | Dossier qui contient l’intégralité du code de l’exemple. |
| `Config` | Dossier | Dossier qui contient les fichiers de configuration. |
| `Image` | Dossier | Le dossier utilisé pour enregistrer les images du fichier LISEZ-MOI. |
| `Model` | Dossier | Dossier utilisé pour enregistrer les fichiers de modèle téléchargés à partir du stockage Blob. |
| `Code/etl.py` | Fichier Python | Fichier Python utilisé pour la préparation des données et l’ingénierie des fonctionnalités. |
| `Code/train.py` | Fichier Python | Fichier Python utilisé pour effectuer l’apprentissage d’un modèle de classification à trois classes.  |
| `Code/webservice.py` | Fichier Python | Le fichier Python utilisé pour l’opérationnalisation.  |
| `Code/scoring_webservice.py` | Fichier Python |  Fichier Python utilisé pour la transformation de données et l’appel du service web. |
| `Code/O16Npreprocessing.py` | Fichier Python | Fichier Python utilisé pour prétraiter les données nécessaires pour scoring_webservice.py.  |
| `Code/util.py` | Fichier Python | Fichier Python qui contient le code de lecture et d’écriture d’objets blob Azure.  
| `Config/storageconfig.json` | Fichier JSON | Fichier de configuration du conteneur d’objets blob Azure qui stocke les résultats intermédiaires et le modèle pour le traitement et l’apprentissage des données d’un mois. |
| `Config/fulldata_storageconfig.json` | Fichier JSON | Fichier de configuration pour le conteneur d’objets blob Azure qui stocke les résultats intermédiaires et le modèle pour le traitement et l’apprentissage de l’ensemble du jeu de données.|
| `Config/webservice.json` | Fichier JSON | Fichier de configuration pour scoring_webservice.py.|
| `Config/conda_dependencies.yml` | Fichier YAML | Fichier de dépendance Conda. |
| `Config/conda_dependencies_webservice.yml` | Fichier YAML | Fichier de dépendance Conda pour le service web.|
| `Config/dsvm_spark_dependencies.yml` | Fichier YAML | Fichier de dépendance Spark pour la machine virtuelle de science des données Ubuntu. |
| `Config/hdi_spark_dependencies.yml` | Fichier YAML | Fichier de dépendance Spark pour le cluster HDInsight Spark. |
| `README.md` | Fichier Markdown | Fichier Markdown LISEZ-MOI. |
| `Code/download_model.py` | Fichier Python | Fichier Python utilisé pour télécharger les fichiers de modèle à partir d’Azure Blob sur le disque local. |
| `Docs/DownloadModelsFromBlob.md` | Fichier Markdown | Fichier Markdown qui contient les instructions d’exécution de `Code/download_model.py`. |



### <a name="data-flow"></a>Flux de données

Le code figurant dans [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) charge les données à partir du conteneur accessible publiquement (champ `dataFile` de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Il inclut la préparation des données et l’ingénierie des fonctionnalités, et enregistre les résultats de calcul intermédiaires et les modèles dans votre conteneur privé. Le code figurant dans [`Code/train.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) charge les résultats de calcul intermédiaires à partir du conteneur privé, effectue l’apprentissage du modèle de classification multiclasse, puis écrit le modèle Machine Learning formé dans le conteneur privé. 

Pour l’expérimentation, vous devez utiliser un conteneur pour le jeu de données d’un mois, puis un autre conteneur pour l’ensemble du jeu de données. Étant donné que les données et les modèles sont enregistrés en tant que fichier Parquet, chaque fichier est en fait un dossier du conteneur, qui contient plusieurs objets blob. Le conteneur obtenu se présente comme suit :

| Nom du préfixe blob | type | Description |
|-----------|------|-------------|
| featureScaleModel | Parquet | Modèle de mesureur standard pour les fonctionnalités numériques. |
| stringIndexModel | Parquet | Modèle d’indexeur de chaîne pour les fonctionnalités non numériques.|
| oneHotEncoderModel|Parquet | Modèle d’encodeur à chaud pour les fonctionnalités catégorielles. |
| mlModel | Parquet | Modèle Machine Learning formé. |
| info| Fichier Pickle Python | Informations sur les données transformées, notamment le début et la fin de l’apprentissage, la durée, l’horodatage du fractionnement apprentissage-test et les colonnes pour l’indexation et l’encodage à chaud.

Tous les fichiers et objets blob du tableau précédent sont utilisés pour l’opérationnalisation.


### <a name="model-development"></a>Développement d’un modèle

#### <a name="architecture-diagram"></a>Diagramme de l’architecture


Le diagramme suivant illustre le flux de travail de bout en bout de l’utilisation de Machine Learning Workbench pour développer le modèle : ![architecture](media/scenario-big-data/architecture.PNG)

Dans les sections suivantes, nous allons décrire le développement d’un modèle à l’aide de la fonctionnalité de cible de calcul à distance dans Machine Learning Workbench. Nous chargeons d’abord un petit échantillon de données et exécutons le script [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) sur une machine virtuelle de science des données Ubuntu pour une itération rapide. Nous pouvons limiter encore plus notre travail dans [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) à l’aide d’un argument supplémentaire pour une itération plus rapide. Pour terminer, nous utilisons un cluster HDInsight pour effectuer l’apprentissage avec l’ensemble des données.     

Le fichier [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) charge et prépare les données, puis effectue l’ingénierie des fonctionnalités. Il accepte deux arguments :
* Un fichier de configuration pour le conteneur de stockage Blob, pour stocker les résultats de calcul et modèles intermédiaires.
* Un argument de configuration de débogage pour une itération rapide.

Le premier argument, `configFilename`, est un fichier de configuration local où vous stockez les informations de stockage Blob et indiquez l’emplacement où charger les données. Par défaut, il s’agit de [`Config/storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json) et il sera utilisé pour l’exécution des données d’un mois. Nous incluons également [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), que vous devez utiliser pour l’exécution de l’ensemble du jeu de données. La configuration contient les éléments suivants : 

| Champ | type | Description |
|-----------|------|-------------|
| storageAccount | Chaîne | Nom du compte de stockage Azure |
| storageContainer | Chaîne | Conteneur du compte de stockage Azure pour stocker les résultats intermédiaires |
| storageKey | Chaîne |Clé d’accès au compte de stockage Azure |
| dataFile|Chaîne | Fichiers de source de données  |
| duration| Chaîne | Durée des données dans les fichiers de source de données|

Modifiez à la fois `Config/storageconfig.json` et `Config/fulldata_storageconfig.json` pour configurer le compte de stockage, la clé de stockage et le conteneur d’objets blob pour stocker les résultats intermédiaires. Par défaut, le conteneur d’objets blob pour l’exécution des données d’un mois est `onemonthmodel` et le conteneur d’objets blob pour l’exécution de l’ensemble du jeu de données est `fullmodel`. Assurez-vous de créer ces deux conteneurs dans votre compte de stockage. Le champ `dataFile` dans [`Config/fulldata_storageconfig.json`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) configure les données qui sont chargées dans [`Code/etl.py`](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). Le champ `duration` configure la plage inclut par les données. Si la durée est définie sur ONE_MONTH, les données chargées doivent correspondre à un seul fichier csv parmi les sept fichiers de données pour juin 2016. Si la durée est définie sur FULL, l’ensemble du jeu de données (1 To) est chargé. Vous n’avez pas besoin de modifier `dataFile` et `duration` dans ces deux fichiers de configuration.

Le second argument est DEBUG. S’il est configuré sur FILTER_IP, l’itération est plus rapide. L’utilisation de ce paramètre est utile quand vous souhaitez déboguer votre script.

> [!NOTE]
> Remplacez toute variable d’argument dans toutes les commandes suivantes par sa valeur réelle.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>Développement d’un modèle sur le Docker d’une machine virtuelle de science des données Ubuntu

#####  <a name="1-set-up-the-compute-target"></a>1. Configurer la cible de calcul

Démarrez la ligne de commande de Machine Learning Workbench en sélectionnant **Fichier** > **Ouvrir l’invite de commande**. Exécutez ensuite la commande suivante : 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

Les deux fichiers suivants sont créés dans le dossier aml_config de votre projet :

-  dockerdsvm.compute: ce fichier contient les informations de connexion et de configuration pour une cible d’exécution distante.
-  dockerdsvm.runconfig: ce fichier est un ensemble d’options d’exécution utilisé dans l’application Workbench.

Accédez à dockerdsvm.runconfig et modifiez la configuration de ces champs par la suivante :

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

Préparez l’environnement de projet en exécutant :

```az ml experiment prepare -c dockerdsvm```


Si `PrepareEnvironment` est défini sur true, Machine Learning Workbench crée l’environnement d’exécution à chaque fois que vous soumettez une tâche. `Config/conda_dependencies.yml` et `Config/dsvm_spark_dependencies.yml` contiennent la personnalisation de l’environnement d’exécution. Vous pouvez toujours modifier les dépendances Conda, la configuration Spark et la configuration des dépendances Spark en modifiant ces deux fichiers YMAL. Pour cet exemple, nous avons ajouté `azure-storage` et `azure-ml-api-sdk` en tant que packages Python supplémentaires dans `Config/conda_dependencies.yml`. Nous avons aussi ajouté `spark.default.parallelism`, `spark.executor.instances` et `spark.executor.cores` dans `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Préparation des données et ingénierie des fonctionnalités sur le Docker d’une machine virtuelle de science des données

Exécutez le script `etl.py` sur le Docker de la machine virtuelle de science des données. Utilisez un paramètre de débogage qui filtre les données chargées avec les adresses IP de serveur spécifiques :

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Accédez au panneau latéral et sélectionnez **Exécuter** pour afficher l’historique des exécutions de `etl.py`. Remarque : le temps d’exécution est d’environ deux minutes. Si vous envisagez de modifier le code pour inclure de nouvelles fonctionnalités, vous pouvez spécifier FILTER_IP comme deuxième argument pour une itération plus rapide. Vous devrez peut-être réaliser cette étape plusieurs fois si vous rencontrez des problèmes de Machine Learning pour explorer le jeu de données ou créer de nouvelles fonctionnalités. 

À l’aide de la restriction personnalisée sur les données à charger et du filtrage supplémentaire des données à traiter, vous pouvez accélérer le processus d’itération du développement de votre modèle. Au fur et à mesure de vos essais, pensez à enregistrer régulièrement les modifications dans votre code pour le référentiel Git. Notez que nous avons utilisé le code suivant dans `etl.py` pour activer l’accès au conteneur privé :

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Exécutez ensuite le script `etl.py` sur le Docker de la machine virtuelle de science des données sans le paramètre de débogage FILTER_IP :

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Accédez au panneau latéral et sélectionnez **Exécuter** pour afficher l’historique des exécutions de `etl.py`. Remarque : le temps d’exécution est d’environ quatre minutes. Le résultat traité de cette étape est enregistré dans le conteneur et chargé pour l’apprentissage dans train.py. Par ailleurs, les indexeurs de chaîne, pipelines d’encodeur et mesureurs standard sont enregistrés dans le conteneur privé. Ils sont utilisés dans l’opérationnalisation. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. Apprentissage d’un modèle sur le Docker d’une machine virtuelle de science des données

Exécutez le script `train.py` sur le Docker de la machine virtuelle de science des données :

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Cette étape charge les résultats de calcul intermédiaires de l’exécution de `etl.py` et effectue l’apprentissage d’un modèle Machine Learning. Cette étape prend environ deux minutes.

Une fois correctement terminés vos essais sur les données de petite taille, vous pouvez étendre l’expérimentation à l’ensemble du jeu de données. Vous pouvez commencer en utilisant le même code, puis faire des essais en modifiant l’argument et la cible de calcul.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Développement d’un modèle sur un cluster HDInsight

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. Créez une cible de calcul dans Machine Learning Workbench pour le cluster HDInsight

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

Les deux fichiers suivants sont créés dans le dossier aml_config :
    
-  myhdi.compute : ce fichier contient les informations de connexion et de configuration pour une cible d’exécution distante.
-  myhdi.runconfig: ce fichier est un ensemble d’options d’exécution utilisé dans l’application Workbench.


Accédez à myhdi.runconfig et modifiez la configuration de ces champs par la suivante :

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

Préparez l’environnement de projet en exécutant :

```az ml experiment prepare -c myhdi```

Cette étape peut prendre jusqu’à sept minutes.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Préparation des données et ingénierie des fonctionnalités sur le cluster HDInsight

Exécutez le script `etl.py` avec les données complètes sur le cluster HDInsight :

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Étant donné que l’exécution de ce travail est relativement longue (environ deux heures), vous pouvez utiliser `-a` pour désactiver le flux de sortie. Une fois la tâche effectuée, vous pouvez consulter les journaux du pilote et du contrôleur dans **Historique des exécutions**. Si vous disposez d’un cluster plus grand, vous pouvez toujours reconfigurer les configurations dans `Config/hdi_spark_dependencies.yml` pour utiliser plusieurs instances ou un plus grand nombre de cœurs. Par exemple, si vous disposez d’un cluster à quatre nœuds Worker, vous pouvez augmenter la valeur de `spark.executor.instances` de 5 à 7. Vous pouvez afficher la sortie de cette étape dans le conteneur **fullmodel** de votre compte de stockage. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. Apprentissage d’un modèle sur un cluster HDInsight

Exécutez le script `train.py` avec fulldata sur un cluster HDInsight :

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Étant donné que l’exécution de ce travail est relativement longue (environ 30 minutes), vous pouvez utiliser `-a` pour désactiver le flux de sortie.

#### <a name="run-history-exploration"></a>Exploration de l’historique des exécutions

L’historique des exécutions est une fonctionnalité qui permet le suivi de votre expérimentation dans Machine Learning Workbench. Par défaut, l’historique suit la durée de l’expérimentation. Dans notre exemple spécifique, quand nous passons à l’ensemble du jeu de données pour `Code/etl.py` dans le cadre de l’expérimentation, nous constatons que la durée augmente considérablement. Vous pouvez également enregistrer des indicateurs de performances spécifiques à des fins de suivi. Pour activer le suivi des indicateurs de performances, ajoutez les lignes de code suivantes au début de votre fichier Python :
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Voici un exemple de suivi d’un indicateur de performances spécifique :

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Accédez à **Exécutions** dans le volet droit de Workbench pour afficher l’historique des exécutions pour chaque fichier Python. Vous pouvez également vous rendre sur votre référentiel GitHub. Une nouvelle branche dont le nom commence par « AMLHistory » a été créée pour suivre les modifications apportées à votre script à chaque exécution. 


### <a name="operationalize-the-model"></a>Opérationnaliser le modèle

Dans cette section, vous opérationnalisez le modèle créé dans les étapes précédentes en tant que service web. Vous apprenez aussi à utiliser le service web pour prédire la charge de travail. Utilisez des interfaces de ligne de commande (CLI) d’opérationnalisation Machine Learning pour empaqueter le code et les dépendances sous forme d’images Docker et publier le modèle en tant que service web en conteneur.

Vous pouvez utiliser l’invite de ligne de commande dans Machine Learning Workbench pour exécuter les CLI.  Vous pouvez également exécuter les interfaces CLI d’opérationnalisation sur Ubuntu Linux en suivant les instructions du [guide d’installation](./deployment-setup-configuration.md#using-the-cli). 

> [!NOTE]
> Remplacez toute variable d’argument dans toutes les commandes suivantes par sa valeur réelle. La procédure de cette section prend environ 40 minutes.
> 

Choisissez une chaîne unique comme environnement pour l’opérationnalisation. Dans le cas présent, nous utilisons la chaîne « [unique] » pour représenter la chaîne de votre choix.

1. Créez d’une part l’environnement pour l’opérationnalisation et d’autre part le groupe de ressources.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Remarque : vous pouvez utiliser Container Service comme environnement à l’aide de `--cluster` dans la commande `az ml env setup`. Vous pouvez opérationnaliser le modèle Machine Learning sur [Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Il se sert de [Kubernetes](https://kubernetes.io/) pour automatiser le déploiement, la mise à l’échelle et la gestion des applications en conteneur. L’exécution de cette commande prend environ 20 minutes. Utilisez la commande suivante pour vérifier si le déploiement s’est correctement effectué : 

        az ml env show -g [unique]rg -n [unique]

   Définissez l’environnement de déploiement comme celui que vous venez de créer en exécutant la commande suivante :

        az ml env set -g [unique]rg -n [unique]

2. Créer et utiliser un compte de gestion des modèles. Pour créer un compte de gestion des modèles, exécutez la commande suivante :

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Utilisez la gestion des modèles pour l’opérationnalisation en exécutant la commande suivante :

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Un compte de gestion des modèles est utilisé pour gérer les modèles et les services web. Depuis le portail Azure, vous voyez qu’un compte de gestion des modèles a été créé. Vous voyez les modèles, les manifestes, les images Docker et les services qui sont créés en utilisant ce compte de gestion des modèles.

3. Téléchargez et enregistrez les modèles.

   Téléchargez les modèles dans le conteneur **fullmodel** sur votre machine locale dans le répertoire de code. Ne téléchargez pas le fichier de données Parquet du nom de « vmlSource.parquet ». Ce n’est pas un fichier de modèle, mais un résultat de calcul intermédiaire. Vous pouvez également réutiliser les fichiers de modèle inclus dans le référentiel Git. Pour en savoir plus, consultez [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Accédez au dossier `Model` dans l’interface CLI et enregistrez les modèles comme suit :

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   La sortie de chaque commande fournit un ID de modèle nécessaire à l’étape suivante. Enregistrez-les dans un fichier texte pour une utilisation ultérieure.

4. Créez un manifeste pour le service web.

   Un manifeste inclut le code pour le service web, tous les modèles Machine Learning et les dépendances de l’environnement d’exécution. Accédez au dossier `Code` dans l’interface CLI et exécutez la commande suivante :

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   La sortie fournit un ID de manifeste pour l’étape suivante. 

   Restez dans le répertoire `Code`. Vous pouvez tester webservice.py en exécutant la commande suivante : 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Créez une image Docker. 

        az ml image create -n [unique]image --manifest-id $manifestID

   La sortie fournit un ID d’image pour l’étape suivante, car cette image Docker est utilisée dans Container Service. 

6. Déployez le service web pour le cluster Container Service.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   La sortie fournit un ID de service. Vous devez l’utiliser pour obtenir la clé d’autorisation et l’URL du service.

7. Appelez le service web dans le code Python pour effectuer la notation de mini-lots.

   Utilisez la commande suivante pour obtenir la clé d’autorisation :

         az ml service keys realtime -i $ServiceID 

   Utilisez la commande suivante pour obtenir l’URL de notation de service :

        az ml service usage realtime -i $ServiceID

   Modifiez le contenu de `./Config/webservice.json` avec l’URL de notation de service adéquate et la bonne clé d’autorisation. Conservez « Bearer » dans le fichier original, et remplacez la partie « xxx ». 
   
   Accédez au répertoire racine de votre projet et testez la notation de mini-lots par le service web en utilisant la commande suivante :

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. Mettez à l’échelle le service web. 

   Pour en savoir plus, consultez [How to scale operationalization on your Azure Container Service cluster](how-to-scale-clusters.md) (Mise à l’échelle d’une opérationnalisation sur un cluster Azure Container Service).
 

## <a name="next-steps"></a>étapes suivantes

Cet exemple montre comment utiliser Machine Learning Workbench pour effectuer l’apprentissage d’un modèle Machine Learning sur du Big Data et opérationnaliser le modèle formé. Vous avez appris à configurer et à utiliser différentes cibles de calcul, à exécuter l’historique des indicateurs de performances à des fins de suivi, et à utiliser différentes exécutions.

Vous pouvez étendre le code pour explorer la validation croisée et le réglage d’hyperparamètres. Pour en savoir plus sur la validation croisée et le réglage d’hyperparamètres, consultez cette [ressource GitHub](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Pour en savoir plus sur la prévision de time-series, consultez cette [ressource GitHub](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
