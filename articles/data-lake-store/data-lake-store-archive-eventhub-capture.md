---
title: "Capturer des données Event Hubs dans Azure Data Lake Store | Microsoft Docs"
description: "Utiliser Azure Data Lake Store pour capturer des données Event Hubs"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: ac8000abc35cba89c4bf655a4806636933ab8d08
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Utiliser Azure Data Lake Store pour capturer des données Event Hubs

Découvrez comment utiliser Azure Data Lake Store pour capturer les données reçues par Azure Event Hubs.

## <a name="prerequisites"></a>Prérequis

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d’Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Un espace de noms Event Hubs**. Pour obtenir des instructions, consultez [Créer un espace de noms Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Vérifiez que le compte Data Lake Store et l’espace de noms Event Hubs appartiennent au même abonnement Azure.


## <a name="assign-permissions-to-event-hubs"></a>Affecter des autorisations à Event Hubs

Dans cette section, vous allez créer un dossier au sein du compte dans lequel vous souhaitez capturer les données Event Hubs. Vous allez également affecter des autorisations à Event Hubs pour qu’il puisse écrire des données dans un compte Data Lake Store. 

1. Ouvrez le compte Data Lake Store dans lequel vous souhaitez capturer les données Event Hubs, puis cliquez sur **Explorateur de données**.

    ![Explorateur de données Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Explorateur de données Data Lake Store")

2.  Cliquez sur **Nouveau dossier**, puis entrez un nom pour le dossier dans lequel vous souhaitez capturer les données.

    ![Créer un dossier dans Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Créer un dossier dans Data Lake Store")

3. Affectez des autorisations à la racine du compte Data Lake Store. 

    a. Cliquez sur **Explorateur de données**, sélectionnez la racine du compte Data Lake Store, puis cliquez sur **Accès**.

    ![Affecter des autorisations pour la racine de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Affecter des autorisations pour la racine de Data Lake Store")

    b. Sous **Accès**, cliquez sur **Ajouter**, cliquez sur **Sélectionner un utilisateur ou un groupe**, puis recherchez `Microsoft.EventHubs`. 

    ![Affecter des autorisations pour la racine de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Affecter des autorisations pour la racine de Data Lake Store")
    
    Cliquez sur **Sélectionner**.

    c. Sous **Affecter des autorisations**, cliquez sur **Sélectionner des autorisations**. Définissez **Autorisations** à **Exécuter**. Définissez **Ajouter à** sur **Ce dossier et tous ses enfants**. Définissez **Ajouter en tant que** sur **Une entrée d’autorisation d’accès et une entrée d’autorisation par défaut**.

    ![Affecter des autorisations pour la racine de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Affecter des autorisations pour la racine de Data Lake Store")

    Cliquez sur **OK**.

4. Affectez des autorisations pour le dossier situé dans le compte Data Lake Store où vous voulez capturer des données.

    a. Cliquez sur **Explorateur de données**, sélectionnez le dossier du compte Data Lake Store, puis cliquez sur **Accès**.

    ![Affecter des autorisations pour le dossier Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Affecter des autorisations pour le dossier Data Lake Store")

    b. Sous **Accès**, cliquez sur **Ajouter**, cliquez sur **Sélectionner un utilisateur ou un groupe**, puis recherchez `Microsoft.EventHubs`. 

    ![Affecter des autorisations pour le dossier Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Affecter des autorisations pour le dossier Data Lake Store")
    
    Cliquez sur **Sélectionner**.

    c. Sous **Affecter des autorisations**, cliquez sur **Sélectionner des autorisations**. Définissez **Autorisations** sur **Lecture, écriture** et **exécution**. Définissez **Ajouter à** sur **Ce dossier et tous ses enfants**. Enfin, définissez **Ajouter en tant que** sur **Une entrée d’autorisation d’accès et une entrée d’autorisation par défaut**.

    ![Affecter des autorisations pour le dossier Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Affecter des autorisations pour le dossier Data Lake Store")
    
    Cliquez sur **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Configurer Event Hubs pour capturer des données et les envoyer vers Data Lake Store

Dans cette section, vous allez créer un Event Hub dans un espace de noms Event Hubs. Vous allez également configurer l’Event Hub pour capturer des données et les envoyer vers un compte Azure Data Lake Store. Cette section part du principe que vous avez déjà créé un espace de noms Event Hubs.

2. Dans le volet **Vue d’ensemble** de l’espace de noms Event Hubs, cliquez sur **+ Event Hub**.

    ![Créer un Event Hub](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Créer un Event Hub")

3. Fournissez les valeurs suivantes pour configurer Event Hubs de manière à capturer des données et à les envoyer vers Data Lake Store.

    ![Créer un Event Hub](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Créer un Event Hub")

    a. Donnez un nom à l’Event Hub.
    
    b. Pour ce didacticiel, définissez **Nombre de partitions** et **Rétention des messages** sur les valeurs par défaut.
    
    c. Définissez **Capture** sur **Activé**. Définissez la **Fenêtre de temps** (fréquence de capture) et la **Fenêtre de taille** (taille des données à capturer). 
    
    d. Pour **Fournisseur de capture**, sélectionnez **Azure Data Lake Store**, puis sélectionnez le Data Lake Store que vous avez créé précédemment. Pour **Chemin Data Lake**, entrez le nom du dossier que vous avez créé dans le compte Data Lake Store. Vous devez uniquement fournir le chemin relatif du dossier.

    e. Pour l’option **Exemples de formats de nom de fichier de capture**, conservez la valeur par défaut. Cette option détermine la structure de dossiers qui est créée sous le dossier de capture.

    f. Cliquez sur **Créer**.

## <a name="test-the-setup"></a>Tester la configuration

Vous pouvez maintenant tester la solution en envoyant des données vers Azure Event Hub. Suivez les instructions de la rubrique [Envoyer des événements vers les hubs d’événements Azure avec .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Une fois que vous démarrez l’envoi des données, vous pouvez voir les données dans Data Lake Store avec la structure de dossiers que vous avez spécifiée. Vous pouvez voir une structure de dossiers, telle que celle de la capture d’écran ci-dessous, dans Data Lake Store.

![Exemple de données EventHub dans Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Exemple de données EventHub dans Data Lake Store")

> [!NOTE]
> Même si aucun message n’entre dans Event Hubs, celui-ci écrit des fichiers vides avec en-tête dans le compte Data Lake Store. Les fichiers sont écrits au même intervalle de temps que celui que vous avez configuré lors de la création des Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Analyse des données dans Data Lake Store

Une fois les données dans Data Lake Store, vous pouvez exécuter des tâches analytiques pour traiter et analyser les données. Pour plus d’informations sur cette procédure à l’aide d’Azure Data Lake Analytics, consultez [Exemple Avro USQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).
  

## <a name="see-also"></a>Voir aussi
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Copier des données d’objets blob Azure Storage vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
