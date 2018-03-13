---
title: "Comparaison entre les images personnalisées et les formules dans DevTest Labs | Microsoft Docs"
description: "Découvrez les différences entre les images personnalisées et les formules comme bases de machine virtuelle afin de déterminer laquelle correspond le mieux à votre environnement."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: v-craic
ms.openlocfilehash: 78c0255f142bd3d4b2311ac953541153b72ac12d
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2018
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Comparaison entre les images personnalisées et les formules dans DevTest Labs
Les [images personnalisées](devtest-lab-create-template.md) et les [formules](devtest-lab-manage-formulas.md) peuvent toutes deux être utilisées comme bases pour [créer des machines virtuelles](devtest-lab-add-vm.md). Cependant, la différence principale entre les images personnalisées et les formules est qu’une image personnalisée est simplement une image basée sur un disque dur virtuel, alors qu’une formule correspond à une image basée sur un disque dur virtuel *en plus* de paramètres préconfigurés, comme la taille de la machine virtuelle, le réseau virtuel, le sous-réseau et les artefacts. Ces paramètres préconfigurés sont définis avec les valeurs par défaut. Celles-ci peuvent être remplacées au moment de la création de la machine virtuelle. Cet article explique quelques-uns des avantages et des inconvénients de l’utilisation des images personnalisées et des formules.

## <a name="custom-image-pros-and-cons"></a>Avantages et inconvénients des images personnalisées
Les images personnalisées représentent un moyen statique et immuable de créer des machines virtuelles dans un environnement de votre choix. 

**Avantages**

* L’approvisionnement de machines virtuelles à partir d’une image personnalisée est rapide car rien ne change une fois que la machine virtuelle est lancée à partir de l’image. En d’autres termes, il n’existe aucun paramètre à appliquer, car l’image personnalisée est simplement une image sans paramètres. 
* Les machines virtuelles créées à partir d’une même image personnalisée sont identiques.

**Inconvénients**

* Si vous avez besoin de mettre à jour certains aspects de l’image personnalisée, l’image doit être recréée.  

## <a name="formula-pros-and-cons"></a>Avantages et inconvénients des formules
Les formules représentent un moyen dynamique de créer des machines virtuelles à partir de la configuration/des paramètres souhaités.

**Avantages**

* Les modifications de l'environnement peuvent être capturées à la volée au moyen d’artefacts. Par exemple, si vous voulez installer une machine virtuelle avec les derniers éléments de votre pipeline de mise en production ou inscrire les lignes de code les plus récentes de votre dépôt, vous pouvez simplement spécifier un artefact qui déploie les derniers éléments ou inscrit les lignes de code les plus récentes dans la formule avec l’image de base cible. Chaque fois que cette formule sera utilisée pour créer des machines virtuelles, les derniers éléments/lignes de code seront déployés/inscrites sur la machine virtuelle. 
* Les formules, à la différence des images personnalisées, peuvent définir des paramètres par défaut, par exemple la taille des machines virtuelles et les paramètres de réseau virtuel. 
* Les paramètres enregistrés dans une formule apparaissent comme valeurs par défaut, mais peuvent être modifiés à la création de la machine virtuelle. 

**Inconvénients**

* La création d’une machine virtuelle à partir d’une formule peut prendre plus de temps qu’à partir d’une image personnalisée.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Billets de blog connexes
* [Custom images or formulas? (Images personnalisées ou formules ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Étapes suivantes
- [FAQ DevTest Labs](devtest-lab-faq.md)