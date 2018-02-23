---
title: "Résoudre des problèmes liés à l’activation de machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Indique les étapes de résolution des problèmes relatifs à l’activation de machines virtuelles Windows dans Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: fd3f52de40c6d448d457824bcc675db036d2cb86
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Résoudre des problèmes liés à l’activation de machines virtuelles Windows Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Si vous rencontrez des problèmes lors de l’activation de machines virtuelles Windows Azure créées à partir d’une image personnalisée, vous pouvez utiliser les informations disponibles de ce document pour résoudre le problème. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Présentation des points de terminaison Azure KMS pour l’activation des produits Windows des machines virtuelles Azure
Azure utilise différents points de terminaison différents pour l’activation de KMS en fonction de la région cloud dans laquelle se trouve la machine virtuelle. Lorsque vous utilisez ce guide de dépannage, utilisez le point de terminaison KMS approprié qui s’applique à votre région.

* Régions de cloud public Azure : kms.core.windows.net:1688
* Régions de cloud national Azure en Chine : kms.core.chinacloudapi.cn:1688
* Régions de cloud national Azure en Allemagne : kms.core.cloudapi.de:1688
* Régions de cloud national Azure aux États-Unis : kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Symptôme

Lorsque vous essayez d’activer une machine virtuelle Windows Azure, vous recevez un message d’erreur semblable à l’exemple suivant :

**Erreur 0xC004F074  : le logiciel LicensingService a signalé que l’ordinateur n’a pas pu être activé. Aucun service de gestion des clés (KMS) n’a pu être contacté. Pour obtenir plus d’informations, veuillez consulter le Journal des événements de l’application.**

## <a name="cause"></a>Cause :
En règle générale, les problèmes d’activation de machines virtuelles Azure se produisent si la machine virtuelle Windows n’est pas configurée à l’aide de la bonne clé d’installation client KMS. Ces problèmes peuvent également survenir si la machine virtuelle Windows rencontre un problème de connectivité au service Azure KMS (kms.core.windows.net, port 1668). 

## <a name="solution"></a>Solution

>[!NOTE]
>Si vous utilisez un réseau privé virtuel de site à site et un tunneling forcé, consultez l’article [Use Azure custom routes to enable KMS activation with forced tunneling](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) (Utiliser les itinéraires personnalisés Azure pour permettre l’activation du service de gestion de clés avec un tunneling forcé). 
>
>Si vous utilisez le service ExpressRoute et possédez un itinéraire publié par défaut, consultez l’article [Azure VM may fail to activate over ExpressRoute](https://blogs.technet.microsoft.com/jpaztech/2016/05/16/azure-vm-may-fail-to-activate-over-expressroute/) (Une machine virtuelle Azure peut être incapable d’activer ExpressRoute).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Étape 1 : Configurer la bonne clé d’installation client KMS (pour Windows Server 2016 et Windows Server 2012 R2)

Pour la machine virtuelle créée à partir d’une image personnalisée de Windows Server 2016 ou Windows Server 2012 R2, vous devez configurer la clé d’installation client KMS adéquate pour la machine virtuelle.

Cette étape ne s’applique pas pour Windows 2012 ou Windows 2008 R2. Elle utilise la fonctionnalité d’activation automatique de machine virtuelle (AVMA). Cette dernière est uniquement prise en charge par Windows Server 2016 et Windows Server 2012 R2.

1. Exécutez **slmgr.vbs /dlv** dans une invite de commandes avec élévation de privilèges. Dans la sortie, vérifiez la valeur Description. Ensuite, déterminez si cette valeur a été créée à partir de la vente au détail (Canal de vente au détail) ou d’un support de licence en volume (VOLUME_KMSCLIENT) :
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Si **slmgr.vbs /dlv**affiche le canal de vente au détail, exécutez les commandes suivantes afin de définir la [clé d’installation client KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) de la version de Windows Server utilisée. Forcez ensuite la tentative d’activation : 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Par exemple, pour Windows Server 2016 Datacenter, exécutez la commande suivante :

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Étape 2 : Vérifier la connectivité entre la machine virtuelle et le service Azure KMS

1. Téléchargez et extrayez l’outil [Psping](http:/technet.microsoft.com/en-us/sysinternals/jj729731.aspx) dans un dossier local sur la machine virtuelle qui ne s’active pas. 

2. Accédez au menu Démarrer, effectuez une recherche pour Windows PowerShell, cliquez avec le bouton droit sur Windows PowerShell, puis sélectionnez l’option Exécuter en tant qu’administrateur.

3. Assurez-vous que la machine virtuelle est configurée pour utiliser le bon serveur Azure KMS. Pour ce faire, exécutez la commande suivante :
  
    ```
    iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms
    kms.core.windows.net:1688
    ```
    La commande doit renvoyer le message suivant : le nom de machine de KMS a été configuré sur kms.core.windows.net:1688 avec succès.

4. Vérifiez à l’aide de Psping que vous avez une connexion au serveur KMS. Basculez vers le dossier où vous avez extrait le téléchargement Pstools.zip, puis exécutez la commande suivante :
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
  Dans l’avant-dernière ligne de la sortie, assurez-vous que les informations suivantes s’affichent : Envoyé = 4, Reçu = 4, Perdu = 0 (0 % de perte).

  Si la perte est supérieure à 0 (zéro), la machine virtuelle n’a pas de connectivité au serveur KMS. Dans ce cas, si la machine virtuelle se trouve au sein d’un réseau virtuel et a spécifié un serveur DNS personnalisé, vérifiez que le serveur DNS est capable de résoudre kms.core.windows.net. Sinon, passez à un serveur DNS capable de résoudre kms.core.windows.net.

  Notez que si vous supprimez l’ensemble des serveurs DNS du réseau virtuel, les machines virtuelles utiliseront le service DNS interne d’Azure. Ce service peut résoudre kms.core.windows.net.
  
Vérifiez également que le pare-feu invité n’a pas été configuré de manière à bloquer les tentatives d’activation.

5. Après avoir vérifié que la connectivité à kms.core.windows.net fonctionne, exécutez la commande suivante dans l’invite Windows PowerShell avec élévation de privilèges. Cette commande tente plusieurs fois l’activation.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Une activation réussie renvoie des informations qui ressemblent à ceci :

**Activation de Windows(R), édition ServerDatacenter (12345678-1234-1234-1234-12345678)... Le produit a été activé.**

## <a name="faq"></a>Forum Aux Questions 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>J’ai créé l’image Windows Server 2016 à partir de la Place de marché Microsoft Azure. Ai-je besoin de configurer la clé KMS pour l’activation de Windows Server 2016 ? 
 
Non. Dans la Place de marché Microsoft Azure, l’image contient la bonne clé d’installation client KMS déjà configurée. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>L’activation de Windows fonctionne-t-elle de la même façon, peu importe si la machine virtuelle utilise ou non Azure Hybrid Use Benefit (HUB) ? 
 
Oui. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>Que se passe-t-il en cas d’expiration de la période d’activation de Windows ? 
 
Lorsque la période de grâce a expiré et si Windows n’est pas encore activé, Windows Server 2008 R2 et versions ultérieures de Windows affichera des notifications supplémentaires sur l’activation. Le fond d’écran du Bureau reste noir, et Windows Update installera uniquement les mises à jour essentielles et de sécurité. Les mises à jour facultatives ne seront pas installées par Windows Update. Consultez la section Notifications au bas de la page [Licensing Conditions](http://technet.microsoft.com/en-us/library/ff793403.aspx) (Conditions d’obtention de licence).   

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contactez le support technique.
Si vous avez besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.
