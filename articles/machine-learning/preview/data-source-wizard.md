---
title: "Assistant Source de données Azure pour Azure Machine Learning | Microsoft Docs"
description: "Explique l’Assistant Source de données d’Azure Machine Learning Workbench"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4f9a2249142220f3c57cb2e479a175d4227749fd
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="data-source-wizard"></a>Assistant Source de données #

L’Assistant Source de données est un moyen simple et rapide d’importer un jeu de données dans Azure Machine Learning Workbench sans code. Il vous permet également de sélectionner un exemple de stratégie pour le jeu de données et les types de données de chaque colonne. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Étape 1 : Déclencher l’Assistant Source de données ## 

Pour importer des données dans un projet à l’aide de l’Assistant source de données, sélectionnez le bouton **+** en regard de la zone de recherche dans la vue de données, puis choisissez Ajouter une source de données. 

![ajouter une source de données](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Étape 2 : Sélectionner le stockage des données ##
Tout d’abord, spécifiez l’emplacement actuel de vos données. Elles peuvent être stockées dans un fichier plat, un répertoire, un fichier parquet, un fichier Excel ou une base de données. Consultez [Supported data sources for Azure Machine Learning data preparation](data-prep-appendix2-supported-data-sources.md) (Sources de données prise en charge pour la préparation de données Azure Machine Learning) pour plus d’informations.

![étape 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Étape 3 : Sélectionner le fichier de données ##
Pour un fichier/répertoire, spécifiez le chemin du fichier. Dans la liste déroulante, choisissez l’emplacement des données. Il peut s’agir d’un chemin local ou du Stockage Blob Azure. 

Spécifiez le chemin en le tapant ou en cliquant sur le bouton **Parcourir...** pour le rechercher. Vous pouvez rechercher un répertoire ou un ou plusieurs fichiers.

Cliquez sur **Terminer** pour accepter les valeurs par défaut du reste des étapes, ou bien sur Suivant pour passer à l’étape suivante.


![étape 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Étape 4 : Choisir des paramètres de fichier ##

L’Assistant Source de données peut détecter automatiquement le type de fichier, les séparateurs et l’encodage. En outre, il donne une idée de l’aspect futur des données. Vous pouvez également changer ces paramètres manuellement. 

![étape 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Étape 5 : Définir les types de données des colonnes ##

L’Assistant Source de données détecte automatiquement les types de données des colonnes du jeu de données. S’il en manque un ou que vous souhaitez appliquer un type de données, vous pouvez le changer manuellement. La colonne **SAMPLE OUTPUT DATA** (EXEMPLES DE DONNÉES DE SORTIE) montre des exemples de l’aspect futur des données.

Pour les colonnes pour lesquelles la préparation des données requiert des dates, vous pouvez être invité à sélectionner l’ordre des mois et des jours dans le format de date. Par exemple, 1/2/2013 pourrait correspondre au 2 janvier (pour ce faire, sélectionnez *Jour-mois*) ou au 1er février (sélectionnez *Mois-jour*).

![étape 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Étape 6 : Choisir la stratégie d’échantillonnage des données ##

Vous pouvez spécifier une ou plusieurs stratégies d’échantillonnage pour le jeu de données et en choisir une comme stratégie active. La stratégie par défaut consiste à charger les 10 000 premières lignes. Vous pouvez modifier cet échantillon en cliquant sur le bouton **Modifier** dans la barre d’outils ou ajouter une nouvelle stratégie en cliquant sur +Nouveau. Les stratégies actuellement prises en charge sont les suivantes :

-     Premières lignes
-     Nombre aléatoire de lignes
-     Pourcentage aléatoire de lignes
-     Fichier complet

Vous pouvez spécifier autant de stratégies d’échantillonnage que vous le souhaitez, mais vous ne pouvez en définir qu’une seule comme active quand vous préparez vos données. Pour définir une stratégie comme active, sélectionnez-la et cliquez sur Définir comme actif dans la barre d’outils.

Suivant l’origine des données, certaines stratégies d’échantillonnage peuvent ne pas être prises en charge. Pour plus d’informations sur l’échantillonnage, consultez la section sur l’échantillonnage de [ce document](data-prep-user-guide.md). 

![étape 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Étape 7 : Gestion de la colonne des chemins ##

Si le chemin inclut des données importantes, vous pouvez choisir de l’inclure en tant que première colonne du jeu de données. Cette option peut s’avérer utile si vous importez plusieurs fichiers. Sinon, vous pouvez choisir de ne pas l’inclure.

![étape 7](media/data-source-wizard/step6.png)

Après avoir cliqué sur Terminer, une nouvelle source de données est ajoutée au projet. Vous pouvez la retrouver sous le groupe Sources de données dans la vue de données, ou en tant que fichier dsource dans **l’affichage des fichiers**.
