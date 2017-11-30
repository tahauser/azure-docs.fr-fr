---
title: Suivi des modifications avec Azure Log Analytics | Microsoft Docs
description: "La solution Change Tracking dans Log Analytics permet d’identifier les modifications apportées aux logiciels et au service Windows qui se produisent dans votre environnement."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f8040d5d-3c89-4f0c-8520-751c00251cb7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 528b569ff9ffb3659e9210ea70e3aa06921cfe0d
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="track-software-changes-in-your-environment-with-the-change-tracking-solution"></a>Suivi des modifications apportées aux logiciels dans votre environnement grâce à la solution de suivi des modifications

![Symbole Change Tracking](./media/log-analytics-change-tracking/change-tracking-symbol.png)

Cet article vous permet d’utiliser la solution Change Tracking de Log Analytics pour identifier facilement les modifications dans votre environnement. La solution suit les modifications apportées aux logiciels Windows et Linux, aux clés de Registre, fichiers et services Windows et aux démons Linux. Identifier les modifications de configuration peut vous aider à identifier les problèmes opérationnels.

Vous installez la solution pour mettre à jour le type d’agent que vous avez installé. Les modifications apportées aux logiciels, services Windows et démons Linux installés sur les serveurs analysés sont lues. Ensuite, les données sont envoyées au service Log Analytics dans le cloud pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

* Vous devez disposer d’un agent [Windows](log-analytics-windows-agents.md), d'un [Operations Manager](log-analytics-om-agents.md) ou d'un agent [Linux](log-analytics-linux-agents.md) sur chaque ordinateur où vous souhaitez analyser les modifications.
* Ajoutez la solution Change Tracking à votre espace de travail OMS à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ChangeTrackingOMS?tab=Overview). Vous pouvez également ajouter la solution en utilisant les informations contenues dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Aucune configuration supplémentaire n'est nécessaire.

### <a name="configure-linux-files-to-track"></a>Configuration des fichiers Linux à suivre
Suivez les étapes ci-dessous pour configurer les fichiers à suivre sur les ordinateurs Linux.

