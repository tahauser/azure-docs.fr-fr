<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Gestion des entités de table

Maintenant que vous avez une table, intéressons-nous à la manière de gérer les entités, ou lignes, dans cette table. 

Une entité peut comprendre jusqu’à 255 propriétés, notamment 3 propriétés système : **PartitionKey**, **RowKey** et **Timestamp**. Vous êtes chargé d’insérer et de mettre à jour les valeurs de **PartitionKey** et **RowKey**. Le serveur gère la valeur de **Timestamp**, qui ne peut pas être modifiée. Les propriétés **PartitionKey** et **RowKey** identifient de manière unique chaque entité d’une table.

* **PartitionKey**: détermine la partition dans laquelle l’entité est stockée
* **RowKey**: identifie de façon unique l’entité dans la partition

Vous pouvez définir au maximum 252 propriétés personnalisées par entité. 

### <a name="add-table-entities"></a>Ajouter des entités de table

Ajouter des entités à une table à l’aide de **Add-StorageTableRow**. Ces exemples utilisent des clés de partition avec des valeurs « partition1 » et « partition2 », ainsi que des clés de ligne équivalant à des abréviations d’état. Les propriétés de chaque entité sont le nom d’utilisateur et l’ID d’utilisateur. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Interroger les entités de table

Il existe plusieurs façons différentes d’interroger les entités dans une table.

#### <a name="retrieve-all-entities"></a>Récupérer toutes les entités

Pour récupérer toutes les entités, utilisez **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Cette commande génère des résultats similaires à la table suivante :

| userId | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Récupérer les entités d’une partition spécifique

Pour récupérer toutes les entités d’une partition spécifique, utilisez **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Les résultats ressemblent à la table suivante :

| userId | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Récupérer les entités d’une valeur spécifique dans une colonne spécifique

Pour récupérer les entités pour lesquelles la valeur comprise dans une colonne spécifique est égale à une valeur particulière, utilisez **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Cette requête récupère un seul enregistrement.

|field|value|
|----|----|
| userId | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Récupérer des entités à l’aide d’un filtre personnalisé 

Pour récupérer des entités à l’aide d’un filtre personnalisé, utilisez **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

Cette requête récupère un seul enregistrement.

|field|value|
|----|----|
| userId | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Mise à jour des entités 

Trois étapes sont nécessaires pour mettre à jour des entités. La première consiste à récupérer l’entité à modifier. La deuxième à apporter la modification. La troisième à valider la modification à l’aide de la commande **Update-AzureStorageTableRow**.

Mettez à jour l’entité dont le nom d’utilisateur est « Jessie » pour que le nom d’utilisateur devienne « Jessie2 ». Cet exemple montre également une autre façon de créer un filtre personnalisé à l’aide de types .NET. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

Les résultats montrent l’enregistrement Jessie2.

|field|value|
|----|----|
| userId | 2 |
| username | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Suppression d’entités de table

Vous pouvez supprimer une entité ou toutes les entités dans une table.

#### <a name="deleting-one-entity"></a>Suppression d’une seule entité

Pour supprimer une seule entité, obtenez une référence à cette entité et redirigez-la vers **Remove-AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Supprimer toutes les entités d’une table 

Pour supprimer toutes les entités d’une table, récupérez-les, puis redirigez les résultats vers l’applet de commande remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```