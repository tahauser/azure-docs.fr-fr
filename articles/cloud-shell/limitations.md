---
title: "Limitations d’Azure Cloud Shell | Microsoft Docs"
description: "Vue d’ensemble des limites d’Azure Cloud Shell"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: 08426b6142dd125a5981d65635ecc55336cb3d15
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitations d’Azure Cloud Shell

Azure Cloud Shell a les limitations connues suivantes :

## <a name="general-limitations"></a>Limitations générales

### <a name="system-state-and-persistence"></a>État du système et persistance

La machine qui fournit votre session Cloud Shell est temporaire. En effet, elle est recyclée lorsque votre session reste inactive pendant 20 minutes. Cloud Shell requiert qu’un partage de fichiers Azure soit monté. Par conséquent, votre abonnement doit être en mesure de configurer des ressources de stockage pour accéder à Cloud Shell. Autres éléments à prendre en compte :

* Avec le stockage monté, seules les modifications apportées à votre répertoire `clouddrive` sont conservées. Dans Bash, votre répertoire `$Home` est également conservé.
* Les partages de fichiers Azure peuvent être montés uniquement depuis votre [région affectée](persisting-shell-storage.md#mount-a-new-clouddrive).
  * Dans Bash, exécutez `env` pour rechercher votre région définie en tant que `ACC_LOCATION`.
* Azure Files prend uniquement en charge les comptes de stockage localement redondant et les comptes de stockage géoredondant.

### <a name="browser-support"></a>Prise en charge des navigateurs

Cloud Shell prend en charge les dernières versions de Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox et Apple Safari. Safari en mode privé n’est pas pris en charge.

### <a name="copy-and-paste"></a>Copier et coller

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Pour un utilisateur donné, un seul interpréteur de commandes peut être actif

Les utilisateurs peuvent lancer uniquement un seul type d’interpréteur de commandes à la fois, soit **Bash** soit **PowerShell**. Toutefois, plusieurs instances de Bash ou de PowerShell peuvent s’exécuter simultanément. Le fait de basculer de Bash à PowerShell entraîne le redémarrage de Cloud Shell, ce qui met fin aux sessions existantes.

### <a name="usage-limits"></a>Limites d’utilisation

Cloud Shell est destiné aux cas d’usage interactif. De fait, les sessions non interactives longues se terminent sans avertissement.

## <a name="bash-limitations"></a>Limites de Bash

### <a name="user-permissions"></a>Autorisations utilisateur

Les autorisations sont définies en tant qu’utilisateurs standards sans accès sudo. Les installations en dehors du répertoire `$Home` ne sont pas conservées.

### <a name="clouddrive-smb-limited-permissions"></a>Autorisations limitées SMB Clouddrive
Certaines commandes situées dans le répertoire `clouddrive` (par exemple, `git clone`) ne disposent pas des autorisations nécessaires pour lire/écrire certains fichiers. Si vous rencontrez ce problème, réessayez à partir de votre répertoire `$Home` qui n’a pas de limitations SMB.

### <a name="editing-bashrc"></a>Modifier .bashrc

Faites attention lorsque vous modifiez .bashrc : cela peut entraîner des erreurs inattendues dans Cloud Shell.

### <a name="bashhistory"></a>.bash_history

Votre historique de commandes de l’interpréteur de commandes risque d’être incohérent en raison d’interruptions de sessions Cloud Shell ou de sessions simultanées.

## <a name="powershell-limitations"></a>Limites de PowerShell

### <a name="slow-startup-time"></a>Temps de démarrage lent

L’initialisation de PowerShell dans Azure Cloud Shell (préversion) peut prendre jusqu’à 60 secondes.

### <a name="no-home-directory-persistence"></a>Aucune persistance du répertoire $Home

Les données écrites sur `$Home` par n’importe quelle application (telle que git, vim, etc.) ne sont pas conservées entre les sessions PowerShell. Consultez [ici](troubleshooting.md#powershell-resolutions) la solution de contournement.

### <a name="default-file-location-when-created-from-azure-drive"></a>Emplacement du fichier par défaut lors de sa création à partir du lecteur Azure :

Les utilisateurs ne peuvent pas créer de fichiers sous le lecteur Azure à l’aide des cmdlets PowerShell. Si les utilisateurs créent des fichiers à l’aide d’autres outils, tels que vim ou nano, les fichiers sont enregistrés dans le dossier C:\Users par défaut. 

### <a name="gui-applications-are-not-supported"></a>Les applications de l’interface graphique utilisateur ne sont pas prises en charge.

Si l’utilisateur exécute une commande susceptible de créer une boîte de dialogue Windows, comme `Connect-AzureAD` ou `Login-AzureRMAccount`, un message d’erreur apparaît tel que : `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="next-steps"></a>étapes suivantes

[Dépannage de Cloud Shell](troubleshooting.md) <br>
[Démarrage rapide pour Bash](quickstart.md) <br>
[Démarrage rapide pour PowerShell](quickstart-powershell.md)
