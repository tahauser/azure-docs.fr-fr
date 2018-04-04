---
title: Présentation du service Azure Batch AI | Microsoft Docs
description: En savoir plus sur l’utilisation du service géré Azure Batch AI pour effectuer l’apprentissage de l’intelligence artificielle (IA) et d’autres modèles de Machine Learning sur des clusters de GPU et d’UC.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: timlt
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/13/2017
ms.author: asutton
ms.custom: ''
ms.openlocfilehash: 69e7617f1a5918a08e3c692a1a25c4fcdeca536b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="what-is-batch-ai-in-azure"></a>Qu’est-ce que Batch AI dans Azure ?
Batch AI est un service géré qui permet aux scientifiques des données et aux chercheurs en IA d’effectuer l’apprentissage de l’IA et d’autres modèles de Machine Learning sur des clusters de machines virtuelles Azure, y compris les machines virtuelles prenant en charge les GPU. Vous décrivez les exigences de votre travail, où trouver les entrées et stocker les sorties, et Batch AI gère le reste.  
 
## <a name="why-batch-ai"></a>Pourquoi Batch AI ? 
La mise au point d’algorithmes d’IA puissants est un processus intensif en calcul et itératif. Les scientifiques des données et les chercheurs en IA travaillent avec des jeux de données de plus en plus volumineux. Ils développent des modèles comprenant plus de couches, et pour cela ils expérimentent avec la conception du réseau et le réglage des hyperparamètres. Pour être efficace, cette opération nécessite plusieurs UC ou GPU par modèle, l’exécution d’expériences en parallèle, et le stockage partagé pour les données d’apprentissage, les journaux et les résultats générés par le modèle.   
 
![Processus Batch AI](media/overview/batchai-context.png)

Les scientifiques des données et les chercheurs en IA sont des experts dans leur domaine, mais la gestion de l’infrastructure à grande échelle peut représenter un obstacle. Le développement de l’IA à grande échelle exige de nombreuses tâches d’infrastructure : déploiement de clusters de machines virtuelles, installation de logiciels et de conteneurs, mise en file d’attente du travail, définition des priorités et planification des travaux, gestion des échecs, distribution des données, partage des résultats, mise à l’échelle des ressources pour gérer les coûts, et intégration avec les outils et les flux de travail. Batch AI gère ces tâches. 
 
## <a name="what-is-batch-ai"></a>Qu’est ce que Batch AI ? 

Batch AI assure la gestion des ressources et la planification des travaux spécialement pour la formation et le test de l’IA. Les fonctionnalités clés sont les suivantes : 

