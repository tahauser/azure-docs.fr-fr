---
title: "Procédure détaillée d’Azure Event Hubs Capture | Microsoft Docs"
description: "Exemple qui utilise le kit SDK Azure Python pour illustrer l’utilisation de la fonctionnalité Event Hubs Capture."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 97cadbde2ddedade1a8688f1380b9ff9194613e7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="event-hubs-capture-walkthrough-python"></a>Procédure pas à pas d’Event Hubs Capture : Python

Capture est une fonctionnalité d’Azure Event Hubs. Vous pouvez l’utiliser pour fournir automatiquement les données de streaming de votre hub d’événements à un compte de stockage Blob Azure de votre choix. Cette fonctionnalité facilite le traitement par lots des données de flux en temps réel. Cet article décrit comment utiliser Event Hubs Capture avec Python. Pour plus d’informations sur Event Hubs Capture, consultez [l’article sur la vue d’ensemble](event-hubs-capture-overview.md).

Cet exemple utilise le [kit SDK Azure Python](https://azure.microsoft.com/develop/python/) pour illustrer la fonctionnalité Capture. Le programme sender.py envoie la télémétrie de l’environnement simulé à Event Hubs au format JSON. Le hub d’événements est configuré pour utiliser la fonctionnalité Capture afin d’écrire ces données dans le stockage Blob en lots. L’application capturereader.py lit ces objets blob et crée un fichier Append par appareil. Ensuite, elle écrit les données dans des fichiers .csv.

## <a name="what-youll-accomplish"></a>Ce que vous allez faire

1. Créer un compte de stockage Blob Azure et un conteneur d’objets blob dans celui-ci, à l’aide du portail Azure
2. Créer un espace de noms Event Hubs à l’aide du portail Azure
3. Créer un hub d’événements avec la fonctionnalité Capture activée dans le portail Azure
4. Envoyer des données au hub d’événements à l’aide d’un script Python
5. Lire les fichiers de la capture et les traiter à l’aide d’un autre script Python

## <a name="prerequisites"></a>Prérequis

- Python 2.7x
- Abonnement Azure
- Un [hub d’événements et un espace de noms Event Hubs](event-hubs-create.md) actifs

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-blob-storage-account"></a>Créer un compte de stockage Blob Azure
1. Connectez-vous au [portail Azure][Azure portal].
2. Dans le volet gauche du portail, sélectionnez **Nouveau** > **Stockage** > **Compte de stockage**.
3. Effectuez les sélections dans le volet **Créer un compte de stockage**, puis sélectionnez **Créer**.
   
   ![Volet « Créer un compte de stockage »][1]
4. Après l’affichage du message **Déploiements réussis**, sélectionnez le nom du nouveau compte de stockage et, dans le volet **Bases**, sélectionnez **Objets blob**. Quand le volet **Service blob** s’ouvre, sélectionnez **+ Conteneur** en haut. Nommez le conteneur **capture**, puis fermez le volet **Service blob**.
5. Sélectionnez **Clés d’accès** dans le volet gauche et copiez le nom du compte de stockage et la valeur de **key1**. Enregistrez ces valeurs dans le Bloc-notes ou un autre emplacement temporaire.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Créer un script Python pour envoyer des événements à votre hub d’événements
1. Ouvrez votre éditeur Python favori, tel que [Visual Studio Code][Visual Studio Code].
2. Créez un script appelé **sender.py**. Ce script envoie 200 événements à votre hub d’événements. Ce sont de simples lectures environnementales envoyées au format JSON.
3. Collez le code suivant dans sender.py :
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Mettez à jour le code précédent pour utiliser votre nom d’espace de noms, vos valeurs de clé et votre nom de hub d’événements obtenus lors de la création de l’espace de noms Event Hubs.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Créer un script Python pour lire vos fichiers Capture

1. Renseignez les champs du volet et sélectionnez **Créer**.
2. Créez un script appelé **capturereader.py**. Ce script lit les fichiers capturés et crée un fichier par appareil pour écrire les données uniquement pour cet appareil.
3. Collez le code suivant dans capturereader.py :
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed_json = json.loads(reading["Body"])
           if not 'id' in parsed_json:
               return
           if not dict.has_key(parsed_json['id']):
               list = []
               dict[parsed_json['id']] = list
           else:
               list = dict[parsed_json['id']]
               list.append(parsed_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print 'Processor started using path: ' + os.getcwd()
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
   ```
4. Collez les valeurs appropriées pour votre nom de compte de stockage et la clé dans l’appel à `startProcessing`.

## <a name="run-the-scripts"></a>Exécuter les scripts
1. Ouvrez une invite de commandes comprenant Python dans son chemin d’accès, puis exécutez ces commandes pour installer les packages de configuration requise Python :
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Si vous disposez d’une version antérieure de **azure-storage** ou **azure**, vous devrez peut-être utiliser l’option **--upgrade**.
   
   Vous devrez peut-être également exécuter la commande suivante (cela n’est pas nécessaire sur la plupart des systèmes) :
   
   ```
   pip install cryptography
   ```
2. Remplacez votre répertoire par celui dans lequel vous avez enregistré sender.py et capturereader.py, puis exécutez cette commande :
   
   ```
   start python sender.py
   ```
   
   Cette commande démarre un nouveau processus Python pour exécuter l’expéditeur.
3. Attendez quelques minutes pour que la capture s’exécute. Puis tapez la commande suivante dans la fenêtre de commande d’origine :
   
   ```
   python capturereader.py
   ```

   Ce processeur de capture utilise le répertoire local pour télécharger tous les objets blob à partir du compte de stockage / conteneur. Il traite tous ceux qui ne sont pas vides et écrit les résultats sous la forme de fichiers .csv dans le répertoire local.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Présentation d’Event Hubs Capture][Overview of Event Hubs Capture]
* [Exemples d’application complets qui utilisent des hubs d’événements](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Vue d’ensemble des hubs d’événements][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
