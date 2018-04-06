---
title: Personnaliser l’image conteneur utilisée pour déployer des modèles Azure ML | Microsoft Docs
description: Cet article décrit comment personnaliser une image conteneur pour les modèles Azure Machine Learning
services: machine-learning
author: tedway
ms.author: tedway, raymondl
manager: mwinkle
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 858c8933565aeeb22dc1b685082dab2c6481737b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Personnaliser l’image conteneur utilisée pour les modèles Azure ML

Cet article décrit comment personnaliser une image conteneur pour les modèles Azure Machine Learning.  Azure ML Workbench utilise des conteneurs pour le déploiement de modèles d’apprentissage automatique. Les modèles sont déployés avec leurs dépendances et Azure ML génère une image à partir du modèle, des dépendances et des fichiers associés.

## <a name="how-to-customize-the-docker-image"></a>Comment personnaliser l’image Docker
Personnalisez l’image Docker déployée par Azure ML avec :

1. un fichier depenencies.yml : pour gérer les dépendances installées à partir de [PyPi]( https://pypi.python.org/pypi), vous pouvez utiliser le fichier conda_dependencies.yml à partir du projet Workbench, ou créer les vôtres. Cette approche est recommandée pour l’installation des dépendances de Python installables avec pip.

   Exemple de commande CLI :
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Exemple de fichier conda_dependencies : 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. un fichier d’étapes Docker : cette option vous permet de personnaliser l’image déployée en installant des dépendances qui ne peut pas être installés à partir de PyPi. 

   Le fichier doit inclure des étapes d’installation de Docker telles que DockerFile. Les commandes suivantes sont autorisées dans le fichier : 

    RUN, ENV, ARG, LABEL, EXPOSE

   Exemple de commande CLI :
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   les commandes Image, Manifest et Service acceptent l’indicateur de fichier docker.

   Exemple de fichier d’étapes Docker :
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> l’image de base pour les conteneurs Azure ML est Ubuntu, et elle ne peut pas être modifié. Si vous spécifiez une autre image de base, elle sera ignorée.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez personnalisé votre image de conteneur, vous pouvez la déployer vers un cluster pour une utilisation à grande échelle.  Pour plus d’informations sur la configuration d’un cluster en vue du déploiement d’un service web, consultez [Configuration de la gestion des modèles](deployment-setup-configuration.md). 
