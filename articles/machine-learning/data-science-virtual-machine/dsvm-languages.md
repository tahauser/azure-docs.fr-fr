---
title: Langages pour la machine virtuelle DSVM sur Azure | Microsoft Docs
description: Langages pour la machine virtuelle DSVM sur Azure
keywords: outils de science des données, machine virtuelle science des données, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 0c780b822e80bb8fce9ef4b1d85b445ee73ae7db
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Langages pris en charge sur la machine virtuelle DSVM 

La machine virtuelle DSVM (Data Science Virtual Machine) est fournie avec plusieurs langages et outils de développement prédéfinis qui permettent de créer vos applications d’intelligence artificielle. En voici une liste non exhaustive. 

## <a name="python-windows-server-2016-edition"></a>Python (Édition Windows Server 2016)

|    |           |
| ------------- | ------------- |
| Versions de langage prises en charge | 2.7 et 3.6 |
| Éditions DSVM prises en charge      | Windows Server 2016     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Deux environnements `conda` généraux sont créés. <br /> * L’environnement `root` situé dans `/anaconda/` est Python 3.6. <br/> * L’environnement `python2` situé dans `/anaconda/envs/python2` est Python 2.7.       |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter pour Python sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | PySpark, R, Julia      |

> [!NOTE]
> Windows Server 2016 créé avant mars 2018 contient Python 3.5 et Python 2.7. Python 2.7 est également l’environnement conda **racine**, et **py35** est l’environnement de Python 3.5. 

### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?    

* Exécution à l’invite de commandes

Ouvrez l’invite de commandes et effectuez les étapes suivantes en fonction de la version de Python à exécuter. 

```
# To run Python 2.7
activate python2
python --version

# To run Python 3.6
activate 
python --version

```
* Utilisation dans un IDE

Utilisez Python Tools pour Visual Studio (PTVS) installé avec Visual Studio Community Edition. Le seul environnement configuré automatiquement par défaut dans PTVS est Python 3.6. 

> [!NOTE]
> Pour faire pointer les outils PTVS vers Python 2.7, vous devez créer un environnement personnalisé dans PTVS. Pour définir le chemin de cet environnement dans Visual Studio Community Edition, accédez à **Outils** -> **Outils Python** -> **Environnements Python**, puis cliquez sur **+ Personnalisé**. Ensuite, définissez `c:\anaconda\envs\python2` comme emplacement et cliquez sur _Détection automatique_. 

* Utilisation dans Jupyter

Ouvrez Jupyter et cliquez sur le bouton `New` pour créer un bloc-notes. À ce stade, vous pouvez choisir le type de noyau _Python [Conda Root]_ pour l’environnement Python 3.6 et _Python [Conda env:python2]_ pour l’environnement Python 2.7. 

* Installation des packages Python

Les environnements Python par défaut sur la machine virtuelle DSVM sont des environnements généraux lisibles par tous les utilisateurs, mais seuls les administrateurs peuvent écrire / installer des packages généraux. Pour installer le package dans l’environnement général, activez l’environnement racine ou python2 en exécutant la commande `activate` en tant qu’administrateur. Vous pouvez ensuite utiliser un Gestionnaire de package comme `conda` ou `pip` pour installer ou mettre à jour les packages. 

## <a name="python-linux-and-windows-server-2012-edition"></a>Python (Linux et Édition Windows Server 2012)

|    |           |
| ------------- | ------------- |
| Versions de langage prises en charge | 2.7 et 3.5 |
| Éditions DSVM prises en charge      | Linux, Windows Server 2012    |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Deux environnements `conda` généraux sont créés. <br /> * L’environnement `root` situé dans `/anaconda/` est Python 2.7. <br/> * L’environnement `py35` situé dans `/anaconda/envs/py35` est Python 3.5.       |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter pour Python sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | PySpark, R, Julia      |
### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?    

**Linux**
* Exécution dans le terminal

Ouvrez le terminal et effectuez les étapes suivantes en fonction de la version de Python à exécuter. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Utilisation dans un IDE

Utilisez PyCharm installé avec Visual Studio Community Edition. 

* Utilisation dans Jupyter

