---
title: "Utilisation de PerfInsights dans Microsoft Azure | Microsoft Docs"
description: "Découvrez comment utiliser PerfInsights pour résoudre les problèmes de performances liés aux machines virtuelles Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2018
---
# <a name="how-to-use-perfinsights"></a>Utilisation de PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) est un script automatisé qui collecte des informations de diagnostic utiles. Il exécute également des charges d’e/s et fournit un rapport d’analyse afin de faciliter la résolution des problèmes de performances liés aux machines virtuelles Windows dans Azure. Vous pouvez l’exécuter sur les machines virtuelles sous forme de script autonome ou directement à partir du portail en installant l’[extension de diagnostic de performance des machines virtuelles Azure](performance-diagnostics-vm-extension.md).

Si vous rencontrez des problèmes de performances avec des machines virtuelles, exécutez ce script avant de contacter le support.

## <a name="supported-troubleshooting-scenarios"></a>Scénarios de résolution des problèmes pris en charge

PerfInsights peut collecter et analyser plusieurs types d’informations. Les sections suivantes couvrent les scénarios courants.

### <a name="collect-basic-configuration"></a>Collecte de la configuration de base 

Ce scénario collecte la configuration de disque et d’autres informations importantes, notamment :

-   Journaux d’événements

-   État du réseau pour toutes les connexions entrantes et sortantes

-   Paramètres de configuration du pare-feu et du réseau

-   Liste des tâches pour toutes les applications en cours d’exécution sur le système

-   Fichier d’informations créé par msinfo32 pour la machine virtuelle

-   Paramètres de configuration de base de données Microsoft SQL Server (si la machine virtuelle est identifiée en tant que serveur exécutant SQL Server)

-   Compteurs de fiabilité de stockage

-   Correctifs logiciels Windows importants

-   Pilotes de filtre installés

Il s’agit d’une collecte passive d’informations qui ne sont pas censées affecter le système. 

>[!Note]
>Ce scénario est automatiquement inclus dans les scénarios suivants.

### <a name="benchmarking"></a>Benchmarking

