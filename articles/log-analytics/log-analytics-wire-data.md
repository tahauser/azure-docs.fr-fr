---
title: "Solution de données de communication dans Log Analytics | Microsoft Docs"
description: "Les données de communication sont des données de réseau et de performance centralisées issues d’ordinateurs sur lesquels des agents OMS sont installés, notamment Operations Manager et les agents connectés à Windows. Les données réseau sont associées à vos données de journaux pour vous aider à mettre les données en corrélation."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 658d92900763249aeeb3542132f4fe0c43ba005f
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Solution Wire Data 2.0 (préversion) dans Log Analytics

![Symbole de Wire Data](./media/log-analytics-wire-data/wire-data2-symbol.png)

Les données de communication sont des données de performances et réseau consolidées collectées à partir d’ordinateurs connectés à Windows et à Linux avec l’agent OMS, notamment les ordinateurs contrôlés par Operations Manager dans votre environnement. Les données réseau sont associées aux autres données de journaux pour faciliter la mise en corrélation des données.

En plus de l’agent OMS, la solution Wire Data utilise des Agents de dépendances Microsoft installés sur les ordinateurs de votre infrastructure informatique. Les agents de dépendances surveillent les données réseau envoyées par et à ces ordinateurs pour les niveaux de réseau 2 et 3 dans le [modèle OSI](https://en.wikipedia.org/wiki/OSI_model), y compris les différents protocoles et ports utilisés. Les données sont ensuite envoyées à Log Analytics à l’aide d’agents.  

> [!NOTE]
> Il n’est pas possible d’ajouter la version précédente de la solution Wire Data à de nouveaux espaces de travail. Si vous avez déjà activé la solution Wire Data d’origine, vous pouvez continuer à l’utiliser. Toutefois, pour utiliser Wire Data 2.0, vous devez tout d’abord supprimer la version d’origine.

Par défaut, Log Analytics journalise les données pour l’UC, la mémoire, le disque et les données de performances du réseau à partir de compteurs intégrés à Windows et à Linux, ainsi que d’autres compteurs de performances que vous pouvez spécifier. La collecte des données réseau et autres est effectuée en temps réel pour chaque agent, notamment les sous-réseaux et les protocoles de niveau application utilisés par l’ordinateur.  Wire Data examine les données réseau au niveau de l’application, pas au niveau de la couche de transport TCP.  La solution n’examine pas les données ACK et SYN individuelles.  Une fois la connexion effectuée, elle est considérée comme une connexion active et marquée en tant que Connecté. Cette connexion reste active tant que les deux côtés acceptent que le socket est ouvert et que des données peuvent être transférées dans les deux sens.  Si l’un des deux côtés ferme la connexion, celle-ci est marquée en tant que Déconnecté.  Par conséquent, elle ne compte que la bande passante des paquets terminés avec succès et ne signale pas les renvois ou les paquets ayant échoué.

Si vous avez déjà utilisé [sFlow](http://www.sflow.org/) ou d’autres logiciels respectant le [protocole NetFlow de Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), les statistiques et les données fournies par les données de communication vous seront familières.

Voici quelques types de requêtes de recherche de journal intégrés :

- Agents qui fournissent des données de communication
- Adresse IP des agents fournissant des données de communication
- Communications sortantes par adresse IP
- Nombre d’octets envoyés par les protocoles d’application
- Nombre d’octets envoyés par un service d’application
- Octets reçus par différents protocoles
- Nombre total d’octets envoyés et reçus par version d’adresse IP
- Latence moyenne pour les connexions qui ont été mesurées de manière fiable
- Processus informatiques ayant initié ou reçu du trafic réseau
- Quantité de trafic réseau pour un processus

Quand vous recherchez à l’aide de données de communication, vous pouvez filtrer et regrouper les données pour afficher des informations sur les principaux agents et protocoles. Vous pouvez aussi savoir quand certains ordinateurs (adresses IP/MAC) ont communiqué les uns avec les autres et connaître la durée de ces communications et la quantité de données envoyées. En fait, vous affichez des métadonnées sur le trafic réseau, qui fonctionne par recherches.

Toutefois, comme il s’agit de métadonnées, elles ne sont pas nécessairement utiles pour résoudre des problèmes complexes. Les données de communication de Log Analytics ne capturent pas toutes les données réseau.  Elles ne sont pas censées être utilisées pour résoudre des problèmes complexes au niveau du paquet. L’avantage de l’utilisation de l’agent (par rapport à d’autres méthodes de collecte) est que vous n’avez pas à installer d’appareils, à reconfigurer vos commutateurs réseau ni à effectuer de configurations compliquées. Les données de communication sont simplement basées sur un agent, qu’il vous suffit d’installer sur un ordinateur pour qu’il surveille son propre trafic réseau. Le recours à un agent est aussi utile quand vous souhaitez surveiller les charges de travail en cours d’exécution dans des fournisseurs de cloud, des fournisseurs de services d’hébergement ou Microsoft Azure, dans les cas où l’utilisateur n’est pas propriétaire de la couche d’infrastructure.

## <a name="connected-sources"></a>Sources connectées

Wire Data obtient ses données auprès de l’agent de dépendances Microsoft. Celui-ci dépend de l’agent OMS pour ses connexions à Log Analytics. Cela signifie que l’agent OMS doit être installé et configuré sur le serveur pour que l’agent de dépendances puisse être installé. Le tableau suivant décrit les sources connectées prises en charge par la solution Wire Data.

| **Source connectée** | **Pris en charge** | **Description** |
| --- | --- | --- |
| Agents Windows | OUI | Wire Data analyse et collecte des données provenant d’ordinateurs agents Windows. <br><br> Outre [l’agent OMS](log-analytics-windows-agent.md), les agents Windows nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, consultez la page [Systèmes d’exploitation pris en charge](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems). |
| Agents Linux | OUI | Wire Data analyse et collecte des données provenant d’ordinateurs agents Linux.<br><br> Outre [l’agent OMS](log-analytics-quick-collect-linux-computer.md), les agents Linux nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, consultez la page [Systèmes d’exploitation pris en charge](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems). |
| Groupe d’administration Microsoft System Center Operations Manager | OUI | Wire Data analyse et collecte des données provenant des agents Windows et Linux dans un [groupe d’administration System Center Operations Manager](log-analytics-om-agents.md) connecté. <br><br> Une connexion directe entre l’ordinateur agent System Center Operations Manager et Log Analytics est requise. Les données sont transférées du groupe d’administration à Log Analytics. |
| Compte Azure Storage | Non  | Wire Data collecte des données provenant des ordinateurs agents. Aucune donnée n’est donc recueillie à partir du Stockage Azure. |

Sous Windows, Microsoft Monitoring Agent (MMA) est utilisé à la fois par System Center Operations Manager et par Log Analytics pour collecter et envoyer des données. En fonction du contexte, l’agent est nommé Agent System Center Operations Manager, Agent OMS, Agent Log Analytics, MMA ou Agent direct. System Center Operations Manager et Log Analytics fournissent des versions légèrement différentes de MMA. Les deux versions peuvent envoyer leurs rapports à System Center Operations Manager, à Log Analytics ou aux deux.

Sous Linux, l’agent OMS pour Linux collecte et envoie les données à Log Analytics. Vous pouvez utiliser Wire Data sur des serveurs équipés d’agents directs OMS ou sur des serveurs rattachés à Log Analytics au moyen de groupes d’administration System Center Operations Manager.

Dans cet article, les références à tous les agents (Linux ou Windows, connectés à un groupe d’administration System Center Operations Manager ou directement à Log Analytics) sont regroupées sous le terme _d’agents OMS_. Nous n’utiliserons le nom de déploiement propre à l’agent que si le contexte l’impose.

L’agent de dépendances ne transmet pas de données par lui-même et ne requiert pas de modifications des pare-feu ni des ports. Les données de Wire Data sont toujours transmises par l’agent OMS à Log Analytics, directement ou par le biais de la passerelle OMS.

![diagramme de l’agent](./media/log-analytics-wire-data/agents.png)

Si vous êtes un utilisateur de System Center Operations Manager avec un groupe d’administration connecté à Log Analytics :

- Aucune configuration supplémentaire n’est requise si vos agents System Center Operations Manager peuvent accéder à Internet pour se connecter à Log Analytics.
- Vous devez configurer la passerelle OMS de sorte qu’elle fonctionne avec System Center Operations Manager si vos agents System Center Operations Manager ne peuvent pas accéder à Log Analytics par Internet.

Si vous utilisez l’agent direct, vous devez configurer l’agent OMS proprement dit pour qu’il se connecte à Log Analytics ou à votre passerelle OMS. Vous pouvez télécharger la passerelle OMS sur le [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>configuration requise

- Requiert l’offre de solution [Insight & Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Si vous utilisez la version précédente de la solution Wire Data, vous devez commencer par la supprimer. Cependant, toutes les données capturées par la solution Wire Data d’origine restent disponibles dans Wire Data 2.0 et la Recherche dans les journaux.
- Des privilèges d’administrateur sont requis pour installer ou désinstaller l’agent de dépendances.
- L’agent de dépendances doit être installé sur un ordinateur équipé d’un système d’exploitation 64 bits.

### <a name="operating-systems"></a>Systèmes d’exploitation

Les sections suivantes répertorient les systèmes d’exploitation pris en charge par l’agent de dépendances. Wire Data ne prend pas en charge les architectures 32 bits, quel que soit le système d’exploitation.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Ordinateurs Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux et Oracle Linux (avec noyau RHEL)

- Seules les versions du noyau SMP Linux et par défaut sont prises en charge.
- Les versions non standard du noyau, par exemple PAE et Xen, ne sont prises en charge par aucune distribution Linux. Par exemple, un système avec la chaîne de version _2.6.16.21-0.8-xen_ n’est pas pris en charge.
- Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.
- Le noyau CentOSPlus n’est pas pris en charge.
- Oracle Unbreakable Enterprise Kernel (UEK) est traité dans une autre section, plus loin dans cet article.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Version du SE** | **Version du noyau** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Version du SE** | **Version du noyau** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **Version du SE** | **Version du noyau** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux avec Unbreakable Enterprise Kernel

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Version du SE** | **Version du noyau** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Version du SE** | **Version du noyau** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Version du SE** | **Version du noyau** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Version du SE** | **Version du noyau** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Téléchargements de l’agent de dépendances

| **File** | **SE** | **Version** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Configuration

Suivez les étapes ci-dessous afin de configurer la solution Wire Data pour vos espaces de travail.

1. Activez la solution Activity Log Analytics depuis la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).
2. Installez l’agent de dépendances sur chacun des ordinateurs sur lesquels vous souhaitez récupérer des données. L’agent de dépendances peut surveiller les connexions aux voisins immédiats ; par conséquent, vous n’avez pas forcément besoin d’un agent sur chaque ordinateur.

### <a name="install-the-dependency-agent-on-windows"></a>Installer l’agent de dépendances sous Windows

Des privilèges d’administrateur sont requis pour installer ou désinstaller l’agent.

L’agent de dépendances s’installe sur les ordinateurs sous Windows par le biais de InstallDependencyAgent-Windows.exe. Si vous exécutez ce fichier exécutable sans aucune option, il démarre un Assistant que vous pouvez suivre pour une installation interactive.

Utilisez les étapes suivantes pour installer l’agent de dépendances sur chaque ordinateur sous Windows :

1. Installez l’Agent OMS à l’aide des étapes décrites dans [Collecter les données à partir d’ordinateurs Windows hébergés dans votre environnement](log-analytics-windows-agent.md).
2. Téléchargez l’agent de dépendances Windows en suivant le lien de la section précédente, puis exécutez-le à l’aide de la commande suivante : `InstallDependencyAgent-Windows.exe`
3. Suivez les instructions de l’Assistant pour installer l’agent.
4. Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux. Pour les agents Windows, le répertoire des journaux est %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Ligne de commande Windows

Utilisez les options dans le tableau suivant pour effectuer l’installation à partir d’une ligne de commande. Pour afficher la liste des indicateurs d’installation, exécutez le programme d’installation à l’aide de l’indicateur /? comme suit.

InstallDependencyAgent-Windows.exe /?

| **Indicateur** | **Description** |
| --- | --- |
| <code>/?</code> | Récupérez la liste des options de ligne de commande. |
| <code>/S</code> | Effectuez une installation silencieuse sans invite utilisateur. |

Par défaut, les fichiers de l’agent de dépendances Windows sont placés dans le répertoire C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Installer l’agent de dépendances sous Linux

L’accès racine est requis pour installer ou configurer l’agent.

L’agent de dépendances s’installe sur les ordinateurs Linux par le biais de InstallDependencyAgent-Linux64.bin, un script shell avec un fichier binaire à extraction automatique. Vous pouvez exécuter le fichier à l’aide de _sh_ ou ajouter des autorisations d’exécution au fichier lui-même.

Utilisez les étapes suivantes pour installer l’agent de dépendances sur chaque ordinateur Linux :

1. Installez l’Agent OMS à l’aide des étapes décrites dans [Collecter les données à partir d’ordinateurs Linux hébergés dans votre environnement](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Téléchargez l’agent de dépendances Linux en suivant le lien de la section précédente, puis installez-le en tant que racine à l’aide de la commande suivante : sh InstallDependencyAgent-Linux64.bin
3. Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux. Sur les agents Linux, le répertoire des journaux est : /var/opt/microsoft/dependency-agent/log.

Pour afficher la liste des indicateurs d’installation, exécutez le programme d’installation avec l’indicateur `-help` comme suit.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Indicateur** | **Description** |
| --- | --- |
| <code>-help</code> | Récupérez la liste des options de ligne de commande. |
| <code>-s</code> | Effectuez une installation silencieuse sans invite utilisateur. |
| <code>--check</code> | Vérifiez les autorisations et le système d’exploitation, sans installer l’agent. |

Les fichiers de l’agent de dépendances sont placés dans les répertoires suivants :

| **Fichiers** | **Lieu** |
| --- | --- |
| Fichiers principaux | /opt/microsoft/dependency-agent |
| Fichiers journaux | /var/opt/microsoft/dependency-agent/log |
| Fichiers de configuration | /etc/opt/microsoft/dependency-agent/config |
| Fichiers exécutables de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Fichiers de stockage binaires | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Exemples de script d’installation

Pour déployer facilement l’agent de dépendances sur plusieurs serveurs d’un coup, il est plus facile d’utiliser un script. Vous pouvez utiliser les exemples de script suivants pour télécharger et installer l’agent de dépendances sous Windows ou sous Linux.

#### <a name="powershell-script-for-windows"></a>Script PowerShell pour Windows

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Script shell pour Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Configuration de l’état souhaité (DSC)

Pour déployer l’agent de dépendances avec Desired State Configuration, vous pouvez utiliser le module xPSDesiredStateConfiguration et un peu de code comme ceci :

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Désinstaller l’agent de dépendances

Utilisez les sections suivantes comme guide pour supprimer l’agent de dépendances.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Désinstaller l’agent de dépendances sous Windows

Un administrateur peut désinstaller l’agent de dépendances pour Windows au moyen du Panneau de configuration.

Un administrateur peut également exécuter %Programfiles%\Microsoft Dependency Agent\Uninstall.exe pour désinstaller l’agent de dépendances.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Désinstaller l’agent de dépendances sous Linux

Pour désinstaller complètement l’agent de dépendances de Linux, vous devez supprimer l’agent lui-même et le connecteur qui est installé automatiquement avec lui. Vous pouvez désinstaller les deux à l’aide de la commande ci-dessous :

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Packs d’administration

Quand Wire Data est activé dans un espace de travail Log Analytics, un pack d’administration de 300 Ko est envoyé à tous les serveurs Windows de cet espace de travail. Si vous utilisez des agents System Center Operations Manager dans un [groupe d’administration connecté](log-analytics-om-agents.md), le pack d’administration Dependency Monitor est déployé à partir de System Center Operations Manager. Si les agents sont connectés directement, Log Analytics remet le pack d’administration.

Le pack d’administration se nomme Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Il est enregistré dans : %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. La source de données utilisée par le pack d’administration est : %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Utilisation de la solution

**Installer et configurer la solution**

Utilisez les informations suivantes pour installer et configurer la solution.

- La solution de données de communication acquiert des données à partir d’ordinateurs exécutant Windows Server 2012 R2, Windows 8.1 et systèmes d’exploitation ultérieurs.
- Vous devez installer Microsoft .NET Framework 4.0 ou ultérieur sur les ordinateurs à partir desquels vous souhaitez acquérir des données de communication.
- Ajoutez la solution Wire Data à votre espace de travail Log Analytics en appliquant la procédure décrite sur la page [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Aucune configuration supplémentaire n’est requise.
- Si vous souhaitez afficher les données de communication d’une solution en particulier, celle-ci doit être déjà ajoutée à votre espace de travail.

Une fois les agents et la solution installés, la vignette de Wire Data 2.0 s’affiche dans votre espace de travail.

![Vignette de Wire Data](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Utiliser la solution Wire Data 2.0

Sur le portail OMS, cliquez sur la vignette de **Wire Data 2.0** pour ouvrir le tableau de bord de Wire Data. Le tableau de bord comprend les panneaux figurant dans le tableau suivant. Chaque panneau répertorie jusqu'à 10 éléments répondant à ses critères en ce qui concerne l’étendue et l’intervalle de temps spécifiés. Vous pouvez exécuter une recherche dans les journaux qui renvoie tous les enregistrements. Pour cela, cliquez sur **Afficher tout** en bas du panneau ou cliquez sur l’en-tête de panneau.

| **Panneau** | **Description** |
| --- | --- |
| Agents qui capturent le trafic réseau | Affiche le nombre d’agents qui capturent le trafic réseau et liste les 10 ordinateurs qui en capturent le plus. Cliquez sur le nombre pour exécuter une recherche de <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code> dans les journaux. Cliquez sur un ordinateur dans la liste pour exécuter une recherche dans les journaux qui renvoie le nombre total d’octets capturés. |
| Sous-réseaux locaux | Affiche le nombre de sous-réseaux locaux découverts par les agents.  Cliquez sur le nombre pour exécuter une recherche de <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> dans les journaux, qui liste tous les sous-réseaux avec le nombre d’octets envoyés sur chacun d’eux. Cliquez sur un sous-réseau dans la liste pour exécuter une recherche dans les journaux qui renvoie le nombre total d’octets envoyés dessus. |
| Protocole de niveau application | Affiche le nombre de protocoles de niveau application en cours d’utilisation, découverts par les agents. Cliquez sur le nombre pour exécuter une recherche de <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code> dans les journaux. Cliquez sur un protocole dans la liste pour exécuter une recherche dans les journaux qui renvoie le nombre total d’octets envoyés par son biais. |

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Tableau de bord de Wire Data](./media/log-analytics-wire-data/wire-data-dash.png)

Vous pouvez utiliser le panneau **Agents qui capturent le trafic réseau** pour déterminer quelle quantité de bande passante réseau est consommée par les ordinateurs. Il vous permet de trouver facilement l’ordinateur _qui communique le plus_ dans votre environnement. Ces ordinateurs peuvent être surchargés, agir de façon anormale ou utiliser plus de ressources réseau que la normale.

![Exemple de recherche de journal](./media/log-analytics-wire-data/log-search-example01.png)

De même, vous pouvez utiliser le panneau **Sous-réseaux locaux** pour déterminer quelle quantité de trafic réseau circule sur vos sous-réseaux. Les utilisateurs définissent souvent des sous-réseaux autour des zones critiques de leurs applications. Ce panneau permet de surveiller ces zones.

![Exemple de recherche de journal](./media/log-analytics-wire-data/log-search-example02.png)

Le Panneau **Protocoles de niveau application** est utile, car il permet de savoir quels protocoles sont en cours d’utilisation. Par exemple, vous pouvez vouloir que SSH ne soit pas utilisé dans votre environnement réseau. Les informations disponibles sur le panneau peuvent le confirmer ou le démentir rapidement.

![Exemple de recherche de journal](./media/log-analytics-wire-data/log-search-example03.png)

Dans cet exemple, vous pourriez explorer en détails les informations SSH pour savoir quels ordinateurs l’utilisent, parmi de nombreux autres détails de communication.

![Résultats de la recherche sh](./media/log-analytics-wire-data/ssh-details.png)

Il est également utile de savoir si le trafic des protocoles augmente ou diminue au fil du temps. Par exemple, si la quantité de données transmises par une application augmente, il peut être nécessaire ou intéressant de le savoir.

## <a name="input-data"></a>Données d’entrée

Les données de communication collectent des métadonnées sur le trafic réseau à l’aide des agents que vous avez activés. Chaque agent envoie des données toutes les 15 secondes environ.

## <a name="output-data"></a>Données de sortie

Un enregistrement de type _WireData_ est créé pour chaque type de données d’entrée. Les propriétés de ces enregistrements sont décrites dans le tableau suivant :

| Propriété | Description |
|---|---|
| Ordinateur | Nom de l’ordinateur sur lequel les données ont été recueillies |
| TimeGenerated | Heure de l’enregistrement |
| LocalIP | Adresse IP de l’ordinateur local |
| SessionState | Connecté ou déconnecté |
| ReceivedBytes | Nombre d’octets reçus |
| ProtocolName | Nom du protocole réseau utilisé |
| IPVersion | Version de l’adresse IP |
| Direction | Entrant ou sortant |
| MaliciousIP | Adresse IP d’une source malveillante connue |
| Niveau de gravité | Niveau de gravité suspecté |
| RemoteIPCountry | Pays de l’adresse IP distante |
| ManagementGroupName | Nom du groupe d’administration Operations Manager |
| SourceSystem | Source où les données ont été recueillies |
| SessionStartTime | Heure de début de la session |
| SessionEndTime | Heure de fin de la session |
| LocalSubnet | Sous-réseau sur lequel les données ont été recueillies |
| LocalPortNumber | Numéro de port local |
| RemoteIP | Adresse IP distante utilisée par l’ordinateur distant |
| RemotePortNumber | Numéro de port utilisé par l’adresse IP distante |
| SessionID | Valeur unique qui identifie la session de communication entre deux adresses IP |
| SentBytes | Nombre d’octets envoyés |
| TotalBytes | Nombre total d’octets envoyés au cours de la session |
| ApplicationProtocol | Type de protocole réseau utilisé   |
| ProcessID | ID du processus Windows |
| ProcessName | Chemin d’accès et nom de fichier du processus |
| RemoteIPLongitude | Valeur de longitude de l’adresse IP |
| RemoteIPLatitude | Valeur de latitude de l’adresse IP |


## <a name="next-steps"></a>étapes suivantes

- [Lancez une recherche dans les journaux](log-analytics-log-searches.md) pour afficher des enregistrements détaillés sur les recherches de données de communication.
