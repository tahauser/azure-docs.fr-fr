---
title: "Utilisation de l’extensibilité Python avec Azure Machine Learning Data Preparations | Microsoft Docs"
description: "Ce document fournit une vue d’ensemble et des exemples détaillés de l’utilisation du code Python pour étendre les fonctionnalités de préparation des données"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: cc1aef7ed7c4a7d03a7fa63e71c8c27aca10095a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparations-python-extensions"></a>Extensions Python Data Preparations
Afin de combler les lacunes de fonctionnalité qui existent entre les fonctions intégrées, Azure Machine Learning Data Preparations offre des possibilités d’extension à plusieurs niveaux. Ce document décrit l’extensibilité via un script Python. 

## <a name="custom-code-steps"></a>Étapes de code personnalisé 
Data Preparations comporte les étapes personnalisées suivantes, dans le cadre desquelles les utilisateurs peuvent écrire du code :

* File Reader*
* Writer*
* Add Column
* Advanced Filter
* Transform Dataflow
* Transform Partition

*À l’heure actuelle, ces étapes ne sont pas prises en charge dans une exécution Spark.

## <a name="code-block-types"></a>Types de blocs de code 
Pour chacune de ces étapes, nous prenons en charge deux types de blocs de code. Tout d’abord, nous prenons en charge une expression Python simple exécutée telle quelle. Deuxièmement, nous prenons en charge un module Python où nous appelons une fonction particulière avec une signature connue dans le code que vous fournissez.

Par exemple, vous pouvez ajouter une colonne qui calcule le journal d’une autre colonne des deux manières suivantes :

Expression 

```python    
    math.log(row["Score"])
```

Module 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


La transformation Add Column en mode Module s’attend à trouver une fonction nommée `newvalue` qui accepte une variable de ligne et renvoie la valeur de la colonne. Ce module peut contenir n’importe quelle quantité de code Python avec d’autres fonctions, des importations, etc.

Les détails de chaque point d’extension sont décrits dans les sections suivantes. 

## <a name="imports"></a>Importations 
Si vous utilisez le type de bloc Expression, vous pouvez toujours ajouter des instructions **import** à votre code. Elles doivent être regroupées dans les premières lignes de votre code.

Correct 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

Error  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
Si vous utilisez le type de bloc Module, vous pouvez suivre toutes les règles Python normales d’utilisation de l’instruction **import**. 

## <a name="default-imports"></a>Importations par défaut
Les importations suivantes sont toujours incluses et utilisables dans votre code. Il est inutile de les importer à nouveau. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>Installer de nouveaux packages
Pour utiliser un package qui n’est pas installé par défaut, vous devez tout d’abord l’installer dans les environnements utilisés par Data Preparations. Cette installation doit être effectuée à la fois sur votre ordinateur local et sur les cibles de calcul que vous souhaitez utiliser.

Pour installer les packages dans une cible de calcul, vous devez modifier le fichier conda_dependencies.yml situé dans le dossier aml_config, sous la racine de votre projet.

### <a name="windows"></a>Windows 
Pour rechercher l’emplacement dans Windows, la méthode consiste à trouver l’installation de Python propre à l’application et son répertoire de scripts. L’emplacement par défaut est :  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

Exécutez ensuite l’une des commandes suivantes : 

`conda install <libraryname>` 

or 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Pour rechercher l’emplacement sur un Mac, la méthode consiste à trouver l’installation de Python propre à l’application et son répertoire de scripts. L’emplacement par défaut est : 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

Exécutez ensuite l’une des commandes suivantes : 

`./conda install <libraryname>`

or 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>Utiliser des modules personnalisés
Dans la transformation d’un flux de données (script), écrivez le code Python suivant

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

Dans Ajouter une colonne (script), définissez le type de bloc de code sur Module puis écrivez le code Python suivant

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
Selon les différents contextes d’exécution (local, Docker, Spark), pointez le chemin d’accès absolu vers l’emplacement approprié. Vous pouvez utiliser « os.getcwd() + relativePath » pour le localiser.


