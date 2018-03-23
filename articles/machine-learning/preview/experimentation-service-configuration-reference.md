---
title: Fichiers de configuration du service d’expérimentation Azure Machine Learning
description: Ce document décrit en détail les paramètres de configuration du service d’expérimentation Azure ML.
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/28/2017
ms.openlocfilehash: 75b55b45c355f585fd73bdc1d97bc6adbbc4e9a0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-machine-learning-experimentation-service-configuration-files"></a>Fichiers de configuration du service d’expérimentation Azure Machine Learning

Quand vous exécutez un script dans Azure Machine Learning (Azure ML) Workbench, le comportement de l’exécution est contrôlé par les fichiers présents dans le dossier **aml_config**. Ce dossier figure à la racine du dossier de votre projet. Il est important de comprendre le contenu de ces fichiers pour pouvoir obtenir le résultat souhaité d’une exécution optimale.

Les fichiers appropriés dans ce dossier sont les suivants :
- conda_dependencies.yml
- spark_dependencies.yml
- Fichiers de cible de calcul
    - \<nom de cible de calcul>.compute
- Fichiers de configuration de série de tests
    - \<nom de configuration de série de tests>.runconfig

>[!NOTE]
>En général, vous disposez d’un fichier de cible de calcul et d’un fichier de configuration de série de tests pour chaque cible de calcul que vous créez. Vous pouvez cependant créer ces fichiers séparément et avoir plusieurs fichiers de configuration de série de tests pointant vers la même cible de calcul.

