---
title: Configurer Service Map dans Azure | Microsoft Docs
description: Service Map est une solution comprise dans Azure qui détecte automatiquement les composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Cet article fournit des informations sur le déploiement de Service Map dans votre environnement et son utilisation dans divers scénarios.
services: operations-management-suite
documentationcenter: ''
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: c01d18b17906a2b243a46241a6ec5c4b1d9ab8d9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="configure-service-map-in-azure"></a>Configurer Service Map dans Azure
La solution Service Map détecte automatiquement les composants d’application sur les systèmes Windows et Linux, et mappe la communication entre les services. Cette solution permet d’afficher les serveurs comme on se les représente, c’est-à-dire comme des systèmes interconnectés qui fournissent des services critiques. Service Map affiche les connexions entre les serveurs, les processus et les ports sur n’importe quelle architecture connectée par TCP, sans configuration requise autre que l’installation d’un agent.

Cet article décrit en détail la configuration de Carte de service et de l’intégration des agents. Pour plus d’informations sur l’utilisation de Service Map, consultez [Utiliser la solution Service Map dans Azure](operations-management-suite-service-map.md).

## <a name="dependency-agent-downloads"></a>Téléchargements de l’agent de dépendances
| Fichier | SE | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.0 | 4125A88E60650FF168D6254AB4FCD14CDD3CC1C7B4CF168F3F5F3C1AF30895DD  |


## <a name="connected-sources"></a>Sources connectées
Carte de service obtient ses données à partir de l’agent de dépendances Microsoft. Celui-ci dépend de l’agent OMS pour ses connexions à Log Analytics. Cela signifie qu’un serveur doit disposer de l’agent OMS installé et configuré avant que l’agent de dépendances puisse être installé. Le tableau suivant décrit les sources connectées prises en charge par la solution Service Map.

| Source connectée | Prise en charge | Description |
|:--|:--|:--|
| Agents Windows | OUI | Carte de service analyse et collecte des données à partir des ordinateurs agents Windows. <br><br>Outre [l’agent OMS](../log-analytics/log-analytics-windows-agent.md), les agents Windows nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, consultez la page [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Agents Linux | OUI | Carte de service analyse et collecte des données à partir des ordinateurs agents Linux. <br><br>Outre [l’agent OMS](../log-analytics/log-analytics-linux-agents.md), les agents Linux nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, consultez la page [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Groupe d’administration Microsoft System Center Operations Manager | OUI | Service Map analyse et collecte des données provenant des agents Windows et Linux dans un [groupe d’administration System Center Operations Manager](../log-analytics/log-analytics-om-agents.md) connecté. <br><br>Une connexion directe entre l’ordinateur agent System Center Operations Manager et Log Analytics est requise. Les données sont transférées du groupe d’administration à l’espace de travail Log Analytics.|
| Compte Azure Storage | Non  | Service Map collecte des données provenant des ordinateurs agents. Aucune donnée n’est donc recueillie à partir du Stockage Azure. |

Service Map prend uniquement en charge les plateformes 64 bits.

Sous Windows, Microsoft Monitoring Agent (MMA) est utilisé à la fois par System Center Operations Manager et par Log Analytics pour collecter et envoyer des données de contrôle. (Cet agent est nommé Agent System Center Operations Manager, Agent OMS, Agent Log Analytics, MMA ou Agent direct, en fonction du contexte.) System Center Operations Manager et Log Analytics fournissent différentes versions prêtes à l’emploi de MMA. Les deux versions peuvent envoyer leurs rapports à System Center Operations Manager, à Log Analytics ou aux deux.  

Sur Linux, Agent OMS pour Linux collecte et envoie les données de contrôle à Log Analytics. Vous pouvez utiliser Service Map sur des serveurs équipés d’agents directs OMS ou sur des serveurs rattachés à Log Analytics au moyen de groupes d’administration System Center Operations Manager.  

Dans cet article, nous faisons référence à tous les agents (Linux ou Windows, connectés à un groupe d’administration System Center Operations Manager ou directement à Log Analytics) sous le terme « d’agent OMS ». Nous n’utiliserons le nom de déploiement propre à l’agent que si le contexte l’impose.

L’agent Carte de service ne transmet pas les données lui-même et il n’est pas nécessaire d’apporter des modifications au pare-feu ni aux ports. Les données de Service Map sont toujours transmises par l’agent OMS à Log Analytics, directement ou par le biais de la passerelle OMS.

![Agents Service Map](media/oms-service-map/agents.png)

Si vous êtes un utilisateur de System Center Operations Manager avec un groupe d’administration connecté à Log Analytics :

- Aucune configuration supplémentaire n’est requise si vos agents System Center Operations Manager peuvent accéder à Internet pour se connecter à Log Analytics.  
- Vous devez configurer la passerelle OMS de sorte qu’elle fonctionne avec System Center Operations Manager si vos agents System Center Operations Manager ne peuvent pas accéder à Log Analytics par Internet.
  
Si vous utilisez l’agent direct OMS, vous devez configurer l’agent OMS proprement dit pour qu’il se connecte à Log Analytics ou à votre passerelle OMS. La passerelle OMS est téléchargeable sur le [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

### <a name="management-packs"></a>Packs d’administration
Quand Service Map est activé dans un espace de travail Log Analytics, un pack d’administration de 300 Ko est envoyé à tous les serveurs Windows de cet espace de travail. Si vous utilisez des agents System Center Operations Manager dans un [groupe d’administration connecté](../log-analytics/log-analytics-om-agents.md), le pack d’administration Service Map est déployé à partir de System Center Operations Manager. Si les agents sont connectés directement, Log Analytics remet le pack d’administration.

Le pack d’administration se nomme Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Il est enregistré dans %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. La source de données utilisée par le pack d’administration est %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Installation
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Installer l’agent de dépendances sous Microsoft Windows
Des privilèges d’administrateur sont requis pour installer ou désinstaller l’agent.

L’agent de dépendances s’installe sur les ordinateurs Windows par le biais de InstallDependencyAgent-Windows.exe. Si vous exécutez ce fichier exécutable sans aucune option, il démarre un Assistant que vous pouvez suivre pour une installation interactive.  

Utilisez les étapes suivantes pour installer l’agent de dépendances sur chaque ordinateur Windows :

1.  Installez l’agent OMS en suivant les instructions de la page [Connecter des ordinateurs Windows au service Log Analytics dans Azure](../log-analytics/log-analytics-windows-agent.md).
2.  Téléchargez l’agent Windows et exécutez-le à l’aide de la commande suivante : <br>`InstallDependencyAgent-Windows.exe`
3.  Suivez les instructions de l’Assistant pour installer l’agent.
4.  Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux. Sur les agents Windows, le répertoire des journaux est %Programfiles%\Microsoft Dependency Agent\logs. 

#### <a name="windows-command-line"></a>Ligne de commande Windows
Utilisez les options dans le tableau suivant pour effectuer l’installation à partir d’une ligne de commande. Pour afficher la liste des indicateurs d’installation, exécutez le programme d’installation à l’aide de l’indicateur /? comme suit.

    InstallDependencyAgent-Windows.exe /?

| Indicateur | Description |
|:--|:--|
| /? | Récupérez la liste des options de ligne de commande. |
| /S | Effectuez une installation silencieuse sans invite utilisateur. |

Par défaut, les fichiers de l’agent de dépendances Windows sont placés dans le répertoire C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Installer l’agent de dépendances sous Linux
L’accès racine est requis pour installer ou configurer l’agent.

L’agent de dépendances s’installe sur les ordinateurs Linux par le biais de InstallDependencyAgent-Linux64.bin, un script shell avec un fichier binaire à extraction automatique. Vous pouvez exécuter le fichier à l’aide de sh ou ajouter des autorisations d’exécution au fichier lui-même.
 
Utilisez les étapes suivantes pour installer l’agent de dépendances sur chaque ordinateur Linux :

1.  Installez l’agent OMS en suivant les instructions de la page [Connecter et gérer les données à partir d’ordinateurs Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Installez l’agent de dépendances Linux en tant que racine à l’aide de la commande suivante :<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux. Sur les agents Linux, le répertoire des journaux est /var/opt/microsoft/dependency-agent/log.

Pour afficher la liste des indicateurs d’installation, exécutez le programme d’installation avec l’indicateur -help comme suit.

    InstallDependencyAgent-Linux64.bin -help

| Indicateur | Description |
|:--|:--|
| -help | Récupérez la liste des options de ligne de commande. |
| -s | Effectuez une installation silencieuse sans invite utilisateur. |
| --check | Vérifiez les autorisations et le système d’exploitation, sans installer l’agent. |

Les fichiers de l’agent de dépendances sont placés dans les répertoires suivants :

| Fichiers | Lieu |
|:--|:--|
| Fichiers principaux | /opt/microsoft/dependency-agent |
| Fichiers journaux | /var/opt/microsoft/dependency-agent/log |
| Fichiers de configuration | /etc/opt/microsoft/dependency-agent/config |
| Fichiers exécutables de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Fichiers de stockage binaires | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exemples de script d’installation
Pour déployer facilement l’agent de dépendances sur plusieurs serveurs d’un coup, il est plus facile d’utiliser un script. Vous pouvez utiliser les exemples de script suivants pour télécharger et installer l’agent de dépendances sous Windows ou sous Linux.

### <a name="powershell-script-for-windows"></a>Script PowerShell pour Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Script shell pour Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="azure-vm-extension"></a>Extension de machine virtuelle Azure
Vous pouvez facilement déployer l’Agent de dépendances sur vos machines virtuelles Azure à l’aide d’une [Extension de machine virtuelle Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Avec l’Extension de machine virtuelle Azure, vous pouvez déployer l’Agent de dépendances sur vos machines virtuelles par le biais d’un script PowerShell ou directement dans le modèle Azure Resource Manager de la machine virtuelle.  Une extension est disponible pour Windows (DependencyAgentWindows) et Linux (DependencyAgentLinux).  Si vous déployez par le biais de l’Extension de machine virtuelle Azure, vos agents peuvent être mis à jour automatiquement avec les dernières versions.

Pour déployer l’Extension de machine virtuelle Azure par le biais de PowerShell, vous pouvez utiliser l’exemple suivant :
```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Pour être sûr que l’Agent de dépendances se trouve sur chacune de vos machines virtuelles, le moyen le plus simple consiste à inclure l’agent dans votre modèle Azure Resource Manager.  Notez que l’Agent de dépendances dépend toujours de l’Agent OMS. Ainsi, [l’Extension de machine virtuelle de l’Agent OMS](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-vm-extension) doit être déployée en premier.  Vous pouvez ajouter l’extrait de code JSON suivant à la section *resources* de votre modèle.
```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
"[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```


## <a name="desired-state-configuration"></a>Configuration de l’état souhaité (DSC)
Pour déployer l’agent de dépendances avec Desired State Configuration, vous pouvez utiliser le module xPSDesiredStateConfiguration et un peu de code comme ceci :
```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
    xRemoteFile DAPackage 
    {
        Uri = "https://aka.ms/dependencyagentwindows"
        DestinationPath = $DAPackageLocalPath
    }

    xPackage DA
    {
        Ensure="Present"
        Name = "Dependency Agent"
        Path = $DAPackageLocalPath
        Arguments = '/S'
        ProductId = ""
        InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
        InstalledCheckRegValueName = "DisplayName"
        InstalledCheckRegValueData = "Dependency Agent"
        DependsOn = "[xRemoteFile]DAPackage"
    }
  }
}
```

## <a name="uninstallation"></a>Désinstallation
### <a name="uninstall-the-dependency-agent-on-windows"></a>Désinstaller l’agent de dépendances sous Windows
Un administrateur peut désinstaller l’agent de dépendances pour Windows au moyen du Panneau de configuration.

Un administrateur peut également exécuter %Programfiles%\Microsoft Dependency Agent\Uninstall.exe pour désinstaller l’agent de dépendances.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Désinstaller l’agent de dépendances sous Linux
Vous pouvez désinstaller l’Agent de dépendances de Linux avec la commande suivante.
<br>RHEL, CentOs ou Oracle :
```
sudo rpm -e dependency-agent
```
Ubuntu :
```
sudo apt -y purge dependency-agent
```
## <a name="troubleshooting"></a>Résolution de problèmes
Si vous rencontrez des problèmes d’installation ou d’exécution de Service Map, cette section vous aidera peut-être. Si vous n’arrivez toujours pas à les résoudre, contactez le Support Microsoft.

### <a name="dependency-agent-installation-problems"></a>Problèmes d’installation de l’agent de dépendances
#### <a name="installer-asks-for-a-reboot"></a>Le programme d’installation vous demande de redémarrer
L’agent de dépendances ne nécessite *généralement* pas un redémarrage à l’installation ou à la désinstallation. Toutefois, dans quelques rares cas, Windows Server nécessite un redémarrage pour poursuivre l’installation. Cela se produit lorsqu’une dépendance, généralement Redistributable Microsoft Visual C++, requiert un redémarrage en raison d’un fichier verrouillé.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Le message « Impossible d’installer l’agent de dépendances : échec de l’installation des bibliothèques runtime de Visual Studio (code = [code_number]) » apparaît.

L’agent de dépendances Microsoft repose sur les bibliothèques runtime de Microsoft Visual Studio. Vous recevrez un message si un problème est survenu lors de l’installation des bibliothèques. 

Les programmes d’installation des bibliothèques Runtime créent des journaux dans le dossier %LOCALAPPDATA%\temp. Le fichier est dd_vcredist_arch_aaaammjjhhmmss.log, où *arch* prend la valeur « x86 » ou « amd64 » et *aaaammjjhhmmss* correspond à la date et à l’heure (au format 24 heures) de création du journal. Le journal fournit des détails sur le problème qui bloque l’installation.

Il peut être utile d’installer d’abord les [dernières bibliothèques runtime](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) par vous-même.

Le tableau ci-dessous liste des numéros de code et des suggestions de résolutions.

| Code | Description | Résolution : |
|:--|:--|:--|
| 0x17 | Le programme d’installation de la bibliothèque nécessite une mise à jour Windows qui n’a pas été installée. | Consultez le dernier journal du programme d’installation de la bibliothèque.<br><br>Si une référence à « Windows8.1-KB2999226-x64.msu » est suivie d’une ligne « Erreur 0x80240017 : Impossible d’exécuter le package MSU », cela signifie que vous n’avez pas la configuration requise pour installer KB2999226. Suivez les instructions de la section Conditions préalables sur la page [Universal C Runtime sous Windows](https://support.microsoft.com/kb/2999226). Vous devrez peut-être exécuter Windows Update et redémarrer plusieurs fois afin d’installer les composants nécessaires.<br><br>Exécutez à nouveau le programme d’installation de l’agent de dépendances Microsoft. |

### <a name="post-installation-issues"></a>Problèmes après installation
#### <a name="server-doesnt-appear-in-service-map"></a>Le serveur n’apparaît pas dans Service Map
Si votre installation de l’agent de dépendances a réussi mais que vous ne voyez pas votre serveur dans la solution de carte de service :
* L’agent de dépendances est-il correctement installé ? Vous pouvez vous en assurer en vérifiant si le service est installé et en cours d’exécution.<br><br>
**Windows** : recherchez le service nommé « Microsoft Dependency Agent ».<br>
**Linux** : recherchez « microsoft-dependency-agent » dans les processus en cours d’exécution.

* Utilisez-vous le [niveau tarifaire Gratuit d’Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers) ? Le forfait Gratuit autorise jusqu'à cinq serveurs de carte de service uniques. Les serveurs suivants ne s’afficheront pas dans la carte de service, même si les cinq précédents n’envoient plus de données.

* Votre serveur envoie-t-il des données de journal et de performances à Log Analytics ? Accédez à la recherche dans les journaux et exécutez la requête suivante sur votre ordinateur : 

        * Computer="<your computer name here>" | measure count() by Type
        
  Avez-vous reçu divers événements dans les résultats ? Les données sont-elles récentes ? Dans ce cas, votre agent OMS fonctionne correctement et communique avec Log Analytics. Si ce n’est pas le cas, vérifiez l’agent OMS sur votre serveur : [Résolution des problèmes de l’agent OMS pour Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) ou [Résolution des problèmes de l’agent OMS pour Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Le serveur s’affiche dans Service Map, mais n’a aucun processus
Si vous voyez votre serveur dans la carte de service, mais qu’il ne comporte aucune donnée de processus ou de connexion, cela indique que l’agent de dépendances est installé et en cours d’exécution, mais que le pilote du noyau ne s’est pas chargé. 

Vérifiez le fichier C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log (Windows) ou le fichier /var/opt/microsoft/dependency-agent/log/service.log (Linux). Les dernières lignes du fichier doivent indiquer la raison pour laquelle le noyau ne s’est pas chargé. Par exemple, le noyau n’est peut-être pas pris en charge sous Linux si vous l’avez mis à jour.

## <a name="data-collection"></a>Collecte des données
Vous pouvez vous attendre à ce que chaque agent transmette par jour environ 25 Mo selon la complexité des dépendances du système. Chaque agent envoie des données de dépendance Service Map toutes les 15 secondes.  

L’agent de dépendances consomme généralement 0,1 % de la mémoire système et 0,1 % du processeur système.

## <a name="supported-azure-regions"></a>Régions Azure prises en charge
Service Map est actuellement disponible dans les régions Azure suivantes :
- Est des États-Unis
- Europe de l'Ouest
- Centre-Ouest des États-Unis
- Asie du Sud-Est


## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Les sections suivantes répertorient les systèmes d’exploitation pris en charge par l’agent de dépendances. Service Map ne prend pas en charge les architectures 32 bits, quel que soit le système d’exploitation.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Ordinateurs Windows
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux et Oracle Linux (avec noyau RHEL)
- Seules les versions du noyau SMP Linux et par défaut sont prises en charge.
- Les versions non standard du noyau, par exemple PAE et Xen, ne sont prises en charge par aucune distribution Linux. Par exemple, un système avec la chaîne de version « 2.6.16.21-0.8-xen » n’est pas pris en charge.
- Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.
- Le noyau CentOSPlus n’est pas pris en charge.
- Oracle Unbreakable Enterprise Kernel (UEK) est traité dans une autre section, plus loin dans cet article.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Version du SE | Version du noyau |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Version du SE | Version du noyau |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Version du SE | Version du noyau |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Serveur Ubuntu
- Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.

| Version du SE | Version du noyau |
|:--|:--|
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux avec Unbreakable Enterprise Kernel
#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Version du SE | Version du noyau
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Version du SE | Version du noyau
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Version du SE | Version du noyau
|:--|:--|
| 11 SP2 | 3.0.101-0.7 |
| 11 SP3 | 3.0.101-0.47 |
| 11 SP4 | 3.0.101-65 |


## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation
Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Service Map. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service Service Map. Elles comprennent des informations sur la configuration du logiciel, notamment son système d’exploitation et sa version. Elles incluent également l’adresse IP, le nom DNS et le nom de la station de travail afin de fournir des capacités de dépannage précises et efficaces. Nous ne collectons pas votre nom, votre adresse, ni vos autres coordonnées.

Pour plus d’informations sur la collecte et l’utilisation des données, consultez la [Déclaration de confidentialité Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [utiliser Service Map](operations-management-suite-service-map.md) une fois le déploiement et la configuration effectués.
