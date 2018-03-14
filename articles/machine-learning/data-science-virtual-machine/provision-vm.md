---
title: Provisionner la machine virtuelle DSVM Windows sur Azure | Microsoft Docs
description: "Configurez et créez une machine virtuelle pour la science des données sur Microsoft Azure pour vos besoins d’analyse et d’apprentissage automatique."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 6f933c75d4829e3b2c5198aeee324f15490d8a93
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>Provisionner la machine virtuelle DSVM Windows sur Azure
La machine virtuelle pour la science des données Microsoft est une image de machine virtuelle Windows Azure préalablement installée et configurée avec plusieurs outils populaires couramment utilisés dans le cadre de l’analyse de données et de l’apprentissage automatique. Elle intègre les outils suivants :

* [Azure Machine Learning](../preview/index.yml) Workbench
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) - Édition Développeur
* Distribution Anaconda Python
* Jupyter Notebook (avec noyaux R, Python, PySpark)
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* Instance autonome Spark pour le développement local et le test
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* Outils Machine Learning et Analyse des données
  * Frameworks d’apprentissage approfondi : un ensemble complet de frameworks d’intelligence artificielle, notamment [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), [TensorFlow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), mxNet, Keras est inclus sur la machine virtuelle.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): système de Machine Learning rapide prenant en charge des techniques (apprentissage en ligne, hachage, allreduce, réductions, learning2search, actif et interactif).
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): outil offrant une implémentation rapide et précise des arborescences optimisées.
  * [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily) : outil graphique qui facilite la prise en main de l’analyse des données et de l’apprentissage automatique dans R. Il inclut l’exploration de données basées sur une interface utilisateur graphique et la modélisation avec la génération de code R automatique.
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/) : logiciel Machine Learning et d’exploration des données visuelle dans Java.
  * [Apache Drill](https://drill.apache.org/) : moteur de requêtes SQL sans schéma pour Hadoop, NoSQL et le stockage cloud.  Prend en charge les interfaces ODBC et JDBC pour activer l’interrogation de NoSQL et des fichiers à partir d’outils décisionnels standard tels que Power BI, Excel, Tableau.
* Bibliothèques dans les langages R et Python à utiliser dans Azure Machine Learning et d’autres services Azure
* Git incluant Git Bash pour travailler avec les référentiels de code source, notamment GitHub, Visual Studio Team Services
* Ports Windows de plusieurs utilitaires de ligne de commande Linux populaires (notamment awk, sed, perl, grep, find, wget, curl, etc.) accessibles via l’invite de commandes. 

La science des données consiste à itérer sur une séquence de tâches :

1. Recherche, chargement et traitement des données
2. Création et test des modèles
3. Déploiement des modèles à des fins d’utilisation dans des applications intelligentes

Les scientifiques de données utilisent différents outils pour effectuer ces tâches. La recherche des versions adéquates des logiciels, puis leur téléchargement et leur installation peuvent prendre un certain temps. La machine virtuelle pour la science des données Microsoft peut faciliter cette tâche en fournissant une image prête à l’emploi qui peut être approvisionnée sur Azure avec tous les outils populaires préalablement installés et configurés. 

Le renvoi de la machine virtuelle pour la science des données démarre votre projet d’analyse. Elle vous permet de travailler sur des tâches basées sur différents langages, notamment R, Python, SQL et C#. Visual Studio propose un IDE qui vous permet de développer et tester très simplement votre code. Le SDK Azure inclus dans la machine virtuelle vous permet de créer des applications à l’aide de divers services disponibles sur la plateforme cloud de Microsoft. 

Cette image de machine virtuelle de science des données ne génère pas de frais. Vous payez uniquement les frais d’utilisation Azure en fonction de la taille de la machine virtuelle approvisionnée. Vous trouverez plus d’informations sur les frais de calcul dans la section sur les détails de tarification de la page [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice) (Machine virtuelle pour la science des données). 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>Autres versions de la machine virtuelle pour la science des données
Une image [Ubuntu](dsvm-ubuntu-intro.md) est également disponible, avec de nombreux outils similaires et quelques autres cadres de formation approfondis. Une image [CentOS](linux-dsvm-intro.md) est également disponible. Nous vous proposons également une [édition Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm) de la machine virtuelle de science des données, bien que certains outils sont disponibles uniquement sur l’édition Windows Server 2016.  Sinon, cet article s’applique également à l’édition Windows Server 2012.

## <a name="prerequisites"></a>Prérequis
Avant de pouvoir créer une machine virtuelle de science des données Microsoft, vous devez disposer des éléments suivants :

* **Un abonnement Azure**: pour obtenir un abonnement, consultez la page [Obtenir une version d’évaluation gratuite d’Azure](http://azure.com/free).


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Création d’une machine virtuelle pour la science des données
Pour créer une instance de la machine virtuelle de sciences des données Microsoft, suivez ces étapes :

1. Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016).
2. Sélectionnez le bouton **Créer** au bas de l’écran pour accéder à un Assistant.![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. L’Assistant utilisé pour créer la machine virtuelle pour la science des données Microsoft nécessite les **entrées** de chacune des **quatre étapes** énumérées à droite de cette figure. Voici les entrées nécessaires à la configuration de chacune de ces étapes :
   
   1. **Concepts de base**
      
      1. **Name**(Nom) : nom du serveur Data Science que vous créez.
      2. **Type de disque de machine virtuelle** : choisissez SSD ou HDD. Pour le GPU (série NC), choisissez **HDD** comme type de disque. 
      3. **Nom d’utilisateur**: identifiant de connexion du compte administrateur.
      4. **Mot de passe**: mot de passe du compte administrateur.
      5. **Subscription**(Abonnement) : si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.
      6. **Resource Group**(Groupe de ressources) : vous pouvez créer un nouveau groupe ou utiliser un groupe existant.
      7. **Location**(Emplacement) : sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau
   2. **Taille**: sélectionnez l’un des types de serveur qui répond à vos exigences fonctionnelles et à vos contraintes de coût. Sélectionnez « Afficher tout » pour obtenir d’autres choix de tailles de machines virtuelles
   3. **Paramètres**:
      
      1. **Utiliser des disques gérés** : choisissez Géré si vous voulez confier à Azure la gestion des disques de la machine virtuelle.  Sinon, vous devez spécifier un compte de stockage nouveau ou existant. 
      2. **Autres paramètres**: généralement, vous utilisez simplement la valeur par défaut. Si vous envisagez de ne pas utiliser les valeurs par défaut, survolez le lien d’informations pour obtenir de l’aide sur les différents champs.
    a. **Résumé**: vérifiez que toutes les informations que vous avez saisies sont correctes puis cliquez sur **Créer**. **REMARQUE** : la machine virtuelle n'est pas assortie de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l'étape **Taille**. 

> [!NOTE]
> L’approvisionnement prend environ 10 à 20 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Accès à une machine virtuelle pour la science des données
Une fois la machine virtuelle créée, vous pouvez vous y connecter à l’aide du bureau distant en utilisant les informations d’identification du compte administrateur que vous avez configurées dans la section **Paramètres de base** précédente. 

Une fois votre machine virtuelle créée et approvisionnée, vous pouvez commencer à utiliser les outils qui y sont installés et configurés. Pour la plupart des outils, vous disposez d'icônes de bureau et de vignettes dans le menu de démarrage. 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Outils installés sur la machine virtuelle de science des données

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Machine Learning Workbench est une application de bureau et une interface de ligne de commande. Workbench intègre une préparation des données qui apprend au fur et à mesure vos étapes de préparation des données. Il fournit également une gestion de projets, un historique des exécutions et une intégration de notebooks pour booster votre productivité. Vous pouvez tirer parti des meilleures infrastructures open source, telles que TensorFlow, Cognitive Toolkit, Spark ML et scikit-learn pour développer vos modèles. Sur la DSVM, nous fournissons une icône de bureau (InstallAMLFromLocal) pour extraire localement le workbench Azure Machine Learning dans le répertoire %LOCALAPPDATA% de chaque utilisateur. Chaque utilisateur qui a besoin d’utiliser le Workbench doit double-cliquer une seule fois sur l’icône du bureau InstallAMLFromLocal afin d’installer son instance du Workbench. Azure Machine Learning crée et utilise également un environnement Python par l’utilisateur qui est extrait dans %LOCALAPPDATA%\amlworkbench\python.

### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
Si vous souhaitez utiliser des bibliothèques d’entreprise Microsoft avec une solution R évolutive ou Python pour vos analyses, l’édition Microsoft ML Server Developer (anciennement Microsoft R Server) est installée sur la machine virtuelle. Microsoft ML Server est une plate-forme d’analyse de classe entreprise déployable à grande échelle, disponible pour R et Python, évolutive, soutenue commercialement et sécurisée. Prenant en charge les statistiques Big Data, la modélisation prédictive et des fonctionnalités Machine Learning, ML Server prend en charge la totalité de l’analyse : exploration, analyse, visualisation et modélisation. En utilisant et en étendant R et Python Open Source, Microsoft ML Server est entièrement compatible avec les scripts R/Python, les fonctions et les packages CRAN R/pip/Conda pour analyser les données à l'échelle de l'entreprise. Il traite également les limitations en mémoire de R Open Source en ajoutant le traitement des données en parallèle et mémorisé en bloc. Cela vous permet d’exécuter des analyses de données plus volumineuses que ce que peut contenir la mémoire principale.  Visual Studio Community Edition inclus sur la machine virtuelle contient les outils R pour Visual Studio et les outils Python pour l’extension Visual Studio qui fournit un IDE complet pour travailler avec R ou Python. Nous fournissons également d’autres environnements IDE comme [RStudio](http://www.rstudio.com) et [l’édition PyCharm Community](https://www.jetbrains.com/pycharm/) sur la machine virtuelle. 

### <a name="python"></a>Python
Pour un développement basé sur Python, les versions 2.7 et 3.5 de la distribution Anaconda Python ont été installées. Cette distribution contient le langage Python de base avec environ 300 packages de mathématiques, d’ingénierie et d’analyse de données figurant parmi les plus populaires. Vous pouvez utiliser les outils Python pour Visual Studio (PTVS) installés dans l’édition Visual Studio 2015 Community ou l’un des IDE fournis avec Anaconda comme IDLE ou Spyder. Pour lancer l’un de ces IDE, vous pouvez effectuer une recherche dans la barre de recherche (**Win** + **S**).

> [!NOTE]
> Pour pointer les Python Tools pour Visual Studio sur Anaconda Python 2.7 et 3.5, vous devez créer des environnements personnalisés pour chaque version. Pour définir les chemins d’accès à ces environnements dans Visual Studio 2015 Community Edition, accédez à **Outils** -> **Outils Python** -> **Environnements Python**, puis cliquez sur **+ personnalisé**. 
> 
> 

Anaconda Python 2.7 est installé sous C:\Anaconda et Anaconda Python 3.5 est installé sous c:\Anaconda\envs\py35. Pour obtenir des instructions détaillées, consultez la [documentation de PTVS](/visualstudio/python/python-environments.md#selecting-and-installing-python-interpreters) . 

### <a name="jupyter-notebook"></a>Jupyter Notebook
La distribution Anaconda est également fournie avec un serveur Jupyter Notebook, un environnement conçu pour le partage de code et d’analyses. Un serveur Jupyter Notebook a été préconfiguré avec Python 2.7, Python 3.5, PySpark, Julia et les noyaux R. Une icône de bureau nommée « Jupyter Notebook » permet de démarrer le serveur Jupyter et de lancer le navigateur pour accéder au serveur Notebook. 

> [!NOTE]
> Si vous recevez des avertissements relatifs au certificat, vous pouvez les ignorer. 
> 
> 

Nous avons inclus plusieurs exemples de notebooks dans Python et R. Les exemples Jupyter Notebook vous montrent comment utiliser Microsoft ML Server, SQL Server ML Services (analytique dans la base de données), Python, Microsoft Cognitive ToolKit, Tensorflow et d’autres technologies Azure, lorsque vous accédez à Jupyter. Après vous être authentifié auprès du serveur Jupyter Notebook avec le mot de passe créé à l’étape précédente, vous pouvez voir le lien vers les exemples sur la page d’accueil du notebook. 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community Edition
Visual Studio Community Edition est installé sur la machine virtuelle. Vous pouvez utiliser cette version gratuite de l’IDE populaire de Microsoft à des fins d’évaluation et dans le cadre de projets en petites équipes. Vous pouvez consulter les termes du contrat de licence [ici](https://www.visualstudio.com/support/legal/mt171547).  Ouvrez Visual Studio en double-cliquant sur l’icône du bureau ou via le menu **Démarrer** . Vous pouvez également lancer une recherche de programmes avec **Win** + **S** et en entrant « Visual Studio ». Là, vous pouvez créer des projets dans des langages tels que C#, Python, R, node.js. Des plug-ins sont également installés pour faciliter l’utilisation des services Azure tels que Azure Data Catalog, Azure HDInsight (Hadoop, Spark) et Azure Data Lake. 

> [!NOTE]
> Il est possible que vous receviez un message indiquant que votre période d’évaluation a expiré. Saisissez vos informations d’identification de compte Microsoft ou créez un compte gratuit pour accéder à Visual Studio Community Edition. 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition
Une version de développement de SQL Server 2017 avec ML Services pour exécuter l’analyse dans la base de données est fournie sur la machine virtuelle dans R ou Python. ML Services fournit une plateforme pour développer et déployer des applications intelligentes. Pour créer des modèles et générer des prédictions à l’aide de vos données SQL Server, vous pouvez utiliser ces langages riches et puissants, et les nombreux packages issus de la Communauté. Vous pouvez conserver les analyses à proximité des données, car ML Services (dans la base de données) intègre à la fois les langages R et Python à SQL Server. Cela élimine les coûts et les risques de sécurité liés au déplacement des données.

> [!NOTE]
> L’édition développeur de SQL Server peut uniquement être utilisée à des fins de test et de développement. Vous devez posséder une licence pour l’exécuter en production. 
> 
> 

Vous pouvez accéder à SQL Server en lançant **SQL Server Management Studio**. Le nom de votre machine virtuelle est celui du serveur. Utilisez l’authentification Windows une fois connecté à Windows en tant qu’administrateur. Dans SQL Server Management Studio, vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL. 

Pour activer l’analyse dans la base de données à l’aide de SQL ML Services, exécutez la commande suivante en tant qu’action unique dans SQL Server Management Studio après vous être connecté en tant qu’administrateur du serveur. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
Plusieurs outils Azure sont installés sur la machine virtuelle :

* Il existe un raccourci bureau pour accéder à la documentation du Kit de développement logiciel (SDK) Azure. 
* **AzCopy**: permet de déplacer des données vers et à partir de votre compte de stockage Microsoft Azure. Il suffit de taper **Azcopy** dans une invite de commandes pour afficher l’utilisation. 
* **Explorateur de stockage Microsoft Azure**: permet de parcourir les objets que vous avez stockés dans votre compte de stockage Azure et de transférer des données vers et à partir du stockage Azure. Vous pouvez taper **Explorateur de stockage** dans la recherche ou utiliser le menu Démarrer de Windows pour accéder à cet outil. 
* **Adlcopy**: permet de déplacer des données vers Azure Data Lake. Il suffit de taper **adlcopy** dans une invite de commandes pour afficher l’utilisation. 
* **dtui** : permet de déplacer des données en direction et en provenance d’Azure Cosmos DB, une base de données NoSQL sur le cloud. Il suffit de taper **dtui** dans une invite de commandes. 
* **Azure Data Factory Integration Runtime** : permet le déplacement des données entre des sources de données locales et le cloud. Elle est utilisée dans les outils tels que Azure Data Factory. 
* **Microsoft Azure PowerShell** : un outil qui permet de gérer vos ressources Azure dans le langage de script PowerShell est également installé sur votre machine virtuelle. 

### <a name="power-bi"></a>Power BI
**Power BI Desktop** a été installé pour vous aider à créer des tableaux de bord et des visualisations attrayantes. Utilisez cet outil pour extraire des données de différentes sources, créer vos tableaux de bord et vos rapports, puis les publier sur le cloud. Pour plus d'informations, consultez le site de [Power BI](http://powerbi.microsoft.com) . Vous pouvez trouver le bureau Power BI dans le menu Démarrer. 

> [!NOTE]
> Vous devez disposer d’un compte Office 365 pour accéder à Power BI. 
> 
> 

## <a name="additional-microsoft-development-tools"></a>Autres outils de développement Microsoft
Le programme [**Microsoft Web Platform Installer**](https://www.microsoft.com/web/downloads/platform.aspx) vous permet de découvrir et de télécharger d’autres outils de développement Microsoft. Il existe également un raccourci vers l’outil fourni sur le bureau de la machine virtuelle pour la science des données.  

## <a name="important-directories-on-the-vm"></a>Répertoires importants sur la machine virtuelle
| Item | Répertoire |
| --- | --- |
| Configurations de serveur de Jupyter Notebook |C:\ProgramData\jupyter |
| Répertoire des exemples de Jupyter Notebook |c:\dsvm\notebooks |
| Autres exemples |c:\dsvm\samples |
| Anaconda (valeur par défaut : Python 2.7) |c:\Anaconda |
| Environnement Anaconda Python 3.5 |c:\Anaconda\envs\py35 |
| Microsoft ML Server Standalone Python  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Instance R par défaut (ML Server Standalone) |C:\Program Files\Microsoft\ML Server\R_SERVER |
| Répertoire de l’instance de base de données SQL ML Services |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench (par utilisateur) | %localappdata%\amlworkbench | 
| Outils divers |c:\dsvm\tools |

> [!NOTE]
> Les instances de la machine virtuelle pour la science des données Microsoft créées avant 1.5.0 (avant le 3 septembre 2016) utilisaient une structure de répertoire légèrement différente à celle spécifiée dans la table précédente. 
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Voici quelques étapes supplémentaires pour poursuivre votre travail d'apprentissage et d'exploration. 

* Explorez les différents outils de science des données sur la machine virtuelle pour la science des données en cliquant sur le menu Démarrer et en consultant les outils répertoriés dans le menu.
* Pour en savoir plus sur Azure Machine Learning Services et le Workbench, visitez la [page de démarrage rapide et des didacticiels](../preview/index.yml) du produit. 
* Accédez à **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts** pour obtenir des exemples utilisant la bibliothèque RevoScaleR dans R qui prend en charge l’analyse des données à l’échelle de l’entreprise.  
* Lisez l’article intitulé [Dix choses que vous pouvez effectuer sur la machine virtuelle pour la science des données](http://aka.ms/dsvmtenthings)
* Découvrez comment créer des solutions analytiques de bout en bout systématiquement à l’aide du [processus TDSP (Team Data Science Process)](../team-data-science-process/index.yml).
* Visitez [Azure AI Gallery](http://gallery.cortanaintelligence.com) pour obtenir des exemples d’apprentissage automatique et d’analytique des données qui utilisent Azure Machine Learning et des services de données connexes sur Azure. Nous avons également inclus une icône dans le menu **Démarrer** et sur le bureau de la machine virtuelle pour accéder à cette galerie.

