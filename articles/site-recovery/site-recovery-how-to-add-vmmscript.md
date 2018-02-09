---
title: "Comment ajouter des scripts au plan de récupération dans Azure Site Recovery | Microsoft Docs"
description: "Décrit les conditions préalables à l’ajout d’un nouveau script à un plan de récupération dans VMM vers Azure"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>Ajouter des scripts VMM à un plan de récupération


Cet article fournit des informations sur la création et l’ajout de scripts VMM de plans de récupération dans [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>Conditions préalables à l’ajout d’un script au plan de récupération

Vous pouvez utiliser des scripts PowerShell dans vos plans de récupération. Vous devez les créer et les placer dans la bibliothèque VMM accessible à partir du plan de récupération. Voici quelques considérations lors de l’écriture du script.

* Veillez à utiliser des blocs try-catch dans vos scripts, ceci pour garantir le traitement approprié des exceptions.
    - Si le script comporte une exception, son exécution s’interrompt et la tâche est mise en échec.
    - Si une erreur se produit, aucune des portions restantes du script ne s’exécute.
    - Si une erreur se produit lorsque vous exécutez un basculement non planifié, le plan de récupération se poursuit.
    - Si une erreur se produit lorsque vous exécutez un basculement planifié, le plan de récupération s’arrête. Vous devez corriger le script, vérifier sa bonne exécution, puis exécuter de nouveau le plan de récupération.
        - La commande Write-Host, qui ne fonctionne pas dans un script de plan de récupération, entraîne la mise en échec du script. Pour créer une sortie, générez un script de proxy exécutant votre script principal. Assurez-vous que l’ensemble des sorties sont extraites à l’aide de la commande >>.
        - Le script expire si aucune sortie n’est produite dans les 600 secondes.
        - Si des éléments sont écrits sur STDERR, le script est classé comme mis en échec. Ces informations s’affichent dans les détails d’exécution du script.

* Les scripts d’un plan de récupération s’exécutent dans le contexte d’un compte de service VMM. Assurez-vous que ce compte dispose des autorisations en lecture pour le partage distant sur lequel se trouve le script. Testez l’exécution du script au niveau de privilège du compte de service VMM.
* Les applets de commande VMM sont fournies dans un module Windows PowerShell. Le module est installé lors du montage de la console VMM. Il peut être chargé dans votre script à l’aide de la commande de script suivante :
   - Import-Module -Name virtualmachinemanager. [Plus d’informations](https://technet.microsoft.com/library/hh875013.aspx)
* Vérifiez que vous disposez d’au moins un serveur de bibliothèque au sein de votre déploiement VMM. Par défaut, le chemin d’accès de partage de bibliothèque d’un serveur VMM est disponible localement sur le serveur VMM, sous le nom de dossier MSCVMMLibrary.
    * Si votre chemin d’accès de partage de bibliothèque est distant (ou local mais non partagé avec MSCVMMLibrary), configurez le partage comme suit (en utilisant \\libserver2.contoso.com\share\ comme exemple) :
      * Ouvrez l’Éditeur du Registre, puis accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Modifiez la valeur de **ScriptLibraryPath** et placez-la comme \\libserver2.contoso.com\share\. Spécifiez le nom de domaine complet. Fournissez des autorisations d’accès à l’emplacement de partage. Notez qu’il s’agit du nœud racine du partage. **Pour vérifier ce point, vous pouvez parcourir la bibliothèque au niveau du nœud racine dans VMM. Le chemin d’accès qui s’ouvre correspond à la racine du chemin d’accès, celui que vous devez utiliser dans la variable**.
      * Veillez à tester le script avec un compte d’utilisateur qui dispose des mêmes autorisations que le compte de service VMM. Cela permet de vérifier que les scripts testés autonomes s’exécuteront de la même manière dans les plans de récupération. Sur le serveur VMM, définissez le mode de contournement suivant de la stratégie d’exécution :
        * Ouvrez la console **Windows PowerShell 64 bits** à l’aide des privilèges élevés.
        * Entrez : **Set-executionpolicy bypass**. [Plus d’informations](https://technet.microsoft.com/library/ee176961.aspx)

> [!IMPORTANT]
> Vous devez définir la stratégie d’exécution sur Contournement uniquement dans les consoles PowerShell 64 bits. Si vous l’avez définie dans la console PowerShell 32 bits, les scripts ne s’exécuteront pas.

## <a name="add-the-script-to-the-vmm-library"></a>Ajouter le script à la bibliothèque VMM

Si vous possédez un site source VMM, libre à vous de créer un script sur le serveur VMM, et de l’inclure dans votre plan de récupération.

1. Créez un dossier dans le partage de bibliothèque. Par exemple, \<VMMServerName>\MSSCVMMLibrary\RPScripts. Placez-le dans les serveurs VMM source et cibles.
2. Créez le script (par exemple RPScript), et vérifiez son bon fonctionnement.
3. Placez le script à l’emplacement \<VMMServerName>\MSSCVMMLibrary sur les serveurs VMM source et cible.

## <a name="add-the-script-to-a-recovery-plan"></a>Ajouter le script à un plan de récupération

Vous pouvez ajouter un script au groupe après y avoir ajouté des machines virtuelles ou des groupes de réplication et après avoir créé le plan.

1. Ouvrez le plan de récupération.
2. Cliquez sur un élément de la liste **Étape,** puis cliquez sur **Script** ou sur **Action manuelle**.
3. Indiquez si vous souhaitez ajouter le script ou l’action avant ou après l’élément sélectionné. Utilisez les boutons de **Déplacement vers le haut** et de **Déplacement vers le bas** pour faire monter ou descendre le script.
4. Si vous ajoutez un script VMM, sélectionnez **Basculement vers script VMM**. Dans **Chemin d’accès du script**, tapez le chemin d’accès relatif au partage. Dans l’exemple VMM ci-dessous, spécifiez le chemin d’accès : **\RPScripts\RPScript.PS1**.
5. Si vous ajoutez un Runbook Azure Automation, spécifiez le compte Azure Automation dans lequel se trouve le runbook, puis sélectionnez le script runbook Azure approprié.
6. Exécutez un test de basculement du plan de récupération, afin de vous assurer du bon fonctionnement du script.


## <a name="next-steps"></a>étapes suivantes

[En savoir plus](site-recovery-failover.md) sur l’exécution des basculements.
