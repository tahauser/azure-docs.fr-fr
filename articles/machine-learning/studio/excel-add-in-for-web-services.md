---
title: Complément Excel pour les services web Machine Learning | Microsoft Docs
description: Comment utiliser les services web Azure Machine Learning directement dans Excel sans écrire de code.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.openlocfilehash: 6610777ff4ad3a04f9d0d5b47f402aea7db59d9b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Complément Excel de services web Azure Machine Learning
Excel permet d'appeler facilement des services web directement sans qu'il soit nécessaire d'écrire du code.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Procédure d’utilisation d’un service web existant dans le classeur

1. Ouvrez l’ [exemple de fichier Excel](http://aka.ms/amlexcel-sample-2), qui contient le complément Excel et les données concernant les passagers sur le Titanic.
2. Choisissez le service web en cliquant dessus : « Titanic Survivor Predictor (exemple de complément Excel) [Score] » dans cet exemple.
   
    ![Sélectionner un service web][01]
3. Vous accédez alors à la section **Prédire**.  Ce classeur contient déjà des exemples de données mais, pour un classeur vide, vous pouvez également sélectionner une cellule dans Excel et cliquer sur **Utiliser les exemples de données**.
4. Sélectionnez les données avec les en-têtes et cliquez sur l'icône de plage de données d'entrée.  Assurez-vous que la case « Mes données ont des en-têtes » est activée.
5. Sous **Sortie**, entrez le numéro de la cellule dans laquelle vous souhaitez insérer le résultat, en l’occurrence, « H1 ».
6. Cliquez sur **Prédire**.
   
    ![Section Prédire][02]

Déployez un service web ou utilisez un service web existant. Pour plus d’informations sur le déploiement d’un service web, consultez la rubrique [Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning](walkthrough-5-publish-web-service.md).

Obtenez la clé API de votre service web. L’emplacement à partir duquel exécutez cette action diffère selon que vous avez publié un service web Machine Learning classique ou un nouveau service web Machine Learning.

**Utiliser un service web classique** 

1. Dans Machine Learning Studio, cliquez sur la section **WEB SERVICES** située sur le volet de gauche, puis sélectionnez le service web.
   
    ![Studio - Sélectionner un service web][04]
2. Copiez la clé API du service web.
   
    ![Studio clé API][05]
3. Sous l’onglet **TABLEAU DE BORD** pour le service web, cliquez sur le lien **REQUÊTE/RÉPONSE**.
4. Recherchez la section **URI de requête** .  Copiez et enregistrez l’URL.

> [!NOTE]
> Il est désormais possible de se connecter au portail [Azure Machine Learning Web Services](https://services.azureml.net) pour obtenir la clé API d’un service web Machine Learning classique.
> 
> 

**Utiliser un nouveau service web**

1. Dans le portail [Services web Azure Machine Learning](https://services.azureml.net), cliquez sur **Services web**, puis sélectionnez votre service web. 
2. Cliquez sur **Consommer**.
3. Recherchez la section **des informations de base sur la consommation** . Copiez et enregistrez la **Clé primaire** et l’URL de **Demande-réponse**.

## <a name="steps-to-add-a-new-web-service"></a>Procédure d’ajout d’un nouveau service web

1. Déployez un service web ou utilisez un service web existant. Pour plus d’informations sur le déploiement d’un service web, consultez la rubrique [Étape 5 du didacticiel pas à pas : Déploiement du service web Azure Machine Learning](walkthrough-5-publish-web-service.md).
2. Cliquez sur **Consommer**.
3. Recherchez la section **des informations de base sur la consommation** . Copiez et enregistrez la **Clé primaire** et l’URL de **Demande-réponse**.
4. Dans Excel, accédez à la section **Services web** (si vous vous trouvez dans la section **Prédire**, cliquez sur la flèche de retour pour accéder à la liste des services web).
   
    ![Accéder à la sélection du service web][03]
5. Cliquez sur **Ajouter un service web**.
6. Collez l’URL dans la zone de texte du complément Excel intitulée **URL**.
7. Collez l’API/Clé primaire dans la zone de texte intitulée **Clé API**.
8. Cliquez sur **Add**.
   
    ![URL et clé API pour un service web classique.][06]
9. Pour utiliser le service web, suivez les instructions de la section « Procédure d’utilisation d’un service web existant » ci-dessus.

## <a name="sharing-your-workbook"></a>Partage de votre classeur
Si vous enregistrez votre classeur, l'API/la clé primaire pour les services web que vous avez ajoutés seront également enregistrés. Cela signifie que vous devez uniquement partager le classeur avec des personnes de confiance.

Posez les questions que vous voulez dans la section de commentaire suivante ou dans notre [forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