1. Dans le portail OMS, cliquez sur **Paramètres** (symbole d'engrenage).
2. Sur la page **Paramètres** cliquez sur **Données**, puis sur **Suivi des fichiers Linux**.
3. Sous Suivi des modifications des fichiers Linux, tapez le chemin d’accès complet, y compris le nom du fichier que vous souhaitez suivre, puis cliquez sur le symbole **Ajouter**. Par exemple : « /etc/*.conf »
4. Cliquez sur **Enregistrer**.  

> [!NOTE]
> Le suivi des fichiers Linux est doté de fonctionnalités supplémentaires, y compris le suivi du répertoire, la récursivité dans les répertoires et le suivi des caractères génériques.

### <a name="configure-windows-files-to-track"></a>Configuration des fichiers Windows à suivre
Utilisez les étapes suivantes pour configurer les fichiers à suivre sur les ordinateurs Windows.

1. Dans le portail OMS, cliquez sur **Paramètres** (symbole d'engrenage).
2. Sur la page **Paramètres** cliquez sur **Données**, puis sur **Suivi des fichiers Windows**.
3. Sous Suivi des modifications des fichiers Windows, tapez le chemin d’accès complet, y compris le nom du fichier que vous souhaitez suivre, puis cliquez sur le symbole **Ajouter**. Par exemple : C:\Program Files (x86)\Internet Explorer\iexplore.exe ou C:\Windows\System32\drivers\etc\hosts.
4. Cliquez sur **Save**.  
   ![Suivi des modifications des fichiers Windows](./media/log-analytics-change-tracking/windows-file-change-tracking.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurer les clés de Registre Windows pour effectuer le suivi
Utilisez les étapes suivantes pour configurer les clés de Registre à suivre sur les ordinateurs Windows.

1. Dans le portail OMS, cliquez sur **Paramètres** (symbole d'engrenage).
2. Sur la page **Paramètres**, cliquez sur **Données**, puis sur **Suivi du Registre Windows**.
3. Sous Suivi des modifications du Registre Windows, tapez la clé que vous souhaitez suivre, puis cliquez sur le symbole **Ajouter**.
4. Cliquez sur **Save**.  
   ![Suivi des modifications du Registre Windows](./media/log-analytics-change-tracking/windows-registry-change-tracking.png)

### <a name="explanation-of-linux-file-collection-properties"></a>Explication des propriétés de collection de fichiers Linux
1. **Type**
   * **Fichier** (rapport des métadonnées du fichier : taille, date de modification, code de hachage, etc.)
   * **Répertoire** (rapport des métadonnées du fichier : taille, date de modification, etc.)
2. **Liens** (gestion des références de lien symbolique Linux pour les autres fichiers ou répertoires)
   * **Ignorer** (ignorer les liens symboliques pendant les opérations de récursivité pour ne pas inclure les fichiers/répertoires référencés)
   * **Suivre** (suivre les liens symboliques pendant les opérations de récursivité pour inclure aussi les fichiers/répertoires référencés)
   * **Gérer** (suivre les liens symboliques et modifier le traitement des contenus renvoyés)

   > [!NOTE]   
   > L’option permettant de « Gérer » les liens n’est pas recommandée. L’extraction du contenu du fichier n’est pas prise en charge.

3. **Traiter récursivement** (traiter récursivement via les niveaux de dossiers et suivre tous les fichiers respectant l’instruction de chemin d’accès)
4. **Sudo** (activer les répertoires ou fichiers d’accès qui nécessitent un privilège sudo)

### <a name="limitations"></a>Limites
Actuellement, la solution Change Tracking ne prend pas en charge les éléments suivants :

* Dossiers (répertoires) pour le suivi des fichiers Windows
* Récursivité pour le suivi des fichiers Windows
* Caractères génériques pour le suivi des fichiers Windows
* Variables de chemin d’accès
* Systèmes de fichiers réseau
* Contenu du fichier

Autres limitations :

* La colonne **Taille maximale des fichiers** et ses valeurs ne sont pas utilisées dans l’implémentation actuelle.
* Si vous collectez plus de 2500 fichiers dans le cycle de collecte de 30 minutes, les performances de la solution peuvent être dégradées.
* Lorsque le trafic réseau est élevé, l'affichage des enregistrements de modifications peut prendre jusqu'à six heures.
* Si vous modifiez la configuration lorsqu’un ordinateur est arrêté, l’ordinateur risque de publier des modifications de fichiers appartenant à la configuration précédente.

### <a name="known-issues"></a>Problèmes connus
La solution Change Tracking connaît les problèmes suivants :
* Les mises à jour de correctif logiciel ne sont pas collectées pour les machines Windows 10 Creators Update et Windows Server 2016 Core RS3.

## <a name="change-tracking-data-collection-details"></a>Détails de la collecte de données de suivi des modifications
Le suivi des modifications collecte des métadonnées d’inventaire logiciel et de service Windows à l’aide des agents que vous avez activés.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte en vue du suivi des modifications.

| plateforme | Agent direct | Agent Operations Manager | Agent Linux | Azure Storage | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Windows et Linux | &#8226; | &#8226; | &#8226; |  |  | &#8226; | Entre 5 minutes et 50 minutes, selon le type de modification. Pour plus d’informations, consultez le tableau suivant. |


Le tableau suivant indique la fréquence de collecte de données selon les types de modification.

| **type de modification** | **frequency** | **L’agent****envoie-t-il****les différences lorsqu’il en détecte ?** |
| --- | --- | --- |
| Registre Windows | 50 minutes | Non |
| Fichier Windows | 30 minutes | Oui. Une capture instantanée est envoyée si aucune modification n’est relevée dans les 24 heures. |
| Fichier Linux | 15 minutes | Oui. Une capture instantanée est envoyée si aucune modification n’est relevée dans les 24 heures. |
| Services Windows | 30 minutes | Oui, toutes les 30 minutes lorsque des modifications sont détectées. Une capture instantanée est envoyée toutes les 24 heures, quelle que soit la modification. La capture instantanée est donc envoyée même en l’absence de modification. |
| Démons Linux | 5 minutes | Oui. Une capture instantanée est envoyée si aucune modification n’est relevée dans les 24 heures. |
| Logiciels Windows | 30 minutes | Oui, toutes les 30 minutes lorsque des modifications sont détectées. Une capture instantanée est envoyée toutes les 24 heures, quelle que soit la modification. La capture instantanée est donc envoyée même en l’absence de modification. |
| Logiciels Linux | 5 minutes | Oui. Une capture instantanée est envoyée si aucune modification n’est relevée dans les 24 heures. |

### <a name="registry-key-change-tracking"></a>Suivi des modifications des clés de Registre

Log Analytics assure le suivi et la surveillance du Registre Windows avec la solution de suivi des modifications. Le contrôle des modifications apportées aux clés de Registre a pour objectif d’identifier les points d’extension où peuvent s’activer du code tiers et des logiciels malveillants. La liste suivante indique les clés de Registre qui sont suivies par défaut par la solution, ainsi que les raisons de ce suivi.

- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup
    - Surveille les scripts qui s’exécutent au démarrage.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown
    - Surveille les scripts qui s’exécutent à l’arrêt.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run
    - Surveille les clés qui sont chargées avant que l’utilisateur ne se connecte à son compte Windows. La clé est utilisée pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components
    - Surveille les modifications apportées aux paramètres d’application.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers
    - Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers
    - Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Background\ShellEx\ContextMenuHandlers
    - Surveille les entrées courantes de démarrage automatique qui se raccordent directement à l’Explorateur Windows et s’exécutent généralement in-process avec Explorer.exe.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Surveille l’enregistrement du gestionnaire des icônes de recouvrement.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
    - Surveille l’enregistrement du gestionnaire des icônes de recouvrement pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
    - Surveille la présence de nouveaux plug-ins d’objet application d’assistance du navigateur pour Internet Explorer. Utilisé pour accéder à l’objet DOM (Document Object Model) de la page actuelle et contrôler la navigation pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions
    - Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils personnalisés et les boutons de barre d’outils personnalisés.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions
    - Surveille les nouvelles extensions Internet Explorer, notamment les menus d’outils personnalisés et les boutons de barre d’outils personnalisés pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits.
- HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Surveille les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc. Analogue à la section [pilotes] du fichier SYSTEM.INI.
- HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    - Surveille les pilotes 32 bits associés à wavemapper, wave1 et wave2, msacm.imaadpcm, .msadpcm, .msgsm610 et vidc pour les programmes 32 bits s’exécutant sur des ordinateurs 64 bits. Analogue à la section [pilotes] du fichier SYSTEM.INI.
- HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls
    - Surveille la liste des DLL système connues ou couramment utilisées ; ce système empêche quiconque d’exploiter des autorisations faibles de répertoire d’application en déposant des versions de type cheval de Troie des DLL système.
- HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify
    - Surveille la liste des packages capables de recevoir des notifications d’événements de la part de Winlogon, le modèle de prise en charge de l’ouverture de session interactive du système d’exploitation Windows.


## <a name="use-change-tracking"></a>Utilisation du suivi des modifications
Une fois la solution installée, vous pouvez afficher le résumé des modifications pour vos serveurs analysés à l’aide de la vignette **Suivi des modifications** sur la page **Vue d’ensemble** d’OMS.

![image de la vignette de suivi des modifications](./media/log-analytics-change-tracking/change-tracking-tile.png)

Vous pouvez afficher les modifications apportées à votre infrastructure, puis étudier en détail les informations pour les catégories suivantes :

* Modifications selon le type de configuration pour les logiciels et services Windows
* Modifications logicielles des applications et mises à jour pour chaque serveur
* Nombre total de modifications logicielles pour chaque application
* Packages Linux
* Modifications de services Windows pour chaque serveur
* Modifications du démon Linux

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/change-tracking-dash01.png)

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/change-tracking-dash02.png)

### <a name="to-view-changes-for-any-change-type"></a>Affichage des modifications pour tout type de modification
1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Suivi des modifications**.
2. Sur le tableau de bord **Suivi des modifications**, passez en revue les informations résumées de l’un des panneaux de type de modification, puis cliquez sur l’une d’entre elles pour afficher des informations détaillées dans la page **Recherche de journal**.
3. Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez les [recherches de journaux dans Log Analytics](log-analytics-log-searches.md) pour afficher les données détaillées du suivi des modifications.
