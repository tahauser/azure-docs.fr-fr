---
title: Résolution des problèmes de stockage Azure avec les diagnostics et Message Analyzer | Microsoft Docs
description: Didacticiel illustrant la résolution des problèmes de bout en bout avec Azure Storage Analytics, AzCopy et Microsoft Message Analyzer
services: storage
documentationcenter: dotnet
author: tamram
manager: timlt
ms.assetid: 643372a3-1c07-4e88-b4ef-042512a43086
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.openlocfilehash: 324370ae18627a1985e6a40aec11ee2fa871e93b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Résolution des problèmes de bout en bout avec les métriques et la journalisation Stockage Azure, AzCopy et Message Analyzer
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnostic et résolution des problèmes sont essentiels pour la création et la prise en charge d'applications clientes avec Microsoft Azure Storage. En raison de la nature distribuée d'une application Azure, diagnostic et résolution des erreurs et des problèmes de performances peuvent être plus complexes que dans les environnements traditionnels.

Dans ce didacticiel, nous allons montrer comment identifier certaines erreurs qui peuvent affecter les performances et comment résoudre ces erreurs de bout en bout à l'aide des outils fournis par Microsoft et Azure Storage, afin d'optimiser l'application cliente.

Ce didacticiel fournit une exploration pratique d'un scénario de dépannage de bout en bout. Pour un guide conceptuel détaillé du dépannage des applications de stockage Azure, consultez la page [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Outils de résolution des problèmes dans les applications Azure Storage
Pour résoudre les problèmes des applications clientes utilisant Microsoft Azure Storage, vous pouvez faire appel à une combinaison d'outils afin de déterminer quand un problème s'est produit et quelle peut en être la cause. Ces outils incluent :

* **Azure Storage Analytics**. [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) fournit des métriques et une journalisation pour Azure Storage.
  
  * **Storage Metrics** assure le suivi des métriques de transaction et des métriques de capacité pour votre compte de stockage. Les métriques vous permettent de déterminer comment votre application s'exécute en fonction de plusieurs mesures différentes. Pour plus d'informations sur les types de métrique suivis par Storage Analytics, consultez la page [Schéma de table de métriques Storage Analytics](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) .
  * **Journalisation du stockage** enregistre chaque demande aux services de stockage Azure dans un journal côté serveur. Le journal assure le suivi des données détaillées de chaque demande, y compris l'opération effectuée, son statut et les informations de latence. Pour plus d’informations sur les données de demande et de réponse qui sont écrites dans les journaux par Storage Analytics, voir la page [Format de journal de Storage Analytics](/rest/api/storageservices/Storage-Analytics-Log-Format) .

* **Portail Azure**. Vous pouvez configurer les mesures et la journalisation pour votre compte de stockage dans le [portail Azure](https://portal.azure.com). Vous pouvez également afficher des tableaux et des graphiques qui illustrent le fonctionnement de votre application au fil du temps et configurer des alertes pour vous avertir si votre application ne fonctionne pas comme prévu pour une métrique spécifique.
  
    Pour plus d’informations sur la configuration de la surveillance dans le portail Azure, consultez la page [Surveillance d’un compte de stockage dans le portail Azure](storage-monitor-storage-account.md).
* **AzCopy**. Les journaux de serveur pour Azure Storage sont stockés sous forme d'objets blob ; vous pouvez donc utiliser AzCopy pour copier les objets blob de journal dans un répertoire local pour l'analyse à l'aide de Microsoft Message Analyzer. Pour plus d’informations sur AzCopy, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) .
* **Microsoft Message Analyzer**. Message Analyzer est un outil qui utilise des fichiers journaux et affiche les données des journaux dans un format visuel qui facilite le filtrage, la recherche et le regroupement des données de journaux dans des ensembles utiles dont vous pouvez vous servir pour analyser les erreurs et les problèmes de performances. Pour plus d'informations sur Message Analyzer, consultez la page [Guide d'exploitation de Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) .

