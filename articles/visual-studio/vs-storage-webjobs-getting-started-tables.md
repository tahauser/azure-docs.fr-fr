---
title: Prise en main du stockage Azure et des services connectés Visual Studio (projets WebJob)
description: Comment prendre en main le stockage de tables Azure dans un projet WebJobs Azure dans Visual Studio après s'être connecté à un compte de stockage à l'aide des services connectés de Visual Studio
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 061a6c46-0592-4e5d-aced-ab7498481cde
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 75ca4914fe60ec6f5c50e4942ad6d5cc7a6fe98b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="getting-started-with-azure-storage-azure-webjob-projects"></a>Prise en main d’Azure Storage (projets de tâche web Azure)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Vue d'ensemble
Cet article fournit des exemples de code C# qui montrent comment utiliser la version 1.x du Kit de développement logiciel (SDK) WebJobs Azure avec le service de stockage de tables Azure. Les exemples de code utilisent le [Kit de développement logiciel (SDK) WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) version 1.x.

Le service de stockage de tables Azure vous permet de stocker de grandes quantités de données structurées. Il s'agit d'une banque de données NoSQL qui accepte les appels authentifiés provenant de l'intérieur et de l'extérieur du cloud Azure. Les tables Azure sont idéales pour le stockage des données structurées non relationnelles.  Pour plus d’informations, consultez la section [Prise en main d’Azure Table Storage à l’aide de .NET](../cosmos-db/table-storage-how-to-use-dotnet.md#create-a-table) .

Certains extraits de code illustrent l’attribut **Table** utilisé dans des fonctions appelées manuellement, c’est-à-dire sans utiliser l’un des attributs de déclenchement.

## <a name="how-to-add-entities-to-a-table"></a>Ajout d’une entité à une table
Pour ajouter des entités à une table, utilisez l’attribut **Table** avec un paramètre **ICollector<T>** ou **IAsyncCollector<T>**, où **T** spécifie le schéma des entités que vous souhaitez ajouter. Le constructeur d’attribut prend un paramètre de chaîne qui spécifie le nom de la table.

L’exemple de code suivant ajoute des entités **Person** à une table nommée *Ingress*.

        [NoAutomaticTrigger]
        public static void IngressDemo(
            [Table("Ingress")] ICollector<Person> tableBinding)
        {
            for (int i = 0; i < 100000; i++)
            {
                tableBinding.Add(
                    new Person() {
                        PartitionKey = "Test",
                        RowKey = i.ToString(),
                        Name = "Name" }
                    );
            }
        }

En général, le type que vous utilisez avec **ICollector** est dérivé de **TableEntity** ou implémente **ITableEntity**, mais pas nécessairement. Les deux classes **Person** suivantes fonctionnent avec le code indiqué dans la méthode **Ingress** précédente.

        public class Person : TableEntity
        {
            public string Name { get; set; }
        }

        public class Person
        {
            public string PartitionKey { get; set; }
            public string RowKey { get; set; }
            public string Name { get; set; }
        }

Si vous souhaitez utiliser directement l’API Azure Storage, vous pouvez ajouter un paramètre **CloudStorageAccount** à la signature de méthode.

## <a name="real-time-monitoring"></a>Surveillance en temps réel
Étant donné que les fonctions d’entrée de données traitent souvent des volumes importants de données, le tableau de bord du Kit de développement logiciel (SDK) WebJobs fournit des données d’analyse en temps réel. La section **Journal d’appels** vous signale si la fonction est toujours en cours d’exécution.

![Fonction d’entrée en cours d’exécution](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

La page **Détails des appels** signale la progression de la fonction (c’est-à-dire le nombre d’entités écrites) alors qu’elle s’exécute et vous permet de l’arrêter.

![Fonction d’entrée en cours d’exécution](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Lorsque l’exécution de la fonction se termine, la page **Détails des appels** indique le nombre de lignes écrites.

![Arrêt de la fonction d’entrée](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## <a name="how-to-read-multiple-entities-from-a-table"></a>Lecture de plusieurs entrées à partir d’une table
Pour lire une table, utilisez l’attribut **Table** avec un paramètre **IQueryable<T>** où le type **T** est dérivé de **TableEntity** ou implémente **ITableEntity**.

L’exemple de code suivant lit et enregistre toutes les lignes de la table **Ingress** :

        public static void ReadTable(
            [Table("Ingress")] IQueryable<Person> tableBinding,
            TextWriter logger)
        {
            var query = from p in tableBinding select p;
            foreach (Person person in query)
            {
                logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
                    person.PartitionKey, person.RowKey, person.Name);
            }
        }

### <a name="how-to-read-a-single-entity-from-a-table"></a>Lecture d’une entité unique à partir d’une table
Il existe un constructeur d’attribut **Table** présentant deux paramètres supplémentaires, qui vous permettent de spécifier la clé de partition et la clé de ligne lorsque vous souhaitez effectuer une liaison avec une entité de table unique.

L’exemple de code suivant lit une ligne de table pour une entité **Person** basée sur des valeurs de clé de partition et de clé de ligne reçues dans un message en file d’attente :  

        public static void ReadTableEntity(
            [QueueTrigger("inputqueue")] Person personInQueue,
            [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
            TextWriter logger)
        {
            if (personInTable == null)
            {
                logger.WriteLine("Person not found: PK:{0}, RK:{1}",
                        personInQueue.PartitionKey, personInQueue.RowKey);
            }
            else
            {
                logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
                        personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
            }
        }


La classe **Person** figurant dans cet exemple n’est pas obligée d’implémenter **ITableEntity**.

## <a name="how-to-use-the-net-storage-api-directly-to-work-with-a-table"></a>Utilisation directe de l’API de stockage .NET pour travailler avec une table
Vous pouvez également utiliser l’attribut **Table** avec un objet **CloudTable**, afin de garantir une utilisation plus souple des tables.

L’exemple de code suivant utilise un objet **CloudTable** pour ajouter une entité unique à la table *Ingress* .

        public static void UseStorageAPI(
            [Table("Ingress")] CloudTable tableBinding,
            TextWriter logger)
        {
            var person = new Person()
                {
                    PartitionKey = "Test",
                    RowKey = "100",
                    Name = "Name"
                };
            TableOperation insertOperation = TableOperation.Insert(person);
            tableBinding.Execute(insertOperation);
        }

Pour en savoir plus sur l’utilisation de l’objet **CloudTable** , consultez la section [Prise en main d’Azure Table Storage à l’aide de .NET](../storage/storage-dotnet-how-to-use-tables.md).

## <a name="related-topics-covered-by-the-queues-how-to-article"></a>Sujets connexes traités dans l’article de procédure relatif aux files d’attente
Pour en savoir plus sur la gestion du traitement de tables déclenché par un message en file d’attente, ou pour consulter des scénarios relatifs au Kit de développement logiciel (SDK) WebJobs non spécifiques du traitement des tables, consultez la section [Prise en main d’Azure Queue Storage et des services connectés Visual Studio (projets WebJobs)](../storage/vs-storage-webjobs-getting-started-queues.md).

## <a name="next-steps"></a>Étapes suivantes
Cet article a fourni des exemples de code qui montrent comment gérer des scénarios courants pour l’utilisation des tables Azure. Pour plus d’informations sur l’utilisation d’Azure WebJobs et du Kit de développement logiciel (SDK) WebJobs, consultez la section [Ressources de documentation Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

