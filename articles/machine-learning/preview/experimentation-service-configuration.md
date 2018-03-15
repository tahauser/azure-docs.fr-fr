---
title: "Configuration du service d’expérimentation Azure Machine Learning | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble du service d’expérimentation Azure Machine Learning et des instructions sur la façon de le configurer."
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: f93c74d0c2f66e6a5001289efca07f074e3d3c5a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="configuring-azure-machine-learning-experimentation-service"></a>Configuration du service d’expérimentation Azure Machine Learning

## <a name="overview"></a>Vue d'ensemble
Le service d’expérimentation Azure Machine Learning permet aux scientifiques des données de mener leurs expérimentations à l’aide des fonctionnalités de gestion et d’exécution d’Azure Machine Learning. Il fournit une infrastructure permettant une expérimentation agile avec des itérations rapides. Azure Machine Learning Workbench vous permet de commencer avec des exécutions locales sur votre ordinateur, puis de passer facilement à d’autres environnements plus conséquents comme des machines virtuelles de science des données (DSVM) avec GPU ou des clusters HDInsight exécutant Spark.

Le service d’expérimentation est conçu pour effectuer des exécutions isolées, reproductibles et cohérentes de vos expérimentations. Il vous aide à gérer vos cibles de calcul, environnements d’exécution et configurations de série de tests. Les fonctionnalités de gestion et d’exécution d’Azure Machine Learning Workbench vous permettent de vous déplacer facilement entre différents environnements. 

Vous pouvez exécuter un script Python ou PySpark dans un projet Workbench localement ou à grande échelle dans le cloud. 

Les environnements dans lesquels vous pouvez exécuter vos scripts sont les suivants : 

