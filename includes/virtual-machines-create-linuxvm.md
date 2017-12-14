
1. Connectez-vous à votre abonnement Azure en suivant les étapes décrites dans [Connectez-vous à Azure à partir de l’interface de ligne de commande (CLI) Azure 1.0](/cli/azure/authenticate-azure-cli).

2. Vérifiez que vous êtes dans le mode de déploiement Classic comme suit :

    ```azurecli
    azure config mode asm
    ```

3. Parmi les images disponibles, trouvez l’image Linux que vous souhaitez charger :

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    Dans une fenêtre d’invite de commandes Windows, utilisez **find** au lieu de grep.
   
4. Utilisez `azure vm create` pour créer une machine virtuelle avec l’image Linux de la liste précédente. Cette étape crée un service cloud et un compte de stockage. Vous pouvez également connecter cette machine virtuelle à un service cloud avec une option `-c` . Créez un point de terminaison SSH pour vous connecter à la machine virtuelle Linux avec l’option `-e` . L’exemple suivant crée une machine virtuelle nommée `myVM` à l’aide de l’image `Ubuntu-14_04_4-LTS` dans l’emplacement `West US` et ajoute un nom d’utilisateur `ops` :
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    Le résultat ressemble à l’exemple suivant :

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Pour une machine virtuelle Linux, vous devez fournir l’option `-e` dans `vm create`. Il n’est pas possible d’activer SSH une fois que la machine virtuelle a été créée. Pour plus d’informations sur SSH, consultez la rubrique [Utilisation de SSH avec Linux dans Azure](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. Vous pouvez vérifier les attributs de la machine virtuelle à l’aide de la commande `azure vm show`. L’exemple suivant affiche les informations concernant la machine virtuelle nommée `myVM` :

    ```azurecli   
    azure vm show myVM
    ```

6. Démarrez votre machine Virtuelle avec la commande `azure vm start` comme suit :

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur toutes les commandes des machines virtuelles de l’interface Azure CLI 1.0, voir [Utilisation de l’interface de ligne de commande (CLI) Azure 1.0 avec l’API de déploiement Classic](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

