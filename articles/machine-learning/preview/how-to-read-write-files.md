---
title: "Lire et écrire des fichiers de données volumineux | Microsoft Docs"
description: "Lisez et écrivez des fichiers volumineux dans des expériences Azure Machine Learning."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: 099ff69b396c35730471d684b59115f03ccf67d9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="persisting-changes-and-working-with-large-files"></a>Persistance des changements et utilisation de fichiers volumineux
Le service Azure Machine Learning - Expérimentation vous permet de configurer des cibles d’exécution variées. Certaines cibles sont locales, comme un ordinateur local ou un conteneur Docker sur un ordinateur local. D’autres sont distantes, comme un conteneur Docker sur un ordinateur distant ou un cluster HDInsight. Pour plus d’informations, consultez [Présentation du service d’exécution d’expérience Azure Machine Learning](experimentation-service-configuration.md). 

Pour exécuter des scripts sur une cible, vous devez au préalable copier le dossier du projet dans la cible de calcul. Cette opération est obligatoire, même si vous avez une exécution locale qui utilise un dossier temporaire local à cet effet. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Isolation, portabilité et reproductibilité de l’exécution
L’objectif de cette conception est de garantir l’isolation, la reproductibilité et la portabilité de l’exécution. Si vous exécutez deux fois ce script sur la même cible de calcul ou sur une autre, vous obtenez les mêmes résultats. Les changements apportés durant la première exécution ne doivent pas affecter ceux de la deuxième exécution. Grâce à cette conception, vous pouvez traiter les cibles de calcul comme des ressources de calcul sans état, chacune d’entre elles n’ayant aucune affinité avec les travaux exécutés une fois terminés.

## <a name="challenges"></a>Défis
Bien que cette conception offre des avantages en matière de portabilité et de répétabilité, elle soulève également des défis uniques.

### <a name="persisting-state-changes"></a>Persistance des changements d’état
Si votre script modifie l’état du contexte de calcul, les changements ne sont pas conservés pour votre prochaine exécution, ni propagés automatiquement vers l’ordinateur client. 

Plus spécifiquement, si votre script crée un sous-dossier ou écrit dans un fichier, ce dossier ou fichier n’est pas présent dans le répertoire de votre projet après l’exécution. Les fichiers sont stockés dans un dossier temporaire dans l’environnement cible de calcul. Vous pouvez les utiliser à des fins de débogage, mais vous ne pouvez pas compter sur leur existence permanente.

### <a name="working-with-large-files-in-the-project-folder"></a>Utilisation de fichiers volumineux dans le dossier du projet

Si le dossier de votre projet contient des fichiers volumineux, une latence se produit quand le dossier est copié dans l’environnement de calcul cible au début d’une exécution. Même si l’exécution se produit localement, le trafic disque inutile doit être évité. Pour cette raison, nous limitons actuellement la taille maximale d’un projet à 25 Mo.

## <a name="option-1-use-the-outputs-folder"></a>Option 1 : Utiliser le dossier *outputs*
Cette option est préférable si toutes les conditions suivantes s’appliquent :
* Votre script génère des fichiers.
* Vous prévoyez des changements au niveau des fichiers pour chaque expérience.
* Vous souhaitez conserver un historique de ces fichiers. 

Les cas d’utilisation courants sont les suivants :
* Training a model
* Création d’un jeu de données
* Traçage d’un graphe comme fichier image dans le cadre de l’exécution de l’apprentissage du modèle 

Vous pouvez également comparer les sorties d’une exécution à l’autre, sélectionner un fichier de sortie (par exemple, un modèle) produit par une exécution précédente et l’utiliser pour une autre tâche (par exemple, pour une notation).

