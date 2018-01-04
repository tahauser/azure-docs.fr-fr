Créez un groupe de ressources avec la commande [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Pour visualiser les emplacements disponibles, exécutez la commande `az appservice list-locations`. Vous créez généralement des ressources dans une région proche de chez vous.
