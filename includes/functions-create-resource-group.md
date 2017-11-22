## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure, comme les applications de fonction, les bases de données et les comptes de stockage, sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup`.  
Si vous n’utilisez pas Cloud Shell, connectez-vous d’abord à l’aide de `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous. Pour afficher tous les emplacements pris en charge pour les plans App Service, exécutez la commande [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations).