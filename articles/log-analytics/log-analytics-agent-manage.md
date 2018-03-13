---
title: "Gestion de l’agent Azure Log Analytics | Microsoft Docs"
description: "Cet article décrit les différentes tâches de gestion à effectuer en règle générale pendant le cycle de vie de Microsoft Monitoring Agent (MMA) déployé sur une machine."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: magoedte
ms.openlocfilehash: 2e4daebf18d5edeba92bc14d5a4f699fbd2d94ce
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Gestion et maintenance de l’agent Log Analytics sous Windows et Linux

Après le déploiement initial de l’agent Log Analytics sous Windows ou Linux, il se peut que vous deviez le reconfigurer ou le supprimer de la machine s’il a atteint la phase de mise hors service dans son cycle de vie.  Vous pouvez facilement effectuer ces tâches de maintenance de routine manuellement ou automatiquement ce qui réduit les erreurs opérationnelles et les coûts.

## <a name="adding-or-removing-a-workspace"></a>Ajout ou suppression d’un espace de travail 

### <a name="windows-agent"></a>Agent Windows

#### <a name="update-settings-from-control-panel"></a>Mettre à jour les paramètres dans le Panneau de configuration

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.
2. Ouvrez le **Panneau de configuration**.
3. Sélectionnez **Microsoft Monitoring Agent** puis cliquez sur l’onglet **Azure Log Analytics (OMS)**.
4. Pour supprimer un espace de travail, sélectionnez-le puis cliquez sur **Supprimer**. Répétez cette étape pour le ou les autres espaces de travail auxquels l’agent ne doit plus communiquer d’informations.
5. Pour ajouter un espace de travail, cliquez sur **Ajouter**, puis dans la boîte de dialogue **Ajouter un espace de travail Log Analytics**, collez l’ID et la clé de l’espace de travail (clé primaire). Si la machine doit communiquer avec un espace de travail Log Analytics dans le cloud Azure Government, sélectionnez Azure - Gouvernement des États-Unis dans la liste déroulante Cloud Azure. 
6. Cliquez sur **OK** pour enregistrer vos modifications.

#### <a name="remove-a-workspace-using-powershell"></a>Supprimer un espace de travail à l’aide de PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Ajouter un espace de travail dans Azure commercial à l’aide de PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Ajouter un espace de travail dans Azure pour US Government à l’aide de PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Si vous avez utilisé la ligne de commande ou un script pour installer ou configurer l’agent, `EnableAzureOperationalInsights` a été remplacé par `AddCloudWorkspace` et `RemoveCloudWorkspace`.
>

## <a name="update-proxy-settings"></a>Mettre à jour les paramètres de proxy 
Pour permettre à l’agent de communiquer avec le service via un serveur proxy ou [Passerelle OMS](log-analytics-oms-gateway.md) après le déploiement, utilisez l’une des méthodes suivantes.

### <a name="windows-agent"></a>Agent Windows

#### <a name="update-settings-using-control-panel"></a>Mettre à jour les paramètres dans le Panneau de configuration

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.
2. Ouvrez le **Panneau de configuration**.
3. Sélectionnez **Microsoft Monitoring Agent** puis cliquez sur l’onglet **Paramètres proxy**.
4. Cliquez sur **Utiliser un serveur proxy** et indiquez l’URL et le numéro de port du serveur proxy ou de la passerelle. Si votre serveur proxy ou Passerelle OMS requiert une authentification, tapez le nom d’utilisateur et un mot de passe pour vous authentifier, puis cliquez sur **OK**. 

#### <a name="update-settings-using-powershell"></a>Mettre à jour les paramètres à l’aide de PowerShell 