Vous pouvez écrire des fichiers dans un dossier nommé *outputs* relatif au répertoire racine. Le dossier bénéficie d’un traitement spécial de la part du service Expérimentation. Tout ce que votre script crée dans le dossier pendant l’exécution, tel qu’un fichier de modèle, un fichier de données ou un fichier image tracé (collectivement appelés _artefacts_), est copié dans le compte Stockage Blob Azure associé à votre compte d’expérimentation une fois l’exécution terminée. Les fichiers font dès lors partie de l’enregistrement de l’historique des exécutions.

Voici un exemple de code pour stocker un modèle dans le dossier *outputs* :
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Vous pouvez télécharger un artefact en accédant à la section **Fichiers de sortie** de la page de détails d’une exécution particulière dans Azure Machine Learning Workbench. Sélectionnez l’exécution, puis cliquez sur le bouton **Télécharger**. Vous pouvez également entrer la commande `az ml asset download` dans la fenêtre de l’interface de ligne de commande.

Pour obtenir un exemple plus complet, consultez la script Python `iris_sklearn.py` dans l’exemple de projet _Classifying Iris_.

## <a name="option-2-use-the-shared-folder"></a>Option 2 : Utiliser le dossier partagé
Si vous n’avez pas besoin de conserver un historique des fichiers de chaque exécution, l’utilisation d’un dossier partagé accessible entre les exécutions indépendantes peut être une option intéressante pour les scénarios suivants : 
- Votre script doit charger des données à partir de fichiers locaux, tels que des fichiers .csv ou un répertoire de fichiers texte ou image, en tant que données de formation ou de test. 
- Votre script traite des données brutes et écrit des résultats intermédiaires, comme des données de formation avec des caractéristiques à partir de fichiers texte ou image, qui sont ensuite utilisés dans une exécution d’apprentissage. 
- Votre script génère un modèle, que votre script de notation suivant doit choisir et utiliser à des fins d’évaluation. 

Il est important de noter que le dossier partagé réside localement sur votre cible de calcul choisie. Cette option ne fonctionne donc que si toutes vos exécutions de script référençant ce dossier partagé sont exécutées sur la même cible de calcul et que celle-ci n’est pas recyclée entre les exécutions.

En tirant parti de la fonctionnalité de dossier partagé, vous pouvez lire ou écrire dans un dossier spécial identifié par une variable d’environnement (`AZUREML_NATIVE_SHARE_DIRECTORY`). 

### <a name="example"></a>Exemples
Voici un exemple de code Python montrant comment utiliser ce dossier de partage pour lire et écrire un fichier texte :
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Pour obtenir un exemple plus complet, consultez le fichier *iris_sklearn_shared_folder.py* dans l’exemple de projet _Classifying Iris_.

Avant d’utiliser cette fonctionnalité, vous devez définir dans le fichier *.compute* certaines configurations simples représentant le contexte d’exécution ciblé dans le dossier *aml_config*. Le chemin réel du dossier peut varier en fonction de la cible de calcul que vous choisissez et de la valeur que vous configurez.

### <a name="configure-local-compute-context"></a>Configurer le contexte de calcul local

