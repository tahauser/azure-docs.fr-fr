---
title: Console série de machine virtuelle Azure | Microsoft Docs
description: Console série bidirectionnelle pour machines virtuelles Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: b7d6e48a6f34472bc38947fd70e850b1c3bf6f8a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Console série de machine virtuelle (préversion) 


La console série de machine virtuelle Azure permet aux machines virtuelles Linux et Windows d’accéder à une console texte. Cette connexion série est effectuée via le port série COM1 de la machine virtuelle. Elle fournit l’accès à la machine virtuelle et n’est pas liée au réseau de la machine virtuelle ni à l’état du système d’exploitation. Pour une machine virtuelle, l’accès à la console série n’est possible que via le portail Azure. De plus, seuls les utilisateurs disposant d’un rôle de Contributeur (ou supérieur) pour cette machine virtuelle sont autorisés à accéder à la console. 

> [!Note] 
> Les préversions sont à votre disposition, à condition que vous acceptiez les conditions d’utilisation. Pour plus d’informations, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Actuellement, ce service est disponible en **préversion publique** et l’accès à la console série pour les machines virtuelles est disponible pour toutes les régions Azure. À ce stade, la console série n’est pas disponible pour Azure Government, Azure Allemagne et Azure Chine.


## <a name="prerequisites"></a>Prérequis
 

