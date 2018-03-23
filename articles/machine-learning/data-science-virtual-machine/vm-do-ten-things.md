---
title: Dix choses que vous pouvez effectuer sur la machine virtuelle Science des données dans Azure | Microsoft Docs
description: Effectuez diverses tâches de modélisation et d'exploration des données sur la machine virtuelle pour la science des données.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: gokuma;weig;bradsev
ms.openlocfilehash: 71745260f45c5a5c150fdd3eac4e9a8221b8bce4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="ten-things-you-can-do-on-the-data-science-virtual-machine"></a>Dix choses que vous pouvez effectuer sur la machine virtuelle pour la science des données

La machine virtuelle pour la science des données (DSVM, « Data Science Virtual Machine ») Microsoft est un environnement puissant de développement de la science des données qui vous permet d'effectuer diverses tâches de modélisation et d'exploration des données. L’environnement est déjà généré et fourni avec plusieurs outils d’analyse de données courants qui facilitent la prise en main rapide de votre analyse pour les déploiements sur site, dans le cloud ou hybrides. La DSVM fonctionne en lien avec de nombreux services Azure et peut lire et traiter les données déjà stockées sur Azure, dans Azure SQL Data Warehouse, Azure Data Lake, Stockage Azure et Azure Cosmos DB. Elle peut également utiliser d’autres outils d’analyse tels qu’Azure Machine Learning et Azure Data Factory.

Dans cet article vous apprendrez comment utiliser votre DSVM afin d'effectuer diverses tâches de science des données et d'interagir avec d'autres services Azure. Voici quelques-unes des tâches que vous pouvez effectuer sur la DSVM :

1. Explorer les données et développer des modèles localement sur la DSVM avec Microsoft ML Server et Python
2. Utiliser un notebook Jupyter pour faire des expériences avec vos données sur un navigateur à l'aide de Python 2, Python 3 et Microsoft R, une version d'entreprise de R conçue pour les performances
3. Déployer des modèles créés avec R et Python sur Azure Machine Learning afin que les applications clientes puissent accéder à vos modèles à l'aide d'une interface de services web simple
4. Administrer vos ressources Azure à l’aide du portail Azure ou de PowerShell
5. Augmenter votre espace de stockage et partager des jeux de données / du code à grande échelle avec toute votre équipe en créant un stockage Fichier Azure comme lecteur montable sur votre DSVM
6. Partager du code avec votre équipe à l’aide de GitHub et accéder à votre dépôt à l’aide des clients Git préinstallés : Git Bash, Git GUI.
7. Accéder aux différents services de données et d’analytique Azure tels que Stockage Blob Azure, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse et Database
8. Générer des rapports et des tableaux de bord à l'aide du Power BI Desktop préinstallé sur la DSVM et les déployer sur le cloud
9. Mettre à l'échelle dynamiquement votre DSVM pour répondre aux besoins de votre projet
10. Installer des outils supplémentaires sur votre machine virtuelle   

