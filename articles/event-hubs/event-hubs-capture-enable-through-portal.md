---
title: "Activer Azure Event Hubs Capture à l’aide du portail | Microsoft Docs"
description: "Activez la fonctionnalité Event Hubs Capture à l’aide du portail Azure."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 39fbdba404bda9383c9164dd1ecd9cb23bfb5cd7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Activer Event Hubs Capture à l’aide du portail Azure

Azure [Event Hubs Capture][capture-overview] vous permet de fournir automatiquement les données de diffusion en continu d’Event Hubs à un compte de [stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/) ou [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) de votre choix.

Vous pouvez configurer la fonctionnalité Capture lors de la création du concentrateur d’événements, à l’aide du [portail Azure](https://portal.azure.com). Vous pouvez capturer les données vers un conteneur de [Stockage Blob](https://azure.microsoft.com/services/storage/blobs/) Azure, ou vers un compte [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Pour plus d’informations, consultez la section [Vue d’ensemble d’Event Hubs Capture][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Capturer des données vers un compte de stockage Azure  

Lors de la création d’un concentrateur d’événement, vous pouvez activer la fonctionnalité Capture en cliquant sur le bouton **Activé** sur la page **Créer un concentrateur d’événement** du portail. Précisez ensuite le compte et le conteneur de stockage à utiliser en cliquant sur **Stockage Azure** dans la zone **Fournisseur de capture**. Comme Event Hubs Capture utilise l’authentification de service à service avec le stockage, il est inutile de spécifier une chaîne de connexion de stockage. Le sélecteur de ressources sélectionne automatiquement l’URI de ressource pour votre compte de stockage. Si vous utilisez Azure Resource Manager, vous devez fournir explicitement cet URI en tant que chaîne.

La fenêtre temporelle par défaut est de cinq minutes. La valeur minimum est 1, la valeur maximum 15. La fenêtre **Taille** présente une plage de 10 à 500 Mo.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Capturer des données vers un compte Azure Data Lake Store

Pour capturer des données vers Azure Data Lake Store, vous créez un compte Data Lake Store et un concentrateur d’événements :

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Créer un compte Azure Data Lake Store et des dossiers

1. Pour créer un compte Data Lake Store, suivez les instructions de [Prise en main d’Azure Data Lake Store avec le portail Azure](../data-lake-store/data-lake-store-get-started-portal.md).
2. Suivez les instructions de la section [Affecter des autorisations à des concentrateurs d’événements](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) pour créer un dossier au sein du compte Data Lake Store dans lequel vous souhaitez capturer les données à partir de concentrateurs d’événements, et affecter des autorisations à des concentrateurs d’événements de sorte qu’ils puissent écrire des données dans votre compte Data Lake Store.  

### <a name="create-an-event-hub"></a>Création d'un concentrateur d'événements

1. Remarque : le concentrateur d’événements doit se trouver dans le même abonnement Azure que le compte Azure Data Lake Store que vous venez de créer. Pour créer le concentrateur d’événements, cliquez sur le bouton **Activé** dans **Capture** sur la page **Créer un concentrateur d’événements** du portail. 
2. Sur la page **Créer un concentrateur d’événements** du portail, sélectionnez **Azure Data Lake Store** dans la zone **Fournisseur de capture**.
3. Dans **Sélectionner Data Lake Store**, spécifiez le compte Data Lake Store que vous avez créé précédemment, puis entrez le chemin d’accès au dossier de données que vous avez créé dans le champ **Chemin d’accès Data Lake**.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Ajouter ou configurer la fonctionnalité Capture sur un concentrateur d’événements existant

Vous pouvez configurer la fonctionnalité Capture sur des concentrateurs d’événements se trouvant dans des espaces de noms Event Hubs. Pour activer Capture sur un hub d’événements existant, ou pour modifier les paramètres de cette fonctionnalité, cliquez sur l’espace de noms afin de charger l’écran de vue d’ensemble, puis cliquez sur le hub d’événements pour lequel vous souhaitez activer ou modifier la configuration de Capture. Pour finir, cliquez sur l’option **Capture** à gauche du panneau ouvert, puis modifiez les paramètres de Capture, comme indiqué dans la figure suivante :

### <a name="azure-blob-storage"></a>un stockage Azure Blob

![][2]

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Event Hubs Capture, consultez [Vue d’ensemble d’Event Hubs Capture][capture-overview].
- Vous pouvez également configurer Event Hubs Capture via des modèles Azure Resource Manager. Pour en savoir plus, consultez la section relative à [l’activation de Capture à l’aide d’un modèle Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Prise en main d’Azure Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md