* L’option [Diagnostics de démarrage](boot-diagnostics.md) doit être activée dans la machine virtuelle. 
* Le compte qui utilise la console série doit disposer du [rôle Contributeur](../../active-directory/role-based-access-built-in-roles.md) pour la machine virtuelle et pour le compte de stockage avec [diagnostics de démarrage](boot-diagnostics.md). 
* Pour découvrir les paramètres spécifiques à la distribution Linux, consultez la section [Accessing the serial console for Linux](#accessing-serial-console-for-linux) (Accès à la console série pour Linux).


## <a name="open-the-serial-console"></a>Ouvrir la console série
Pour les machines virtuelles, la console série est accessible uniquement via le [portail Azure](https://portal.azure.com). Voici les étapes permettant aux machines virtuelles d’accéder à la console série via le portail : 

  1. Ouvrez le portail Azure
  2. Dans le menu de gauche, sélectionnez Machines virtuelles.
  3. Cliquez sur la machine virtuelle de votre choix dans la liste. La page de présentation de la machine virtuelle s’ouvre.
  4. Faites défiler jusqu’à la section Support + dépannage, puis cliquez sur l’option Console série (en préversion). Un nouveau volet s’ouvre avec la console série, puis démarre la connexion.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> La console série nécessite la configuration d’un utilisateur local avec un mot de passe. À ce stade, les machines virtuelles configurées uniquement avec la clé publique SSH ne disposent d’aucun accès à la console en série. Pour créer un utilisateur local avec un mot de passe, suivez les instructions de la section [Extension d’accès aux machines virtelles](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), puis créez un utilisateur local avec un mot de passe.

### <a name="disable-feature"></a>Désactiver la fonctionnalité
La fonctionnalité Console série peut être désactivée pour certaines machines virtuelles, en désactivant le paramètre de diagnostics de démarrage.

## <a name="serial-console-security"></a>Sécurité de la console série 

### <a name="access-security"></a>Sécurité des accès 
L’accès à la console série est limité aux utilisateurs qui disposent du rôle [Contributeur](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) (ou supérieur) pour la machine virtuelle. Si votre locataire AAD nécessite l’authentification MFA, l’accès à la console série nécessite également l’authentification MFA, puisque son accès s’effectue via le [portail Azure](https://portal.azure.com).

### <a name="channel-security"></a>Sécurité des canaux
Toutes les données envoyées sur les canaux sont chiffrées.

### <a name="audit-logs"></a>Journaux d’audit
Tous les accès à la console série sont journalisés dans les journaux [Diagnostics de démarrage](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la machine virtuelle. L’accès à ces journaux est détenu et contrôlé par l’administrateur de la machine virtuelle Azure.  

>[!CAUTION] 
Même si les mots de passe d’accès à la console ne sont pas journalisés, si des commandes exécutées dans la console contiennent ou affichent des mots de passe, des secrets, des noms d’utilisateur ou toute autre forme d’informations d’identification personnelle (PII), ceux-ci seront écrits dans les journaux Diagnostics de démarrage de la machine virtuelle, avec tout autre texte visible, dans le cadre de l’implémentation de la fonctionnalité de scrollback de la console série. Ces journaux sont circulaires et seules les personnes disposant d’autorisations de lecture pour le compte de stockage de diagnostics peuvent y accéder. Toutefois, nous vous recommandons de suivre les bonnes pratiques concernant l’utilisation de la console SSH pour tout élément pouvant impliquer des secrets et/ou des informations d’identification personnelle. 

### <a name="concurrent-usage"></a>Utilisation simultanée
Si un utilisateur est connecté à la console série alors qu’un autre utilisateur demande l’accès à la même machine virtuelle, le premier utilisateur est déconnecté pendant que le deuxième utilisateur est connecté. Le premier utilisateur quitte la console pendant que le nouvel utilisateur s’installe, en quelque sorte.

>[!CAUTION] 
Cela signifie donc que l’utilisateur qui laisse sa place n’est pas réellement déconnecté. La possibilité d’appliquer une déconnexion réelle (via SIGHUP ou autre mécanisme similaire) est actuellement étudiée. Pour Windows, il existe un délai d’expiration automatique qui est activé dans la console SAC (Special Administrative Console). Toutefois, pour Linux, vous pouvez configurer un délai d’expiration terminal. Pour ce faire, il vous suffit d’ajouter `export TMOUT=600` à votre profil .bash_profile ou .profile pour l’utilisateur avec lequel vous vous connectez à la console, pour mettre fin à la session après un délai de 10 minutes.

### <a name="disable-feature"></a>Désactiver la fonctionnalité
La fonctionnalité Console série peut être désactivée pour certaines machines virtuelles, en désactivant le paramètre de diagnostics de démarrage.

## <a name="common-scenarios-for-accessing-serial-console"></a>Scénarios courants pour l’accès à la console série 
Scénario          | Actions à effectuer dans la console série                |  Applicabilité du système d’exploitation 
:------------------|:-----------------------------------------|:------------------
Fichier FSTAB endommagé | Saisissez une clé pour continuer et réparer le fichier fstab à l’aide d’un éditeur de texte. Découvrez comment [résoudre les incidents fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) | Linux 
Règles de pare-feu incorrectes | Accédez à la console série et corrigez les tables d’adresses IP ou les règles de pare-feu Windows | Linux/Windows 
Contrôle de la corruption du système de fichiers | Accès à la console série et récupération du système de fichiers | Linux/Windows 
Problèmes de configuration SSH/RDP | Accès à la console série et modification des paramètres | Linux/Windows 
Système de verrouillage du réseau| Accès à la console série via le portail de gestion du système | Linux/Windows 
Interaction avec le chargeur de démarrage | Accès à GRUB/BCD via la console série | Linux/Windows 

## <a name="accessing-serial-console-for-linux"></a>Accès à la console série pour Linux
Pour permettre le bon fonctionnement de la console série, le système d’exploitation invité doit être configuré pour la lecture et l’écriture des messages de console sur le port série. La plupart des [distributions Azure Linux approuvées](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) présentent la console série configurée par défaut. Un simple clic sur le portail de la section de la console série vous octroie l’accès à la console. 

### <a name="access-for-redhat"></a>Accès Red Hat 
Les images Red Hat disponibles sur Azure disposent de l’accès à la console activé par défaut. Dans Red Hat, le mode d’utilisateur unique nécessite l’activation d’un utilisateur racine, qui est désactivé par défaut. Si vous avez besoin d’activer le mode d’utilisateur unique, appliquez les instructions suivantes :

1. Connectez-vous au système Red Hat via SSH
2. Activez le mot de passe pour l’utilisateur racine 
 * `passwd root` (définissez un mot de passe racine fort)
3. Assurez-vous que l’utilisateur racine peut se connecter uniquement via ttyS0
 * `edit /etc/ssh/sshd_config` et veillez à ce que la connexion PermitRootLogin soit désactivée
 * `edit /etc/securetty file` pour autoriser uniquement la connexion via ttyS0 

Désormais, si le système démarre en mode d’utilisateur unique, vous pouvez vous connecter uniquement à l’aide du mot de passe racine.

Sinon, pour RHEL 7.4+ ou 6.9+, vous pouvez activer le mode d’utilisateur unique dans les invites GRUB ; consultez les instructions [ici](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="access-for-ubuntu"></a>Accès Ubuntu 
Les images Ubuntu disponibles sur Azure disposent de l’accès à la console activé par défaut. Si le système démarre en mode d’utilisateur unique, vous pouvez accéder sans informations d’identification supplémentaires. 

### <a name="access-for-coreos"></a>Accès CoreOS
Les images CoreOS disponibles sur Azure disposent de l’accès à la console activé par défaut. Si nécessaire, le système peut être démarré en mode d’utilisateur unique au moyen de la modification des paramètres GRUB, tandis que l’ajout de `coreos.autologin=ttyS0` permet à l’utilisateur principal de se connecter et d’être disponible dans la console série. 

### <a name="access-for-suse"></a>Accès SUSE
Les images SLES disponibles sur Azure disposent de l’accès à la console activé par défaut. Si vous utilisez des versions antérieures de SLES sur Azure, suivez les instructions de l’[article de la base de connaissances](https://www.novell.com/support/kb/doc.php?id=3456486) pour activer la console série. Les images plus récentes de SLES 12 SP3+ autorisent également l’accès via la console en série dans les situations où le système démarre en mode d’urgence.

### <a name="access-for-centos"></a>Accès CentOS
Les images CentOS disponibles sur Azure disposent de l’accès à la console activé par défaut. En mode d’utilisateur unique, suivez les instructions associées aux images Red Hat ci-dessus. 

### <a name="access-for-oracle-linux"></a>Accès Oracle Linux
Les images Oracle Linux disponibles sur Azure disposent de l’accès à la console activé par défaut. En mode d’utilisateur unique, suivez les instructions associées aux images Red Hat ci-dessus.

### <a name="access-for-custom-linux-image"></a>Accès à l’image personnalisé Linux
Pour activer la console série pour votre image Linux personnalisée de machine virtuelle, activez l’accès à la console dans /etc/inittab pour exécuter un terminal sur ttyS0. Vous trouverez ci-dessous un exemple d’ajout de cet élément dans le fichier inittab. 

`S0:12345:respawn:/sbin/agetty -L 115200 console vt102` 

## <a name="errors"></a>Errors
La plupart des erreurs sont de nature temporaire et peuvent être corrigées par une nouvelle tentative de connexion. Le tableau ci-dessous présente une liste d’erreurs accompagnées de solutions d’atténuation. 

Error                            |   Atténuation 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>'. To use the serial console, ensure that boot diagnostics is enabled for this VM (Impossible de récupérer les paramètres de diagnostic de démarrage. Pour utiliser la console série, vérifiez que les diagnostics de démarrage sont activés dans la machine virtuelle) | Vérifiez que l’option [diagnostics de démarrage](boot-diagnostics.md) est activée dans la machine virtuelle. 
The VM is in a stopped deallocated state. Start the VM and retry the serial console connection (La machine virtuelle est arrêtée et à l’état Désalloué. Démarrez la machine virtuelle, puis retentez une connexion à la console série) | La machine virtuelle doit être à l’état Démarré pour accéder à la console série.
You do not have the required permissions to use this VM serial console. Ensure you have at least VM Contributor role permissions (Vous ne disposez pas des autorisations nécessaires pour utiliser la console série sur cette machine virtuelle. Vous devez disposer du rôle Contributeur pour accéder à la console sur cette machine virtuelle)| L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. Verify that boot diagnostics is enabled for this VM and you have access to this storage account (Impossible de déterminer le groupe de ressources pour le compte de stockage de diagnostic de démarrage. Vérifiez que les diagnostics de démarrage sont activés sur la machine virtuelle et que vous avez accès au compte de stockage) | L’accès à la console série nécessite certaines autorisations. Pour plus de détails, consultez les [conditions d’accès](#prerequisites).

## <a name="known-issues"></a>Problèmes connus 
Étant donné qu’il s’agit d’une préversion, l’accès à la console série présente encore certains problèmes, pour lesquels nous fournissons les solutions ci-dessous. 

Problème                           |   Atténuation 
:---------------------------------|:--------------------------------------------|
Aucune option n’est disponible pour la console série de l’instance de groupe de machines virtuelles identiques |  À ce stade de la préversion, l’accès à la console série n’est pas pris en charge pour les instances de groupe de machines virtuelles identiques.
L’invite de connexion ne s’affiche pas lorsque vous appuyez sur la touche Entrée après l’affichage de la bannière de connexion | [La touche Entrée n’a aucun effet](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 
**Q. Comment puis-je envoyer des commentaires ?**

R. Pour nous contacter au sujet d’un problème, accédez à la page https://aka.ms/serialconsolefeedback (méthode conseillée). Vous pouvez également envoyer des commentaires sur azserialhelp@microsoft.com ou sous la catégorie Machine virtuelle de la page http://feedback.azure.com.

**Q. J’obtiens l’erreur indiquant que le type de système d’exploitation (Windows) de la console existante est en conflit avec le type de système d’exploitation demandé (Linux)**

R. Il s’agit d’un problème connu. Pour le résoudre, ouvrez [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) en mode Bash, puis réessayez.

**Q. Je n’arrive pas à accéder à la console série. Où puis-je effectuer une demande de support ?**

R. Cette fonctionnalité est en préversion, et est donc couverte par les Conditions d’utilisation des préversions Azure. La prise en charge de cette fonctionnalité est optimale avec les canaux mentionnés plus haut. 

## <a name="next-steps"></a>Étapes suivantes
* La console série est également disponible pour les machines virtuelles [Windows](../windows/serial-console.md).
* En savoir plus sur les [diagnostics de démarrage](boot-diagnostics.md)