


Un groupe à haute disponibilité maintient la disponibilité de vos machines virtuelles pendant une interruption (par exemple, en cas de maintenance). Le placement de deux machines virtuelles ou plus dans un groupe à haute disponibilité crée les conditions de redondance indispensables au maintien de la disponibilité des applications ou des services exécutés par votre machine virtuelle. Pour plus d’informations sur cette procédure, voir la rubrique [Gestion de la disponibilité des machines virtuelles][Manage the availability of virtual machines].

Il est recommandé d’utiliser des groupes à haute disponibilité et des points de terminaison à équilibrage de charge pour garantir que votre application soit toujours disponible et qu’elle s’exécute correctement. Pour plus d’informations sur les points de terminaison à équilibrage de la charge, consultez la page [Équilibrage de charge pour les services d’infrastructure Azure][Load balancing for Azure infrastructure services].

Vous pouvez ajouter des machines virtuelles classiques dans un groupe à haute disponibilité en utilisant l’une des deux options suivantes :

* [Option 1 : Créer simultanément une machine virtuelle et un groupe à haute disponibilité][Option 1: Create a virtual machine and an availability set at the same time]. Ensuite, ajouter de nouvelles machines virtuelles à l’ensemble lorsque vous créez ces ordinateurs virtuels.
* [Option 2 : Ajouter une machine virtuelle existante à un groupe à haute disponibilité][Option 2: Add an existing virtual machine to an availability set].

> [!NOTE]
> Dans le modèle classique, les machines virtuelles que vous voulez placer dans le même groupe à haute disponibilité doivent appartenir au même service cloud.
> 
> 

## <a id="createset"></a>Option 1 : Créer simultanément une machine virtuelle et un groupe à haute disponibilité.
Pour cela, vous pouvez utiliser le portail Azure ou des commandes Azure PowerShell.

Pour utiliser le portail Azure :

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Créer une ressource** > **Calcul**.
3. Sélectionner l’image de machine virtuelle Marketplace que vous voulez utiliser. Vous pouvez choisir de créer une machine virtuelle Linux ou Windows.
4. Pour la machine virtuelle sélectionnée, vérifiez que le modèle de déploiement est défini sur **Classique**, puis cliquez sur **Créer**.
   
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. Entrez le nom de la machine virtuelle, le nom d’utilisateur et le mot de passe (pour les machines Windows) ou la clé publique SSH (pour les machines Linux). 
6. Choisissez une taille de machine virtuelle, puis cliquez sur **Sélectionner** pour continuer.
7. Choisissez **Configuration facultative > Groupe à haute disponibilité**, puis sélectionnez le groupe à haute disponibilité auquel vous voulez ajouter la machine virtuelle.
   
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. Passez en revue vos paramètres de configuration. Une fois ces opérations effectuées, cliquez sur **Créer**.
9. Pendant qu’Azure crée votre machine virtuelle, vous pouvez suivre la progression de cette opération dans le menu Hub, sous **Machines virtuelles** .

Pour plus d’informations sur l’utilisation des commandes Azure PowerShell pour créer une machine virtuelle Azure et l’ajouter à un groupe à haute disponibilité nouveau ou existant, consultez l’article [Utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles Windows](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a id="addmachine"></a>Option 2 : Ajouter une machine virtuelle existante à un groupe à haute disponibilité
Dans le portail Azure, vous pouvez ajouter des machines virtuelles classiques existantes à un groupe à haute disponibilité existant, ou en créer un pour ces machines. (N’oubliez pas que les machines virtuelles dans un même groupe à haute disponibilité doivent appartenir au même service cloud). Les opérations à effectuer sont pratiquement identiques. Avec Azure PowerShell, vous pouvez ajouter la machine virtuelle à un groupe à haute disponibilité existant.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Machines virtuelles (classiques)**.
   
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. Dans la liste des machines virtuelles, sélectionnez le nom de la machine virtuelle que vous voulez ajouter au groupe.
4. Choisissez **Groupe à haute disponibilité** dans les **Paramètres** de machine virtuelle.
   
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. Sélectionner le groupe à haute disponibilité auquel vous voulez ajouter la machine virtuelle. La machine virtuelle doit appartenir au même service cloud que le groupe à haute disponibilité.
   
    ![Texte de remplacement d’image](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. Cliquez sur **Enregistrer**.

Pour utiliser les commandes Azure PowerShell, ouvrez une session Azure PowerShell de niveau administrateur et exécutez la commande suivante. Pour les espaces réservés (tels que &lt;VmCloudServiceName&gt;), remplacez tout ce qui se trouve entre guillemets, y compris les caractères < et >, par les noms adéquats.

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> Il se peut que la machine virtuelle doive être redémarrée pour terminer son ajout au groupe à haute disponibilité.
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