Copiez l’exemple de code PowerShell suivant, mettez-le à jour avec les informations propres à votre environnement et enregistrez-le avec une extension de fichier PS1.  Exécutez le script sur chaque machine qui se connecte directement au service Log Analytics.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object.  We need to determine if we
#have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
     Write-Output 'Health Service proxy API not present, will not update settings.'
     return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```  

### <a name="linux-agent"></a>Agent Linux
Effectuez les opérations suivantes si vos ordinateurs Linux doivent communiquer avec Log Analytics via un serveur proxy ou Passerelle OMS.  La valeur de configuration de proxy a la syntaxe suivante `[protocol://][user:password@]proxyhost[:port]`.  La propriété *proxyhost* accepte un nom de domaine complet ou l’adresse IP du serveur proxy.

1. Modifiez le fichier `/etc/opt/microsoft/omsagent/proxy.conf` en exécutant les commandes suivantes et modifiez les valeurs en vous basant sur vos paramètres spécifiques.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Redémarrez l’agent en exécutant la commande suivante : 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>Désinstaller l’agent
Utilisez l’une des procédures suivantes pour désinstaller l’agent Windows ou Linux à l’aide de la ligne de commande ou de l’assistant d’installation.

### <a name="windows-agent"></a>Agent Windows

#### <a name="uninstall-from-control-panel"></a>Désinstaller à partir du Panneau de configuration
1. Connectez-vous à la machine avec un compte disposant des droits d’administration.  
2. Dans le **Panneau de configuration**, cliquez sur **Programmes et fonctionnalités**.
3. Dans **Programmes et fonctionnalités**, sélectionnez **Microsoft Monitoring Agent**, cliquez sur **Désinstaller** puis sur **Oui**.

>[!NOTE]
>Pour exécuter l’Assistant d’installation de l’agent, vous pouvez aussi double-cliquer sur le fichier **MMASetup-<platform>.exe**, disponible en téléchargement à partir d’un espace de travail dans le portail Azure.

#### <a name="uninstall-from-the-command-line"></a>Désinstaller à partir de la ligne de commande
Le fichier téléchargé de l’agent est un package d’installation autonome créé avec IExpress.  Le programme d’installation de l’agent et les fichiers de prise en charge sont contenus dans le package et doivent être extraits pour effectuer une désinstallation correcte à l’aide de la ligne de commande indiquée dans l’exemple suivant. 

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.  
2. Pour extraire les fichiers d’installation de l’agent, à partir d’une invite de commandes avec élévation de privilèges, exécutez `extract MMASetup-<platform>.exe` et indiquez l’emplacement où extraire les fichiers.  L’autre possibilité consiste à spécifier le chemin d’accès à l’aide des arguments `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Pour plus d’informations sur les commutateurs de ligne de commande pris en charge par IExpress, consultez [Commutateurs de ligne de commande pour IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) puis mettez à jour l’exemple en fonction de vos besoins.
3. À l’invite, tapez `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Agent Linux
Pour supprimer l’agent, exécutez la commande suivante sur l’ordinateur Linux.  L’argument *--purge* supprime complètement l’agent et sa configuration.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configurer l’agent pour qu’il communique avec un groupe d’administration Operations Manager

### <a name="windows-agent"></a>Agent Windows
Procédez comme suit pour que l’Agent OMS pour Windows communique avec un groupe d’administration System Center Operations Manager. 

1. Connectez-vous à la machine avec un compte disposant des droits d’administration.
2. Ouvrez le **Panneau de configuration**. 
3. Cliquez sur **Microsoft Monitoring Agent** puis sur l’onglet **Operations Manager**.
4. Si vos serveurs Operations Manager sont intégrés à Active Directory, cliquez sur **Met à jour automatiquement les attributions du groupe d’administration à partir d’AD DS**.
5. Cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Ajouter un groupe d’administration**.
6. Dans la zone **Nom du groupe d’administration**, entrez le nom de votre groupe d’administration.
7. Dans le champ **Serveur d’administration principal**, entrez le nom d’ordinateur du serveur d’administration principal.
8. Dans le champ **Port du serveur d’administration**, entrez le numéro du port TCP.
9. Sous **Compte d’action d’agent**, choisissez le compte système local ou un compte de domaine local.
10. Cliquez sur **OK** pour fermer la boîte de dialogue **Ajouter un groupe d’administration**, puis cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de l’agent Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Agent Linux
Procédez comme suit pour configurer l’Agent OMS pour Linux pour envoyer des rapports à un groupe d’administration System Center Operations Manager. 

1. Modifiez le fichier `/etc/opt/omi/conf/omiserver.conf`
2. Vérifiez que la ligne commençant par `httpsport=` spécifie le port 1270. Par exemple : `httpsport=1270`
3. Redémarrez le serveur OMI : `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Étapes suivantes

Consultez [Résolution des problèmes de l’agent Linux](log-analytics-agent-linux-support.md) si vous rencontrez des problèmes lors de l’installation ou de gestion de l’agent.  