Pour activer cette fonctionnalité dans un contexte de calcul local, ajoutez simplement la ligne suivante à votre fichier *.compute*, qui représente l’environnement _local_ (généralement appelé *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

Le chemin ~/.azureml/share est le chemin du dossier de base par défaut. Vous pouvez le remplacer par n’importe quel chemin absolu local accessible à l’exécution du script. Les nom de compte d’expérimentation, nom d’espace de travail et nom de projet sont automatiquement ajoutés au nom du répertoire de base pour former le chemin complet du répertoire partagé. Par exemple, les fichiers peuvent être écrits dans le chemin suivant (et récupérés de celui-ci) si vous utilisez la valeur par défaut précédente :

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>Configurer le contexte de calcul Docker (local ou distant)
Pour activer cette fonctionnalité sur un contexte de calcul Docker, vous devez ajouter les deux lignes suivantes à votre fichier *.compute* Docker local ou distant.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>La propriété **sharedVolumes** doit avoir la valeur *true* quand vous utilisez la variable d’environnement `AZUREML_NATIVE_SHARE_DIRECTORY` pour accéder au dossier partagé. Sinon, l’exécution échoue.

Le code s’exécutant dans le conteneur Docker voit toujours ce dossier partagé comme étant */azureml-share/*. Le chemin du dossier tel que vu par le conteneur Docker n’est pas configurable. N’utilisez pas ce nom de dossier dans votre code. Au lieu de cela, utilisez toujours le nom de variable d’environnement `AZUREML_NATIVE_SHARE_DIRECTORY` pour faire référence à ce dossier. Il est mappé à un dossier local sur le contexte de calcul ou la machine hôte Docker. Le répertoire de base de ce dossier local est la valeur configurable du paramètre `nativeSharedDirectory` dans le fichier *.compute*. Le chemin local du dossier partagé sur l’ordinateur hôte, si vous utilisez la valeur par défaut, est le suivant :
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>Le chemin du dossier partagé sur le disque local est le même qu’il s’agisse d’un contexte de calcul local ou d’un contexte de calcul Docker local. Cela signifie que vous pouvez même partager des fichiers entre une exécution locale et une exécution Docker locale.

Vous pouvez placer les données d’entrée directement dans ces dossiers en vue de leur récupération éventuelle par vos exécutions locales ou Docker sur l’ordinateur. Vous pouvez également, à partir de vos exécutions locales ou Docker, écrire des fichiers dans ce dossier afin qu’ils y soient rendus persistants, survivant ainsi au cycle de vie de l’exécution.

Pour plus d’informations, consultez [Fichiers de configuration de l’exécution Azure Machine Learning Workbench](experimentation-service-configuration-reference.md).

>[!NOTE]
>La variable d’environnement `AZUREML_NATIVE_SHARE_DIRECTORY` n’est pas prise en charge dans un contexte de calcul HDInsight. Toutefois, vous pouvez facilement obtenir le même résultat en utilisant explicitement un chemin de stockage d’objets blob Azure absolu pour lire et écrire dans le stockage d’objets blob lié.

## <a name="option-3-use-external-durable-storage"></a>Option 3 : Utiliser un stockage durable externe

Vous pouvez utiliser un stockage durable externe pour rendre l’état persistant pendant l’exécution. Cette option est utile dans les scénarios suivants :
- Vos données d’entrée sont déjà stockées dans un stockage durable accessible à partir de l’environnement de calcul cible.
- Les fichiers n’ont pas besoin de faire partie des enregistrements de l’historique des exécutions.
- Les fichiers doivent être partagés par des exécutions dans différents environnements de calcul.
- Les fichiers doivent pouvoir survivre au contexte de calcul lui-même.

Une approche consiste à utiliser le stockage blob Azure à partir de votre code Python ou PySpark. Voici un exemple :

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Voici également un exemple d’attachement d’un stockage d’objets blob Azure arbitraire à un runtime Spark HDI :
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Conclusion
Étant donné qu’Azure Machine Learning exécute des scripts en copiant le dossier de projet entier dans le contexte de calcul cible, vous devez accorder une attention particulière aux fichiers d’entrée, de sortie et intermédiaires volumineux. Pour les transactions impliquant des fichiers volumineux, vous pouvez utiliser le dossier outputs spécial, le dossier partagé accessible par le biais de la variable d’environnement `AZUREML_NATIVE_SHARE_DIRECTORY` ou un stockage durable externe. 

## <a name="next-steps"></a>Étapes suivantes
- Passez en revue l’article [Fichiers de configuration de l’exécution Azure Machine Learning Workbench](experimentation-service-configuration-reference.md).
- Découvrez comment le projet de didacticiel [Classifying Iris](tutorial-classifying-iris-part-1.md) utilise le dossier outputs pour rendre persistant un modèle formé.
