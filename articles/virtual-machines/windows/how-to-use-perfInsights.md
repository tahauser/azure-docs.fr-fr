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
ms.openlocfilehash: bb4c21456643532df040df4fcd5f4fa1a4f48d2c
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-perfinsights"></a>Utilisation de PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) est un script automatisé qui collecte des informations de diagnostic utiles, exécute des charges d’E/S et fournit un rapport d’analyse afin de faciliter la résolution de problèmes de performances liés aux machines virtuelles Windows dans Microsoft Azure. Vous pouvez l’exécuter sur les machines virtuelles sous forme de script autonome ou directement à partir du portail en installant l’[extension de diagnostic de performance des machines virtuelles Azure](performance-diagnostics-vm-extension.md).

Nous vous recommandons d’exécuter ce script avant d’ouvrir un ticket de support Microsoft pour les problèmes de performances liés aux machines virtuelles.

## <a name="supported-troubleshooting-scenarios"></a>Scénarios de résolution des problèmes pris en charge

PerfInsights peut collecter et analyser plusieurs types d’informations qui sont regroupés dans des scénarios uniques.

### <a name="collect-basic-configuration"></a>Collecte de la configuration de base 

Ce scénario collecte la configuration de disque et d’autres informations importantes, y compris les éléments suivants :

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

