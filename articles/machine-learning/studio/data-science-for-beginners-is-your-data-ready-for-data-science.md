---
title: "Vos données sont-elles prêtes pour la science des données ? Évaluation des données - Azure Machine Learning | Microsoft Docs"
description: "Quatre critères que vos données doivent respecter pour être prêtes pour la science des données. Cette vidéo contient des exemples concrets pour faciliter l’évaluation de données de base."
keywords: "données pertinentes,évaluer des données,préparer les données,critères de données,données prêtes"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: 4ab9462c4cc4573717450ce48028807960cecee9
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="is-your-data-ready-for-data-science"></a>Vos données sont-elles prêtes pour la science des données ?
## <a name="video-2-data-science-for-beginners-series"></a>Vidéo 2 : série Science des données pour les débutants
Découvrez comment évaluer vos données pour vous assurer qu’elles répondent aux critères de base pour la science des données.

Pour tirer le meilleur parti de la série, regardez l’ensemble des vidéos. [Accéder à la liste des vidéos](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Autres vidéos de cette série
*Science des données pour les débutants* offre une introduction rapide à la science des données en cinq petites vidéos.

* Vidéo 1 : [Les 5 questions auxquelles la science des données répond](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sec)*
* Vidéo 2 : Vos données sont-elles prêtes pour la science des données ?
* Vidéo 3 : [Poser une question à laquelle les données permettent de répondre](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sec)*
* Vidéo 4 : [Prédire une réponse à l’aide d’un modèle simple](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sec)*
* Vidéo 5 : [Copier le travail d’autres personnes pour des projets de science des données](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sec)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Transcription : Vos données sont-elles prêtes pour la science des données ?
Bienvenue dans « Vos données sont-elles prêtes pour la science des données ? », la seconde vidéo de la série *Science des données pour les débutants*.  

Avant que la science des données ne puisse vous fournir les réponses souhaitées, vous devez lui donner des matériaux bruts avec lesquels elle pourra travailler. C’est comme pour préparer une pizza : plus les ingrédients sont excellents, plus la qualité du produit fini est bonne. 

## <a name="criteria-for-data"></a>Critères pour les données
Par conséquent, dans le cas de la science des données, vous avez besoin de certains ingrédients.

Nous avons besoin de données :

* Pertinentes
* Connecté
* Précises
* Suffisantes

## <a name="is-your-data-relevant"></a>Vos données sont-elles pertinentes ?
Le premier ingrédient est donc des données pertinentes.

![Données pertinentes et données non pertinentes - évaluation des données](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Examinez le tableau sur la gauche. Nous avons rencontré sept personnes à la sortie de bars à Boston, mesuré leur taux d’alcool dans le sang, la moyenne à la batte des Red Sox lors de leur dernier match et le prix du lait dans le magasin le plus proche.

Il s’agit de données parfaitement légitimes. Le seul problème est qu’elles ne sont pas pertinentes. À première vue, il n’existe aucune relation évidente entre ces chiffres. Si je vous donne le prix actuel du lait et la moyenne à la batte des Red Sox, il n’existe aucun moyen vous permettant de deviner mon taux d’alcool dans le sang.

Maintenant, examinez le tableau sur la droite. Cette fois, nous avons mesuré la masse corporelle de chaque personne et compté le nombre de boissons qu’elles ont consommé.  Les chiffres dans chaque ligne sont désormais pertinents une fois mis en relation. Si je vous ai donné mon indice de masse corporelle et le nombre de margaritas que j’ai bus, vous pouvez estimer mon taux d’alcool dans le sang.

## <a name="do-you-have-connected-data"></a>Avez-vous des données connectées ?
L’ingrédient suivant est des données connectées.

![Données connectées et données non connectées - critères de données, données prêtes](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Voici certaines données sur la qualité des hamburgers : la température du grill, le poids du steak et le classement dans le magasine culinaire local. Mais notez les espaces vides de la table de gauche.

La plupart des jeux de données n’ont pas de valeurs. C’est un problème commun qu’il est possible de contourner. Mais si trop de valeurs manquent, vos données commencent à ressembler à gruyère.

Si vous examinez le tableau sur la gauche, il manque tant de données qu’il est difficile de trouver n’importe quel type de relation entre la température du grill et le poids du steak. Il s’agit d’un exemple de données déconnectées.

La table de droite, cependant, est remplie (un exemple de données connectées).

## <a name="is-your-data-accurate"></a>Vos données sont-elles précises ?
L’ingrédient suivant dont nous avons besoin est la précision. Voici les quatre cibles à atteindre.

![Données précises et des données inexactes - critères de données](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Examinez la cible dans l’angle supérieur droit. Nous avons un regroupement serré autour du cercle concentrique. Ces données sont bien sûr précises. Curieusement, dans la langue de la science des données, les données de la cible en bas à droite sont également considérées comme précises.

Si vous mappez le centre de ces flèches, vous voyez qu’il est très proche du cercle concentrique. Les flèches sont réparties autour de la cible. Elles sont donc considérées comme non précises. Mais elles sont centrées autour du cercle concentrique et peuvent donc être considérées comme précises.

À présent, examinez la cible de l’angle supérieur gauche. Ici, nos flèches sont très proches, regroupées étroitement. Elles sont précises, mais inexactes, car le centre est la sortie du cercle concentrique. Et, bien entendu, les flèches de la cible en bas à gauche sont inexactes et non précises. Cet archer a besoin de plus de pratique.

## <a name="do-you-have-enough-data-to-work-with"></a>Disposez-vous d’assez de données à analyser ?
Enfin, le quatrième ingrédient : nous avons besoin d’assez de données.

![Disposez-vous d’assez de données pour une analyse ? Évaluation des données](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Considérez chaque point de données dans votre table comme un trait dans un dessin. Si vous ne faites que quelques traits, la peinture peut être assez floue. Il est difficile de déterminer ce qu’elle représente.

Si vous ajoutez quelques traits, votre peinture devient plus nette.

Lorsque vous avez à peine suffisamment de traits, vous voyez juste assez pour prendre certaines décisions générales. Est-ce un endroit que j’aimerais visiter ? Il y fait clair, l’eau semble propre : oui, c’est là où je vais partir en vacances.

Si vous ajoutez davantage de données, le tableau devient plus clair et vous pouvez prendre des décisions plus détaillées. Maintenant, je peux regarder les trois hôtels sur la rive gauche. Vous savez, j’aime vraiment l’architecture de celui qui se trouve au premier plan. Je réserve, au troisième étage.

Avec assez de données pertinentes, connectées et précises, nous disposons de tous les ingrédients dont nous avons besoin pour une science des données de pointe.

Nous vous invitons à consulter les quatre autres vidéos de la série *« Science des données pour les débutants »* de Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Étapes suivantes
* [Menez une première expérience de science des données avec Machine Learning Studio](create-experiment.md)
* [Consultez la présentation de Machine Learning sur Microsoft Azure](what-is-machine-learning.md)
