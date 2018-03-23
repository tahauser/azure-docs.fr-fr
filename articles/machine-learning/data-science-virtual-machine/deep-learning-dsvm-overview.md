---
title: Introduction à la machine virtuelle DLVM - Azure | Microsoft Docs
description: Principaux scénarios et composants d’analytique pour les machines virtuelles DLVM.
keywords: apprentissage profond, IA, outils de science des données, machine virtuelle dsvm, outils pour la science des données, science des données linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: f74b5c68c2c04eebc1d6f1e59fac8cbb11fef46a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introduction à la machine virtuelle DLVM

## <a name="why-deep-learning-virtual-machine"></a>Pourquoi une machine virtuelle DLVM ? 

Les algorithmes d’apprentissage profond (deep learning) et les réseaux neuronaux sont aujourd’hui l’une des méthodes les plus couramment utilisées pour résoudre de nombreux problèmes d’apprentissage automatique. Ils sont particulièrement performants pour des tâches cognitives comme la compréhension d’image, de texte, audio/vidéo, car ils se rapprochent souvent des niveaux cognitifs humains dans certains domaines spécifiques avec des architectures de réseaux neuronaux profonds et un accès à des jeux de données volumineux pour former des modèles. L’apprentissage profond nécessite une grande puissance de calcul pour former les modèles avec ces jeux de données volumineux. Avec le cloud et la disponibilité de processeurs graphiques (GPU, Graphical Processing Units), il devient possible de générer des architectures neuronales profondes et sophistiquées, puis de les former sur un grand jeu de données d’une puissante infrastructure de calcul dans le cloud.  La machine virtuelle [DSVM](overview.md) (Data Science Virtual Machine) offre un ensemble complet d’outils et d’exemples pour la préparation des données, l’apprentissage automatique et l’apprentissage profond. Toutefois, l’un des défis auxquels sont confrontés les utilisateurs consiste à découvrir facilement les outils et les exemples pour des scénarios spécifiques comme l’apprentissage profond et encore plus facilement pour le provisionnement des instances de machine virtuelle basées sur GPU. Cette machine virtuelle DLVM (Deep Learning Virtual Machine) relève ces défis. 

## <a name="what-is-deep-learning-virtual-machine"></a>Qu’est-ce qu’une machine virtuelle DLVM ? 
La machine virtuelle DLVM est une variante de la machine virtuelle [DSVM](overview.md) (Data Science Virtual Machine). Elle a été spécialement configurée pour faciliter l’utilisation des instances de machines virtuelles basées sur GPU visant à former des modèles d’apprentissage profond. Elle est prise en charge sur Windows 2016 et sur la machine virtuelle DSVM Ubuntu.  Elle partage les mêmes images principales de machine virtuelle (et donc tous les outils) que la machine virtuelle DSVM, mais elle est configurée pour faciliter l’apprentissage profond. Nous fournissons également des exemples complets pour la compréhension d’image et de texte qui sont largement applicables à de nombreux scénarios d’intelligence artificielle réels. La machine virtuelle DLVM tente également de faciliter la découverte de l’ensemble des outils et des exemples de la machine virtuelle DSVM en exposant un catalogue des outils et des exemples sur la machine virtuelle. En termes d’outils, la machine virtuelle DLVM fournit plusieurs frameworks d’apprentissage profond courants et des outils pour acquérir et prétraiter des données texte et image. Pour obtenir une liste complète des outils, consultez la [Page de vue d’ensemble de la machine virtuelle DSVM](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec la machine virtuelle DLVM, effectuez les étapes suivantes :

* [Provisionner une machine virtuelle DLVM](provision-deep-learning-dsvm.md)
* [Utiliser la machine virtuelle DLVM](use-deep-learning-dsvm.md)
* [Référence des outils](dsvm-deep-learning-ai-frameworks.md)
* [Exemples](dsvm-samples-and-walkthroughs.md)
