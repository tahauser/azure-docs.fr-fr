Lorsque vous n’avez plus besoin d’un disque de données qui est attaché à une machine virtuelle, vous pouvez le détacher facilement. Cette opération ne supprime pas le disque du stockage. Si vous souhaitez réutiliser les données du disque, vous pouvez l’attacher à la même machine virtuelle ou à une autre.  

> [!NOTE]
> Dans Azure, une machine virtuelle utilise différents types de disque, comme le disque du système d’exploitation, un disque temporaire local et des disques de données facultatifs. Pour plus d’informations, consultez l’article [À propos des disques et VHD pour machines virtuelles](../articles/virtual-machines/linux/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Vous ne pouvez pas détacher un disque de système d’exploitation si vous ne supprimez pas également la machine virtuelle.

## <a name="find-the-disk"></a>Recherche du disque
Avant de pouvoir détacher un disque d'une machine virtuelle, vous devez rechercher le nombre LUN (numéro d’unité logique), qui est un identificateur pour le disque à détacher. Pour ce faire, procédez comme suit :

1. Ouvrez l’interface de ligne de commande (CLI) Azure, puis [connectez-vous à votre abonnement Azure](/cli/azure/authenticate-azure-cli). Vérifiez que vous êtes en mode Gestion des services Azure (`azure config mode asm`).
2. Recherchez les disques attachés à votre machine virtuelle. L’exemple ci-après répertorie les disques de la machine virtuelle nommée `myVM` :

    ```azurecli
    azure vm disk list myVM
    ```

    Le résultat ressemble à l’exemple suivant :

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Notez le LUN ou le **numéro d'unité logique** pour le disque que vous souhaitez détacher.

## <a name="remove-operating-system-references-to-the-disk"></a>Supprimer les références de système d’exploitation sur le disque
Avant de détacher le disque de l’invité Linux, assurez-vous que toutes les partitions sur le disque ne sont pas en cours d’utilisation. Assurez-vous que le système d’exploitation n’essaie pas de les remonter après un redémarrage. Ces étapes annulent la configuration que vous avez probablement créée lors de [l’attachement](../articles/virtual-machines/linux/classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) du disque.

1. Utilisez la commande `lsscsi` pour détecter l’iddentifiateur de disque. `lsscsi` peut être installé par `yum install lsscsi` (dans des distributions Red Hat) ou `apt-get install lsscsi` (dans des distributions Debian). Vous pouvez trouver l’identificateur de disque que vous recherchez à l’aide du numéro de LUN. Le dernier numéro du tuple dans chaque ligne est le LUN. Dans l’exemple ci-après pour la commande `lsscsi`, LUN 0 correspond à */dev/sdc*

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Utilisez `fdisk -l <disk>` pour détecter les partitions associées au disque à détacher. L’exemple ci-après illustre la sortie obtenue pour `/dev/sdc` :

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Démontez chaque partition répertoriée pour le disque. L’exemple ci-après démonte `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

4. Utilisez la commande `blkid` pour détecter les UUID de toutes les partitions. Le résultat ressemble à l’exemple suivant :

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. Supprimez les entrées dans le fichier **/etc/fstab** associé aux chemins d’accès de l’appareil ou aux UUID pour toutes les partitions du disque à détacher.  Pour cet exemple, les entrées peuvent être les suivantes :

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    or
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>Détachement du disque
Une fois le numéro de LUN du disque identifié et les références de système d’exploitation supprimées, vous pouvez détacher le disque :

1. Détachez le disque sélectionné de la machine virtuelle en exécutant la commande `azure vm disk detach
   <virtual-machine-name> <LUN>`. L’exemple ci-après détache LUN `0` de la machine virtuelle nommée `myVM` :
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. Vous pouvez vérifier si le disque est détaché en réexécutant la commande `azure vm disk list`. L’exemple ci-après vérifie la machine virtuelle nommée `myVM` :
   
    ```azurecli
    azure vm disk list myVM
    ```

    La sortie obtenue est comparable à l’exemple ci-dessous, qui indique que le disque de données n’est plus attaché :

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

Le disque détaché reste dans le stockage, mais il n'est plus attaché à une machine virtuelle.

