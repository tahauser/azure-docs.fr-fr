---
title: Conserver les résultats ou les journaux des tâches et des travaux terminés dans un magasin de données - Azure Batch | Microsoft Docs
description: Découvrez les différentes possibilités pour conserver les données de sortie des tâches et des travaux Batch. Vous pouvez conserver les données dans le Stockage Azure ou un autre magasin de données.
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb8b1ca3514e27221e95cb2def823c8f89d151e5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="persist-job-and-task-output"></a>Conserver les résultats des tâches et des travaux

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Voici quelques exemples courants de sortie de tâche :

- Fichiers créés au moment où la tâche traite des données d’entrée.
- Fichiers journaux associés à l’exécution des tâches. 

Cet article décrit différentes possibilités pour conserver la sortie des tâches et présente les méthodes les plus appropriées en fonction des scénarios.   

## <a name="about-the-batch-file-conventions-standard"></a>À propos des conventions standard de nommage des fichiers Batch

Batch définit un ensemble facultatif de conventions pour nommer les fichiers de sortie de tâche dans le Stockage Azure. Les conventions standard de nommage des fichiers Batch sont décrites dans ce [document](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Les conventions standard de nommage des fichiers déterminent les noms du conteneur et du chemin d’objet blob de destination dans le Stockage Azure pour un fichier de sortie donné en fonction des noms du travail et de la tâche.

C’est à vous qu’il appartient d’appliquer ou non les conventions standard de nommage des fichiers pour vos fichiers de données de sortie. Vous pouvez aussi nommer le conteneur et l’objet blob de destination comme vous le souhaitez. Si vous utilisez les conventions standard de nommage des fichiers pour nommer vos fichiers de sortie, ceux-ci sont visibles dans le [portail Azure][portal].

Il existe différentes manières d’utiliser les conventions standard de nommage des fichiers :

- Si vous utilisez l’API du service Batch pour conserver les fichiers de sortie, vous pouvez choisir de nommer les conteneurs et les objets blob de destination conformément aux conventions standard de nommage des fichiers. L’API du service Batch vous permet de conserver les fichiers de sortie à partir du code client, sans modifier votre application de tâche.
- Si vous développez avec .NET, vous pouvez utiliser la [bibliothèque Conventions de nommage des fichiers Azure Batch pour .NET][nuget_package]. L’avantage de cette bibliothèque est qu’elle prend en charge l’interrogation des fichiers de sortie en fonction de leur ID ou de leur usage. La fonctionnalité d’interrogation intégrée permet d’accéder facilement aux fichiers de sortie à partir d’une application cliente ou à partir d’autres tâches. Cependant, votre application de tâche doit être modifiée de façon à appeler la bibliothèque Conventions de nommage des fichiers. Pour plus d’informations, consultez le document de référence concernant la [bibliothèque Conventions de nommage des fichiers pour .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Si vous développez avec un autre langage que .NET, vous pouvez implémenter les conventions standard de nommage des fichiers dans votre application.

## <a name="design-considerations-for-persisting-output"></a>Considérations de conception pour la conservation des sorties 

Au moment de concevoir votre solution Batch, tenez compte des points suivants s’agissant des sorties des travaux et des tâches.

* **Durée de vie de nœud de calcul**: les nœuds de calcul sont souvent temporaires, notamment dans les pools à mise à l’échelle automatique. La sortie d’une tâche qui s’exécute sur un nœud est disponible uniquement pendant que le nœud existe, et seulement pendant la période de rétention des fichiers que vous avez définie pour la tâche. Si une tâche génère une sortie qui peut être nécessaire à la fin de la tâche, celle-ci doit charger ses fichiers de sortie dans un magasin durable tels que le Stockage Azure.

* **Stockage de sortie** : le Stockage Azure est recommandé comme magasin de données pour la sortie des tâches, mais vous pouvez utiliser n’importe quel stockage durable. L’écriture de la sortie des tâches dans le Stockage Azure est intégrée dans l’API du service Batch. Si vous utilisez une autre forme de stockage durable, vous devez écrire la logique d’application pour conserver vous-même la sortie des tâches.   

* **Récupération de sortie** : vous pouvez récupérer la sortie des tâches directement à partir des nœuds de calcul de votre pool ou depuis le Stockage Azure ou un autre magasin de données si vous avez conservé la sortie des tâches. Pour récupérer la sortie d’une tâche directement à partir d’un nœud de calcul, vous avez besoin du nom de fichier et de son emplacement de sortie sur le nœud. Si vous conservez la sortie des tâches dans le Stockage Azure, vous avez besoin du chemin complet des fichiers dans le Stockage Azure pour télécharger les fichiers de sortie à l’aide du kit SDK Stockage Azure.

* **Affichage de sortie** : lorsque vous accédez à une tâche Batch dans le portail Azure et sélectionnez **Fichiers sur le nœud**, tous les fichiers associés à la tâche s’affichent, et pas seulement les fichiers de sortie qui vous intéressent. De nouveau, les fichiers hébergés sur les nœuds de calcul sont disponibles uniquement lorsque le nœud existe, et seulement pendant la durée de rétention des fichiers que vous avez définie pour la tâche. Pour afficher la sortie des tâches que vous avez conservée dans le Stockage Azure, vous pouvez utiliser le portail Azure ou une application cliente du Stockage Azure telle que l’[Explorateur de Stockage Azure][storage_explorer]. Pour afficher des données de sortie dans le Stockage Azure avec le portail ou un autre outil, vous devez connaître l’emplacement du fichier correspondant et y accéder directement.

## <a name="options-for-persisting-output"></a>Options de conservation de sortie

Selon votre scénario, vous pouvez adopter différentes approches pour conserver une sortie de tâche :

- Utiliser l’API du service Batch.  
- Utiliser la bibliothèque Conventions de nommage des fichiers Batch pour .NET.  
- Implémenter les conventions standard de nommage des fichiers Batch dans votre application.
- Implémenter une solution de déplacement de fichiers personnalisée.

Les sections suivantes décrivent plus en détail chacune de ces approches.

### <a name="use-the-batch-service-api"></a>Utiliser l’API du service Batch

Avec la version 2017-05-01, le service Batch permet de spécifier des fichiers de sortie dans le Stockage Azure pour les données de tâches au moment d’[ajouter une tâche à un travail](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) ou d’[ajouter une collection de tâches à un travail](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

L’API du service Batch prend en charge la conservation des données de tâches sur un compte Stockage Azure à partir des pools créés avec la configuration de machine virtuelle. Avec l’API du service Batch, vous pouvez conserver les données de tâches sans modifier l’application exécutée par votre tâche. Vous pouvez éventuellement vous conformer aux [conventions standard de nommage des fichiers Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) pour nommer les fichiers que vous conservez dans le Stockage Azure. 

Conservez la sortie des tâches à l’aide de l’API du service Batch dans les cas suivants :

- Vous voulez conserver les données des tâches Batch et des tâches du Gestionnaire de travaux dans des pools créés avec la configuration de machine virtuelle.
- Vous voulez conserver les données dans un conteneur de Stockage Azure avec un nom arbitraire.
- Vous voulez conserver les données dans un conteneur de Stockage Azure nommé conformément aux [conventions standard de nommage des fichiers Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> L’API du service Batch ne prend pas en charge la conservation des données des tâches s’exécutant dans des pools créés avec la configuration de service cloud. Pour plus d’informations sur la conservation de la sortie des tâches de pools exécutant la configuration de services cloud, consultez [Conserver les données des travaux et des tâches dans le Stockage Azure avec la bibliothèque Conventions de nommage des fichiers Batch pour .NET](batch-task-output-file-conventions.md).
> 
> 

Pour plus d’informations sur la conservation de la sortie des tâches avec l’API du service Batch, consultez [Conserver les données des tâches dans le Stockage Azure à l’aide de l’API du service Batch](batch-task-output-files.md). Consultez aussi l’exemple de projet [PersistOutputs][github_persistoutputs] sur GitHub, qui montre comment utiliser la bibliothèque cliente Batch pour .NET pour conserver la sortie des tâches dans un stockage durable.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Utiliser la bibliothèque Conventions de nommage des fichiers Batch pour .NET

Les développeurs qui élaborent des solutions Batch avec C# et .NET peuvent utiliser la [bibliothèque Conventions de nommage des fichiers pour .NET][nuget_package] pour conserver les données des tâches dans un compte de Stockage Azure, conformément aux [conventions standard de nommage des fichiers Batch](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). La bibliothèque Conventions de nommage des fichiers gère le déplacement des fichiers de sortie vers le Stockage Azure, ainsi que le nommage des conteneurs et objets blob de destination d’une façon bien connue.

La bibliothèque Conventions de nommage des fichiers prend en charge l’interrogation des fichiers de sortie par ID ou usage, ce qui permet de les localiser facilement sans avoir besoin d’URI de fichiers complets. 

Conservez la sortie des tâches à l’aide de la bibliothèque Conventions de nommage des fichiers Batch pour .NET dans les cas suivants :

- Vous voulez diffuser les données vers le Stockage Azure alors que la tâche est en cours d’exécution.
- Vous voulez conserver les données de pools créés avec la configuration de service cloud ou la configuration de machine virtuelle.
- Votre application cliente ou d’autres tâches du travail doivent localiser et télécharger les fichiers de sortie des tâches par ID ou usage. 
- Vous voulez procéder à la création de points de contrôle ou effectuer un chargement anticipé des résultats initiaux.
- Vous voulez consulter la sortie des tâches dans le portail Azure.

Pour plus d’informations sur la conservation de la sortie des tâches avec la bibliothèque Conventions de nommage des fichiers pour .NET, consultez [Conserver les données des travaux et des tâches dans le Stockage Azure avec la bibliothèque Conventions de nommage des fichiers Batch pour .NET](batch-task-output-file-conventions.md). Consultez aussi l’exemple de projet [PersistOutputs][github_persistoutputs] sur GitHub, qui montre comment utiliser la bibliothèque Conventions de nommage des fichiers pour .NET pour conserver la sortie des tâches dans un stockage durable.

L’exemple de projet [PersistOutputs][github_persistoutputs] sur GitHub montre comment utiliser la bibliothèque cliente Batch pour .NET pour conserver la sortie des tâches dans un stockage durable.

### <a name="implement-the-batch-file-conventions-standard"></a>Implémenter les conventions standard de nommage des fichiers Batch

Si vous utilisez un autre langage que .NET, vous pouvez implémenter les [conventions standard de nommage des fichiers](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) dans votre propre application. 

Vous pouvez souhaiter implémenter les conventions standard de nommage des fichiers par vous-même si vous voulez un schéma de nommage qui a fait ses preuves ou si vous voulez afficher la sortie des tâches dans le portail Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implémenter une solution de déplacement de fichiers personnalisée

Vous pouvez aussi implémenter votre propre solution de déplacement de fichiers complète. Suivez cette approche dans les cas suivants :

- Vous voulez conserver les données des tâches dans un magasin de données différent du Stockage Azure. Pour charger les fichiers dans un magasin de données comme Azure SQL ou Azure DataLake, vous pouvez créer un script personnalisé ou un exécutable à charger à cet emplacement. Vous pouvez ensuite l’appeler sur la ligne de commande après avoir exécuté votre exécutable principal. Par exemple, sur un nœud Windows, vous pouvez appeler ces deux commandes : `doMyWork.exe && uploadMyFilesToSql.exe`
- Vous voulez procéder à la création de points de contrôle ou effectuer un chargement anticipé des résultats initiaux.
- Vous voulez conserver un contrôle précis de la gestion des erreurs. Par exemple, vous pouvez souhaiter implémenter votre propre solution si vous voulez utiliser des actions de dépendance de tâches pour effectuer certaines actions de chargement en fonction de codes de sortie de tâche spécifiques. Pour plus d’informations sur les actions de dépendance de tâches, consultez [Créer des dépendances de tâches pour exécuter des tâches qui dépendent d’autres tâches](batch-task-dependencies.md). 

## <a name="next-steps"></a>Étapes suivantes

- Explorez l’utilisation des nouvelles fonctionnalités de l’API du service Batch pour conserver les données des tâches dans [Conserver les données des tâches dans le Stockage Azure à l’aide de l’API du service Batch](batch-task-output-files.md).
- Découvrez comment utiliser la bibliothèque Conventions de nommage des fichiers pour .NET dans [Conserver les données des travaux et des tâches dans le Stockage Azure avec la bibliothèque Conventions de nommage des fichiers Batch pour .NET](batch-task-output-file-conventions.md).
- Consultez l’exemple de projet [PersistOutputs][github_persistoutputs] sur GitHub, qui montre comment utiliser la bibliothèque du client Batch pour .NET et la bibliothèque Conventions de nommage des fichiers pour .NET pour conserver la sortie des tâches dans un stockage durable.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
