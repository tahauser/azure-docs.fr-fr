---
title: Configuration d’une machine virtuelle Geo Artificial Intelligence sur Azure | Microsoft Docs
description: Comment configurer une machine virtuelle Geo AI sur Azure.
keywords: apprentissage profond, IA, outils de science des données, machine virtuelle de science des données, analyse géospatiale
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 2994ef858e960640d98ab2f7d02a401b11aa9e8f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Configuration d’une machine virtuelle Geo Artificial Intelligence sur Azure 

La machine virtuelle de science des données Geo AI (Geo-DSVM) est une extension de la célèbre [machine virtuelle de science des données Azure](http://aka.ms/dsvm), spécialement configurée spécialement pour associer intelligence artificielle et analyse géospatiale. L’analyse géospatiale de la machine virtuelle est alimentée par [ArcGIS Pro](https://www.arcgis.com/features/index.html). La machine virtuelle de science des données permet d’effectuer un apprentissage rapide des modèles de Machine Learning, et même des modèles de Deep Learning, à partir de données enrichie par des informations géographiques. Elle est prise en charge uniquement sous Windows 2016 DSVM. 

La Geo-DSVM contient plusieurs outils dédiés à l’IA, notamment :

- les éditions GPU d’infrastructures de Deep Learning aussi populaires que Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer ; 
- des outils permettant d’acquérir et de prétraiter des données d’image ou des données textuelles ; 
- des outils dédiés aux activités de développement, tels que Microsoft R Server Developer Edition, Anaconda Python, les notebooks Jupyter pour Python et R, les IDE pour Python et R, des bases de données SQL ;
- le logiciel de bureau ArcGIS Pro d’ESRI, avec des interfaces Python et R prenant en charge les données géospatiales de vos applications IA. 


## <a name="create-your-geo-ai-data-science-vm"></a>Créer une machine virtuelle de science des données Geo AI

Voici la procédure permettant de créer une instance de la machine virtuelle de science des données Geo AI : 


1. Accédez à la liste des machines virtuelles présentes sur le [portail Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Sélectionnez le bouton **Créer** au bas de l’écran pour accéder à un Assistant.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. L’Assistant utilisé pour créer l’instance Geo-DLVM nécessite les **entrées** de chacune des **quatre étapes** énumérées à droite de cette figure. Voici les entrées nécessaires à la configuration de chacune de ces étapes :



   - **Concepts de base**

      1. **Nom** : nom du serveur de science des données que vous créez.

      2. **Nom d’utilisateur**: identifiant de connexion du compte administrateur.

      3. **Mot de passe**: mot de passe du compte administrateur.

      4. **Subscription**(Abonnement) : si vous disposez de plusieurs abonnements, sélectionnez celui qui sera associé à la création et à la facturation de la machine.

      5. **Groupe de ressources** : vous pouvez en créer un nouveau ou utiliser un groupe de ressources Azure **vide** existant dans votre abonnement.

      6. **Location**(Emplacement) : sélectionnez le centre de données qui convient le mieux. Généralement, il s’agit du centre de données qui héberge la plupart de vos données ou du centre de données le plus proche de votre emplacement physique afin d’accélérer l’accès au réseau Si vous souhaitez effectuer un Deep Learning sur un GPU, vous devez choisir un emplacement dans Azure qui contient des instances de machines virtuelles GPU de la série NC. Les régions qui comprennent des machines virtuelles GPU sont les suivantes : **Est des États-Unis, Nord du centre des États-Unis, Sud-Centre des États-Unis, États-Unis de l'Ouest 2, Europe du Nord, Europe de l’Ouest**. Pour obtenir la dernière liste en date, accédez à la [page Disponibilité des produits par région](https://azure.microsoft.com/regions/services/), puis recherchez **NC-Series** sous **Compute**. 


   - **Paramètres** : sélectionnez l’une des tailles de machine virtuelle GPU série NC si vous projetez d’exécuter un Deep Learning sur un GPU de votre Geo-DSVM. Sinon, vous pouvez choisir une instance basée sur une UC.  Créez un compte de stockage pour votre machine virtuelle. 
   
   - **Résumé**: vérifiez que toutes les informations que vous avez saisies sont correctes.

   - **Acheter** : cliquez sur **Acheter** pour démarrer l’approvisionnement. Les conditions de service vous sont communiquées via un lien. La machine virtuelle n'est pas assortie de frais supplémentaires au-delà du calcul de la taille de serveur que vous avez choisie à l'étape **Taille** . 

>[!NOTE]
> L’approvisionnement prend environ 20 à 30 minutes. L’état de l’approvisionnement est affiché sur le portail Azure.


## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Accès à une machine virtuelle de science des données Geo AI

Une fois votre machine virtuelle créée, vous pouvez commencer à utiliser les outils qui y sont installés et préconfigurés. Pour la plupart des outils, vous disposez d'icônes de bureau et de vignettes dans le menu de démarrage. Vous pouvez vous y connecter à l’aide du bureau distant en utilisant les informations d’identification du compte administrateur que vous avez configurées dans la section **Paramètres de base** précédente. 


## <a name="using-arcgis-pro-installed-in-the-vm"></a>Utilisation du logiciel ArcGIS Pro installé sur la machine virtuelle

Le logiciel de bureau ArcGIS Pro est déjà préinstallé sur la Geo-DSVM et l’environnement est préconfiguré pour fonctionner avec tous les outils contenus dans la DSVM. Au démarrage d’ArcGIS, vous êtes invité à vous connecter à votre compte ArcGIS. Si vous disposez déjà d’un compte ArcGIS et que vous possédez des licences pour ce logiciel, vous pouvez utiliser vos informations d’identification existantes.  

![Connexion à ArcGIS](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Sinon, vous pouvez inscrire à un nouveau compte ArcGIS et demander une licence ou obtenir une [version d’évaluation gratuite](https://www.arcgis.com/features/free-trial.html). 

![Évaluation gratuite d’ArcGIS](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Dès lors que vous êtes connecté à un compte ArcGIS (payant ou en version d’évaluation gratuite), vous pouvez autoriser ArcGIS Pro pour votre compte en suivant les instructions décrites dans la [documentation de mise en route d’ArcGIS Pro](http://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Une fois connecté au bureau ArcGIS Pro avec votre compte ArcGIS, vous pouvez commencer à utiliser les outils de science des données installés et configurés sur la machine virtuelle dans le cadre de vos projets d’analyse géospatiale et de Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser la machine virtuelle de science des données Geo AI en lisant les rubriques suivantes :

* [Use the Geo AI Data Science VM](use-geo-ai-dsvm.md) (Utiliser la machine virtuelle de sciences des données Geo AI)