## <a name="condadependenciesyml"></a>conda_dependencies.yml
Ce fichier est un [fichier d’environnement conda](https://conda.io/docs/using/envs.html#create-environment-file-by-hand) qui spécifie la version du runtime Python et les packages dont dépend votre code. Quand Azure ML Workbench exécute un script dans un conteneur Docker ou un cluster HDInsight, il crée un [environnement conda](https://conda.io/docs/using/envs.html) pour l’exécution de votre script. 

Dans ce fichier, vous spécifiez les packages Python nécessaires pour l’exécution de votre script. Le service d’expérimentation Azure ML crée l’environnement conda en fonction de votre liste de dépendances. La liste de packages présentée ici doit être accessible au moteur d’exécution via des canaux comme :

* [continuum.io](https://anaconda.org/conda-forge/repo)
* [PyPI](https://pypi.python.org/pypi)
* Point de terminaison accessible publiquement (URL)
* Chemin d’accès de fichier local
* Autres canaux accessibles au moteur d’exécution

>[!NOTE]
>Lors de l’exécution sur un cluster HDInsight, Azure ML Workbench crée un environnement conda pour votre série de tests. Ainsi, différents utilisateurs peuvent exécuter des tests dans des environnements Python différents sur le même cluster.  

Voici un exemple de fichier **conda_dependencies.yml** classique.
```yaml
name: project_environment
dependencies:
  # Python version
  - python=3.5.2
  
  # some conda packages
  - scikit-learn
  - cryptography
  
  # use pip to install some more packages
  - pip:
     # a package in PyPi
     - azure-storage
     
     # a package hosted in a public URL endpoint
     - https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl
     
     # a wheel file available locally on disk (this only works if you are executing against local target)
     - C:\temp\my_private_python_pkg.whl
```

Azure ML Workbench utilise le même environnement conda sans régénération tant que le fichier **conda_dependencies.yml** reste identique. Il régénèrera votre environnement si vos dépendances sont modifiées.

>[!NOTE]
>Si vous ciblez l’exécution dans le contexte de calcul _local_, le fichier **conda_dependencies.yml** n’est **pas** utilisé. Les dépendances de package pour l’environnement Python de votre Azure Machine Learning Workbench local doivent être installées manuellement.

## <a name="sparkdependenciesyml"></a>spark_dependencies.yml
Ce fichier spécifie le nom de l’application Spark lorsque vous soumettez un script PySpark et des packages Spark à installer. Vous pouvez également spécifier un référentiel Maven public, ainsi qu’un package Spark figurant dans ce référentiel.

Voici un exemple : 

```yaml
configuration:
  # Spark application name
  "spark.app.name": "ClassifyingIris"
  
repositories:
  # Maven repository hosted in Azure CDN
  - "https://mmlspark.azureedge.net/maven"
  
  # Maven repository hosted in spark-packages.org
  - "https://spark-packages.org/packages"
  
packages:
  # MMLSpark package hosted in the Azure CDN Maven
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.5"
    
  # spark-sklearn packaged hosted in the spark-packages.org Maven
  - group: "databricks"
    artifact: "spark-sklearn"
    version: "0.2.0"
```

>[!NOTE]
>Les paramètres de réglage de cluster tels que la taille de Worker et les cœurs doivent figurer dans la section « configuration » du fichier spark_dependecies.yml 

>[!NOTE]
>Si vous exécutez le script dans un environnement Python, le fichier *spark_dependencies.yml* est ignoré. Il est utilisé uniquement si vous exécutez sur Spark (sur un cluster Docker ou HDInsight).

## <a name="run-configuration"></a>Configuration de série de tests
Pour spécifier une configuration de série de tests spécifique, vous avez besoin d’un fichier .compute et un fichier .runconfig. Ceux-ci sont généralement générés à l’aide d’une commande d’interface de ligne de commande. Vous pouvez également cloner des fichiers existants, les renommer, puis les modifier.

```azurecli
# create a compute target pointing to a VM via SSH
$ az ml computetarget attach remotedocker -n <compute target name> -a <IP address or FQDN of VM> -u <username> -w <password>

# create a compute context pointing to an HDI cluster head-node via SSH
$ az ml computetarget attach cluster -n <compute target name> -a <IP address or FQDN of HDI cluster> -u <username> -w <password> 
```

Cette commande crée une paire de fichiers en fonction de la cible de calcul spécifiée. Supposons que vous ayez nommé votre cible de calcul _foo_. Cette commande génère les fichiers _foo.compute_ et _foo.runconfig_ dans votre dossier **aml_config**.

>[!NOTE]
> Les noms _local_ ou _docker_ des fichiers de configuration de série de tests sont arbitraires. Azure Machine Learning Workbench ajoute ces deux configurations de série de tests lorsque vous créez un projet vide par commodité. Vous pouvez renommer les fichiers « <run configuration name>.runconfig » accompagnant le modèle de projet, ou créer de nouveaux sous le nom de votre choix.

### <a name="compute-target-namecompute"></a>\<nom de cible de calcul>.compute
Un fichier _\<compute target name>.compute_ spécifie les informations de connexion et de configuration de la cible de calcul. Il s’agit d’une liste de paires nom-valeur. Voici les paramètres pris en charge :

**type** : type de l’environnement de calcul. Les valeurs prises en charge sont les suivantes :
  - local
  - remote
  - docker
  - remotedocker
  - cluster

**baseDockerImage** : image Docker utilisée pour exécuter le script Python/PySpark. La valeur par défaut est _microsoft/mmlspark:plus-0.7.91_. Nous prenons également en charge une autre image, _microsoft/mmlspark:plus-gpu-0.7.91_, qui donne un accès GPU à l’ordinateur hôte (si GPU est présent).

**address** : adresse IP ou nom de domaine complet (FQDN) de la machine virtuelle ou nœud principal de cluster HDInsight.

**username** : nom d’utilisateur SSH pour accéder à la machine virtuelle ou au nœud principal HDInsight.

**password**: mot de passe chiffré pour la connexion SSH.

**sharedVolumes** : indicateur pour signaler que le moteur d’exécution doit utiliser une fonctionnalité de volume partagé Docker pour échanger les fichiers de projet. L’activation de cet indicateur peut accélérer l’exécution, car Docker peut accéder directement à des projets sans avoir besoin de les copier. Il est préférable de définir _false_ si le moteur Docker s’exécute sur Windows, car le partage de volume pour Docker sur Windows peut être inconsistant. Définissez-le sur _true_ s’il s’exécute sur macOS ou Linux.

**nvidiaDocker** : quand cet indicateur a la valeur _true_, le service d’expérimentation Azure ML utilise la commande _nvidia-docker_ au lieu de la commande _docker_ pour lancer l’image Docker. Le moteur _nvidia-docker_ permet au conteneur Docker d’accéder au matériel de GPU. Le paramètre est nécessaire si vous souhaitez exécuter le GPU dans le conteneur Docker. Seul l’hôte Linux prend en charge _nvidia-docker_. Par exemple, une machine virtuelle de science des données basée sur Linux dans Azure est fournie avec _nvidia-docker_. Désormais _nvidia-docker_ à n’est plus pris en charge sur Windows.

**nativeSharedDirectory** : cette propriété spécifie le répertoire de base (par exemple, _~/.azureml/share/_) dans lequel les fichiers peuvent être enregistrés pour être partagés entre les séries de tests sur la même cible de calcul. Si ce paramètre est utilisé lors de l’exécution sur un conteneur Docker, _sharedVolumes_ doit être défini sur true. Autrement, l’exécution échoue.

**userManagedEnvironment**: cette propriété spécifie si cette cible de calcul est gérée directement par l’utilisateur ou via le service d’expérimentation.  

### <a name="run-configuration-namerunconfig"></a>\<nom de configuration de série de tests>.runconfig
_\<nom de configuration de série de tests>.runconfig_ spécifie le comportement d’exécution de l’expérience Azure ML. Vous pouvez configurer un comportement d’exécution, comme le suivi de l’historique des exécutions ou la cible de calcul à utiliser, entre autres. Les noms des fichiers de configuration de série de tests sont utilisés pour remplir la liste déroulante des contextes d’exécution dans l’application de bureau Azure Machine Learning Workbench.

**ArgumentVector** : cette section spécifie le script à exécuter dans le cadre de cette exécution, ainsi que les paramètres du script. Par exemple, si vous avez dans votre fichier « <run configuration name>.runconfig » l’extrait de code suivant 

```
 "ArgumentVector":[
  - "myscript.py"
  - 234
  - "-v" 
 ] 
```
_« az ml experiment submit foo.runconfig »_ exécute automatiquement la commande avec le fichier _myscript.py_ passant 234 comme paramètre, et définit l’indicateur --verbose.

**Target** : ce paramètre est le nom du fichier _.compute_ auquel le fichier _/runconfig_ fait référence. En règle générale, il pointe sur le fichier _foo.compute_, mais vous pouvez le modifier pour qu’il pointe sur une autre cible de calcul.

**Environment Variables** : cette section permet aux utilisateurs de définir des variables d’environnement dans le cadre de séries de tests. L’utilisateur peut spécifier des variables d’environnement à l’aide de paires nom-valeur au format suivant :
```
EnvironmentVariables:
  "EXAMPLE_ENV_VAR1": "Example Value1"
  "EXAMPLE_ENV_VAR2": "Example Value2"
```

Ces variables d’environnement sont accessibles dans le code de l’utilisateur. Par exemple, ce code Python imprime la variable d’environnement nommée « EXAMPLE_ENV_VAR »
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

**Framework** : cette propriété spécifie si Azure Machine Learning Workbench doit lancer une session Spark pour exécuter le script. La valeur par défaut est _PySpark_. Définissez-là sur _Python_ si vous n’exécutez pas de code PySpark, ce qui peut aider à lancer la tâche plus rapidement avec moins de surcharge.

**CondaDependenciesFile** : cette propriété pointe sur le fichier qui spécifie les dépendances d’environnement conda dans le dossier *aml_config*. Si la valeur est _null_, la propriété pointe sur le fichier par défaut **conda_dependencies.yml**.

**SparkDependenciesFile** : cette propriété pointe sur le fichier qui spécifie les dépendances Spark dans le dossier **aml_config**. Elle est définie sur _null_ par défaut et pointe sur le fichier par défaut **spark_dependencies.yml**.

**PrepareEnvironment** : cette propriété, quand sa valeur est _true_, indique au service d’expérimentation de préparer l’environnement conda en fonction des dépendances conda spécifiées dans le cadre de votre série de tests initiale. Cette propriété est effective uniquement lorsque l’exécution s’applique à un environnement Docker. Ce paramètre est sans effet si l’exécution s’applique à un environnement _local_. 

**TrackedRun** : cet indicateur signale au service d’expérimentation s’il faut suivre ou non la série de tests dans l’infrastructure de l’historique des exécutions Azure ML Workbench. La valeur par défaut est _true_. 

**UseSampling** :_UseSampling_spécifie si les jeux de données échantillons actifs pour les sources de données sont utilisés pour la série de tests. Si la valeur est _false_, les sources de données ingèrent et utilisent la lecture de données complète de la banque de données. Si la valeur est _true_, les échantillons actifs sont utilisés. Les utilisateurs peuvent utiliser la section **DataSourceSettings » pour spécifier les jeux de données échantillons à utiliser s’ils souhaitent remplacer l’échantillon actif. 

**DataSourceSettings** : cette section de configuration spécifie les paramètres de source de données. Dans cette section, l’utilisateur spécifie l’échantillon de données existant pour une source de données particulière à utiliser dans le cadre de la série de tests. 

Le paramètre de configuration suivant spécifie que l’exemple nommé « MySample » est utilisé pour la source de données nommée « MyDataSource »
```
DataSourceSettings:
    MyDataSource.dsource:
    Sampling:
    Sample: MySample
```

**DataSourceSubstitutions** : des substitutions de source de données peuvent être utilisées lorsque l’utilisateur veut passer d’une source de données à une autre sans modifier leur code. Par exemple, les utilisateurs peuvent passer d’un fichier local échantillonné au jeu de données d’origine, plus volumineux, stocké dans un objet blob Azure, en modifiant la référence de la source de données. Quand une substitution est utilisée, Azure Machine Learning Workbench exécute vos sources de données et packages de préparation des données en faisant référence à la source de données de remplacement.

L’exemple suivant remplace les références « mylocal.datasource » dans les sources de données et packages de préparation des données d’Azure Machine Learning par « myremote.dsource ». 
 
```
DataSourceSubstitutions:
    mylocal.dsource: myremote.dsource
```

Suite à cette substitution, l’exemple de code suivant lit la source de données « myremote.dsource » au lieu de « mylocal.dsource » sans que les utilisateurs modifient leur code.
```
df = datasource.load_datasource('mylocal.dsource')
```
## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la [configuration du service d’expérimentation](experimentation-service-configuration.md).
