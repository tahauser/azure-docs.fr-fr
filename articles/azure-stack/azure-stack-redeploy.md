---
title: "Redéployer Azure Stack | Microsoft Docs"
description: "Redéployer Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>Redéployer Azure Stack
Si vous recevez une erreur durant le déploiement d’Azure Stack, vous pouvez exécuter de nouveau la configuration à l’aide de la commande PowerShell suivante : `.\InstallAzureStackpoc.ps1 -rerun`. Cette commande redémarre Azure Stack au moment de la dernière défaillance, non depuis le début. Si vous recevez à nouveau la même erreur de configuration, il peut s’avérer nécessaire d’effectuer un redéploiement complet. 

Pour redéployer Azure Stack, vous devez recommencer à partir de zéro comme décrit ci-dessous.

## <a name="steps-to-redeploy-azure-stack"></a>Procédure de redéploiement d’Azure Stack
1. Sur l’hôte du kit de développement, ouvrez une console PowerShell avec élévation de privilèges > accédez au script asdk-installer.ps1 > exécutez-le > cliquez sur **Redémarrer**.
2. Sélectionnez le système d’exploitation de base (et non pas **Azure Stack**), puis cliquez sur **Suivant**.
3. Après le redémarrage de l’hôte du kit de développement, supprimez le fichier CloudBuilder.vhdx qui a été utilisé lors du déploiement précédent.
4. [Déployer le kit de développement](azure-stack-run-powershell-script.md).

## <a name="next-steps"></a>Étapes suivantes
[Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)