> [!NOTE]
> Des frais d’utilisation supplémentaires s’appliquent pour la plupart des services supplémentaires de stockage et d’analyse des données figurant dans cet article. Pour plus d’informations, consultez la page sur la [tarification Azure](https://azure.microsoft.com/pricing/).
> 
> 

**Composants requis**

* Vous avez besoin d’un abonnement Azure. Vous pouvez vous inscrire à un essai gratuit [ici](https://azure.microsoft.com/free/).
* Des instructions d’approvisionnement d’une machine virtuelle pour la science des données sur le Portail Azure sont disponibles dans [Création d’une machine virtuelle](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Explorer les données et développer des modèles à l'aide de Microsoft ML Server ou de Python
Vous pouvez utiliser des langages tels que R et Python pour effectuer des analyses de données directement sur la DSVM.

Pour R, vous pouvez utiliser un IDE comme RStudio, qui se trouve sur le menu Démarrer, sur le Bureau OU dans Outils R pour Visual Studio. Microsoft a fourni des bibliothèques supplémentaires en plus d'Open source/CRAN-R pour permettre de mettre à l'échelle les analyses et d'analyser des volumes de données plus importants que la taille de mémoire autorisée en effectuant une analyse parallèle par blocs. 

Pour Python, vous pouvez utiliser un IDE comme Visual Studio Community Edition, dans lequel l'extension Outils Python pour Visual Studio (PTVS) est préinstallée. Par défaut, seul Python 3.6, l’environnement racine de Conda, est configuré sur PTVS. Pour activer Anaconda Python 2.7, vous devez procéder comme suit :

* Créez des environnements personnalisés pour chaque version en accédant à **Outils** -> **Outils Python** -> **Environnements Python**, puis en cliquant sur « **+ Personnalisé** » dans Visual Studio 2015 Community Edition
* Donnez une description et définissez le préfixe du chemin d’accès de l’environnement comme *c:\anaconda\envs\python2* pour Anaconda Python 2.7
* Cliquez sur **Détection automatique**, puis sur **Appliquer** pour enregistrer l’environnement.

Voici à quoi ressemble la configuration de l'environnement personnalisé dans Visual Studio.

![Installation PTVS](./media/vm-do-ten-things/PTVSSetup.png)

Consultez la page [Documentation de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) pour plus d’informations sur la création d’environnements Python.

Vous êtes maintenant prêt à créer un nouveau projet Python. Accédez à **Fichier** -> **Nouveau** -> **Projet** -> **Python** et sélectionnez le type d’application Python que vous créez. Vous pouvez définir l’environnement Python pour le projet actuel sur la version souhaitée (Anaconda 2.7 ou 3.6) en cliquant avec le bouton droit sur l’**environnement Python**, puis en sélectionnant **Ajouter/supprimer des environnements Python** et l’environnement souhaité. Vous trouverez plus d’informations sur l’utilisation de PTVS sur la page de [documentation](https://github.com/Microsoft/PTVS/wiki) produit.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Utiliser un Notebook Jupyter pour explorer et modéliser vos données avec Python ou R
Le Notebook Jupyter est un environnement puissant qui fournit un « IDE » de modélisation et d'exploration de données sur navigateur. Vous pouvez utiliser Python 2, Python 3 ou R (Open Source et Microsoft R Server) dans le notebook Jupyter.

Pour lancer le notebook Jupyter, cliquez sur l’icône du menu Démarrer ou du Bureau intitulée **Jupyter Notebook**. À l’invite de commande DSVM, vous pouvez également exécuter la commande ```jupyter notebook``` à partir du répertoire où se trouvent vos notebooks, ou bien à partir de celui où vous souhaitez en créer d’autres.  

Une fois que vous avez démarré Jupyter, vous devriez voir un répertoire contenant quelques exemples de notebooks pré-intégrés dans la DSVM. Vous pouvez désormais :

* cliquer sur le notebook pour visualiser le code.
* Exécutez chaque cellule en appuyant sur **MAJ-ENTRÉE**.
* Exécutez le notebook entier en cliquant sur **Cellule** -> **Exécuter**
* Créez un notebook en cliquant sur l’icône Jupyter (coin supérieur gauche), puis sur le bouton **Nouveau** sur la droite et en sélectionnant la langue du notebook (également appelé noyau).   

> [!NOTE]
> Actuellement, Python 2.7, Python 3.6, R, Julia et les noyaux PySpark dans Jupyter sont actuellement pris en charge. Le noyau de R prend en charge la programmation dans R Open source ainsi que les performances de Microsoft R.   
> 
> 

Une fois dans le notebook, vous pouvez explorer vos données, générer le modèle et le tester avec les bibliothèques de votre choix.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. Générer des modèles avec R ou Python et les rendre opérationnels à l’aide d’Azure Machine Learning
Une fois que vous avez créé et validé votre modèle, l'étape suivante consiste généralement à le déployer en production. Cela permet à vos applications clientes d’appeler les prédictions de modèle en temps réel ou par lots. Azure Machine Learning fournit un mécanisme permettant de faire fonctionner un modèle généré avec R ou Python.

Lorsque vous rendez votre modèle opérationnel dans Azure Machine Learning, un service web est exposé. Il permet aux clients d'effectuer des appels REST qui transmettent des paramètres d'entrée et reçoivent des prédictions du modèle en tant que sorties.   

> [!NOTE]
> Si vous n’êtes pas encore inscrit à Azure Machine Learning, vous pouvez obtenir un espace de travail gratuit ou standard en vous rendant sur la page d’accueil [Azure Machine Learning Studio](https://studio.azureml.net/) et en cliquant sur « Démarrer ».   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Générer et rendre opérationnels des modèles Python
Voici un extrait de code développé dans un notebook Jupyter Python qui génère un modèle simple à l’aide de la bibliothèque SciKit-learn.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

La méthode utilisée pour déployer vos modèles Python sur Azure Machine Learning inclut la prédiction du modèle dans une fonction et lui ajoute des attributs fournis par la bibliothèque Python Azure Machine Learning préinstallée. Celle-ci dépend de l’ID, de la clé API et des paramètres d’entrée et de retour de votre espace de travail Azure Machine Learning.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Un client peut désormais effectuer des appels au service web. Il existe des wrappers tout préparés qui construisent les demandes d'API REST. Voici un exemple de code pour utiliser le service web.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Actuellement, la bibliothèque Azure Machine Learning est uniquement prise en charge par Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Générer et rendre opérationnels des modèles R
Vous pouvez déployer des modèles R générés sur la machine virtuelle pour la science des données ou ailleurs sur Azure Machine Learning de la même façon que pour Python. Voici la procédure à suivre :

* Créez un fichier settings.json pour fournir votre ID d’espace de travail et votre jeton d’authentification 
* Écrivez ensuite un wrapper pour la fonction de prédiction du modèle.
* Appelez ```publishWebService``` dans la bibliothèque Azure Machine Learning pour transmettre le wrapper de la fonction.  

Voici la procédure et les extraits de code qui peuvent être utilisés pour configurer, créer, publier et utiliser un modèle tel qu’un service web dans Azure Machine Learning.

#### <a name="setup"></a>Paramétrage

* Créez un fichier settings.json dans un répertoire appelé ```.azureml``` dans votre répertoire de base, puis entrez les paramètres de votre espace de travail Azure Machine Learning :

Structure du fichier settings.json :

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Générer un modèle dans R et le publier dans Azure Machine Learning
    library(AzureML)
    ws <- workspace(config="~/.azureml/settings.json")

    if(!require("lme4")) install.packages("lme4")
    library(lme4)
    set.seed(1)
    train <- sleepstudy[sample(nrow(sleepstudy), 120),]
    m <- lm(Reaction ~ Days + Subject, data = train)

    # Define a prediction function to publish based on the model:
    sleepyPredict <- function(newdata){
          predict(m, newdata=newdata)
    }

    ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Utiliser le modèle déployé dans Azure Machine Learning
Pour utiliser le modèle à partir d’une application cliente, nous utilisons la bibliothèque Azure Machine Learning pour rechercher le service web publié par nom à l’aide de l’appel d’API `services` afin de déterminer le point de terminaison. Il vous suffit ensuite d’appeler la fonction `consume` et de transmettre la trame de données à prédire.
Le code suivant permet d’utiliser le modèle publié comme service web Azure Machine Learning.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Vous trouverez plus d’informations sur la bibliothèque R Azure Machine Learning [ici](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Administrer vos ressources Azure à l’aide du Portail Azure ou de PowerShell
La DSVM vous permet non seulement de développer votre solution d'analyse localement sur la machine virtuelle, mais également d'accéder aux services sur le cloud Microsoft Azure. Azure fournit plusieurs services de calcul, de stockage, d'analyse de données et autres, que vous pouvez administrer et auxquels vous pouvez accéder à partir de votre DSVM.

Pour gérer vos ressources cloud et votre abonnement Azure, vous pouvez utiliser votre navigateur et pointer vers le [Portail Azure](https://portal.azure.com). Vous pouvez également utiliser Azure PowerShell pour administrer votre abonnement Azure et vos ressources à l’aide d’un script.
Vous pouvez exécuter Azure PowerShell à partir d'un raccourci sur le bureau ou dans le menu Démarrer intitulé « Microsoft Azure PowerShell ». Reportez-vous à la [documentation Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) pour plus d’informations sur l’administration de votre abonnement Azure et de vos ressources à l’aide de scripts Windows PowerShell.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. Augmenter votre espace de stockage avec un système de fichiers partagés
Les scientifiques des données peuvent partager des jeux de données volumineux, du code ou d’autres ressources au sein de leur équipe. La DSVM proprement dite a environ 45 Go d'espace disponible. Pour étendre votre espace de stockage, vous pouvez utiliser le service Azure File Service et l’installer sur une ou plusieurs instances DSVM ou y accéder via l’API REST.  Vous pouvez également utiliser le [Portail Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) ou [Azure Powershell](../../virtual-machines/windows/attach-disk-ps.md) pour ajouter des disques de données supplémentaires dédiés. 

> [!NOTE]
> L'espace maximal du partage Azure File Service est de 5 To et la limite de taille par fichier est de 1 To. 
> 
> 

Vous pouvez utiliser Azure PowerShell pour créer un partage Azure File Service. Voici le script à exécuter sous Azure PowerShell pour créer un partage Azure File Service.

    # Authenticate to Azure.
    Login-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


Maintenant que vous avez créé un partage de fichiers Azure, vous pouvez l’installer sur n’importe quelle machine virtuelle d’Azure. Il est vivement recommandé que la machine virtuelle soit dans le même centre de données Azure que le compte de stockage pour éviter des frais de transfert de données et la latence. Voici les commandes pour installer le lecteur sur la DSVM que vous pouvez exécuter sur Azure PowerShell.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Vous pouvez désormais accéder à ce lecteur comme à n’importe quel lecteur normal sur la machine virtuelle.

## <a name="6-share-code-with-your-team-using-github"></a>6. Partager du code avec votre équipe à l’aide de GitHub
GitHub est un référentiel de code dans lequel vous trouverez de nombreux exemples de code et de sources pour différents outils utilisant diverses technologies et partagés par la communauté des développeurs. Il utilise la technologie Git pour suivre et stocker les versions des fichiers de code. GitHub est également une plateforme qui vous permet de créer votre propre référentiel pour stocker le code et la documentation partagés de votre équipe, d’implémenter le contrôle de version et de contrôler les accès pour afficher le code et y contribuer. Visitez les [pages d’aide GitHub](https://help.github.com/) pour plus d’informations sur l’utilisation de Git. Vous pouvez utiliser GitHub pour collaborer avec votre équipe, utiliser le code développé par la communauté et apporter une contribution au code pour la communauté.

La DSVM est déjà livrée avec des outils clients en ligne de commande et avec une interface graphique utilisateur pour accéder au dépôt GitHub. L’outil de ligne de commande pour utiliser Git et GitHub est appelé Git Bash. La version de Visual Studio installée sur la DSVM comprend les extensions Git. Vous pouvez trouver les icônes de démarrage de ces outils dans le menu Démarrer et sur le bureau.

Pour télécharger du code à partir d’un référentiel GitHub, utilisez la commande ```git clone```. Par exemple, pour télécharger le référentiel de science des données publié par Microsoft dans le répertoire actif, vous pouvez exécuter la commande suivante une fois dans ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

Dans Visual Studio, vous pouvez effectuer la même opération de clonage. La capture d’écran suivante indique comment accéder aux outils Git et GitHub dans Visual Studio.

![Git dans Visual Studio](./media/vm-do-ten-things/VSGit.PNG)

Vous trouverez plus d’informations sur l’utilisation de Git pour travailler avec votre dépôt GitHub dans plusieurs ressources disponibles sur github.com. L’ [aide-mémoire](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) constitue une référence utile.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Accéder à divers services de données et d'analyse Azure
### <a name="azure-blob"></a>Objets blob Azure
Les objets blob Azure constituent un stockage fiable et économique dans le cloud pour tous les types de données. Cette section décrit comment procéder pour déplacer des données d’objets blob Azure et accéder à des données stockées dans un objet blob Azure.

**Configuration requise**

* **Créez votre compte de stockage d’objets blob Azure sur le [Portail Azure](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Vérifiez que l’outil de ligne de commande AzCopy préinstallé se trouve ici : ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Le répertoire contenant le fichier azcopy.exe se trouve déjà sur votre variable d’environnement PATH pour éviter d’avoir à saisir tout le chemin d’accès à la commande lors de l’exécution de cet outil. Pour plus d’informations sur l’outil AzCopy, consultez la [documentation AzCopy](../../storage/common/storage-use-azcopy.md)
* Lancez l’outil Azure Storage Explorer. Vous pouvez le télécharger sur le site [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Déplacer des données de la machine virtuelle vers Azure Blob : AzCopy**

Pour déplacer des données entre vos fichiers locaux et le stockage d’objets blob, vous pouvez utiliser AzCopy en ligne de commande ou PowerShell :

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Remplacez **C:\myfolder** par le chemin d’accès où se trouve votre fichier, **mystorageaccount** par le nom de votre compte de stockage blob, **mycontainer** par le nom du conteneur et **storage account key** par votre clé d’accès de stockage blob. Vous trouverez les informations d’identification de votre compte de stockage sur le [Portail Azure](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

Exécutez la commande AzCopy dans PowerShell ou à partir d’une invite de commandes. Voici un exemple d'utilisation de la commande AzCopy :

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Une fois que vous avez exécuté la commande AzCopy pour copier vers un objet blob Azure, votre fichier s'affiche dans l’Explorateur Stockage Azure.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Déplacer des données de la machine virtuelle vers Azure Blob : Azure Storage Explorer**

Vous pouvez également télécharger des données du fichier local vers votre machine virtuelle à l'aide d'Azure Storage Explorer :

* Pour charger des données dans un conteneur, sélectionnez le conteneur cible, puis cliquez sur le bouton **Charger**.![Charger dans l'Explorateur de stockage](./media/vm-do-ten-things/storage-accounts.png)
* Cliquez sur **...** à droite de la zone **Fichiers**, sélectionnez un ou plusieurs fichiers à charger à partir du système de fichiers, puis cliquez sur **Charger** pour lancer le chargement des fichiers.![Charger les fichiers dans le blob](./media/vm-do-ten-things/upload-files-to-blob.png)

**Lire des données à partir d'Azure Blob : module lecteur Machine Learning**

Dans Azure Machine Learning Studio, vous pouvez utiliser un **module Importer les données** pour lire des données à partir de votre objet blob.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Lire des données à partir d'Azure Blob : Python ODBC**

Vous pouvez utiliser la bibliothèque **BlobService** pour lire les données directement à partir de l’objet blob dans un Notebook Jupyter ou un programme Python.

Commencez par importer les packages requis :

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random

Puis entrez les informations d'identification de votre compte d'objets blob Azure et lisez les données de l'objet Blob :

    CONTAINERNAME = 'xxx'
    STORAGEACCOUNTNAME = 'xxxx'
    STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
    BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
    localfilename = 'trip_data_1.csv'
    LOCALDIRECTORY = os.getcwd()
    LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

    #download from blob
    t1 = time.time()
    blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
    t2 = time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

Les données sont lues comme une trame de données :

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage est un référentiel hyperscale pour les charges de travail d’analyse des Big Data, compatible avec le système de fichiers HDFS (Hadoop Distributed File System). Il fonctionne avec Hadoop et Azure Data Lake Analytics. Dans cette section, vous apprendrez comment déplacer des données dans Azure Data Lake Store et exécuter l’analyse à l’aide d’Azure Data Lake Analytics.

**Configuration requise**

* Créez votre Azure Data Lake Analytics dans le [Portail Azure](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Les **outils Azure Data Lake** dans **Visual Studio** disponibles sur ce [lien](https://www.microsoft.com/download/details.aspx?id=49504) sont déjà installés dans Visual Studio Community Edition sur la machine virtuelle. Après avoir lancé Visual Studio et vous être connecté à votre abonnement Azure, vous devriez voir votre compte et votre stockage Azure Data Analytics dans le volet gauche de Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Déplacer des données de la machine virtuelle vers Data Lake : Azure Data Lake Explorer**

Vous pouvez utiliser **Azure Data Lake Explorer** pour charger des données à partir des fichiers locaux de votre machine virtuelle vers Data Lake Storage.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Vous pouvez également créer un pipeline de données pour utiliser vos données vers ou à partir d’Azure Data Lake à l’aide [d’Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/). Consultez cet [article](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) pour vous guider à travers les étapes de création des pipelines de données.

**Lire des données à partir d'Azure Blob vers Data Lake : U-SQL**

Si vos données se trouvent dans Stockage Blob Azure, vous pouvez les lire directement à partir de l'objet blob de stockage Azure dans la requête SQL-U. Avant de composer votre requête SQL-U, assurez-vous que votre compte de stockage d'objets blob soit lié à votre Azure Data Lake. Accédez au **portail Azure**, recherchez votre tableau de bord Azure Data Lake Analytics, cliquez sur **Ajouter une source de données**, sélectionnez le type de stockage **Azure Storage** et entrez votre nom et votre clé de compte de stockage Azure. Vous pouvez ensuite référencer les données stockées dans le compte de stockage.

![Saisie d'un compte de stockage et d'une clé](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

Dans Visual Studio, vous pouvez lire les données à partir d’un stockage d’objets blob, effectuer certaines manipulations de données et extractions de paramètres et envoyer les données résultantes vers Azure Data Lake ou un stockage d’objets blob Azure. Lorsque vous référencez les données Stockage Blob, utilisez **wasb://**. Lorsque vous référencez les données d’Azure Data Lake, utilisez **swbhdfs://**.

![Trame de données](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Vous pouvez utiliser les requêtes U-SQL suivantes dans Visual Studio :

    @a =
        EXTRACT medallion string,
                hack_license string,
                vendor_id string,
                rate_code string,
                store_and_fwd_flag string,
                pickup_datetime string,
                dropoff_datetime string,
                passenger_count int,
                trip_time_in_secs double,
                trip_distance double,
                pickup_longitude string,
                pickup_latitude string,
                dropoff_longitude string,
                dropoff_latitude string

        FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
        USING Extractors.Csv();

    @b =
        SELECT vendor_id,
        COUNT(medallion) AS cnt_medallion,
        SUM(passenger_count) AS cnt_passenger,
        AVG(trip_distance) AS avg_trip_dist,
        MIN(trip_distance) AS min_trip_dist,
        MAX(trip_distance) AS max_trip_dist,
        AVG(trip_time_in_secs) AS avg_trip_time
        FROM @a
        GROUP BY vendor_id;

    OUTPUT @b   
    TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
    USING Outputters.Csv();

    OUTPUT @b   
    TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
    USING Outputters.Csv();



Une fois votre requête envoyée au serveur, un diagramme indiquant l'état de votre travail s'affiche.

![Diagramme d’état du travail](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Interroger des données dans Data Lake : U-SQL**

Une fois le jeu de données réceptionné dans Azure Data Lake, vous pouvez utiliser le [langage U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) pour interroger et explorer les données. Le langage U-SQL est similaire à T-SQL, mais combine certaines fonctionnalités de C# afin de permettre aux utilisateurs d'écrire des modules personnalisés, des fonctions définies par l'utilisateur, etc. Vous pouvez utiliser les scripts de l'étape précédente.

Une fois la requête envoyée au serveur, tripdata_summary.CSV est créé dans **Azure Data Lake Explorer**. Vous pouvez prévisualiser les données en cliquant avec le bouton droit sur le fichier.

![Fichier dans Azure Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Pour afficher les informations du fichier :

![Résumé du fichier](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clusters HDInsight Hadoop
Azure HDInsight est un service Apache Hadoop, Spark, HBase et Storm géré dans le cloud. Vous pouvez travailler facilement avec les clusters Azure HDInsight à partir de la machine virtuelle pour la science des données.

**Configuration requise**

* Créez votre compte de stockage d’objets blob Azure sur le [Portail Azure](https://portal.azure.com). Ce compte de stockage est utilisé pour stocker les données des clusters HDInsight.

![Créer un compte de stockage d’objets blob Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Personnalisez les clusters Hadoop Azure HDInsight sur le [Portail Azure](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Liez le compte de stockage créé à votre cluster HDInsight une fois sa création terminée. Ce compte de stockage est utilisé pour accéder aux données qui peuvent être traitées au sein du cluster.

![Liaison d'un compte de stockage créé avec un cluster HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Activez **Accès à distance** sur le nœud principal du cluster après sa création. Retenez les informations d’identification pour l’accès à distance que vous spécifiez ici, car vous en aurez besoin pour la procédure suivante.

![Activer l'accès à distance](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Créez un espace de travail Machine Learning. Vos expériences Machine Learning sont stockées dans cet espace de travail Machine Learning. Sélectionnez les options en surbrillance sur le portail, comme indiqué dans la capture d’écran suivante :

![Création d’un espace de travail Microsoft Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Entrez ensuite les paramètres pour votre espace de travail

![Entrer les paramètres de l'espace de travail Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Téléchargez des données à l’aide d’IPython Notebook. Importez d’abord les packages requis, saisissez vos informations d’identification, créez une base de données dans votre compte de stockage, puis chargez les données dans les clusters HDI.

        #Import required Packages
        import pyodbc
        import time as time
        import json
        import os
        import urllib
        import urllib2
        import warnings
        import re
        import pandas as pd
        import matplotlib.pyplot as plt
        from azure.storage.blob import BlobService
        warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
        queryString = "create database if not exists nyctaxidb;"
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.trip
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            rate_code string,
                            store_and_fwd_flag string,
                            pickup_datetime string,
                            dropoff_datetime string,
                            passenger_count int,
                            trip_time_in_secs double,
                            trip_distance double,
                            pickup_longitude double,
                            pickup_latitude double,
                            dropoff_longitude double,
                            dropoff_latitude double)  
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.fare
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            pickup_datetime string,
                            payment_type string,
                            fare_amount double,
                            surcharge double,
                            mta_tax double,
                            tip_amount double,
                            tolls_amount double,
                            total_amount double)
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


* Vous pouvez également suivre cette [procédure pas à pas](../team-data-science-process/hive-walkthrough.md) pour charger les données NYC Taxi vers le cluster HDI. Voici les grandes étapes :
  
  * AzCopy : téléchargez les CSV compressés de l'objet blob public vers votre dossier local
  * AzCopy : téléchargez les CSV décompressés de votre dossier local vers le cluster HDI
  * Connectez-vous au nœud principal du cluster Hadoop et préparez une analyse exploratoire des données

Une fois les données chargées dans le cluster HDI, vous pouvez les vérifier dans Azure Storage Explorer. Et vous disposez d'une base de données nyctaxidb créée dans le cluster HDI.

**Exploration des données : requêtes Hive dans Python**

Les données se trouvant dans le cluster Hadoop, vous pouvez utiliser le package pyodbc pour vous connecter aux clusters Hadoop et envoyer des requêtes à la base de données avec Hive pour l’exploration et l’extraction de paramètres. Vous pouvez afficher les tables existantes que nous avons créées à l'étape des conditions préalables.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Afficher les tables existantes](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Nous allons examiner le nombre d'enregistrements chaque mois et la fréquence des pourboires dans la table de trajet :

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Tracer le nombre d’enregistrements de chaque mois](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![Traçage des fréquences de pourboires](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Vous pouvez également calculer la distance entre les emplacements de départ et d'arrivée, puis les comparer à la distance du trajet.

    queryString = """
                    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                        *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                        pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                        from nyctaxidb.trip
                        where month=1
                            and pickup_longitude between -90 and -30
                            and pickup_latitude between 30 and 90
                            and dropoff_longitude between -90 and -30
                            and dropoff_latitude between 30 and 90;
                """
    results = pd.read_sql(queryString,connection)
    results.head(5)


![Table des départs et arrivées](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Traçage de la distance des départs/arrivées à la distance des trajets](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nous allons maintenant préparer le jeu de données sous-échantillonné (1 %) pour la modélisation. Vous pouvez utiliser ces données dans le module lecteur Machine Learning.

        queryString = """
        create  table if not exists nyctaxi_downsampled_dataset_testNEW (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\\n'
        stored as textfile;
        """
        cursor.execute(queryString)

        --- now insert contents of the join into the preceding internal table

        queryString = """
        insert overwrite table nyctaxi_downsampled_dataset_testNEW
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
        rand() as sample_key

        from trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01
        """
        cursor.execute(queryString)

Après un certain temps, vous pouvez voir que les données ont été chargées dans les clusters Hadoop :

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Table de données](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Lire des données à partir de HDI en utilisant le module lecteur Machine Learning**

Vous pouvez également utiliser le module **lecteur** Machine Learning Studio pour accéder à la base de données dans le cluster Hadoop. Entrez les informations d'identification de votre compte Azure Storage et de vos clusters HDI pour pouvoir générer des modèles d'apprentissage automatique à l'aide de la base de données des clusters HDI.

![Propriétés du module lecteur](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Le jeu de données évalué peut ensuite être affiché :

![Affichage du jeu de données évalué](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse et bases de données
Azure SQL Data Warehouse est un entrepôt de données élastique en tant que service offrant une expérience SQL Server professionnelle.

Vous pouvez approvisionner votre Azure SQL Data Warehouse en suivant les instructions fournies dans cet [article](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Une fois votre Azure SQL Data Warehouse approvisionné, vous pouvez utiliser cette [procédure pas à pas](../team-data-science-process/sqldw-walkthrough.md) pour le chargement de données, mais aussi pour l’exploration et la modélisation à l’aide de données dans SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB est une base de données NoSQL sur le cloud. Elle vous permet de travailler avec des documents comme JSON, de les stocker et de les interroger.

Voici les étapes préalables pour accéder à Azure Cosmos DB à partir de la DSVM :

1. Le Kit de développement logiciel Azure Cosmos DB Python est déjà installé sur la DSVM (exécuter ```pip install pydocumentdb --upgrade``` à partir de l’invite de commandes pour mettre à jour)
2. Créez un compte Azure Cosmos DB et une base de données à partir du [portail Azure](https://portal.azure.com)
3. Téléchargez [ici](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) « l’outil de migration Azure Cosmos DB » et extrayez-le dans le répertoire de votre choix
4. Importez les données JSON (données sur le volcan) stockées sur un [objet blob public](https://cahandson.blob.core.windows.net/samples/volcano.json) dans Cosmos DB avec les paramètres de commande suivants pour l’outil de migration (dtui.exe à partir du répertoire où vous avez installé l’outil de migration Cosmos DB). Entrez les paramètres d'emplacement source et cible suivant :
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1

Une fois les données importées, vous pouvez accéder à Jupyter et ouvrir le notebook intitulé *DocumentDBSample* qui contient le code Python pour accéder à Azure Cosmos DB et effectuer des requêtes de base. Pour en savoir plus sur Cosmos DB, consultez la [page de documentation](https://docs.microsoft.com/azure/cosmos-db/) du service.

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Générer des rapports et des tableaux de bord à l'aide de Power BI Desktop
Vous pouvez visualiser le fichier Volcan JSON de l’exemple Cosmos DB précédent dans Power BI afin d’obtenir un aperçu visuel des données. Les étapes détaillées sont présentées dans [l’article Power BI](../../cosmos-db/powerbi-visualize.md). Les étapes principales sont les suivantes :

1. Ouvrez Power BI Desktop et cliquez sur « Obtenir les données ». Spécifiez l’URL comme : https://cahandson.blob.core.windows.net/samples/volcano.json
2. Vous devez voir les enregistrements JSON importés sous forme de liste
3. Convertissez la liste en table afin que Power BI puisse l’utiliser
4. Développez les colonnes en cliquant sur l’icône Développer (celle avec l’icône « flèche gauche et flèche droite » à droite de la colonne)
5. Notez que cet emplacement est un champ « Enregistrement ». Développez l'enregistrement et sélectionnez uniquement les coordonnées. Une coordonnée est une colonne de liste
6. Ajoutez une nouvelle colonne pour convertir la colonne coordonnée de liste en une colonne LatLong séparée par des virgules et concaténant les deux éléments dans le champ de liste de coordonnées à l’aide de la formule ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Enfin, convertissez la colonne ```Elevation``` en Decimal et sélectionnez **Fermer** et **Appliquer**.

À la place de la procédure précédente, vous pouvez coller le code suivant qui exécute ces étapes dans l'éditeur avancé de Power BI, qui vous permet d'écrire les transformations de données dans un langage de requête.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Vous disposez maintenant des données dans votre modèle de données Power BI. Votre Power BI Desktop doit apparaître comme suit :

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Vous pouvez commencer à créer des rapports et des visualisations à l'aide du modèle de données. Vous pouvez suivre la procédure décrite dans cet [article Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) pour générer un rapport. Le résultat sera un rapport similaire à ce qui suit.

![Power BI Desktop - Vue Rapport - Connecteur Power BI](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. Mettre à l'échelle dynamiquement votre DSVM pour répondre aux besoins de votre projet
Vous pouvez mettre à l'échelle la DSVM pour répondre aux besoins de votre projet. Si vous n’avez pas besoin d’utiliser la machine virtuelle le soir ou le week-end, vous pouvez simplement arrêter la machine virtuelle à partir du [Portail Azure](https://portal.azure.com).

> [!NOTE]
> Vous encourez des frais de calcul si vous utilisez simplement le bouton d’arrêt du système d’exploitation sur la machine virtuelle.  
> 
> 

Si vous devez gérer une analyse à grande échelle et avez besoin de davantage de capacité de processeur, de mémoire et/ou de disque, vous trouverez un grand choix de tailles de machine virtuelle en termes de cœurs de processeur, d’instances basées sur GPU pour un apprentissage approfondi, de capacité de mémoire et de types de disques (y compris les disques SSD) qui répondent à vos besoins budgétaires et de calcul. La liste complète des machines virtuelles, ainsi que leur tarif horaire de calcul, sont disponibles sur la page [Tarification des machines virtuelles Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) .

De même, si vos besoins en matière de capacité de traitement de la machine virtuelle diminuent (par exemple : vous avez déplacé une charge de travail importante vers un cluster Hadoop ou Spark), vous pouvez descendre en puissance le cluster à partir du [Portail Azure](https://portal.azure.com) en accédant aux paramètres de votre instance de machine virtuelle. Voici une capture d'écran.

![Paramètres de l'instance de machine virtuelle](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. Installer des outils supplémentaires sur votre machine virtuelle
La DVSM inclut plusieurs outils prédéfinis qui peuvent traiter une bonne partie de l’analyse des données courantes. Vous pouvez ainsi gagner du temps, puisque vous n’avez pas à installer et à configurer vos environnements un par un, et économiser de l’argent, car vous payez uniquement pour les ressources que vous utilisez.

Vous pouvez utiliser d’autres services de données et d’analyse Azure présentés dans cet article pour améliorer votre environnement d’analyse. Dans certains cas, vous pouvez avoir besoin d’outils supplémentaires, notamment d’outils tiers propriétaires. Vous avez un accès administratif total à la machine virtuelle pour installer les nouveaux outils dont vous avez besoin. Vous pouvez également installer des packages supplémentaires non préinstallés en Python et R. Pour Python, vous pouvez utiliser soit ```conda```, soit ```pip```. Pour R, vous pouvez utiliser ```install.packages()``` dans la console R ou utiliser l’IDE et choisir « **Packages** -> **Installer les packages...** ».

## <a name="summary"></a>Résumé
Ce sont quelques-unes des actions possibles sur la machine virtuelle pour la science des données Microsoft. Il existe bien d'autres actions que vous pouvez effectuer pour en faire un environnement d'analyse efficace.

