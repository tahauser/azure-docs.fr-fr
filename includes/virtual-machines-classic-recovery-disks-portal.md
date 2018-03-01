Si votre machine virtuelle dans Azure rencontre une erreur de démarrage ou de disque, il vous faudra éventuellement appliquer la procédure de dépannage directement sur le disque dur virtuel. Comme exemple courant, citons l’échec de mise à jour d’une application qui empêche le bon démarrage de la machine virtuelle. Cet article vous explique comment utiliser le portail Azure pour connecter votre disque dur virtuel à une autre machine virtuelle pour corriger les éventuelles erreurs, puis recréer votre machine virtuelle d’origine.


## <a name="recovery-process-overview"></a>Vue d’ensemble du processus de récupération
Le processus de résolution de problème se présente comme suit :

1. Supprimez les machines virtuelles présentant des erreurs, mais conservez les disques durs virtuels.
2. Fixez et montez le disque dur virtuel sur une autre machine virtuelle à des fins de résolution des problèmes.
3. Connectez-vous à la machine virtuelle de dépannage. Modifiez les fichiers ou exécutez des outils afin de corriger les erreurs sur le disque dur virtuel d’origine.
4. Démontez le disque dur virtuel d’origine et dissociez-le de la machine virtuelle de dépannage.
5. Créez une machine virtuelle à l’aide du disque dur virtuel d’origine.

## <a name="delete-the-original-vm"></a>Supprimez la machine virtuelle d’origine
Les disques durs virtuels et les machines virtuelles sont deux ressources distinctes dans Azure. Le disque dur virtuel est l’emplacement de stockage du système d’exploitation, des applications et des configurations. La machine virtuelle correspond à des métadonnées définissant la taille ou l’emplacement ; elle référence des ressources comme un disque dur virtuel ou une carte d’interface réseau virtuelle (NIC). Chaque disque dur virtuel associé à une machine virtuelle se voit attribuer un bail. Bien que l’association et la dissociation des disques de données puisse s’effectuer pendant l’exécution de la machine virtuelle, le disque du système d’exploitation ne peut pas être dissocié, sauf si la ressource de machine virtuelle est supprimée. Le bail continue à associer le disque du système d’exploitation à une machine virtuelle, même lorsque cette machine virtuelle se trouve dans un état d’arrêt ou de libération.

La première étape de la récupération de votre machine virtuelle consiste à supprimer la ressource de machine virtuelle. En supprimant la machine virtuelle, vous ne vous séparez pas des disques durs virtuels de votre compte de stockage. Une fois la machine virtuelle supprimée, vous pouvez associer le disque dur virtuel à une autre machine virtuelle afin de réparer et de corriger les erreurs. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Dans le menu de gauche, cliquez sur **Machines virtuelles (classiques)**.
3. Sélectionnez la machine virtuelle défaillante, cliquez sur **Disques**, puis identifiez le nom du disque dur virtuel. 
4. Sélectionnez le disque dur virtuel du système d’exploitation et vérifiez son **Emplacement** pour identifier le compte de stockage dans lequel il se trouve. Dans l’exemple suivant, la chaîne précédant « .blob.core.windows.net » correspond au nom du compte de stockage.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![L’image de l’emplacement de la machine virtuelle](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Faites un clic droit sur la machine virtuelle, puis sélectionnez **Supprimer**. Veillez à ce que les disques ne soient pas sélectionnés lors de la suppression de la machine virtuelle.
6. Créez une nouvelle machine virtuelle de récupération. Cette machine virtuelle doit se trouver dans la même région et le même groupe de ressources (Service Cloud) que la machine virtuelle défaillante.
7. Sélectionnez la machine virtuelle de récupération, puis **Disques** > **Attacher un disque existant**.
8. Pour sélectionner votre disque dur virtuel existant, cliquez sur **Fichier VHD** :

    ![Rechercher le disque dur virtuel existant](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Sélectionnez le compte de stockage > Conteneur du disque dur virtuel > le disque dur virtuel, et confirmez votre choix en cliquant sur **Sélectionner**.

    ![Sélectionner votre disque dur virtuel existant](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. Une fois que votre disque dur virtuel est sélectionné, sélectionnez **OK** pour attacher le disque dur virtuel existant.
11. Après quelques secondes, le volet **Disques** de votre machine virtuelle affiche votre disque dur virtuel existant connecté en tant que disque de données :

    ![Disque dur virtuel existant attaché en tant que disque de données](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Résoudre des problèmes sur le disque dur virtuel d’origine
Une fois le disque dur virtuel existant monté, vous pouvez exécuter les procédures de maintenance et de dépannage requises. Une fois que vous avez résolu les problèmes, passez aux étapes suivantes.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Démonter et dissocier le disque dur virtuel d’origine
Une fois les erreurs résolues, démontez et détachez le disque dur virtuel existant de votre machine virtuelle de dépannage. Vous ne pouvez pas utiliser votre disque dur virtuel avec une autre machine virtuelle avant la libération du bail associant le disque dur virtuel à la machine virtuelle de dépannage.  

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 
2. Dans le menu de gauche, sélectionnez **Machines virtuelles (classiques)**.
3. Recherchez la machine virtuelle de récupération. Sélectionnez Disques, faites un clic droit sur le disque, puis sélectionnez **Détacher**.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Créer une machine virtuelle à partir du disque dur d’origine

Pour créer une machine virtuelle à partir de votre disque dur virtuel d’origine, utilisez le [portail Azure](https://portal.azure.com).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le coin supérieur gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle** > **À partir de la galerie**.
3. Dans la section **Choisir une image**, sélectionnez **Mes disques**, puis le disque dur virtuel d’origine. Vérifiez les informations d’emplacement. Il s’agit de la région dans laquelle vous devez déployer la machine virtuelle. Cliquez sur Suivant.
4. Dans la section **Configuration de la machine virtuelle**, entrez le nom de la machine virtuelle et sélectionnez une taille.
