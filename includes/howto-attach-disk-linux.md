
Pour plus d’informations sur les disques, consultez l’article [À propos des disques et VHD pour machines virtuelles](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Attacher un disque vide
1. Ouvrez l’interface Azure CLI 1.0, puis [connectez-vous à votre abonnement Azure](/cli/azure/authenticate-azure-cli). Vérifiez que vous êtes en mode Gestion des services Azure (`azure config mode asm`).
2. Entrez `azure vm disk attach-new` pour créer et attacher un disque comme indiqué dans l’exemple ci-dessous. Remplacez *myVM* par le nom de votre machine virtuelle Linux et spécifiez la taille du disque en Go (*100 Go* dans cet exemple) :

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Une fois créé et attaché, le disque de données s’affiche dans la sortie de `azure vm disk list <virtual-machine-name>`, comme indiqué dans l’exemple ci-dessous :
   
    ```azurecli
    azure vm disk list TestVM
    ```

    Le résultat ressemble à l’exemple suivant :

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Association d'un disque existant
Pour attacher un disque existant, vous devez disposer d’un fichier .vhd dans un compte de stockage.

1. Ouvrez l’interface Azure CLI 1.0, puis [connectez-vous à votre abonnement Azure](/cli/azure/authenticate-azure-cli). Vérifiez que vous êtes en mode Gestion des services Azure (`azure config mode asm`).
2. Vérifiez si le disque dur virtuel que vous souhaitez attacher est déjà chargé dans votre abonnement Azure :
   
    ```azurecli
    azure vm disk list
    ```

    Le résultat ressemble à l’exemple suivant :

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Si le disque que vous souhaitez utiliser est introuvable, vous pouvez charger un disque dur virtuel local dans votre abonnement en utilisant `azure vm disk create` ou `azure vm disk upload`. Un exemple d’utilisation de `disk create` serait semblable à ce qui suit :
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    Le résultat ressemble à l’exemple suivant :

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   Vous pouvez également utiliser la commande `azure vm disk upload` pour charger un disque dur virtuel dans un compte de stockage. Vous trouverez plus d’informations sur les commandes de gestion de vos disques de données de machine virtuelle Azure [ici](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

4. Maintenant, vous attachez le disque dur virtuel souhaité à votre machine virtuelle :
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Veillez à remplacer *myVM* par le nom de votre machine virtuelle, et *myVHD* par le disque dur virtuel de votre choix.

5. Vous pouvez vérifier que le disque est attaché à la machine virtuelle avec la commande `azure vm disk list <virtual-machine-name>`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    Le résultat ressemble à l’exemple suivant :

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> Après avoir ajouté un disque de données, vous devez vous connecter à la machine virtuelle et initialiser le disque. La machine virtuelle pourra alors utiliser le disque pour le stockage (pour plus d’informations sur l’initialisation du disque, consultez la procédure ci-après).
> 
> 

