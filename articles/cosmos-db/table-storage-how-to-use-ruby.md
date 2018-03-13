---
title: Utilisation du stockage Table Azure avec Ruby | Microsoft Docs
description: "Stockez des données structurées dans le cloud à l’aide du stockage de tables Azure, un magasin de données NoSQL."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: decc6ffb38a4358d3593642f9cedb59d08f6bfef
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-use-azure-table-storage-with-ruby"></a>Utilisation du stockage Table Azure avec Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Vue d'ensemble
Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de tables Azure. Les exemples sont écrits à l'aide de l'API Ruby. Les scénarios traités incluent la **création et la suppression d'une table, l'insertion et l'interrogation d'entités dans une table**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Création d'une application Ruby
Pour obtenir des instructions sur la création d’une application Ruby, consultez la page [Application web Ruby on Rails sur une machine virtuelle Azure](../virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Configuration de votre application pour accéder au stockage
Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services REST d’Azure Storage.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilisation de RubyGems pour obtenir le package
1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Tapez **gem install azure** dans la fenêtre de commande pour installer gem et les dépendances.

### <a name="import-the-package"></a>Importation du package
À l’aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser Azure Storage :

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Configurer une connexion Azure Storage
Le module Azure lit les variables d’environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY** pour obtenir les informations nécessaires à la connexion à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::TableService** grâce au code suivant :

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Pour obtenir ces valeurs à partir d’un compte de stockage classique ou Resource Manager sur le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au compte de stockage que vous souhaitez utiliser.
3. Dans le panneau Paramètres à droite, cliquez sur **Clés d’accès**.
4. Dans le panneau Clés d’accès qui apparaît, la clé d’accès 1 et la clé d’accès 2 sont affichées. Vous pouvez utiliser les deux.
5. Cliquez sur l'icône de copie pour copier la clé dans le Presse-papiers.

## <a name="create-a-table"></a>Création d’une table
L’objet **Azure::TableService** permet d’utiliser des tables et des entités. Pour créer une table, utilisez la méthode **create\_table()**. L’exemple suivant crée une table ou imprime l’erreur le cas échéant.

```ruby
azure_table_service = Azure::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Ajout d'une entité à une table
Pour ajouter une entité, créez tout d'abord un objet de hachage qui définit les propriétés de votre entité. Notez que pour chaque entité, vous devez spécifier les clés **PartitionKey** et **RowKey**. Il s’agit d’identificateurs uniques de vos entités, dont les valeurs peuvent être interrogées bien plus rapidement que d’autres propriétés. Azure Storage utilise **PartitionKey** pour distribuer automatiquement les entités de la table sur plusieurs nœuds de stockage. Les entités partageant la même clé **PartitionKey** sont stockées sur le même nœud. **RowKey** est l'identifiant unique de l'entité dans sa partition.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Mise à jour d'une entité
Plusieurs méthodes permettent de mettre à jour une entité existante :

* **update\_entity() :** met à jour une entité existante en la remplaçant.
* **merge\_entity() :** met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l’entité existante.
* **insert\_or\_merge\_entity() :** met à jour une entité existante en la remplaçant. En l'absence d'entité, une nouvelle entité est insérée.
* **insert\_or\_replace\_entity() :** met à jour une entité existante en fusionnant les nouvelles valeurs des propriétés avec l’entité existante. En l'absence d'entité, une nouvelle entité est insérée.

L’exemple suivant illustre la mise à jour d’une entité avec **update\_entity()** :

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Avec **update\_entity()** et **merge\_entity()**, si l’entité que vous mettez à jour n’existe pas, l’opération échoue. Si vous voulez stocker une entité, qu’elle existe déjà ou non, utilisez plutôt **insert\_or\_replace\_entity()** ou **insert\_or\_merge\_entity()**.

## <a name="work-with-groups-of-entities"></a>Utilisation des groupes d'entités
Il est parfois intéressant de soumettre un lot d'opérations simultanément pour assurer un traitement atomique par le serveur. Pour cela, vous devez d’abord créer un objet **Lot**, puis utiliser la méthode **execute\_batch()** sur **TableService**. L'exemple ci-dessous présente l'envoi de deux entités avec RowKey 2 et 3 dans un lot. Notez qu'il ne s'applique qu'aux entités possédant le même élément PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Interrogation d’une entité
Pour interroger une entité dans une table, utilisez la méthode **get\_entity()** en transmettant le nom de table, **PartitionKey** et **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Interrogation d’un ensemble d’entités
Pour interroger un ensemble d’entités dans une table, créez un objet de hachage de requête et utilisez la méthode **query\_entities()**. L'exemple ci-dessous présente l'obtention de toutes les identités avec le même élément **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Si l’ensemble de résultats est trop grand pour être renvoyé par une seule requête, un jeton de liaison est renvoyé. Vous pouvez l’utiliser pour récupérer les pages suivantes.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Interrogation d'un sous-ensemble de propriétés d'entité
Vous pouvez utiliser une requête de table pour extraire uniquement quelques propriétés d’une entité. Cette technique, nommée « projection », réduit la consommation de bande passante et peut améliorer les performances des requêtes, notamment pour les entités volumineuses. Utilisez la clause select et transmettez le nom des propriétés à soumettre au client.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Suppression d’une entité
Pour supprimer une entité, utilisez la méthode**delete\_entity()**. Vous devez transmettre le nom de la table qui contient l’entité, ainsi que les éléments PartitionKey et RowKey de l’entité.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Suppression d’une table
Pour supprimer une table, utilisez la méthode **delete\_table()** et transmettez le nom de la table que vous souhaitez supprimer.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Étapes suivantes

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
* [Kit de développement logiciel (SDK) Azure pour Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub

