---
title: "Créer une capacité Power BI Embedded dans le portail Azure | Microsoft Docs"
description: "Cet article vous montre comment créer une capacité Power BI Embedded dans Microsoft Azure."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: bf7eb967760338626cd7e0465b16dc570e5f582a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Créer une capacité Power BI Embedded dans le portail Azure

Cet article vous montre comment créer une capacité Power BI Embedded dans Microsoft Azure. Power BI Embedded simplifie les fonctionnalités de Power BI en vous permettant d’ajouter rapidement des visuels, des rapports et des tableaux de bord dans vos applications.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

* **Abonnement Azure :** visitez [version d’évaluation gratuite d’Azure](https://azure.microsoft.com/free/) pour créer un compte.
* **Azure Active Directory :** votre abonnement doit être associé à un locataire Azure Active Directory (AAD). Vous devez également ***être connecté à Azure avec un compte dans ce locataire***. Les comptes Microsoft ne sont pas pris en charge. Pour en savoir plus, consultez Authentification et autorisations utilisateur.
* **Locataire Power BI :** au moins un compte dans votre locataire AAD doit avoir souscrit à Power BI.
* **Groupe de ressources :** utilisez un groupe de ressources existant ou [créez-en un](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>Créer une capacité

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Créer une ressource** > **Données + Analytique**.

3. Dans la zone de recherche, recherchez *Power BI Embedded*.

4. Dans Power BI Embedded, sélectionnez **Créer**.

5. Renseignez les informations demandées et sélectionnez **Créer**.

    ![Champs à renseigner pour créer une nouvelle capacité](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |Paramètre |Description |
    |---------|---------|
    |**Nom de la ressource**|Nom destiné à identifier la capacité. Le nom de la ressource est affiché dans le portail d’administration Power BI et dans le portail Azure.|
    |**Abonnement**|L’abonnement dans lequel vous souhaitez créer la capacité.|
    |**Groupe de ressources**|Le groupe de ressources qui contient cette nouvelle capacité. Choisissez un groupe de ressources existant ou créez-en un. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).|
    |**Administrateur de capacité Power BI**|Les administrateurs de capacité Power BI peuvent afficher la capacité dans le portail d’administration Power BI et accorder des autorisations d’affectation à d’autres utilisateurs. Par défaut, l’administrateur de la capacité correspond à votre compte. L’administrateur de capacité doit se trouver dans votre locataire Power BI.|
    |**Lieu**|L’emplacement où Power BI est hébergé pour votre locataire. Ce paramètre est résolu automatiquement, il est impossible de sélectionner un emplacement différent.|
    |**Niveau tarifaire**|Sélectionnez la référence SKU (taille de mémoire et nombre v-core) qui répond à vos besoins.  Pour plus de détails, consultez [Tarification de Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded/)|

6. Sélectionnez **Créer**.

La création prend généralement moins d’une minute, souvent quelques secondes seulement. Si vous avez sélectionné **Épingler au tableau de bord**, accédez à votre tableau de bord pour afficher la nouvelle capacité. Ou accédez à **Tous les services** > **Power BI Embedded** pour voir si votre capacité est prête.

![Tableau de bord du portail Azure avec capacité Power BI Embedded](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

Pour utiliser la nouvelle capacité Power BI Embedded, accédez au portail d’administration Power BI pour affecter des espaces de travail. Pour en savoir plus, voir [Gérer les capacités dans Power BI Premium et Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

Si vous n’avez pas besoin d’utiliser cette capacité, suspendez-la pour arrêter la facturation. Pour plus d’informations, consultez [Pause and start your Power BI Embedded capacity in the Azure portal](pause-start.md) (Suspendre et démarrer votre capacité Power BI Embedded dans le portail Azure).

Pour commencer l’incorporation de contenus Power BI dans une application, consultez la page [Guide pratique pour incorporer vos tableaux de bord, rapports et vignettes Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

Des questions ? [Essayer d’interroger la Communauté Power BI](http://community.powerbi.com/).