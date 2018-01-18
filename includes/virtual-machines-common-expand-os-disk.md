## <a name="overview"></a>Vue d'ensemble
Lorsque vous créez une machine virtuelle (VM) dans un groupe de ressources en déployant une image à partir d’[Azure Marketplace](https://azure.microsoft.com/marketplace/), le lecteur du système d’exploitation par défaut est de 127 Go (par défaut, certaines images peuvent avoir de plus petits disques de système d’exploitation). Même s’il est possible d’ajouter des disques de données à la machine virtuelle (le nombre dépend de la référence (SKU) choisie) et de plus, il est recommandé d’installer les applications et les charges de travail intensives du processeur sur ces disques supplémentaires, il peut arriver que les clients doivent développer le lecteur du système d’exploitation pour prendre en charge certains scénarios, tels que les suivants :

1. Prendre en charge les applications héritées qui installent des composants sur le lecteur du système d’exploitation.
2. Migrer un ordinateur physique ou une machine virtuelle depuis un emplacement local avec un lecteur de système d’exploitation plus volumineux.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : Resource Manager et classique. Cet article traite de l’utilisation du modèle Resource Manager. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.
> 
> 

## <a name="resize-the-os-drive"></a>Redimensionner le lecteur du système d’exploitation
Dans cet article, nous allons exécuter la tâche consistant à redimensionner le lecteur du système d’exploitation à l’aide de modules Resource Manager d’ [Azure Powershell](/powershell/azureps-cmdlets-docs). Nous allons montrer le redimensionnement du lecteur du système d’exploitation à la fois pour les disques non gérés et gérés, car l’approche diffère entre les deux types de disques.

### <a name="for-resizing-unmanaged-disks"></a>Pour le redimensionnement de disques non gérés :

Ouvrez votre Powershell ISE ou une fenêtre Powershell en mode administrateur et suivez les étapes ci-dessous :

1. Connectez-vous à votre compte Microsoft Azure en mode de gestion des ressources et sélectionnez votre abonnement comme suit :
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Définissez le nom du groupe de ressources et le nom de la machine virtuelle comme suit :
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obtenez une référence à votre machine virtuelle comme suit :
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Arrêtez la machine virtuelle avant de redimensionner le disque comme suit :
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Et voici le moment que nous attendions ! Définissez la taille du disque du système d’exploitation non géré sur la valeur souhaitée et mettez à jour la machine virtuelle comme suit :
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > La nouvelle taille doit être supérieure à la taille du disque actuelle. Le maximum autorisé est de 2 048 Go pour les disques du système d’exploitation. (Il est possible d’étendre la taille de l’objet blob du disque virtuel, mais le système d’exploitation ne peut utiliser que l’espace des premiers 2 048 Go.)
   > 
   > 
6. La mise à jour de la machine virtuelle peut prendre quelques secondes. Une fois que l’exécution de la commande est terminée, redémarrez la machine virtuelle comme suit :
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>Pour le redimensionnement de disques gérés :

Ouvrez votre Powershell ISE ou une fenêtre Powershell en mode administrateur et suivez les étapes ci-dessous :

1. Connectez-vous à votre compte Microsoft Azure en mode de gestion des ressources et sélectionnez votre abonnement comme suit :
   
   ```Powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Définissez le nom du groupe de ressources et le nom de la machine virtuelle comme suit :
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Obtenez une référence à votre machine virtuelle comme suit :
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Arrêtez la machine virtuelle avant de redimensionner le disque comme suit :
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Obtenez une référence vers le disque de système d’exploitation géré. Définissez la taille du disque du système d’exploitation géré sur la valeur souhaitée et mettez à jour le disque comme suit :
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > La nouvelle taille doit être supérieure à la taille du disque actuelle. Le maximum autorisé est de 2 048 Go pour les disques du système d’exploitation. (Il est possible d’étendre la taille de l’objet blob du disque virtuel, mais le système d’exploitation ne peut utiliser que l’espace des premiers 2 048 Go.)
   > 
   > 
6. La mise à jour de la machine virtuelle peut prendre quelques secondes. Une fois que l’exécution de la commande est terminée, redémarrez la machine virtuelle comme suit :
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

Vous avez terminé. Connectez-vous via RDP à la machine virtuelle, ouvrez Gestion de l’ordinateur (ou Gestion des disques) et développez le lecteur à l’aide de l’espace qui vient d’être alloué.

## <a name="summary"></a>Résumé
Dans cet article, nous avons utilisé les modules Azure Resource Manager de PowerShell pour développer le lecteur du système d’exploitation d’une machine virtuelle IaaS. Voici le script complet de référence pour les disques non gérés et gérés :

Disques non gérés :

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
Disques gérés :

```Powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>Étapes suivantes
Bien que dans cet article, nous avons choisi de nous concentrer principalement sur l’expansion du disque du système d’exploitation géré/non géré de la machine virtuelle, le script développé peut également servir à développer les disques de données associés à la machine virtuelle. Par exemple, pour développer le premier disque de données associé à la machine virtuelle, remplacez l’objet ```OSDisk``` de ```StorageProfile``` par le tableau ```DataDisks``` et utilisez un index numérique pour obtenir une référence au premier disque de données associé, comme indiqué ci-dessous :

Disque non géré :
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Disque géré :
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

De même, vous pouvez référencer d’autres disques de données associés à la machine virtuelle, à l’aide d’un index comme indiqué ci-dessus ou à l’aide de la propriété ```Name``` du disque comme illustré ci-dessous :

Disque non géré :
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Disque géré :
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Si vous souhaitez savoir comment associer des disques à une machine virtuelle Azure Resource Manager, consultez cet [article](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

