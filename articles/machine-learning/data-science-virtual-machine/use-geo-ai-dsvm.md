---
title: Utilisation de la machine virtuelle de science des données de l’intelligence artificielle de géoréplication - Azure | Microsoft Docs
description: Comment utiliser une machine virtuelle IA de géoréplication sur Azure.
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
ms.openlocfilehash: f7ee109c26f8b2f6107dfcb4853d5517384aef76
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Utilisation de la machine virtuelle de science des données de l’intelligence artificielle de géoréplication

Utilisez la machine virtuelle de science des données IA de géo-réplication pour extraire des données pour analyse, effectuer la transformation préalable des données et générer des modèles pour les applications IA qui consomment des informations géospatiales. Une fois que vous avez configuré votre machine virtuelle de science des données IA de géoréplication et que vous vous êtes connecté à ArcGIS Pro avec votre compte ArcGIS, vous pouvez commencer à interagir avec le bureau ArcGIS et ArcGis en ligne. Vous pouvez également accéder à ArcGIS à partir des interfaces Python et un pont de langage R préconfiguré sur la machine virtuelle de science des données de géo-réplication. Pour générer des applications IA riches, vous devez les associer aux infrastructures de Machine Learning et d’apprentissage profond et à d’autres logiciels de science des données disponibles sur la machine virtuelle de science des données.  


## <a name="configuration-details"></a>Détails de configuration

La bibliothèque Python, [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), utilisée pour interfacer avec ArcGIS est installée dans l’environnement Conda racine global de la machine virtuelle de science des données, qui se trouve sur ```c:\anaconda```. 

- Si vous exécutez Python dans une invite de commandes, exécutez ```activate``` pour l’activer dans l’environnement Python racine Conda. 
- Si vous utilisez un bloc-notes IDE ou Jupyter, vous pouvez sélectionner l’environnement ou le noyau pour vous assurer que vous êtes dans l’environnement Conda correct. 

Le pont R vers ArcGIS est installé en tant que bibliothèque R nommée [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) dans l’instance autonome principale du serveur R Microsoft, située sur ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio et Jupyter sont déjà préconfigurés pour utiliser cet environnement R et auront accès à la bibliothèque R ```arcgisbinding```. 


## <a name="geo-ai-data-science-vm-samples"></a>Exemples de machine virtuelle de science des données IA de géoréplication

Outre les exemples d’infrastructures de Machine Learning de d’apprentissage profond à partir de la machine virtuelle de science des données, un ensemble d’exemples geospatiaux est également fourni dans le cadre de la machine virtuelle de science des données IA de géoréplication. Ces exemples peuvent vous aider à lancer rapidement le développement de vos applications IA à l’aide des données géospatiales et du logiciel ArcGIS. 


1. [Mise en route avec l’analyse géospatiale avec Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb) : un exemple d’introduction montrant comment utiliser des données géospatiales à l’aide de l’interface Python vers ArcGIS fournie par la bibliothèque [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm). Il montre également comment combiner une Machine Learning traditionnelle d’apprentissage avec des données géospatiales et visualiser le résultat sur un mappage dans ArcGIS. 

2. [Mise en route avec l’analyse géospatiale avec R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb) : un exemple d’introduction montrant comment utiliser des données géospatiales à l’aide de l’interface R vers ArcGIS fournie par la bibliothèque [arcpy](https://github.com/R-ArcGIS/r-bridge). 

3. [Classification d’utilisation de terrain au niveau des pixels](https://github.com/Azure/pixel_level_land_classification) : un didacticiel qui montre comment créer un modèle de réseau neuronal profond qui accepte une image aérienne comme entrée et retourne une étiquette de couverture de terrain. Les exemples d’étiquettes de couverture de terrain sont « forestier » ou « eau ». Le modèle retourne une telle étiquette pour chaque pixel de l’image. Le modèle est généré à l’aide de l’infrastructure d’apprentissage profond [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) open source de Microsoft. L’exemple montre également comment augmenter la taille des instances de la formation sur [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) et consommer les prédictions du modèle dans le logiciel ArcGIS Pro. 


## <a name="next-steps"></a>Étapes suivantes

Des exemples supplémentaires qui utilisent la machine virtuelle de science des données sont disponibles ici :

* [Exemples](dsvm-samples-and-walkthroughs.md)

