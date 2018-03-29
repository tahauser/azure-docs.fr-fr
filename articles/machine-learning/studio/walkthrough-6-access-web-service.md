---
title: 'Étape 6 : Accéder au service web Machine Learning | Microsoft Docs'
description: 'Étape 6 du guide pas à pas du développement d’une solution prédictive : accès à un service web actif Azure Machine Learning.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 82e068ca3656b28e5e8dad19a31d6e5ff9c6f8b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Étape de didacticiel pas à pas 6 : accéder au service web Azure Machine Learning

Voici la dernière étape de la procédure pas à pas [Développement d'une solution d'analyse prédictive dans Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Créer un espace de travail Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Télécharger des données existantes](walkthrough-2-upload-data.md)
3. [Créer une expérience](walkthrough-3-create-new-experiment.md)
4. [Former et évaluer les modèles](walkthrough-4-train-and-evaluate-models.md)
5. [Déployer le service web](walkthrough-5-publish-web-service.md)
6. **Accéder au service web**

- - -
Dans l'étape précédente de cette procédure pas à pas, nous avons déployé un service web qui utilise notre modèle prédictif de risque de crédit. Désormais, les utilisateurs peuvent lui envoyer des données et recevoir des résultats. 

Il s’agit d’un service web Azure qui peut recevoir et renvoyer des données de deux manières à l’aide d’API REST :  

* **Demande/Réponse** : l'utilisateur envoie une ou plusieurs lignes de crédit au service en utilisant le protocole HTTP et le service répond avec un ou plusieurs jeux de résultats.
* **Exécution en lots** : l'utilisateur stocke une ou plusieurs lignes de données de crédit dans le stockage d'objets blob Azure, puis envoie l'emplacement du stockage d'objets blob au service. Le service évalue toutes les lignes de données dans l'objet blob d'entrée, enregistre les résultats dans un autre objet blob et renvoie l'URL de ce conteneur.  

Le moyen le plus rapide et le plus simple d’accéder à un service web classique est d’utiliser [Azure ML Request-Response Service Web App](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) ou [Modèle d’application Web Azure ML Batch Execution Service](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Ces modèles d'applications web peuvent générer une application Web personnalisée qui connaît les données d'entrée et les résultats attendus de votre service Web. Il vous suffit de donner à l'application Web l'accès à votre service Web et aux données associées, et le modèle fait le reste.

Pour plus d’informations sur les modèles d’applications web, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning à l’aide d’un modèle d’application Web](consume-web-service-with-web-app-template.md).

Vous pouvez également développer une application personnalisée pour accéder au service web à l'aide du code de démarrage fourni dans R, C# et les langages de programmation Python.

Vous trouverez des détails complets dans [Utilisation d’un service web Azure Machine Learning](consume-web-services.md).

