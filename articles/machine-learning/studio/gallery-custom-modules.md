---
title: Modules personnalisés de la galerie Azure AI | Microsoft Docs
description: Découvrez les modules Machine Learning personnalisés dans la galerie Azure AI.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.openlocfilehash: 26cd814980af0824cbf40052a4a9f47161b8cc62
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="discover-custom-machine-learning-modules-in-azure-ai-gallery"></a>Découvrir les modules Machine Learning personnalisés dans la galerie Azure AI
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Modules personnalisés pour Machine Learning Studio
La galerie Azure AI offre plusieurs [modules personnalisés](https://gallery.cortanaintelligence.com/customModules) qui étendent les fonctionnalités d’Azure Machine Learning Studio. Vous pouvez importer ces modules pour les utiliser dans vos expériences. Vous pouvez ainsi développer des solutions d’analyse prédictive encore plus avancées.

Pour l’instant, la galerie propose des modules sur l’*analytique de série temporelle*, les *règles d’association*, les *algorithmes de clustering* (au-delà de k-means) et les *visualisations*, ainsi que d’autres modules utilitaires efficaces.


## <a name="discover"></a>Découvrez
Pour parcourir les modules personnalisés [dans la galerie](http://gallery.cortanaintelligence.com), allez dans **Autres** et sélectionnez **Modules personnalisés**.

![Sélectionner Modules personnalisés sur la page d’accueil de la galerie](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

La page **[Modules personnalisés](https://gallery.cortanaintelligence.com/customModules)** affiche la liste des modules récemment ajoutés et populaires. Pour afficher tous les modules personnalisés, sélectionnez le bouton **Afficher tout**. Pour rechercher un module personnalisé spécifique, sélectionnez **Afficher tout**, puis sélectionnez les critères de filtre. Vous pouvez également entrer des termes de recherche dans la zone **Recherche** située en haut de la page de la galerie.

![Sélectionnez « Afficher tout » pour parcourir tous les modules personnalisés](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Comprendre

Afin de comprendre comment fonctionne un module personnalisé publié, sélectionnez le module personnalisé pour ouvrir la page de détails du module. La page de détails délivre une expérience d’apprentissage cohérente et informative. Par exemple, la page de détails met en avant l’objectif du module et répertorie les entrées, sorties et paramètres attendus. La page de détails comprend également un lien vers le code source sous-jacent que vous pouvez examiner et personnaliser.

### <a name="comment-and-share"></a>Commenter et partager
Dans la section **Commentaires** de la page de détails d’un module personnalisé, vous pouvez commenter, donner un avis ou poser des questions sur le module. Vous pouvez même partager le module avec vos amis ou collègues sur Twitter ou LinkedIn. Vous pouvez également envoyer un lien par e-mail vers la page de détails du module afin d’inviter d’autres utilisateurs à consulter cette page.

![Partager cet élément avec des amis](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Ajouter vos propres commentaires](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importer
Vous pouvez importer n’importe quel module personnalisé dans la galerie pour vos propres expériences.

La galerie Azure AI propose deux méthodes pour importer une copie du module :

* **À partir de la galerie**. Lorsque vous importez un module personnalisé depuis la galerie, vous obtenez également un exemple d’expérience qui vous montre comment utiliser le module.
* **À partir de Machine Learning Studio**. Vous pouvez importer n’importe quel module personnalisé lorsque vous travaillez dans Machine Learning Studio (dans ce cas, vous n’obtenez pas l’exemple d’expérience).

### <a name="from-the-gallery"></a>À partir de la galerie

1. Dans la galerie, ouvrez la page de détails du module. 
2. Sélectionnez **Ouvrir dans Studio**.
   
    ![Ouvrez le module personnalisé de la Galerie.](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
Chaque module personnalisé inclut un exemple d’expérience qui montre comment utiliser le module. Lorsque vous sélectionnez **Ouvrir dans Studio**, l’exemple d’expérience s’ouvre dans votre espace de travail Machine Learning Studio. (Si vous n’êtes pas déjà connecté à Studio, vous êtes invité à vous connecter une première fois avec votre compte Microsoft.)

En plus de l’exemple d’expérience, le module personnalisé est copié dans votre espace de travail. Il est également placé dans votre palette de modules, qui comprend tous vos modules Machine Learning Studio intégrés ou personnalisés. Vous pouvez maintenant l’utiliser dans vos propres expériences, comme tout autre module de votre espace de travail.

### <a name="from-within-machine-learning-studio"></a>À partir de Machine Learning Studio

1. Dans Machine Learning Studio, sélectionnez **NOUVEAU**.
2. Sélectionnez **Module**. Vous pouvez faire votre choix dans la liste des modules de la galerie ou rechercher un module précis à l’aide de la zone **Recherche**.
3. Pointez votre souris sur un module, puis sélectionnez **Importer le module**. (Pour en savoir plus sur le module, sélectionnez **Afficher dans la galerie**. Vous accéderez à la page de détails du module dans la galerie.)
   
    ![Importer un module personnalisé dans Machine Learning Studio](./media/gallery-custom-modules/add-custom-module-in-studio.png)

Le module personnalisé est copié dans votre espace de travail et placé dans votre palette de modules, qui comprend vos modules Machine Learning Studio intégrés ou personnalisés. Vous pouvez maintenant l’utiliser dans vos propres expériences, comme tout autre module de votre espace de travail.

## <a name="use"></a>Utilisation

Quelle que soit la méthode que vous choisissez pour importer un module personnalisé, lorsque vous importez le module, celui-ci est placé dans votre palette de modules dans Machine Learning Studio. À partir de votre palette de modules, vous pouvez utiliser le module personnalisé dans n’importe quelle expérience de votre espace de travail, comme tout autre module.

Pour utiliser un module importé :

1. Créez une expérience ou ouvrez une expérience existante.
2. Pour développer la liste des modules personnalisés dans votre espace de travail, sélectionnez **Personnalisés** dans la palette. La palette est située à gauche du canevas d’expérience.
   
    ![Liste des modules personnalisés dans la palette Studio](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. Sélectionnez le module que vous avez importé et faites-le glisser vers votre expérience.


**[Accéder à la galerie](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

