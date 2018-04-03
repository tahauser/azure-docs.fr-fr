---
title: Événements personnalisés pour Azure Event Grid avec le portail Azure| Microsoft Docs
description: Utilisez Azure Event Grid et PowerShell pour publier une rubrique et pour vous abonner à cet événement.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/23/2018
ms.topic: hero-article
ms.service: event-grid
ms.openlocfilehash: f1185c0b2d5d320cd712642f422408348bee7a37
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-and-route-custom-events-with-the-azure-portal-and-event-grid"></a>Créer et acheminer des événements personnalisés avec le portail Azure et Event Grid

Azure Event Grid est un service de gestion d’événements pour le cloud. Dans cet article, vous utilisez le portail Azure pour créer une rubrique personnalisée, vous abonner à cette rubrique et déclencher l’événement pour afficher le résultat. Vous envoyez l’événement à une fonction d’Azure qui enregistre les données d’événement. Une fois terminé, vous voyez que les données d’événement ont été envoyées à un point de terminaison et enregistrées.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-custom-topic"></a>Créer une rubrique personnalisée

Une rubrique de grille d’événement fournit un point de terminaison défini par l’utilisateur vers lequel vous envoyez vos événements. 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Pour créer une rubrique personnalisée, sélectionnez **Créer une ressource**. 

   ![Créer une ressource](./media/custom-event-quickstart-portal/create-resource.png)

1. Recherchez *Rubrique Event Grid* et sélectionnez l’option correspondante dans les options disponibles.

   ![Rechercher la rubrique Event Grid](./media/custom-event-quickstart-portal/search-event-grid.png)

1. Sélectionnez **Créer**.

   ![Étapes de démarrage](./media/custom-event-quickstart-portal/select-create.png)

1. Donnez un nom unique à la rubrique personnalisée. Le nom de la rubrique doit être unique, car elle est représentée par une entrée DNS. N’utilisez pas le nom indiqué dans l’image. Au lieu de cela, créez votre propre nom. Sélectionnez une des [régions prises en charge](overview.md). Fournissez un nom pour le groupe de ressources. Sélectionnez **Créer**.

   ![Donner des valeurs à la rubrique Event Grid](./media/custom-event-quickstart-portal/create-custom-topic.png)

1. Une fois la rubrique personnalisée créée, vous voyez la notification de réussite.

   ![Voir la notification de réussite](./media/custom-event-quickstart-portal/success-notification.png)

   Si le déploiement a échoué, trouvez ce qui a provoqué l’erreur. Sélectionnez **Échec du déploiement**.

   ![Sélectionner Échec du déploiement](./media/custom-event-quickstart-portal/select-failed.png)

   Sélectionnez le message d’erreur.

   ![Sélectionner Échec du déploiement](./media/custom-event-quickstart-portal/failed-details.png)

   L’illustration suivante montre un déploiement ayant échoué du fait que le nom de la rubrique personnalisée est déjà utilisé. Si vous voyez cette erreur, réessayez le déploiement avec un nom différent.

   ![Conflit de noms](./media/custom-event-quickstart-portal/name-conflict.png)

## <a name="create-an-azure-function"></a>Création d’une fonction Azure

Avant de nous abonner à la rubrique, nous allons créer le point de terminaison pour le message de l’événement. Dans cet article, Azure Functions vous permet de créer une application de fonction pour le point de terminaison.

1. Pour créer une fonction, sélectionnez **Créer une ressource**.

   ![Créer une ressource](./media/custom-event-quickstart-portal/create-resource-small.png)

1. Sélectionnez **Compute** et **Function App**.

   ![Créer une fonction](./media/custom-event-quickstart-portal/create-function.png)

1. Donnez un nom unique à la fonction Azure. N’utilisez pas le nom indiqué dans l’image. Sélectionnez le groupe de ressources créé dans cet article. Pour le plan d’hébergement, utilisez **Plan de consommation**. Utilisez le nouveau compte de stockage suggéré. Après avoir défini les valeurs, sélectionnez **Créer**.

   ![Fournir des valeurs de fonction](./media/custom-event-quickstart-portal/provide-function-values.png)

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

   ![Accéder à la ressource](./media/custom-event-quickstart-portal/go-to-resource.png)

