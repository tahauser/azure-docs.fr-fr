---
title: "Résoudre les problèmes d’extension de machine virtuelle Azure Log Analytics | Microsoft Docs"
description: "Décrit les symptômes, les causes et les solutions aux problèmes courants liés à l’extension de machine virtuelle Log Analytics pour les machines virtuelles Azure Windows et Linux."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: d1e70d8f9fb929e3877c88fd4c1169a0c76ac2a6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Dépannage de l’extension de machine virtuelle Log Analytics
Cet article présente les problèmes qui peuvent survenir avec l’extension de machine virtuelle Log Analytics sur les machines virtuelles Windows et Linux en cours d’exécution sur Microsoft Azure, puis propose des solutions possibles pour les résoudre.

Pour vérifier l’état de l’extension, effectuez les étapes suivantes à partir du portail Azure.

1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste des ressources, tapez **machines virtuelles**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Machines virtuelles**.
3. Dans la liste de vos machines virtuelles, recherchez et sélectionnez-la.
3. Sur la machine virtuelle, cliquez sur **Extensions**.
4. Dans la liste, vérifiez si l’extension Log Analytics est activée.  Pour Linux, l’agent apparaît sous le nom **OMSAgentforLinux**. Pour Windows, il apparaît sous le nom **MicrosoftMonitoringAgent**.

   ![Vue Extension de machine virtuelle](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Cliquez sur l’extension pour afficher les détails. 

   ![Détails de l’extension de machine virtuelle](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Dépannage de l’extension de machine virtuelle Windows dans Azure

Si l’extension de machine virtuelle *Microsoft Monitoring Agent* ne s’installe pas ou ne génère pas de rapports, vous pouvez effectuer les étapes suivantes pour résoudre le problème.

1. Vérifiez si l’agent de machine virtuelle Azure est installé et fonctionne correctement en suivant la procédure décrite dans l’article [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Vous pouvez également consulter le fichier journal de l’agent de machine virtuelle `C:\WindowsAzure\logs\WaAppAgent.log`.
   * Si le journal n’existe pas, l’agent de machine virtuelle n’est pas installé.
   * [Installer l’agent de machine virtuelle Azure](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Vérifiez que la tâche de vérification des pulsations de l’extension Microsoft Monitoring Agent est en cours d’exécution en procédant comme suit :
   * Connectez-vous à la machine virtuelle.
   * Ouvrez le Planificateur de tâches et recherchez la tâche `update_azureoperationalinsight_agent_heartbeat`.
   * Vérifiez que la tâche est activée et exécutée toutes les minutes.
   * Consultez le fichier journal des pulsations dans `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`.
3. Examinez les fichiers de journaux d’extension de machine virtuelle Microsoft Monitoring Agent dans `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`.
4. Vérifiez que la machine virtuelle peut exécuter des scripts PowerShell.
5. Vérifiez que les autorisations sur C:\Windows\temp n’ont pas été modifiées.
6. Affichez l’état de Microsoft Monitoring Agent en tapant ce qui suit dans une fenêtre PowerShell avec des privilèges élevés sur la machine virtuelle `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`.
7. Examinez les fichiers journaux d’installation de Microsoft Monitoring Agent dans `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`.

Pour plus d’informations, consultez [Résolution des problèmes des extensions Windows](../virtual-machines/windows/extensions-oms.md).

## <a name="troubleshooting-linux-vm-extension"></a>Dépannage de l’extension de machine virtuelle Linux
Si l’extension de machine virtuelle *Agent OMS pour Linux* ne s’installe pas ou ne génère pas de rapports, vous pouvez effectuer les étapes suivantes pour résoudre le problème.

1. Si l’état de l’extension est *Inconnu*, vérifiez si l’agent de machine virtuelle Azure est installé et fonctionne correctement en examinant le fichier journal de l’agent de machine virtuelle `/var/log/waagent.log`
   * Si le journal n’existe pas, l’agent de machine virtuelle n’est pas installé.
   * [Installez l’agent de machine virtuelle Azure sur les machines virtuelles Linux](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).
2. Pour d’autres états défectueux, examinez les fichiers journaux de l’extension de machine virtuelle Agent OMS pour Linux dans `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` et `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`.
3. Si l’état de l’extension est intègre, mais que les données ne sont pas chargées, examinez les fichiers journaux de l’Agent OMS pour Linux dans `/var/opt/microsoft/omsagent/log/omsagent.log`.

Pour plus d’informations, consultez [Résolution des problèmes des extensions Linux](../virtual-machines/linux/extensions-oms.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres conseils de dépannage de l’Agent OMS pour Linux hébergé sur des ordinateurs en dehors d’Azure, consultez [Dépanner l’agent Linux Azure Log Analytics](log-analytics-agent-linux-support.md).  
