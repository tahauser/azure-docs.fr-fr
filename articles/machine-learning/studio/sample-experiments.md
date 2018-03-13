---
title: "Créer des expériences à partir des exemples d’apprentissage automatique - Azure | Microsoft Docs"
description: "Découvrez comment utiliser les exemples d’expérience d’apprentissage automatique pour créer des expériences avec la galerie Azure AI et Azure Machine Learning."
keywords: "exemples d’apprentissage automatique, exemple d’expérience, exemple d’apprentissage automatique, exemples de AI"
services: machine-learning
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: 81e6c1d8-682c-4db3-bfd5-d7bfb1150ff3
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2018
ms.author: cgronlun
ms.openlocfilehash: 9ea19f137acb23d3527315aae2624c746e138545
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="create-machine-learning-experiments-from-working-examples-in-azure-ai-gallery"></a>Créer des expériences d’apprentissage automatique à partir des exemples de travail dans la galerie AI Azure

Découvrez comment démarrer avec des exemples d’expérience de la [galerie Azure AI](https://gallery.cortanaintelligence.com/) au lieu de créer des expériences d’apprentissage automatique de toutes pièces. Vous pouvez utiliser les exemples pour créer votre propre solution d’apprentissage automatique.

La galerie contient des exemples d’expérience de l’équipe Microsoft Azure Machine Learning, ainsi que des exemples partagés par la communauté Machine Learning. Vous pouvez poser des questions ou publier des commentaires sur les expériences.

Pour savoir comment utiliser la galerie, regardez la vidéo de 3 minutes [Copier le travail d’autres personnes pour des projets de science des données](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) issue de la série [Science des données pour les débutants](data-science-for-beginners-the-5-questions-data-science-answers.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="find-an-experiment-to-copy-in-azure-ai-gallery"></a>Rechercher une expérience à copier dans la galerie AI Azure
Pour afficher les expériences disponibles dans la galerie, rendez-vous dans la [Galerie](https://gallery.cortanaintelligence.com/) , puis cliquez sur **Expériences** en haut de la page.

### <a name="find-the-newest-or-most-popular-experiments"></a>Rechercher les expériences les plus récentes ou les plus populaires
Dans cette page, vous pouvez afficher les expériences **Recently added** (Récemment ajoutées), consulter la section **What’s popular** (Les plus demandées) ou découvrir les dernières **expériences Microsoft les plus populaires**.

### <a name="look-for-an-experiment-that-meets-specific-requirements"></a>Rechercher une expérience qui remplit les conditions requises
Pour parcourir toutes les expériences :

1. Cliquez sur **Browse all** (Parcourir tout) en haut de la page.
2. À gauche, dans la section **Catégories**, sous **Affiner par**, sélectionnez **Tester** pour afficher toutes les expériences de la galerie.
3. Vous y trouverez des expériences qui répondent à vos besoins de différentes façons :
   * **Sélectionnez des filtres à gauche.** Par exemple, pour parcourir les expériences utilisant un algorithme de détection d’anomalie PCA : sous **Catégories**, cliquez sur **Tester**. Ensuite, sous **Algorithmes utilisés**, cliquez sur **Afficher tout** et choisissez **Détection d’anomalie PCA** dans la boîte de dialogue. Vous devrez peut-être faire défiler le voir.<br></br>
     ![Sélectionner des filtres](./media/sample-experiments/choose-an-algorithm.png)
   * **Utilisez la zone de recherche.** Par exemple, pour rechercher les expériences partagées par Microsoft sur la reconnaissance de chiffres et qui utilisent un algorithme de machine à vecteurs de support à deux classes, entrez « reconnaissance de chiffres » dans la zone de recherche. Ensuite, sélectionnez les filtres **Experiment** (Expérience), **Microsoft content only** (Contenu Microsoft uniquement) et **Two-Class Support Vector Machine** (Machine à vecteurs de support à deux classes) :<br></br>
     ![Utiliser la zone de recherche](./media/sample-experiments/search-for-experiments.png)
4. Cliquez sur une expérience pour en savoir plus à propos de celle-ci.
5. Pour exécuter et/ou modifier l’expérience, cliquez sur **Ouvrir dans Studio** sur la page de l’expérience. <br></br>

    ![Exemple d'expérience](./media/sample-experiments/example-experiment.png)

    > [!NOTE]
    > Lorsque vous ouvrez une expérience dans Machine Learning Studio pour la première fois, vous pouvez l’essayer gratuitement ou souscrire un abonnement Azure. [En savoir plus sur les différences entre l’essai gratuit et le service payant de Machine Learning Studio](https://azure.microsoft.com/pricing/details/machine-learning/)
    >
    >

## <a name="create-a-new-experiment-using-an-example-as-a-template"></a>Créer une expérience à l’aide d’un exemple en tant que modèle
Vous pouvez également créer une expérience dans Machine Learning Studio en utilisant un exemple de la galerie comme modèle.

1. Connectez-vous à [Studio](https://studio.azureml.net) avec les informations d’identification de votre compte Microsoft, puis cliquez sur **Nouveau** pour créer une expérience.
2. Parcourez les exemples de contenu, puis cliquez sur l’un d’entre eux.

Une expérience est créée dans votre espace de travail Machine Learning Studio à l’aide de l’exemple d’expérience en tant que modèle.

## <a name="next-steps"></a>Étapes suivantes
* [Importer des données à partir de diverses sources](import-data.md)
* [Didacticiel de démarrage rapide sur le langage R pour Machine Learning](r-quickstart.md)
* [Déploiement d’un service web Machine Learning](publish-a-machine-learning-web-service.md)
