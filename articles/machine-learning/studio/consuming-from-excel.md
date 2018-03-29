---
title: Utilisation d’un service web Machine Learning à partir de Microsoft Excel | Microsoft Docs
description: Utilisation d’un service web Microsoft Azure Machine Learning à partir de Microsoft Excel.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/1/2018
ms.openlocfilehash: 2c415f62a8ef28b1dfe185fcd1543cd2c4a63138
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="consuming-an-azure-machine-learning-web-service-from-excel"></a>Utilisation d’un service web Microsoft Azure Machine Learning à partir de Microsoft Excel.
 Microsoft Azure Machine Learning Studio permet d’appeler facilement des services web directement à partir de Microsoft Excel sans qu’il soit nécessaire d’écrire du code.

Si vous utilisez Excel 2013 (ou une version ultérieure) ou Excel Online, nous vous recommandons d’utiliser la [macro complémentaire Excel](excel-add-in-for-web-services.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="steps"></a>Étapes
Publiez un service web. [Cette page](walkthrough-5-publish-web-service.md) explique comment procéder. Actuellement, la fonctionnalité de classeur Excel est uniquement prise en charge pour les services de requête/réponse qui produisent une seule sortie (autrement dit, une étiquette de notation unique). 

Quand vous disposez d’un service web, cliquez sur la section **WEB SERVICES** située sur la partie gauche de Microsoft Azure Machine Learning Studio, puis sélectionnez le service web à utiliser à partir de Microsoft Excel.

**Service web classique**

1. Sur l’onglet **TABLEAU DE BORD** du service web figure une ligne pour le service **REQUÊTE/RÉPONSE**. Si ce service produit une sortie unique, le lien **Télécharger un classeur Excel** doit apparaître sur cette ligne.
   
    ![][1]
2. Cliquez sur **Télécharger un classeur Excel**.

**Nouveau service web**

1. Dans le portail Service web Azure Machine Learning, sélectionnez **Consommer**.
2. Dans la page Consommer, dans les **options de consommation de service Web** cliquez sur l’icône Excel.

**Utilisation du classeur**

1. Ouvrez le classeur.
2. Un avertissement de sécurité s’affiche. Cliquez sur le bouton **Activer la modification**.
   
    ![][2]
3. Un avertissement de sécurité apparaît. Cliquez sur le bouton **Activer le contenu** pour pouvoir exécuter des macros sur votre feuille de calcul.
   
    ![][3]
4. Une fois les macros activées, une table est générée. Les valeurs des colonnes en bleu sont requises en tant qu’entrées dans le service web RRS (Request/Response Service), ou en tant que **PARAMÈTRES**. Notez la sortie du service RRS, appelée **VALEURS PRÉDITES** et affichée en vert. Lorsque toutes les colonnes d’une ligne donnée sont remplies, le classeur appelle automatiquement l’API de notation et affiche les notes résultantes.
   
    ![][4]
5. Pour noter plusieurs lignes, remplissez la deuxième ligne avec les données ; des valeurs prédites sont produites. Vous pouvez même coller plusieurs lignes à la fois.

Vous pouvez utiliser toutes les fonctionnalités de Microsoft Excel (graphiques, Power Map, mise en forme conditionnelle, etc.) avec les valeurs prédites pour visualiser les données.    

## <a name="sharing-your-workbook"></a>Partage de votre classeur
Pour que les macros fonctionnent, votre Clé API doit faire partie de la feuille de calcul. Cela signifie que vous devez uniquement partager le classeur avec des entités/personnes de confiance.

## <a name="automatic-updates"></a>Mises à jour automatiques
Un appel RRS est initié dans les deux cas suivants :

1. la première fois que du contenu apparaît dans l’ensemble des **PARAMÈTRES**
2. chaque fois que l’un ou l’autre des **PARAMÈTRES** change dans une ligne dont l’ensemble des **PARAMÈTRES** a été entré.

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