* Python (3.5.2) sur votre ordinateur local installé par Workbench
* Conda Python à l’intérieur d’un conteneur Docker sur un ordinateur local ;
* Environnement Python que vous possédez et gérez sur un ordinateur Linux distant
* Conda Python à l’intérieur d’un conteneur Docker sur une machine Linux distante, Par exemple, une [machine virtuelle de science des données basée sur la distribution Linux Ubuntu sur Azure] (https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* [HDInsight pour Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) sur Azure.

>[!IMPORTANT]
>Actuellement, le service d’expérimentation Azure Machine Learning prend en charge les versions de runtime Python 3.5.2 et Spark 2.1.11. 


### <a name="key-concepts-in-experimentation-service"></a>Concepts clés dans le service d’expérimentation
Il est important de comprendre les concepts suivants concernant l’exécution d’expérimentation Azure Machine Learning. Les sections suivantes expliquent en détail comment appliquer ces concepts. 

#### <a name="compute-target"></a>Cible de calcul
Une _cible de calcul_ spécifie l’emplacement où exécuter votre programme, par exemple votre bureau, un Docker distant sur une machine virtuelle ou un cluster. Une cible de calcul doit être adressable et accessible. Workbench vous permet de créer des cibles de calcul et de les gérer à l’aide de l’application Workbench et de l’interface de ligne de commande. 

La commande _az ml computetarget attach_ dans l’interface de ligne de commande permet de créer une cible de calcul utilisable dans les exécutions.

Les cibles de calcul prises en charge sont les suivantes :
* Environnement Python (3.5.2) sur votre ordinateur installé par Workbench
* Docker local sur votre ordinateur ;
* Environnement Python géré par l’utilisateur sur des machines virtuelles Ubuntu-Linux distantes. par exemple, une [machine virtuelle de science des données basée sur la distribution Linux Ubuntu sur Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) ;
* Docker distant sur machines virtuelles exécutant la distribution Linux Ubuntu, par exemple, une [machine virtuelle de science des données basée sur la distribution Linux Ubuntu sur Azure](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) ;
* [cluster HDInsight pour Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/) sur Azure.

Actuellement, le service d’expérimentation prend en charge les versions de runtime Python 3.5.2 et Spark 2.1.11. 

>[!IMPORTANT]
> Les machines virtuelles Windows exécutant Docker ne sont **pas** prises en charge en tant que cibles de calcul à distance.

#### <a name="execution-environment"></a>Environnement d’exécution
L’_environnement d’exécution_ définit la configuration de runtime et les dépendances nécessaires pour exécuter le programme dans Workbench.

Vous gérez l’environnement d’exécution local avec vos outils et gestionnaires de packages favoris en cas d’exécution sur le runtime par défaut de Workbench. 

Conda est utilisé pour gérer les exécutions sur Docker local et Docker distant, ainsi que les exécutions basées sur HDInsigh. Pour ces cibles de calcul, la configuration de l’environnement d’exécution est gérée par le biais des fichiers **Conda_dependencies.yml** et **Spark_dependencies.yml**. Ces fichiers se trouvent dans le dossier **aml_config** à l’intérieur de votre projet.

**Les runtimes pris en charge pour les environnements d’exécution sont les suivants :**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>Configuration de série de tests
En plus de la cible de calcul et de l’environnement d’exécution, Azure Machine Learning offre une infrastructure permettant de définir et de changer les *configurations de série de tests*. Dans le cadre d’une expérimentation itérative, les différentes exécutions de votre expérience peuvent nécessiter une configuration distincte. Vous balayez peut-être diverses plages de paramètres en utilisant des sources de données et paramètres de réglage Spark différents. Le service d’expérimentation fournit une infrastructure pour la gestion des configurations de série de tests.

L’exécution de la commande _az ml computetarget attach_ a pour effet de générer deux fichiers dans le dossier **aml_config** de votre projet : un dossier « .compute » et un dossier « .runconfig » suivant la convention de dénomination _<nom_de_votre_cible_de_calcul>.compute_ et _<nom_de_votre_cible_de_calcul>.runconfig_. Par souci de facilité, le fichier .runconfig est créé automatiquement lorsque vous créez une cible de calcul. Vous pouvez créer et gérer d’autres configurations de série de tests à l’aide de la commande _az ml runconfigurations_ dans l’interface de ligne de commande. Vous pouvez également les créer et modifier sur votre système de fichiers.

Une configuration de série de tests dans Workbench vous permet également de spécifier des variables d’environnement. Vous pouvez spécifier des variables d’environnement et les utiliser dans votre code en ajoutant la section suivante à votre fichier .runconfig. 

```
EnvironmentVariables:
    "EXAMPLE_ENV_VAR1": "Example Value1"
    "EXAMPLE_ENV_VAR2": "Example Value2"
```

Ces variables d’environnement sont accessibles dans votre code. Par exemple, cet extrait de code Python imprime la variable d’environnement nommée « EXAMPLE_ENV_VAR1 ».
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

_**La figure suivante illustre le flux de haut niveau pour la série de tests d’expérimentation initiale.**_
![](media/experimentation-service-configuration/experiment-execution-flow.png)

## <a name="experiment-execution-scenarios"></a>Scénarios d’exécution d’expérience
Cette section explore des scénarios d’exécution et montre comment Azure Machine Learning exécute les expériences, en particulier localement, sur une machine virtuelle distante et sur un cluster HDInsight. Cette section est une présentation pas à pas de la procédure menant de la création d’une cible de calcul à l’exécution de vos expériences.

>[!NOTE]
>Dans la suite de cet article, nous utilisons les commandes de l’interface de ligne de commande pour présenter les concepts et fonctionnalités. Les fonctionnalités décrites ici peuvent également être utilisées à partir de Workbench.

## <a name="launching-the-cli"></a>Lancement de l’interface de ligne de commande
Une manière simple de lancer l’interface de ligne de commande consiste à ouvrir un projet dans Workbench, puis à accéder à **Fichier --> Ouvrir l’invite de commandes**.

![](media/experimentation-service-configuration/opening-cli.png)

Cette commande a pour effet d’ouvrir une fenêtre de terminal dans laquelle vous pouvez entrer des commandes pour exécuter des scripts dans le dossier du projet actuel. Cette fenêtre de terminal est configurée avec l’environnement Python 3.5.2 installé par Workbench.

>[!NOTE]
> Lorsque vous exécutez une commande _az ml_ à partir de la fenêtre Commande, il se peut que vous deviez vous authentifier auprès d’Azure. L’interface de ligne de commande utilisant un cache d’authentification indépendant, la connexion à Workbench ne signifie pas que vous êtes authentifié dans l’environnement de votre interface de ligne de commande. Pour l’authentification, suivez la procédure ci-dessous. Le jeton d’authentification est mis en cache localement pendant un certain temps. Il suffit de répéter ces étapes lorsqu’il expire. Lorsque le jeton expire ou si vous rencontrez des erreurs d’authentification, exécutez les commandes suivantes :

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>Quand vous exécutez la commande _az ml_ à l’intérieur d’un dossier de projet, vérifiez que le projet appartient à un compte d’expérimentation Azure Machine Learning de l’abonnement Azure _actuel_. Autrement, vous pourriez rencontrer des erreurs d’exécution.


## <a name="running-scripts-and-experiments"></a>Exécution de scripts et d’expériences
Workbench vous permet d’exécuter vos scripts Python et PySpark sur différentes cibles de calcul à l’aide de la commande _az ml experiment submit_. Cette commande requiert la définition d’une configuration de série de tests. 

Workbench crée un fichier runconfig correspondant quand vous créez une cible de calcul, mais vous pouvez créer des configurations de série de tests supplémentaires à l’aide de la commande _az ml runconfiguration  create_. Vous pouvez aussi modifier manuellement les fichiers de configuration de série de tests.

Les configurations de série de tests apparaissent en tant que partie du processus d’exécution d’expérience dans Workbench. 

>[!NOTE]
>Plus en savoir plus sur le fichier de configuration de série de tests, voir la section [Référence de configuration d’exécution d’expérience](experimentation-service-configuration-reference.md).

## <a name="running-a-script-locally-on-workbench-installed-runtime"></a>Exécution d’un script localement sur un runtime installé par Workbench
Workbench permet d’exécuter des scripts directement sur le runtime Python 3.5.2 installé par Workbench. Ce runtime par défaut est installé au moment de la configuration de Workbench. Il inclut des bibliothèques et dépendances Azure Machine Learning. Les résultats et artefacts de la série de tests pour les exécutions locales sont toujours enregistrés dans le service d’historique des exécutions dans le cloud.

Contrairement aux exécutions sur Docker, cette configuration n’est _pas_ gérée par Conda. Vous devez configurer manuellement les dépendances de package pour votre environnement Python Workbench local.

Vous pouvez exécuter la commande suivante pour appliquer votre script localement dans l’environnement Python installé par Workbench. 

```
$az ml experiment submit -c local myscript.py
```

Vous pouvez trouver le chemin de l’environnement Python par défaut en tapant la commande suivante dans la fenêtre de l’interface de ligne de commande de Workbench.
```
$ conda env list
```

>[!NOTE]
>L’exécution locale de PySpark directement sur des environnements Spark locaux n’est **pas** prise en charge actuellement. Workbench ne prend pas en charge les scripts PySpark exécutés sur un Docker local. Une image Docker de base Azure Machine Learning est intégrée dans la version préinstallée de Spark 2.1.11. 

_**Présentation de l’exécution locale d’un script Python :**_
![](media/experimentation-service-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>Exécution d’un script sur un Docker local
Vous pouvez également exécuter vos projets sur un conteneur Docker sur votre ordinateur local par le biais du service d’expérimentation. Workbench fournit une image Docker de base qui intègre des bibliothèques Azure Machine Learning, ainsi qu’un runtime Spark 2.1.11 pour faciliter les exécutions locales de Spark. Docker doit déjà être en cours d’exécution sur l’ordinateur local.

Pour exécuter votre script Python ou PySpark sur un Docker local, vous pouvez exécuter les commandes suivantes dans l’interface de ligne de commande.

```
$az ml experiment submit -c docker myscript.py
```
or
```
az ml experiment submit --run-configuration docker myscript.py
```

L’environnement d’exécution sur un Docker local est préparé à l’aide de l’image Docker de base Azure Machine Learning. Workbench télécharge cette image lors de la première exécution, puis la superpose aux packages spécifiés dans votre fichier conda_dependencies.yml. Cette opération ralentit la série de tests initiale, mais accélère sensiblement les séries suivantes du fait qu’Azure Machine Learning Workbench réutilise les couches mises en cache. 

>[!IMPORTANT]
>Vous devez commencer par exécuter la commande _az ml experiment prepare -c docker_ afin de préparer l’image Docker pour votre première série de tests. Vous pouvez également définir le paramètre **PrepareEnvironment** sur true dans votre fichier docker.runconfig. Cette action a pour effet de préparer automatiquement votre environnement à l’exécution de votre série de tests.  

>[!NOTE]
>Si vous exécutez un script PySpark sur Spark, le fichier spark_dependencies.yml est utilisé en plus du fichier conda_dependencies.yml.

L’exécution de vos scripts sur une image Docker offre les avantages suivants :

1. elle garantit que votre script peut être exécuté de manière fiable dans d’autres environnements d’exécution. L’exécution sur un conteneur Docker vous aide à détecter et à éviter toute référence locale susceptible d’avoir un impact sur la portabilité. 

2. Elle vous permet de tester rapidement le code sur des runtimes et infrastructures dont l’installation et la configuration sont complexes, comme Apache Spark, sans avoir à les installer vous-même.


_**Présentation de l’exécution d’un Docker local pour un script Python :**_
![](media/experimentation-service-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>Exécution d’un script sur un Docker distant
Parfois, les ressources disponibles sur votre ordinateur local sont insuffisantes pour former le modèle souhaité. Dans ce cas, le service d’expérimentation offre un moyen simple d’exécuter vos scripts Python ou PySpark sur des machines virtuelles plus puissantes en utilisant un Docker distant. 

Une machine virtuelle distante doit remplir les conditions suivantes :
* Elle doit exécuter la distribution Linux Ubuntu et être accessible via SSH. 
* Elle doit exécuter Docker.

>[!IMPORTANT]
> Les machines virtuelles Windows exécutant Docker ne sont **pas** prises en charge en tant que cibles de calcul distantes.


Vous pouvez utiliser la commande suivante afin de créer la définition de cible de calcul et la configuration de série de tests pour des exécutions basées sur un Docker distant.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```

Une fois la cible de calcul configurée, vous pouvez utiliser la commande suivante pour exécuter votre script.
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>N’oubliez pas que l’environnement d’exécution est configuré à l’aide des spécifications du fichier conda_dependencies.yml. Le fichier spark_dependencies.yml est également utilisé si l’infrastructure PySpark est spécifiée dans le fichier .runconfig. 

Le processus de construction du Docker pour les machines virtuelles distantes étant exactement le même que pour les séries de tests du Docker local, vous devez vous attendre à une expérience d’exécution similaire.

>[!TIP]
>Si vous préférez éviter la latence résultant de la création de l’image Docker pour votre première série de tests, vous pouvez utiliser la commande suivante pour préparer la cible de calcul avant l’exécution de votre script. az ml experiment prepare -c remotedocker

_**Présentation de l’exécution de machine virtuelle distante pour un script Python :**_
![](media/experimentation-service-configuration/remote-vm-run.png)

## <a name="running-a-script-on-a-remote-vm-targeting-user-managed-environments"></a>Exécution d’un script sur une machine virtuelle distante ciblant les environnements gérés par l’utilisateur
Le service d’expérimentation prend également en charge l’exécution d’un script dans l’environnement Python de l’utilisateur au sein d’une machine virtuelle Ubuntu distante. Cela vous permet de gérer votre propre environnement d’exécution et de toujours utiliser les fonctionnalités d’Azure Machine Learning. 

Suivez les étapes ci-après pour exécuter votre script dans votre propre environnement.
* Préparez votre environnement Python sur une machine virtuelle Ubuntu distante ou une machine virtuelle de science des données installant vos dépendances.
* Installez la configuration requise d’Azure Machine Learning à l’aide de la commande suivante.

```
pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```

>[!TIP]
>Dans certains cas, vous devrez peut-être exécuter cette commande en mode sudo en fonction de vos privilèges. 
```
sudo pip install -I --index-url https://azuremldownloads.azureedge.net/python-repository/preview --extra-index-url https://pypi.python.org/simple azureml-requirements
```
 
* Utilisez la commande suivante afin de créer la définition de cible de calcul et la configuration de série de tests pour des exécutions gérées par l’utilisateur sur une machine virtuelle distante.
```
az ml computetarget attach remote --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" 
```
>[!NOTE]
>Cela définira le paramètre « userManagedEnvironment » de votre fichier config .compute sur true.

* Définissez l’emplacement de votre fichier exécutable de runtime Python dans votre fichier .compute. Vous devez faire référence au chemin d’accès complet de votre fichier exécutable Python. 
```
pythonLocation: python3
```

Une fois la cible de calcul configurée, vous pouvez utiliser la commande suivante pour exécuter votre script.
```
$ az ml experiment submit -c remotevm myscript.py
```

>[!NOTE]
> Lorsque vous exécutez des opérations sur une machine virtuelle de science des données, vous devez utiliser les commandes suivantes

Si vous souhaitez exécuter des opérations directement sur l’environnement Python global de la machine virtuelle de science des données, exécutez cette commande.
```
sudo /anaconda/envs/py35/bin/pip install <package>
```


## <a name="running-a-script-on-an-hdinsight-cluster"></a>Exécution d’un script sur un cluster HDInsight
HDInsight est une plateforme populaire pour l’analytique du Big Data qui prend en charge Apache Spark. Workbench permet d’effectuer une expérimentation sur le Big Data à l’aide de clusters HDInsight Spark. 

>[!NOTE]
>Le cluster HDInsight doit utiliser Stockage Blob Azure comme stockage principal. L’utilisation du stockage Azure Data Lake n’est pas encore prise en charge.

Pour créer une cible de calcul et une configuration de série de tests pour un cluster Azure HDInsight Spark, vous pouvez utiliser la commande suivante :

```
$ az ml computetarget attach cluster --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword"  
```

>[!NOTE]
>Si vous utilisez un nom de domaine complet (FQDN) au lieu d’une adresse IP, et que votre cluster Azure HDInsight Spark est nommé _foo_, le point de terminaison SSH se trouve sur le nœud de pilote nommé _foo-ssh.azurehdinsight.net_. N’oubliez pas le suffixe **-ssh** dans le nom du serveur lors de l’utilisation du nom de domaine complet (FQDN) pour le paramètre _--address_.


Une fois que vous avez le contexte de calcul, la commande suivante vous permet d’exécuter votre script PySpark.

```
$ az ml experiment submit -c myhdi myscript.py
```

Workbench prépare et gère l’environnement d’exécution sur le cluster HDInsight à l’aide de Conda. La configuration est gérée par les fichiers _conda_dependencies.yml_ et _spark_dependencies.yml_. 

Pour pouvoir exécuter des expériences dans ce mode, vous avez besoin d’un accès SSH au cluster HDInsight. 

>[!NOTE]
>Une configuration prise en charge est celle de clusters Azure HDInsight Spark exécutant Linux (distribution Linux Ubuntu avec Python/PySpark 3.5.2 et Spark 2.1.11).

_**Présentation de l’exécution d’un script PySpark basée sur HDInsight**_
![](media/experimentation-service-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>Exécution d’un script sur GPU
Pour exécuter vos scripts sur des GPU, vous pouvez suivre les instructions du [Guide pratique pour utiliser des GPU dans Azure Machine Learning](how-to-use-gpu.md).

## <a name="using-ssh-key-based-authentication-for-creating-and-using-compute-targets"></a>Utilisation de l’authentification par clé SSH pour créer et utiliser des cibles de calcul
Azure Machine Learning Workbench vous permet de créer et d’utiliser des cibles de calcul à l’aide de l’authentification par clé SSH en plus du schéma par nom d’utilisateur/mot de passe. Vous pouvez utiliser cette fonctionnalité lorsque vous utilisez remotedocker ou d’un cluster comme cible de calcul. Lorsque vous utilisez ce schéma, Workbench crée une paire de clés publique/privée et rapporte la clé publique. Vous ajoutez la clé publique aux fichiers ~/.ssh/authorized_keys de votre nom d’utilisateur. Azure Machine Learning Workbench utilise ensuite l’authentification par clé SSH pour accéder à et exécuter sur cette cible de calcul. Étant donné que la clé privée de la cible de calcul est enregistrée dans le magasin de clés pour l’espace de travail, d’autres utilisateurs de l’espace de travail peuvent utiliser la cible de calcul de la même façon en fournissant le nom d’utilisateur indiqué pour la création de la cible de calcul.  

Vous suivez ces étapes pour utiliser cette fonctionnalité. 

- Créez une cible de calcul en utilisant l’une des commandes suivantes.

```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
```
or
```
az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" -k
```
- Ajoutez la clé publique générée par le Workbench aux fichiers ~/.ssh/authorized_keys sur la cible de calcul associée. 

>[!IMPORTANT]
>Vous devez vous connecter à la cible de calcul à l’aide du même nom d’utilisateur que celui utilisé pour créer la cible de calcul. 

- Vous pouvez maintenant préparer et utiliser la cible de calcul à l’aide de l’authentification par clé SSH.

```
az ml experiment prepare -c remotevm
```

## <a name="next-steps"></a>étapes suivantes
* [Créer et installer Azure Machine Learning](quickstart-installation.md)
* [Gestion des modèles](model-management-overview.md)
