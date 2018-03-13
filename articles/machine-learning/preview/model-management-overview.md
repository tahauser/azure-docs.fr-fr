---
title: "Vue d’ensemble conceptuelle de la gestion des modèles Azure Machine Learning | Microsoft Docs"
description: "Ce document explique les concepts de gestion des modèles pour Azure Machine Learning."
services: machine-learning
author: nk773
ms.author: padou
manager: mwinkle
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 931dfae740996325cc62071a861e81ef5f67548b
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="azure-machine-learning-model-management"></a>Gestion des modèles Azure Machine Learning

La gestion des modèles Azure Machine Learning vous permet de gérer et de déployer des flux de travail et des modèles d’apprentissage automatique. 

La gestion des modèles offre les fonctionnalités suivantes :
- Gestion des versions des modèles
- Suivi des modèles en production
- Déploiement de modèles en production via l’environnement Compute AzureML avec [Azure Container Service](https://azure.microsoft.com/services/container-service/) et [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Création de conteneurs Docker avec les modèles et test de ceux-ci en local
- Reformation automatisée des modèles
- Capture des données de télémétrie des modèles pour bénéficier d’insights exploitables. 

La gestion des modèles Azure Machine Learning fournit un registre des versions des modèles. Elle propose aussi des flux de travail automatisés qui permettent d’empaqueter et de déployer des conteneurs d’apprentissage automatique sous forme d’API REST. Les modèles et leurs dépendances de runtime sont empaquetés dans un conteneur Docker basé sur Linux avec une API de prédiction. 

Les environnements Compute Azure Machine Learning permettent de configurer et de gérer des clusters scalables pour héberger les modèles. L’environnement Compute est basé sur Azure Container Services. Azure Container Services assure une exposition automatique des API Machine Learning comme points de terminaison d’API REST avec les fonctionnalités suivantes :

- Authentification
- Équilibrage de la charge
- Montée en puissance automatique
- Chiffrement

La gestion des modèles Azure Machine Learning propose ces fonctionnalités via l’interface CLI, l’API et le portail Azure. 

La gestion des modèles Azure Machine Learning utilise les informations suivantes :

 - Un fichier de modèle ou un répertoire contenant les fichiers de modèle
 - Un fichier Python créé par l’utilisateur implémentant une fonction de notation de modèle
 - Un fichier de dépendances Conda répertoriant les dépendances de runtime
 - Le choix de l’environnement runtime et 
 - Un fichier de schéma pour les paramètres d’API 

Ces informations servent au moment d’effectuer les actions suivantes :

- Inscription d’un modèle
- Création d’un manifeste utilisé pendant la création d’un conteneur
- Création d’une image de conteneur Docker
- Déploiement d’un conteneur sur Azure Container Service
 
Le schéma suivante donne un aperçu de la façon dont les modèles sont inscrits et déployés dans le cluster. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Créer et gérer des modèles 
Vous pouvez inscrire les modèles auprès de la gestion des modèles Azure Machine Learning dans le but de suivre les versions des modèles en production. Pour faciliter la reproductibilité et la gouvernance, le service capture toutes les dépendances et les informations associées. Pour bénéficier d’insights plus approfondis sur les performances, vous pouvez capturer les données de télémétrie des modèles à l’aide du Kit de développement logiciel (SDK) fourni. Les données de télémétrie des modèles sont archivées à un emplacement de stockage fourni par l’utilisateur. Les données de télémétrie des modèles peuvent être utilisées ultérieurement pour l’analyse des performances des modèles, la formation et l’obtention d’insights pour votre entreprise.

## <a name="create-and-manage-manifests"></a>Créer et gérer des manifestes 
Les modèles exigent des artefacts supplémentaires pour être déployés en production. Le système permet de créer un manifeste qui comprend un modèle, des dépendances, un script d’inférence (aussi appelé script de notation), des exemples de données, un schéma, etc. Ce manifeste peut être considéré comme une recette visant à créer une image de conteneur Docker. Les entreprises peuvent générer automatiquement un manifeste, en créer différentes versions et gérer leurs manifestes. 

## <a name="create-and-manage-docker-container-images"></a>Créer et gérer des images de conteneur Docker 
Vous pouvez utiliser le manifeste de l’étape précédente pour créer des images de conteneur Docker dans leurs environnements respectifs. Les images Docker en conteneur offrent aux entreprises la possibilité d’exécuter ces images dans les environnements Compute suivants :

- [Azure Container Service basé sur Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Services de conteneur locaux
- Environnements de développement
- Appareils IoT

Autonomes, ces images en conteneur Docker contiennent toutes les dépendances nécessaires pour générer des prédictions. 

## <a name="deploy-docker-container-images"></a>Déployer des images de conteneur Docker 
Avec la gestion des modèles Azure Machine Learning, vous pouvez déployer des images de conteneur Docker avec une seule commande dans Azure Container Service, qui est géré par l’environnement Compute ML. Ces déploiements sont créés avec un serveur frontend qui offre les fonctionnalités suivantes :

- Prédictions de faible latence à l’échelle
- Équilibrage de la charge
- Mise à l’échelle automatique des points de terminaison ML
- Autorisation de clé API
- Document Swagger d’API

Vous pouvez contrôler l’échelle du déploiement et les données de télémétrie via les paramètres de configuration suivants :

- Journalisation système et données de télémétrie de modèle pour chaque niveau de service web. Si ce paramètre est activé, tous les journaux stdout sont diffusés en continu à [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Les données de télémétrie de modèle sont archivées à un emplacement de stockage que vous fournissez. 
- Mise à l’échelle automatique et limites de concurrence. Ces paramètres augmentent automatiquement le nombre de conteneurs déployés en fonction de la charge au sein du cluster existant. Ils contrôlent aussi le débit et la cohérence de la latence de prédiction.

## <a name="consumption"></a>Consommation 
La gestion des modèles Azure Machine Learning crée une API REST pour le modèle déployé en même temps que le document Swagger. Vous pouvez utiliser les modèles déployés en appelant les API REST avec la clé d’API et les entrées de modèle pour obtenir les prédictions dans le cadre des applications métier. L’exemple de code est disponible sur GitHub pour les langages Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py)et C# pour l’appel d’API REST. L’environnement CLI de la gestion des modèles Azure Machine Learning permet de travailler facilement avec ces API REST. Vous pouvez utiliser les API à l’aide d’une simple commande CLI, d’une application compatible Swagger ou en utilisant curl. 

## <a name="retraining"></a>Reformation 
La gestion des modèles Azure Machine Learning propose des API qui vous permettent de reformer vos modèles. Vous pouvez aussi vous servir des API pour mettre à jour des déploiements existants avec des versions mises à jour du modèle. Dans le cadre du flux de travail de science des données, vous devez recréer le modèle dans votre environnement d’expérimentation. Ensuite, vous inscrivez le modèle auprès de la gestion des modèles et mettez à jour des déploiements existants. Les mises à jour s’effectuent par le biais d’une seule commande CLI UPDATE. La commande UPDATE met à jour les déploiements existants sans modifier l’URL ou la clé d’API. Les applications qui utilisent le modèle continuent de fonctionner sans aucune modification de code et commencent à obtenir de meilleures prédictions en utilisant le nouveau modèle.

Le flux de travail complet décrivant ces concepts est illustré dans le schéma suivant :

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Forum Aux Questions (FAQ) 
- Quels types de données sont pris en charge ? Est-il possible de passer des tableaux NumPy directement comme entrée au service web ?

   Si vous indiquez le fichier de schéma qui a été créé à l’aide du Kit de développement logiciel (SDK) generate_schema, vous pouvez passer des tableaux NumPy et/ou des cadres de données Pandas. Vous pouvez aussi passer des entrées sérialisables JSON. Vous pouvez même passer une image sous forme de chaîne encodée binaire.

- Le service web prend-il en charge plusieurs entrées ou analyse-t-il différentes entrées ? 

   Oui, vous pouvez prendre plusieurs entrées empaquetées dans la demande JSON en tant que dictionnaire. Chaque entrée correspond à une clé de dictionnaire unique.

- L’appel activé par une demande adressée au service web est-il un appel bloquant ou un appel asynchrone ?

   Si le service a été créé en utilisant une option en temps réel dans l’interface CLI ou l’API, il s’agit d’un appel bloquant/synchrone. Il est supposé être en temps réel. En revanche, côté client, vous pouvez l’appeler en utilisant la bibliothèque HTTP asynchrone pour éviter de bloquer le thread client.

- Combien de demandes le service web peut-il traiter simultanément ?

   Cela dépend de l’échelle du cluster et du service web. Si vous multipliez le nombre de réplicas par 100 dans votre service, celui-ci pourra traiter un grand nombre de demandes de façon simultanée. Vous pouvez aussi configurer le nombre maximal de demandes simultanées par réplica pour accroître le débit du service.

- Combien de demandes le service web peut-il mettre en file d’attente ?

   Ce nombre est configurable. Par défaut, il est défini à environ 10 par réplica, mais vous pouvez accroître/réduire ce nombre en fonction des besoins de votre application. En règle générale, l’augmentation du nombre de demandes mises en file d’attente se traduit par une augmentation du débit du service, mais cela amplifie les latences aux centiles supérieurs. Pour maintenir la constance des latences, vous pouvez attribuer à la mise en file d’attente une valeur faible (1-5) et augmenter le nombre de réplicas pour gérer le débit. Vous pouvez aussi activer la mise à l’échelle automatique pour que le nombre de réplicas soit ajusté automatiquement en fonction de la charge. 

- Est-il possible d’utiliser une même machine ou un même cluster pour plusieurs points de terminaison de service web ?

   Absolument. Vous pouvez exécuter 100 fois plus de services/points de terminaison sur un même cluster. 

## <a name="next-steps"></a>Étapes suivantes
Pour bien commencer avec la gestion des modèles, consultez [Configuration de la gestion des modèles](deployment-setup-configuration.md).