1. À côté de **Fonctions**, sélectionnez **+**.

   ![Ajouter une fonction](./media/custom-event-quickstart-portal/add-function.png)

1. Parmi les options disponibles, sélectionnez **Fonction personnalisée**.

   ![Fonction personnalisée](./media/custom-event-quickstart-portal/select-custom-function.png)

1. Faites défiler vers le bas jusqu'à trouver **Déclencheur Event Grid**. Sélectionnez **C#**.

   ![Sélectionnez le déclencheur Event Grid](./media/custom-event-quickstart-portal/select-event-grid-trigger.png)

1. Acceptez les valeurs par défaut, puis sélectionnez **Créer**.

   ![Nouvelle fonction](./media/custom-event-quickstart-portal/new-function.png)

Votre fonction est maintenant prête à recevoir des événements.

## <a name="subscribe-to-a-topic"></a>S’abonner à une rubrique

Vous vous abonnez à une rubrique pour communiquer à Event Grid les événements qui vous intéressent, et où les envoyer.

1. Dans votre fonction Azure, sélectionnez **Ajouter un abonnement Event Grid**.

   ![Ajouter un abonnement Event Grid](./media/custom-event-quickstart-portal/add-event-grid-subscription.png)

1. Entrez des valeurs pour l’abonnement. Sélectionnez **Rubriques Event Grid** comme type de rubrique. Pour l’abonnement et le groupe de ressources, sélectionnez l’abonnement et le groupe de ressources dans lesquels vous avez créé votre rubrique personnalisée. Par exemple, sélectionnez le nom de votre rubrique personnalisée. Le point de terminaison d’abonné est prérempli avec l’URL pour la fonction.

   ![Entrer des valeurs d’abonnement](./media/custom-event-quickstart-portal/provide-subscription-values.png)

1. Avant de déclencher l’événement, ouvrez les journaux pour la fonction afin que vous puissiez voir les données d’événement lorsqu’elles sont envoyées. Au bas de votre fonction Azure, sélectionnez **Journaux**.

   ![Sélectionner des journaux](./media/custom-event-quickstart-portal/select-logs.png)

Nous allons maintenant déclencher un événement pour voir comment Event Grid distribue le message à votre point de terminaison. Pour simplifier cet article, utilisez Cloud Shell pour envoyer des exemples de données d’événements à la rubrique personnalisée. En règle générale, une application ou un service Azure envoie les données d’événements.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="send-an-event-to-your-topic"></a>Envoyer un événement à votre rubrique

Tout d’abord, il nous faut l’URL et la clé de la rubrique. Utilisez le nom de votre rubrique pour `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g myResourceGroup --query "key1" --output tsv)
```

L’exemple suivant obtient les exemples de données d’événements :

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
```

Pour voir l’événement complet, utilisez `echo "$body"`. L’élément `data` du fichier JSON est la charge utile de l’événement. N’importe quel fichier JSON bien construit peut être placé dans ce champ. Vous pouvez aussi utiliser le champ objet pour un routage et un filtrage avancés.

CURL est un utilitaire qui envoie des requêtes HTTP. Dans cet article, utilisez CURL pour envoyer un événement à la rubrique personnalisée. 

```azurecli-interactive
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Vous avez déclenché l’événement, et Event Grid a envoyé le message au point de terminaison configuré lors de l’abonnement. Consultez les journaux pour afficher les données d’événement.

![Consulter les journaux](./media/custom-event-quickstart-portal/view-log-entry.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de continuer à utiliser cet événement, ne supprimez pas les ressources créées dans cet article. Dans le cas contraire, supprimez les ressources créées avec cet article.

Sélectionnez le groupe de ressources, puis **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer des rubriques et des abonnements d’événements personnalisés, vous pouvez en apprendre davantage sur Event Grid et ce qu’il peut vous offrir :

- [Event Grid](overview.md)
- [Acheminer des événements de stockage Blob Azure vers un point de terminaison Web personnalisé ](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md)