## <a name="column-data"></a>Données de la colonne 
Les données de la colonne sont accessibles à partir d’une ligne à l’aide de la notation par points ou de la notation clé-valeur. Les noms de colonnes qui contiennent des espaces ou des caractères spéciaux ne sont pas accessibles à l’aide de la notation par points. La variable `row` doit toujours être définie dans les deux modes des extensions Python (Module et Expression). 

Exemples 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="file-reader"></a>File Reader 
### <a name="purpose"></a>Objectif 
Le point d’extension File Reader vous permet de contrôler intégralement le processus de lecture d’un fichier dans un flux de données. Le système appelle votre code et transmet la liste des fichiers que vous devez traiter. Votre code doit créer et retourner un cadre de données Pandas. 

>[!NOTE]
>Ce point d’extension ne fonctionne pas dans Spark. 


### <a name="how-to-use"></a>Utilisation 
Vous accédez à ce point d’extension à partir de l’Assistant **Ouvrir la source de données**. Choisissez **Fichier** sur la première page, puis choisissez l’emplacement de votre fichier. Dans la page **Choisir les paramètres de fichier**, déroulez la liste **Type de fichier** et choisissez **Fichier personnalisé (Script)**. 

Votre code reçoit un cadre de données Pandas nommé « df », qui contient des informations sur les fichiers que vous devez lire. Si vous choisissez d’ouvrir un répertoire qui contient plusieurs fichiers, le cadre de données contient plusieurs lignes.  

Cette trame de données comporte les colonnes suivantes :

- Path : fichier à lire.
- PathHint : indique l’emplacement du fichier. Valeurs : Local, AzureBlobStorage et AzureDataLakeStorage.
- AuthenticationType : type d’authentification utilisé pour accéder au fichier. Valeurs : None, SasToken et OAuthToken.
- AuthenticationValue : contient la valeur None ou le jeton à utiliser.

### <a name="syntax"></a>Syntaxe 
Expression 

```python
    paths = df['Path'].tolist()  
    df = pd.read_csv(paths[0])
```


Module  
```python
PathHint = Local  
def read(df):  
    paths = df['Path'].tolist()  
    filedf = pd.read_csv(paths[0])  
    return filedf  
```
 

## <a name="writer"></a>Writer 
### <a name="purpose"></a>Objectif 
Le point d’extension Writer vous permet de contrôler intégralement le processus d’écriture de données à partir d’un flux de données. Le système appelle votre code et transmet un cadre de données. Votre code peut utiliser le cadre de données pour écrire des données comme vous le souhaitez. 

>[!NOTE]
>Le point d’extension Writer ne fonctionne pas dans Spark.


### <a name="how-to-use"></a>Utilisation 
Vous pouvez ajouter ce point d’extension à l’aide du bloc Write Dataflow (Script). Ce dernier est disponible dans le menu **Transformations** de niveau supérieur.

### <a name="syntax"></a>Syntaxe 
Expression

```python
    df.to_csv('c:\\temp\\output.csv')
```

Module

```python
def write(df):  
    df.to_csv('c:\\temp\\output.csv')  
    return df
```
 
 
Ce bloc d’écriture personnalisé peut apparaître au milieu d’une liste d’étapes. Si vous utilisez un Module, votre fonction d’écriture doit renvoyer le cadre de données qui constitue l’entrée vers l’étape qui suit. 

## <a name="add-column"></a>Add Column 
### <a name="purpose"></a>Objectif
Le point d’extension Add Column vous permet d’écrire le code Python afin de calculer une nouvelle colonne. Le code que vous écrivez a accès à la ligne entière. Il doit renvoyer une nouvelle valeur de colonne à chaque ligne. 

### <a name="how-to-use"></a>Utilisation
Vous pouvez ajouter ce point d’extension à l’aide du bloc Add Column (Script). Ce dernier est disponible dans le menu **Transformations** de niveau supérieur, ainsi que dans le menu contextuel de la **colonne**. 

