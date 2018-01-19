---
title: "Rendre les données d’application hautement disponibles dans Azure | Microsoft Docs"
description: "Utiliser le stockage géoredondant avec accès en lecture pour rendre vos données d’application hautement disponibles"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 63ca91c2eadf7b003427e9716d99621fca1b1a19
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Rendre vos données d’application hautement disponibles avec Stockage Azure

Ce didacticiel est la première partie d’une série d’étapes. Ce didacticiel vous montre comment rendre vos données d’application hautement disponibles dans Azure. Quand vous avez terminé, vous disposez d’une application console .NET Core qui charge et récupère un objet blob dans un compte de stockage [géographiquement redondant avec accès en lecture ](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS). Le stockage RA-GRS réplique des transactions de la région primaire vers la région secondaire. Ce processus de réplication garantit que les données de la région secondaire sont cohérentes. L’application utilise le modèle [Disjoncteur](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) pour déterminer à quel point de terminaison se connecter. L’application bascule vers le point de terminaison secondaire quand une défaillance est simulée.

Dans ce premier volet, vous apprenez à :

> [!div class="checklist"]
> * Créez un compte de stockage.
> * Téléchargez l’exemple
> * Définir la chaîne de connexion
> * Exécuter l’application console

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce didacticiel :

* Installez [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec les charges de travail suivantes :
  - **Développement Azure**

  ![Développement Azure (sous Web & Cloud)](media/storage-create-geo-redundant-storage/workloads.png)

* Téléchargez et installez [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Le compte de stockage Azure fournit un espace de noms unique pour stocker les objets de données de Stockage Azure et y accéder.

Suivez ces étapes pour créer un compte de stockage géographiquement redondant avec accès en lecture :

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Stockage** dans la page **Nouveau**, puis sélectionnez **Compte de stockage - blob, fichier, table, file d’attente** sous **Sélection**.
3. Remplissez le formulaire de compte de stockage avec les informations suivantes, comme indiqué dans l’image ci-après et sélectionnez **Créer** :

   | Paramètre       | Valeur suggérée | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Name** | mystorageaccount | Valeur unique pour votre compte de stockage |
   | **Modèle de déploiement** | Gestionnaire de ressources  | Le Gestionnaire des ressources contient les fonctionnalités les plus récentes.|
   | **Type de compte** | Usage général | Pour plus d’informations sur les types de compte, consultez [Types de compte de stockage](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Performances** | standard | Le type Standard est suffisant pour l’exemple de scénario. |
   | **Réplication**| Stockage géo-redondant avec accès en lecture (RA-GRS) | Ce paramètre est nécessaire pour que l’exemple fonctionne. |
   |**Transfert sécurisé requis** | Désactivé| Le transfert sécurisé n’est pas nécessaire pour ce scénario. |
   |**Abonnement** | Votre abonnement |Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   |**Lieu** | Est des États-Unis | Choisissez un emplacement. |

![créer un compte de stockage](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>Téléchargez l’exemple

[Téléchargez l’exemple de projet](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip).

Extrayez (décompressez) le fichier storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip.
L’exemple de projet contient une application console.

## <a name="set-the-connection-string"></a>Définir la chaîne de connexion

Dans l’application, vous devez fournir la chaîne de connexion de votre compte de stockage. Il est recommandé de stocker cette chaîne de connexion dans une variable d’environnement sur l’ordinateur local exécutant l’application. Suivez l’un des exemples ci-dessous, en fonction de votre système d’exploitation, pour créer la variable d’environnement.

Dans le portail Azure, accédez à votre compte de stockage. Sélectionnez **Clés d’accès** sous **Paramètres** dans votre compte de stockage. Copiez la **chaîne de connexion** de la clé principale ou secondaire. Remplacez \<yourconnectionstring\> par la chaîne de connexion réelle en exécutant l’une des commandes suivantes pour votre système d’exploitation. Cette commande enregistre une variable d’environnement sur la machine locale. Dans Windows, la variable d’environnement n’est pas disponible tant que vous n’avez pas rechargé **l’invite de commandes** ou l’interpréteur de commandes que vous utilisez. Remplacez **\<storageConnectionString\>**  dans l’exemple suivant :

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

![fichier de configuration d’application](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>Exécuter l’application console

Dans Visual Studio, appuyez sur **F5** ou sélectionnez **Démarrer** pour commencer le débogage de l’application. Visual Studio restaure automatiquement les packages NuGet manquants si cette option est configurée. Pour en savoir plus, consultez [Installation et réinstallation de packages avec la restauration de packages](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview).

Une fenêtre de console apparaît et l’application commence à s’exécuter. L’application charge l’image **HelloWorld.png** de la solution dans le compte de stockage. L’application vérifie que l’image s’est répliquée sur le point de terminaison RA-GRS secondaire. Elle commence ensuite à télécharger l’image jusqu’à 999 fois. Chaque lecture est représentée par un **P** ou un **S**. **P** représente le point de terminaison principal et **S** le point de terminaison secondaire.

![Exécution de l’application console](media/storage-create-geo-redundant-storage/figure3.png)

Dans l’exemple de code, la tâche `RunCircuitBreakerAsync` dans le fichier `Program.cs` est utilisée pour télécharger une image du compte de stockage à l’aide de la méthode [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet). Avant le téléchargement, un [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) est défini. Le contexte d’opération définit les gestionnaires d’événements, qui se déclenchent quand un téléchargement se termine correctement ou si un téléchargement échoue et effectue une nouvelle tentative.

### <a name="retry-event-handler"></a>Gestionnaire d’événements de nouvelle tentative

Le Gestionnaire d’événements `OperationContextRetrying` est appelé quand le téléchargement de l’image échoue et qu’une nouvelle tentative est définie. Si le nombre maximal de tentatives définies dans l’application est atteint, le paramètre [LocationMode](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) de la requête passe à `SecondaryOnly`. Ce paramètre oblige l’application à essayer de télécharger l’image à partir du point de terminaison secondaire. Cette configuration réduit le temps nécessaire pour demander l’image puisque les nouvelles tentatives ne sont pas indéfiniment effectuées sur le point de terminaison principal.

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Gestionnaire d’événements de demande terminée

Le Gestionnaire d’événements `OperationContextRequestCompleted` est appelé quand le téléchargement de l’image est réussi. Si l’application utilise le point de terminaison secondaire, elle continue à utiliser ce point de terminaison jusqu’à 20 fois. Au bout de 20 fois, l’application redéfinit le paramètre [LocationMode](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) sur `PrimaryThenSecondary` et réessaie le point de terminaison principal. Si une requête réussit, l’application poursuit la lecture à partir du point de terminaison principal.

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>étapes suivantes

Dans la première partie de la série, vous avez appris à rendre une application hautement disponible avec des comptes de stockage RA-GRS, en effectuant les opérations suivantes :

> [!div class="checklist"]
> * Créez un compte de stockage.
> * Téléchargez l’exemple
> * Définir la chaîne de connexion
> * Exécuter l’application console

Passez à la deuxième partie de la série pour apprendre à simuler un échec et à forcer votre application à utiliser le point de terminaison RA-GRS secondaire.

> [!div class="nextstepaction"]
> [Simuler un échec de connexion à votre point de terminaison de stockage principal](storage-simulate-failure-ragrs-account-app.md)
