---
title: Console série de machine virtuelle Azure | Microsoft Docs
description: Console série bidirectionnelle pour machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: 9c16114b4f8d335dc750b1beef1fb6204ae20e0f
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

## <a name="open-the-serial-console"></a>Ouvrir la console série
Pour les machines virtuelles, la console série est accessible uniquement via le [portail Azure](https://portal.azure.com). Voici les étapes permettant aux machines virtuelles d’accéder à la console série via le portail : 

  1. Ouvrez le portail Azure
  2. Dans le menu de gauche, sélectionnez Machines virtuelles.
  3. Cliquez sur la machine virtuelle de votre choix dans la liste. La page de présentation de la machine virtuelle s’ouvre.
  4. Faites défiler jusqu’à la section Support + dépannage, puis cliquez sur l’option Console série (en préversion). Un nouveau volet s’ouvre avec la console série, puis démarre la connexion.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

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
Même si les mots de passe d’accès à la console ne sont pas journalisés, si des commandes exécutées dans la console contiennent ou affichent des mots de passe, des secrets, des noms d’utilisateur ou toute autre forme d’informations d’identification personnelle (PII), ceux-ci seront écrits dans les journaux Diagnostics de démarrage de la machine virtuelle, avec tout autre texte visible, dans le cadre de l’implémentation de la fonctionnalité de scrollback de la console série. Ces journaux sont circulaires et seules les personnes disposant d’autorisations de lecture pour le compte de stockage de diagnostics peuvent y accéder. Toutefois, nous vous recommandons de suivre les bonnes pratiques concernant l’utilisation du Bureau à distance pour tout élément pouvant impliquer des secrets et/ou des informations d’identification personnelle. 

### <a name="concurrent-usage"></a>Utilisation simultanée
Si un utilisateur est connecté à la console série alors qu’un autre utilisateur demande l’accès à la même machine virtuelle, le premier utilisateur est déconnecté pendant que le deuxième utilisateur est connecté. Le premier utilisateur quitte la console pendant que le nouvel utilisateur s’installe, en quelque sorte.

>[!CAUTION] 
Cela signifie donc que l’utilisateur qui laisse sa place n’est pas réellement déconnecté. La possibilité d’appliquer une déconnexion réelle (via SIGHUP ou autre mécanisme similaire) est actuellement étudiée. Pour Windows, il existe un délai d’expiration automatique qui est activé dans la console SAC (Special Administrative Console). Toutefois, pour Linux, vous pouvez configurer un délai d’expiration terminal. 


## <a name="accessing-serial-console-for-windows"></a>Accès à la console série pour Windows 
La [console SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) est activée par défaut dans les nouvelles images Windows Server sur Azure. La console SAC est prise en charge sur les versions serveur de Windows, mais n’est pas disponible sur les versions client (comme Windows 10, Windows 8 ou Windows 7). Pour activer la console série dans les machines virtuelles Windows créées à l’aide de l’image Feb2018 (ou images antérieures), procédez aux étapes suivantes : 

1. Connectez-vous à la machine virtuelle Windows via le Bureau à distance
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes : 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Redémarrez le système pour activer la console SAC

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Si nécessaire, la console SAC peut être activée hors connexion. 

1. Sélectionnez le disque Windows pour lequel vous souhaitez configurer la console SAC et attachez-le en tant que disque de données à la machine virtuelle existante. 
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes : 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Comment savoir si la console SAC est activée ? 

Si la [console SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) n’est pas activée, la console série n’affiche pas l’invite pour la console SAC. Elle peut afficher des informations relatives à l’intégrité de la machine virtuelle dans certains cas, ou ne rien afficher.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>Activation du menu de démarrage à afficher dans la console série 

Si vous souhaitez que les invites du chargeur de démarrage Windows s’affichent dans la console série, vous pouvez définir les options supplémentaires suivantes pour le chargeur de démarrage de Windows.

1. Connectez-vous à la machine virtuelle Windows via le Bureau à distance
2. Dans une invite de commandes d’administration, exécutez les commandes suivantes : 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Redémarrer le système pour activer le menu de démarrage

> [!NOTE] 
> À ce stade, la prise en charge des clés de fonction n’est pas activée. Si vous avez besoin d’options de démarrage avancées, utilisez bcdedit /set {current} onetimeadvancedoptions on. Pour plus d’informations, consultez [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

## <a name="common-scenarios-for-accessing-windows-serial-console"></a>Scénarios courants pour l’accès à la console série Windows 
Scénario          | Actions à effectuer dans la console série                
:------------------|:----------------------------------------
Règles de pare-feu incorrectes | Accédez à la console série et corrigez les tables d’adresses IP ou les règles de pare-feu Windows 
Contrôle de la corruption du système de fichiers | Accédez à la console série et effectuez une récupération du système de fichiers après vous être connecté au canal CMD de la console SAC
Problèmes de configuration RDP | Accédez à la console série et connectez-vous au canal CMD Vérifiez l’intégrité des services Terminal et redémarrez si nécessaire
Système de verrouillage du réseau| Accédez à la console série et connectez-vous au canal CMD. Vérifiez l’état du pare-feu avec la commande [netsh](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts). 

## <a name="errors"></a>Errors
La plupart des erreurs sont de nature temporaire et peuvent être corrigées par une nouvelle tentative de connexion. Le tableau ci-dessous présente une liste d’erreurs accompagnées de solutions d’atténuation. 

Error                            |   Atténuation 
:---------------------------------|:--------------------------------------------|
Unable to retrieve boot diagnostics settings for '<VMNAME>'. To use the serial console, ensure that boot diagnostics is enabled for this VM (Impossible de récupérer les paramètres de diagnostic de démarrage. Pour utiliser la console série, vérifiez que les diagnostics de démarrage sont activés dans la machine virtuelle) | Vérifiez que l’option [diagnostics de démarrage](boot-diagnostics.md) est activée dans la machine virtuelle. 
The VM is in a stopped deallocated state. Start the VM and retry the serial console connection (La machine virtuelle est arrêtée et à l’état Désalloué. Démarrez la machine virtuelle, puis retentez une connexion à la console série) | La machine virtuelle doit être à l’état Démarré pour accéder à la console série.
You do not have the required permissions to use this VM serial console. Ensure you have at least VM Contributor role permissions (Vous ne disposez pas des autorisations nécessaires pour utiliser la console série sur cette machine virtuelle. Vous devez disposer du rôle Contributeur pour accéder à la console sur cette machine virtuelle)| L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).
Unable to determine the resource group for the boot diagnostics storage account '<STORAGEACCOUNTNAME>'. Verify that boot diagnostics is enabled for this VM and you have access to this storage account (Impossible de déterminer le groupe de ressources pour le compte de stockage de diagnostic de démarrage. Vérifiez que les diagnostics de démarrage sont activés sur la machine virtuelle et que vous avez accès au compte de stockage) | L’accès à la console série nécessite certaines autorisations. Pour plus d’informations, consultez les [conditions d’accès](#prerequisites).

## <a name="known-issues"></a>Problèmes connus 
Étant donné qu’il s’agit d’une préversion, l’accès à la console série présente encore certains problèmes, pour lesquels nous fournissons les solutions ci-dessous. 

Problème                           |   Atténuation 
:---------------------------------|:--------------------------------------------|
Aucune option n’est disponible pour la console série de l’instance de groupe de machines virtuelles identiques | À ce stade de la préversion, l’accès à la console série n’est pas pris en charge pour les instances de groupe de machines virtuelles identiques.
L’invite de connexion ne s’affiche pas lorsque vous appuyez sur la touche Entrée après l’affichage de la bannière de connexion | [La touche Entrée n’a aucun effet](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Seules les informations d’intégrité sont affichées lors de la connexion à une machine virtuelle Windows| [Signaux d’intégrité Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ) 
**Q. Comment puis-je envoyer des commentaires ?**

R. Pour nous contacter au sujet d’un problème, accédez à la page https://aka.ms/serialconsolefeedback (méthode conseillée). Vous pouvez également envoyer des commentaires sur azserialhelp@microsoft.com ou sous la catégorie Machine virtuelle de la page http://feedback.azure.com. 

**Q. J’obtiens l’erreur indiquant que le type de système d’exploitation (Windows) de la console existante est en conflit avec le type de système d’exploitation demandé (Linux)**

R. Il s’agit d’un problème connu. Pour le résoudre, ouvrez [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) en mode Bash, puis réessayez.

**Q. Je n’arrive pas à accéder à la console série. Où puis-je effectuer une demande de support ?**

R. Cette fonctionnalité est en préversion, et est donc couverte par les Conditions d’utilisation des préversions Azure. La prise en charge de cette fonctionnalité est optimale avec les canaux mentionnés plus haut. 

## <a name="next-steps"></a>Étapes suivantes
* La console série est également disponible pour les machines virtuelles [Linux](../linux/serial-console.md).
* En savoir plus sur les [diagnostics de démarrage](boot-diagnostics.md)
