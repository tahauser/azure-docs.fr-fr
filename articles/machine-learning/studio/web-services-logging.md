---
title: Journalisation pour les services web Machine Learning | Microsoft Docs
description: Découvrez comment activer la journalisation pour les services Web de Machine Learning. La journalisation fournit des informations supplémentaires pour vous aider à résoudre les problèmes des API.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 345c1b1bd72d3df19f8b087d4cbffd4e25e092ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="enable-logging-for-machine-learning-web-services"></a>Activation de la journalisation pour les services Web de Machine Learning
Ce document fournit des informations sur la fonctionnalité de journalisation des services web Machine Learning. La journalisation fournit des informations supplémentaires, au-delà d’un numéro et d’un message d’erreur, qui peuvent vous aider à résoudre des problèmes liés à vos appels aux API Machine Learning.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Comment activer la journalisation pour un service web

Vous activez la journalisation à partir du portail des [services web Azure Machine Learning](https://services.azureml.net). 

1. Connectez-vous au portail des services web Azure Machine Learning à l’adresse [https://services.azureml.net](https://services.azureml.net). Pour un service web classique, vous pouvez également accéder au portail en cliquant sur **Nouvelle expérience de services web** dans la page des services web Machine Learning dans Machine Learning Studio.

   ![Lien Nouvelle expérience de services web](./media/web-services-logging/new-web-services-experience-link.png)

2. Dans la barre de menus supérieure, cliquez sur **Services web** pour un nouveau service web, ou sur **Services web classiques** pour un service web classique.

   ![Sélectionner les services web nouveaux ou classiques](./media/web-services-logging/select-web-service.png)

3. Pour un nouveau service web, cliquez sur son nom. Pour un service web classique, cliquez sur son nom, puis, dans la page suivante, sur le point de terminaison approprié.

4. Dans la barre de menus supérieure, cliquez sur **Configurer**.

5. Définissez l’option **Activer la journalisation** sur *Erreur* (pour journaliser uniquement les erreurs) ou sur *Tout* (pour une journalisation complète).

   ![Sélectionner le niveau de journalisation](./media/web-services-logging/enable-logging.png)

6. Cliquez sur **Enregistrer**.

7. Pour les services web classiques, créez le conteneur **ml-diagnostics**.

   Tous les journaux du service web sont conservés dans un conteneur d’objets blob nommé **ml-diagnostics** dans le compte de stockage associé au service web. Pour les nouveaux services web, ce conteneur est créé la première fois que vous accédez au service. Pour les services web classiques, vous devez créer le conteneur s’il n’existe pas. 

   1. Dans le [portail Azure](https://portal.azure.com), accédez au compte de stockage associé au service web.

   2. Sous **Service Blob**, cliquez sur **Conteneurs**.

   3. Si le conteneur **ml-diagnostics** n’existe pas, cliquez sur **+Conteneur**, nommez le conteneur « ml-diagnostics », puis sélectionnez le **Type d’accès** « Blob ». Cliquez sur **OK**.

      ![Sélectionner le niveau de journalisation](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Pour un service web classique, le tableau de bord Services Web dans Machine Learning Studio comprend également un commutateur pour activer la journalisation. Toutefois, la journalisation étant désormais gérée via le portail Services Web, vous devez l’activer via celui-ci, comme décrit dans cet article. Si vous déjà activé l’enregistrement dans Studio, puis dans le portail Services Web, désactiver, puis réactivez la journalisation.


## <a name="the-effects-of-enabling-logging"></a>Effets de l’activation de la journalisation
Lorsque la journalisation est activée, les diagnostics et erreurs du point de terminaison du service web sont journalisés dans le conteneur d’objets blob **ml-diagnostics** dans le compte de Stockage Azure lié à l’espace de travail de l’utilisateur. Ce conteneur contient toutes les informations de diagnostic pour tous les points de terminaison de service web pour tous les espaces de travail associés à ce compte de stockage.

Les journaux peuvent être consultés à l’aide de plusieurs outils servant à « explorer » un compte de Stockage Azure. Le plus simple peut être d’accéder au compte de stockage dans le portail Azure, de cliquer sur **Conteneurs**, puis sur le conteneur **ml-diagnostics**.  

## <a name="log-blob-detail-information"></a>Journaliser les informations détaillées sur l’objet blob
Chaque objet blob dans le conteneur conserve les informations de diagnostic pour une et une seule des actions suivantes :

* une exécution de la méthode de traitement par lot  
* une exécution de la méthode de requête-réponse  
* l’initialisation d'un conteneur de requête-réponse

Le nom de chaque objet blob a un préfixe sous la forme suivante : 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Où _Type de journal_ est l’une des valeurs suivantes :  

* lot  
* score/requests  
* score/init  