Ce scénario exécute le test d’évaluation [Diskspd](https://github.com/Microsoft/diskspd) (E/S par seconde et Mbits/s) pour tous les disques joints à la machine virtuelle. 

> [!Note]
> Ce scénario peut affecter le système et ne doit pas être exécuté sur un système de production en direct. Si nécessaire, exécutez ce scénario dans une fenêtre de maintenance dédiée pour éviter tout problème. Une charge de travail accrue qui est provoquée par un test d’évaluation ou un suivi peut nuire aux performances de votre machine virtuelle.
>

### <a name="slow-vm-analysis"></a>Analyse lente de machine virtuelle 

Ce scénario exécute un suivi du [compteur de performances](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) en utilisant les compteurs spécifiés dans le fichier Generalcounters.txt. Si la machine virtuelle est identifiée en tant que serveur qui exécute SQL Server, elle exécute un suivi du compteur de performances. Elle réalise cela à l’aide des compteurs qui se trouvent dans le fichier Sqlcounters.txt, et cela inclut également des données de diagnostics de performances.

### <a name="slow-vm-analysis-and-benchmarking"></a>Évaluation et analyse lente de machine virtuelle

Ce scénario exécute un suivi du [compteur de performances](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx), puis un test d’évaluation [Diskspd](https://github.com/Microsoft/diskspd). 

> [!Note]
> Ce scénario peut affecter le système et ne doit pas être exécuté sur un système de production en direct. Si nécessaire, exécutez ce scénario dans une fenêtre de maintenance dédiée pour éviter tout problème. Une charge de travail accrue qui est provoquée par un test d’évaluation ou un suivi peut nuire aux performances de votre machine virtuelle.
>

### <a name="azure-files-analysis"></a>Analyse de fichiers Azure 

Ce scénario exécute une capture du compteur de performances spéciale ainsi qu’un suivi du réseau. La capture inclut tous les compteurs de partages de clients SMB. Voici quelques compteurs de performances de partages de clients SMB clés, qui font partie de la capture :

| **Type**     | **Compteur de partages de clients SMB** |
|--------------|-------------------------------|
| E/S par seconde         | Requêtes de données/s             |
|              | Requêtes de lecture/s             |
|              | Requêtes d’écriture/s            |
| Latency      | Requête de données/s (moyenne)         |
|              | Lecture/s (moyenne)                 |
|              | Écriture/s (moyenne)                |
| Taille d’E/S      | Avg. Octets/requête de données       |
|              | Avg. Octets/lecture               |
|              | Avg. Octets/écriture              |
| Throughput   | Octets de données/s                |
|              | Octets de lecture/s                |
|              | Octets d’écriture/s               |
| Longueur de la file d’attente | Avg. Longueur de la file d’attente de lecture        |
|              | Avg. Longueur de la file d’attente d’écriture       |
|              | Avg. Longueur de la file d’attente de données        |

### <a name="custom-slow-vm-analysis"></a>Analyse lente personnalisée de machine virtuelle 

Lorsque vous exécutez une analyse lente personnalisée de machine virtuelle, vous sélectionnez des traces à s’exécuter en parallèle. Vous pouvez toutes les exécuter (compteur de performances, Xperf, réseau et StorPort) si vous le souhaitez. Une fois le suivi terminé, l’outil exécute l’évaluation de Diskspd, si elle est sélectionnée. 

> [!Note]
> Ce scénario peut affecter le système et ne doit pas être exécuté sur un système de production en direct. Si nécessaire, exécutez ce scénario dans une fenêtre de maintenance dédiée pour éviter tout problème. Une charge de travail accrue qui est provoquée par un test d’évaluation ou un suivi peut nuire aux performances de votre machine virtuelle.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Quelles informations sont collectées par le script ?

Les informations portant sur la configuration de la machine virtuelle Windows, des disques ou des pools de stockage, les compteurs de performances, les journaux et les différentes traces sont recueillies. Cela varie selon le scénario de performances que vous utilisez. Le tableau suivant fournit les détails :

|Données collectées                              |  |  | Scénarios de performances |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Collecte de la configuration de base | Benchmarking | Analyse lente de machine virtuelle | Évaluation et analyse lente de machine virtuelle | Analyse de fichiers Azure | Analyse lente personnalisée de machine virtuelle |
| Informations tirées des journaux d’événements      | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Informations système               | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Mappage de volume                       | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Mappage de disque                         | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Tâches en cours d’exécution                    | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Compteurs de fiabilité de stockage     | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Informations sur le stockage              | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Sortie Fsutil                    | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Informations du pilote de filtre               | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Sortie Netstat                   | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Configuration réseau            | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Configuration du pare-feu           | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Configuration de SQL Server         | OUI                        | OUI                                | OUI                      | OUI                            | OUI                  | OUI                  |
| Suivis des diagnostics de performances * | OUI                        | OUI                                | OUI                      |                                | OUI                  | OUI                  |
| Suivi du compteur de performances **     |                            |                                    |                          |                                |                      | OUI                  |
| Suivi du compteur SMB **             |                            |                                    |                          |                                | OUI                  |                      |
| Suivi du compteur SQL Server **      |                            |                                    |                          |                                |                      | OUI                  |
| Suivi XPerf                      |                            |                                    |                          |                                |                      | OUI                  |
| Suivi StorPort                   |                            |                                    |                          |                                |                      | OUI                  |
| Suivi réseau                    |                            |                                    |                          |                                | OUI                  | OUI                  |
| Suivi d’évaluation Diskspd ***      |                            | OUI                                |                          | OUI                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Suivi des diagnostics de performances (*)

Exécute un moteur basé sur des règles en arrière-plan afin de collecter des données et diagnostiquer les problèmes de performances en cours. Les règles actuellement prises en charge sont les suivantes :

- Règle HighCpuUsage : détecte les périodes d’utilisation élevée du processeur et montre les principaux utilisateurs du processeur pendant ces périodes.
- Règle HighDiskUsage : détecte les périodes d’utilisation élevée de disque sur les disques physiques et montre les principaux utilisateurs de disque pendant ces périodes.
- Règle HighResolutionDiskMetric : montre les mesures d’E/S par seconde, de débit et de latence d’E/S par intervalles de 50 millisecondes pour chaque disque physique. Cela permet d’identifier rapidement les périodes de limitation de disque.
- Règle HighMemoryUsage : détecte les périodes d’utilisation élevée de la mémoire et montre les principaux utilisateurs de la mémoire pendant ces périodes.

> [!NOTE] 
> Actuellement, les versions Windows incluant .NET Framework 3.5 ou versions ultérieures sont prises en charge.

### <a name="performance-counter-trace-"></a>Suivi du compteur de performances (**)

Collecte les compteurs de performances suivants :

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk, et \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures, et \Server\Pool Paged Failures
- Certains compteurs sous \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments,  \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity, and \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Pour les instances SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats, et \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Pour les fichiers Azure
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Suivi d’évaluation Diskspd (***)
Tests de charge de travail d’E/S Diskspd (disque de système d’exploitation [écriture] et disques du pool [lecture/écriture])

## <a name="run-the-perfinsights-script-on-your-vm"></a>Exécuter le script PerfInsights sur votre machine virtuelle

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Que dois-je savoir avant d’exécuter le script ? 

#### <a name="script-requirements"></a>Configuration requise pour le script

-  Ce script doit être exécuté sur la machine virtuelle qui présente le problème de performances. 

-  Les systèmes d’exploitation suivants sont pris en charge : Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, et Windows Server 2016; Windows 8.1 and Windows 10.

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>Problèmes possibles lorsque vous exécutez le script sur des machines virtuelles de production

-  Pour les scénarios d’évaluation ou d’analyse lente personnalisée de machine virtuelle configurés pour utiliser XPerf ou DiskSpd, le script peut nuire aux performances de la machine virtuelle. Vous ne devez pas exécuter ces scénarios dans un environnement de production.

-  Pour les scénarios d’évaluation ou d’analyse lente personnalisée de machine virtuelle configurés pour utiliser DiskSpd, assurez-vous qu’aucune autre activité d’arrière-plan n’interfère avec la charge de travail d’E/S.

-  Par défaut, le script utilise le disque de stockage temporaire pour collecter les données. Si le suivi est activé sur une durée plus longue, la quantité de données collectées peut être pertinente. Cela peut réduire la disponibilité de l’espace sur le disque temporaire, et donc affecter toute application s’appuyant sur ce disque.

### <a name="how-do-i-run-perfinsights"></a>Comment exécuter PerfInsights ? 

Vous pouvez exécuter PerfInsights sur une machine virtuelle en installant l’[extension de diagnostic de performance des machines virtuelles Azure](performance-diagnostics-vm-extension.md). Vous pouvez également l’exécuter en tant que script autonome. 

**Installer et exécuter PerfInsights à partir du portail Azure**

Pour plus d’informations sur cette option, consultez [Installer l’extension de diagnostic de performance Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Exécuter le script PerfInsights en mode autonome**

Pour exécuter le script PerfInsights, suivez ces étapes :


1. Téléchargez [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Débloquez le fichier PerfInsights.zip. Pour ce faire, cliquez avec le bouton droit sur le fichier PerfInsights.zip, puis sélectionnez **Propriétés**. Sous l’onglet **Général**, sélectionnez **Débloquer**, puis **OK**. Cela garantit que le script s’exécute sans invite de sécurité supplémentaire.  

    ![Capture d’écran des propriétés PerfInsights, avec mise en surbrillance du déblocage](media/how-to-use-perfInsights/unlock-file.png)

3.  Développez le fichier compressé PerfInsights.zip dans votre disque temporaire (par défaut, il s’agit généralement du disque D). Le fichier compressé doit contenir les fichiers et dossiers suivants :

    ![Captures d’écran des fichiers du dossier .zip](media/how-to-use-perfInsights/file-folder.png)

4.  Ouvrez Windows PowerShell en tant qu’administrateur, puis exécutez le script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Vous devrez peut-être entrer « y » pour confirmer que vous souhaitez modifier la stratégie d’exécution.

    Dans la boîte de dialogue **Informations et consentement**, vous disposez de l’option de partage des informations de diagnostic avec le Support Microsoft. Vous devez également accepter le contrat de licence pour continuer. Procédez à vos sélections, puis sélectionnez **Exécuter le script**.

    ![Capture d’écran de la boîte de dialogue Informations et Consentement](media/how-to-use-perfInsights/disclaimer.png)

5.  Soumettez le numéro de dossier, s’il est disponible, lorsque vous exécutez le script. Sélectionnez ensuite **OK**.
    
    ![Capture d’écran de la boîte de dialogue ID de support](media/how-to-use-perfInsights/enter-support-number.png)

6.  Sélectionnez votre disque de stockage temporaire. Le script peut détecter automatiquement la lettre de lecteur du disque. Si des problèmes surviennent à cette étape, il se peut que vous soyez invité à sélectionner le disque (le disque par défaut est D). Les journaux générés sont stockés ici dans le dossier log\_collection. Une fois que vous avez entré ou accepté la lettre de lecteur, cliquez sur **OK**.

    ![Capture d’écran de la boîte de dialogue Lecteur temporaire](media/how-to-use-perfInsights/enter-drive.png)

7.  Sélectionnez un scénario de dépannage dans la liste fournie.

       ![Capture d’écran de la liste des scénarios de résolution des problèmes](media/how-to-use-perfInsights/select-scenarios.png)

Vous pouvez également exécuter PerfInsights sans interface utilisateur. La commande suivante exécute le scénario de résolution des problèmes d’analyse lente de machine virtuelle sans interface utilisateur. Vous êtes invité à accepter les mêmes exclusion de responsabilité et CLUF qu’à l’étape 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

Si vous souhaitez que PerfInsights s’exécute en mode silencieux, utilisez le paramètre **-AcceptDisclaimerAndShareDiagnostics**. Par exemple, utilisez la commande suivante :

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Comment résoudre des problèmes lors de l’exécution du script ?

Si le script s’est terminé anormalement, vous pouvez revenir à un état cohérent en exécutant le script avec le commutateur -Cleanup, comme suit :

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

En cas de problème pendant la détection automatique du disque temporaire, vous devrez peut-être sélectionner le disque (le disque par défaut est D).

![Capture d’écran de la boîte de dialogue Lecteur temporaire](media/how-to-use-perfInsights/enter-drive.png)

Le script désinstalle les outils de l’utilitaire et supprime les dossiers temporaires.

### <a name="troubleshoot-other-script-issues"></a>Résolution d’autres problèmes de script 

En cas de problème pendant l’exécution du script, appuyez sur Ctrl+C pour interrompre le script. Si vous rencontrez un échec de script même après plusieurs tentatives, exécutez le script en mode débogage à l’aide de l’option de paramètre « -Debug » au démarrage.

Après la défaillance, copiez la sortie complète de la console PowerShell, puis envoyez-la à l’agent du Support technique Microsoft qui vous aidera à résoudre ce problème.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>Comment faire pour exécuter le script en mode d’analyse lente personnalisée de machine virtuelle ?

En sélectionnant **l’analyse lente personnalisée de machine virtuelle**, vous pouvez activer plusieurs suivis en parallèle (maintenez la touche Maj enfoncée pour sélectionner plusieurs suivis) :

![Capture d’écran de la liste des scénarios](media/how-to-use-perfInsights/select-scenario.png)

Lorsque vous sélectionnez les scénarios portant sur le suivi du compteur de performances, le suivi XPerf, le suivi réseau ou le suivi Storport, référez-vous aux instructions indiquées dans les boîtes de dialogue suivantes. Essayez de reproduire le problème de ralentissement des performances lorsque vous démarrez les traces.

La boîte de dialogue ci-dessous vous permet de lancer un suivi :

![Capture d’écran de la boîte de dialogue du démarrage du suivi du compteur de performances](media/how-to-use-perfInsights/start-trace-message.png)

Pour arrêter les suivis, vous devez confirmer la commande dans une seconde boîte de dialogue.

![Capture d’écran de la boîte de dialogue d’arrêt du suivi du compteur de performances](media/how-to-use-perfInsights/stop-trace-message.png)
![Capture d’écran de la boîte de dialogue d’arrêt tous les suivis](media/how-to-use-perfInsights/ok-trace-message.png)

Lorsque les suivis ou les opérations sont terminés, un nouveau fichier apparaît dans D:\\log\_collection (ou le lecteur temporaire). Le nom du fichier est **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip.** Vous pouvez envoyer ce fichier à l’agent de Support pour analyse.

## <a name="review-the-diagnostics-report"></a>Examinez le rapport de diagnostics

Le fichier **CollectedData\_aaaa-MM-jj\_hh\_mm\_ss.zip** peut inclure un rapport HTML qui détaille les conclusions de PerfInsights. Pour passer en revue le rapport, développez le fichier **CollectedData\_aaaa-MM-jj\_hh\_mm\_ss.zip**, puis ouvrez le fichier **PerfInsights report.htm**.

Sélectionnez l’onglet **Conclusions**.

![Capture d’écran du rapport de PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![Capture d’écran du rapport de PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Les conclusions identifiées comme étant critiques sont des problèmes connus qui peuvent conduire à des problèmes de performances. Les conclusions identifiées comme étant importantes représentent des configurations non optimales ne provoquant pas forcément de problèmes de performances. Les conclusions identifiées comme étant informatives sont des instructions données à titre informatif uniquement.

Examinez les recommandations et les liens pour toutes les conclusions critiques et importantes. En savoir plus sur la façon dont elles peuvent affecter les performances, et sur les meilleures pratiques pour les configurations de performances optimisées.

### <a name="storage-tab"></a>Onglet Stockage

La section **Conclusions** affiche les différentes conclusions et recommandations relatives au stockage.

Les sections **Mappage de disque** et **Mappage de volume** décrivent les liens entre volumes logiques et disques physiques.

Dans la perspective du disque physique (Mappage de disque), le tableau affiche tous les volumes logiques en cours d’exécution sur le disque. Dans l’exemple suivant, **PhysicalDrive2** exécute deux volumes logiques créés sur plusieurs partitions (J et H) :

![Capture d’écran de l’onglet du disque](media/how-to-use-perfInsights/disktab.png)

Dans la perspective du volume (Mappage de volume), les tables affichent tous les disques physiques sous chaque volume logique. Pour les disques RAID/dynamiques, vous pouvez exécuter un volume logique sur plusieurs disques physiques. Dans l’exemple suivant, *C:\\mount* est un point de montage configuré en tant que *SpannedDisk* sur les disques physiques 2 et 3 :

![Capture d’écran de l’onglet de volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Onglet SQL

Si la machine virtuelle cible héberge des instances SQL Server, un onglet supplémentaire appelé **SQL** apparaît dans le rapport :

![Capture d’écran de l’onglet SQL](media/how-to-use-perfInsights/sqltab.png)

Cette section contient un onglet **Conclusions**, et des sous-onglets supplémentaires pour chacune des instances SQL Server hébergées sur la machine virtuelle.

L’onglet **Conclusions** contient une liste de tous les problèmes de performances relatifs à SQL trouvés, ainsi que les recommandations correspondantes.

Dans l’exemple suivant, **PhysicalDrive0** (en cours d’exécution du lecteur C) s’affiche. Cela est dû au fait que les fichiers **modeldev** et **modellog** se trouvent sur le disque C, et ils sont de types différents (par exemple, fichier de données et journal des transactions, respectivement).

![Capture d’écran des informations du journal](media/how-to-use-perfInsights/loginfo.png)

Les onglets des instances spécifiques de SQL Server contiennent une section générale qui affiche des informations de base sur l’instance sélectionnée. Les onglets contiennent également des sections supplémentaires pour les informations avancées, notamment les paramètres, les configurations et les options utilisateur.

### <a name="diagnostic-tab"></a>Onglet Diagnostic
L’onglet **Diagnostic** contient des informations sur les principaux consommateurs d’UC, de disque et de mémoire sur l’ordinateur pour la durée de l’exécution de PerfInsights. Vous pouvez également obtenir des informations sur les correctifs critiques non présents dans le système, la liste des tâches et les événements système importants. 

## <a name="references-to-the-external-tools-used"></a>Références aux outils externes utilisés

### <a name="diskspd"></a>Diskspd

Diskspd est un générateur de charge de stockage et un outil de test de performances de Microsoft. Pour plus d’informations, consultez [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

Xperf est un outil en ligne de commande qui permet de capturer des suivis à partir de Windows Performance Toolkit. Pour plus d’informations, consultez [Windows Performance Toolkit : Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez charger les journaux de diagnostic et les rapports vers le Support Microsoft pour un examen approfondi. Le Support peut vous demander de transmettre la sortie générée par PerfInsights pour faciliter le processus de dépannage.

La capture d’écran suivante affiche un message semblable à ce que vous pouvez recevoir :

![Capture d’écran de l’exemple de message envoyé par le Support Microsoft](media/how-to-use-perfInsights/supportemail.png)

Suivez les instructions dans le message pour accéder à l’espace de travail de transfert de fichier. Pour plus de sécurité, vous devez modifier votre mot de passe à la première utilisation.

Après vous être connecté, une boîte de dialogue s’affiche pour vous inviter à charger le fichier **CollectedData\_aaaa-MM-jj\_hh\_mm\_ss.zip** collecté par PerfInsights.