Ce scénario exécute le test d’évaluation [diskspd](https://github.com/Microsoft/diskspd) (E/S par seconde et Mbits/s) pour tous les disques joints à la machine virtuelle. 

> [!Note]
> Ce scénario peut affecter le système et ne doit pas être exécuté sur un système de production en direct. Si nécessaire, exécutez ce scénario dans une fenêtre de maintenance dédiée pour éviter tout problème. Une charge de travail accrue qui est provoquée par un test d’évaluation ou un suivi peut nuire aux performances de votre machine virtuelle.
>

### <a name="slow-vm-analysis"></a>Analyse lente de machine virtuelle 

Ce scénario exécute un suivi du [compteur de performances](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) en utilisant les compteurs spécifiés dans le fichier Generalcounters.txt. Si la machine virtuelle est identifiée en tant que serveur qui exécute SQL Server, elle exécute un suivi du compteur de performances en utilisant les compteurs du fichier Sqlcounters.txt. Cela inclut également des données de diagnostics de performances.

### <a name="slow-vm-analysis-and-benchmarking"></a>Évaluation et analyse lente de machine virtuelle

Ce scénario exécute un suivi du [compteur de performances](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx), puis un test d’évaluation [diskspd](https://github.com/Microsoft/diskspd). 

> [!Note]
> Ce scénario peut affecter le système et ne doit pas être exécuté sur un système de production en direct. Si nécessaire, exécutez ce scénario dans une fenêtre de maintenance dédiée pour éviter tout problème. Une charge de travail accrue qui est provoquée par un test d’évaluation ou un suivi peut nuire aux performances de votre machine virtuelle.
>

### <a name="azure-files-analysis"></a>Analyse de fichiers Azure 

Ce scénario exécute une capture du compteur de performances spéciale ainsi qu’un suivi du réseau. La capture inclut tous les compteurs « Partages de clients SMB ». Voici quelques compteurs de performances de partages de clients SMB clés, qui font partie de la capture :

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
| Débit   | Octets de données/s                |
|              | Octets de lecture/s                |
|              | Octets d’écriture/s               |
| Longueur de la file d’attente | Avg. Longueur de la file d’attente de lecture        |
|              | Avg. Longueur de la file d’attente d’écriture       |
|              | Avg. Longueur de la file d’attente de données        |

### <a name="custom-slow-vm-analysis"></a>Analyse lente personnalisée de machine virtuelle 

Lorsque vous exécutez une analyse lente personnalisée de machine virtuelle, vous exécutez tous les suivis (compteur de performances, xperf, réseau, storport) en parallèle, selon le nombre de suivis sélectionné. Une fois le suivi terminé, l’outil exécute l’évaluation de diskspd, si elle est sélectionnée. 

> [!Note]
> Ce scénario peut affecter le système et ne doit pas être exécuté sur un système de production en direct. Si nécessaire, exécutez ce scénario dans une fenêtre de maintenance dédiée pour éviter tout problème. Une charge de travail accrue qui est provoquée par un test d’évaluation ou un suivi peut nuire aux performances de votre machine virtuelle.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Quelles informations sont collectées par le script ?

Les informations portant sur la configuration de la machine virtuelle Windows, des disques ou des pools de stockage, les compteurs de performances, les journaux et les différentes traces sont recueillies en fonction du scénario de performances utilisé :

|Données collectées                              |  |  | Scénarios de performances |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Collecte de la configuration de base | Benchmarking | Analyse lente de machine virtuelle | Évaluation et analyse lente de machine virtuelle | Analyse de fichiers Azure | Analyse lente personnalisée de machine virtuelle |
| Informations tirées des journaux d’événements      | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Informations système               | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Mappage de volume                       | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Mappage de disque                         | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Tâches en cours d’exécution                    | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Compteurs de fiabilité de stockage     | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Informations sur le stockage              | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Sortie Fsutil                    | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Informations du pilote de filtre               | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Sortie Netstat                   | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Configuration réseau            | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Configuration du pare-feu           | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Configuration de SQL Server         | Oui                        | Oui                                | Oui                      | Oui                            | Oui                  | Oui                  |
| Suivis des diagnostics de performances* | Oui                        | Oui                                | Oui                      |                                | Oui                  | Oui                  |
| Suivi du compteur de performances**     |                            |                                    |                          |                                |                      | Oui                  |
| Suivi du compteur SMB**             |                            |                                    |                          |                                | Oui                  |                      |
| Suivi du compteur SQL Server**      |                            |                                    |                          |                                |                      | Oui                  |
| Suivi XPerf                      |                            |                                    |                          |                                |                      | Oui                  |
| Suivi StorPort                   |                            |                                    |                          |                                |                      | Oui                  |
| Suivi réseau                    |                            |                                    |                          |                                | Oui                  | Oui                  |
| Suivi d’évaluation Diskspd***      |                            | Oui                                |                          | Oui                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Suivi des diagnostics de performances (*)

Exécute un moteur basé sur des règles en arrière-plan afin de collecter des données et diagnostiquer les problèmes de performances en cours. Les règles actuellement prises en charge sont les suivantes :

- Règle HighCpuUsage : détecte les périodes d’utilisation élevée du processeur et montre les principaux utilisateurs du processeur pendant ces périodes.
- Règle HighDiskUsage : détecte les périodes d’utilisation élevée de disque sur les disques physiques et montre les principaux utilisateurs de disque pendant ces périodes.
- Règle HighResolutionDiskMetric : montre les mesures d’E/S par seconde, de débit et de latence d’E/S par intervalles de 50 millisecondes pour chaque disque physique. Cela permet d’identifier rapidement les périodes de limitation de disque.
- Règle HighMemoryUsage : détecte les périodes d’utilisation élevée de la mémoire et montre les principaux utilisateurs de la mémoire pendant ces périodes.

> [!NOTE] 
> Actuellement, les versions Windows incluant .NET Framework 3.5 ou versions ultérieures sont prises en charge.

### <a name="performance-counter-trace-"></a>Suivi du compteur de performances (**)

Collecte les compteurs de performances suivants :

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk, \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures, \Server\Pool Paged Failures
- Certains compteurs sous \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity, \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Pour les instances SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats, \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Pour les fichiers Azure
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Suivi d’évaluation Diskspd (***)
Tests de charge de travail d’E/S Diskspd [disque de système d’exploitation (écriture) et disques du pool (lecture/écriture)]

## <a name="run-the-perfinsights-on-your-vm"></a>Exécuter PerfInsights sur votre machine virtuelle

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>Que dois-je savoir avant d’exécuter le script ? 

**Configuration requise pour le script**

1.  Ce script doit être exécuté sur la machine virtuelle qui présente le problème de performances. 

2.  Les systèmes d’exploitation suivants sont pris en charge : Windows Server 2008 R2, 2012, 2012 R2, 2016 ; Windows 8.1 et Windows 10.

**Problèmes possibles lorsque vous exécutez le script sur des machines virtuelles de production :**

1.  Lorsque vous utilisez des scénarios d’évaluation ou d’analyse lente personnalisée de machine virtuelle configurés pour utiliser XPerf ou DiskSpd, le script peut nuire aux performances de la machine virtuelle. Il est déconseillé d’exécuter ces scénarios dans un environnement de production sans la surveillance d’un ingénieur CSS.

2.  Lorsque vous utilisez les scénarios d’évaluation ou d’analyse lente personnalisée de machine virtuelle configurés pour utiliser DiskSpd, assurez-vous qu’aucune autre activité d’arrière-plan n’interfère avec la charge de travail d’E/S sur les disques testés.

3.  Par défaut, le script utilise le disque de stockage temporaire pour collecter les données. Si le suivi est activé sur une durée plus longue, la quantité de données collectées peut être pertinente. Cela peut réduire la disponibilité de l’espace sur le disque temporaire, et donc affecter toute application s’appuyant sur ce disque.

### <a name="how-do-i-run-perfinsights"></a>Comment exécuter PerfInsights ? 

Vous pouvez exécuter PerfInsights sur une machine virtuelle en installant l’[extension de diagnostic de performance des machines virtuelles Azure](performance-diagnostics-vm-extension.md) ou en l’exécutant comme script autonome. 

**Installer et exécuter PerfInsights à partir du portail Azure**

Vous pouvez maintenant exécuter PerfInsights à l’aide d’une extension de machine virtuelle appelée extension de diagnostic de performance Azure. Pour plus d’informations, consultez [Installer l’extension de diagnostic de performance Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Exécuter le script PerfInsights en mode autonome**

Pour exécuter le script PerfInsights, suivez ces étapes :


1. Téléchargez [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Débloquez le fichier PerfInsights.zip. Pour ce faire, cliquez avec le bouton droit sur le fichier PerfInsights.zip, puis sélectionnez **Propriétés**. Sous l’onglet **Général**, sélectionnez **Débloquer**, puis **OK**. Cela garantit que le script s’exécute sans invite de sécurité supplémentaire.  

    ![Déverrouiller le fichier zip](media/how-to-use-perfInsights/unlock-file.png)

3.  Développez le fichier compressé PerfInsights.zip dans votre disque temporaire (par défaut, il s’agit généralement du disque D). Le fichier compressé doit contenir les fichiers et dossiers suivants :

    ![fichiers du dossier .zip.](media/how-to-use-perfInsights/file-folder.png)

4.  Ouvrez Windows PowerShell en tant qu’administrateur, puis exécutez le script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Vous devrez peut-être entrer « y » si vous êtes invité à confirmer que vous souhaitez modifier la stratégie d’exécution.

    Dans la boîte de dialogue Exclusion, vous disposez de l’option de partage des informations de diagnostic avec le Support Microsoft. Vous devez également accepter le contrat de licence pour continuer. Procédez à vos sélections, puis cliquez sur **Exécuter le script**.

    ![Boîte de dialogue Exclusion](media/how-to-use-perfInsights/disclaimer.png)

5.  Soumettez le numéro de dossier, s’il est disponible, lorsque vous exécutez le script (à des fins statistiques). Cliquez ensuite sur **OK**.
    
    ![entrer l’ID de support](media/how-to-use-perfInsights/enter-support-number.png)

6.  Sélectionnez votre disque de stockage temporaire. Le script peut détecter automatiquement la lettre du disque. Si des problèmes surviennent à cette étape, il se peut que vous soyez invité à sélectionner le disque (le disque par défaut est D). Les journaux générés sont stockés ici dans le dossier log\_collection. Une fois que vous avez entré ou accepté la lettre de disque, cliquez sur **OK**.

    ![entrer le disque](media/how-to-use-perfInsights/enter-drive.png)

7.  Sélectionnez un scénario de dépannage dans la liste fournie.

       ![Sélectionner des scénarios de support](media/how-to-use-perfInsights/select-scenarios.png)

8.  Vous pouvez également exécuter PerfInsights sans interface utilisateur.

    La commande suivante exécute le scénario de dépannage d’analyse lente de machine virtuelle sans invite de l’interface utilisateur ou données de capture pendant 30 secondes. Vous êtes invité à accepter les mêmes exclusion de responsabilité et CLUF qu’à l’étape 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    Si vous souhaitez que PerfInsights s’exécute en mode silencieux, utilisez le paramètre **-AcceptDisclaimerAndShareDiagnostics**. Par exemple, utilisez la commande suivante :

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Comment résoudre des problèmes lors de l’exécution du script ?

Si le script s’est terminé anormalement, vous pouvez nettoyer un état incohérent en exécutant le script avec le commutateur -Cleanup, comme suit :

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

En cas de problème pendant la détection automatique du disque temporaire, vous devrez peut-être sélectionner le disque (le disque par défaut est D).

![entrer le disque](media/how-to-use-perfInsights/enter-drive.png)

Le script désinstalle les outils de l’utilitaire et supprime les dossiers temporaires.

### <a name="troubleshooting-other-script-issues"></a>Résolution d’autres problèmes de script 

En cas de problème pendant l’exécution du script, appuyez sur Ctrl+C pour interrompre l’exécution du script. Pour supprimer des objets temporaires, consultez la section « Clean up after abnormal termination » (Nettoyer après un arrêt anormal).

Si vous rencontrez un échec de script même après plusieurs tentatives, nous vous recommandons d’exécuter le script en mode débogage à l’aide de l’option de paramètre -Debug au démarrage.

Après la défaillance, copiez la sortie complète de la console PowerShell, puis envoyez-la à l’agent du Support technique Microsoft qui vous aidera à résoudre ce problème.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>Comment exécuter le script en mode d’analyse lente personnalisée de machine virtuelle ?

En sélectionnant **l’analyse lente personnalisée de machine virtuelle**, vous pouvez activer plusieurs suivis en parallèle (maintenez la touche Maj enfoncée pour sélectionner plusieurs suivis) :

![sélectionner des scénarios](media/how-to-use-perfInsights/select-scenario.png)

Lorsque vous sélectionnez les scénarios portant sur le suivi du compteur de performances, le suivi XPerf, le suivi réseau ou le suivi Storport, référez-vous aux instructions indiquées dans les boîtes de dialogue et essayez de reproduire le problème de ralentissement des performances après avoir lancé les suivis.

La boîte de dialogue ci-dessous vous permet de lancer un suivi :

![lancer un suivi](media/how-to-use-perfInsights/start-trace-message.png)

Pour arrêter les suivis, vous devez confirmer la commande dans une seconde boîte de dialogue.

![arrêter un suivi](media/how-to-use-perfInsights/stop-trace-message.png)
![arrêter un suivi](media/how-to-use-perfInsights/ok-trace-message.png)

Lorsque les suivis ou les opérations sont terminés, un nouveau fichier est généré dans D:\\log\_collection (ou dans le disque temporaire) : il est nommé **CollectedData\_aaaa-MM-jj\_hh\_mm\_ss.zip.** Vous pouvez envoyer ce fichier à l’agent de Support pour analyse.

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>Passer en revue le rapport de diagnostics créé par PerfInsights

Le fichier **CollectedData\_aaaa-MM-jj\_hh\_mm\_ss.zip** généré par PerfInsights peut inclure un rapport HTML qui détaille les conclusions de PerfInsights. Pour passer en revue le rapport, développez le fichier **CollectedData\_aaaa-MM-jj\_hh\_mm\_ss.zip**, puis ouvrez le fichier **PerfInsights report.htm**.

Sélectionnez l’onglet **Conclusions**.

![onglet rechercher](media/how-to-use-perfInsights/findingtab.png)
![conclusions](media/how-to-use-perfInsights/findings.PNG)

**Remarques**

-   Les conclusions identifiées comme étant critiques sont des problèmes connus qui peuvent conduire à des problèmes de performances.

-   Les conclusions identifiées comme étant importantes représentent des configurations non optimales ne provoquant pas forcément de problèmes de performances.

-   Les conclusions identifiées comme étant informatives sont des instructions données à titre informatif uniquement.

Passez en revue les recommandations et les liens de toutes les conclusions importantes et critiques afin d’obtenir des informations plus détaillées et leurs répercussions sur les performances ou les meilleures pratiques associées aux configurations optimisées pour les performances.

### <a name="storage-tab"></a>Onglet Stockage

La section **Conclusions** affiche les différentes conclusions et recommandations relatives au stockage.

Les sections **DiskMap** et **VolumeMap** offrent un double point de vue sur les liens entre volumes logiques et disques physiques.

Dans la perspective PhysicalDisk (DiskMap), le tableau affiche tous les volumes logiques en cours d’exécution sur le disque. Dans l’exemple suivant, PhysicalDrive2 exécute deux volumes logiques créés sur plusieurs partitions (J et H) :

![onglet Données](media/how-to-use-perfInsights/disktab.png)

Dans la perspective Volume (*VolumeMap*), les tables affichent tous les disques physiques sous chaque volume logique. Pour les disques RAID/dynamiques, vous pouvez exécuter un volume logique sur plusieurs disques physiques. Dans l’exemple suivant, *C:\\mount* est un point de montage configuré en tant que *SpannedDisk* sur PhysicalDisks \#2 et \#3 :

![onglet Volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Onglet SQL

Si la machine virtuelle cible héberge toutes les instances de SQL Server, un onglet supplémentaire appelé **SQL** apparaît dans le rapport :

![onglet sql](media/how-to-use-perfInsights/sqltab.png)

Cette section contient un onglet « Conclusions » et des sous-onglets supplémentaires pour chacune des instances de SQL Server hébergées sur la machine virtuelle.

L’onglet « Conclusions » contient une liste de tous les problèmes de performances relatifs à SQL trouvés, ainsi que les recommandations correspondantes.

Dans l’exemple suivant, *PhysicalDrive0* (qui exécute le disque C) est affiché, car les fichiers *modeldev* et *modellog* se trouvent sur le disque C, et ils sont de types différents (par exemple, fichier de données et journal des transactions, respectivement) :

![loginfo](media/how-to-use-perfInsights/loginfo.png)

Les onglets spécifiques de l’instance de SQL Server contiennent une section générale qui affiche des informations de base sur l’instance sélectionnée, et d’autres sections pour plus d’informations, y compris les paramètres, les configurations et les options de l’utilisateur.

### <a name="diagnostic-tab"></a>Onglet Diagnostic
L’onglet Diagnostic contient des informations sur les principaux consommateurs d’UC, de disque et de mémoire sur la boîte de dialogue pour la durée de l’exécution de PerfInsights. Vous pouvez également obtenir d’autres informations utiles comme les correctifs critiques non présents dans le système, la liste des tâches et les événements système importants. 

## <a name="references-to-the-external-tools-used"></a>Références aux outils externes utilisés

### <a name="diskspd"></a>Diskspd

DISKSPD est un générateur de charge de stockage et un outil de test des performances des équipes d’ingénierie d’infrastructure Windows, Windows Server et serveur cloud. Pour plus d’informations, consultez [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

Xperf est un outil en ligne de commande qui permet de capturer des suivis à partir du Kit Windows Performance Toolkit.

Pour plus d’informations, consultez [Windows Performance Toolkit : Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Étapes suivantes

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>Charger les journaux de diagnostic et les rapports vers le Support Microsoft pour un examen approfondi

Lorsque vous travaillez avec le Support Microsoft, vous pouvez être invité à transmettre la sortie générée par PerfInsights pour faciliter le processus de dépannage.

L’agent de Support vous crée un espace de travail DTM, et vous recevez un e-mail comportant un lien vers le portail DTM (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm), ainsi qu’un ID d’utilisateur unique et un mot de passe.

Ce message sera envoyé à partir de **CTS Automated Diagnostics Services** (ctsadiag@microsoft.com).

![Exemple de message](media/how-to-use-perfInsights/supportemail.png)

Pour plus de sécurité, vous devez modifier votre mot de passe à la première utilisation.

Après vous être connecté à DTM, une boîte de dialogue s’affiche pour vous inviter à charger le fichier **CollectedData\_aaaa-MM-jj\_hh\_mm\_ss.zip** collecté par PerfInsights.
