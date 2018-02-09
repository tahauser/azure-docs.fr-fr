## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

Si vous ne savez pas quelle localisation utiliser, affichez la liste des localisations disponibles. Dans la liste, trouvez celle que vous souhaitez utiliser. Cet exemple utilise **eastus**. Stockez-la dans une variable et utilisez cette variable. Vous pourrez ainsi changer son contenu dans un seul emplacement.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage standard à usage général avec la réplication LRS à l’aide de [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), puis récupérez le contexte du compte de stockage qui définit le compte de stockage à utiliser. Si un compte de stockage est utilisé, référencez le contexte au lieu d’entrer les informations d’identification à plusieurs reprises. Cet exemple crée un compte de stockage nommé *mystorageaccount* avec les options de stockage localement redondant (LRS) et de chiffrement des objets blob (activées par défaut).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```