## <a name="about-the-sample-scenario"></a>À propos de l'exemple de scénario
Pour ce didacticiel, nous allons examiner un scénario où les métriques Azure Storage indiquent un faible taux de réussite pour une application qui appelle le stockage Azure. La métrique de taux faible de réussite (indiquée en tant que **PercentSuccess** dans le [portail Azure](https://portal.azure.com) et dans les tables de mesures) assure le suivi des opérations qui réussissent, mais qui retournent un code d’état HTTP supérieur à 299. Dans les fichiers journaux de stockage côté serveur, ces opérations sont enregistrées avec un statut de transaction **ClientOtherErrors**. Pour plus d'informations sur les métriques de faible taux de réussite, consultez la rubrique [Les métriques indiquent une valeur PercentSuccess faible ou les entrées du journal d'analyse incluent des opérations avec un statut de transaction ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Les opérations d’Azure Storage peuvent renvoyer les codes d’état HTTP supérieurs à 299 dans le cadre de leur fonctionnement normal. Mais dans certains cas, ces erreurs indiquent que vous pouvez peut-être optimiser votre application cliente pour améliorer les performances.

Dans ce scénario, nous allons considérer comme un faible taux de réussite tout ce qui est inférieur à 100 %. Vous pouvez cependant choisir un autre niveau de métrique, en fonction de vos besoins. Nous vous recommandons, pendant le test de votre application, d'établir une tolérance de référence pour les métriques de performances clés. Par exemple, vous pouvez déterminer, sur la base de tests, que votre application doit avoir un taux de réussite de 90 % ou 85 %. Si vos données métriques montrent que l'application s'écarte de cette valeur, vous pouvez rechercher ce qui est susceptible de provoquer l'augmentation.

Pour notre exemple de scénario, une fois que nous avons établi que la métrique du taux de réussite est inférieure à 100 %, nous allons examiner les journaux afin de rechercher les erreurs correspondant aux métriques et les utiliser pour déterminer ce qui provoque le taux de réussite plus faible. Nous examinerons plus particulièrement les erreurs dans la plage 400. Ensuite, nous étudierons plus en détail les erreurs 404 (Introuvable).

### <a name="some-causes-of-400-range-errors"></a>Certaines causes d'erreurs dans la plage 400
Les exemples ci-dessous présentent un échantillon d'erreurs dans la plage 400 pour des demandes sur le stockage d'objets blob d'Azure, avec leurs causes possibles. Toutes ces erreurs, ainsi que des erreurs dans la plage 300 et la plage 500, peuvent contribuer à un faible taux de réussite.

Notez que les listes ci-dessous sont loin d'être complètes. Pour plus d'informations sur les erreurs générales dans Azure Storage et sur les erreurs propres à chacun des services de stockage, consultez la page [Codes d'état et d'erreur](http://msdn.microsoft.com/library/azure/dd179382.aspx) sur MSDN.

**Exemples de code d’état 404 (Introuvable)**

Se produit lorsqu'une opération de lecture sur un conteneur ou un objet blob échoue parce que l'objet blob ou le conteneur est introuvable.

* Se produit si un conteneur ou un objet blob a été supprimé par un autre client avant cette demande.
* Se produit si vous utilisez un appel d'API qui crée le conteneur ou l'objet blob après avoir vérifié s'il existe. Les API CreateIfNotExists effectuent un appel HEAD pour vérifier l'existence du conteneur ou de l'objet blob ; s'il n'existe pas, une erreur 404 est retournée, puis un second appel PUT est effectué pour écrire le conteneur ou l'objet blob.

**Exemples de code d'état 409 (Conflit)**

* Se produit si vous utilisez une API de création pour créer un conteneur ou un objet blob, sans vérification préalable de l'existence, et qu'un conteneur ou un objet blob avec ce nom existe déjà.
* Se produit si un conteneur est supprimé et que vous tentez de créer un nouveau conteneur portant le même nom avant que l'opération de suppression ne soit terminée.
* Se produit si vous spécifiez un bail sur un conteneur ou un objet blob, alors qu'il existe déjà un bail.

**Exemples de code d'état 412 (Échec de la précondition)**

* Se produit lorsque la condition spécifiée par un en-tête conditionnel n'a pas été remplie.
* Se produit lorsque l'ID de bail spécifié ne correspond pas à l'ID de bail sur le conteneur ou l'objet blob.

## <a name="generate-log-files-for-analysis"></a>Génération de fichiers journaux pour l'analyse
Dans ce didacticiel, nous allons utiliser Message Analyzer pour travailler avec trois types de fichiers journaux différents, mais vous pouvez travailler avec celui de votre choix :

* Le **journal du serveur**, qui est créé lorsque vous activez la journalisation Azure Storage. Le journal du serveur contient des données sur chaque opération appelée sur un des services Azure Storage - objet blob, file d'attente, table et fichier. Le journal du serveur indique quelle opération a été appelée et quel code d'état a été retourné, ainsi que d'autres détails sur la demande et la réponse.
* Le **journal du client .NET**, qui est créé lorsque vous activez la journalisation côté client à partir de votre application .NET. Le journal du client inclut des informations détaillées sur la façon dont le client prépare la demande, puis reçoit et traite la réponse.
* Le **journal de suivi du réseau HTTP**, qui collecte les données sur les données des demandes et réponses HTTP/HTTPS, notamment pour les opérations sur Azure Storage. Dans ce didacticiel, nous allons générer le suivi réseau via Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Configuration de la journalisation et des métriques côté serveur
Tout d’abord, nous allons devoir configurer la journalisation et les métriques Azure Storage afin d’avoir des données de l’application cliente à analyser. Vous pouvez configurer la journalisation et les métriques de plusieurs manières : via le [portail Azure](https://portal.azure.com), à l’aide de PowerShell ou par programme. Pour plus d’informations sur la configuration de la journalisation et des métriques, consultez les pages [Activation de Storage Metrics et affichage des données de métriques](http://msdn.microsoft.com/library/azure/dn782843.aspx) et [Activation de la journalisation du stockage et accès aux données de journal](http://msdn.microsoft.com/library/azure/dn782840.aspx) sur MSDN.

**Via le portail Azure**

Pour configurer la journalisation et les métriques pour votre compte de stockage à l’aide du [portail Azure](https://portal.azure.com), suivez les instructions de la page [Surveillance d’un compte de stockage dans le portail Azure](storage-monitor-storage-account.md).

> [!NOTE]
> Il n’est pas possible de définir des métriques par minute à l’aide du portail Azure. Toutefois, nous vous recommandons de les définir dans le cadre de ce didacticiel et pour examiner les problèmes de performances de votre application. Vous pouvez définir des métriques par minute à l’aide de PowerShell comme indiqué ci-dessous, par programme à l’aide de la bibliothèque cliente de stockage.
> 
> Notez que le portail Azure ne peut pas afficher les métriques par minute, mais seulement les métriques par heure.
> 
> 

**Via PowerShell**

Pour commencer à utiliser PowerShell pour Azure, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

1. Utilisez l'applet de commande [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0) pour ajouter votre compte d'utilisateur Azure dans la fenêtre PowerShell :
   
    ```powershell
    Add-AzureAccount
    ```

2. Dans la fenêtre **Connectez-vous à Microsoft Azure** , tapez l'adresse électronique et le mot de passe associés à votre compte. Azure authentifie et enregistre les informations d’identification, puis ferme la fenêtre.
3. Définissez le compte de stockage par défaut sur le compte de stockage que vous utilisez pour le didacticiel en exécutant ces commandes dans la fenêtre PowerShell :
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Activez la journalisation du stockage pour le service d'objets blob :
   
    ```powershell
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. Activez les métriques du stockage pour le service d’objets blob, en veillant à définir **-MetricsType** sur `Minute` :
   
    ```powershell
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Configuration de la journalisation côté client .NET
Pour configurer la journalisation côté client pour une application .NET, activez les diagnostics .NET dans le fichier de configuration de l'application (web.config ou app.config). Pour plus d’informations, consultez les pages [Journalisation côté client avec la bibliothèque cliente de stockage .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) et [Journalisation côté client avec le Kit de développement logiciel (SDK) Microsoft Azure Storage pour Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) sur MSDN.

Le journal côté client inclut des informations détaillées sur la manière dont le client prépare la demande, puis reçoit et traite la réponse.

La bibliothèque cliente de stockage stocke les données du journal côté client à l'emplacement spécifié dans le fichier de configuration de l'application (web.config ou app.config).

### <a name="collect-a-network-trace"></a>Collecte d'un suivi réseau
Vous pouvez utiliser Message Analyzer pour collecter un suivi réseau HTTP/HTTPS pendant l'exécution de votre application cliente. Message Analyzer utilise [Fiddler](http://www.telerik.com/fiddler) sur le serveur principal. Avant de collecter le suivi réseau, nous vous recommandons de configurer Fiddler pour enregistrer le trafic HTTPS non chiffré :

1. Installez [Fiddler](http://www.telerik.com/download/fiddler).
2. Lancez Fiddler.
3. Sélectionnez **Tools | Fiddler Options (Outils | Options de Fiddler)**.
4. Dans la boîte de dialogue Options (Options), vérifiez que **Capture HTTPS CONNECTs (Capturer les CONNECT HTTPS)** et **Decrypt HTTPS Traffic (Déchiffrer le trafic HTTPS)** sont tous deux activés, comme illustré ci-dessous.

![Configuration des options de Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Dans le didacticiel, collectez et enregistrez d'abord un suivi réseau dans Message Analyzer, puis créez une session d'analyse pour analyser le suivi et les journaux. Pour collecter un suivi réseau dans Message Analyzer :

1. Dans Message Analyzer, sélectionnez **File | Quick Trace | Unencrypted HTTPS (Fichier | Suivi rapide | HTTPS non chiffré)**.
2. Le suivi commence immédiatement. Cliquez sur **Arrêter** pour arrêter le suivi afin que nous puissions le configurer pour le suivi du trafic de stockage uniquement.
3. Cliquez sur **Modifier** pour modifier la session de suivi.
4. Cliquez sur le lien **Configure (Configurer)** à droite du fournisseur ETW **Microsoft-performance-WebProxy**.
5. Dans la boîte de dialogue **Advanced Settings (Paramètres avancés)**, cliquez sur l’onglet **Provider (Fournisseur)**.
6. Dans le champ **Filtre de nom d'hôte** , indiquez vos points de terminaison de stockage séparés par des espaces. Par exemple, vous pouvez spécifier vos points de terminaison comme suit (remplacez `storagesample` par le nom de votre compte de stockage) :

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Fermez la boîte de dialogue, puis cliquez sur **Redémarrer** pour commencer à collecter le suivi avec le filtre de nom d'hôte en place, afin que seul le trafic réseau d'Azure Storage soit inclus dans le suivi.

> [!NOTE]
> Après avoir terminé la collecte de votre suivi réseau, nous vous recommandons fortement de rétablir les paramètres que vous avez éventuellement modifiés dans Fiddler pour déchiffrer le trafic HTTPS. Dans la boîte de dialogue Fiddler Options (Options de Fiddler), désactivez les cases à cocher **Capture HTTPS CONNECTs (Capturer les CONNECT HTTPS)** et **Decrypt HTTPS Traffic (Déchiffrer le trafic HTTPS)**.
> 
> 

Pour plus de détails, voir la page [Utilisation des fonctionnalités de suivi réseau](http://technet.microsoft.com/library/jj674819.aspx) sur Technet.

## <a name="review-metrics-data-in-the-azure-portal"></a>Revue des données des métriques dans le portail Azure
Une fois que votre application a fonctionné pendant une période donnée, vous pouvez consulter les graphiques des métriques qui apparaissent dans le [portail Azure](https://portal.azure.com) pour observer la façon dont votre service a fonctionné.

D’abord, accédez à votre compte de stockage dans le Portail Azure. Par défaut, un graphique de surveillance avec la mesure **pourcentage de réussite** mesure s’affiche dans le panneau du compte. Si vous avez modifié précédemment le graphique pour afficher des mesures différentes, ajoutez la mesure **pourcentage de réussite**.

**Pourcentage de réussite** apparaît désormais dans le graphique de surveillance, ainsi que les autres métriques vous avez ajoutées. Dans le scénario que nous étudierons ensuite en analysant les journaux dans Message Analyzer, le taux de pourcentage de réussite est légèrement inférieur à 100 %.

Pour plus d’informations sur l’ajout et la personnalisation de graphiques de mesures, consultez [Personnalisation des graphiques de mesures](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Les données de vos mesures peuvent mettre un certain temps pour apparaître dans le portail Azure une fois que vous avez activé les mesures de stockage. C’est parce que les métriques horaires correspondant à l’heure précédente ne sont pas affichées dans le portail Azure tant que l’heure courante n’est pas écoulée. En outre, les mesures par minute ne sont pas actuellement affichées dans le portail Azure. Donc, selon le moment où vous activez des métriques, l’affichage des données correspondantes peut prendre jusqu’à deux heures.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Utiliser AzCopy pour copier les journaux de serveur dans un répertoire local
Azure Storage écrit les données des journaux de serveur dans des objets blob, tandis que les métriques sont écrites dans des tables. Les objets blob de journal sont disponibles dans le fameux conteneur `$logs` de votre compte de stockage. Les objets blob de journal sont nommés hiérarchiquement par année, mois, jour et heure, afin que vous puissiez localiser facilement la plage de temps que vous souhaitez examiner. Par exemple, dans le compte `storagesample`, le conteneur des objets blob de journal pour le 01/02/2015, de 8-9 h, est `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Les objets blob individuels dans ce conteneur sont nommés de manière séquentielle, à partir de `000000.log`.

Vous pouvez utiliser l'outil en ligne de commande AzCopy pour télécharger ces fichiers journaux côté serveur à l'emplacement de votre choix sur votre ordinateur local. Par exemple, vous pouvez utiliser la commande suivante pour télécharger les fichiers journaux pour les opérations sur des objets blob qui ont eu lieu le 2 janvier 2015 dans le dossier `C:\Temp\Logs\Server`. Remplacez `<storageaccountname>` par le nom de votre compte de stockage et `<storageaccountkey>` par la clé d'accès de votre compte :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy est disponible en téléchargement sur la page [Téléchargements Azure](https://azure.microsoft.com/downloads/) . Pour plus d’informations sur l’utilisation d’AzCopy, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md).

Pour plus d’informations sur le téléchargement des journaux côté serveur, consultez [Télécharger les données de journal de la journalisation du stockage](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Utilisation de Microsoft Message Analyzer pour analyser les données de journal
Microsoft Message Analyzer est un outil de capture, d'affichage et d'analyse du trafic de messagerie de protocole, des événements et des autres messages du système ou des applications dans les scénarios de résolution des problèmes et de diagnostic. Message Analyzer vous permet également de charger, d'agréger et d'analyser les données de journal et les fichiers de suivi enregistrés. Pour plus d'informations sur Message Analyzer, consultez la page [Guide d'exploitation de Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx).

Message Analyzer inclut des ressources pour Azure Storage qui vous aident à analyser les journaux du serveur, du client et du réseau. Dans cette section, nous aborderons l'utilisation de ces outils pour résoudre le problème de faible pourcentage de réussite dans les journaux de stockage.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Téléchargement et installation de Message Analyzer et des ressources Azure Storage
1. Téléchargez [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) depuis le Centre de téléchargement de Microsoft et exécutez le programme d'installation.
2. Lancez Message Analyzer.
3. Dans le menu **Tools (Outils)**, sélectionnez **Asset Manager (Gestionnaire de biens)**. Dans la boîte de dialogue **Asset Manager (Gestionnaire de biens)**, sélectionnez **Downloads (Téléchargements)**, puis filtrez sur **Azure Storage**. Vous verrez les ressources Azure Storage, comme illustré ci-après.
4. Cliquez sur **Synchroniser tous les éléments affichés** pour installer les ressources Azure Storage. Les ressources disponibles sont les suivantes :
   * **Règles de couleur Azure Storage :** celles-ci permettent de définir des filtres spéciaux qui utilisent des styles de texte, de couleur et de police pour mettre en surbrillance les messages contenant des informations spécifiques dans une trace.
   * **Graphiques Azure Storage :** il s’agit de représentations graphiques prédéfinies des données du journal du serveur. Notez que pour utiliser des graphiques Azure Storage à ce stade, vous pouvez uniquement charger le journal du serveur dans la grille d'analyse.
   * **Analyseurs Azure Storage :** ceux-ci analysent les journaux du client, du serveur et HTTP d'Azure Storage pour les afficher dans la grille d'analyse.
   * **Filtres d’Azure Storage :** il s’agit de critères prédéfinis que vous pouvez utiliser pour interroger vos données dans la grille d’analyse.
   * **Dispositions de vue Azure Storage :** il s'agit des dispositions de colonnes et des regroupements prédéfinis dans la grille d'analyse.
5. Redémarrez Message Analyzer après avoir installé les éléments multimédias.

![Gestionnaire de biens de l’analyseur de message](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Installez toutes les ressources Azure Storage indiquées dans le cadre de ce didacticiel.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>Importation de vos fichiers journaux dans Message Analyzer
Vous pouvez importer tous vos fichiers journaux enregistrés (côté serveur, côté client et réseau) dans une session de Microsoft Message Analyzer pour l'analyse.

1. Dans le menu **File (Fichier)** de Microsoft Message Analyzer, cliquez sur **New Session (Nouvelle session)**, puis sur **Blank Session (Session vide)**. Dans la boîte de dialogue **Nouvelle session** , entrez un nom pour votre session d'analyse. Dans le panneau **Session Details (Détails de la session)**, cliquez sur le bouton **Files (Fichiers)**.
2. Pour charger les données de suivi du réseau générées par Message Analyzer, cliquez sur **Add Files (Ajouter des fichiers)**, accédez à l’emplacement où vous avez enregistré votre fichier .matp à partir de votre session de suivi web, sélectionnez ce fichier, puis cliquez sur **Open (Ouvrir)**.
3. Pour charger les données du journal côté serveur, cliquez sur **Add Files (Ajouter des fichiers)**, accédez à l’emplacement où vous avez téléchargé vos journaux côté serveur, sélectionnez ceux de la période que vous souhaitez analyser, puis cliquez sur **Open (Ouvrir)**. Ensuite, dans le panneau **Session Details (Détails de la session)**, réglez le menu déroulant **Text Log Configuration (Configuration du journal texte)** correspondant à chaque fichier journal côté serveur sur **AzureStorageLog** pour vous assurer que Microsoft Message Analyzer peut analyser le fichier journal correctement.
4. Pour charger les données du journal côté client, cliquez sur **Add Files (Ajouter des fichiers)**, accédez à l’emplacement où vous avez enregistré vos journaux côté client, sélectionnez ceux que vous souhaitez analyser, puis cliquez sur **Ouvrir**. Ensuite, dans le panneau **Session Details (Détails de la session)**, réglez le menu déroulant **Text Log Configuration (Configuration du journal texte)** correspondant à chaque fichier journal côté client sur **AzureStorageClientDotNetV4** pour vous assurer que Microsoft Message Analyzer peut analyser le fichier journal correctement.
5. Dans la boîte de dialogue **New Session (Nouvelle session)**, cliquez sur **Start (Démarrer)** pour charger et analyser les données du journal. Les données du journal s'affichent dans la grille d'analyse de Message Analyzer.

L'illustration ci-dessous présente un exemple de session configurée avec les fichiers journaux du serveur, du client et de suivi du réseau.

![Configuration d’une session de Message Analyzer](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Notez que Message Analyzer charge les fichiers journaux en mémoire. Si l'ensemble des données des journaux est volumineux, vous devez le filtrer afin d'obtenir les meilleures performances de Message Analyzer.

Tout d'abord, déterminez l'intervalle de temps que vous souhaitez examiner en veillant à ce qu'il soit le plus petit possible. Dans de nombreux cas, vous devez examiner une période de quelques minutes ou quelques heures au maximum. Importez le plus petit ensemble de journaux qui puisse répondre à vos besoins.

Si vous avez encore une grande quantité de données de journal, vous pouvez spécifier un filtre de session afin de les filtrer avant de les charger. Dans la zone **Session Filter (Filtre de session)**, cliquez sur le bouton **Library (Bibliothèque)** pour choisir un filtre prédéfini. Par exemple, choisissez **Global Time Filter I (Filtre de temps global I)** dans les filtres Azure Storage pour filtrer sur un intervalle de temps. Vous pouvez ensuite modifier les critères de filtre afin de spécifier l'horodatage de début et de fin pour l'intervalle que vous souhaitez afficher. Vous pouvez également filtrer sur un code d'état spécifique ; par exemple, vous pouvez choisir de charger uniquement les entrées de journal où le code d'état est 404.

Pour plus d'informations sur l'importation des données de journal dans Microsoft Message Analyzer, consultez la page [Récupération des données des messages](http://technet.microsoft.com/library/dn772437.aspx) sur TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Utilisation de l'ID de demande client pour mettre en corrélation des données de fichiers journaux
La bibliothèque cliente de stockage Azure génère automatiquement un ID de demande client unique pour chaque demande. Cette valeur est écrite dans le journal du client, le journal du serveur et le suivi du réseau ; vous pouvez ainsi l'utiliser pour mettre en corrélation des données entre les trois journaux dans Message Analyzer. Pour plus d'informations sur l'ID de demande client, consultez la rubrique [ID de la demande client](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) .

Les sections ci-dessous expliquent comment utiliser des vues avec des dispositions préconfigurées et personnalisées pour mettre en corrélation et regrouper les données en fonction de l'ID de demande client.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Sélection d'une disposition de vue à afficher dans la grille d'analyse
Les ressources de stockage de Message Analyzer incluent les dispositions de vue Azure Storage, qui sont des vues préconfigurées que vous pouvez utiliser pour afficher vos données avec des regroupements et des colonnes utiles dans différents scénarios. Vous pouvez également créer des dispositions de vue personnalisées et les enregistrer pour pouvoir les réutiliser.

L’illustration ci-dessous présente le menu **View Layout (Disposition de vue)**, auquel vous pouvez accéder en sélectionnant **View Layout (Disposition de vue)** dans le ruban de la barre d’outils. Les dispositions de vue Azure Storage sont regroupées sous le nœud **Azure Storage** dans le menu. Vous pouvez rechercher `Azure Storage` dans la zone de recherche pour filtrer les disposition de vue et afficher uniquement celles d’Azure Storage. Vous pouvez également sélectionner l'étoile en regard d'une disposition de vue pour l'ajouter aux Favoris et l'afficher au début du menu.

![Menu Disposition de vue](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Pour commencer, sélectionnez **Regroupé par ClientRequestID et Module**. Cette disposition de vue regroupe les données des trois journaux, d'abord par ID de demande client, puis par fichier journal source (ou **Module** dans Message Analyzer). Avec cette vue, vous pouvez explorer de manière détaillée un ID de demande client particulier et afficher les données des trois fichiers journaux pour cette ID de demande client.

L'illustration ci-dessous présente cette disposition de vue appliquée à l'exemple de données de journal, avec l'affichage d'un sous-ensemble de colonnes. Vous pouvez voir que pour un ID de demande client particulier, la grille d'analyse affiche les données du journal du client, du journal du serveur et du suivi du réseau.

![Disposition de vue Azure Storage](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Différents fichiers journaux ont des colonnes différentes ; par conséquent, lorsque les données de plusieurs fichiers journaux sont affichées dans la grille d'analyse, il se peut que certaines colonnes ne contiennent pas toutes les données d'une ligne particulière. Par exemple, dans l’illustration ci-dessus, les lignes du journal du client n’affichent pas toutes les données des colonnes **Timestamp**, **TimeElapsed**, **Source** et **Destination**, car ces dernières existent dans le suivi du réseau mais pas dans le journal du client. De même, la colonne **Timestamp** affiche les données d’horodatage du journal du serveur, mais aucune donnée n’est affichée pour les colonnes **TimeElapsed**, **Source** et **Destination**, qui ne font pas partie du journal du serveur.
> 
> 

Outre les dispositions de vue Azure Storage, vous pouvez également définir et enregistrer vos propres dispositions. Vous pouvez également sélectionner d'autres champs souhaités pour le regroupement des données et enregistrer le regroupement dans le cadre de votre disposition personnalisée.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Application de règles de couleur à la grille d'analyse
Les ressources de stockage incluent également des règles de couleur, qui offrent un moyen visuel d'identifier les différents types d'erreur dans la grille d'analyse. Les règles de couleur prédéfinies s'appliquent aux erreurs HTTP et n'apparaissent par conséquent que pour le suivi du serveur et du réseau.

Pour appliquer des règles de couleur, sélectionnez **Règles de couleur** à partir du ruban de la barre d'outils. Les règles de couleur Azure Storage apparaissent dans le menu. Pour le didacticiel, sélectionnez **Erreurs du client (StatusCode entre 400 et 499)**, comme illustré dans l'image ci-dessous.

![Disposition de vue Azure Storage](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Outre les règles de couleur Azure Storage, vous pouvez également définir et enregistrer vos propres règles de couleur.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Regroupement et filtrage des données de journal pour rechercher les erreurs de la plage 400
Nous allons ensuite regrouper et filtrer les données de journal pour rechercher toutes les erreurs dans la plage 400.

1. Recherchez la colonne **StatusCode** dans la grille d’analyse, cliquez avec le bouton droit sur le titre de la colonne et sélectionnez **Group (Grouper)**.
2. Effectuez ensuite un regroupement sur la colonne **ClientRequestId** . Vous pouvez constater que les données dans la grille d'analyse sont désormais organisées par code d'état et par ID de demande client.
3. Affichez la fenêtre d'outil Filtre d'affichage si elle n'est pas déjà affichée. Dans le ruban de la barre d’outils, sélectionnez **Tool Windows (Fenêtres d’outil)**, puis **View Filter (Filtre d’affichage)**.
4. Pour filtrer les données de journal de manière à n’afficher que les erreurs de la plage 400, ajoutez les critères de filtre suivants dans la fenêtre **View Filter (Filtre d’affichage)**, puis cliquez sur **Apply (Appliquer)** :

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

L'illustration ci-dessous montre les résultats du regroupement et du filtre. Si vous développez le champ **ClientRequestID** sous le regroupement correspondant au code d'état 409, par exemple, une opération qui a provoqué ce code d'état s'affiche.

![Disposition de vue Azure Storage](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Après avoir appliqué ce filtre, vous verrez que les lignes du journal du client sont exclues, ce dernier n’incluant aucune colonne **StatusCode** . Pour commencer, nous allons revoir le serveur et les journaux de suivi du réseau pour rechercher les erreurs de la plage 404, puis revenir au journal du client pour examiner les opérations du client qui ont déclenché ces erreurs.

> [!NOTE]
> Vous pouvez filtrer sur la colonne **StatusCode** et continuer d'afficher les données des trois journaux, y compris du journal du client, si vous ajoutez au filtre une expression qui inclut des entrées de journal où le code d'état a la valeur null. Pour construire cette expression de filtre, utilisez :
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Ce filtre retourne toutes les lignes du journal du client et uniquement les lignes du journal du serveur et du journal HTTP où le code d'état est supérieur à 400. Si vous l'appliquez à la disposition de la vue regroupée par ID de demande client et module, vous pouvez rechercher ou faire défiler les entrées de journal pour rechercher celles où les trois journaux sont représentés.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Filtrage des données du journal pour rechercher les erreurs 404
Les ressources de stockage incluent les filtres prédéfinis que vous pouvez utiliser pour limiter les données du journal afin de trouver les erreurs ou les tendances que vous recherchez. Ensuite, nous allons appliquer deux filtres prédéfinis : un qui filtre les journaux de suivi du serveur et du réseau pour rechercher les erreurs 404 et l'autre qui filtre les données sur une période spécifiée.

1. Affichez la fenêtre d'outil Filtre d'affichage si elle n'est pas déjà affichée. Dans le ruban de la barre d’outils, sélectionnez **Tool Windows (Fenêtres d’outil)**, puis **View Filter (Filtre d’affichage)**.
2. Dans la fenêtre View Filter (Filtre d’affichage), sélectionnez **Library (Bibliothèque)** et effectuez une recherche sur `Azure Storage` pour trouver les filtres Azure Storage. Sélectionnez le filtre pour **Messages 404 (Introuvable) dans tous les journaux**.
3. Affichez de nouveau le menu **Library (Bibliothèque)**, puis localisez et sélectionnez **Global Time Filter (Filtre de temps global)**.
4. Modifiez l'horodatage indiqué dans le filtre en indiquant la plage que vous souhaitez afficher. Cela vous aidera à limiter la plage de données à analyser.
5. Le filtre doit apparaître comme dans l'exemple ci-dessous. Cliquez sur **Appliquer** pour appliquer le filtre à la grille d'analyse.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Disposition de vue Azure Storage](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analyse des données du journal
Maintenant que vous avez regroupé et filtré vos données, vous pouvez examiner les détails des demandes individuelles qui ont généré des erreurs 404. Dans la disposition de la vue actuelle, les données sont regroupées par ID de demande client, puis par source du journal. Dans la mesure où le filtrage porte sur les demandes où le champ StatusCode contient 404, nous voyons seulement les données de suivi du serveur et du réseau, pas les données du journal du client.

L'illustration ci-dessous montre une demande spécifique où une opération Get Blob a généré une erreur 404 parce que l'objet blob n'existait pas. Notez que certaines colonnes ont été supprimées de la vue standard pour afficher les données pertinentes.

![Journaux de suivi du serveur et du réseau filtrés](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Ensuite, nous allons mettre en corrélation cet ID de demande du client avec les données du journal du client pour voir quelles étaient les actions exécutées par le client lorsque l'erreur est survenue. Vous pouvez afficher une nouvelle vue Grille d'analyse pour cette session afin de consulter les données du journal du client, qui s'ouvre sous un deuxième onglet :

1. Tout d'abord, copiez la valeur du champ **ClientRequestId** dans le Presse-papiers. Pour ce faire, sélectionnez une ligne, recherchez le champ **ClientRequestId**, cliquez avec le bouton droit sur la valeur des données et choisissez **Copy 'ClientRequestId' (Copier 'ClientRequestId')**.
2. Dans le ruban de la barre d’outils, sélectionnez **New Viewer (Nouvelle visionneuse)** puis **Analysis Grid (Grille d’analyse)** pour ouvrir un nouvel onglet. Sous le nouvel onglet s'affichent toutes les données de vos fichiers journaux, sans regroupement, filtrage ni règles de couleur.
3. Dans le ruban de la barre d’outils, sélectionnez **View Layout (Disposition de vue)**, puis **All .NET Client Columns (Toutes les colonnes de client .NET)** sous la section **Azure Storage**. Cette disposition de la vue affiche les données à partir du journal du client ainsi que les journaux de suivi du serveur et du réseau. Par défaut, elle est triée en fonction de la colonne **MessageNumber** .
4. Ensuite, recherchez le journal du client pour l'ID de demande client. Dans le ruban de la barre d’outils, sélectionnez **Find Messages (Rechercher des messages)**, puis spécifiez un filtre personnalisé sur l’ID de demande client dans le champ **Find (Rechercher)**. Utilisez cette syntaxe pour le filtre en indiquant votre propre ID de demande client :

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer localise et sélectionne la première entrée de journal dans laquelle les critères de recherche correspondent à l'ID de demande client. Dans le journal du client, il existe plusieurs entrées pour chaque ID de demande client ; vous pouvez donc les regrouper sur le champ **ClientRequestId** pour pouvoir les afficher toutes ensemble. L'illustration ci-dessous montre tous les messages du journal du client pour l'ID de demande client spécifié.

![Journal client affichant des erreurs 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

À l'aide des données affichées dans les dispositions de vue sous ces deux onglets, vous pouvez analyser les données de la demande pour déterminer ce qui peut avoir provoqué l'erreur. Vous pouvez également consulter les demandes qui ont précédé celle-ci pour voir si un événement antérieur a pu conduire à l'erreur 404. Par exemple, vous pouvez consulter les entrées du journal du client précédant cet ID de demande client pour déterminer si l'objet blob a été supprimé, ou si l'erreur est due à l'appel par l'application cliente d'une API CreateIfNotExists sur un conteneur ou un objet blob. Dans le journal du client, vous pouvez trouver l’adresse de l’objet blob dans le champ **Description (Description)**. Dans les journaux de suivi du serveur et du réseau, cette information s’affiche dans le champ **Summary (Résumé)**.

Une fois que vous connaissez l'adresse de l'objet blob qui a généré l'erreur 404, vous pouvez effectuer un examen plus approfondi. Si vous recherchez les entrées du journal pour les autres messages associés à des opérations sur le même objet blob, vous pouvez vérifier si le client a précédemment supprimé l'entité.

## <a name="analyze-other-types-of-storage-errors"></a>Analyse des autres types d'erreur de stockage
Maintenant que vous êtes familiarisé avec Message Analyzer pour analyser vos données de journal, vous pouvez analyser d'autres types d'erreur avec les dispositions de vue, les règles de couleur , la recherche et le filtrage. Les tableaux ci-dessous répertorient certains problèmes que vous pouvez rencontrer et les critères de filtre que vous pouvez utiliser pour les localiser. Pour plus d'informations sur la construction des filtres et le langage de filtrage de Message Analyzer, consultez la page [Filtrage des données de message](http://technet.microsoft.com/library/jj819365.aspx).

| Pour examiner... | Utiliser l'expression de filtre... | L’expression s’applique au journal (client, serveur, réseau, tout) |
| --- | --- | --- |
| Retards inattendus de la remise des messages dans une file d'attente |AzureStorageClientDotNetV4.Description contient "Retrying failed operation." |Client |
| HTTP, augmentation de la valeur PercentThrottlingError |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |Réseau |
| Augmentation de la valeur PercentTimeoutError |HTTP.Response.StatusCode   == 500 |Réseau |
| Augmentation de la valeur PercentTimeoutError (tous) |*StatusCode   == 500 |Tous |
| Augmentation de la valeur PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Client |
| Messages HTTP 403 (refusé) |HTTP.Response.StatusCode   == 403 |Réseau |
| Messages HTTP 404 (non trouvé) |HTTP.Response.StatusCode   == 404 |Réseau |
| 404 (tous) |*StatusCode   == 404 |Tous |
| Problème d'autorisation de la signature d'accès partagé (SAS) |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |Réseau |
| Messages HTTP 409 (conflit) |HTTP.Response.StatusCode   == 409 |Réseau |
| 409 (tous) |*StatusCode   == 409 |Tous |
| Les entrées à faible valeur PercentSuccess ou du journal   d’analyse incluent des opérations avec un statut de transaction ClientOtherErrors |AzureStorageLog.RequestStatus ==   "ClientOtherError" |Serveur |
| Avertissement   de Nagle |((AzureStorageLog.EndToEndLatencyMS   - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS *   1.5)) et (AzureStorageLog.RequestPacketSize <1460) et (AzureStorageLog.EndToEndLatencyMS -   AzureStorageLog.ServerLatencyMS >= 200) |Serveur |
| Plage horaire   dans les journaux serveur et réseau |#Timestamp   >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39 |Serveur, réseau |
| Plage horaire   dans les journaux serveur |AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 et AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 |Serveur |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur les scénarios de résolution des problèmes de bout en bout dans Azure Storage, consultez les ressources suivantes :

* [Analyser, diagnostiquer et dépanner Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Analyse du stockage](http://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)
* [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
* [Guide d'exploitation de Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx)