Ouvrez Jupyter et cliquez sur le bouton `New` pour créer un bloc-notes. À ce stade, vous pouvez choisir le type de noyau _Python [Conda Root]_ pour l’environnement Python 2.7 et _Python [Conda env:py35]_ pour l’environnement Python 3.5. 

* Installation des packages Python

Les environnements Python par défaut sur la machine virtuelle DSVM sont des environnements généraux lisibles par tous les utilisateurs, mais seuls les administrateurs peuvent écrire / installer des packages généraux. Pour installer le package dans l’environnement général, activez l’environnement root ou py35 en exécutant la commande `source activate` en tant qu’administrateur ou utilisateur disposant de l’autorisation sudo. Vous pouvez ensuite utiliser un Gestionnaire de package comme `conda` ou `pip` pour installer ou mettre à jour les packages. 

**Windows 2012**
* Exécution à l’invite de commandes

Ouvrez l’invite de commandes et effectuez les étapes suivantes en fonction de la version de Python à exécuter. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Utilisation dans un IDE

Utilisez Python Tools pour Visual Studio (PTVS) installé avec Visual Studio Community Edition. Le seul environnement configuré automatiquement dans PTVS est Python 2.7. 
> [!NOTE]
> Pour faire pointer les outils PTVS vers Python 3.5, vous devez créer un environnement personnalisé dans PTVS. Pour définir le chemin de cet environnement dans Visual Studio Community Edition, accédez à **Outils** -> **Outils Python** -> **Environnements Python**, puis cliquez sur **+ Personnalisé**. Ensuite, définissez `c:\anaconda\envs\py35` comme emplacement et cliquez sur _Détection automatique_. 

* Utilisation dans Jupyter

Ouvrez Jupyter et cliquez sur le bouton `New` pour créer un bloc-notes. À ce stade, vous pouvez choisir le type de noyau _Python [Conda Root]_ pour l’environnement Python 2.7 et _Python [Conda env:py35]_ pour l’environnement Python 3.5. 

* Installation des packages Python

Les environnements Python par défaut sur la machine virtuelle DSVM sont des environnements généraux lisibles par tous les utilisateurs, mais seuls les administrateurs peuvent écrire / installer des packages généraux. Pour installer le package dans l’environnement général, activez l’environnement root ou py35 en exécutant la commande `activate` en tant qu’administrateur. Vous pouvez ensuite utiliser un Gestionnaire de package comme `conda` ou `pip` pour installer ou mettre à jour les packages. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versions de langage prises en charge | Microsoft R Open 3.x (100 % compatible avec CRAN-R)<br /> Microsoft R Server 9.x Developer Edition (plateforme R d’entreprise scalable)|
| Éditions DSVM prises en charge      | Linux, Windows     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Windows : `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux : ` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter pour R sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | SparkR, Python, Julia      |
### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?    

**Windows** :

* Exécution à l’invite de commandes

Ouvrez l’invite de commandes et tapez simplement `R`.

* Utilisation dans un IDE

Utilisez RTools for Visual Studio (RTVS) installé avec Visual Studio Community Edition ou RStudio. Ces outils sont disponibles dans le menu Démarrer ou en tant qu’icône sur le Bureau. 

* Utilisation dans Jupyter

Ouvrez Jupyter et cliquez sur le bouton `New` pour créer un bloc-notes. À ce stade, vous pouvez choisir le type de noyau _R_ pour utiliser le noyau R Jupyter (IRKernel). 

* Installation des packages R

R est installé sur la machine virtuelle DSVM dans un environnement général lisible par tous les utilisateurs, mais seuls les administrateurs peuvent écrire / installer des packages généraux. Pour installer le package dans l’environnement général, exécutez R en appliquant l’une des méthodes ci-dessus. Vous pouvez ensuite utiliser le Gestionnaire de package R `install.packages()` pour installer ou mettre à jour les packages. 

**Linux** :

* Exécution dans le terminal

Ouvrez le terminal et exécutez simplement `R`.  

* Utilisation dans un IDE

Utilisez RStudio installé sur la machine virtuelle DSVM Linux.  

* Utilisation dans Jupyter

Ouvrez Jupyter et cliquez sur le bouton `New` pour créer un bloc-notes. À ce stade, vous pouvez choisir le type de noyau _R_ pour utiliser le noyau R Jupyter (IRKernel). 