* Exécution de programmes de traitement par lots de longue durée, expérimentation itérative, apprentissage interactif 
* Mise à l’échelle automatique ou manuelle des clusters de machines virtuelles utilisant des GPU ou des UC 
* Configuration de la communication SSH entre les machines virtuelles et pour l’accès à distance 
* Prise en charge de toutes les infrastructures d’apprentissage profond ou de Machine Learning, avec une configuration optimisée pour les outils courants tels que [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), [TensorFlow](https://www.tensorflow.org/) et [Chainer](https://chainer.org/) 
* File d’attente des travaux axée sur les priorités pour partager des clusters et tirer parti des machines virtuelles à basse priorité ainsi que des instances réservées  
* Options de stockage flexibles incluant les fichiers Azure et un serveur NFS géré 
* Montage des partages de fichiers distants dans la machine virtuelle et le conteneur facultatif 
* Renseignement sur l’état du travail et redémarrage en cas de défaillance de la machine virtuelle 
* Accès aux journaux de sortie, stdout, stderr et modèles, y compris la diffusion en continu depuis le stockage Azure 
* [Interface de ligne de commande](/cli/azure) (CLI) Azure, kits de développement logiciel (SDK) pour [Python](https://github.com/Azure/azure-sdk-for-python), [C#](https://www.nuget.org/packages/Microsoft.Azure.Management.BatchAI/1.0.0-preview)et Java, surveillance dans le Portail Azure et intégration avec les outils Microsoft AI 

Le kit de développement logiciel (SDK) Batch AI prend en charge l’écriture de scripts ou d’applications pour la gestion des pipelines d’apprentissage et l’intégration avec des outils. Le kit de développement logiciel (SDK) inclut actuellement Python, C#, Java et les API REST.  
 

Batch AI utilise Azure Resource Manager pour les opérations de plan de contrôle (créer, répertorier, obtenir, supprimer). Azure Active Directory est utilisé pour l’authentification et le contrôle d’accès en fonction du rôle.  
 
## <a name="how-to-use-batch-ai"></a>Comment utiliser Batch AI 

Pour utiliser Batch AI, vous définissez et gérez des *clusters* et des *travaux*. 

 
Les **clusters** décrivent vos exigences de calcul : 
* La région Azure où vous souhaitez fonctionner 
* La famille et la taille de machine virtuelle à utiliser, par exemple, une machine virtuelle NC24, qui contient 4 GPU NVIDIA K80 
* Le nombre de machines virtuelles, ou le nombre minimal et maximal pour la mise à l’échelle automatique 
* L’image de machine virtuelle, par exemple, Ubuntu 16.04 LTS ou [Microsoft Deep Learning Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* Tous les volumes de partage de fichiers distants à monter, par exemple, à partir de fichiers Azure ou d’un serveur NFS gérés par Batch AI 
* Nom d’utilisateur et clé SSH ou mot de passe pour configurer sur les machines virtuelles afin d’activer la connexion interactive pour le débogage  
 

Les **travaux** décrivent : 
* Le cluster et la région à utiliser 
* Le nombre de machines virtuelles nécessaires pour le travail 
* Les répertoires d’entrée et de sortie à transmettre au travail lors du démarrage. Pour cela, le système de fichiers partagé monté lors de l’installation de cluster est généralement utilisé 
* Un conteneur facultatif pour exécuter votre logiciel ou script d’installation 
* Une configuration spécifique à l’infrastructure d’IA ou la ligne de commande et les paramètres pour démarrer le travail 
 

Prise en main de Batch AI avec [Azure CLI](/cli/azure) et les fichiers de configuration pour les clusters et les travaux. Cette approche permet de créer rapidement votre cluster lorsque cela est nécessaire et d’exécuter des travaux afin d’expérimenter avec la conception du réseau ou les hyperparamètres.  
 

Batch AI facilite l’utilisation en parallèle de plusieurs GPU. Lorsque les travaux doivent être mis à l’échelle sur plusieurs GPU, Batch AI définit une connectivité réseau sécurisée entre les machines virtuelles. Lorsque InfiniBand est utilisé, Batch AI configure les pilotes et démarre les MPI sur l’ensemble des nœuds d’un travail.  

## <a name="data-management"></a>Gestion des données
Batch AI propose des options flexibles pour les scripts d’apprentissage, les données et les sorties :
  
* Utilisez **disque local** pour l’expérimentation anticipée et les jeux de données plus petits. Pour ce scénario, vous pouvez vous connecter à la machine virtuelle sur SSH pour modifier des scripts et lire des journaux. 

* Utilisez **fichiers Azure** pour partager des données d’apprentissage sur plusieurs travaux et stocker les journaux de sortie et les modèles dans un emplacement unique 

* Configurez un **serveur NFS** pour prendre en charge une plus grande échelle de données et de machines virtuelles pour l’apprentissage. Batch AI vous permet de configurer un serveur NFS comme un type de cluster particulier, avec des disques sauvegardés dans le stockage Azure. 
 
* Un **système de fichiers parallèle** offre davantage d’extensibilité pour les données et l’apprentissage parallèle. Bien que Batch AI ne gère pas les systèmes de fichiers parallèles, des exemples de modèles de déploiement sont disponibles pour Lustre, Gluster et BeeGFS.  

## <a name="next-steps"></a>Étapes suivantes

* Commencez la création de votre premier travail de formation Batch AI à l’aide [d’Azure CLI](quickstart-cli.md) ou de [Python](quickstart-python.md).
* Consultez des exemples de [recettes de formation](https://github.com/Azure/BatchAI) pour différentes infrastructures.

