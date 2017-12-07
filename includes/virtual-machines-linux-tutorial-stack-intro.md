## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). 

L’exemple suivant crée une machine virtuelle nommée *myVM* et des clés SSH si elles n’existent pas déjà dans un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`. La commande définit aussi *azureuser* comme nom d’utilisateur administrateur. Vous utilisez ce nom pour vous connecter à la machine virtuelle. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Lorsque la machine virtuelle a été créée, l’interface de ligne de commande Azure affiche des informations similaires à l’exemple suivant. Notez la valeur de `publicIpAddress`. Cette adresse est utilisée pour accéder à la machine virtuelle dans des étapes ultérieures.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web 

Par défaut, seules les connexions SSH sont autorisées dans les machines virtuelles Linux déployées dans Azure. Étant donné que cette machine virtuelle sera un serveur web, vous devez ouvrir le port 80 à partir d’Internet. Utilisez la commande [az vm open-port](/cli/azure/vm#az_vm_open_port) pour ouvrir le port souhaité.  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>Se connecter avec SSH à votre machine virtuelle


Pour trouver l’adresse IP publique de votre machine virtuelle, exécutez la commande [az network public-ip list](/cli/azure/network/public-ip#list). Vous aurez besoin de cette adresse au cours de plusieurs étapes ultérieures.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle. Remplacez l’adresse IP publique correcte de votre machine virtuelle. Dans cet exemple, l’adresse IP est *40.68.254.142*. *azureuser* est le nom d’utilisateur administrateur défini lors de la création de la machine virtuelle.

```bash
ssh azureuser@40.68.254.142
```

