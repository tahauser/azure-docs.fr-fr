---
title: Prise en main du stockage File d’attente et des services connectés Visual Studio (projets WebJob) | Microsoft Docs
description: Comment commencer à utiliser le stockage de files d'attente Azure dans un projet WebJob après la connexion à un compte Azure Storage à l'aide des services connectés Visual Studio.
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 52c089d2657d61ee42730a00def060dec74439fc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Prise en main du stockage de files d'attente Azure et des services connectés Visual Studio (projets WebJob)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d'ensemble
Cet article explique comment prendre en main Stockage File d’attente Azure dans un projet WebJob Visual Studio Azure après avoir créé ou référencé un compte de stockage Azure à l’aide de la boîte de dialogue **Ajouter des services connectés** de Visual Studio. Quand vous ajoutez un compte de stockage à un projet de tâche web à l’aide de la boîte de dialogue **Ajouter des services connectés** de Visual Studio, le package NuGet d’Azure Storage approprié est installé, les références .NET appropriées sont ajoutées au projet et les chaînes de connexion pour le compte de stockage sont mises à jour dans le fichier App.config.  

Cet article fournit des exemples de code C# qui indiquent comment utiliser la version 1.x du Kit de développement logiciel (SDK) WebJobs Azure avec le service de stockage de files d’attente Azure.

Les files d’attente de stockage Azure sont un service permettant de stocker un grand nombre de messages accessibles depuis n’importe où dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d’attente peut avoir une taille de 64 Ko et une file d’attente peut contenir des millions de messages, jusqu’à la limite de capacité totale d’un compte de stockage. Pour plus d’informations, consultez la section [Prise en main d’Azure Queue Storage à l’aide de .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) . Pour plus d’informations sur ASP.NET, voir le site [ASP.NET](http://www.asp.net)(en anglais).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Déclenchement d'une fonction à la réception d'un message de file d'attente
Pour écrire une fonction que le Kit de développement logiciel (SDK) WebJobs appelle durant la réception d'un message en file d'attente, utilisez l'attribut **QueueTrigger** . Le constructeur d’attribut prend un paramètre de chaîne qui spécifie le nom de la file d’attente à interroger. Pour découvrir comment définir de manière dynamique le nom de la file d’attente, consultez la section [Définition des options de configuration](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Messages de file d’attente de chaîne
Dans l’exemple suivant, la file d’attente contient un message de chaîne, ainsi **QueueTrigger** est appliqué à un paramètre de chaîne nommé **logMessage** qui renferme le contenu du message de file d’attente. La fonction [écrit un message de journal dans le tableau de bord](#how-to-write-logs).

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

Outre **string**, le paramètre peut être un tableau d’octets, un objet **CloudQueueMessage** ou un objet POCO que vous définissez.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Messages en file d’attente POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Dans l’exemple suivant, le message de file d’attente contient JSON pour un objet **BlobInformation** qui inclut une propriété **BlobName**. Le Kit de développement logiciel (SDK) désérialise automatiquement l’objet.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Le Kit de développement logiciel (SDK) utilise le package [NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages. Si vous créez des messages en file d’attente dans un programme qui n’utilise pas le Kit de développement logiciel (SDK) WebJobs, vous pouvez écrire le code comme dans l’exemple suivant, afin de créer un message en file d’attente POCO que le Kit de développement logiciel (SDK) peut analyser.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Fonctions asynchrones
La fonction asynchrone suivante [écrit un journal dans le tableau de bord](#how-to-write-logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Les fonctions asynchrones peuvent prendre un [jeton d’annulation](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), comme illustré dans l’exemple suivant, qui copie un objet blob. (Pour obtenir une explication de l'espace réservé **queueTrigger** , consultez la section [Objets blob](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) ).

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

## <a name="types-the-queuetrigger-attribute-works-with"></a>Types gérés par l’attribut QueueTrigger
Vous pouvez utiliser l'attribut **QueueTrigger** avec les types suivants :

* **string**
* Type d’objet POCO sérialisé au format JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algorithme d’interrogation
Le Kit de développement logiciel (SDK) implémente un algorithme d’interruption exponentiel et aléatoire pour réduire l’effet de l’interrogation de file d’attente inactive sur les coûts de transactions de stockage.  Quand un message est détecté, le Kit de développement logiciel (SDK) attend deux secondes, puis vérifie s’il existe un autre message ; quand aucun message n’est détecté, il attend environ quatre secondes avant de réessayer. Après plusieurs échecs de tentatives d’obtention d’un message de file d’attente, le temps d’attente continue à augmenter jusqu’à ce qu’il atteigne le délai d’attente maximal par défaut (une minute). [Le délai d’attente maximal est configurable](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Instances multiples
Si votre application web s’exécute sur plusieurs instances, une tâche web continue se lance sur chaque machine, qui attend des déclencheurs et essaie d’exécuter les fonctions. Dans certains scénarios, cela peut entraîner que certaines fonctions traitent deux fois les mêmes données ; ces fonctions doivent donc être idempotentes (écrites de façon que, si elles sont appelées de manière répétitive avec les mêmes données d’entrée, elles ne produisent pas des résultats en double).  

## <a name="parallel-execution"></a>Exécution en parallèle
Si vous avez plusieurs fonctions à l’écoute sur différentes files d’attente, le Kit de développement logiciel (SDK) les appelle en parallèle en cas de réception de messages simultanés.

Il en est de même quand plusieurs messages sont reçus en provenance d’une file d’attente unique. Par défaut, le Kit de développement logiciel (SDK) obtient un lot de 16 messages de file d’attente à la fois et exécute la fonction qui les traite en parallèle. [La taille de lot est configurable](#how-to-set-configuration-options). Quand le nombre de messages en cours de traitement tombe sous la moitié de la taille de lot, le Kit de développement logiciel (SDK) obtient un autre lot et commence à traiter ces messages. Par conséquent, le nombre maximal de messages traités simultanément par la fonction est une fois et demie la taille de lot. Cette limite s'applique séparément à chaque fonction qui a un attribut **QueueTrigger** . Si vous ne souhaitez pas d’exécution en parallèle pour les messages reçus sur une file d’attente, affectez la valeur 1 à la taille de lot.

## <a name="get-queue-or-queue-message-metadata"></a>Obtention des métadonnées de file d'attente ou de message de file d'attente
Vous pouvez obtenir les propriétés de messages suivantes en ajoutant des paramètres à la signature de méthode :

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **string** queueTrigger (contient le texte du message)
* **string** id
* **string** popReceipt
* **int** dequeueCount

Si vous souhaitez travailler directement avec l'API de stockage Azure, vous pouvez également ajouter un paramètre **CloudStorageAccount** .

L’exemple suivant écrit toutes ces métadonnées dans un journal d’application INFO. Dans l’exemple, logMessage et queueTrigger contiennent le contenu du message de file d’attente.

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

Voici un exemple de journal écrit par l’exemple de code :

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Arrêt approprié
Une fonction qui s'exécute dans une tâche web WebJob continue peut accepter un paramètre **CancellationToken** qui permet au système d'exploitation de notifier la fonction quand la tâche web est sur le point de se terminer. Vous pouvez utiliser cette notification pour vous assurer que la fonction ne s’arrête pas de manière inattendue et laisse les données dans un état incohérent.

L’exemple suivant montre comment vérifier l’arrêt imminent d’une tâche web dans une fonction.

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**Remarque :** le tableau de bord peut ne pas afficher correctement l’état et la sortie des fonctions qui ont été arrêtées.

Pour plus d’informations, consultez [Arrêt correct de WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Création d'un message de file d'attente pendant le traitement d'un message de file d'attente
Pour écrire une fonction qui crée un message en file d'attente, utilisez l'attribut **Queue** . Comme **QueueTrigger**, vous transmettez le nom de la file d'attente sous forme de chaîne, ou vous pouvez [définir le nom de la file d'attente de manière dynamique](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Messages de file d’attente de chaîne
L’exemple de code non asynchrone suivant crée un message en file d’attente dans la file d’attente nommée « outputqueue », avec le même contenu que le message de file d’attente reçu dans la file d’attente « inputqueue ». (Dans le cas de fonctions asynchrones, utilisez l'élément **IAsyncCollector<T>** , comme indiqué plus loin dans la présente section.)

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Messages en file d’attente POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Pour créer un message de file d'attente qui contient un objet POCO plutôt qu'une chaîne, passez le type POCO en tant que paramètre de sortie au constructeur d'attribut **Queue** .

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

Le Kit de développement logiciel (SDK) sérialise automatiquement l’objet au format JSON. Un message de file d’attente est toujours créé, même si l’objet est null.

### <a name="create-multiple-messages-or-in-async-functions"></a>Création de plusieurs messages de file d’attente dans des fonctions asynchrones
Pour créer plusieurs messages, affectez **ICollector<T>** ou **IAsyncCollector<T>**, comme type de paramètre pour la file d’attente de sortie, comme indiqué dans l’exemple suivant.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Chaque message de file d'attente est créé immédiatement quand la méthode **Add** est appelée.

### <a name="types-that-the-queue-attribute-works-with"></a>Types gérés par l’attribut Queue
Vous pouvez utiliser l'attribut **Queue** sur les types de paramètres suivants :

* **out string** (crée le message en file d'attente si la valeur du paramètre n'est pas null lorsque la fonction se termine)
* **out byte[]** (fonctionne comme **string**)
* **out CloudQueueMessage** (fonctionne comme **string**)
* **out POCO** (type sérialisable, qui crée un message avec un objet null si le paramètre est null lorsque la fonction se termine)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (dédié à la création manuelle de messages via l'utilisation directe de l'API Azure Storage)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Utilisation des attributs du Kit de développement logiciel (SDK) WebJobs dans le corps d’une fonction
Si vous avez besoin d’effectuer une tâche dans votre fonction avant d’utiliser un attribut de Kit de développement logiciel (SDK) WebJobs comme **Queue**, **Blob** ou **Table**, vous pouvez utiliser l’interface **IBinder**.

L’exemple suivant prend un message en file d’attente d’entrée et crée un message avec le même contenu dans une file d’attente de sortie. Le nom de file d’attente de sortie est défini par le code dans le corps de la fonction.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

L’interface **IBinder** peut également être utilisée avec les attributs **Table** et **Blob**.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Lecture et écriture d'objets blob et de tables pendant le traitement d'un message de file d'attente
Les attributs **Blob** et **Table** vous permettent de lire et d’écrire des objets blob et des tables. Les exemples de cette section s’appliquent aux objets blob. Pour obtenir des exemples de code qui montrent comment déclencher des processus quand des objets blob sont créés ou mis à jour, consultez [Utilisation du stockage d’objets blob Azure avec le SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Messages en file d’attente de chaîne déclenchant des opérations d’objet blob
Pour un message de file d’attente qui contient une chaîne, **queueTrigger** est un espace réservé que vous pouvez utiliser dans le paramètre **blobPath** de l’attribut **Blob** qui contient le contenu du message.

L'exemple suivant utilise des objets **Stream** pour lire et écrire des objets blob. Le message de file d’attente correspond au nom d’un objet blob situé dans le conteneur textblobs. Une copie de l’objet blob, la chaîne « -new » étant ajoutée au nom, est créée dans le même conteneur.

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Le constructeur d’attribut **Blob** prend un paramètre **blobPath** qui indique le nom de l’objet blob et du conteneur. Pour en savoir plus sur cet espace réservé, consultez la section [Utilisation du stockage d’objets blob Azure avec le Kit de développement logiciel (SDK) WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Lorsque l’attribut décore un objet **Stream**, un autre paramètre de constructeur spécifie le mode **FileAccess** (lecture, écriture ou lecture/écriture).

L'exemple suivant utilise un objet **CloudBlockBlob** pour supprimer un objet blob. Le message de la file d’attente correspond au nom de l’objet blob.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Messages en file d’attente POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Pour un objet POCO stocké au format JSON dans le message de file d’attente, vous pouvez utiliser des espaces réservés qui nomment les propriétés de nom de l’objet dans le paramètre **blobPath** de l’attribut **Queue**. Vous pouvez également utiliser des noms de propriété de métadonnées de file d'attente comme espaces réservés. Consultez la section [Obtention des métadonnées de file d’attente ou de message de file d’attente](#get-queue-or-queue-message-metadata).

L’exemple suivant copie un objet blob dans un nouvel objet blob, avec une autre extension. Le message de la file d’attente est un objet **BlobInformation** qui inclut les propriétés **BlobName** et **BlobNameWithoutExtension**. Les noms de propriété sont utilisés en tant qu'espaces réservés dans le chemin de l'objet blob pour les attributs **blob** .

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

Le Kit de développement logiciel (SDK) utilise le package [NuGet Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json) pour sérialiser et désérialiser les messages. Si vous créez des messages en file d’attente dans un programme qui n’utilise pas le Kit de développement logiciel (SDK) WebJobs, vous pouvez écrire le code comme dans l’exemple suivant, afin de créer un message en file d’attente POCO que le Kit de développement logiciel (SDK) peut analyser.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Si vous devez effectuer un travail dans votre fonction avant de lier un objet blob à un objet, vous pouvez utiliser l’attribut dans le corps de la fonction comme indiqué dans [Utilisation des attributs du Kit de développement logiciel (SDK) WebJobs dans le corps d’une fonction](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Types avec lesquels vous pouvez utiliser l’attribut Blob
L'attribut **blob** peut être utilisé avec les types suivants :

* **Stream** (lecture ou écriture ; spécifié à l'aide du paramètre de constructeur FileAccess)
* **TextReader**
* **TextWriter**
* **string** (lecture)
* **out string** (écriture ; crée un objet blob uniquement si le paramètre de chaîne n'est pas null lorsque la fonction renvoie des résultats)
* POCO (lecture)
* out POCO (écriture ; crée systématiquement un objet blob, créé en tant qu’objet null si le paramètre POCO est null lorsque la fonction renvoie des résultats)
* **CloudBlobStream** (écriture)
* **ICloudBlob** (lecture ou écriture)
* **CloudBlockBlob** (lecture ou écriture)
* **CloudPageBlob** (lecture ou écriture)

## <a name="how-to-handle-poison-messages"></a>Gestion de messages incohérents
Les messages dont le contenu provoque l'échec d'une fonction sont appelés *messages incohérents*. Lorsque la fonction échoue, le message en file d’attente n’est pas supprimé, mais finalement récupéré à nouveau, provoquant la répétition du cycle. Le Kit de développement logiciel (SDK) peut interrompre automatiquement le cycle après un nombre limité d’itérations, ou vous pouvez le faire manuellement.

### <a name="automatic-poison-message-handling"></a>Gestion automatique des messages incohérents
Le Kit de développement logiciel (SDK) appelle une fonction jusqu’à cinq fois pour traiter un message de file d’attente. Si la cinquième tentative échoue, le message est déplacé vers une file d’attente de messages incohérents. Vous pouvez voir comment configurer le nombre maximal de nouvelles tentatives dans [Définition des options de configuration](#how-to-set-configuration-options).

La file d'attente de messages incohérents se nomme *{nom_file_d'attente_d'origine}*-poison. Vous pouvez écrire une fonction pour traiter les messages de la file d’attente de messages incohérents en les consignant dans un journal ou en envoyant une notification signalant qu’une attention manuelle est nécessaire.

Dans l'exemple suivant, la fonction **CopyBlob** ne fonctionnera pas si un message de file d'attente contient le nom d'un objet blob qui n'existe pas. Quand cela se produit, le message est déplacé de la file d’attente copyblobqueue vers la file d’attente copyblobqueue-poison. Le **ProcessPoisonMessage** enregistre ensuite le message incohérent.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

L’illustration suivante montre la sortie de console de ces fonctions pendant le traitement d’un message incohérent.

![Sortie de la console pour la gestion des messages incohérents](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Gestion manuelle des messages incohérents
Vous pouvez obtenir le nombre de fois qu’un message a été récupéré en vue de son traitement en ajoutant un paramètre **int** nommé **dequeueCount** à votre fonction. Vous pouvez ensuite vérifier le nombre d’enlèvements de la file d’attente dans le code de fonction et effectuer votre propre gestion des messages incohérents quand le nombre dépasse un seuil, comme illustré dans l’exemple suivant.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a name="how-to-set-configuration-options"></a>Définition des options de configuration
Vous pouvez utiliser le type **JobHostConfiguration** pour définir les options de configuration suivantes :

* Définition des chaînes de connexion du Kit de développement logiciel (SDK) dans le code.
* Configuration des paramètres **QueueTrigger** tels que le nombre de retraits maximal.
* Obtention des noms de file d’attente de la configuration.

### <a name="set-sdk-connection-strings-in-code"></a>Définition des chaînes de connexion du Kit de développement logiciel (SDK) dans le code
La définition des chaînes de connexion du Kit de développement logiciel (SDK) dans le code vous permet d’utiliser vos propres noms de chaînes de connexion dans les fichiers de configuration ou les variables d’environnement, comme illustré dans l’exemple suivant.

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;

            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;

            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="configure-queuetrigger--settings"></a>Configuration des paramètres QueueTrigger
Vous pouvez configurer les paramètres suivants, qui s’appliquent au traitement des messages en file d’attente :

* nombre maximal de messages de file d'attente prélevés simultanément pour être exécutés en parallèle (la valeur par défaut est 16) ;
* nombre maximal de tentatives avant l'envoi d'un message de file d'attente à une file d'attente de messages incohérents (la valeur par défaut est 5) ;
* durée d'attente maximale avant la nouvelle interrogation quand une file d'attente est vide (la valeur par défaut est 1 minute).

L’exemple suivant montre comment configurer ces paramètres :

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Définition des valeurs des paramètres de constructeur du Kit de développement logiciel (SDK) WebJobs dans le code
Dans certains cas, vous souhaitez spécifier dans le code un nom de file d’attente, un nom d’objet blob ou un conteneur, ou encore un nom de table, plutôt que de les coder en dur. Par exemple, vous pouvez souhaiter spécifier le nom de la file d'attente de **QueueTrigger** dans une variable d'environnement ou un fichier de configuration.

Vous pouvez le faire en transmettant un objet **NameResolver** au type **JobHostConfiguration**. Incluez des espaces réservés spéciaux, entourés par des signes de pourcentage (%) dans les paramètres de constructeur d'attribut de Kit de développement logiciel (SDK) pour que votre code **NameResolver** spécifie les valeurs réelles à utiliser à la place de ces espaces réservés.

Par exemple, supposons que vous souhaitez utiliser une file d’attente nommée logqueuetest dans l’environnement de test et une autre nommée logqueueprod en production. Au lieu d'un nom de file d'attente codé en dur, vous voulez spécifier le nom d'une entrée dans la collection **appSettings** ayant le nom de la file d'attente réelle. Si la clé **appSettings** est logqueue, votre fonction peut ressembler à l'exemple suivant.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

Votre classe **NameResolver** peut alors obtenir le nom de la file d’attente à partir d’**appSettings** comme indiqué dans l’exemple suivant :

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

Vous transmettez la classe **NameResolver** à l’objet **JobHost** comme indiqué dans l’exemple suivant.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**Remarque :** les noms d’objet blob, de table et de file d’attente sont résolus chaque fois qu’une fonction est appelée, mais les noms de conteneur d’objet blob sont uniquement résolus au démarrage de l’application. Vous ne pouvez pas modifier le nom d’un conteneur d’objet blob lorsque la tâche s’exécute.

## <a name="how-to-trigger-a-function-manually"></a>Déclenchement manuel d'une fonction
Pour déclencher manuellement une fonction, utilisez la méthode **Call** ou **CallAsync** sur l’objet **JobHost** et l’attribut **NoAutomaticTrigger** sur la fonction, comme indiqué dans l’exemple suivant.

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }

            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger,
                string value,
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## <a name="how-to-write-logs"></a>Écriture de journaux
Le tableau de bord affiche des journaux à deux emplacements : la page relative aux tâches web et la page portant sur l’appel d’une tâche web spécifique.

![Journaux affichés dans la page relative aux tâches web](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Journaux affichés dans la page d’appel de fonctions](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

La sortie des méthodes de console que vous appelez dans une fonction ou dans la méthode **Main()** s'affiche dans la page Tableau de bord de la tâche web, et non dans la page relative à l'appel d'une méthode particulière. La sortie de l’objet TextWriter que vous obtenez à partir d’un paramètre dans la signature de méthode s’affiche dans la page Tableau de bord relative à l’appel d’une méthode.

La sortie de la console ne peut pas être liée à un appel de méthode particulier, car la console présente un thread unique, tandis que de nombreuses fonctions de tâche peuvent s’exécuter en même temps. C’est pourquoi le Kit de développement logiciel (SDK) fournit à chaque appel de fonction son propre objet d’enregistreur de journal unique.

Pour écrire des [journaux de suivi d’application](../app-service/web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), utilisez **Console.Out** (crée des journaux marqués INFO) et **Console.Error** (crée des journaux marqués ERROR). Vous pouvez aussi utiliser des éléments [Trace ou TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), qui fournissent des niveaux supplémentaires (En clair, Avertissement et Critique). Les journaux de suivi d’application s’affichent dans les fichiers de journaux d’application web, les tables Microsoft Azure, ou les objets blob Microsoft Azure, selon la configuration de votre application web Microsoft Azure. Comme pour toutes les autres sorties de console, les 100 journaux d’application les plus récents s’affichent également dans la page Tableau de bord de la tâche web, et non dans la page d’appel d’une fonction.

La sortie de console s’affiche dans le tableau de bord uniquement si le programme s’exécute dans une tâche web Microsoft Azure, et non lorsque le programme est exécuté localement ou dans un autre environnement.

Vous pouvez désactiver la journalisation en définissant la chaîne de connexion du tableau de bord sur null. Pour plus d’informations, consultez la section [Définition des options de configuration](#how-to-set-configuration-options).

L’exemple suivant montre plusieurs manières d’écrire des journaux :

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

Dans le tableau de bord du Kit de développement logiciel (SDK) WebJobs, la sortie de l’objet **TextWriter** apparaît quand vous accédez à la page d’un appel de fonction spécifique et sélectionnez **Activer/désactiver la sortie** :

![Lien d’appel](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Journaux affichés dans la page d’appel de fonctions](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

Dans le tableau de bord du Kit de développement logiciel (SDK) WebJobs, les 100 lignes les plus récentes de la sortie de console apparaissent lorsque vous accédez à la page de la tâche web (et non à celle de l’appel de fonction) et que vous sélectionnez **Activer/désactiver la sortie**.

![Activer/désactiver la sortie](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Dans une tâche web continue, les journaux des applications apparaissent dans /data/jobs/continuous/*{nom_tâche_web*/job_log.txt dans le système de fichiers du site web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Dans un objet blob Azure, les journaux d’application ressemblent à ceci : 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Dans une table Azure, les journaux **Console.Out** et **Console.Error** ressemblent à ceci :

![Journal d’informations dans la table](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Journal d’erreurs dans la table](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Étapes suivantes
Cet article fournit des exemples de code qui montrent comment gérer des scénarios courants pour l’utilisation des files d’attente Azure. Pour plus d’informations sur l’utilisation d’Azure WebJobs et du Kit de développement logiciel (SDK) WebJobs, consultez la section [Ressources de documentation Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

