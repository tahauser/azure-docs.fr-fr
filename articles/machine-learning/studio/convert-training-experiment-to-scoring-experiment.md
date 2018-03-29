---
title: 'Guide pratique : préparer un modèle au déploiement dans Azure Machine Learning Studio | Microsoft Docs'
description: Comment préparer votre modèle entraîné au déploiement sous forme de service web en convertissant votre expérience de formation Machine Learning Studio en une expérience prédictive.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 45b8e8812c2c63d60ccdb143656cca9f0dade183
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Guide pratique : préparer un modèle au déploiement dans Azure Machine Learning Studio

Azure Machine Learning Studio vous donne les outils nécessaires pour développer un modèle d’analyse prédictive, puis le faire fonctionner en le déployant sous forme de service web Azure.

Pour cela, vous utiliserez Studio afin de créer une expérience, appelée *expérience de formation*, dans laquelle vous entraînerez, évaluerez et modifierez votre modèle. Une fois satisfait, vous préparerez votre modèle au déploiement en convertissant votre expérience de formation en une *expérience prédictive* configurée pour évaluer les données utilisateur.

Vous trouverez un exemple de ce processus à la page [Procédure pas à pas : développer une solution d’analyse prédictive pour l’évaluation des risques de crédit dans Azure Machine Learning](walkthrough-develop-predictive-solution.md).

Cet article étudie en détail la façon dont une expérience de formation est convertie en une expérience prédictive et dont cette expérimentation prédictive est déployée. Ces informations vous permettront d’apprendre à configurer votre modèle déployé de façon à le rendre plus efficace.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Vue d'ensemble 

Le processus de conversion d’une expérience d’apprentissage en expérience prédictive comprend trois étapes :

1. Remplacez les modules d’algorithmes de Machine Learning par votre modèle entraîné.
2. Découpez l’expérience pour ne garder que les modules nécessaires au calcul des notations. Une expérience de formation inclut un certain nombre de modules nécessaires pour la formation, mais qui ne sont plus requis une fois le modèle entraîné.
3. Définissez dans quelle mesure votre modèle acceptera les données de l’utilisateur du service web et choisissez le type des données qui seront renvoyées.

> [!TIP]
> Dans votre expérience de formation, vous vous êtes concentré sur la formation et la notation de votre modèle avec vos propres données. Mais, une fois qu’il sera déployé, les utilisateurs enverront de nouvelles données à votre modèle, et il renverra des résultats de prédiction. Par conséquent, lorsque vous convertirez l’expérience de formation en une expérience prédictive pour la préparer au déploiement, n’oubliez pas la façon dont le modèle sera utilisé par d’autres personnes.
> 
> 

## <a name="set-up-web-service-button"></a>Définir le bouton de service web
Après avoir exécuté votre expérience (cliquez sur **EXÉCUTER** en bas du canevas de l’expérience), cliquez sur **Configurer le service web** (sélectionnez l’option **Service web prédictif**). **Configurer le service web** effectue les trois étapes de conversion de votre expérience de formation en une expérience prédictive :

1. Il enregistre votre modèle entraîné dans la section **Modèles entraînés** de la palette du module (à gauche du canevas de l’expérience). Il remplace ensuite l’algorithme de Machine Learning et les modules [Train Model][train-model] par le modèle entraîné enregistré.
2. Il analyse votre expérience et supprime les modules qui ont clairement été utilisés exclusivement pour la formation et ne sont plus nécessaires.
3. Il insère les modules _Web service input_ et de _Web service output_ aux emplacements par défaut de votre expérience (ces modules acceptent et renvoient des données utilisateur).

Par exemple, l’expérience suivante effectue l’apprentissage d’un modèle d’arborescence de décision augmenté incluant deux classes, au moyen des données de recensement :

![expérience de formation][figure1]

Les modules de cette expérience effectuent quatre fonctions de base :

![Fonctions du module][figure2]

Lorsque vous convertissez cette expérience de formation en une expérience prédictive, certains de ces modules ne sont plus nécessaires ou sont à présent utilisés à une autre fin :

* **Data** : les données de cet exemple de jeu de données ne sont pas utilisées pour le calcul de la notation ; l’utilisateur du service web fournit les données à noter. Toutefois, les métadonnées de ce jeu de données, comme les types de données, sont utilisées par le modèle formé. Vous devez donc conserver le jeu de données dans l’expérience prédictive, afin qu’il puisse fournir ces métadonnées.

* **Prep** : selon les données utilisateur qui seront soumises pour la notation, ces modules ne seront pas forcément nécessaires pour le traitement des données entrantes. Le bouton **Configurer le service web** ne les affecte pas : il vous faut décider comment vous souhaitez les gérer.
  
    Notamment, cet exemple de jeu de données peut avoir des valeurs manquantes : un module [Clean Missing Data][clean-missing-data] a donc été inclus pour les gérer. En outre, l’exemple de jeu de données comprend des colonnes qui ne sont pas nécessaires pour effectuer l’apprentissage du modèle. Par conséquent, un module [Select Columns in Dataset][select-columns] a été inclus afin d’exclure ces colonnes supplémentaires du flux de données. Si vous savez qu’aucune donnée ne manque parmi les données qui seront soumises à des fins de calcul de la notation via le service web, vous pouvez retirer le module [Clean Missing Data][clean-missing-data]. Toutefois, étant donné qu’il permet de définir les colonnes de données attendues par le modèle entraîné, le module [Select Columns in Dataset][select-columns] doit être conservé.

