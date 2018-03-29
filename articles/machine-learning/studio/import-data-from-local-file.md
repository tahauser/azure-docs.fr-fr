---
title: Importer des données à partir d’un fichier dans Azure Machine Learning Studio | Microsoft Docs
description: Découvrez comment charger un fichier de données d’apprentissage de votre disque dur sur Azure Machine Learning Studio. Un module de jeu de données est ainsi créé dans l’espace de travail.
keywords: importer des données, format de données, types de données, sources de données, données d'apprentissage
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: f7096ee9e887f13e5bff1f79b7e81702061b8dea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>Importer des données d’apprentissage à partir d’un fichier situé sur votre disque dur dans Machine Learning Studio
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Découvrez comment charger un fichier de données de votre disque dur pour vous en servir comme données d’apprentissage dans Azure Machine Learning Studio. En important le fichier de données, vous disposez d’un module de jeu de données prêt à l’emploi dans votre espace de travail.

## <a name="steps-to-import-data-from-a-local-file"></a>Étapes d’importation des données à partir d’un fichier local
Pour importer des données à partir d’un disque dur local, procédez comme suit :

1. Cliquez sur **+NOUVEAU** en bas de la fenêtre de Machine Learning Studio.
2. Sélectionnez **JEU DE DONNÉES** et **DEPUIS UN FICHIER LOCAL**.
3. Dans la boîte de dialogue **Télécharger un nouveau jeu de données** , recherchez le fichier que vous souhaitez télécharger
4. Saisissez un nom, identifiez le type de données puis saisissez éventuellement une description. Une description est recommandée : elle vous permet d’enregistrer des caractéristiques relatives aux données que vous souhaitez mémoriser pour une utilisation future.
5. La case à cocher **Il s'agit de la nouvelle version d'un jeu de donnée existant** vous permet de mettre à jour un jeu de données existant avec de nouvelles données. Cliquez sur cette case à cocher, puis saisissez le nom d’un jeu de données existant.

![Charger un nouveau jeu de données](./media/import-data-from-local-file/upload-dataset.png)

Pendant le téléchargement, vous verrez un message indiquant que votre fichier est en cours de téléchargement. Le temps de téléchargement dépend de la taille de vos données et de la vitesse de votre connexion au service. Si vous savez que le fichier prendra du temps, vous pouvez faire autre chose dans Machine Learning Studio en attendant. Cependant, la fermeture du navigateur entraîne l’échec du téléchargement.

## <a name="dataset-module-is-ready-for-use"></a>Module de jeu de données prêt à l’emploi
Une fois que vos données sont téléchargées, elles sont stockées dans un module de jeu de données et sont disponibles pour n'importe quelle expérience dans votre espace de travail.

Quand vous éditez une expérimentation, les jeux de données que vous avez créés apparaissent sous **Mes jeux de données** dans la liste **Jeux de données enregistrés** de la palette des modules. Vous pouvez glisser-déplacer le jeu de données dans le canevas de l’expérience en vue d’affiner l’analyse et Machine Learning.
