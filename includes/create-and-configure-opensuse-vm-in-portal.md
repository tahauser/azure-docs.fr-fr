1. Connectez-vous au [portail Azure](http://portal.azure.com).  
2. Cliquez sur **Nouveau** > **Compute** > **Afficher tout**, puis tapez **SUSE**.
   
3. Sélectionnez une image de machine virtuelle OpenSUSE, puis cliquez sur la flèche pour continuer.
5. Sur la première page **Configuration de la machine virtuelle** :
   
   * Entrez le **Nom de la machine virtuelle**, par exemple, « testlinuxvm ». Le nom doit contenir entre 3 et 15 caractères, peut contenir uniquement des lettres, des chiffres et des traits d’union, et doit commencer par une lettre et se terminer par une lettre ou un chiffre.
   * Vérifiez le **Niveau** et choisissez une **Taille**. Le niveau détermine les tailles que vous pouvez choisir. La taille d'une machine virtuelle affecte son coût d'utilisation, ainsi que les options de configuration comme le nombre de disques de données que vous pouvez joindre. Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   * Entrez un **nouveau nom d’utilisateur** ou acceptez la valeur par défaut, **azureuser**. Ce nom est ajouté au fichier de liste Sudoers.
   * Déterminez le type d’ **Authentification** à utiliser. Pour obtenir des instructions générales sur les mots de passe, consultez la rubrique [Mots de passe forts](http://msdn.microsoft.com/library/ms161962.aspx).
6. Sur la page **Configuration de la machine virtuelle** suivante :
   
   * Utilisez le paramètre par défaut **Créer un nouveau service de cloud computing**.
   * Dans le champ **Nom DNS** , tapez un nom DNS unique à utiliser dans l’adresse, comme « testlinuxvm ».
   * Dans la zone **Région/Groupe d'affinités/Réseau virtuel** , sélectionnez une région d’hébergement pour cette image virtuelle.
   * Sous **Points de terminaison**, conservez le point de terminaison SSH. Vous pouvez en ajouter d'autres maintenant ou les ajouter, les modifier ou les supprimer après avoir créé la machine virtuelle.
     
     > [!NOTE]
     > Si vous voulez qu’une machine virtuelle utilise un réseau virtuel, lorsque vous la créez, vous **devez** indiquer le réseau virtuel. Vous ne pouvez pas ajouter la machine virtuelle à un réseau virtuel après l'avoir créée. Pour en savoir plus, consultez [Vue d’ensemble d’Azure Virtual Network](../articles/virtual-network/virtual-networks-overview.md).
     > 
     > 
7. Sur la dernière page **Configuration de la machine virtuelle** , conservez les paramètres par défaut et cliquez sur la coche pour terminer.

Le portail répertorie la nouvelle machine virtuelle sous **Machines virtuelles**. Tant que l’état indiqué est **(Approvisionnement)**, la machine virtuelle est en cours de configuration. Lorsque l’état indiqué est **En cours d’exécution**, vous pouvez passer à l’étape suivante.

## <a name="connect-to-the-virtual-machine"></a>Connexion à la machine virtuelle
Vous allez utiliser SSH ou PuTTY pour vous connecter à la machine virtuelle, selon le système d’exploitation que vous exécutez sur votre ordinateur :

* À partir d'un ordinateur exécutant Linux, utilisez SSH. À l’invite de commandes, tapez :
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    Tapez le mot de passe de l’utilisateur.
* À partir d'un ordinateur exécutant Windows, utilisez PuTTY. S’il n’est pas installé, téléchargez-le à partir de la [page de téléchargement de PuTTY][PuTTYDownload].
  
    Enregistrez **putty.exe** dans un répertoire de votre ordinateur. Ouvrez une invite de commande, accédez à ce dossier, puis exécutez **putty.exe**.
  
    Tapez le nom d’hôte, comme « testlinuxvm.cloudapp.net », puis tapez « 22 » dans le champ **Port**.
  
    ![Écran PuTTY][Image6]  

## <a name="update-the-virtual-machine-optional"></a>Mise à jour de la machine virtuelle (facultatif)
1. Une fois connecté à la machine virtuelle, vous pouvez éventuellement installer des mises à jour système et des correctifs. Pour exécuter la mise à jour, tapez :
   
    `$ sudo zypper update`
2. Sélectionnez **Logiciel** puis **Mise à jour en ligne** pour afficher les mises à jour disponibles. Sélectionnez **Accepter** pour démarrer l’installation et appliquer tous les nouveaux correctifs disponibles (sauf ceux qui sont facultatifs).
3. Une fois l’installation terminée, sélectionnez **Terminer**.  Votre système est maintenant à jour.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
