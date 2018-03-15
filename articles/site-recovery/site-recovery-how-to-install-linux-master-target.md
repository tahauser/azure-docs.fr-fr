---
title: "Installation d’un serveur cible maître Linux pour le basculement à partir d’Azure vers un site local | Microsoft Docs"
description: "Pour reprotéger une machine virtuelle Linux, vous avez besoin d’un serveur cible maître Linux. Découvrez comment en installer."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/22/2018
ms.author: nisoneji
ms.openlocfilehash: 27b38eafd9ab5da11d66ede4d446a4952e30c534
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="install-a-linux-master-target-server"></a>Installer un serveur cible maître Linux
Après avoir basculé une machine virtuelle sur Azure, vous pouvez la restaurer automatiquement sur le site local. L’opération de restauration vous oblige à reprotéger la machine virtuelle à partir d’Azure sur le site local. Pour ce faire, vous avez besoin d’un serveur cible maître, capable de recevoir le trafic. 

Si votre machine virtuelle protégée est de type Windows, vous avez besoin d’un serveur cible maître Windows. Si vous avez une machine virtuelle Linux, vous avez besoin d’un serveur cible maître Linux. Pour savoir comment créer et installer un serveur cible maître Linux, lisez les étapes ci-dessous.

> [!IMPORTANT]
> À partir de la version 9.10.0, le serveur cible maître le plus récent ne peut être installé que sur un serveur Ubuntu 16.04. Les nouvelles installations ne sont pas autorisées sur les serveurs de CentOS6.6. Toutefois, vous pouvez continuer la mise à niveau de vos anciens serveurs cible maître à l’aide de la version 9.10.0.

## <a name="overview"></a>Vue d'ensemble
Cet article fournit la marche à suivre pour installer un serveur cible maître Linux.

Publiez vos commentaires ou vos questions en bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Prérequis

* Pour choisir l’hôte sur lequel déployer le serveur cible maître, déterminez si la restauration automatique s’effectuera sur une machine virtuelle locale existante ou sur une nouvelle machine virtuelle. 
    * Dans le cas d’une machine virtuelle existante, l’hôte du serveur cible maître doit avoir accès aux magasins de données de celle-ci.
    * Si la machine virtuelle locale n’existe pas (en cas de récupération à un autre emplacement), la machine virtuelle restaurée est créée sur le même hôte que le serveur cible maître. Vous pouvez choisir n’importe quel hôte ESXi pour installer le serveur cible maître.
* Le serveur maître cible doit appartenir à un réseau capable de communiquer avec le serveur de processus et le serveur de configuration.
* La version du serveur cible maître doit être inférieure ou égale à celle du serveur de processus et du serveur de configuration. Par exemple, si la version du serveur de configuration est 9.4, celle du serveur cible maître peut être 9.4 ou 9.3 mais pas 9.5.
* Le serveur cible maître ne peut être qu’une machine virtuelle VMware, et pas un serveur physique.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Instructions de dimensionnement pour la création du serveur cible maître

Créez le serveur cible maître selon les instructions de dimensionnement suivantes :
- **RAM** : 6 Go ou plus
- **Taille du disque de système d’exploitation** : 100 Go ou plus (pour installer CentOS6.6)
- **Taille du disque supplémentaire pour le lecteur de conservation** : 1 To
- **Cœurs d’UC** : 4 cœurs ou plus

Les noyaux Ubuntu suivants sont pris en charge.


|Série de noyau  |Prise en charge jusqu'à  |
|---------|---------|
|4.4      |4.4.0-81-generic         |
|4.8      |4.8.0-56-generic         |
|4.10     |4.10.0-24-generic        |


## <a name="deploy-the-master-target-server"></a>Déployer le serveur cible maître

### <a name="install-ubuntu-16042-minimal"></a>Installer Ubuntu version 16.04.2 Minimal

Procédez comme suit pour installer le système d’exploitation Ubuntu 16.04.2 64 bits.

1.   Accédez au [lien de téléchargement](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64) et choisissez le miroir le plus proche à partir duquel télécharger un fichier ISO Ubuntu 16.04.2 Minimal 64 bits.
Conservez le fichier ISO Ubuntu 16.04.2 Minimal 64 bits dans le lecteur DVD et démarrez le système.

1.  Sélectionnez **French** (Français) comme langue par défaut, puis appuyez sur **Entrée**.
    
    ![Sélectionner une langue](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)