* **Train** : ces modules sont utilisés pour former le modèle. Lorsque vous cliquez sur **Configurer le service web**, ces modules sont remplacés par un module unique qui contient le modèle entraîné. Ce nouveau module est enregistré dans la section **Modèles formés** de la palette des modules.

* **Score** : dans cet exemple, le module [Split Data][split] est utilisé pour diviser le flux de données en données de test, d’une part, et données d’apprentissage, d’autre part. Dans l’expérience prédictive, nous n’effectuons plus l’apprentissage ; nous pouvons donc supprimer [Split Data][split]. De même, le deuxième module [Score Model][score-model] et le module [Evaluate Model][evaluate-model] sont utilisés pour comparer les résultats à partir des données de test. Ils ne sont donc pas nécessaires à l’expérience prédictive. Le module [Score Model][score-model] restant est cependant requis pour renvoyer le résultat de la notation par le biais du service web.

Voici comment notre exemple apparaît une fois que vous avez cliqué sur **Définir un service Web**:

![Expérience prédictive convertie][figure3]

Le travail effectué par **Configurer le service web** peut être suffisant pour préparer votre expérience à être déployée en tant que service web. Toutefois, certaines tâches supplémentaires, spécifiques à votre expérience, seront peut-être à prévoir.

### <a name="adjust-input-and-output-modules"></a>Ajuster des modules d’entrée et de sortie
Dans votre expérience d’apprentissage, vous avez utilisé un ensemble de données d’apprentissage, puis effectué un certain traitement pour obtenir les données au format requis par l’algorithme ML. S’il n’est pas nécessaire de soumettre à ce traitement les données que vous vous attendez à recevoir via le service web, vous pouvez le contourner : connectez la sortie du **module Web service input** à un autre module de votre expérience. Les données de l’utilisateur arrivent à présent à cet emplacement du modèle.

Par exemple, le bouton **Définir un service web** place par défaut le module **Web service input** en haut de votre flux de données, comme l’indique la figure ci-dessus. Mais nous pouvons positionner manuellement le module **Web service input** au-delà des modules de traitement des données :

![Déplacement de l’entrée du service web][figure4]

Les données d’entrée fournies via le service web accéderont directement au module Score Model, sans être soumises à un traitement préalable.

De même, par défaut, **Définir un service Web** place le module Web service output en bas de votre flux de données. Dans cet exemple, le service web renvoie à l’utilisateur la sortie du module [Score Model][score-model], qui inclut la totalité du vecteur de données d’entrée, ainsi que les résultats de la notation.
Toutefois, si vous préférez renvoyer quelque chose de différent, vous pouvez ajouter des modules supplémentaires avant le modules **Web service output**. 

Par exemple, pour renvoyer uniquement les résultats de la notation et non la totalité du vecteur de données d’entrée, ajoutez un module [Select Columns in Dataset][select-columns] pour exclure toutes les colonnes, sauf les résultats de la notation. Ensuite, déplacez le module **Web service output** vers la sortie du module [Select Columns in Dataset][select-columns]. L’expérience se présente ainsi :

![Déplacement de la sortie du service web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Ajouter ou supprimer des modules de traitement des données supplémentaires
Si votre expérience inclut un plus grand nombre de modules que nécessaire pour le calcul de la notation, vous pouvez en supprimer. Par exemple, étant donné que nous avons déplacé le module **Web service input** vers un point situé après les modules de traitement des données, nous pouvons supprimer le module [Clean Missing Data][clean-missing-data] de l’expérience prédictive.

Notre expérience de notation ressemble à présent à ce qui suit :

![Suppression du module supplémentaire][figure6]


### <a name="add-optional-web-service-parameters"></a>Ajouter des paramètres de service web facultatifs
Dans certains cas, vous voudrez permettre à l’utilisateur de votre service web de modifier le comportement des modules en cas d’accès au service. *paramètres de service Web* .

Un exemple courant consiste à configurer un module [Import Data][import-data], afin que l’utilisateur du service web publié puisse spécifier une autre source de données lors de l’accès au service web. Il est également possible de configurer un module [Exporter les données][export-data] de façon à spécifier une destination différente.

Vous pouvez définir des paramètres de service web et les associer à un ou plusieurs paramètres de module, en spécifiant s’ils sont obligatoires ou facultatifs. L’utilisateur du service web fournit ensuite des valeurs pour ces paramètres lors de l’accès au service ; les actions de module sont modifiées en conséquence.

Pour en savoir plus sur les paramètres de service web et leur utilisation, consultez la page [Utilisation des paramètres de service web Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Déploiement de l’expérience prédictive sous la forme d’un service web
Maintenant que l’expérience prédictive est correctement préparée, vous pouvez la publier en tant que service web Azure. En accédant au service web, les utilisateurs peuvent envoyer des données à votre modèle, qui renvoie alors ses prédictions.

Pour en savoir plus sur le processus de déploiement complet, consultez la page [Déploiement d’un service web Machine Learning Azure][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
