---
title: Forum Aux Questions (FAQ) Azure Machine Learning | Microsoft Docs
description: "Présentation d'Azure Machine Learning : FAQ portant sur la facturation, les fonctionnalités et les limitations d'un service cloud pour la modélisation prédictive rationalisée."
keywords: introduction à l’apprentissage automatique, modélisation prédictive, présentation de l’apprentissage automatique
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: a4a32a06-dbed-4727-a857-c10da774ce66
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/02/2017
ms.openlocfilehash: ceec69ee822e118adfa3825620a31575109894ea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-machine-learning-frequently-asked-questions-billing-capabilities-limitations-and-support"></a>Forum Aux Questions Azure Machine Learning : facturation, fonctionnalités, limitations et support
Voici quelques-unes des questions fréquemment posées et des réponses correspondantes sur Azure Machine Learning, un service cloud pour le développement de modèles prédictifs et la mise en œuvre de solutions via des services web. Ce FAQ aborde l’utilisation du service, notamment le modèle de facturation, les fonctionnalités, les limitations et le support.

**Vous ne parvenez pas à trouver votre question ?**

Azure Machine Learning dispose également d’un forum sur MSDN, où des membres de la communauté de science des données peuvent poser des questions concernant Azure Machine Learning. Ce forum est géré par l’équipe Azure Machine Learning. Rendez-vous sur le [forum Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) pour trouver des réponses ou publier une nouvelle question de votre choix.

## <a name="general-questions"></a>Questions générales
**Qu'est-ce que Microsoft Azure Machine Learning ?**

