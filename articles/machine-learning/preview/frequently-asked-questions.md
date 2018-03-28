---
title: FAQ sur la préversion d’Azure Machine Learning 2017 | Microsoft Docs
description: Cet article contient des questions fréquemment posées et les réponses à celles-ci concernant les fonctionnalités en préversion d’Azure Machine Learning
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ms.openlocfilehash: 6146e1ce98144a05bef2ca29705f451aa3f5fb50
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Forum aux questions sur Azure Machine Learning

## <a name="general-product-questions"></a>Questions générales relatives au produit

**Qu'est-ce que Microsoft Azure Machine Learning ?**

Azure Machine Learning est un service Azure entièrement géré qui permet de créer, tester, gérer et déployer des modèles d’apprentissage automatique et d’intelligence artificielle. Nos nouveaux services et notre application téléchargeable offrent une approche de type Code First, qui s’appuie sur le cloud, les capacités locales et la périphérie pour former, déployer, gérer et surveiller des modèles avec puissance, vitesse et flexibilité. Azure Machine Learning Studio offre également un environnement visuel de création par glisser-déplacer basé sur navigateur où aucun codage n’est requis. 

**Dans quelles régions les nouveaux services sont-ils disponibles ?**

Si vous souhaitez connaître les régions Azure prises en charge pour les services d’expérimentation et de gestion des modèles sous Azure Machine Learning, consultez la page [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

D’autres régions seront ajoutées au fur et à mesure du développement du produit.  Vous pouvez nous aider à hiérarchiser les régions de déploiement via la page des [Commentaires sur Azure Machine Learning](https://feedback.azure.com/forums/257792-machine-learning). 

**Quels sont les autres services Azure requis ?**

Stockage Blob Azure et Azure Container Registry sont utilisés par Azure Machine Learning. En outre, vous devez approvisionner des ressources de calcul comme une machine virtuelle de science des données ou un cluster HDInsight. Le calcul et l’hébergement sont également requis lorsque vous déployez vos services Web, tel que [Azure Container Service](https://docs.microsoft.com/azure/aks).

**Quelle est la relation entre Azure Machine Learning et Machine Learning Services dans SQL Server 2017 ?**   

Machine Learning Services dans SQL Server 2017 est une plateforme extensible évolutive pour l’intégration de tâches d’apprentissage automatique (Machine Learning) dans des flux de travail de base de données. Elle convient parfaitement quand une solution locale est requise, par exemple, lorsque le déplacement de données est coûteux ou intenable. En revanche, les charges de travail dans le cloud ou hybrides sont parfaitement adaptées à nos nouveaux services Azure. 

**Prenez-vous en charge les langages Python et R ? Qu’en est-il d’autres langages de programmation tels que C++ ?**

Nous prenons en charge actuellement uniquement Python. Nous travaillons sur l’intégration de R et prévoyons qu’elle sera bientôt disponible. 

**Quelle est la relation entre Azure Machine Learning et Microsoft Machine Learning pour Spark ?**

Microsoft Machine Learning pour Spark (MMLSpark) fournit des outils de science des données et d’apprentissage profond pour Apache Spark, mettant l’accent sur la productivité, la facilité d’expérimentation et des algorithmes de pointe. MMLSpark intègre les pipelines de Machine Learning Spark avec Microsoft Cognitive Toolkit et OpenCV. Vous pouvez créer des modèles prédictifs et analytiques puissants et hautement évolutifs pour les données d’image et de texte. MMLSpark est disponible sous licence open source, et inclus dans Azure Machine Learning Workbench en tant qu’ensemble de modèles et d’algorithmes consommables. Pour plus d’informations sur MMLSpark, consultez la documentation du produit. 

**Quelles versions de Spark les nouveaux outils et services prennent-ils en charge ?**

Actuellement, Workbench inclut et prend en charge MMLSpark version 0.8, qui est compatible avec Apache Spark 2.1. Vous pouvez également utiliser un image Docker compatible GPU de MMLSpark 0.8 sur des machines virtuelles Linux.

## <a name="experimentation-service"></a>Service d’expérimentation

**Qu’est-ce que le Service d’expérimentation d’Azure Machine Learning ?**

Le Service d’expérimentation est un service Azure géré qui élève le niveau de l’expérimentation de l’apprentissage automatique. Il permet d’élaborer des expériences localement ou dans le cloud. Créez rapidement un prototype sur un ordinateur de bureau, puis passez à l’échelle de machines virtuelles ou de clusters Spark. Des machines virtuelles Azure disposant de la dernière technologie GPU vous permettent de vous engager rapidement et efficacement dans un apprentissage profond. Nous avons également inclus une intégration profonde avec Git, qui vous permet de vous connecter facilement à des flux de travail existants à des fins de traçabilité des codes, de configuration et de collaboration. 

**Comment le Service d’expérimentation est-il facturé ?**

Les deux premiers utilisateurs associés à votre Service d’expérimentation Azure Machine Learning sont gratuits. Les utilisateurs supplémentaires sont facturés au taux Préversion publique de 50 USD par mois. Pour plus d’informations sur la tarification et la facturation, consultez la page Tarification.

**Serai-je facturé sur la base du nombre d’expériences menées ?**

Non. Le Service d’expérimentation vous autorise à faire autant d’expériences que nécessaire, et facture uniquement sur la base du nombre d’utilisateurs. Les ressources de calcul liées à l’expérimentation sont facturées séparément. Nous vous encourageons à effectuer plusieurs expériences afin de trouver le modèle le mieux adapté à votre solution.   

**Quels types spécifiques de ressources de calcul et de stockage puis-je utiliser ?**

Le service d’expérimentation peut exécuter vos expériences sur des ordinateurs locaux (directs ou basés sur Docker), sur [Machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/) et sur [HDInsight](https://azure.microsoft.com/services/hdinsight/). Le service accède également à un compte de [Stockage Azure](https://azure.microsoft.com/services/storage/) pour stocker les résultats d’exécution, et peut utiliser un compte [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) pour la gestion de version et le stockage Git. Notez que vous êtes facturé séparément pour les ressources de calcul et de stockage consommées, conformément à leur tarifications respectives.  


## <a name="model-management"></a>Gestion des modèles

**Qu’est-ce que la Gestion des modèles Machine Learning ?**

La Gestion des modèles Machine Learning est un service Azure géré qui permet à des équipes de chercheurs de données et de DevOps de déployer des modèles prédictifs de manière fiable dans un vaste éventail d’environnements. Des référentiels GIT et des conteneurs Docker assurent la traçabilité et la répétabilité. Vous pouvez déployer des modèles de manière fiable dans le cloud, localement ou en périphérie. Une fois un modèle en production, vous pouvez gérer ses performances, puis relancer l’apprentissage de manière proactive en cas de dégradation de celles-ci. Vous pouvez déployer des modèles sur des ordinateurs locaux, sur des [Machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), sur des clusters Spark sur [HDInsight](https://azure.microsoft.com/services/hdinsight/) ou sur des clusters [Azure Container Service](https://azure.microsoft.com/services/container-service/) orchestrés par Kubernetes.  

**Qu’est un « modèle » ?**

Un modèle est le résultat d’une expérimentation qui a été promue pour la gestion des modèles. Un modèle inscrit dans le compte hébergeur est imputé sur votre plan, y compris s’il s’agit d’un modèle mis à jour via un réapprentissage ou une itération de version.

**Qu’est-ce qu’un « modèle géré » ?**

Un modèle est le résultat d’un processus d’apprentissage. Il consiste en l’application d’un algorithme d’apprentissage automatique à des données d'apprentissage. Le Gestion des modèles vous permet de déployer des modèles en tant que services web, de gérer différentes versions de vos modèles, ainsi que de surveiller les performances et métriques de ceux-ci. Des modèles gérés sont des modèles qui ont été inscrits auprès d’un compte de Gestion des modèles Azure Machine Learning. Prenons l’exemple d’un scénario où vous tentez de prévoir des ventes. Pendant la phase d’expérimentation, vous générez de nombreux modèles à l’aide de différents jeux de données ou algorithmes. Si vous avez généré quatre modèles de précisions variables, vous pouvez choisir d’inscrire uniquement le modèle dont la précision est la plus élevée. Le modèle inscrit devient votre premier modèle géré.
 
**Qu’est-ce qu’un « déploiement » ?**

Le Gestion des modèles vous permet de déployer des modèles en tant que conteneurs de services web empaquetés dans Azure. Vous pouvez appeler ces services web à l’aide d’API REST. Chaque service web est compté comme un déploiement unique, et le nombre total des déploiements actifs est imputé sur votre plan. En prenant l’exemple de prévision des ventes, lorsque vous déployez votre modèle le plus performant, votre plan est incrémenté d’un déploiement. Ensuite, si vous effectuez une réapprentissage et déployez une nouvelle version, vous avez deux déploiements. Si vous déterminez que le modèle plus récent est meilleur et supprimez l’original, le nombre de déploiements est décrémenté d’une unité.  

**Quelles ressources de calcul spécifiques sont disponibles pour mes déploiements ?** 

La Gestion des modèles peut exécuter vos déploiements en tant que conteneurs Docker inscrits auprès d’[Azure Container Service](https://azure.microsoft.com/services/container-service/) ou en tant que [Machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/), ou encore sur des ordinateurs locaux. D’autres cibles de déploiement seront ajoutées sous peu. Notez que vous êtes facturé séparément pour les ressources de calcul consommées, conformément à leur tarification.     

**Puis-je utiliser le service Gestion des modèles Azure Machine Learning pour déployer des modèles créés à l’aide d’outils autres que le Service d’expérimentation ?**

Vous obtenez un résultat optimal lorsque vous déployez des modèles créés à l’aide du Service d’expérimentation. Toutefois, vous pouvez déployer des modèles créés à l’aide d’autres infrastructures et outils. Nous prenons en charge divers modèles, dont MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-learn, Keras, etc. 

**Puis-je utiliser mes propres ressources Azure ?**

Oui, le Service d’expérimentation et la Gestion des modèles fonctionnent conjointement avec plusieurs banques de données, charges de travail de calcul et autres services Azure. Pour plus d’informations sur les services Azure requis, voir notre documentation technique.

**Prenez-vous en charge les scénarios de déploiement local et cloud ?**

Oui. Nous prenons en charge les scénarios de déploiement local et sur le cloud via des conteneurs Docker. Les cibles d’exécution locale sont les suivantes : déploiements Docker à nœud unique, [Microsoft SQL Server avec Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop ou Spark. Nous prenons également en charge les déploiements cloud via Docker, à savoir : déploiements en cluster via Azure Container Service et des clusters Kubernetes, HDInsight ou Spark. Des scénarios de périphérie sont pris en charge via des conteneurs Docker et Azure IOT Edge. 

**Puis-je exécuter une image Docker créée à l’aide de l’interface de ligne de commande d’Azure Machine Learning sur un autre ordinateur hôte ?**

Oui. Vous pouvez utiliser l’image en tant que service web sur tout hôte Docker, pour autant que celui-ci dispose de suffisamment de ressources de calcul pour héberger l’image Docker.

**Prenez-vous en charge le réapprentissage de modèles déployés ?**

Oui, vous pouvez déployer plusieurs versions d’un même modèle. La Gestion des modèles prend en charge les mises à jour de service pour l’ensemble des modèles et images mis à jour.

## <a name="workbench"></a>Workbench

**Qu’est-ce qu’Azure Machine Learning Workbench ?**

Azure Machine Learning Workbench est une application compagnon conçue pour les chercheurs de données professionnels. Disponible pour Windows et Mac, Azure Machine Learning Workbench permet d’avoir une vue d’ensemble, ainsi que d’assurer la gestion et le contrôle des solutions d’apprentissage automatique. Azure Machine Learning Workbench inclut l’accès à des infrastructures d’intelligence artificielle de pointe de Microsoft et de la communauté Open Source. Nous avons inclus les kits de ressources de science des données les plus populaires, dont TensorFlow, Microsoft Cognitive Toolkit, Spark ML, scikit-learn, et bien plus encore. Nous avons également permis l’intégration avec des IDE de science des données populaires tels que Jupyter Notebook, PyCharm et Visual Studio Code. Azure Machine Learning Workbench offre des fonctionnalités intégrées de préparation des données permettant d’échantillonner, de comprendre et de préparer rapidement des données, structurées ou non. Notre nouvel outil de préparation de données, appelé [PROSE](https://microsoft.github.io/prose/), s’appuie sur une technologie de pointe de Microsoft Research.  

**Workbench est-il un IDE ?**

Non. Azure Machine Learning Workbench a été conçu en tant qu’application compagnon d’IDE populaires tels que Jupyter Notebook, Visual Studio Code et PyCharm, mais il ne s’agit pas d’un IDE entièrement fonctionnel. Azure Machine Learning Workbench offre certaines fonctionnalités d’édition de texte de base, mais les fonctionnalités de débogage, IntelliSense et d’autres fonctionnalités IDE couramment utilisée ne sont pas prises en charge. Pour développer, modifier et déboguer du code, nous vous recommandons d’utiliser votre IDE de prédilection. Vous pouvez également souhaiter essayer [Outils Visual Studio Code pour l’IA](https://www.visualstudio.com/downloads/ai-tools-vscode).

**L’utilisation d’Azure Machine Learning Workbench a-t-elle un coût ?**

Non. Azure Machine Learning Workbench est une application gratuite. Vous pouvez la télécharger sur le nombre de machines et pour le nombre d’utilisateurs de votre choix. Pour utiliser Azure Machine Learning Workbench, vous devez avoir un compte Expérimentation. .  

**Prenez-vous en charge les fonctionnalités de ligne de commande ?**

Oui, Azure Machine Learning offre une interface de ligne de commande complète. L’interface de ligne de commande de Machine Learning est installée par défaut avec Azure Machine Learning Workbench. Elle est également intégrée à la machine virtuelle de science des données Linux sur Azure, et sera intégrée à [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)


**Puis-je utiliser Jupyter Notebook avec Workbench ?**

Oui. Vous pouvez exécuter Jupyter Notebook dans Workbench, celui-ci étant l’application d'hébergement de client, tout comme vous utiliseriez un navigateur en tant que client. 

**Quels noyaux Jupyter Notebook sont pris en charge ?**

La version actuelle de Jupyter incluse dans Workbench lance un noyau Python 3 et un noyau supplémentaire pour chaque fichier « runconfig » dans votre dossier aml_config. Les configurations prises en charge sont les suivantes :
- Python local ;
- Python dans Docker local ou distant.

## <a name="data-formats-and-capabilities"></a>Formats de données et fonctionnalités

**Quels formats de fichiers sont actuellement pris en charge pour l’ingestion de données dans Workbench ?**

Les outils de préparation des données dans Workbench prennent actuellement en charge l’ingestion de données aux formats suivants : 
- Fichiers dans lesquels les valeurs dont délimitées, tels que CSV, TSV, etc.  
- Fichiers de type largeur fixe
- Fichiers de texte brut
- Excel (.xls/xlsx)
- Fichiers JSON
- Fichiers Parquet 
- Fichiers personnalisés (scripts) Si votre solution requiert l’ingestion de données provenant d’autres sources, un code Python peut être utilisé pour... 

**Quels emplacements de stockage de données sont actuellement pris en charge ?**

Dans la préversion publique, Workbench prend en charge l’ingestion de données provenant des sources suivantes : 
- Disque dur local ou emplacement de stockage réseau mappé
- Objet blob Azure ou Stockage Azure (nécessite un abonnement Azure)
- Azure SQL Server
- Microsoft SQL Server


**Quels types retraitements, préparations et transformations de données sont disponibles ?**

Dans la préversion publique, Workbench prend en charge les opérations de dérivation de colonne par exemple, de fractionnement de colonne par exemple, de clustering de texte, de gestion de valeurs manquantes, et bien d’autres.  Workbench prend également en charge la conversion de type de données, l’agrégation de données (COUNT, MEAN, VARIANCE, etc.) et les jointures de données complexes. Pour obtenir la liste complète des fonctionnalités prises en charge, consultez la documentation du produit. 

**Les services Azure d’apprentissage automatique, d’expérimentation ou de gestion des modèles imposent-ils des limites à la taille des données ?**

Non, les nouveaux services n’imposent aucune limitation des données. Toutefois, il existe des limitations résultant de l’environnement dans lequel vous effectuez la préparation des données, ainsi que l’apprentissage, l’expérimentation ou le déploiement du modèle. Par exemple, si vous ciblez un environnement local pour l’apprentissage, vous êtes limité par l’espace disponible sur votre disque dur. Par ailleurs, si vous ciblez HDInsight, vous êtes limité par toute restrictions de taille ou de calcul associée. 

## <a name="algorithms-and-libraries"></a>Algorithmes et bibliothèques

**Quels sont les algorithmes pris en charge dans Azure Machine Learning Workbench ?**

Nos produits et services en préversion incluent ce qu’il y a de meilleur au sein de la communauté Open Source. Nous prenons en charge un vaste éventail d’algorithmes et de bibliothèques, dont TensorFlow, scikit-learn, Apache Spark et Microsoft Cognitive Toolkit. Azure Machine Learning Workbench crée également le package [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy).

**Quelle est la relation entre Azure Machine Learning et Microsoft Cognitive Toolkit ?**

[Microsoft Cognitive Toolkit](https://www.microsoft.com/cognitive-toolkit/) est l’une des nombreuses infrastructures que nos nouveaux outils et services prennent en charge. Microsoft Cognitive Toolkit est une boîte à outils unifiée d’apprentissage profond qui vous permet de consommer et de combiner des modèles populaires d’apprentissage automatique, dont les réseaux neuronaux profonds en aval, les réseaux convolutifs, les réseaux séquence-à-séquence et les réseaux récurrents. Pour plus d’informations sur Microsoft Cognitive Toolkit, consultez la [documentation du produit](https://docs.microsoft.com/cognitive-toolkit/). 