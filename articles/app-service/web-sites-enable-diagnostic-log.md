---
title: Activer la journalisation des diagnostics pour les applications web dans Azure App Service
description: Découvrez comment activer la journalisation de diagnostic et ajouter la fonctionnalité d’instrumentation à votre application, mais aussi comment accéder aux informations enregistrées par Azure.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.openlocfilehash: 8dc955b3556477e04e6ef3e92b1c7dbe82ac7f35
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Activer la journalisation des diagnostics pour les applications web dans Azure App Service
## <a name="overview"></a>Vue d'ensemble
Azure fournit des diagnostics intégrés pour aider au débogage d'une [application Web App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Cet article vous explique comment activer la journalisation de diagnostic et ajouter la fonctionnalité d’instrumentation à votre application, et comment accéder aux informations enregistrées par Azure.

Cet article utilise le [portail Azure](https://portal.azure.com), Azure PowerShell et l’interface de ligne de commande Azure pour l’exploitation des journaux de diagnostic. Pour plus d’informations sur l’utilisation de journaux de diagnostic avec Visual Studio, consultez [Résolution des problèmes Azure dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnostics de serveur Web et diagnostics d’application
Les applications web App Service fournissent des fonctionnalités de diagnostic pour les informations de journalisation provenant du serveur Web et de l'application web. Ces informations sont réparties, en toute logique, en **diagnostics de serveur web** et en **diagnostics d’application**.

### <a name="web-server-diagnostics"></a>Diagnostics de serveur web
Vous pouvez activer ou désactiver les types de journaux suivants :

* **Messages d’erreur détaillés** : informations d’erreur détaillées pour les codes d’état HTTP qui indiquent un échec (code d’état 400 ou supérieur). Il peut s’agir d’informations permettant de déterminer la raison pour laquelle le serveur a renvoyé le code d’erreur.
* **Suivi des demandes ayant échoué** : informations détaillées sur les demandes qui ont échoué, y compris une trace des composants IIS utilisés pour traiter la demande et la durée dans chaque composant. Ces informations peuvent se révéler utiles si vous essayez d’améliorer les performances du site ou d’isoler la cause d’une erreur HTTP spécifique.
* **Journalisation du serveur Web** : informations sur les transactions HTTP à l’aide du [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ces informations peuvent se révéler utiles pour déterminer les métriques globales d’un site, comme le nombre de demandes traitées ou le nombre de demandes émanant d’une adresse IP spécifique.

### <a name="application-diagnostics"></a>Diagnostic d'application
Le diagnostic d'application vous permet de capturer des informations générées par une application Web. Les applications ASP.NET peuvent utiliser la classe [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) pour enregistrer des informations dans le journal de diagnostic d'application. Par exemple : 

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Au moment de l’exécution, vous pouvez récupérer ces journaux pour vous aider durant le dépannage. Pour plus d’informations, consultez la page [Résolution des problèmes des applications web Azure dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Les applications web App Service journalisent également les informations de déploiement lorsque vous publiez du contenu dans une application web. Cette opération est automatique et il n’existe aucun paramètre de configuration pour la journalisation du déploiement. Cette dernière vous permet de déterminer le motif d'échec d'un déploiement. Si vous utilisez, par exemple, un script de déploiement personnalisé, vous pouvez recourir à la journalisation de déploiement pour déterminer la cause de l'échec du script.

## <a name="enablediag"></a>Activation des diagnostics
Pour activer les diagnostics sur le [portail Azure](https://portal.azure.com), accédez à la page de votre application web, puis cliquez sur **Paramètres > Journaux de diagnostics**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Partie des journaux](./media/web-sites-enable-diagnostic-log/logspart.png)

Quand vous activez les **diagnostics d’application**, choisissez également le **niveau**. Ce paramètre vous permet de filtrer les données capturées selon le critère **Information**, **Avertissement** ou **Erreur**. Vous pouvez également sélectionner le niveau **Détaillé** pour journaliser toutes les informations générées par l’application.

> [!NOTE]
> Contrairement à la modification du fichier web.config, le fait d'activer le diagnostic d'application ou de modifier les niveaux de journalisation de diagnostic ne recycle pas le domaine dans lequel l'application s'exécute.
>
>

Pour **Journal des applications**, vous pouvez temporairement activer l’option système à des fins de débogage. Cette option se désactive automatiquement au bout de 12 heures. Vous pouvez également activer l’option de stockage Blob pour sélectionner un conteneur d’objets blob pour y écrire des journaux.

Pour **Journalisation du serveur web**, vous pouvez sélectionner **Stockage** ou **Système de fichiers**. Si vous sélectionnez le **stockage**, vous avez également la possibilité de sélectionner un compte de stockage, puis un conteneur d’objets blob dans lequel les journaux sont écrits. 

Si vous stockez les journaux sur le système de fichiers, vous pouvez accéder à ces fichiers par FTP ou les télécharger sous forme d’archive ZIP en utilisant Azure PowerShell ou l’interface de ligne de commande Azure (Azure CLI).

Par défaut, les journaux ne sont pas automatiquement supprimés (à l’exception du **Journal des applications (Système de fichiers)**). Pour supprimer automatiquement les journaux, définissez le champ **Période de rétention (jours)**.

> [!NOTE]
> Si vous [régénérez les clés d’accès de votre compte de stockage](../storage/common/storage-create-storage-account.md), vous devez réinitialiser la configuration de journalisation correspondante pour utiliser les clés mises à jour. Pour ce faire :
>
> 1. Sous l’onglet **Configurer**, définissez la fonctionnalité de journalisation correspondante sur **Désactivé**. Enregistrez votre paramètre.
> 2. Réactivez la journalisation de l’objet blob ou de la table du compte de stockage. Enregistrez votre paramètre.
>
>

Vous pouvez activer simultanément toute combinaison de système de fichiers, stockage de tables ou stockage d’objets blob. Des configurations de niveau de journalisation individuelles sont également possibles. Vous pouvez, par exemple, consigner les erreurs et les avertissements dans le stockage d'objets blob dans le cadre d'une solution de journalisation à long terme, tout en activant un niveau de journalisation détaillé du système de fichiers.

Bien que ces trois emplacements de stockage fournissent les mêmes informations de base pour les événements consignés, le **stockage table** et le **stockage blob** consignent davantage d’informations, telles que l’ID d’instance, l’ID de thread et un horodatage plus précis, que lorsque vous optez pour la journalisation dans le **système de fichiers**.

> [!NOTE]
> Les informations stockées dans le **stockage table** ou le **stockage blob** ne sont accessibles qu’à l’aide d’un client de stockage ou d’une application capable d’utiliser directement ces systèmes de stockage. Par exemple, Visual Studio 2013 contient un Explorateur de stockage qui peut être utilisé pour explorer un système de stockage de tables ou d'objets blob, tandis que HDInsight peut accéder aux données stockées dans un stockage d'objets blob. Vous pouvez également écrire une application qui accède à Azure Storage en utilisant l'un des [Kits de développement logiciel (SDK) Azure](/downloads/#).
>
> [!NOTE]
> Les diagnostics peuvent également être activés à partir du module Azure PowerShell via la cmdlet **Set-AzureWebsite** . Si vous n’avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d’Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

## <a name="download"></a> Téléchargement de journaux
Les informations de diagnostic stockées dans le système de fichiers d’application web sont directement accessibles via FTP. Vous pouvez également les télécharger sous la forme d’une archive ZIP en utilisant Azure PowerShell ou l’interface de ligne de commande Azure.

La structure de répertoires dans laquelle les journaux sont stockés est la suivante :

* **Journaux d'application** : /LogFiles/Application/. Ce dossier contient un ou plusieurs fichiers texte contenant des informations générées dans le cadre de la journalisation des applications.
* **Suivi des demandes ayant échoué** : /LogFiles/W3SVC#########/. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer.
* **Journaux d'erreurs détaillés** : /LogFiles/DetailedErrors/. Ce dossier contient un ou plusieurs fichiers .htm fournissant des informations détaillées sur toute erreur HTTP qui s'est produite.
* **Journaux des serveurs Web** : /LogFiles/http/RawLogs. Ce dossier contient un ou plusieurs fichiers texte au format [de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Journaux de déploiement** : /LogFiles/Git. Ce dossier contient les journaux générés par les processus de déploiement internes utilisés par les applications web Azure, ainsi que les journaux des déploiements Git. Vous trouverez également les journaux de déploiement sous D:\home\site\deployments.

### <a name="ftp"></a>FTP

Pour ouvrir une connexion FTP sur le serveur FTP de votre application, consultez [Déployer votre application dans Azure App Service avec FTP/S](app-service-deploy-ftp.md).

Une fois connecté au serveur FTP/S de votre application web, ouvrez le dossier **LogFiles**, où les fichiers journaux sont stockés.

### <a name="download-with-azure-powershell"></a>Téléchargement avec Azure PowerShell
Pour télécharger les fichiers journaux, démarrez une nouvelle instance du module Azure PowerShell et utilisez la commande suivante :

    Save-AzureWebSiteLog -Name webappname

Cette commande enregistre les journaux de l’application web spécifiée par le paramètre **-Name** dans un fichier nommé **logs.zip** du répertoire actif.

> [!NOTE]
> Si vous n’avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d’Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="download-with-azure-command-line-interface"></a>Téléchargement avec l’interface de ligne de commande Azure
Pour télécharger les fichiers journaux à l’aide de l’interface de ligne de commande Azure, ouvrez une nouvelle session d’invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

    az webapp log download --resource-group resourcegroupname --name webappname

Cette commande enregistre les journaux de l’application web nommée « webappname » dans un fichier **diagnostics.zip** du répertoire actif.

> [!NOTE]
> Si vous n’avez pas installé ou configuré l’interface de ligne de commande Azure (CLI Azure) de manière à utiliser votre abonnement Azure, consultez la page [Utilisation de l’interface de ligne de commande Azure](../cli-install-nodejs.md).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Affichage des journaux dans Application Insights
Visual Studio Application Insights fournit des outils de filtrage et de recherche dans les journaux, mais aussi de mise en corrélation des journaux avec les requêtes et d’autres événements.

1. Ajoutez le Kit de développement logiciel Application Insights à votre projet dans Visual Studio.
   * Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis sélectionnez Ajouter Application Insights. L’interface vous guide tout au long de la création de la ressource Application Insights. [En savoir plus](../application-insights/app-insights-asp-net.md)
2. Ajoutez le package de l’écouteur de suivi à votre projet.
   * Cliquez avec le bouton droit sur votre projet et choisissez Gérer les packages NuGet. Sélectionnez `Microsoft.ApplicationInsights.TraceListener` [En savoir plus](../application-insights/app-insights-asp-net-trace-logs.md)
3. Téléchargez votre projet et exécutez-le pour générer des données de journal.
4. Dans le [portail Azure](https://portal.azure.com/), accédez à votre nouvelle ressource Application Insights, puis ouvrez la fonction de **recherche**. Vous devriez voir vos données de journal, ainsi que la requête, l’utilisation et les autres mesures de télémétrie. Vous devrez parfois patienter quelques minutes pour accéder à certaines mesures de télémétrie : dans ce cas, cliquez sur Actualiser. [En savoir plus](../application-insights/app-insights-diagnostic-search.md)

[En savoir plus sur le suivi des performances avec Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> Diffusion en continu des journaux
Lors du développement d’une application, il est utile de visualiser des informations de journalisation en temps quasi réel. Vous pouvez diffuser ces informations vers votre environnement de développement en utilisant Azure PowerShell ou l’interface de ligne de commande Azure.

> [!NOTE]
> Certains types de mémoire tampon de journalisation sont écrits dans le fichier journal. Dès lors, il se peut que les événements apparaissent de manière désordonnée dans le flux. Ainsi, il est possible qu'une entrée du journal d'application qui se produit lorsqu'un utilisateur visite une page soit affichée dans le flux avant l'entrée de journal HTTP correspondante pour la demande de page.
>
> [!NOTE]
> Pendant le streaming des journaux, les informations écrites dans tout fichier texte stocké dans le dossier **D:\\home\\LogFiles\\** sont également diffusées.
>
>

### <a name="streaming-with-azure-powershell"></a>Diffusion d'informations en continu avec Azure PowerShell
Pour diffuser des informations de journalisation en continu, démarrez une nouvelle instance du module Azure PowerShell et utilisez la commande suivante :

    Get-AzureWebSiteLog -Name webappname -Tail

Cette commande établit une connexion avec l’application web spécifiée par le paramètre **-Name**, puis diffuse les informations vers la fenêtre PowerShell à mesure que des événements de journalisation se produisent sur l’application web. Toute information enregistrée dans un fichier ayant l’extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **-Message** . Par exemple : 

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **-Path** . Par exemple : 

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Pour afficher la liste des chemins disponibles, utilisez le paramètre -ListPath.

> [!NOTE]
> Si vous n’avez pas installé ou configuré Azure PowerShell de manière à utiliser votre abonnement Azure, consultez la page [Utilisation d’Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Diffusion d’informations en continu avec l’interface de ligne de commande Azure
Pour diffuser des informations de journalisation, ouvrez une nouvelle session d'invite de commandes, PowerShell, Bash ou Terminal, puis entrez la commande suivante :

    az webapp log tail --name webappname --resource-group myResourceGroup

Cette commande établie une connexion avec l’application web nommée « webappname », puis diffuse les informations vers la fenêtre à mesure que des événements de journalisation se produisent sur l’application web. Toute information enregistrée dans un fichier ayant l’extension .txt, .log ou .htm et stocké dans le répertoire /LogFiles (d:/home/logfiles) est diffusée vers la console locale.

Pour filtrer des événements spécifiques, tels que des erreurs, utilisez le paramètre **--Filter** . Par exemple : 

    az webapp log tail --name webappname --resource-group myResourceGroup --filter Error

Pour filtrer des types de journaux spécifiques, tels que HTTP, utilisez le paramètre **--Path** . Par exemple : 

    az webapp log tail --name webappname --resource-group myResourceGroup --path http

> [!NOTE]
> Si vous n’avez pas installé ou configuré l’interface de ligne de commande Azure de manière à utiliser votre abonnement Azure, consultez la page [Utilisation de l’interface de ligne de commande Azure](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Présentation des journaux de diagnostic
### <a name="application-diagnostics-logs"></a>Journaux de diagnostic d'application
Le diagnostic d'application stocke les informations dans un format spécifique pour les applications .NET selon que vous stockez les journaux dans le système de fichiers, le stockage de tables ou le stockage d'objets blob. L’ensemble de base des données stockées est le même dans les trois types de stockage, à savoir : date et heure auxquelles l’événement s’est produit, ID de processus qui a généré l’événement, type d’événement (informations, avertissement, erreur) et message d’événement.

**Système de fichiers**

Chaque ligne journalisée dans le système de fichiers ou reçue par le biais d’un streaming se présente au format suivant :

    {Date}  PID[{process ID}] {event type/level} {message}

Par exemple, un événement d’erreur peut se présenter comme suit :

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

Parmi les trois méthodes disponibles, la journalisation d’informations dans le système de fichiers est celle qui fournit les informations les plus élémentaires. L’heure, l’ID de processus, le niveau d’événement et le message sont, en effet, les seules informations fournies.

**Stockage Table**

Lorsque vous consignez des informations dans le stockage de tables, des propriétés supplémentaires sont utilisées pour faciliter la recherche des données stockées dans la table, ainsi que des informations plus précises sur l'événement. Les propriétés suivantes (colonnes) sont utilisées pour chaque entité (ligne) stockée dans la table.

| Nom de la propriété | Valeur/format |
| --- | --- |
| PartitionKey |Date/heure de l'événement au format aaaaMMjjHH |
| RowKey |Valeur GUID qui identifie cette entité de façon unique |
| Timestamp |Date et heure auxquelles l'événement s'est produit |
| EventTickCount |Date et heure auxquelles l'événement s'est produit, au format Tick (précision accrue) |
| ApplicationName |Nom de l’application web |
| Level |Niveau d’événement (par exemple, erreur, avertissement ou information) |
| EventId |ID de cet événement<p><p>Il est, par défaut, défini sur 0 |
| InstanceId |Instance de l’application web sur laquelle l’événement s’est produit |
| Pid |ID du processus |
| Tid |ID de thread qui a généré l'événement |
| Message |Message détaillé sur l'événement |

**Stockage d’objets blob**

Lorsque vous consignez des données dans un stockage d'objets blob, elles sont stockées au format CSV (valeurs séparées par des virgules). Comme c'est le cas avec le stockage de tables, des champs supplémentaires sont consignés afin de fournir des informations plus précises sur l'événement. Les propriétés suivantes sont utilisées pour chaque ligne du fichier CSV :

| Nom de la propriété | Valeur/format |
| --- | --- |
| Date |Date et heure auxquelles l'événement s'est produit |
| Level |Niveau d’événement (par exemple, erreur, avertissement ou information) |
| ApplicationName |Nom de l’application web |
| InstanceId |Instance d’application web sur laquelle l’événement s’est produit |
| EventTickCount |Date et heure auxquelles l'événement s'est produit, au format Tick (précision accrue) |
| EventId |ID de cet événement<p><p>Il est, par défaut, défini sur 0 |
| Pid |ID du processus |
| Tid |ID de thread qui a généré l'événement |
| Message |Message détaillé sur l'événement |

Les données stockées dans un objet blob peuvent se présenter comme suit :

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> La première ligne du journal contient les en-têtes de colonne, tels qu’ils sont représentés dans cet exemple.
>
>

### <a name="failed-request-traces"></a>Suivi des demandes ayant échoué
Le suivi des demandes ayant échoué est stocké dans des fichiers XML nommés **fr######.xml**. Pour faciliter la consultation des informations consignées, une feuille de style XSL nommée **freb.xsl** est fournie dans le même répertoire que les fichiers XML. Si vous ouvrez un des fichiers XML dans Internet Explorer, ce dernier utilise la feuille de style XSL pour fournir un affichage mis en forme des informations de suivi, comme dans l’exemple suivant :

![affichage d'une demande ayant échoué dans le navigateur](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Journaux d'erreurs détaillés
Les journaux d'erreurs détaillés sont des documents HTML qui fournissent des informations plus détaillées sur les erreurs HTTP qui se sont produites. Puisqu'il s'agit simplement de documents HTML, ils peuvent être consultés à l'aide d'un navigateur Web.

### <a name="web-server-logs"></a>Journaux des serveurs Web
Les journaux de serveur Web utilisent le [format de fichier journal étendu W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ces informations peuvent être lues à l'aide d'un éditeur de texte ou analysées à l'aide d'utilitaires tels que [Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Les journaux générés par les applications web Azure ne prennent pas en charge les champs **s-computername**, **s-ip** ou **cs-version**.
>
>

## <a name="nextsteps"></a> Étapes suivantes
* [Surveillance d’applications Web](web-sites-monitor.md)
* [Résolution des problèmes des applications web Azure dans Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Analyse des journaux d’application Web dans HDInsight (en anglais)](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
>
>
