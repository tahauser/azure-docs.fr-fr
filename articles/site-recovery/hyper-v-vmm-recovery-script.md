---
title: "Ajouter un script au plan de récupération dans Azure Site Recovery | Microsoft Docs"
description: "Découvrez les prérequis pour l’ajout d’un nouveau script VMM (System Center Virtual Machine Manager) à un plan de récupération dans Azure."
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
ms.openlocfilehash: 2e00f812fb35ac9a0cb390fc6a3ba40a8678f8dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Ajouter un script VMM à un plan de récupération

Cet article explique comment créer un script VMM (System Center Virtual Machine Manager) et l’ajouter à un plan de récupération dans [Azure Site Recovery](site-recovery-overview.md).

Postez des commentaires ou des questions au bas de cet article ou sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Prérequis

Vous pouvez utiliser des scripts PowerShell dans vos plans de récupération. Pour qu’il soit accessible depuis le plan de récupération, vous devez créer le script et le placer dans la bibliothèque VMM. Gardez les points suivants à l’esprit lors de l’écriture du script :

* Veillez à utiliser des blocs try-catch dans vos scripts, ceci pour garantir le traitement approprié des exceptions.
    - Si une exception survient dans le script, son exécution s’interrompt et la tâche est mise en échec.
    - Si une erreur se produit, le reste du script ne s’exécute pas.
    - Si une erreur se produit lorsque vous exécutez un basculement non planifié, le plan de récupération se poursuit.
    - Si une erreur se produit lorsque vous exécutez un basculement planifié, le plan de récupération s’arrête. Corrigez le script, vérifiez sa bonne exécution, puis exécutez de nouveau le plan de récupération.
        - La commande `Write-Host` ne fonctionne pas dans un script de plan de récupération. Si vous utilisez la commande `Write-Host` dans un script, il échoue. Pour créer une sortie, générez un script de proxy exécutant votre script principal. Pour vérifier que l’ensemble des sorties sont extraites, utilisez la commande **\>\>**.
        - Le script expire si aucune sortie n’est produite dans les 600 secondes.
        - Si des éléments sont écrits sur STDERR, le script est classé comme mis en échec. Ces informations s’affichent dans les détails d’exécution du script.

* Les scripts d’un plan de récupération s’exécutent dans le contexte d’un compte de service VMM. Assurez-vous que ce compte dispose des autorisations en lecture pour le partage distant sur lequel se trouve le script. Testez le script à exécuter avec le même niveau de droits d’utilisateur que le compte de service VMM.
* Les applets de commande VMM sont fournies dans un module Windows PowerShell. Le module est installé lors du montage de la console VMM. Pour charger le module dans votre script, utilisez la commande suivante dans le script : 

    `Import-Module -Name virtualmachinemanager`

    Pour plus d’informations, consultez [Bien démarrer avec Windows PowerShell et VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Vérifiez que vous disposez d’au moins un serveur de bibliothèque au sein de votre déploiement VMM. Par défaut, le chemin de partage de bibliothèque d’un serveur VMM est disponible localement sur le serveur VMM. Le nom du dossier est MSCVMMLibrary.

  Si votre chemin de partage de bibliothèque est distant (ou local mais non partagé avec MSCVMMLibrary), configurez le partage comme suit, en utilisant \\libserver2.contoso.com\share\ comme exemple :
  
  1. Ouvrez l’Éditeur du Registre, puis accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  2. Remplacez la valeur de **ScriptLibraryPath** par **\\\libserver2.contoso.com\share\\**. Spécifiez le nom de domaine complet. Fournissez des autorisations d’accès à l’emplacement de partage. Il s’agit du nœud racine du partage. Pour rechercher le nœud racine, dans VMM, accédez au nœud racine dans la bibliothèque. Le chemin qui s’ouvre est la racine du chemin. C’est le chemin que vous devez utiliser dans la variable.

  3. Testez le script en utilisant un compte d’utilisateur qui a le même niveau de droits d’utilisateur que le compte de service VMM. Le fait d’utiliser ces droits d’utilisateur confirme que les scripts testés et autonomes s’exécutent de la même façon que dans les plans de récupération. Sur le serveur VMM, définissez le mode de contournement suivant de la stratégie d’exécution :

     a. Ouvrez la console **Windows PowerShell 64 bits** en tant qu’administrateur.
     
     b. Entrez **Set-executionpolicy bypass**. Pour plus d’informations, consultez [Utilisation de l’applet de commande Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Définissez **Set-executionpolicy bypass** uniquement dans la console PowerShell 64 bits. Si vous la définissez pour la console PowerShell de 32 bits, les scripts ne s’exécutent pas.

## <a name="add-the-script-to-the-vmm-library"></a>Ajouter le script à la bibliothèque VMM

Si vous avez un site source VMM, libre à vous de créer un script sur le serveur VMM. Il vous suffit ensuite d’inclure ce script dans votre plan de récupération.

1. Dans le partage de bibliothèque, créez un dossier. Par exemple, \<Nom du serveur VMM> \MSSCVMMLibrary\RPScripts. Placez le dossier sur les serveurs VMM source et cible.
2. Créez le script. Par exemple, nommez le script RPScript. Vérifiez le bon fonctionnement du script.
3. Placez le script dans le dossier \<Nom du serveur VMM>\MSSCVMMLibrary sur les serveurs VMM source et cible.

## <a name="add-the-script-to-a-recovery-plan"></a>Ajouter le script à un plan de récupération

Une fois les machines virtuelles ou les groupes de réplication ajoutés à un plan de récupération, et le plan créé, vous pouvez ajouter le script au groupe.

1. Ouvrez le plan de récupération.
2. Dans la liste **Étape**, sélectionnez un élément. Ensuite, sélectionnez **Script** ou **Action manuelle**.
3. Indiquez s’il faut ajouter le script ou l’action avant ou après l’élément sélectionné. Pour faire monter ou descendre le script dans la liste, utilisez les boutons **Monter** et **Descendre**.
4. Si vous ajoutez un script VMM, sélectionnez **Basculement vers script VMM**. Dans **Chemin du script**, entrez le chemin relatif du partage. Par exemple, entrez **\RPScripts\RPScript.PS1**.
5. Si vous ajoutez un runbook Azure Automation, spécifiez le compte Automation dans lequel se trouve le runbook. Ensuite, sélectionnez le script de runbook Azure que vous souhaitez utiliser.
6. Afin de vous assurer du bon fonctionnement du script, effectuez un test de basculement du plan de récupération.


## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [l’exécution des basculements](site-recovery-failover.md).