Microsoft Azure Machine Learning est un service entièrement géré qui permet de créer, de tester, de faire fonctionner et de gérer des solutions analytiques prédictives dans le cloud. À l’aide d’un simple navigateur, vous pouvez vous connecter, charger des données et commencer immédiatement des expériences d’apprentissage automatique. Grâce à la modélisation prédictive par glisser-déplacer, à une vaste palette de modules et à une bibliothèque de modèles de départ, les tâches d’apprentissage automatique les plus courantes sont simples et rapides. Pour plus d’informations, consultez la [Vue d’ensemble du service Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Pour une présentation de l’apprentissage automatique qui aborde la terminologie et les concepts principaux, consultez [Présentation d’Azure Machine Learning](what-is-machine-learning.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

**Qu'est-ce que Machine Learning Studio ?**

Machine Learning Studio est un environnement de banc d’essai accessible par l’intermédiaire d’un navigateur web. Il héberge de nombreux modules dans une interface de composition visuelle et vous aide à concevoir un workflow de science des données de bout en bout qui prend la forme d’une expérience.

Pour plus d’informations sur Machine Learning Studio, consultez [Qu'est-ce que Machine Learning Studio ?](what-is-ml-studio.md)

**Qu'est-ce que le service API Machine Learning ?**

Le service API Machine Learning vous permet de déployer des modèles prédictifs, tels que ceux intégrés à Machine Learning Studio, sous forme de services web évolutifs et tolérants aux pannes. Les services web créés par le service API Machine Learning sont des API REST qui fournissent une interface pour les communications entre les applications externes et vos modèles analytiques prédictifs.

Pour plus d’informations, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning](consume-web-services.md).

**Où sont répertoriés mes services web Classic ? Où sont répertoriés mes nouveaux services web (basés sur Azure Resource Manager) ?**

Les services web créés à l’aide du modèle de déploiement Classic et ceux créés à l’aide du nouveau modèle de déploiement Azure Resource Manager sont répertoriés dans le portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/).

Les services web Classic figurent également dans [Machine Learning Studio](http://studio.azureml.net) dans l’onglet **Services web**.

## <a name="azure-machine-learning-questions"></a>Questions sur Azure Machine Learning
**Que sont les services web Azure Machine Learning ?**

Les services web Machine Learning fournissent une interface entre une application et un modèle de notation du workflow Machine Learning. Une application externe peut utiliser Azure Machine Learning pour communiquer en temps réel avec un modèle de notation du workflow Machine Learning. Un appel à un service web Machine Learning renvoie les résultats d’une prédiction à une application externe. Pour générer cet appel, vous transmettez une clé API créée au moment du déploiement du service web. Un service web Machine Learning s’appuie sur l’architecture REST, souvent choisie pour les projets de programmation web.

Azure Machine Learning propose deux types de services web :

* RSS (Request-Response Service) : service hautement évolutif, à faible latence, qui fournit une interface pour les modèles sans état créés et déployés à partir de Machine Learning Studio.
* BES (Batch Execution Service) : service asynchrone qui effectue la notation d’un batch pour les enregistrements de données.

Il existe plusieurs moyens d’utiliser l’API REST et d’accéder au service web. Par exemple, vous pouvez écrire une application en C#, R ou Python en utilisant l’exemple de code généré lors du déploiement du service web.

L’exemple de code est disponible sur :
- La page d’utilisation du service web dans le portail des services web Azure Machine Learning
- La page d’aide de l’API dans le tableau de bord des services web de Machine Learning Studio

Vous pouvez également utiliser l’exemple de classeur Microsoft Excel créé pour vous et disponible dans le tableau de bord des services web de Machine Learning Studio.

**Quelles sont les principales mises à jour apportées à Azure Machine Learning ?**

Pour accéder aux mises à jour les plus récentes, consultez [Nouveautés d’Azure Machine Learning](whats-new.md).

## <a name="machine-learning-studio-questions"></a>Questions sur Machine Learning Studio
### <a name="import-and-export-data-for-machine-learning"></a>Importation et exportation de données pour Machine Learning
**Quelles sources de données sont prises en charge par Machine Learning ?**

Il existe trois façons de télécharger des données dans une expérience Machine Learning Studio :

- Chargement d’un fichier local en tant que jeu de données
- Utilisation d’un module pour importer des données à partir des services de données cloud
- Importation d’un jeu de données enregistré à partir d’une autre expérience

Pour en savoir plus sur les formats de fichiers pris en charge, voir [Importation de vos données d’apprentissage Azure Machine Learning Studio depuis différentes sources de données](import-data.md).

#### <a id="ModuleLimit"></a>Quelle est la taille maximale du jeu de données de mes modules ?
Les modules de Machine Learning Studio prennent en charge les jeux de données d’une taille maximale de 10 Go de données numériques denses pour les scénarios d’utilisation courants. Si un module accepte plusieurs entrées, le total de toutes les tailles d’entrée est de 10 Go. Vous pouvez également échantillonner des jeux de données plus importants par le biais de requêtes Hive ou Azure SQL Database ou encore via un prétraitement Learning by Counts avant l’ingestion.  

Les types de données suivants peuvent être développés en jeux de données plus importants au moment de la normalisation des fonctionnalités et ils sont limités à moins de 10 Go :

* Partiellement alloué
* Par catégorie
* Chaînes
* Données binaires

Les modules suivants sont limités à des jeux de données inférieurs à 10 Go :

* modules de recommandation
* Module SMOTE (Synthetic Minority Oversampling Technique)
* modules de script : R, Python, SQL
* Modules dont la taille des données de sortie peut être supérieure à la taille des données d’entrée, comme Join ou Feature Hashing
* Validation croisée, réglage des hyperparamètres de modèle, régression ordinale et plusieurs classes de un contre tous, lorsque le nombre d’itérations est très élevé

#### <a id="UploadLimit"></a>Quelles sont les limites de téléchargement de données ?
Pour les tailles de jeux de données supérieures à quelques gigaoctets, chargez les données dans le stockage Azure ou Azure SQL Database. Vous pouvez aussi utiliser Azure HDInsight au lieu d’effectuer un chargement directement à partir d’un fichier local.

**Puis-je lire les données à partir d’Amazon S3 ?**

Si vous disposez d’une petite quantité de données et que vous souhaitez l’exposer par le biais d’une URL HTTP, vous pouvez utiliser le module [Importer des données][import-data]. Si vous utilisez de plus grandes quantités de données, commencez par les transférer vers le stockage Azure, puis utilisez le module [Importer des données][import-data] pour les importer dans votre expérience.
<!--

<SEE CLOUD DS PROCESS>
-->

**Existe-t-il une capacité d’entrée d’image intégrée ?**

Découvrez la capacité d’entrée d’image dans la référence [Importer des images][image-reader].

### <a name="modules"></a>Modules
**L’algorithme, la source des données, le format des données ou l’opération de transformation des données que je recherche ne sont pas disponibles dans Azure Machine Learning Studio. Quelles sont mes options ?**

Vous pouvez consulter le [forum des commentaires des utilisateurs](http://go.microsoft.com/fwlink/?LinkId=404231) (en anglais) pour visualiser les demandes de fonctionnalités dont nous effectuons le suivi. Si la fonctionnalité que vous recherchez a déjà fait l’objet d’une requête, ajoutez votre vote à cette requête. Si la fonctionnalité que vous recherchez n’existe pas, créez une requête. Vous pouvez également afficher le statut de votre requête dans ce forum. Nous suivons cette liste de près et mettons à jour régulièrement le statut de disponibilité des fonctionnalités. Par ailleurs, le support intégré pour R et Python permet de créer des transformations personnalisées en fonction des besoins.

**Puis-je importer mon code existant dans Machine Learning Studio ?**

Oui. Vous pouvez importer votre code R ou Python existant dans Machine Learning Studio, l’exécuter au sein de la même expérience avec Azure Machine Learning, puis déployer la solution en tant que service web via Azure Machine Learning. Pour plus d’informations, voir [Prolongez votre expérience avec R](extend-your-experiment-with-r.md) et [Exécution des scripts d’apprentissage automatique Python dans Azure Machine Learning Studio](execute-python-scripts.md).

**Est-il possible d’utiliser [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language), par exemple, pour définir un modèle ?**

Non. Le modèle PMML (Predictive Model Markup Language) n’est pas pris en charge. Vous pouvez utiliser le code R et Python personnalisé pour définir un module.

**Combien de modules puis-je exécuter en parallèle dans mon expérience ?**  

Vous pouvez exécuter jusqu’à quatre modules en parallèle dans une expérience.

### <a name="data-processing"></a>Traitement des données
**Existe-t-il une possibilité de visualiser les données (autre que les visualisations R) de manière interactive au sein de l’expérience ?**

Cliquez sur la sortie d’un module pour visualiser les données et obtenir des statistiques.

**Lorsque vous générez l’aperçu des résultats ou des données dans un navigateur, le nombre de lignes et de colonnes est limité. Pourquoi ?**

Étant donné les grandes quantités de données susceptibles d’être envoyées au navigateur, la taille des données est limitée pour éviter de ralentir Machine Learning Studio. Pour visualiser l’ensemble des données et des résultats, il est préférable de télécharger les données, puis d’utiliser Excel ou un autre outil.

### <a name="algorithms"></a>Algorithmes
**Quels sont les algorithmes ML existants pris en charge dans Machine Learning Studio ?**

Machine Learning Studio fournit des algorithmes de pointe tels que les arbres de décision optimisés évolutifs, les systèmes de recommandation bayésiens, les réseaux neuronaux profonds et les jungles de décision développés chez Microsoft Research. Des modules d’apprentissage automatique open source évolutifs tels que Vowpal Wabbit sont également inclus. Machine Learning Studio prend en charge les algorithmes d’apprentissage automatique pour la classification, la régression et le clustering multiclasses et binaires. Consultez la liste complète des [Modules de Machine Learning][machine-learning-modules].

**Suggérez-vous automatiquement le bon algorithme Machine Learning à utiliser pour mes données ?**

Non. Toutefois, Machine Learning Studio propose plusieurs façons de comparer les résultats de chaque algorithme afin de déterminer le mieux approprié à votre cas.

**Y a-t-il des instructions sur le choix d’un algorithme plutôt qu’un autre pour les algorithmes fournis ?**

Voir [Comment choisir les algorithmes dans Microsoft Azure Machine Learning](algorithm-choice.md).

**Les algorithmes fournis sont-ils écrits en Python ou en R ?**

Non. Pour la plupart, ces algorithmes sont écrits dans des langages compilés afin d’obtenir de meilleures performances.

**Des détails relatifs aux algorithmes sont-ils disponibles ?**

La documentation propose des informations sur les algorithmes, et les paramètres fournis pour le paramétrage sont décrits afin d’optimiser votre utilisation de l’algorithme choisi.  

**Existe-t-il une prise en charge pour l’apprentissage en ligne ?**

Non. Actuellement, seul le recyclage par programme est pris en charge.

**Est-il possible de visualiser les couches d’un modèle de réseau neuronal à l’aide du module intégré ?**

Non.

**Puis-je créer mes propres modules en C# ou un autre langage ?**

Actuellement, vous pouvez uniquement utiliser R pour créer des modules personnalisés.

### <a name="r-module"></a>Module R
**Quels packages R sont disponibles dans Machine Learning Studio ?**

Machine Learning Studio prend actuellement en charge plus de 400 packages CRAN R. Voici la [liste actuelle](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) de tous les packages inclus. Pour découvrir comment récupérer vous-même cette liste, voir [Prolongez votre expérience avec R](extend-your-experiment-with-r.md). Si le package souhaité ne figure pas dans cette liste, indiquez son nom sur le [forum des commentaires des utilisateurs](http://go.microsoft.com/fwlink/?LinkId=404231).

**Est-il possible de créer un module R personnalisé ?**

Oui. Consultez [Créer des modules R personnalisés dans Microsoft Azure Machine Learning](custom-r-modules.md) pour plus d’informations.

**Existe-t-il un environnement REPL pour R ?**

Non. Il n’existe aucun environnement REPL (Read-Eval-Print-Loop) pour R dans Studio.

### <a name="python-module"></a>Module Python
**Est-il possible de créer un module Python personnalisé ?**

Pas pour l’instant, mais vous pouvez utiliser un ou plusieurs modules [Exécuter le script Python][python] pour obtenir le même résultat.

**Existe-t-il un environnement REPL pour Python ?**

Vous pouvez utiliser les Notebooks Jupyter dans Machine Learning Studio. Pour plus d’informations, voir [Introducing Jupyter Notebooks in Azure Machine Learning Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx)(Présentation des notebooks Jupyter dans Azure Machine Learning Studio).

## <a name="web-service"></a>Service Web
### <a name="retrain"></a>Recyclage
**Comment recycler des modèles Azure Machine Learning par programme ?**

Utilisez les API de recyclage. Pour plus d'informations, consultez la page [Reformation des modèles Machine Learning par programme](retrain-models-programmatically.md). Un exemple de code est également disponible sur la page [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/)(Démonstration de reformation Microsoft Azure Machine Learning).

### <a name="create"></a>Créer
**Puis-je déployer le modèle localement ou dans une application sans connexion Internet ?**

Non.

**Existe-t-il une latence de référence attendue pour tous les services Web ?**

Consultez les [Limites d’abonnement Azure](../../azure-subscription-service-limits.md).

### <a name="use"></a>Utilisation
**Quand ai-je avantage à exécuter mon modèle prédictif en tant que service d’exécution de lots plutôt qu’un service Web requête-réponse ?**

Le service de requête-réponse (RRS, Request-Response Service) est un service web à faible latence et à grande échelle qui permet de fournir une interface aux modèles sans état créés et déployés à partir de l’environnement Expérimentation. BES (Batch Execution Service) est un service de notation asynchrone des batchs d’enregistrements de données. La saisie pour BES ressemble à la saisie de données utilisée par RRS. La principale différence réside dans le fait que BES lit un bloc d’enregistrements à partir de différentes sources, par exemple le stockage Blob Azure, le stockage Table Azure, Azure SQL Database, HDInsight (requêtes Hive) et les sources HTTP. Pour plus d’informations, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning](consume-web-services.md).

**Comment puis-je mettre à jour le modèle pour le service Web déployé ?**

Si vous voulez mettre à jour un modèle prédictif pour un service déjà déployé, il vous suffit de modifier et de réexécuter l’expérience utilisée pour créer et enregistrer le modèle formé. Une fois qu’une nouvelle version du modèle formé est disponible, Machine Learning Studio vous demande si vous voulez mettre à jour votre service web. Pour plus d’informations sur la mise à jour d’un service web déployé, voir [Déploiement d’un service web Machine Learning](publish-a-machine-learning-web-service.md).

Vous pouvez également utiliser les API de recyclage.
Pour plus d'informations, consultez la page [Reformation des modèles Machine Learning par programme](retrain-models-programmatically.md). Un exemple de code est également disponible sur la page [Microsoft Azure Machine Learning Retraining Demo](https://azuremlretrain.codeplex.com/)(Démonstration de reformation Microsoft Azure Machine Learning).

**Comment puis-je surveiller mon service web déployé en production ?**

Une fois que vous déployez un modèle prédictif, vous pouvez le surveiller à partir du portail des services web Azure Machine Learning. Chaque service déployé dispose de son propre tableau de bord, à partir duquel vous pouvez consulter les informations de surveillance correspondant à ce service. Pour plus d’informations sur la gestion de vos services web déployés, voir [Gestion d’un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md) et [Gestion d’un espace de travail Azure Machine Learning](manage-workspace.md).

**Existe-t-il un endroit où je peux voir le résultat de mon service RRS/BES ?**

Pour les enregistrements de ressources, c'est généralement dans la réponse du service web que vous voyez le résultat. Vous pouvez également l’écrire dans le stockage Blob Azure. Pour les environnements d'initialisation, la sortie est écrite dans un objet blob par défaut. Vous pouvez également écrire la sortie dans une base de données ou une table à l’aide du module [Exporter les données][export-data].

**Puis-je créer des services web uniquement à partir de modèles générés dans Machine Learning Studio ?**

Non. Vous pouvez également créer des services web directement à partir de Jupyter Notebooks et RStudio.

**Où puis-je trouver plus d'informations sur les codes d'erreur ?**

Pour obtenir la liste des codes d’erreur et leur description, voir [Machine Learning Module Error Codes](https://msdn.microsoft.com/library/azure/dn905910.aspx) (Codes d’erreur des modules Machine Learning).

## <a name="scalability"></a>Extensibilité
**Quelle est l’évolutivité du service Web ?**

Actuellement, le point de terminaison par défaut est doté de 20 demandes RR simultanées par point de terminaison. Vous pouvez l’étendre à 200 demandes simultanées par point de terminaison, et vous pouvez étendre chaque service web à 10 000 points de terminaison par service web, comme indiqué dans l’article [Mise à l’échelle du service web](scaling-webservice.md). Pour BES, chaque point de terminaison permet de traiter 40 demandes simultanées. Au-delà, les demandes supplémentaires sont mises en file d’attente. Ces requêtes en file d’attente sont exécutées automatiquement au fur et à mesure que la file diminue.

**Les travaux R sont-ils répartis entre les nœuds ?**

Non.  

**Quelle quantité de données puis-je utiliser pour l'apprentissage ?**

Les modules de Machine Learning Studio prennent en charge les jeux de données d’une taille maximale de 10 Go de données numériques denses pour les scénarios d’utilisation courants. Si un module prend plusieurs entrées, la taille totale de toutes les entrées est de 10 Go. Vous pouvez également échantillonner des jeux de données plus importants par le biais de requêtes Hive ou Azure SQL Database, ou encore par l’intermédiaire d’un prétraitement avec des modules [Learning with Counts][counts] avant l’ingestion.  

Les types de données suivants peuvent être développés en jeux de données plus importants au moment de la normalisation des fonctionnalités et ils sont limités à moins de 10 Go :

* Partiellement alloué
* Par catégorie
* Chaînes
* Données binaires

Les modules suivants sont limités à des jeux de données inférieurs à 10 Go :

* modules de recommandation
* Module SMOTE (Synthetic Minority Oversampling Technique)
* modules de script : R, Python, SQL
* Modules dont la taille des données de sortie peut être supérieure à la taille des données d’entrée, comme Join ou Feature Hashing
* Validation croisée, réglage des hyperparamètres de modèle, régression ordinale et plusieurs classes de un contre tous, lorsque le nombre d’itérations est très élevé

Pour les jeux de données supérieurs à quelques gigaoctets, mieux vaut charger les données dans le stockage Azure ou Azure SQL Database, ou encore utiliser HDInsight plutôt que d’effectuer un chargement directement à partir d’un fichier local.

**Existe-t-il des limitations de taille de vecteurs ?**

Les lignes et colonnes sont limitées selon la limitation Max Int de .NET : 2 147 483 647.

**Puis-je ajuster la taille de la machine virtuelle qui exécute le service web ?**

Non.  

## <a name="security-and-availability"></a>Sécurité et disponibilité
**Qui a accès au point de terminaison http pour le service web par défaut ? Comment puis-je limiter l’accès au point de terminaison ?**

Après le déploiement d’un service web, un point de terminaison par défaut est créé pour ce service. Le point de terminaison par défaut peut être appelé à l’aide de sa clé API. Il est possible d’ajouter des points de terminaison supplémentaires avec leurs propres clés à partir du portail des services web ou par le biais d’un programme, à l’aide des API Gestion des services web. Des clés d’accès sont nécessaires pour effectuer des appels au service web. Pour plus d’informations, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning](consume-web-services.md).

**Que se passe-t-il si mon compte de stockage Azure est introuvable ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires au moment de l’exécution du workflow. Ce compte de stockage est fourni à Machine Learning Studio au moment de la création d’un espace de travail. Une fois créé (si le compte de stockage est supprimé et reste introuvable), l’espace de travail cesse de fonctionner et toutes ses expériences échouent.

Si vous avez supprimé par inadvertance le compte de stockage, vous devez recréer ce compte de stockage avec exactement le même nom, dans la même région que celui que vous avez supprimé. Après cela, effectuez une nouvelle synchronisation de la clé d’accès.

**Que se passe-t-il si ma clé d’accès au compte de stockage n’est pas synchronisée ?**

Machine Learning Studio s’appuie sur un compte de stockage Azure fourni par l’utilisateur pour enregistrer les données intermédiaires au moment de l’exécution du workflow. Ce compte de stockage est fourni à Machine Learning Studio au moment de la création d’un espace de travail et les clés d’accès sont associées à cet espace de travail. Si les clés d’accès sont modifiées après la création de l’espace de travail, celui-ci ne pourra plus accéder au compte de stockage. Il cessera de fonctionner, et toutes les expériences dans cet espace de travail échoueront.

Si vous avez modifié les clés d’accès du compte de stockage, resynchronisez-les dans l’espace de travail à l’aide du portail Azure.  

## <a name="support-and-training"></a>Support et formations
**Où puis-je obtenir des formations pour Azure Machine Learning ?**

Le [Centre de documentation Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) propose des didacticiels vidéo ainsi que des guides pratiques. Ces derniers présentent les services et expliquent le cycle de vie de science des données lié à l’importation et au nettoyage des données, à la conception de modèles prédictifs et à leur déploiement en production avec Azure Machine Learning.

Nous ajoutons en permanence de nouveaux documents à ce centre de documentation. Pour obtenir des documents supplémentaires sur le Centre Machine Learning, vous pouvez envoyer des demandes à partir du [forum des commentaires des utilisateurs](https://windowsazure.uservoice.com/forums/257792-machine-learning)(en anglais).

Vous pouvez également rechercher des formations sur [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning).

**Comment puis-je bénéficier d’un support pour Azure Machine Learning ?**

Pour obtenir un support technique pour Azure Machine Learning, accédez au [Support Azure](/support/options/) et sélectionnez **Machine Learning**.

Azure Machine Learning dispose également d’un forum communautaire sur MSDN, où vous pouvez poser des questions en rapport avec Azure Machine Learning. Ce forum est géré par l’équipe Azure Machine Learning. Visitez le [Forum Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning).

## <a name="billing-questions"></a>Questions sur la facturation
**Comment fonctionne la facturation dans Machine Learning ?**

Azure Machine Learning possède deux composants : Machine Learning Studio et les services web Machine Learning.

Si vous évaluez Machine Learning Studio, vous pouvez utiliser le niveau de facturation Gratuit. Le niveau Gratuit vous permet également de déployer un service web Classic de capacité limitée.

Une fois que vous estimez qu’Azure Machine Learning répond à vos besoins, vous pouvez vous inscrire au niveau Standard. Pour cela, vous devez disposer d’un abonnement à Microsoft Azure.

Dans le niveau Standard, vous êtes facturé mensuellement pour chaque espace de travail que vous définissez dans Machine Learning Studio. Lorsque vous exécutez une expérience dans le studio, vous êtes facturé selon les ressources de calcul nécessaires à son exécution. Lorsque vous déployez un service web Classic, les transactions et les heures de calcul sont facturées sur la base d’un paiement à l’utilisation.

Les nouveaux services web (qui s’appuient sur Azure Resource Manager) incluent des profils de facturation qui offrent une plus grande prévisibilité des coûts. La tarification par niveau propose des taux avec remise aux clients ayant besoin d’une capacité importante.

Lorsque vous créez un profil, vous acceptez un coût fixe associé à une quantité incluse d’heures de calcul et de transactions d’API. Si vous avez besoin de davantage de quantités incluses, vous pouvez ajouter des instances supplémentaires à votre plan. Si vous avez besoin de beaucoup plus de quantités incluses, vous pouvez choisir un plan de niveau supérieur qui en offre davantage à un meilleur tarif.

Une fois les quantités incluses dans les instances existantes épuisées, toute utilisation supplémentaire est facturée d’après le tarif de dépassement associé à votre niveau de profil de facturation.

> [!NOTE]
Les quantités incluses sont réallouées tous les 30 jours et les quantités incluses non utilisées ne sont pas reportées sur la période suivante.

Pour plus d’informations sur la facturation et la tarification, consultez [Tarification Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

**Une version d’évaluation de Machine Learning est-elle disponible ?**

 Azure Machine Learning dispose d’une option d’abonnement gratuit qui est expliquée dans [Tarification de Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/). Machine Learning Studio comporte une version d’évaluation rapide de huit heures qui vous est proposée lorsque vous vous connectez à [Machine Learning Studio](https://studio.azureml.net/?selectAccess=true&o=2).

 En outre, lorsque vous vous inscrivez pour une version d’évaluation gratuite d’Azure, vous pouvez essayer n’importe quel service Azure pendant un mois. Pour plus d’informations sur la version d’évaluation gratuite d’Azure, voir [FAQ Compte gratuit Azure](https://azure.microsoft.com/pricing/free-trial-faq/).

**Qu’est-ce qu’une transaction ?**

Une transaction correspond à un appel d’API pris en charge par Azure Machine Learning. Les transactions provenant d’appels de service de requête-réponse (RRS) et de service d’exécution de lots (BES) sont regroupées et facturées selon votre profil de facturation.

**Puis-je utiliser les quantités de transactions incluses dans un plan à la fois pour les transactions de services RSS et BES ?**

Oui, les transactions de vos RRS et BES sont regroupées et facturées selon votre profil de facturation.

**Qu’est-ce qu’une heure de calcul API ?**

Une heure de calcul API est l’unité de facturation correspondant au temps nécessaire à l’exécution des appels d’API à l’aide des ressources de calcul Machine Learning. Tous vos appels sont regroupés pour la facturation.

**Combien de temps prend un appel d’API de production type ?**

Les durées d’appel API de production peuvent varier considérablement, en général de quelques centaines de millisecondes à quelques secondes. Parfois, selon la complexité du modèle d’apprentissage automatique et de traitement des données, plusieurs minutes sont nécessaires à certains appels d’API. La meilleure façon d’évaluer les délais d’appel des API de production consiste à définir un modèle sur le service Machine Learning.

**Qu’est-ce qu’une heure de calcul Studio ?**

Une heure de calcul Studio est l’unité de facturation correspondant à la durée cumulée d’utilisation des ressources de calcul dans Studio par vos expériences.

**Dans les nouveaux services web (basés sur Azure Resource Manager), à quoi sert le niveau de développement/test ?**

Les nouveaux services web basés sur Azure Resource Manager proposent différents niveaux que vous pouvez utiliser pour configurer votre profil de facturation. Le niveau de développement/test fournit des quantités incluses et limitées qui vous permettent de tester votre expérience à titre gratuit, en tant que nouveau service web. Vous avez la possibilité de voir comment elle fonctionne.

**Existe-t-il des frais distincts pour le stockage ?**

Le niveau Gratuit de Machine Learning n’autorise pas de stockage distinct. Le niveau Standard de Machine Learning nécessite que les utilisateurs aient un compte de stockage Azure. Le stockage Azure est [facturé séparément](https://azure.microsoft.com/pricing/details/storage/).

**Machine Learning prend-il en charge la disponibilité élevée ?**

Oui. Pour plus d’informations, voir [Tarification de Machine Learning](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) et une description du contrat de niveau de service (SLA).

**Sur quel type de ressources de calcul mes appels d’API de production seront-ils exécutés ?**

Le service Machine Learning est un service multi-locataire. Les ressources de calcul réel qui sont utilisées sur le serveur principal varient et sont optimisées pour les performances et la prévisibilité.

### <a name="management-of-new-resource-manager-based-web-services"></a>Gestion de nouveaux services web (basés sur Azure Resource Manager)
**Que se passe-t-il si je supprime mon profil ?**

Le plan est supprimé de votre abonnement et vous êtes facturé au prorata de votre utilisation.

> [!NOTE]
Vous ne pouvez pas supprimer un plan utilisé par un service web. Pour supprimer le profil, vous devez en attribuer un nouveau au service web ou supprimer ce dernier.

**Qu’est-ce qu’une instance de plan ?**

Une instance de plan est une unité de quantités incluses que vous pouvez ajouter à votre profil de facturation. Lorsque vous sélectionnez un niveau de facturation pour votre profil de facturation, il contient une seule instance. Si vous avez besoin de davantage de quantités incluses, vous pouvez ajouter des instances du niveau de facturation sélectionné à votre profil.

**Combien d’instances de plan puis-je ajouter ?**

Vous ne pouvez avoir qu’un seul niveau de tarification développement/test par abonnement.

Par contre, pour les niveaux Standard S1, Standard S2 et Standard S3, vous pouvez en ajouter autant que nécessaire.

> [!NOTE]
Selon l’utilisation que vous prévoyez, il peut s’avérer plus rentable de passer à un niveau plus élevé de quantités incluses plutôt que d’ajouter des instances au niveau actuel.

**Que se passe-t-il lorsque je change de niveau de plan (et que je passe à un niveau supérieur/inférieur) ?**

L’ancien profil est supprimé et l’utilisation en cours est facturée au prorata. Un nouveau profil comprenant la totalité des quantités incluses correspondant au niveau supérieur/inférieur est créé pour le reste de la période.

> [!NOTE]
Les quantités incluses sont allouées par période et les quantités non utilisées ne sont pas reportées.

**Que se passe-t-il lorsque j’augmente les instances d’un profil ?**

Les quantités sont ajoutées au prorata. L’opération peut prendre 24 heures.

**Que se passe-t-il lorsque je supprime une instance d’un profil ?**

L’instance est supprimée de votre abonnement et vous êtes facturé au prorata de votre utilisation.

### <a name="sign-up-for-new-resource-manager-based-web-services-plans"></a>Inscription à de nouveaux services web (basés sur Azure Resource Manager)
**Comment s’inscrire à un plan ?**

Il existe deux manières de créer des profils de facturation.

Lorsque vous déployez un nouveau service web basé sur Azure Resource Manager pour la première fois, vous pouvez choisir un plan existant ou en créer un.

Les plans créés de cette façon se situent dans votre région par défaut, et votre service web sera déployé dans celle-ci.

Si vous souhaitez déployer des services dans des régions autres que votre région par défaut, nous vous recommandons de définir vos plans de facturation avant de déployer votre service.

Dans ce cas, connectez-vous au portail des services web Azure Machine Learning et accédez à la page consacrée aux abonnements. Vous pourrez alors ajouter et supprimer des plans, mais aussi modifier des plans existants.

**Quel profil devrais-je choisir pour commencer ?**

Nous vous recommandons de commencer par le niveau Standard S1 et de surveiller l’utilisation du service. Si vous estimez que vous utilisez rapidement les quantités incluses, ajoutez des instances ou optez pour un niveau supérieur afin d’obtenir un meilleur tarif. Vous pouvez ajuster votre profil de facturation en fonction de vos besoins tout au long de votre cycle de facturation.

**Dans quelles régions les nouveaux profils sont-ils disponibles ?**

Les nouveaux profils de facturation sont disponibles dans les trois régions de production dans lesquelles nous prenons en charge les nouveaux services web :

* États-Unis - partie centrale méridionale
* Europe de l'Ouest
* Asie du Sud-Est

**J’ai des services web dans plusieurs régions. Dois-je avoir un profil pour chaque région ?**

Oui. La tarification des profils varie selon les régions. Si vous déployez un service web dans une autre région, vous devrez lui affecter un plan spécifique. Pour plus d’informations, consultez [Disponibilité des produits par région]( https://azure.microsoft.com/regions/services/).

### <a name="new-web-services-overages"></a>Nouveaux services web : dépassements
**Comment vérifier si mon utilisation du service web est en dépassement ?**

Vous pouvez consulter l’utilisation de tous vos profils sur la page qui leur est dédiée dans le portail de services web Azure Machine Learning. Connectez-vous au portail, puis cliquez sur l’option de menu **Abonnements**.

Dans les colonnes **Transactions** et **Calcul** du tableau, vous pouvez consulter les quantités incluses dans le plan et le pourcentage utilisé.

**Que se passe-t-il lorsque j’ai épuisé les quantités incluses dans le niveau de tarification développement/test ?**

Les services affectés au niveau de tarification développement/test sont arrêtés jusqu’à la période suivante ou jusqu’à ce que vous les déplaciez vers l’un des niveaux payants.

**Pour les services web Classic et les dépassements des nouveaux services web (basés sur Azure Resource Manager), comment sont calculés les tarifs pour les charges de travail RRS et BES ?**

Pour une charge de travail RRS, vous êtes facturé pour chaque appel de transaction API, ainsi que pour le temps de calcul associé à ces requêtes. Les coûts relatifs aux transactions API de production RRS sont calculés comme suit : nombre total d’appels d’API effectués multiplié par le tarif par 1 000 transactions (au prorata par transaction individuelle). Les coûts relatifs aux heures de calcul API de production RRS sont calculés comme suit : durée d’exécution de chaque appel d’API multiplié par le nombre total de transactions API, multiplié par le tarif par heure de calcul API de production.

Par exemple, avec le niveau de dépassement Standard S1, le coût de 1 000 000 transactions API d’une durée de 0,72 seconde chacune est calculée comme suit : (1 000 000 * 0,50 $/1 000 transactions API), soit un tarif pour les transactions API de production de 500 $ et (1 000 000 * 0,72 s * 2 $/heure), soit un tarif pour les heures de calcul API de production de 400 $, pour un total de 900 $.

Pour une charge de travail BES, vous êtes facturé de la même manière. Toutefois, les coûts de transaction API représentent le nombre de traitements par lot que vous soumettez, et les coûts de calcul représentent le temps de calcul associé à ces traitements par lot. Les coûts relatifs aux transactions API de production BES sont calculés comme suit : nombre total de travaux soumis multiplié par le tarif par 1 000 transactions (au prorata par transaction individuelle). Les coûts relatifs aux heures de calcul API de production BES sont calculés comme suit : durée d’exécution de chaque ligne de votre travail multiplié par le nombre total de lignes de votre travail, multiplié par le nombre total de travaux, multiplié par le tarif par heure de calcul API de production. Lorsque vous utilisez la calculatrice Machine Learning, le compteur de transactions représente le nombre de travaux que vous planifiez de soumettre, et la durée par transaction représente la durée combinée nécessaire à l’exécution de toutes les lignes de chaque travail.

Par exemple, supposons que vous dépassez le niveau Standard S1 et que vous envoyez 100 tâches par jour, ce qui correspond à 500 lignes qui prennent 0,72 seconde chacune. Le coût mensuel de votre dépassement est le suivant : (100 travaux par jour = 3 100 travaux par mois * 0,50 $/1 000 transactions API), soit 1,55 $ en coût de transaction API de production et (500 lignes * 0,72 s * 3 100 travaux * 2 $/h), soit 620 $ en heures de calcul API de production, pour un total de 621,55 $.

### <a name="azure-machine-learning-classic-web-services"></a>Services web Azure Machine Learning
**Le paiement à l’utilisation est-il toujours disponible ?**

Oui. Les services web Classic sont toujours disponibles dans Azure Machine Learning.  

### <a name="azure-machine-learning-free-and-standard-tier"></a>Niveaux Gratuit et Standard d’Azure Machine Learning
**Qu’est-ce qui est inclus dans le niveau Gratuit d’Azure Machine Learning ?**

Le niveau Gratuit d’Azure Machine Learning offre une introduction complète à Azure Machine Learning Studio. Il vous suffit d'un compte Microsoft pour vous inscrire. Le niveau Gratuit inclut un accès gratuit à un espace de travail Azure Machine Learning Studio par [compte Microsoft](https://www.microsoft.com/account/default.aspx). Dans ce niveau, vous pouvez utiliser jusqu’à 10 Go de stockage et rendre des modèles opérationnels en tant qu’API intermédiaires. Les charges de travail du niveau Gratuit ne sont couvertes par aucun contrat de niveau de service et sont uniquement destinées au développement et à une utilisation personnelle. 

Les espaces de travail du niveau Gratuit présentent les limites suivantes :

* Les charges de travail ne peuvent pas accéder aux données en se connectant à un serveur SQL Server local.
* Vous ne pouvez pas déployer de nouveaux services web basés sur Resource Manager.


**Qu’est-ce qui est inclus dans le niveau et les plans Standard d'Azure Machine Learning ?**

Le niveau Standard d’Azure Machine Learning est une version de production payante d’Azure Machine Learning Studio. Les frais mensuels du service Azure Machine Learning Studio sont facturés par espace de travail et par mois et au prorata pour les mois incomplets. Les heures d’expérience Azure Machine Learning Studio sont facturées par heure de calcul pour l’expérience active. La facturation est ajustée pour les heures incomplètes.  

Le service API d’Azure Machine Learning est facturé selon le type de service web : Classic ou nouveau (basé sur Azure Resource Manager).

Les frais suivants sont regroupés par espace de travail dans votre abonnement.

* Abonnement à un espace de travail Machine Learning : l’abonnement à un espace de travail Machine Learning est un abonnement mensuel qui donne accès à un espace de travail Machine Learning Studio. Cet abonnement est requis pour exécuter des expériences dans Studio et utiliser les API de production.
* Heures d’expérience Studio : ce compteur regroupe tous les frais de calcul cumulés suite à l’exécution d’expériences dans Machine Learning Studio et à l’exécution d’appels d’API de production dans l’environnement intermédiaire.
* Accédez aux données en vous connectant à un serveur SQL Server local dans vos modèles pour votre formation et votre notation.
* Pour les services web Classic :
  * Heures de calcul API de production : ce compteur inclut les frais de calcul cumulés par les services web exécutés en production.
  * Transactions API de production (par milliers) : ce compteur inclut les frais cumulés par appel à votre service web de production.

Mis à part les frais ci-dessus, dans le cas de services web basés sur Azure Resource Manager, les frais sont regroupés dans le plan sélectionné :

* Plan d’API Standard S1/S2/S3 (unités) : ce compteur représente le type d’instance sélectionné pour les services web basés sur Azure Resource Manager.
* Heures de calcul des API Dépassement Standard S1/S2/S3 : ce compteur inclut les frais de calcul cumulés par les services web basés sur Azure Resource Manager exécutés en production, après épuisement des quantités incluses dans les instances existantes. L’utilisation supplémentaire est facturée au tarif de dépassement associé au niveau du plan S1/S2/S3.
* Transactions des API Dépassement Standard S1/S2/S3 (par milliers) : ce compteur inclut les frais cumulés par appel à votre service web de production basé sur Azure Resource Manager, après épuisement des quantités incluses dans les instances existantes. L’utilisation supplémentaire est facturée au tarif de dépassement associé au niveau du plan S1/S2/S3.
* Heures de calcul des API Quantité incluse : avec les services web basés sur Azure Resource Manager, ce compteur représente la quantité incluse d’heures de calcul d’API.
* Transactions des API Quantité incluse (en milliers) : avec les services web basés sur Azure Resource Manager, ce compteur représente la quantité incluse de transactions d’API.

**Comment s’inscrire au niveau Gratuit d’Azure Machine Learning ?**

Vous avez besoin uniquement d'un compte Microsoft. Accédez à la [page d’accueil Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) puis cliquez sur le bouton **Démarrer maintenant**. Connectez-vous à votre compte Microsoft. Un espace de travail de niveau Gratuit est alors créé pour vous. Vous pouvez immédiatement commencer à explorer et à créer des expériences Machine Learning.

**Comment s’inscrire au niveau Standard d’Azure Machine Learning ?**

Tout d’abord, vous devez avoir accès à un abonnement Azure pour créer un espace de travail Machine Learning Standard. Vous pouvez vous souscrire pour un abonnement Azure gratuit de 30 jours, puis migrer vers un abonnement Azure payant ou acheter directement ce dernier. Vous pouvez ensuite créer un espace de travail Machine Learning à partir du portail de gestion Microsoft Azure, après avoir obtenu l’accès à l’abonnement. Veuillez consulter [les instructions pas à pas](https://azure.microsoft.com/trial/get-started-machine-learning-b/).

En outre, vous pouvez être invité par un propriétaire à accéder à son espace de travail Machine Learning Standard.

**Puis-je spécifier mon propre compte de stockage Blob Azure et l’utiliser avec le niveau Gratuit ?**

Non, le niveau Standard est équivalent à la version du service Machine Learning qui était disponible avant l’introduction des niveaux.

**Puis-je déployer mes modèles d'apprentissage automatique en tant qu'API dans le niveau Gratuit ?**

Oui. Avec le niveau Gratuit, vous pouvez faire fonctionner des modèles Machine Learning dans les services API intermédiaires. Pour que le service API intermédiaire soit mis en production, et pour obtenir un point de terminaison de production pour le service fonctionnel, vous devez utiliser le niveau Standard.

**Quelle est la différence entre l’essai gratuit de Microsoft Azure et le niveau Gratuit d’Azure Machine Learning ?**

L’[essai gratuit de Microsoft Azure](https://azure.microsoft.com/free/) offre des crédits qui peuvent être appliqués à n’importe quel service Azure pendant un mois. Le niveau Gratuit d’Azure Machine Learning offre plus précisément un accès continu au service Azure Machine Learning pour les charges de travail autres que de production.

**Comment migrer l'expérience du niveau Gratuit au niveau Standard ?**

Pour copier vos expériences du niveau Gratuit vers le niveau Standard :

1. Connectez-vous à Azure Machine Learning Studio. Assurez-vous que les espaces de travail Gratuit et Standard figurent dans le sélecteur d’espaces de travail, dans la barre de navigation supérieure.
2. Passez à l’espace de travail Gratuit si vous êtes dans l’espace de travail Standard.
3. Dans la liste des expériences, sélectionnez une expérience à copier, puis cliquez sur le bouton de commande **Copier**.
4. Sélectionnez l’espace de travail Standard dans la boîte de dialogue qui s’ouvre, puis cliquez sur le bouton **Copier**.
   Tous les jeux de données associés, le modèle formé et les autres éléments sont copiés en même temps que l’expérience dans l’espace de travail Standard.
5. Vous devez réexécuter l’expérience et republier votre site web dans l’espace de travail Standard.

### <a name="studio-workspace"></a>Espace de travail Studio
**Y a-t-il différentes factures pour différents espaces de travail ?**

Les frais liés à l’espace de travail sont décomposés pour chaque compteur applicable sur une seule facture.

**Sur quel type de ressources de calcul mes expériences seront-elles exécutées ?**

Le service Machine Learning est un service multi-locataire. Les ressources de calcul réel qui sont utilisées sur le serveur principal varient et sont optimisées pour les performances et la prévisibilité.

### <a name="guest-access"></a>Accès invité
**Qu’est-ce qu’un accès invité à Azure Machine Learning Studio ?**

L’accès invité offre une expérience d’évaluation limitée. Vous pouvez créer et exécuter des expériences dans Azure Machine Learning Studio sans frais et sans authentification. Les sessions en tant qu’invité ne sont pas persistantes (et ne peuvent pas être enregistrées). Elles sont limitées à huit heures. Elles sont assorties d’autres limitations comme l’absence de prise en charge des langages R et Python, l’absence d’API intermédiaires et une taille de jeu de données et une capacité de stockage limitées. À titre de comparaison, les utilisateurs qui choisissent de se connecter à un compte Microsoft bénéficient d’un accès complet au niveau Gratuit de Machine Learning Studio décrit ci-dessus. Celui-ci inclut un espace de travail persistant et davantage de fonctionnalités complètes. Pour choisir l’expérience Machine Learning gratuite, cliquez sur le bouton **Commencer** de la page [https://studio.azureml.net](https://studio.azureml.net), puis sélectionnez **Accès invité** ou connectez-vous avec un compte Microsoft.

<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx
