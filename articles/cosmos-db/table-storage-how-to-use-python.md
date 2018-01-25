---
title: Utilisation du stockage Table Azure avec Python | Microsoft Docs
description: "Stockez des données structurées dans le cloud à l’aide du stockage de tables Azure, un magasin de données NoSQL."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: a786f82d94a1a0039ed65a618670f872ffa3e3c2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-use-azure-table-storage-with-python"></a>Utilisation du stockage Table Azure avec Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Ce guide vous montre comment traiter des scénarios courants de stockage de table Azure en Python à l’aide du [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python](https://github.com/Azure/azure-storage-python). Les scénarios traités incluent la création et la suppression d'une table, l'insertion et l'interrogation d'entités.

Pendant que vous étudiez les scénarios de ce didacticiel, vous pouvez vous référer à la [Référence du kit de développement logiciel (SDK) Stockage Azure pour l’API Python](https://azure-storage.readthedocs.io/en/latest/index.html).

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>Installation du kit de développement logiciel (SDK) Microsoft Azure Storage pour Python

Une fois que vous avez créé un compte de stockage, l’étape suivante consiste à installer le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python](https://github.com/Azure/azure-storage-python). Pour plus de détails sur l’installation du kit de développement logiciel, reportez-vous au fichier [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) dans le kit de développement de stockage pour le référentiel de Python sur GitHub.

## <a name="create-a-table"></a>Création d’une table

Pour travailler avec le service de table Azure dans Python, vous devez importer le module [TableService][py_TableService]. Comme vous allez travailler avec les entités de table, vous avez également besoin de la classe [entité][py_Entity]. Ajoutez ce code vers le haut de votre fichier Python pour importer les deux :

```python
from azure.storage.table import TableService, Entity
```

Créez un objet [TableService][py_TableService], en passant votre clé de compte et le nom du compte de stockage. Remplacez `myaccount` et `mykey` par votre nom de compte et votre clé, et appelez [create_table][py_create_table] pour créer la table dans le stockage Azure.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table

Pour ajouter une entité, vous créez d’abord un objet qui représente votre entité, puis passez l’objet à la méthode [TableService][py_TableService].[insert_entity][py_insert_entity]. L’objet d’entité peut être un dictionnaire ou un objet de type [Entité][py_Entity], et définit les noms et valeurs de votre entité. Chaque entité doit inclure les propriétés [PartitionKey et RowKey](#partitionkey-and-rowkey), en plus de toutes les autres propriétés que vous définissez pour l’entité.

Cet exemple crée un objet dictionnaire représentant une entité, puis le passe à la méthode [insert_entity][py_insert_entity] pour l’ajouter à la table :

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Cet exemple crée un objet [Entité][py_Entity], puis le passe à la méthode [insert_entity][py_insert_entity] pour l’ajouter à la table :

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey et RowKey

Vous devez spécifier les propriétés **PartitionKey** et **RowKey** pour chaque entité. Il s’agit des identificateurs uniques de vos entités, car ils forment ensemble la clé primaire d’une entité. Vous pouvez interroger à l’aide de ces valeurs beaucoup plus vite que vous pouvez interroger d’autres propriétés d’entité, car seules ces propriétés sont indexées.

Le service de table utilise **PartitionKey** pour répartir intelligemment les entités de table entre nœuds de stockage. Les entités partageant la même clé **PartitionKey** sont stockées sur le même nœud. **RowKey** est l'identifiant unique de l'entité dans sa partition.

## <a name="update-an-entity"></a>Mise à jour d'une entité

Pour mettre à jour toutes les valeurs de propriété d’une entité, appelez la méthode [update_entity][py_update_entity]. Cet exemple montre comment remplacer d'une entité existante par une version mise à jour :

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Si l'entité à remplacer n'existe pas encore, l'opération de mise à jour échoue. Si vous voulez stocker une entité, qu’elle existe déjà ou non, utilisez [insert_or_replace_entity][py_insert_or_replace_entity]. Dans l'exemple suivant, le premier appel remplace l'entité existante. Le deuxième appel insère une nouvelle entité, car il n’existe aucune entité ayant les clés PartitionKey et RowKey spécifiées.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> La méthode [update_entity][py_update_entity] remplace toutes les propriétés et valeurs d’une entité existante, ce qui vous permet également de supprimer les propriétés d’une entité existante. Vous pouvez utiliser la méthode [merge_entity][py_merge_entity] pour mettre à jour une entité existante avec des valeurs de propriétés nouvelles ou modifiées sans remplacement complet de l’entité.

## <a name="modify-multiple-entities"></a>Modifier plusieurs entités

Pour garantir le traitement atomique d’une demande par le service de table, vous pouvez envoyer plusieurs opérations dans un lot. Tout d’abord, utilisez la classe [TableBatch][py_TableBatch] pour ajouter plusieurs opérations à un seul lot. Ensuite, appelez [TableService][py_TableService].[ commit_batch][py_commit_batch] pour envoyer les opérations dans une opération atomique. Toutes les entités à modifier dans le lot doivent être dans la même partition.

Cet exemple permet d'ajouter deux entités dans un lot :

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Les lots peuvent également être utilisés avec la syntaxe du gestionnaire de contexte :

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Interrogation d’une entité

Pour rechercher une entité dans une table, passez ses paramètres PartitionKey et RowKey à la méthode [TableService][py_TableService].[ get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Interrogation d’un ensemble d’entités

Vous pouvez interroger un jeu d’entités en fournissant une chaîne de filtre avec le paramètre **filter**. Cet exemple recherche toutes les tâches dans Seattle pour appliquer un filtre sur PartitionKey :

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Interrogation d'un sous-ensemble de propriétés d'entité

Vous pouvez également restreindre les propriétés renvoyées pour chaque entité dans une requête. Cette technique, nommée *projection*, réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses ou les jeux de résultats. Utilisez le paramètre **select** et transmettez le nom des propriétés à renvoyer au client.

La requête contenue dans le code suivant ne renvoie que la description des entités de la table.

> [!NOTE]
> L’extrait suivant ne fonctionne qu’avec Stockage Azure. L’émulateur de stockage ne le prend pas en charge.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Suppression d’une entité

Supprimez une entité en passant des propriétés PartitionKey et RowKey à la méthode [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Suppression d’une table

Si vous n’avez plus besoin une table ou une des entités qui s’y trouvent, appelez la méthode [delete_table][py_delete_table] pour supprimer définitivement la table du stockage Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>étapes suivantes

* [Référence du kit de développement logiciel (SDK) Stockage Azure pour l’API Python](https://azure-storage.readthedocs.io/en/latest/index.html)
* [Kit de développement logiciel (SDK) Stockage Azure pour Python](https://github.com/Azure/azure-storage-python)
* [Centre de développement Python](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) : une application gratuite et multiplateforme de Microsoft qui vous permet d’exploiter visuellement les données de stockage Azure sur Windows, macOS et Linux.

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch