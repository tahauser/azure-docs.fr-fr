---
title: 'Étape 5 : Déploiement du service web Machine Learning | Microsoft Docs'
description: 'Étape 5 de la procédure pas à pas de développement d’une solution prédictive : Déploiement d’une expérience prédictive en tant que service web dans Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3fca74a3-c44b-4583-a218-c14c46ee5338
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 416a6d1c151fae01a00d00282ab9118e7391fbb7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-5-deploy-the-azure-machine-learning-web-service"></a>Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning
Voici la cinquième étape de la procédure pas à pas [Développement d’une solution d’analyse prédictive avec Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Créer un espace de travail Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Télécharger des données existantes](walkthrough-2-upload-data.md)
3. [Créer une expérience](walkthrough-3-create-new-experiment.md)
4. [Former et évaluer les modèles](walkthrough-4-train-and-evaluate-models.md)
5. **Déployer le service web**
6. [Accéder au service web](walkthrough-6-access-web-service.md)

- - -
Pour que d’autres personnes puissent utiliser le modèle de prévision que nous avons développé dans cette procédure pas à pas, nous pouvons le déployer en tant que service web sur Azure.

Jusqu’à présent, nous avons réalisé l’expérience avec la formation de notre modèle. Mais le service déployé n’effectue plus l’apprentissage ; il va produire de nouvelles prédictions en évaluant l’entrée de l’utilisateur en fonction de notre modèle. Nous allons donc effectuer quelques préparatifs pour convertir cette expérience de ***i*** en expérience ***prédictive***. 

Ce processus comprend trois étapes :  

1. Supprimer l’un des modèles
2. Convertir l’*expérience de formation* que nous avons créée en *expérience prédictive*.
3. Déploiement de l’expérience prédictive sous la forme d’un service web

## <a name="remove-one-of-the-models"></a>Supprimer l’un des modèles

Tout d’abord, nous devons réduire un peu cette expérience. Nous disposons actuellement de deux modèles différents dans l’expérience, mais nous ne souhaitons utiliser qu’un seul modèle au moment de déployer cette expérience en tant que service web.  

Supposons que nous ayons décidé que le modèle Arbre de décision optimisé est plus adapté que le modèle SVM. La première chose à faire est de supprimer le module [Machine à vecteurs de support à deux classes][two-class-support-vector-machine], ainsi que les modules qui ont été utilisés pour sa formation. Vous pouvez d'abord copier l'expérience en cliquant sur **Enregistrer sous** dans la partie inférieure de la zone de dessin.

Nous devons supprimer les modules suivants :  

* [Machine à vecteurs de support à deux classes][two-class-support-vector-machine]
* Modules [Former le modèle][train-model] et [Noter le modèle][score-model] qui lui étaient connectés
* [Normaliser les données][normalize-data] (les deux)
* [Évaluer le modèle][evaluate-model] (puisque nous avons terminé d’évaluer les modèles)

Sélectionnez chaque module et appuyez sur la touche Suppr, ou cliquez avec le bouton droit sur le module puis sélectionnez **Supprimer**. 

![Modèle SVM supprimé][3a]

Notre modèle doit alors ressembler à ceci :

![Modèle SVM supprimé][3]

À présent, nous sommes prêts à déployer ce modèle avec le module [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree].

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir l'expérience de formation en expérience prédictive

Pour préparer ce modèle pour le déploiement, nous devons convertir cette expérience de formation d’une expérience prédictive. Cela implique trois étapes :

1. Enregistrer le modèle que nous avons formé, puis remplacer nos modules de formation
2. Réduire l’expérience en supprimant les modules uniquement nécessaires à l’apprentissage
3. Définir où le service web doit accepter l’entrée et où il génère la sortie

Nous pourrions effectuer cette opération manuellement, mais heureusement il est possible d’accomplir ces trois étapes en cliquant sur **Configurer le service web** dans la partie inférieure de la zone de dessin de l’expérience (sélectionnez l’option **Service web prédictif**).

