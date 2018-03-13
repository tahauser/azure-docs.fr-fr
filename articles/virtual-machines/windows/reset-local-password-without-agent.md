---
title: "Réinitialiser un mot de passe Windows local sans agent Azure | Microsoft Docs"
description: "Réinitialisation du mot de passe d’un compte d’utilisateur Windows local lorsque l’agent invité Azure n’est pas installé ou ne fonctionne pas sur une machine virtuelle"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: iainfou
ms.openlocfilehash: 2f9efdbaf0ae79781d6f9c7dfa4c8317185be79e
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Réinitialiser un mot de passe Windows local pour la machine virtuelle Azure hors connexion
Vous pouvez réinitialiser le mot de passe Windows local d’une machine virtuelle dans Azure à l’aide du [portail Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à condition que l’agent invité Azure soit installé. Cette méthode est le principal moyen de réinitialiser un mot de passe sur une machine virtuelle Azure. Si l’agent invité Azure ne répond pas ou ne parvient pas à s’installer après chargement d’une image personnalisée, vous pouvez réinitialiser manuellement un mot de passe Windows. Cet article explique comment réinitialiser un mot de passe de compte local en attachant le disque virtuel du système d’exploitation source à une autre machine virtuelle. Les étapes décrites dans cet article ne s’appliquent pas aux contrôleurs de domaine Windows. 

> [!WARNING]
> N’utilisez ce processus qu’en dernier recours. Essayez toujours de réinitialiser un mot de passe à l’aide du [portail Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en premier lieu.
> 
> 

## <a name="overview-of-the-process"></a>Vue d’ensemble du processus
La procédure de réinitialisation d’un mot de passe local pour une machine virtuelle Windows dans Azure lorsque l’agent invité Azure est inaccessible est la suivante :

* Supprimez la machine virtuelle source. Les disques virtuels sont conservés.
* Attachez le disque de système d’exploitation de la machine virtuelle source à une autre machine virtuelle au même emplacement au sein de votre abonnement Azure. Cette machine virtuelle est appelée machine virtuelle de dépannage.
* À l’aide de la machine virtuelle de dépannage, créez des fichiers de configuration sur le disque de système d’exploitation de la machine virtuelle source.
* Détachez le disque de système d’exploitation de la machine virtuelle source de la machine virtuelle de dépannage.
* Utilisez un modèle Resource Manager pour créer une machine virtuelle à l’aide du disque virtuel d’origine.
* Au démarrage de la nouvelle machine virtuelle, les fichiers de configuration que vous créez mettent à jour le mot de passe de l’utilisateur concerné.

## <a name="detailed-steps"></a>Procédure détaillée

> [!NOTE]
> Les étapes ne s’appliquent pas aux contrôleurs de domaine Windows. Elles fonctionnent uniquement sur un serveur autonome ou un serveur qui est membre d’un domaine.
> 
> 

Essayez toujours de réinitialiser un mot de passe à l’aide du [portail Azure ou Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avant d’effectuer la procédure suivante. Avant de commencer, vérifiez que vous disposez d’une sauvegarde de votre machine virtuelle. 

1. Supprimez la machine virtuelle affectée dans le portail Azure. La suppression de la machine virtuelle ne supprime que les métadonnées, la référence de la machine virtuelle dans Azure. Les disques virtuels sont conservés lors de la suppression de la machine virtuelle :
   
   * Sélectionnez la machine virtuelle dans le portail Azure et cliquez sur *Supprimer* :
     
     ![Supprimer une machine virtuelle existante](./media/reset-local-password-without-agent/delete_vm.png)
2. Attachez le disque de système d’exploitation de la machine virtuelle source à la machine virtuelle de dépannage. La machine virtuelle de dépannage doit être dans la même région que le disque de système d’exploitation de la machine virtuelle source (par exemple `West US`) :
   
   * Sélectionnez la machine virtuelle de dépannage dans le portail Azure. Cliquez sur *Disques* | *Attacher existant* :
     
     ![Attachement d’un disque existant](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Sélectionnez *Fichier VHD*, puis le compte de stockage qui contient votre machine virtuelle source :
     
     ![Sélectionner le compte de stockage](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Sélectionnez le conteneur source. En général, le conteneur source est *disques durs virtuels* :
     
     ![Sélectionner le conteneur de stockage](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Sélectionnez le disque dur virtuel de système d’exploitation à attacher. Cliquez sur *Sélectionner* pour terminer le processus :
     
     ![Sélectionner le disque virtuel source](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Connectez-vous à la machine virtuelle de dépannage à l’aide du Bureau à distance et vérifiez que le disque de système d’exploitation de la machine virtuelle source est visible :
   
   * Sélectionnez la machine virtuelle de dépannage dans le portail Azure et cliquez sur *Se connecter*.
   * Ouvrez le fichier RDP qui télécharge. Entrez le nom d’utilisateur et le mot de passe de la machine virtuelle de dépannage.
   * Dans l’Explorateur de fichiers, recherchez le disque de données que vous avez attaché. Si le disque dur virtuel de la machine virtuelle source est le seul disque de données attaché à la machine virtuelle de dépannage, il doit s’agir du lecteur F: :
     
     ![Afficher le disque de données attaché](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Créez `gpt.ini` dans `\Windows\System32\GroupPolicy` sur le lecteur de la machine virtuelle source (si le fichier gpt.ini existe, renommez-le gpt.ini.bak) :
   
   > [!WARNING]
   > Veillez à ne pas accidentellement créer les fichiers suivants dans C:\Windows, le lecteur de système d’exploitation de la machine virtuelle de dépannage. Créez les fichiers suivants dans le lecteur du système d’exploitation de votre machine virtuelle source qui est attaché en tant que disque de données.
   > 
   > 
   
   * Ajoutez les lignes suivantes au fichier `gpt.ini` que vous avez créé :
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Créer le fichier gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Créez `scripts.ini` dans `\Windows\System32\GroupPolicy\Machine\Scripts`. Vérifiez que les dossiers masqués sont affichés. Si nécessaire, créez les dossiers `Machine` ou `Scripts`.
   
   * Ajoutez les lignes suivantes au fichier `scripts.ini` que vous avez créé :
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Créer scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Créez `FixAzureVM.cmd` dans `\Windows\System32` avec le contenu suivant, en remplaçant `<username>` et `<newpassword>` par vos propres valeurs :
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Créer FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Vous devez respecter les exigences de complexité du mot de passe configuré pour votre machine virtuelle lors de la définition du nouveau mot de passe.
7. Dans le portail Azure, détachez le disque de la machine virtuelle de dépannage :
   
   * Sélectionnez la machine virtuelle de dépannage dans le portail Azure, puis cliquez sur *Disques*.
   * Sélectionnez le disque de données attaché à l’étape 2, puis cliquez sur *Détacher* :
     
     ![Détacher le disque](./media/reset-local-password-without-agent/detach_disk.png)
8. Avant de créer une machine virtuelle, obtenez l’URI vers le disque du système d’exploitation source :
   
   * Sélectionnez le compte de stockage dans le portail Azure, puis cliquez sur *Objets blob*.
   * Sélectionnez le conteneur. En général, le conteneur source est *disques durs virtuels* :
     
     ![Sélectionner l’objet blob de compte de stockage](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Sélectionnez le disque dur virtuel de système d’exploitation de votre machine virtuelle source et cliquez sur le bouton *Copier* en regard du nom de l’*URL* :
     
     ![Copier l’URI de disque](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Créez une machine virtuelle à partir du disque de système d’exploitation de la machine virtuelle source :
   
   * Utilisez [ce modèle Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) pour créer une machine virtuelle à partir d’un disque dur virtuel spécialisé. Cliquez sur le bouton `Deploy to Azure` pour ouvrir le portail Azure avec les informations préremplies.
   * Si vous souhaitez conserver tous les paramètres précédents pour la machine virtuelle, sélectionnez *Modifier le modèle* pour indiquer vos réseau virtuel, sous-réseau, carte réseau ou adresse IP publique existants.
   * Dans la zone de texte `OSDISKVHDURI`, collez l’URI de votre disque dur virtuel source obtenu à l’étape précédente :
     
     ![Créer une machine virtuelle à partir d’un modèle](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Une fois la nouvelle machine virtuelle exécutée, connectez-vous à elle à l’aide du Bureau à distance et du nouveau mot de passe que vous avez spécifié dans le script `FixAzureVM.cmd`.
11. Dans votre session à distance vers la nouvelle machine virtuelle, supprimez les fichiers suivants pour nettoyer l’environnement :
    
    * Dans %windir%\System32
      * supprimez FixAzureVM.cmd
    * Dans %windir%\System32\GroupPolicy\Machine\
      * supprimez scripts.ini
    * Dans %windir%\System32\GroupPolicy
      * supprimez gpt.ini (si le fichier gpt.ini existait déjà, et que vous l’avez renommé gpt.ini.bak, renommez à nouveau le fichier .bak « gpt.ini »)

## <a name="next-steps"></a>Étapes suivantes
Si vous ne parvenez toujours pas à vous connecter à l’aide du Bureau à distance, consultez le [guide de résolution des problèmes de connexion Bureau à distance](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Le [guide détaillé de résolution des problèmes de connexion Bureau à distance](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) décrit des méthodes de résolution des problèmes plutôt que des procédures spécifiques. Vous pouvez également [ouvrir une demande de support Azure](https://azure.microsoft.com/support/options/) pour obtenir une assistance pratique.

