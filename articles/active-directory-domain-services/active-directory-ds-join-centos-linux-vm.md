---
title: 'Azure Active Directory Domain Services : joindre une machine virtuelle CentOS à un domaine managé | Microsoft Docs'
description: Joindre une machine virtuelle Linux CentOS à Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: f7095e82605d12221ba7beb0c48fd3eda75a2e7b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Joindre une machine virtuelle Linux CentOS à un domaine managé
Cet article indique comment joindre une machine virtuelle Linux CentOS dans Azure à un domaine managé par Azure AD Domain Services.

## <a name="before-you-begin"></a>Avant de commencer
Pour exécuter les tâches indiquées dans cet article, vous avez besoin des éléments suivants :
1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](active-directory-ds-getting-started.md).
4. Veillez à configurer les adresses IP du domaine géré en tant que serveurs DNS pour le réseau virtuel. Pour plus d’informations, consultez [Comment mettre à jour les paramètres DNS pour le réseau virtuel Azure](active-directory-ds-getting-started-dns.md)
5. Effectuez les étapes requises pour [synchroniser les mots de passe à votre domaine géré Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Provisionner une machine virtuelle Linux CentOS
Provisionnez une machine virtuelle CentOS dans Azure, en utilisant l’une des méthodes suivantes :
* [Portail Azure](../virtual-machines/linux/quick-create-portal.md)
* [interface de ligne de commande Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Déployez la machine virtuelle dans le **réseau virtuel au sein duquel vous avez activé Azure Active Directory Domain Services**.
> * Choisissez un **sous-réseau** autre que celui dans lequel vous avez activé Azure Active Directory Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Connexion à distance à la machine virtuelle Linux qui vient d’être configurée
La machine virtuelle CentOS a été configurée dans Azure. La tâche suivante consiste à se connecter à distance à la machine virtuelle à l’aide du compte administrateur local créé lors de l’approvisionnement de la machine virtuelle.

Suivez les instructions de l’article [Connexion à une machine virtuelle exécutant Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurer le fichier hosts sur la machine virtuelle Linux
Dans votre terminal SSH, modifiez le fichier/etc/hosts et mettez à jour l’adresse IP et le nom d’hôte de votre machine.

```
sudo vi /etc/hosts
```

Dans le fichier hosts, saisissez la valeur suivante :

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
Ici, « contoso100.com » est le nom de domaine DNS de votre domaine géré. « contoso-centos » est le nom d’hôte de la machine virtuelle CentOS que vous joignez au domaine managé.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installer les packages requis sur la machine virtuelle Linux
Ensuite, installez les packages requis pour la jonction de domaine sur la machine virtuelle. Dans votre terminal SSH, saisissez la commande suivante pour installer les packages nécessaires :

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
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
    > * Spécifiez un utilisateur appartenant au groupe « AAD DC Administrators ».
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Vous devez voir apparaître un message signalant que l’ordinateur a bien été joint au domaine géré : « La machine a bien été inscrite dans le domaine ».


## <a name="verify-domain-join"></a>Vérifier la jonction de domaine
Vérifiez rapidement si la machine a bien été jointe au domaine géré. Connectez-vous à la machine virtuelle CentOS jointe au domaine à l’aide d’une autre connexion SSH. Utilisez un compte d’utilisateur de domaine, puis vérifiez que le compte d’utilisateur est correctement résolu.

1. Sur votre terminal SSH, saisissez la commande suivante pour vous connecter à la machine virtuelle CentOS jointe au domaine à l’aide de SSH. Utilisez un compte de domaine appartenant au domaine géré (par exemple dans ce cas, « bob@CONTOSO100.COM »).
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. Sur votre terminal SSH, tapez la commande suivante pour voir si le répertoire de base a été initialisé correctement.
    ```
    pwd
    ```

3. Sur votre terminal SSH, tapez la commande suivante pour voir si les membres du groupe ont été correctement résolus.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Résolution des problèmes de jonction de domaine
Reportez-vous à l’article relatif à la [résolution des problèmes de jonction de domaine](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) .

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de prise en main](active-directory-ds-getting-started.md)
* [Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Connexion à une machine virtuelle exécutant Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Installing Kerberos (Installation de Kerberos)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7 - Guide d’intégration à Windows)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
