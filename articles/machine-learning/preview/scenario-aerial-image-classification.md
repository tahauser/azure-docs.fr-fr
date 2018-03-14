---
title: "Classification d’images aériennes | Microsoft Docs"
description: "Fournit des instructions pour la classification d’images aériennes dans un scénario réel"
author: mawah
ms.author: mawah
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.workload: data-services
ms.date: 12/13/2017
ms.openlocfilehash: 76c706496b3bcdbc1604661be85dc31000873ad3
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="aerial-image-classification"></a>Classification d’images aériennes

Cet exemple montre comment utiliser Azure Machine Learning Workbench pour coordonner l’apprentissage distribué et l’opérationnalisation des modèles de classification des images. Nous suivons deux approches pour effectuer l’apprentissage : (i) en affinant un réseau neuronal profond à l’aide d’un cluster GPU [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) et (ii) à l’aide du package [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) pour caractériser les images selon des modèles CNTK et pour former des classifieurs à partir de caractéristiques dérivées. Nous appliquons ensuite les modèles formés de manière parallèle à de vastes ensembles d’images dans le cloud à l’aide un cluster [Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/), ce qui nous permet de mettre à l’échelle la vitesse de formation et d’opérationnalisation en ajoutant ou supprimant des nœuds de travail.

Cet exemple met en avant deux approches visant à transférer l’apprentissage, qui tire parti des modèles préformés pour éviter les coûts de formation des réseaux neuronaux profonds à partir de zéro. Reformer un réseau neuronal profond nécessite généralement des ressources de calcul GPU, mais permet d’obtenir une plus grande précision lorsque l’ensemble de formation est suffisamment grand. L’opération de formation d’un simple classifieur sur des images caractérisées ne nécessite pas de ressources de calcul GPU, est par nature rapide et évolutive de façon arbitraire et s’adapte à moins de paramètres. Cette méthode est par conséquent un excellent choix si vous avez peu d’exemples d’apprentissage, comme cela est fréquent dans les cas d’usage personnalisés. 

Le cluster Spark utilisé dans cet exemple a 40 nœuds de travail et son coût de fonctionnement s’élève à environ 40 $/h ; les ressources de cluster Batch AI incluent huit GPU, pour un coût de fonctionnement environ égal à 10 $/h. Pour effectuer cette procédure pas à pas, comptez environ trois heures. Quand vous l’aurez terminée, suivez les instructions de nettoyage pour supprimer les ressources créées et arrêter la facturation.

## <a name="link-to-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie

Le dépôt GitHub public correspondant à ce scénario réel contient tous les supports, y compris les exemples de code, nécessaires pour cet exemple :

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Description du cas d’usage

Dans ce scénario, nous formons des modèles pour classer les types de sol montrés dans des images aériennes prises sur un tracé de 224 mètres sur 224. Les modèles de classification de l’occupation des sols sont utiles pour suivre l’évolution de l’urbanisation, de la déforestation, de la disparition des zones humides et d’autres tendances environnementales majeures à partir des images aériennes régulièrement collectées. Nous avons préparé des ensembles d’images d’apprentissage et de validation sur la base d’images issues du programme américain NAIP (National Agriculture Imagery Program) et d’étiquettes d’occupation des sols publiées dans la base de données NLCD (National Land Cover Database) des États-Unis. Des exemples d’images de chaque classe d’occupation des sols sont présentés ici :

![Exemples de régions pour chaque étiquette d’occupation des sols](media/scenario-aerial-image-classification/example-labels.PNG)

Après l’apprentissage et la validation du modèle de classifieurs, nous appliquons ce modèle aux images aériennes couvrant le comté du Middlesex au Massachusetts (où se trouve le siège du centre NERD de Microsoft pour la Nouvelle-Angleterre) pour illustrer l’utilisation de ce modèle dans l’étude des tendances du développement urbain.

Pour obtenir un classifieur d’images à l’aide de l’apprentissage par transfert, les chercheurs de données doivent souvent créer plusieurs modèles avec différentes méthodes et choisir ensuite le modèle le plus performant. Azure Machine Learning Workbench peut aider les chercheurs de données à coordonner l’apprentissage entre différents environnements Compute, à suivre et comparer les performances de plusieurs modèles, et à appliquer le modèle choisi à des jeux de données volumineux dans le cloud.

## <a name="scenario-structure"></a>Structure du scénario

