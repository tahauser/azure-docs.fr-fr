---
title: "Configurer Postman pour les appels d’API REST Azure Media Services"
description: "Découvrez comment configurer Postman pour les appels d’API REST Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: juliako
ms.openlocfilehash: 72b110cac8d4945c958d760ff98e2da2f2796b62
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configurer Postman pour les appels d’API REST Media Services

Ce didacticiel vous montre comment configurer **Postman** afin de l’utiliser pour appeler l’API REST Azure Media Services (AMS). Le didacticiel montre comment importer des fichiers d’environnement et de collection dans **Postman**. La collection contient des définitions groupées de requêtes HTTP qui appellent les API REST de Azure Media Services (AMS). Le fichier d’environnement contient des variables qui sont utilisées par la collection.

Cet environnement et la collection sont utilisés dans les articles qui indiquent comment effectuer diverses tâches liées aux API REST Azure Media Services.

## <a name="prerequisites"></a>Prérequis

- Installez le client REST [Postman](https://www.getpostman.com/) pour exécuter les API REST indiquées dans certains des didacticiels REST AMS. 

    Nous utilisons **Postman** mais n’importe quel outil REST serait approprié. Les autres solutions sont : **Visual Studio Code** avec le plug-in REST ou **Telerik Fiddler**. 

## <a name="configure-the-environment"></a>Configurer l’environnement 

1. Créez un fichier .json qui contient les variables d’environnement utilisées dans les didacticiels AMS. Nommez le fichier (**AzureMediaServices.postman_environment.json**). Ouvrez le fichier et collez le code qui définit l’environnement Postman à partir de [cette liste de code](postman-environment.md). 
2. Ouvrez **Postman**.
3. Sur la droite de l’écran, sélectionnez l’option **Gérer environnement**.

    ![Charger un fichier](./media/media-services-rest-upload-files/postman-create-env.png)
4. Dans la boîte de dialogue **Gérer environnement**, cliquez sur **Importer**.
5. Naviguez et sélectionnez le fichier **AzureMediaServices.postman_environment.json**.
6. L’environnement **AzureMedia** est ajouté.
7. Fermez la boîte de dialogue.
8. Sélectionnez l’environnement **AzureMedia**.

    ![Charger un fichier](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Configurer la collection

1. Créez un fichier .json qui contient la collection **Postman** avec toutes les opérations nécessaires pour charger un fichier dans Media Services. Nommez le fichier (par exemple **AzureMediaServicesOperations.postman_collection.json**). Ouvrez le fichier et collez le code qui définit la collection **Postman** à partir de [cette liste de code](postman-collection.md).
2. Cliquez sur **Importer** pour importer le fichier de la collection.
3. Choisissez le fichier **AzureMediaServicesOperations.postman_collection.json**.

    ![Charger un fichier](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez l’article [charger des ressources](media-services-rest-upload-files.md).  