> [!TIP]
> Pour en savoir plus sur ce qui se passe quand vous convertissez une expérience d’apprentissage en une expérience de prévision, consultez [Guide pratique pour préparer votre modèle au déploiement dans Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Lorsque vous cliquez sur **Configurer le service web**, plusieurs choses se produisent :

* Le modèle formé est converti en un module **Modèle formé** et stocké dans la palette de modules située à gauche de la zone de dessin de l’expérience (sous **Modèles formés**).
* Les modules qui ont été utilisés pour l’apprentissage sont supprimés :
  * [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree]
  * [Former le modèle][train-model]
  * [Fractionner les données][split]
  * Deuxième module [Exécuter le script R][execute-r-script] utilisé pour les données de test
* Le modèle formé enregistré est rajouté à l’expérience.
* Les modules **Entrée du service web** et **Sortie du service web** sont ajoutés (ils identifient où les données de l’utilisateur entrent dans le modèle, ainsi que les données renvoyées lors de l’accès au service web)

> [!NOTE]
> Vous constatez que l’expérience est enregistrée en deux parties, sous les onglets ajoutés en haut de la zone de dessin. L’expérience de formation d’origine se trouve sous l’onglet **Expérience de formation**, tandis que l’expérience prédictive tout juste créée est sous **Expérience prédictive**. L’expérience prédictive est celle que nous déployons sous la forme d’un service web.

Nous devons effectuer une étape supplémentaire avec cette expérience.
Nous avons ajouté deux modules [Exécuter le script R][execute-r-script] pour pondérer les données. Cette opération n’étant requise que pour la formation et le test, nous pouvons retirer ces modules du modèle final.
Machine Learning Studio a supprimé un module [Exécuter le script R][execute-r-script] lors de la suppression du module [Fractionner][split]. Nous pouvons maintenant supprimer l’autre et relier [Éditeur de métadonnées][metadata-editor] directement à [Noter le modèle][score-model].    

Notre expérience doit alors ressembler à cela :  

![Scoring the trained model][4]  

> [!NOTE]
> Vous vous demandez peut-être pourquoi nous avons laissé le jeu de données Données de carte de crédit allemande UCI dans l’expérience prédictive. Comme ce service va utiliser les données de l’utilisateur et non le jeu de données d’origine, pourquoi conserver ce dernier dans le modèle ?
> 
> Il est vrai que ce service n'a pas besoin des données de la carte de crédit d'origine. Mais il a besoin du schéma pour ces données, incluant des informations telles que le nombre de colonnes et lesquelles sont numériques. Ces informations sur le schéma sont indispensables pour interpréter les données de l’utilisateur. Nous laissons ces composants connectés de façon à ce que le module de notation comporte le schéma du jeu de données lorsque le service est en cours d’exécution. Les données ne sont pas utilisées, uniquement le schéma.  
> 
> 

Exécutez une dernière fois l’expérience (cliquez sur **Exécuter**). Si vous voulez vérifier que le modèle fonctionne toujours, cliquez sur la sortie du module [Noter le modèle][score-model] et sélectionnez **Afficher les résultats**. Vous constatez que les données d’origine sont affichées, ainsi que la valeur du risque sur le crédit (« Étiquettes notées ») et la probabilité de la notation (« Probabilités notées »). 

## <a name="deploy-the-web-service"></a>Déploiement du service web
Vous pouvez déployer l’expérience en tant que service web classique ou nouveau service web basé sur Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Déployer comme un service web classique
Pour déployer un service web classique dérivé de notre expérience, cliquez sur **Déployer le service web** sous la zone de dessin, puis sélectionnez **Déployer le service web [Classique]**. Machine Learning Studio déploie l’expérience en tant que service web et vous amène au tableau de bord associé à ce service web. Depuis cette page, vous pouvez revenir à l’expérience (**Afficher l’instantané** ou **Afficher les dernières**) et exécuter un test simple du service web (voir **Test du service web** ci-dessous). En outre, il contient des informations sur la création d’applications pouvant accéder au service web (l’étape suivante de cette procédure pas à pas aborde ce point plus en détail).

![Tableau de bord du service web][6]

Vous pouvez configurer le service en cliquant sur l'onglet **CONFIGURATION** . Vous pouvez modifier le nom du service (il s'agit par défaut du nom de l'expérience) et lui attribuer une description. Vous pouvez également attribuer des étiquettes plus significatives aux données d’entrée et de sortie.  

![Configuration du service web][5]  

### <a name="deploy-as-a-new-web-service"></a>Déployer comme un nouveau service web

> [!NOTE] 
> Pour déployer un nouveau service web, vous devez disposer d’autorisations suffisantes dans l’abonnement dans lequel vous déployez le service web. Pour en savoir plus, consultez la rubrique [Gérer un service web à l’aide du portail des services web Azure Machine Learning](manage-new-webservice.md). 

Pour déployer un nouveau service web dérivé de notre expérience :

1. Cliquez sur **Déployer le service web** sous la zone de dessin, puis sélectionnez **Déployer le service web [nouveau]**. Machine Learning Studio vous redirige vers la page **Deploy Experiment (Déployer l’expérience)** des services web Azure Machine Learning.

2. Entrez le nom du service web. 

3. Dans **Price Plan (Plan de tarification)**, choisissez un plan de tarification ou sélectionnez « Créer », nommez le nouveau plan et sélectionnez l’option de plan mensuel. Les niveaux de plan s’appliquent par défaut aux plans de votre région par défaut et votre service web est déployé dans cette région.

4. Cliquez sur **Déployer**.

Après quelques minutes, la page **Démarrage rapide** de votre service web s’ouvre.

Vous pouvez configurer le service en cliquant sur l’onglet **Configurer**. Dans cette page, vous pouvez modifier le nom du service et indiquer une description. 

Pour tester le service web, cliquez sur l’onglet **Tester** (consultez **Tester le service web** ci-dessous). Pour plus d’informations sur la création d’applications pouvant accéder au service web, cliquez sur l’onglet **Utiliser** (l’étape suivante de cette procédure pas à pas aborde ce point plus en détail).

> [!TIP]
> Vous pouvez mettre à jour le service web après l’avoir déployé. Par exemple, si vous souhaitez modifier votre modèle, modifiez l’expérience de formation, ajustez les paramètres du modèle, cliquez sur **Déployer le service web**, puis sélectionnez **Déployer le service web [Classic]** ou **Déployer le service web [Nouveau]**. Lorsque vous redéployez l’expérience, le service web est remplacé par votre modèle mis à jour.  
> 
> 

## <a name="test-the-web-service"></a>Test du service web

Dans le service web, les données de l’utilisateur transitent par le module **Entrée du service web** puis par le module [Noter le modèle][score-model] où elles sont notées. Selon notre configuration de l’expérience prédictive, le modèle attend des données dans le même format que le jeu de données de risque de crédit d’origine.
Les résultats sont renvoyés à l’utilisateur par le service web via le module **Sortie du service web**.

> [!TIP]
> Conformément à notre configuration de l’expérience prédictive, tous les résultats du module [Noter le modèle][score-model] sont renvoyés. Ces résultats incluent toutes les données d’entrée, ainsi que la valeur du risque de crédit et la probabilité de la notation. Mais vous pouvez renvoyer quelque chose de différent si vous le souhaitez. Par exemple, vous pouvez retourner simplement la valeur du risque de crédit. Pour ce faire, insérez un module [Colonnes de projets][project-columns] entre [Noter le modèle][score-model] et **Sortie du service web** pour éliminer les colonnes que vous ne souhaitez pas que le service web renvoie. 
> 
> 

Vous pouvez tester le service web Classique dans **Machine Learning Studio** ou dans le portail des **services web Azure Machine Learning**.
Vous pouvez tester un nouveau service web uniquement dans le portail **des services web Machine Learning**.

> [!TIP]
> Lors du test dans le portail des services web Azure Machine Learning, vous pouvez demander au portail de créer un échantillon de données pour tester le service Demande-Réponse. Dans la page **Configurer**, sélectionnez « Oui » pour **Échantillon de données activé ?**. Lorsque vous ouvrez l’onglet Demande-Réponse dans la page **Tester**, le portail intègre l’échantillon provenant du jeu de données Risque de crédit.

### <a name="test-a-classic-web-service"></a>Tester un service web classique

Vous pouvez tester un service web classique dans Machine Learning Studio ou dans le portail des services web Machine Learning. 

#### <a name="test-in-machine-learning-studio"></a>Tester dans Machine Learning Studio

1. Sur la page **TABLEAU DE BORD** du service web, cliquez sur le bouton **Test** sous **Point de terminaison par défaut**. La boîte de dialogue qui s’affiche vous demande les données d’entrée du service. Les colonnes sont identiques à celles du jeu de données d’origine (Risque de crédit).  

2. Entrez un jeu de données, puis cliquez sur **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Tester dans le portail des services web Machine Learning

1. Sur la page **TABLEAU DE BORD** du service web, cliquez sur le lien d’aperçu **Test** sous **Point de terminaison par défaut**. La page de test dans le portail des services web Azure Machine Learning pour le point de terminaison de service web s’ouvre en vous invitant à entrer les données du service. Les colonnes sont identiques à celles du jeu de données d’origine (Risque de crédit).

2. Cliquez sur **Tester Demande-réponse**. 

### <a name="test-a-new-web-service"></a>Tester un nouveau service web

Vous pouvez tester un nouveau service web uniquement dans le portail des services web Machine Learning.

1. Dans le portail des [services web Azure Machine Learning](https://services.azureml.net/quickstart), cliquez sur **Tester** en haut de la page. La page **Test** s’ouvre pour vous permettre d’entrer les données du service. Les champs d’entrée affichés correspondent à ceux du jeu de données d’origine (Risque de crédit). 

2. Entrez un jeu de données, puis cliquez sur **Test Request-Response**(Tester la requête-réponse).

Les résultats du test apparaissent sur le côté droit de la page, dans la colonne de sortie. 


## <a name="manage-the-web-service"></a>Gérer le service web

Une fois votre service web déployé, qu’il soit classique ou nouveau, vous pouvez le gérer dans le portail [des services web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart).

Pour surveiller les performances de votre service web :

1. Se connecter au [portail des services web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart)
2. Cliquez sur **Services web**.
3. Cliquer sur votre service web
4. Cliquez sur **Tableau de bord**.

- - -
**Étape suivante : [Accéder au service web](walkthrough-6-access-web-service.md)**

[3]: ./media/walkthrough-5-publish-web-service/publish3.png
[3a]: ./media/walkthrough-5-publish-web-service/publish3a.png
[4]: ./media/walkthrough-5-publish-web-service/publish4.png
[5]: ./media/walkthrough-5-publish-web-service/publish5.png
[6]: ./media/walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
