---
title: "Résolution de problèmes de connexion SSH à une machine virtuelle Azure | Microsoft Docs"
description: "Dépannage d’erreurs SSH telles que l’échec de connexion SSH ou le refus de connexion SSH pour une machine virtuelle Azure exécutant Linux."
keywords: "connexion ssh refusée, erreur ssh, ssh azure, échec de connexion SSH"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 176477105e1f660b0bd22d95142b744ef17044ee
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Dépannage d’une connexion SSH à une machine virtuelle Linux Azure défaillante, qui génère une erreur ou qui est refusée
Il existe différentes raisons pour lesquelles des erreurs SSH (Secure Shell) se produisent, la connexion SSH échoue ou cette connexion est refusée lorsque vous tentez de vous connecter à une machine virtuelle Linux. Cet article vous aide à identifier et à corriger ces problèmes. Vous pouvez utiliser le portail Azure, l’interface de ligne de commande Azure ou l’extension d’accès aux machines virtuelles pour Linux pour dépanner et résoudre des problèmes de connexion.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](http://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](http://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Étapes de dépannage rapide
Après chaque étape de résolution des problèmes, essayez de vous reconnecter à la machine virtuelle.

1. réinitialiser la configuration SSH.
2. Réinitialisation des informations d'identification pour l’utilisateur.
3. Vérifiez les règles du [groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md) autorise le trafic SSH.
   * Vérifiez l’existence d’une règle de groupe de sécurité réseau pour autoriser le trafic SSH (par défaut, le port TCP 22).
   * Vous ne pouvez pas utiliser la redirection / mappage de port sans utiliser un équilibreur de charge Azure.
4. Vérifiez [l’intégrité des ressources de la machine virtuelle](../../resource-health/resource-health-overview.md). 
   * Assurez-vous que la machine virtuelle est intègre.
   * Si vous avez des diagnostics de démarrage activés, vérifiez que la machine virtuelle ne signale pas les erreurs de démarrage dans les journaux.
5. Redémarrez la machine virtuelle.
6. Redéployez la machine virtuelle.

Si vous cherchez des procédures de dépannage plus détaillées et des explications, poursuivez la lecture.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Méthodes disponibles pour résoudre les problèmes de connexion SSH
Vous pouvez réinitialiser les informations d’identification ou la configuration SSH avec l’une des méthodes suivantes :

* [Portail Azure](#use-the-azure-portal) : utile si vous devez rapidement réinitialiser la configuration SSH ou la clé SSH et que vous n’avez pas installé les outils Azure.
* [Azure CLI 2.0](#use-the-azure-cli-20) : si vous êtes déjà en ligne de commande, réinitialisez rapidement la configuration SSH ou les informations d’identification. Vous pouvez aussi utiliser [Azure CLI 1.0](#use-the-azure-cli-10).
* [L’extension VMAccessForLinux Azure](#use-the-vmaccess-extension) : création et réutilisation de fichiers de définition json pour réinitialiser la configuration SSH ou les informations d’identification utilisateur.

Après chaque étape de résolution des problèmes, essayez de nouveau de vous connecter à la machine virtuelle. Si vous ne parvenez toujours pas à vous connecter, essayez l’étape suivante.

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure
Le portail Azure offre un moyen rapide de réinitialiser la configuration SSH ou les informations d’identification utilisateur sans installer d’outils sur votre ordinateur local.

Sélectionnez votre machine virtuelle dans le portail Azure. Faites défiler jusqu'à la section **Support + dépannage** et sélectionnez **Réinitialiser le de mot de passe** comme dans l’exemple suivant :

![Réinitialisation de la configuration SSH ou des informations d’identification dans le portail Azure](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Réinitialisation de la configuration SSH
Dans un premier temps, sélectionnez `Reset configuration only` dans le menu déroulant **Mode** comme illustré dans la capture d’écran précédente, puis cliquez sur le bouton **Réinitialiser**. Une fois cette opération terminée, essayez de nouveau d’accéder à votre machine Virtuelle.

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialisation des informations d’identification SSH d’un utilisateur
Pour réinitialiser les informations d’identification d’un utilisateur existant, sélectionnez `Reset SSH public key` ou `Reset password` dans le menu de **Mode** comme dans la capture d’écran précédente. Spécifiez le nom d’utilisateur et une clé SSH ou un nouveau mot de passe, puis cliquez sur le bouton **Réinitialiser**.

Vous pouvez également créer un utilisateur avec des privilèges sudo sur la machine virtuelle à partir de ce menu. Entrez un nouveau nom d’utilisateur et un mot de passe ou une clé SSH qui correspond, puis cliquez sur le bouton **Réinitialiser**.

## <a name="use-the-azure-cli-20"></a>Utiliser Azure CLI 2.0
Si ce n’est déjà fait, installez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à votre compte Azure avec [az login](/cli/azure/reference-index#az_login).

Si vous avez créé et téléchargé une image de disque Linux personnalisée, assurez-vous que le [Microsoft Azure Linux Agent](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) version 2.0.5 ou ultérieure est installé. Pour les machines virtuelles créées à l’aide d’images de la galerie, cette extension de l’accès est déjà installée et configurée.

### <a name="reset-ssh-configuration"></a>Réinitialisation de la configuration SSH
Vous pouvez initialement essayer de réinitialiser la configuration SSH aux valeurs par défaut et de redémarrer le serveur SSH sur la machine virtuelle. Notez que cela ne change pas le nom du compte d’utilisateur, le mot de passe, ou les clés SSH.
L’exemple suivant utilise [az vm user reset-ssh](/cli/azure/vm/user#az_vm_user_reset_ssh) pour réinitialiser la configuration SSH sur la machine virtuelle nommée `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialisation des informations d’identification SSH d’un utilisateur
L’exemple suivant utilise [az vm user update](/cli/azure/vm/user#az_vm_user_update) pour réinitialiser les informations d’identification pour `myUsername` à la valeur spécifiée dans `myPassword`, sur la machine virtuelle `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Si vous utilisez l’authentification par clé SSH, vous pouvez réinitialiser la clé SSH pour un utilisateur donné. L’exemple suivant utilise **az vm access set-linux-user** pour mettre à jour la clé SSH stockée dans `~/.ssh/id_rsa.pub` pour l’utilisateur `myUsername`, sur la machine virtuelle `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Utilisation de l’extension VMAccess
L’extension d’accès de machine virtuelle pour Linux lit dans un fichier json qui définit les actions à effectuer. Ces actions incluent la réinitialisation SSHD, la réinitialisation d’une clé SSH ou l’ajout d’un utilisateur. Vous utilisez toujours l’interface de ligne de commande Azure pour appeler l’extension VMAccess, mais vous pouvez réutiliser les fichiers json sur plusieurs machines virtuelles si vous le souhaitez. Cette approche vous permet de créer un référentiel de fichiers json que vous pouvez ensuite appeler en fonction des scénarios.

### <a name="reset-sshd"></a>Réinitialiser SSHD
Créez un fichier nommé `settings.json` avec le contenu suivant :

```json
{  
    "reset_ssh":"True"
}
```

À l’aide de l’interface de ligne de commande Azure, appelez ensuite l’extension `VMAccessForLinux` pour réinitialiser votre connexion SSHD en spécifiant votre fichier json. L’exemple suivant utilise [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) pour réinitialiser SSHD sur la machine virtuelle nommée `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialisation des informations d’identification SSH d’un utilisateur
Si SSHD semble fonctionner correctement, vous pouvez réinitialiser les informations d’identification d’un utilisateur donné. Pour réinitialiser le mot de passe pour un utilisateur, créez un fichier nommé `settings.json`. L’exemple suivant réinitialise les informations d’identification pour `myUsername` sur la valeur spécifiée dans `myPassword`. Entrez les lignes suivantes dans votre fichier `settings.json` en utilisant vos propres valeurs :

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Pour réinitialiser la clé SSH pour un utilisateur, créez tout d’abord un fichier nommé `settings.json`. L’exemple suivant réinitialise les informations d’identification pour `myUsername` sur la valeur spécifiée dans `myPassword` sur la machine virtuelle nommée `myVM` dans `myResourceGroup`. Entrez les lignes suivantes dans votre fichier `settings.json` en utilisant vos propres valeurs :

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Après avoir créé votre fichier json, utilisez l’interface de ligne de commande Azure pour appeler l’extension `VMAccessForLinux` pour réinitialiser vos informations d’identification d’utilisateur SSH en spécifiant votre fichier json. L’exemple suivant réinitialise les informations d’identification sur la machine virtuelle nommée `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Utilisation de la CLI Azure 1.0
Si ce n’est déjà fait, [installez la CLI Azure 1.0 et connectez-vous à votre abonnement Azure](../../cli-install-nodejs.md). Assurez-vous d’utiliser le mode Resource Manager comme indiqué ci-après :

```azurecli
azure config mode arm
```

Si vous avez créé et téléchargé une image de disque Linux personnalisée, assurez-vous que le [Microsoft Azure Linux Agent](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) version 2.0.5 ou ultérieure est installé. Pour les machines virtuelles créées à l’aide d’images de la galerie, cette extension de l’accès est déjà installée et configurée.

### <a name="reset-ssh-configuration"></a>Réinitialisation de la configuration SSH
Il est possible que la configuration SSHD soit mal configurée ou que le service ait rencontré une erreur. Vous pouvez réinitialiser SSHD pour vous assurer que la configuration SSH elle-même est valide. La réinitialisation du SSHD doit être la première étape de dépannage que vous effectuez.

L’exemple suivant redémarre le SSHD nommé `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utilisez vos propres noms de machine virtuelle et de groupe de ressources comme suit :

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Réinitialisation des informations d’identification SSH d’un utilisateur
Si SSHD semble fonctionner correctement, vous pouvez réinitialiser le mot de passe d’un utilisateur donné. L’exemple suivant réinitialise les informations d’identification pour `myUsername` sur la valeur spécifiée dans `myPassword` sur la machine virtuelle nommée `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Si vous utilisez l’authentification par clé SSH, vous pouvez réinitialiser la clé SSH pour un utilisateur donné. L’exemple suivant met à jour la clé SSH stockée dans `~/.ssh/id_rsa.pub` pour l’utilisateur nommé `myUsername` sur la machine virtuelle nommée `myVM` dans `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Redémarrer une machine virtuelle
Si vous avez réinitialisé la configuration SSH et les informations d’identification utilisateur, ou si une erreur a été générée lors de cette opération, vous pouvez essayer de redémarrer la machine virtuelle à l’adresse liée aux problèmes de calcul.

### <a name="azure-portal"></a>Portail Azure
Pour redémarrer une machine virtuelle à l’aide du portail Azure, sélectionnez votre machine virtuelle, puis cliquez sur le bouton **Redémarrer** comme dans l’exemple suivant :

![Redémarrage d’une machine virtuelle dans le portail Azure](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
L’exemple suivant redémarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
L’exemple suivant utilise [az vm restart](/cli/azure/vm#az_vm_restart) pour redémarrer la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Redéploiement d’une machine virtuelle
Vous pouvez redéployer une machine virtuelle vers un autre nœud dans Azure, ce qui peut permettre de résoudre les problèmes de mise en réseau sous-jacents. Pour en savoir plus sur le redéploiement d’une machine virtuelle, consultez [Redéployer une machine virtuelle vers un nouveau nœud Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Une fois cette opération terminée, les données de disque éphémères sont perdues et les adresses IP dynamiques associées à la machine virtuelle sont mises à jour.
> 
> 

### <a name="azure-portal"></a>Portail Azure
Pour redéployer une machine virtuelle à l’aide du portail Azure, sélectionnez votre machine virtuelle et faites défiler jusqu'à la section **Support + dépannage**. Cliquez sur le bouton **Redéployer** comme dans l’exemple suivant :

![Redéploiement de la machine virtuelle dans le portail Azure](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
L’exemple suivant redéploie la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
L’exemple suivant utilise [az vm redeploy](/cli/azure/vm#az_vm_redeploy) pour redéployer la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`. Utilisez vos propres valeurs comme suit :

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Machines virtuelles créées à l’aide du modèle de déploiement Classic
Procédez comme suit pour résoudre les problèmes de connexion SSH les plus courants sur les machines virtuelles créées à l’aide du modèle de déploiement Classic. Après chaque étape, essayez de vous reconnecter à la machine virtuelle.

* Réinitialisez l’accès à distance à partir du [portail Azure](https://portal.azure.com). Dans le portail Azure, sélectionnez votre machine virtuelle et cliquez sur le bouton **Réinitialiser à distance...**.
* Redémarrez la machine virtuelle. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre machine virtuelle et cliquez sur le bouton **Redémarrer**.
    
* Redéployez la machine virtuelle vers un nouveau nœud Azure. Pour en savoir plus sur le redéploiement d’une machine virtuelle, consultez [Redéployer une machine virtuelle vers un nouveau nœud Azure](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    Une fois cette opération terminée, les données de disque éphémères sont perdues et les adresses IP dynamiques associées à la machine virtuelle sont mises à jour.
* Suivez les instructions dans [Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion dans une machine virtuelle Windows](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) pour :
  
  * réinitialiser le mot de passe ou la clé SSH ;
  * créer un nouveau compte utilisateur *sudo* ;
  * réinitialiser la configuration SSH.
* Vérifiez l’intégrité des ressources de la ressource de machine virtuelle et recherchez s’il existe des problèmes liés à la plateforme.<br>
     Sélectionnez votre machine virtuelle et faites défiler jusqu’à **Paramètres** > **Vérifier l’intégrité**.

## <a name="additional-resources"></a>Ressources supplémentaires
* Si vous ne parvenez toujours pas à établir une connexion SSH à votre machine virtuelle une fois ces étapes effectuées, suivez les [étapes supplémentaires de dépannage détaillées](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour découvrir des étapes supplémentaires susceptibles de résoudre votre problème.
* Pour plus d’informations sur la résolution des problèmes d’accès aux applications, consultez la page [Résolution des problèmes d’accès à une application exécutée sur une machine virtuelle Azure](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Pour plus d’informations sur la résolution des problèmes liés aux machines virtuelles créées à l’aide du modèle de déploiement Classic, consultez [Réinitialisation d’un mot de passe ou de SSH pour les machines virtuelles basées sur Linux](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

