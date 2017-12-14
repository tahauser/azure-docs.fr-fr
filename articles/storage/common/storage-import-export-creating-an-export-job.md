---
title: "Création d’un travail d’exportation pour Azure Import/Export | Microsoft Docs"
description: "Découvrez comment créer un travail d’exportation pour le service Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 70f74b685f4d5b902b6f6780f7c1de4d357164b8
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Création d’un travail d’exportation pour le service Azure Import/Export
La création d’un travail d’exportation pour le service Microsoft Azure Import/Export à l’aide de l’API REST implique les étapes suivantes :

-   Sélection des blobs à exporter.

-   Obtention d’un emplacement d’expédition.

-   Création du travail d’exportation.

-   Expédition de vos disques vides à Microsoft via un service de transport pris en charge.

-   Mise à jour du travail d’exportation avec les informations du colis.

-   Réception des disques renvoyés par Microsoft.

 Consultez [Transfert de données vers le stockage Blob à l’aide du service Microsoft Azure Import/Export](storage-import-export-service.md) pour une présentation du service Import/Export et un didacticiel expliquant comment utiliser le [portail Azure](https://portal.azure.com/) pour créer et gérer les travaux d’importation et d’exportation.

## <a name="selecting-blobs-to-export"></a>Sélection des blobs à exporter
 Pour créer un travail d’exportation, vous devrez fournir une liste des blobs que vous souhaitez exporter à partir de votre compte de stockage. Plusieurs options s’offrent à vous pour sélectionner les blobs à exporter :

-   Vous pouvez utiliser un chemin d’accès de blob relatif pour sélectionner un seul objet blob et toutes ses captures instantanées.

-   Vous pouvez utiliser un chemin d’accès de blob relatif pour sélectionner un seul objet blob, sans ses captures instantanées.

-   Vous pouvez utiliser un chemin d’accès de blob relatif et une heure de capture instantanée pour sélectionner une seule capture instantanée.

-   Vous pouvez utiliser un préfixe de blob pour sélectionner tous les blobs et les captures instantanées avec le préfixe spécifié.

-   Vous pouvez exporter tous les blobs et les captures instantanées dans le compte de stockage.

 Pour plus d’informations sur la spécification des blobs à exporter, consultez l’opération [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate).

## <a name="obtaining-your-shipping-location"></a>Obtention de votre emplacement d’expédition
Avant de créer un travail d’exportation, vous devez obtenir un nom et une adresse d’emplacement d’expédition en appelant l’opération [Get Location](https://portal.azure.com) ou [List Locations](/rest/api/storageimportexport/listlocations). `List Locations` renvoie une liste d’emplacements et leurs adresses postales. Vous pouvez sélectionner un emplacement dans la liste renvoyée et expédier vos disques durs à cette adresse. Vous pouvez également utiliser l’opération `Get Location` pour obtenir directement l’adresse d’expédition relative à un emplacement spécifique.

Suivez les étapes ci-dessous pour obtenir l’emplacement d’expédition :

-   Identifiez le nom de l’emplacement de votre compte de stockage. Cette valeur se trouve sous le champ **Emplacement** sur le **Tableau de bord** du compte de stockage dans le portail Azure. Elle peut également être obtenue en utilisant l’opération de l’API Gestion des services [Get Storage Account Properties](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties).

-   Récupérez l’emplacement disponible pour traiter ce compte de stockage en appelant l’opération `Get Location`.

-   Si la propriété `AlternateLocations` de l’emplacement contient l’emplacement lui-même, alors il est possible d’utiliser cet emplacement. Sinon, appelez une nouvelle fois l’opération `Get Location` en utilisant l’un des autres emplacements. L’emplacement d’origine peut être fermé temporairement pour maintenance.

## <a name="creating-the-export-job"></a>Création du travail d’exportation
 Pour créer le travail d’exportation, appelez l’opération [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate). Vous devez fournir les informations suivantes :

-   Nom du travail.

-   nom du compte de stockage.

-   Nom de l’emplacement d’expédition, obtenu à l’étape précédente.

-   Type de travail (exportation).

-   L’adresse de retour à laquelle les disques doivent être envoyés une fois le travail d’exportation terminé.

-   La liste des blobs (ou des préfixes de blob) à exporter.

## <a name="shipping-your-drives"></a>Expédition de vos disques
 Ensuite, utilisez l’outil Azure Import/Export pour déterminer le nombre de disques à envoyer, en fonction des blobs que vous avez sélectionnés pour l’exportation et de la taille du disque. Consultez la [référence sur l’outil Azure Import/Export](storage-import-export-tool-how-to-v1.md) pour plus d’informations.

 Placez les disques dans un seul colis et expédiez-les à l’adresse obtenue à l’étape précédente. Notez le numéro de suivi de votre colis pour l’étape suivante.

> [!NOTE]
>  Vous devez expédier vos disques via un service de transport pris en charge, qui vous fournira un numéro de suivi pour votre colis.

## <a name="updating-the-export-job-with-your-package-information"></a>Mise à jour du travail d’exportation avec vos informations de colis
 Dès que vous avez votre numéro de suivi, appelez l’opération [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) pour mettre à jour le numéro de suivi et le nom du transporteur correspondant au travail. Vous pouvez éventuellement spécifier le nombre de disques, l’adresse de retour et la date d’expédition.

## <a name="receiving-the-package"></a>Réception du package
 Une fois votre travail d’exportation traité, vos disques vous sont renvoyés avec vos données chiffrées. Vous pouvez récupérer la clé BitLocker pour chacun des disques en appelant l’opération [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). Cette clé vous permettra de déverrouiller le disque. Le fichier manifeste de disque sur chaque disque contient la liste des fichiers sur le disque, ainsi que l’adresse du blob d’origine pour chaque fichier.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)
