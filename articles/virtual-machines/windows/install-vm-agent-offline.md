---
title: "Installer l’agent de machine virtuelle Azure en mode hors connexion | Microsoft Docs"
description: "Découvrez comment installer l’agent de machine virtuelle Azure en mode hors connexion."
services: virtual-machines-windows
documentationcenter: 
author: genlin
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: genli
ms.openlocfilehash: 3770cc3338c89a9bf88e2cf9ec3faa37e2ff109b
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Installer l’agent de machine virtuelle Azure en mode hors connexion 

L’agent de machine virtuelle Azure fournit des fonctionnalités utiles, telles que la réinitialisation de mot de passe d’administrateur local et l’exécution d’un push de script. Cet article explique comment installer l’agent de machine virtuelle pour une machine virtuelle Windows hors connexion. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>À quel moment utiliser l’agent de machine virtuelle en mode hors connexion

Installez l’agent de machine virtuelle en mode hors connexion dans les scénarios suivants :

- La machine virtuelle Azure déployée n’a pas l’agent de machine virtuelle installé ou l’agent ne fonctionne pas.
- Vous avez oublié le mot de passe administrateur pour la machine virtuelle, ou vous ne pouvez pas accéder à la machine virtuelle.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Comment installer l’agent de machine virtuelle en mode hors connexion

Procédez comme suit pour installer l’agent de machine virtuelle en mode hors connexion.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Étape 1 : attacher le disque du système d’exploitation de la machine virtuelle à une autre machine virtuelle en tant que disque de données

1.  Supprimez la machine virtuelle. Assurez-vous de sélectionner l’option **Conserver les disques** lorsque vous supprimez la machine virtuelle.

2.  Attachez le disque du système d’exploitation en tant que disque de données à une autre machine virtuelle (appelée _machine virtuelle de dépannage_). Pour plus d’informations, consultez [Attachement d’un disque de données à une machine virtuelle Windows dans le portail Azure](attach-managed-disk-portal.md).

3.  Connectez-vous à la machine virtuelle de dépannage. Ouvrez **Gestion de l’ordinateur** > **Gestion des disques**. Vérifiez que le disque du système d’exploitation est en ligne et que les lettres de lecteur sont attribuées aux partitions de disque.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Étape 2 : modifier le disque du système d’exploitation pour installer l’agent de machine virtuelle Azure

1.  Effectuez une connexion Bureau à distance à la machine virtuelle de dépannage.

2.  Sur le disque du système d’exploitation que vous avez attaché, accédez au dossier \windows\system32\config. Copiez tous les fichiers dans ce dossier de sauvegarde, au cas où une restauration serait requise.

3.  Démarrez **l’Éditeur du Registre** (regedit.exe).

4.  Sélectionnez la clé **HKEY_LOCAL_MACHINE**. Dans le menu, sélectionnez **Fichier** > **Charger la ruche** :

    ![Charger la ruche](./media/install-vm-agent-offline/load-hive.png)

5.  Sur le disque du système d’exploitation que vous avez attaché, accédez au dossier \windows\system32\config\SYSTEM. Pour le nom de la ruche, entrez **BROKENSYSTEM**. La nouvelle ruche du Registre est affichée sous la clé **HKEY_LOCAL_MACHINE**.

6.  Sur le disque du système d’exploitation que vous avez attaché, accédez au dossier \windows\system32\config\SOFTWARE. Pour le nom du logiciel de la ruche, entrez **BROKENSOFTWARE**.

7.  Si l’agent de machine virtuelle ne fonctionne pas, sauvegardez la configuration actuelle.

    >[!NOTE]
    >Si la machine virtuelle n’a pas l’agent installé, passez à l’étape 8. 
      
    1. Renommez le dossier \windowsazure en \windowsazure.old.

    2. Exportez les registres suivants :
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Utilisez les fichiers existants de la machine virtuelle de dépannage comme référentiel pour l’installation de l’agent de machine virtuelle. Effectuez ensuite les tâches suivantes :

    1. À partir de la machine virtuelle de dépannage, exportez les sous-clés suivantes au format du Registre (.reg) : 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

        ![Exporter les sous-clés du Registre](./media/install-vm-agent-offline/backup-reg.png)

    2. Modifiez les fichiers du Registre. Dans chaque fichier, changez la valeur d’entrée **SYSTEM** en **BROKENSYSTEM** (comme indiqué dans les images suivantes) et enregistrez le fichier.

        ![Modifier les valeurs de sous-clés du Registre](./media/install-vm-agent-offline/change-reg.png)

    3. Importez les fichiers du Registre dans le référentiel en double-cliquant sur chaque fichier du Registre.

    4. Vérifiez que les trois sous-clés suivantes sont importées avec succès dans la ruche **BROKENSYSTEM** :
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

9.  Copiez le dossier de l’agent de machine virtuelle C:\windowsazure\packages sur le &lt;disque du système d’exploitation que vous avez attaché&gt; : \windowsazure\packages.

    ![Copier les fichiers de l’agent de machine virtuelle sur le disque du système d’exploitation](./media/install-vm-agent-offline/copy-package.png)
      
    >[!NOTE]
    >Ne copiez pas le **dossier des journaux**. Une fois le service démarré, les nouveaux journaux sont générés.

10.  Sélectionnez **BROKENSYSTEM**. Dans le menu, sélectionnez **Fichier** > **Décharger la ruche**.

11.  Sélectionnez **BROKENSOFTWARE**. Dans le menu, sélectionnez **Fichier** > **Décharger la ruche**.

12.  Détachez le disque du système d’exploitation, puis recréez la machine virtuelle à l’aide du disque du système d’exploitation.

13.  Accédez à la machine virtuelle. Notez que le RdAgent est en cours d’exécution et que les journaux sont générés.

Si vous avez créé la machine virtuelle à l’aide du modèle de déploiement classique, vous avez terminé.


### <a name="use-the-provisionguestagent-property-for-vms-created-with-azure-resource-manager"></a>Utiliser la propriété ProvisionGuestAgent pour les machines virtuelles créées avec Azure Resource Manager

Si vous avez créé la machine virtuelle à l’aide du modèle de déploiement Azure Resource Manager, utilisez le module Azure PowerShell pour mettre à jour la propriété **ProvisionGuestAgent**. La propriété informe Azure que la machine virtuelle a l’agent de machine virtuelle installé.

Pour définir la propriété **ProvisionGuestAgent**, exécutez les commandes suivantes dans Azure PowerShell :

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Exécutez ensuite la commande `Get-AzureVM` . Notez que la propriété **GuestAgentStatus** est maintenant remplie avec des données :

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’agent de machine virtuelle Azure](agent-user-guide.md)
- [Extensions et fonctionnalités de machine virtuelle pour Windows](extensions-features.md)