Dans cet exemple, les images et les modèles préformés sont hébergés dans un compte de stockage Azure. Un cluster Azure HDInsight Spark lit ces fichiers et construit un modèle de classification des images à l’aide de MMLSpark. Le modèle formé et ses prédictions sont ensuite écrits dans le compte de stockage, où ils peuvent être analysés et visualisés par un notebook Jupyter exécuté localement. Azure Machine Learning Workbench coordonne l’exécution à distance des scripts sur le cluster Spark. Il effectue également le suivi des métriques de précision pour plusieurs modèles formés avec des méthodes différentes, permettant ainsi à l’utilisateur de choisir le modèle le plus performant.

![Schéma d’un scénario réel de classification d’images aériennes](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Ces instructions pas à pas vous guident d’abord dans les étapes de création et de préparation d’un compte de stockage Azure et d’un cluster Spark, y compris le transfert des données et l’installation des dépendances. Ensuite, elles expliquent comment lancer les travaux d’apprentissage et comment comparer les performances des modèles obtenus. Pour finir, elles montrent comment appliquer un modèle choisi à un grand ensemble d’images sur le cluster Spark et comment analyser les résultats de prédiction localement.


## <a name="set-up-the-execution-environment"></a>Configurer l’environnement d’exécution

Les instructions suivantes vous guident pendant le processus de configuration de l’environnement d’exécution pour cet exemple.

### <a name="prerequisites"></a>Prérequis
- Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles)
    - Vous allez créer un cluster HDInsight Spark contenant 40 nœuds de travail (sur un total de 168 cœurs). Vérifiez que votre compte a assez de cœurs disponibles en consultant l’onglet « Utilisation + quotas » de votre abonnement dans le portail Azure.
       - Si vous n’avez pas assez de cœurs disponibles, modifiez le modèle de cluster HDInsight pour réduire le nombre de workers provisionnés. Pour obtenir les instructions correspondantes, consultez la section « Créer le cluster HDInsight Spark ».
    - Cet exemple crée un cluster de formation Batch AI avec deux machines virtuelles NC6 (1 GPU, 6 processeurs virtuels). Vérifiez que votre compte a assez de cœurs disponibles dans la région Est des États-Unis en consultant l’onglet « Utilisation + quotas » de votre abonnement dans le portail Azure.
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - Suivez le [guide de démarrage rapide Installer et créer](quickstart-installation.md) pour installer Azure Machine Learning Workbench et créer des comptes d’expérimentation et de gestion des modèles.
- Kit de développement logiciel (SDK) Python [Batch AI](https://github.com/Azure/BatchAI) et Azure CLI 2.0
    - Consultez les sections suivantes du fichier [Lisezmoi des recettes Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes) :
        - « Configuration requise »
        - « Créer et obtenir votre application Azure Active Directory (AAD) »
        - « Inscrire des fournisseurs de ressources BatchAI » (sous « Exécuter des recettes à l’aide d’Azure CLI 2.0 »)
        - « Installer le client de gestion Azure Batch AI »
        - « Installer le Kit de développement logiciel (SDK) Python Azure »
    - Notez l’ID client, le secret et ID de locataire de l’application Azure Active Directory que vous êtes invité à créer. Vous utiliserez ces informations d’identification ultérieurement dans ce didacticiel.
    - Au moment de l’écriture de cet article, Azure Machine Learning Workbench et Azure Batch AI utilisent des duplications distinctes d’Azure CLI 2.0. Pour des soucis de clarté, nous faisons référence à la version Workbench de CLI en l’appelant « CLI lancée à partir d’Azure Machine Learning Workbench » et à la version généralement disponible (qui inclut Batch AI) en l’appelant « Azure CLI 2.0 ».
- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy), un utilitaire gratuit pour coordonner le transfert des fichiers entre les comptes de stockage Azure
    - Assurez-vous que le dossier contenant le fichier exécutable AzCopy est spécifié dans la variable d’environnement PATH de votre système. (Les instructions sur la modification des variables d’environnement sont disponibles [ici](https://support.microsoft.com/help/310519/how-to-manage-environment-variables-in-windows-xp).)
- Un client SSH ; nous vous recommandons [PuTTY](http://www.putty.org/).

Cet exemple a été testé sur un PC Windows 10. Vérifiez que vous pouvez l’exécuter sur n’importe quel ordinateur Windows, y compris des machines virtuelles Azure Data Science. Azure CLI 2.0 a été installée à partir d’un MSI conformément à [ces instructions](https://github.com/Azure/azure-sdk-for-python/wiki/Contributing-to-the-tests#getting-azure-credentials). Vous devrez peut-être apporter quelques modifications mineures (par exemple, changer les variables filepath) si vous exécutez cet exemple sur macOS.

### <a name="set-up-azure-resources"></a>Configurer les ressources Azure

Cet exemple nécessite un cluster HDInsight Spark et un compte de stockage Azure pour héberger les fichiers appropriés. Suivez les instructions ci-dessous pour créer ces ressources dans un nouveau groupe de ressources Azure :

#### <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Classification d’images aériennes » et sélectionnez le modèle
5.  Cliquez sur **Créer**
 
#### <a name="create-the-resource-group"></a>Créer le groupe de ressources

1. Après le chargement de votre projet dans Azure Machine Learning Workbench, ouvrez une CLI en cliquant sur Fichier -> Ouvrir l’invite de commandes.
    Utilisez cette version de CLI pour la majorité du didacticiel. (À l’endroit indiqué, vous êtes invité à ouvrir une autre version de CLI pour préparer les ressources Batch AI.)

1. À partir de l’interface de ligne de commande, connectez-vous à votre compte Azure en exécutant la commande suivante :

    ```
    az login
    ```

    Vous êtes invité à visiter une URL et à taper un code temporaire fourni. Le site web demande les informations d’identification de votre compte Azure.
    
1. Une fois la connexion établie, retournez à l’interface CLI et exécutez la commande suivante pour déterminer les abonnements Azure disponibles pour votre compte Azure :

    ```
    az account list
    ```

    Cette commande répertorie tous les abonnements associés à votre compte Azure. Recherchez l’ID de l’abonnement à utiliser. Écrivez l’ID d’abonnement à l’endroit indiqué dans la commande suivante, puis exécutez-la pour définir l’abonnement actif :

    ```
    az account set --subscription [subscription ID]
    ```

1. Les ressources Azure créées dans cet exemple sont stockées dans un groupe de ressources Azure. Choisissez un nom de groupe de ressources unique et écrivez-le à l’endroit indiqué, puis exécutez les deux commandes pour créer le groupe de ressources Azure :

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Créer le compte de stockage

Nous allons maintenant créer le compte de stockage qui héberge les fichiers projet auxquels HDInsight Spark doit avoir accès.

1. Choisissez un nom de compte de stockage unique et écrivez-le à l’endroit indiqué dans la commande `set` suivante, puis créez un compte de stockage Azure en exécutant les deux commandes :

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Répertoriez les clés de compte de stockage en exécutant la commande suivante :

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Enregistrez la valeur de `key1` comme clé de stockage dans la commande suivante, puis exécutez-la pour stocker la valeur.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Créez un partage de fichier nommé `baitshare` dans votre compte de stockage à l’aide de la commande suivante :

    ```
    az storage share create --account-name %STORAGE_ACCOUNT_NAME% --account-key %STORAGE_ACCOUNT_KEY% --name baitshare
    ```
1. Dans l’éditeur de texte de votre choix, chargez le fichier `settings.cfg` à partir du sous-répertoire « Code » du projet Azure Machine Learning Workbench, puis insérez le nom et la clé du compte de stockage aux endroits indiqués. Enregistrez et fermez le fichier `settings.cfg`.
1. Si vous ne l’avez pas déjà fait, téléchargez et installez l’utilitaire [AzCopy](http://aka.ms/downloadazcopy). Vérifiez que l’exécutable AzCopy se trouve sur votre chemin système. Pour cela, tapez « AzCopy » et appuyez sur Entrée pour afficher sa documentation.
1. Lancez les commandes suivantes pour copier l’ensemble des exemples de données, des modèles préformés et des scripts d’apprentissage de modèle aux emplacements appropriés dans votre compte de stockage :

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/scripts /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.file.core.windows.net/baitshare/scripts /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Le transfert des fichiers peut prendre environ une heure. Pour patienter, vous pouvez passer à la section suivante : vous devrez peut-être ouvrir une autre interface de ligne de commande dans Workbench pour y redéfinir les variables temporaires.

#### <a name="create-the-hdinsight-spark-cluster"></a>Créer le cluster HDInsight Spark

Pour créer un cluster HDInsight, nous vous recommandons d’utiliser le modèle Resource Manager de cluster HDInsight Spark inclus dans le sous-dossier « Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning » de ce projet.

1. Le modèle de cluster HDInsight Spark est le fichier « template.json » qui se trouve dans le sous-dossier « Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning » de ce projet. Par défaut, le modèle crée un cluster Spark avec 40 nœuds Worker. Si vous devez ajuster ce nombre, ouvrez le modèle dans l’éditeur de texte de votre choix et remplacez toutes les instances de « 40 » par le nombre de nœuds Worker désiré.
    - Si vous choisissez un plus petit nombre de nœuds Worker, des erreurs de mémoire insuffisante risquent de se produire par la suite. Pour éviter ces erreurs, exécutez les scripts d’apprentissage et une d’opérationnalisation sur un sous-ensemble des données disponibles, comme décrit plus loin dans ce document.
2. Choisissez un nom et un mot de passe uniques pour le cluster HDInsight et écrivez-les aux endroits indiqués dans la commande suivante : Créez ensuite le cluster en exécutant les commandes :

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Le déploiement de votre cluster peut prendre jusqu’à 30 minutes (ce qui comprend le provisionnement et l’exécution des actions de script).

### <a name="set-up-batch-ai-resources"></a>Configurer les ressources Batch AI

Pendant que vous attendez que le transfert de fichier de compte de stockage et le déploiement du cluster Spark se terminent, vous pouvez préparer le cluster GPU et le serveur de fichiers réseau Batch AI. Ouvrez une invite de commandes Azure CLI 2.0 et exécutez la commande suivante :

```
az --version 
```

Vérifiez que `batchai` est répertorié parmi les modules installés. Dans le cas contraire, vous utilisez peut-être une CLI différente (par exemple, une CLI ouverte via Workbench).

Ensuite, vérifiez que l’inscription du fournisseur a réussi. (L’inscription du fournisseur peut prendre jusqu’à quinze minutes et est peut-être toujours en cours si vous avez récemment appliqué les [instructions de configuration de Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes).) Vérifiez que « Microsoft.Batch » et « Microsoft.BatchAI » apparaissent avec l’état « Inscrit » dans la sortie de la commande suivante :

```
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Si ce n’est pas le cas, exécutez les commandes d’inscription du fournisseur suivantes et attendez environ 15 minutes que l’inscription se termine.
```
az provider register --namespace Microsoft.Batch
az provider register --namespace Microsoft.BatchAI
```

Modifiez les commandes suivantes pour remplacer les expressions entre crochets avec les valeurs que vous avez utilisées précédemment pendant la création du compte de stockage et du groupe de ressources. Ensuite, stockez les valeurs en tant que variables en exécutant les commandes :
```
az account set --subscription [subscription ID]
set AZURE_RESOURCE_GROUP=[resource group name]
set STORAGE_ACCOUNT_NAME=[storage account name]
set STORAGE_ACCOUNT_KEY=[storage account key]
az configure --defaults location=eastus
az configure --defaults group=%AZURE_RESOURCE_GROUP%
```

Identifiez le dossier contenant votre projet Azure Machine Learning (par exemple, `C:\Users\<your username>\AzureML\aerialimageclassification`). Remplacez la valeur entre crochets par le chemin d’accès du dossier (avec une barre oblique de fin) et exécutez la commande :
```
set PATH_TO_PROJECT=[The filepath of your project's root directory]
```
Vous êtes maintenant prêt à créer les ressources Batch AI nécessaires pour ce didacticiel.

#### <a name="prepare-the-batch-ai-network-file-server"></a>Préparer le serveur de fichiers réseau Batch AI

Votre cluster Batch AI accède à vos données de formation sur un serveur de fichiers réseau. L’accès aux données peut être beaucoup plus rapide lorsque vous accédez à des fichiers sur un serveur de fichiers réseau par rapport à un partage de fichiers Azure ou un stockage blob Azure.

1. Exécutez la commande suivante pour créer un serveur de fichiers réseau :

    ```
    az batchai file-server create -n landuseclassifier -u demoUser -p Dem0Pa$$w0rd --vm-size Standard_DS2_V2 --disk-count 1 --disk-size 1000 --storage-sku Premium_LRS
    ```

1. Vérifiez l’état d’approvisionnement de votre serveur de fichiers réseau à l’aide de la commande suivante :

    ```
    az batchai file-server list
    ```

    Lorsque l’état « provisioningState » du serveur de fichiers réseau nommé « landuseclassifier » est défini sur « succeeded », il est prêt à être utilisé. L’approvisionnement prend environ cinq minutes.
1. Recherchez l’adresse IP de votre serveur de fichiers réseau dans la sortie de la commande précédente (la propriété « fileServerPublicIp » sous « mountSettings »). Écrivez l’adresse IP à l’endroit indiqué dans la commande suivante, puis stockez la valeur en exécutant la commande :

    ```
    set AZURE_BATCH_AI_TRAINING_NFS_IP=[your NFS IP address]
    ```

1. À l’aide de votre outil SSH préféré (l’exemple de commande suivant utilise [PuTTY](http://www.putty.org/)), exécutez le script `prep_nfs.sh` de ce projet sur le serveur de fichiers réseau pour transférer les ensembles d’images de formation et de validation ici.

    ```
    putty -ssh demoUser@%AZURE_BATCH_AI_TRAINING_NFS_IP% -pw Dem0Pa$$w0rd -m %PATH_TO_PROJECT%\Code\01_Data_Acquisition_and_Understanding\02_Batch_AI_Training_Provisioning\prep_nfs.sh
    ```

    Ne vous inquiétez pas si les mises à jour de la progression de l’extraction et du téléchargement des données défilent si rapidement dans la fenêtre de l’interpréteur de commandes qu’elles sont illisibles.

Si vous le souhaitez, vous pouvez vérifier que le transfert de données s’est déroulé comme prévu en vous connectant au serveur de fichiers avec votre outil SSH préféré et en vérifiant les contenus du répertoire `/mnt/data`. Vous devriez trouver deux dossiers, training_images et validation_images. Chacun de ces dossiers contient des sous-dossiers nommés en fonction des catégories d’occupation des sols.  Les ensembles de formation et de validation doivent contenir environ 44k et 11k images, respectivement.

#### <a name="create-a-batch-ai-cluster"></a>Créer un cluster Batch AI

1. Exécutez les commandes suivantes pour créer le cluster :

    ```
    az batchai cluster create -n landuseclassifier2 -u demoUser -p Dem0Pa$$w0rd --afs-name baitshare --nfs landuseclassifier --image UbuntuDSVM --vm-size STANDARD_NC6 --max 2 --min 2 --storage-account-name %STORAGE_ACCOUNT_NAME% 
    ```

1. Utilisez la commande suivante pour vérifier l’état d’approvisionnement de votre cluster :

    ```
    az batchai cluster list
    ```

    Lorsque l’état d’allocation du cluster nommé « landuseclassifier » passe du redimensionnement à la stabilité, il est possible d’envoyer des travaux. Toutefois, les travaux ne s’exécutent pas tant que toutes les machines virtuelles dans le cluster n’ont pas quitté l’état « en préparation ». Si la propriété « errors » du cluster n’est pas null, une erreur s’est produite lors de la création du cluster et ne doit pas être utilisée.

#### <a name="record-batch-ai-training-credentials"></a>Enregistrer les informations d’identification de formation Batch AI

Pendant que vous attendez que l’allocation du cluster se termine, ouvrez le fichier `settings.cfg` depuis le sous-répertoire « Code » de ce projet dans l’éditeur de texte de votre choix. Mettez à jour les variables suivantes avec vos propres informations d’identification :
- `bait_subscription_id` (votre ID d’abonnement Azure de 36 caractères)
- `bait_aad_client_id` (l’ID de client/d’application Azure Active Directory mentionné dans la section « Configuration requise »)
- `bait_aad_secret` (le secret d’application Azure Active Directory mentionné dans la section « Configuration requise »)
- `bait_aad_tenant` (l’ID de locataire Azure Active Directory mentionné dans la section « Configuration requise »)
- `bait_region` (au moment de l’écriture de cet article, la seule option disponible est : eastus)
- `bait_resource_group_name` (le groupe de ressources que vous avez choisi précédemment)

Une fois que vous avez attribué ces valeurs, les lignes modifiées de votre fichier settings.cfg doivent ressembler à ce qui suit :

```
[Settings]
    # Credentials for the Azure Storage account
    storage_account_name = yoursaname
    storage_account_key = kpIXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXQ==
    
    # Batch AI training credentials
    bait_subscription_id = 0caXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX9c3
    bait_aad_client_id = d0aXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX7f8
    bait_aad_secret = ygSXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX6I=
    bait_aad_tenant = 72fXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXb47
    bait_region = eastus
    bait_resource_group_name = yourrgname
```

Enregistrez et fermez `settings.cfg`.

Vous pouvez maintenant fermer la fenêtre de CLI dans laquelle vous avez exécuté les commandes de création de ressources Batch AI. Toutes les étapes supplémentaires de ce didacticiel utilisent une CLI lancée depuis Azure Machine Learning Workbench.

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Préparer l’environnement d’exécution Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Inscrire le cluster HDInsight comme cible de calcul Azure Machine Learning Workbench

Une fois la création du cluster HDInsight est terminée, inscrivez le cluster comme cible de calcul pour votre projet comme suit :

1.  Exécutez la commande suivante à partir de l’interface de ligne de commande Azure Machine Learning :

    ```
    az ml computetarget attach cluster --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD%
    ```

    Cette commande ajoute deux fichiers, `myhdi.runconfig` et `myhdi.compute`, au dossier `aml_config` de votre projet.

1. Ouvrez le fichier `myhdi.compute` dans l’éditeur de texte de votre choix. Remplacez la ligne `yarnDeployMode: cluster` par `yarnDeployMode: client`, puis enregistrez et fermez le fichier.
1. Exécutez la commande suivante pour préparer votre environnement HDInsight :
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Installer des dépendances locales

Ouvrez une interface CLI à partir d’Azure Machine Learning Workbench et exécutez la commande suivante pour installer les dépendances nécessaires pour une exécution locale :

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn azure-mgmt-batchai
```

## <a name="data-acquisition-and-understanding"></a>Acquisition de données et compréhension

Ce scénario utilise les données publiques d’imagerie aérienne du programme NAIP ([National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/)) avec une résolution de 1 mètre. Nous avons rogné les données NAIP d’origine pour générer des jeux de fichiers PNG de 224 x 224 pixels, puis nous les avons triés selon les étiquettes d’occupation du sol de la base de données NLCD ([National Land Cover Database](https://www.mrlc.gov/nlcd2011.php)). Voici un exemple d’image avec l’étiquette « Developed » en taille réelle :

![Exemple de vignette d’un terrain développé](media/scenario-aerial-image-classification/sample-tile-developed.png)

Nous utilisons des jeux d’environ 44 000 images et 11 000 images en classes équilibrées pour l’apprentissage et la validation du modèle, respectivement. Pour démontrer le déploiement du modèle, nous utilisons un jeu d’environ 67 000 images couvrant le comté de Middlesex, dans le Massachusetts. C’est là que se trouve le centre de recherche et de développement de Microsoft en Nouvelle-Angleterre (NERD). Pour plus d’informations sur la construction de ces jeux d’images, consultez le [dépôt git Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Localisation du comté de Middlesex, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Durant la configuration, les jeux d’images aériennes utilisés dans cet exemple ont été transférés au compte de stockage que vous avez créé. Toutes les images d’apprentissage, de validation et d’opérationnalisation sont des fichiers PNG de 224 x 224 pixels avec une résolution d’un pixel par mètre carré. Les images d’apprentissage et de validation ont été organisées dans des sous-dossiers selon leur étiquette d’occupation du sol. (Les étiquettes d’occupation du sol des images d’opérationnalisation sont inconnues et, dans de nombreux cas, ambiguës ; certaines de ces images de contiennent plusieurs types de sols.) Pour plus d’informations sur la construction de ces jeux d’images, consultez le [dépôt git Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Pour afficher des exemples d’images dans votre compte de stockage Azure (facultatif) :
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez le nom de votre compte de stockage dans la barre de recherche en haut de l’écran. Cliquez sur votre compte de stockage dans les résultats de la recherche.
2. Cliquez sur le lien « Objets blob » dans le volet principal du compte de stockage.
3. Cliquez sur le conteneur nommé « train ». Vous devriez voir une liste de répertoires nommés en fonction de l’occupation du sol.
4. Cliquez sur l’un de ces répertoires pour charger la liste des images qu’il contient.
5. Cliquez sur n’importe quelle image et téléchargez-la pour l’afficher.
6. Si vous le souhaitez, cliquez sur les conteneurs nommés « test » et « middlesexma2016 » pour afficher leur contenu.

## <a name="modeling"></a>Modélisation

### <a name="training-models-with-azure-batch-ai"></a>Formation de modèles avec Azure Batch AI

Le script `run_batch_ai.py` dans le sous-dossier « Code\02_Modeling » du projet Workbench sert à exécuter un travail de formation Batch AI. Ce travail reforme un DNN de classifieur d’image choisi par l’utilisateur (AlexNet ou ResNet 18 préformé sur ImageNet). La profondeur de la reformation peut également être spécifiée : reformer uniquement la couche finale du réseau peut réduire le surajustement lorsque quelques exemples de formation sont disponibles, tout en ajustant l’intégralité du réseau (ou, pour AlexNet, les couches entièrement connectées) peut conduire à des performances de modèles supérieures lorsque l’ensemble de formation est suffisamment grand.

Quand le travail de formation se termine, ce script enregistre le modèle (avec un fichier décrivant le mappage entre la sortie d’entier et les étiquettes de chaîne du modèle) et les prédictions dans le stockage blob. Le fichier journal du travail BAIT est analysé pour extraire le timecourse de l’amélioration du taux d’erreur sur les époques de formation. Le timecourse de l’amélioration du taux d’erreur est consigné dans la fonctionnalité d’historique des exécutions d’AML Workbench pour une consultation ultérieure.

Sélectionnez un nom pour votre modèle formé, un type de modèle préformé et une profondeur de reformation. Écrivez vos sélections aux endroits indiqués dans la commande suivante, puis commencez la reformation en exécutant la commande à partir d’une CLI Azure ML :

```
az ml experiment submit -c local Code\02_Modeling\run_batch_ai.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --retraining_type {last_only,fully_connected,all} --num_epochs 10
```

L’exécution d’Azure Machine Learning prend environ une demi-heure. Nous vous conseillons d’exécuter quelques commandes similaires (en variant le nom du modèle de sortie, le type de modèle formé et la profondeur de reformation) afin que vous puissiez comparer les performances des modèles formés avec différentes méthodes.

### <a name="training-models-with-mmlspark"></a>Modèles d’apprentissage avec MMLSpark

Le script `run_mmlspark.py` dans le sous-dossier « Code\02_Modeling » du projet Workbench sert à effectuer l’apprentissage d’un modèle [MMLSpark](https://github.com/Azure/mmlspark) pour la classification d’images. Le script commence par caractériser les images du jeu d’apprentissage à l’aide d’un DNN de classification d’images préformé sur le jeu de données ImageNet (AlexNet ou ResNet à 18 couches). Le script utilise ensuite les images caractérisées pour effectuer l’apprentissage d’un modèle MMLSpark (forêt aléatoire ou modèle de régression logistique) pour classer les images. Le jeu d’images de test est ensuite caractérisé et noté avec le modèle formé. La précision des prédictions du modèle sur le jeu de test est calculée et consignée dans la fonctionnalité d’historique des exécutions d’Azure Machine Learning Workbench. Enfin, le modèle MMLSpark formé et ses prédictions sur le jeu de test sont enregistrés dans le stockage d’objets blob.

Sélectionnez un nom de modèle de sortie unique pour votre modèle formé, un type de modèle préformé et un type de modèle MMLSpark. Écrivez vos sélections aux endroits indiqués dans le modèle de commande suivant, puis commencez le réapprentissage en exécutant la commande à partir d’une interface de ligne de commande Microsoft Azure Machine Learning :

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Vous pouvez utiliser un paramètre `--sample_frac` supplémentaire pour effectuer l’apprentissage du modèle et le tester avec un sous-ensemble des données disponibles. L’utilisation d’une fraction des échantillons permet de diminuer les exigences liées au runtime et à la mémoire, mais c’est au détriment de la précision du modèle formé. (Par exemple, une exécution avec `--sample_frac 0.1` dure à peu près vingt minutes.) Pour plus d’informations sur ce paramètre et les autres paramètres disponibles, exécutez `python Code\02_Modeling\run_mmlspark.py -h`.

Les utilisateurs sont invités à exécuter ce script plusieurs fois avec différents paramètres d’entrée. Vous pouvez ensuite comparer les performances des modèles résultants dans la fonctionnalité d’historique des exécutions d’Azure Machine Learning Workbench.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Comparaison des performances des modèles à l’aide de la fonctionnalité d’historique des exécutions de Workbench

Après au moins deux exécutions d’apprentissage d’un type ou l’autre, cliquez sur l’icône d’horloge située à côté de la barre de menus de gauche pour accéder à la fonctionnalité Historique des exécutions dans Workbench. Sélectionnez `run_mmlspark.py` dans la liste des scripts à gauche. Un volet se charge et compare la précision du jeu de test pour toutes les exécutions. Pour plus de détails, faites défiler vers le bas et cliquez sur le nom d’une exécution individuelle.

## <a name="deployment"></a>Déploiement

Pour appliquer l’un de vos modèles formés aux images aériennes couvrant le comté de Middlesex, MA à l’aide de l’exécution distante sur HDInsight, insérez le nom du modèle désiré dans la commande suivante et exécutez-la :

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Vous pouvez utiliser un paramètre `--sample_frac` supplémentaire pour opérationnaliser le modèle avec un sous-ensemble des données disponibles. L’utilisation d’une fraction des échantillons permet de diminuer les exigences liées au runtime et à la mémoire, mais c’est au détriment de la précision du modèle formé. Pour plus d’informations sur ce paramètre et les autres paramètres disponibles, exécutez `python Code\03_Deployment\batch_score_spark -h`.

Ce script écrit les prédictions du modèle dans votre compte de stockage. Vous pouvez examiner les prédictions selon les indications de la section suivante.

## <a name="visualization"></a>Visualisation

Le notebook Jupyter « Model prediction analysis » dans le sous-dossier « Code\04_Result_Analysis » du projet Workbench visualise les prédictions d’un modèle. Pour charger et exécuter le notebook, effectuez les étapes suivantes :
1. Ouvrez le projet dans Workbench et cliquez sur l’icône de dossier (« Files ») à côté du menu de gauche pour charger la liste de répertoires.
2. Accédez au sous-dossier « Code\04_Result_Analysis » et cliquez sur le notebook nommé « Model prediction analysis ». Un rendu de l’aperçu du notebook doit s’afficher.
3. Cliquez sur « Start Notebook Server » pour charger le notebook.
4. Dans la première cellule, entrez le nom du modèle dont vous souhaitez analyser les résultats à l’endroit indiqué.
5. Cliquez sur « Cell -> Run All » pour exécuter toutes les cellules dans le notebook.
6. Passez en revue le notebook pour découvrir plus en détail les analyses et les visualisations qu’il présente.

## <a name="cleanup"></a>Nettoyage
Une fois l’exemple terminé, nous vous recommandons de supprimer toutes les ressources que vous avez créées en exécutant la commande suivante à partir de l’interface de ligne de commande Azure :

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Références

- [Dépôt Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Décrit la construction du jeu de données au moyen d’images disponibles gratuitement et d’étiquettes
- Dépôt GitHub [MMLSpark](https://github.com/Azure/mmlspark)
   - Contient des exemples supplémentaires d’apprentissage et d’évaluation de modèle avec MMLSpark

## <a name="conclusions"></a>Conclusions

Azure Machine Learning Workbench permet aux scientifiques des données de déployer facilement leur code sur des cibles de calcul distantes. Dans cet exemple, le code de formation MMLSpark local a été déployé pour une exécution à distance sur un cluster HDInsight et un script local a lancé un travail de formation sur un cluster GPU Azure Batch AI. La fonctionnalité d’historique des exécutions d’Azure Machine Learning Workbench nous a permis de faire le suivi des performances de plusieurs modèles et d’identifier le modèle le plus exact. Grâce aux notebooks Jupyter de Workbench, nous avons pu visualiser les prédictions de nos modèles dans un environnement graphique interactif.

## <a name="next-steps"></a>étapes suivantes
Pour approfondir cet exemple :
- Dans la fonctionnalité d’historique des exécutions d’Azure Machine Learning Workbench, cliquez sur le symbole d’engrenage pour sélectionner les graphes et les métriques à afficher.
- Examinez les instructions qui appellent `run_logger` dans les exemples de scripts. Veillez à bien comprendre comment chaque métrique est enregistrée.
- Examinez les instructions qui appellent `blob_service` dans les exemples de scripts. Veillez à bien comprendre comment les modèles formés et les prédictions sont stockés dans le cloud et récupérés.
- Explorez le contenu des conteneurs créés dans votre compte de stockage d’objets blob. Veillez à bien comprendre le script ou la commande responsable de la création de chaque groupe de fichiers.
- Modifiez le script d’apprentissage pour effectuer l’apprentissage d’un autre type de modèle MMLSpark ou pour changer les hyperparamètres du modèle. Utilisez la fonctionnalité d’historique des exécutions pour déterminer si vos changements ont permis d’améliorer ou non la précision du modèle.
