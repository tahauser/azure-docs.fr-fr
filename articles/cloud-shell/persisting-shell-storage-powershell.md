---
title: "Conserver des fichiers dans PowerShell dans Azure Cloud Shell (préversion) | Microsoft Docs"
description: "Procédure pas à pas de conservation des fichiers avec Azure Cloud Shell."
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: 66d0e20d670e49cdfe64614d1fc6f5739fde6155
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-powershell-in-azure-cloud-shell-preview-works"></a>Utilisation de PowerShell dans Azure Cloud Shell (préversion)
PowerShell dans Cloud Shell (préversion) conserve les fichiers à l’aide de la méthode suivante : 
* Montage du partage de fichiers spécifié en tant que `clouddrive` dans votre répertoire `$Home` pour l’interaction directe avec le partage de fichiers.

## <a name="list-cloud-drive-file-shares"></a>Répertorier les partages de fichiers du lecteur Cloud
La commande `Get-CloudDrive` récupère les informations du partage de fichier actuellement monté par le lecteur Cloud dans Cloud Shell. <br>
![Exécution de Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>Démonter le lecteur Cloud
Vous pouvez démonter un partage de fichiers monté sur Cloud Shell à tout moment. En cas de suppression du partage de fichiers, vous serez invité à créer et à monter un nouveau partage de fichiers lors de la prochaine session.

La commande `Dismount-CloudDrive` démonte un partage de fichiers à partir du compte de stockage actuel. Le démontage du lecteur Cloud met fin à la session active. L’utilisateur sera invité à créer et monter un nouveau partage de fichiers lors de la prochaine session.
![Exécution de Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide pour PowerShell](quickstart-powershell.md) <br>
[En savoir plus sur le stockage de fichiers Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[En savoir plus sur les balises de stockage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>