### <a name="syntax"></a>Syntaxe
Expression

```python
    math.log(row["Score"])
```

Module 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>Advanced Filter
### <a name="purpose"></a>Objectif 
Le point d’extension Advanced Filter vous permet d’écrire un filtre personnalisé. Vous avez accès à la ligne entière et votre code doit renvoyer la valeur True (inclure la ligne) ou False (exclure la ligne). 

### <a name="how-to-use"></a>Utilisation
Vous pouvez ajouter ce point d’extension à l’aide du bloc Advanced Filter (Script). Ce dernier est disponible dans le menu **Transformations** de niveau supérieur. 

### <a name="syntax"></a>Syntaxe

Expression

```python
    row["Score"] > 95
```

Module  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>Transform Dataflow
### <a name="purpose"></a>Objectif 
Le point d’extension Transform Dataflow vous permet de transformer intégralement le flux de données. Vous avez accès à un cadre de données Pandas qui contient toutes les colonnes et les lignes que vous êtes en train de traiter. Votre code doit renvoyer un cadre de données Pandas avec les nouvelles données. 

>[!NOTE]
>Dans Python, toutes les données à charger en mémoire se trouvent dans un cadre de données Pandas si cette extension est utilisée. 
>
>Dans Spark, toutes les données sont collectées sur un nœud Worker unique. Si les données sont très volumineuses, un Worker peut manquer de mémoire. Utilisez-le avec précaution.

### <a name="how-to-use"></a>Utilisation 
Vous pouvez ajouter ce point d’extension à l’aide du bloc Transform DataFlow (Script). Ce dernier est disponible dans le menu **Transformations** de niveau supérieur. 
### <a name="syntax"></a>Syntaxe 

Expression

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Module 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>Transform Partition  
### <a name="purpose"></a>Objectif 
Le point d’extension Transform Partition vous permet de transformer une partition du flux de données. Vous avez accès à un cadre de données Pandas qui contient toutes les colonnes et les lignes pour cette partition. Votre code doit renvoyer un cadre de données Pandas avec les nouvelles données. 

>[!NOTE]
>Dans Python, vous pouvez obtenir une partition unique ou plusieurs partitions, en fonction de la taille de vos données. Dans Spark, vous travaillez sur un cadre de données qui contient les données d’une partition sur un nœud Worker donné. Dans les deux cas, vous ne pouvez pas partir du principe que vous avez accès au jeu de données dans son intégralité. 


### <a name="how-to-use"></a>Utilisation
Vous pouvez ajouter ce point d’extension à l’aide du bloc Transform Partition (Script). Ce dernier est disponible dans le menu **Transformations** de niveau supérieur. 

### <a name="syntax"></a>Syntaxe 

Expression 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

Module 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>Valeurs d’erreur  
Data Preparations intègre le concept de valeurs d’erreur. 

Il est possible de rencontrer des valeurs d’erreur dans du code Python personnalisé. Ce sont des instances d’une classe Python nommée `DataPrepError`. Cette classe inclut dans un wrapper une exception Python et présente deux propriétés. Les propriétés contiennent des informations sur l’erreur qui s’est produite lors du traitement de la valeur d’origine, ainsi que la valeur d’origine. 


### <a name="datapreperror-class-definition"></a>Définition de la classe DataPrepError
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
En règle générale, la création d’une classe DataPrepError dans la structure Python Data Preparations se présente comme suit : 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>Utilisation 
Lorsque Python est exécuté à un point d’extension, il est possible de générer des erreurs DataPrepErrors en tant que valeurs renvoyées, à l’aide de la méthode de création précédente. Il est beaucoup plus probable que des erreurs DataPrepErrors surviennent lors du traitement de données à un point d’extension. À ce stade, le code Python personnalisé doit gérer une erreur DataPrepError en tant que type de données valide.

#### <a name="syntax"></a>Syntaxe 
Expression 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
Module 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