* Installation des packages R

R est installé sur la machine virtuelle DSVM dans un environnement général lisible par tous les utilisateurs, mais seuls les administrateurs peuvent écrire / installer des packages généraux. Pour installer le package dans l’environnement général, exécutez R en appliquant l’une des méthodes ci-dessus. Vous pouvez ensuite utiliser le Gestionnaire de package R `install.packages()` pour installer ou mettre à jour les packages. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Versions de langage prises en charge | 0,6 |
| Éditions DSVM prises en charge      | Linux, Windows     |
| Comment est-il configuré / installé sur la machine virtuelle DSVM ?  | Windows : installé dans `C:\JuliaPro-VERSION`<br /> Linux : Installé dans `/opt/JuliaPro-VERSION`    |
| Liens vers des exemples      | Des exemples de bloc-notes Jupyter pour Julia sont inclus.     |
| Outils connexes sur la machine virtuelle DSVM      | Python, R      |
### <a name="how-to-use--run-it"></a>Comment l’utiliser/l’exécuter ?    

**Windows** :

* Exécution à l’invite de commandes

Ouvrez l’invite de commandes et exécutez simplement `julia`. 
* Utilisation dans un IDE

Utilisez `Juno`, l’IDE Julia installé sur la machine virtuelle DSVM et disponible en tant que raccourci sur le Bureau.

* Utilisation dans Jupyter

Ouvrez Jupyter et cliquez sur le bouton `New` pour créer un bloc-notes. À ce stade, vous pouvez choisir le type de noyau `Julia VERSION`. 

* Installation des packages Julia

L’emplacement Julia par défaut est un environnement général lisible par tous les utilisateurs, mais seuls les administrateurs peuvent écrire / installer des packages généraux. Pour installer le package dans l’environnement général, exécutez Julia en appliquant l’une des méthodes ci-dessus. Vous pouvez ensuite utiliser un Gestionnaire de package Julia comme `Pkg.add()` pour installer ou mettre à jour les packages. 


**Linux** :
* Exécution dans le terminal

Ouvrez le terminal et exécutez simplement `julia`. 
* Utilisation dans un IDE

Utilisez `Juno`, l’IDE Julia installé sur la machine virtuelle DSVM et disponible en tant que raccourci dans le menu Application.

* Utilisation dans Jupyter

Ouvrez Jupyter et cliquez sur le bouton `New` pour créer un bloc-notes. À ce stade, vous pouvez choisir le type de noyau `Julia VERSION`. 

* Installation des packages Julia

L’emplacement Julia par défaut est un environnement général lisible par tous les utilisateurs, mais seuls les administrateurs peuvent écrire / installer des packages généraux. Pour installer le package dans l’environnement général, exécutez Julia en appliquant l’une des méthodes ci-dessus. Vous pouvez ensuite utiliser un Gestionnaire de package Julia comme `Pkg.add()` pour installer ou mettre à jour les packages. 

## <a name="other-languages"></a>Autres langages

**C#** : disponible sur Windows et accessible par le biais de Visual Studio Community Edition ou à une `Developer Command Prompt for Visual Studio` où il suffit d’exécuter la commande `csc`. 

**Java** : OpenJDK est disponible sur les éditions Linux et Windows de la machine virtuelle DSVM, et défini sur le chemin. Vous pouvez taper la commande `javac` ou `java` à l’invite de commandes dans Windows ou dans l’interpréteur de commandes Bash sur Linux pour utiliser Java. 

**node.js** : node.js est disponible sur les éditions Linux et Windows de la machine virtuelle DSVM, et défini sur le chemin. Vous pouvez taper la commande `node` ou `npm` à l’invite de commandes dans Windows ou dans l’interpréteur de commandes Bash sur Linux pour accéder à node.js. Sur Windows, l’extension Node.js Tools pour Visual Studio est installée afin de fournir un IDE graphique pour le développement de votre application node.js. 

**F#** : disponible sur Windows et accessible par le biais de Visual Studio Community Edition ou à une `Developer Command Prompt for Visual Studio` où il suffit d’exécuter la commande `fsc`. 