1. Sélectionnez **Install Ubuntu Server** (Installer le serveur Ubuntu), puis appuyez sur **Entrée**.

    ![Sélectionner l’option d’installation du serveur Ubuntu](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

1.  Sélectionnez **French** (Français) comme langue par défaut, puis appuyez sur **Entrée**.

    ![Sélectionner French (Français) comme langue par défaut](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

1. Sélectionnez l’option appropriée dans la liste **Time Zone** (Fuseau horaire), puis appuyez sur **Entrée**.

    ![Sélectionner le fuseau horaire correct](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

1. Sélectionnez **No** (Non) (option par défaut), puis appuyez sur **Entrée**.

     ![Configurer le clavier](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)
1. Sélectionnez **French** (France) (Français (France)) comme pays d’origine pour le clavier, puis appuyez sur **Entrée**.

1. Sélectionnez **French (France)** (Français (France)) comme disposition du clavier, puis appuyez sur **Entrée**.

1. Entrez le nom d’hôte de votre serveur dans la zone **Hostname** (Nom d’hôte), puis cliquez sur **Continue** (Continuer).

1. Pour créer un compte d’utilisateur, entrez le nom d’utilisateur, puis sélectionnez **Continue** (Continuer).

      ![Création d'un compte d'utilisateur](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

1. Entrez le mot de passe du nouveau compte d’utilisateur, puis sélectionnez **Continue** (Continuer).

1.  Confirmez le mot de passe du nouvel utilisateur, puis sélectionnez **Continue** (Continuer).

    ![Confirmer le mot de passe](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

1.  Dans la sélection suivante pour le chiffrement de votre répertoire de base, sélectionnez **Non** (option par défaut), puis sélectionnez **Entrée**.

1. Si le fuseau horaire affiché est correct, sélectionnez **Yes (Oui)** (option par défaut), puis appuyez sur **Entrée**. Pour reconfigurer votre fuseau horaire, sélectionnez **No** (Non).

1. Sélectionnez l’option **Guided - Use entire disk** (Guidée - Utiliser ensemble disque) depuis les options de méthode de partitionnement, puis appuyez sur **Entrée**.

     ![Sélectionner l’option de méthode de partitionnement](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

1.  Sélectionnez le disque approprié depuis l’option **Select disk to partition** (Sélectionner disque pour partition), puis appuyez sur **Entrée**.

    ![Sélectionner le disque](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

1.  Sélectionnez **Yes** (Oui) pour écrire les modifications sur le disque, puis appuyez sur **Entrée**.

1.  Dans la sélection de proxy de configuration, sélectionnez l’option par défaut, puis sélectionnez **Continuer**, et enfin **Entrée**.

     ![Sélectionner l’option par défaut](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

1.  Sélectionnez **Aucune mise à jour automatique** dans la sélection pour la gestion des mises à niveau sur votre système, puis sélectionnez **Entrée**.

     ![Sélectionner comment gérer les mises à niveau](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

    > [!WARNING]
    > Le serveur cible maître Azure Site Recovery nécessite une version spécifique de Ubuntu. Vous devez donc veiller à ce que les mises à niveau du noyau de la machine virtuelle soient désactivées. Si elles sont activées, toute mise à niveau entraîne un dysfonctionnement du serveur cible maître. Veillez à sélectionner l’option **No automatic updates** (Aucune mise à jour automatique).

1.  Sélectionnez les options par défaut. Si vous souhaitez openSSH pour la connexion SSH, sélectionnez l’option **OpenSSH server** (Serveur OpenSSH), puis **Continue** (Continuer).

    ![Sélectionner les logiciels](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

1. Dans la sélection pour l’installation du chargeur de démarrage GRUB, sélectionnez **Oui**, puis **Entrée**.

1. Sélectionner l’appareil approprié pour l’installation du chargeur de démarrage (de préférence **/dev/sda**), puis appuyez sur **Entrée**.

1. Sélectionnez **Continue** (Continuer), puis appuyez sur **Entrée** pour terminer l’installation.

    ![Terminer l’installation](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

1. Une fois l’installation terminée, connectez-vous à la machine virtuelle avec les informations d’identification du nouvel utilisateur. (Consultez l’**étape 10** pour plus d’informations.)

1. Suivez les étapes décrites dans la capture d’écran ci-dessous pour définir le mot de passe de l’utilisateur ROOT. Ensuite, connectez-vous en tant qu’utilisateur ROOT.

    ![Définir le mot de passe de l’utilisateur ROOT](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Configurez la machine comme un serveur cible maître

Pour obtenir l’ID de chaque disque dur SCSI d’une machine virtuelle Linux, vous devez activer le paramètre **disk.EnableUUID = TRUE**. Pour ce faire, procédez comme suit :

1. Arrêtez votre machine virtuelle.

2. Cliquez avec le bouton droit de la souris sur l’entrée de la machine virtuelle dans le volet gauche, puis sélectionnez **Edit Settings** (Modifier les paramètres).

3. Sélectionnez l’onglet **Options** (Options).

4. Dans le volet gauche, sélectionnez **Advanced (Avancé)** > **General (Général)**, puis cliquez sur le bouton **Configuration Parameters** (Paramètres de configuration) dans la partie inférieure droite de l’écran.

    ![Onglets Options](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    L’option **Paramètres de configuration** n’est pas disponible lorsque la machine est arrêtée. Pour activer cet onglet, arrêtez la machine virtuelle.

5. Vérifiez si une ligne comportant **disk.EnableUUID** existe.

    - Si le paramètre est présent et a pour valeur **False**, remplacez-la par **True**. (Les valeurs ne respectent pas la casse.)

    - Si le paramètre existe et a pour valeur **True**, sélectionnez **Cancel** (Annuler).

    - Si le paramètre n’existe pas, sélectionnez **Add Row** (Ajouter une ligne).

    - Dans la colonne de nom, ajoutez **disk.EnableUUID**, puis définissez la valeur sur **TRUE**.

    ![Vérification de la présence de disk.EnableUUID](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="disable-kernel-upgrades"></a>Désactiver les mises à niveau du noyau

Le serveur cible maître Azure Site Recovery nécessite une version spécifique de Ubuntu. Vous devez donc veiller à ce que les mises à niveau du noyau de la machine virtuelle soient désactivées. Si elles sont activées, elles peuvent entraîner un dysfonctionnement du serveur cible maître.

#### <a name="download-and-install-additional-packages"></a>Télécharger et installer les packages supplémentaires

> [!NOTE]
> Vérifiez que vous avez accès à Internet pour télécharger et installer les packages supplémentaires. Si vous n’êtes pas connecté à Internet, vous devez rechercher ces packages RPM et les installer manuellement.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Obtenir le programme d’installation

Si votre serveur cible maître est connecté à Internet, vous pouvez utiliser la procédure suivante pour télécharger le programme d’installation. Sinon, vous pouvez copier le programme d’installation à partir du serveur de traitement et l’installer.

#### <a name="download-the-master-target-installation-packages"></a>Télécharger les packages d’installation du serveur maître cible

[Téléchargez les éléments d’installation du serveur maître cible Linux](https://aka.ms/latestlinuxmobsvc).

Pour le télécharger avec Linux, tapez :

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Veillez à télécharger et décompresser le programme d’installation dans le répertoire d’accueil. Si vous le décompressez dans **/usr/Local**, l’installation échoue.


#### <a name="access-the-installer-from-the-process-server"></a>Accéder au programme d’installation à partir du serveur de traitement

1. Accédez au répertoire **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository** sur le serveur de processus.

2. Copiez le fichier d’installation requis à partir du serveur de processus et enregistrez-le sous **latestlinuxmobsvc.tar.gz** dans votre répertoire d’accueil.


### <a name="apply-custom-configuration-changes"></a>Appliquer des modifications de configuration personnalisées

Pour appliquer les modifications de configuration personnalisées, procédez comme suit :


1. Exécutez la commande suivante pour décompresser le fichier binaire.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Capture d’écran de la commande à exécuter](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Exécutez la commande suivante pour accorder l’autorisation.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Exécutez la commande suivante pour exécuter le script.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Exécutez le script une seule fois sur le serveur. Puis arrêtez le serveur. Redémarrez le serveur après avoir ajouté un disque, comme indiqué dans la section ci-dessous.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Ajouter un disque de rétention à la machine virtuelle du serveur cible maître Linux

Pour créer un disque de rétention, procédez comme suit :

1. Connectez un nouveau disque de 1 To à la machine virtuelle du serveur cible maître Linux et démarrez la machine.

2. Utilisez la commande **multipath -ll** pour obtenir l’ID multichemin du disque de rétention.
    
     `multipath -ll`

        ![The multipath ID of the retention disk](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formatez le lecteur, puis créez un système de fichiers dessus.

    
    `mkfs.ext4 /dev/mapper/<Retention disk's multipath id>`
    
    ![Création d’un système de fichiers sur le lecteur](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Après avoir créé le système de fichiers, montez le disque de rétention.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```
    ![Montage du disque de rétention](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Créez l’entrée **fstab** pour monter le lecteur de rétention à chaque démarrage du système.
    
    `vi /etc/fstab`
    
    Appuyez sur **Inser** pour commencer à modifier le fichier. Créez une ligne, puis insérez-y le texte suivant. Modifiez l’ID multichemin du disque en fonction de l’ID multichemin de la commande précédente.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Appuyez sur la touche **Echap** et saisissez **:wq** (écrire et quitter) pour fermer la fenêtre de l’éditeur.

### <a name="install-the-master-target"></a>Installer le serveur cible maître

> [!IMPORTANT]
> La version du serveur cible maître doit être supérieure ou égale à celle du serveur de processus et du serveur de configuration. Si cette condition n’est pas remplie, la reprotection aboutit, mais la réplication échoue.


> [!NOTE]
> Avant d’installer le serveur cible maître, vérifiez que le fichier **/etc/hosts** sur la machine virtuelle contient des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau.

1. Copiez la phrase secrète à partir de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** sur le serveur de configuration. Ensuite, enregistrez-la dans un fichier **passphrase.txt** dans le même répertoire local en exécutant la commande suivante :

    `echo <passphrase> >passphrase.txt`

    Exemple : 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Notez l’adresse IP du serveur de configuration. Exécutez la commande suivante pour installer le serveur cible maître et l’inscrire auprès du serveur de configuration.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemple : 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Attendez la fin du script. Si le serveur cible maître est inscrit, il figure sur la page **Site Recovery Infrastructure** (Infrastructure Site Recovery) du portail.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Installer le serveur cible maître en mode interactif

1. Exécutez la commande suivante pour installer le service cible maître. Choisissez le rôle d’agent **Master Target** (Cible maître).

    ```
    ./install
    ```

2. Choisissez l’emplacement par défaut de l’installation, puis appuyez sur **Entrée** pour continuer.

    ![Choix d’un emplacement par défaut pour l’installation du serveur cible maître](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Une fois l’installation terminée, inscrivez le serveur de configuration à l’aide de la ligne de commande.

1. Notez l’adresse IP du serveur de configuration. Vous en aurez besoin à l’étape suivante.

2. Exécutez la commande suivante pour installer le serveur cible maître et l’inscrire auprès du serveur de configuration.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exemple : 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Attendez la fin du script. Si le serveur cible maître est inscrit, il figure sur la page **Site Recovery Infrastructure** (Infrastructure Site Recovery) du portail.


### <a name="install-vmware-tools-on-the-master-target-server"></a>Installer les outils VMware sur le serveur cible maître

Vous devez installer les outils VMware sur le serveur maître cible pour que ce dernier puisse détecter les magasins de données. Si les outils ne sont pas installés, l’écran de reprotection n’est pas répertorié dans les magasins de données. Vous devrez redémarrer après l’installation des outils VMware.

### <a name="upgrade-the-master-target-server"></a>Mettre le serveur cible maître à niveau

Exécutez le programme d’installation. Il détecte automatiquement que l’agent est installé sur le serveur cible maître. Sélectionnez **Y** (O) pour effectuer la mise à niveau.  Une fois la configuration terminée, vérifiez la version du serveur cible maître installée à l’aide de la commande suivante :

`cat /usr/local/.vx_version`


Vous pouvez constater que le champ **Version** indique le numéro de version du serveur cible maître.

## <a name="common-issues"></a>Problèmes courants

* Veillez à ne pas activer Storage vMotion sur des composants de gestion tels qu’un serveur cible maître. Si le serveur cible maître est déplacé après une reprotection, les disques de machine virtuelle (VMDK) ne peuvent pas être détachés. Dans ce cas, la restauration automatique échoue.

* Le serveur cible maître ne doit pas présenter d’instantanés sur la machine virtuelle. Si des instantanés sont présents, la restauration automatique échoue.

* En raison de configurations de carte réseau personnalisées, l’interface réseau est désactivée au démarrage et l’agent du serveur cible maître ne s’initialise pas. Vérifiez que les propriétés suivantes sont configurées correctement. Vérifiez ces propriétés dans le fichier /etc/sysconfig/network-scripts/ifcfg-eth de la carte Ethernet.
    * BOOTPROTO=dhcp
    * ONBOOT=yes


## <a name="next-steps"></a>Étapes suivantes
Une fois installé et inscrit, le serveur cible maître apparaît dans la section **Master Target** (Cible maître) de la page **Site Recovery Infrastructure** (Infrastructure Site Recovery), sous la vue d’ensemble du serveur de configuration.

Vous pouvez maintenant procéder à la [reprotection](site-recovery-how-to-reprotect.md), puis à la restauration automatique.

