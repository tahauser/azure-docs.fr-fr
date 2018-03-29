---
title: 'Version préliminaire des services de domaine Azure Active Directory : associer une machine virtuelle Ubuntu à un domaine géré | Microsoft Docs'
description: Joindre une machine virtuelle Linux Ubuntu aux services de domaine Azure AD
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: maheshu
ms.openlocfilehash: d968548f9ac369f02f10a10d8f1ecc99c48bca60
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Joindre une machine virtuelle Ubuntu dans Azure à un domaine géré
Cet article indique comment joindre une machine virtuelle Linux Ubuntu à un domaine géré par les services de domaine Azure AD.


## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter les tâches indiquées dans cet article, vous avez besoin des éléments suivants :  
1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](active-directory-ds-getting-started.md).
4. Veillez à configurer les adresses IP du domaine géré en tant que serveurs DNS pour le réseau virtuel. Pour plus d’informations, consultez [Comment mettre à jour les paramètres DNS pour le réseau virtuel Azure](active-directory-ds-getting-started-dns.md)
5. Effectuez les étapes requises pour [synchroniser les mots de passe à votre domaine géré Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Approvisionner une machine virtuelle Linux Ubuntu
Approvisionnez une machine virtuelle Linux Ubuntu dans Azure, en utilisant l’une des méthodes suivantes :
* [Portail Azure](../virtual-machines/linux/quick-create-portal.md)
* [interface de ligne de commande Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Déployez la machine virtuelle dans le **réseau virtuel au sein duquel vous avez activé Azure Active Directory Domain Services**.
> * Choisissez un **sous-réseau** autre que celui dans lequel vous avez activé Azure Active Directory Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Connexion à distance à la machine virtuelle Linux Ubuntu
La machine virtuelle Ubuntu a été configurée dans Azure. La tâche suivante consiste à se connecter à distance à la machine virtuelle à l’aide du compte administrateur local créé lors de l’approvisionnement de la machine virtuelle.

Suivez les instructions de l’article [Connexion à une machine virtuelle exécutant Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurer le fichier hosts sur la machine virtuelle Linux
Dans votre terminal SSH, modifiez le fichier/etc/hosts et mettez à jour l’adresse IP et le nom d’hôte de votre machine.

```
sudo vi /etc/hosts
```

Dans le fichier hosts, saisissez la valeur suivante :

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
Ici, « contoso100.com » est le nom de domaine DNS de votre domaine géré. « contoso-ubuntu » est le nom d’hôte de la machine virtuelle Ubuntu que vous joignez au domaine géré.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installer les packages requis sur la machine virtuelle Linux
Ensuite, installez les packages requis pour la jonction de domaine sur la machine virtuelle. Procédez comme suit :

1.  Dans votre terminal SSH, tapez la commande suivante pour télécharger les listes de package à partir des référentiels. Cette commande met à jour les listes de package pour obtenir plus d’informations sur les dernières versions des packages et leurs dépendances.

    ```
    sudo apt-get update
    ```

2. Saisissez la commande suivante pour installer les packages nécessaires.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Pendant l’installation de Kerberos, vous voyez un écran rose. L’installation du package « krb5-user » demande le nom de domaine (en majuscules). L’installation écrit les sections [realm] et [domain_realm] dans /etc/krb5.conf.

    > [!TIP]
    > Si le nom de votre domaine géré est contoso100.com, saisissez « CONTOSO100.COM » comme domaine. N’oubliez pas que le nom de domaine doit être écrit en majuscules.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Configurer les paramètres de protocole NTP (Network Time Protocol) sur la machine virtuelle Linux
La date et l’heure de votre machine virtuelle Ubuntu être synchronisées avec le domaine géré. Ajoutez le nom d’hôte NTP de votre domaine géré dans le fichier /etc/ntp.conf.

```
sudo vi /etc/ntp.conf
```

Dans le fichier ntp.conf, saisissez la valeur suivante et enregistrez le fichier :

```
server contoso100.com
```
Ici, « contoso100.com » est le nom de domaine DNS de votre domaine géré.

Effectuez maintenant la synchronisation de la date et de l’heure de la machine virtuelle Ubuntu avec le serveur NTP, puis démarrez le service NTP :

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Joindre la machine virtuelle Linux au domaine géré
Maintenant que les packages requis sont installés sur la machine virtuelle Linux, la tâche suivante consiste à joindre cette machine virtuelle au domaine géré.

1. Découvrez le domaine géré par les services de domaine Azure AD. Sur votre terminal SSH, saisissez la commande suivante :

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE] 
   > **Résolution des problèmes :** si *realm discover* ne peut pas trouver votre domaine géré :
     * Vérifiez que le domaine est accessible à partir de la machine virtuelle (effectuez un test ping).
     * Vérifiez également que la machine virtuelle a bien été déployée dans le réseau virtuel au sein duquel le domaine managé est disponible.
     * Vérifiez si vous avez mis à jour les paramètres du serveur DNS pour le réseau virtuel afin de pointer vers les contrôleurs de domaine du domaine managé.
   >

2. Initialisez Kerberos. Sur votre terminal SSH, saisissez la commande suivante : 

    > [!TIP] 
    > * Vérifiez que vous spécifiez un utilisateur appartenant au groupe « AAD DC Administrators ». 
    > * Spécifiez le nom de domaine en majuscules. Dans le cas contraire, kinit échoue.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Joignez l’ordinateur au domaine. Sur votre terminal SSH, saisissez la commande suivante : 

    > [!TIP] 
    > Utilisez le même compte utilisateur qu’à l’étape précédente (« kinit »).
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Vous devez voir apparaître un message signalant que l’ordinateur a bien été joint au domaine géré : « La machine a bien été inscrite dans le domaine ».


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Mettre à jour la configuration SSSD et redémarrer le service
1. Sur votre terminal SSH, saisissez la commande suivante. Ouvrez le fichier sssd.conf et effectuez la modification suivante.
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Commentez la ligne **use_fully_qualified_names = True** et enregistrez le fichier.
    ```
    # use_fully_qualified_names = True
    ```

3. Redémarrez le service SSSD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Configurer la création automatique du répertoire de base
Pour activer la création automatique du répertoire de base après la connexion des utilisateurs, saisissez les commandes suivantes dans votre terminal PuTTY :
```
sudo vi /etc/pam.d/common-session
```
    
Ajoutez la ligne suivante dans ce fichier sous la ligne « session optional pam_sss.so », puis enregistrez-le :
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Vérifier la jonction de domaine
Vérifiez rapidement si la machine a bien été jointe au domaine géré. Connectez-vous à la machine virtuelle Ubuntu jointe au domaine à l’aide d’une autre connexion SSH. Utilisez un compte d’utilisateur de domaine, puis vérifiez que le compte d’utilisateur est correctement résolu.

1. Sur votre terminal SSH, saisissez la commande suivante pour vous connecter à la machine virtuelle Ubuntu jointe au domaine à l’aide de SSH. Utilisez un compte de domaine appartenant au domaine géré (par exemple dans ce cas, « bob@CONTOSO100.COM »).
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. Sur votre terminal SSH, tapez la commande suivante pour voir si le répertoire de base a été initialisé correctement.
    ```
    pwd
    ```

3. Sur votre terminal SSH, tapez la commande suivante pour voir si les membres du groupe ont été correctement résolus.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Accorder les privilèges sudo au groupe « Administrateurs du contrôleur de domaine AAD »
Vous pouvez accorder des privilèges d’administration aux membres du groupe « Administrateurs du contrôleur de domaine AAD» sur la machine virtuelle Ubuntu. Le fichier sudo se trouve dans/etc/sudoers. Les membres de groupes AD ajoutés dans sudoers peuvent utiliser la commande sudo.

1. Sur votre terminal SSH, assurez-vous que vous êtes connecté avec des privilèges de superutilisateur. Vous pouvez utiliser le compte d’administrateur local que vous avez spécifié lors de la création de la machine virtuelle. Exécutez la commande suivante :
    ```
    sudo vi /etc/sudoers
    ```

2. Ajoutez l’entrée suivante au fichier/etc/sudoers et enregistrez-le :
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Vous pouvez maintenant vous connecter en tant que membre du groupe « Administrateurs de contrôleur de domaine AAD » et devriez avoir des privilèges d’administrateur sur la machine virtuelle.


## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
Reportez-vous à l’article relatif à la [résolution des problèmes de jonction de domaine](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) .


## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Connexion à une machine virtuelle exécutant Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
