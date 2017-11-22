## <a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage

Functions utilise un compte à usage général dans stockage Azure pour conserver l’état et d’autres informations sur vos fonctions. Créez un compte de stockage à usage général dans le groupe de ressources que vous avez créé à l’aide de la commande [az storage account create](/cli/azure/storage/account#create).

Dans la commande suivante, indiquez un nom de compte de stockage global unique là où se trouve l’espace réservé `<storage_name>`. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Une fois le compte de stockage créé, Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```