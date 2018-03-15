---
title: Solution Update Management dans Azure | Microsoft Docs
description: "Cet article vise à vous aider à comprendre comment utiliser cette solution pour gérer les mises à jour de vos ordinateurs Windows et Linux."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: gwallace
ms.openlocfilehash: 9280925cdd5cccf8d1d2f2b33a7de8523a07cd14
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="update-management-solution-in-azure"></a>Solution Update Management dans Azure

La solution Update Management dans Azure Automation vous permet de gérer les mises à jour de sécurité du système d’exploitation de vos ordinateurs Windows et Linux déployés dans Azure, des environnements locaux ou d’autres fournisseurs de cloud. Vous pouvez rapidement évaluer l’état des mises à jour disponibles sur tous les ordinateurs d’agent et gérer le processus d’installation des mises à jour requises pour les serveurs.

Vous pouvez activer la gestion de mise à jour pour les machines virtuelles directement depuis votre compte [Azure Automation](automation-offering-get-started.md).
Pour découvrir comment activer la gestion de mise à jour pour les machines virtuelles depuis votre compte Automation, consultez l’article [Gérer les mises à jour pour plusieurs machines virtuelles](manage-update-multi.md).

## <a name="solution-overview"></a>Vue d’ensemble de la solution

Les ordinateurs gérés par Update Management utilisent les configurations suivantes pour effectuer l’évaluation et les déploiements de mises à jour :

* Microsoft Monitoring Agent pour Windows ou Linux
* PowerShell DSC (Desired State Configuration, configuration d’état souhaité) pour Linux
* Runbook Worker hybride Automation
* Services Microsoft Update ou Windows Server Update pour ordinateurs Windows

Le schéma suivant présente une vue conceptuelle du comportement et du flux de données liés à l’évaluation des mises à jour de sécurité par la solution et à leur application à tous les ordinateurs Windows Server et Linux connectés dans un espace de travail.    

![Flux du processus Update Management](media/automation-update-management/update-mgmt-updateworkflow.png)

Une fois qu’un ordinateur effectue une analyse de conformité de mise à jour, l’agent transfère les informations en bloc à Log Analytics. Sur un ordinateur Windows, l’analyse de conformité est effectuée toutes les 12 heures par défaut. En plus de l’analyse planifiée, l’analyse de conformité de mise à jour est lancée dans les 15 minutes si Microsoft Monitoring Agent (MMA) est redémarré, avant l’installation de la mise à jour et après l’installation de la mise à jour. Avec un ordinateur Linux, l’analyse de conformité est effectuée toutes les 3 heures par défaut, et une analyse de conformité est lancée dans les 15 minutes si l’agent MMA est redémarré.

La solution rapporte l’état de mise à jour de l’ordinateur en fonction de la source avec laquelle synchroniser que vous configurez. Si l’ordinateur Windows est configuré pour rapporter à WSUS, en fonction de la date de dernière synchronisation de WSUS avec Microsoft Update, les résultats peuvent être différents de ce que Microsoft Updates indique. C’est également le cas pour les ordinateurs Linux qui sont configurés pour rapporter à un référentiel local par rapport à un référentiel public.

Vous pouvez déployer et installer des mises à jour logicielles sur des ordinateurs qui nécessitent les mises à jour en créant un déploiement planifié. Les mises à jour classées comme *facultatives* ne sont pas incluses dans le déploiement pour ordinateurs Windows, uniquement les mises à jour requises. Le déploiement planifié définit les ordinateurs cibles qui reçoivent les mises à jour applicables, soit en spécifiant explicitement les ordinateurs ou en sélectionnant un [groupe d’ordinateurs](../log-analytics/log-analytics-computer-groups.md) déterminé par des recherches dans les journaux d’un ensemble spécifique d’ordinateurs. Vous spécifiez également une planification pour approuver et désigner une période de temps pendant laquelle l’installation des mises à jour est autorisée. Les mises à jour sont installées par des Runbooks dans Azure Automation. Vous ne pouvez pas visualiser ces Runbooks, qui ne nécessitent aucune configuration. Lorsqu’un déploiement de mises à jour est créé, il génère une planification qui démarre un Runbook de mises à jour principal au moment indiqué pour les ordinateurs inclus. Ce Runbook principal lance un Runbook enfant sur chaque agent qui effectue l’installation des mises à jour obligatoires.

À la date et l’heure spécifiées dans le déploiement de mises à jour, les ordinateurs cibles exécutent le déploiement en parallèle. Une analyse est tout d’abord effectuée pour vérifier si les mises à jour sont toujours obligatoires et les installe. Pour les ordinateurs clients WSUS, si les mises à jour ne sont pas approuvées dans WSUS, leur déploiement échoue.

## <a name="clients"></a>Clients

### <a name="supported-client-types"></a>Types de clients pris en charge

Le tableau suivant répertorie la liste des systèmes d’exploitation pris en charge : 

|Système d’exploitation  |Notes  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | Prend uniquement en charge les évaluations de mises à jour.         |
|Windows Server 2008 R2 SP1 et versions ultérieures     |.NET Framework 4.5 et WMF 5.0 ou des versions ultérieures sont nécessaires pour Windows Server 2008 R2 SP1.        |
|CentOS 6 (x86/x64) et 7 (x 64)      | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|Red Hat Enterprise 6 (x86/x64) et 7 (x 64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|SUSE Linux Enterprise Server 11 (x86/x64) et 12 (x64)     | Les agents Linux doivent avoir accès à un référentiel de mise à jour.        |
|Ubuntu 12.04 LTS et x86/x64 plus récente       |Les agents Linux doivent avoir accès à un référentiel de mise à jour.         |

### <a name="unsupported-client-types"></a>Types de clients non pris en charge

Le tableau suivant répertorie les systèmes d’exploitation qui ne sont pas pris en charge :

|Système d’exploitation  |Notes  |
|---------|---------|
|Client Windows     | Les systèmes d’exploitation clients (Windows 7, Windows 10, etc.) ne sont pas pris en charge.        |
|Windows Server 2016 Nano Server     | Non pris en charge       |

### <a name="client-requirements"></a>Configuration requise des clients

#### <a name="windows"></a>Windows

Les agents Windows doivent être configurés pour communiquer avec un serveur WSUS (Windows Server Update Services) ou avoir accès à Microsoft Update. De plus, l’agent Windows ne peut pas être géré simultanément par System Center Configuration Manager. L’[agent Windows](../log-analytics/log-analytics-agent-windows.md) est obligatoire. Cet agent est automatiquement installé si vous intégrez une machine virtuelle Azure.

#### <a name="linux"></a>Linux

Pour Linux, l’ordinateur doit avoir accès à un dépôt de mise à jour, qui peut être privé ou public. Les agents OMS pour Linux configurés pour rapporter à plusieurs espaces de travail Log Analytics ne sont pas pris en charge avec cette solution.

Pour plus d’informations sur la manière d’installer Agent OMS pour Linux et de télécharger la dernière version, consultez [Agent Operations Management Suite pour Linux](https://github.com/microsoft/oms-agent-for-linux). Pour plus d’informations sur l’installation de l’agent OMS pour Windows, consultez [Operations Management Suite Agent pour Windows](../log-analytics/log-analytics-windows-agent.md).  

## <a name="permissions"></a>Autorisations
Pour créer et gérer des déploiements de mises à jour, vous devez disposer d’autorisations spécifiques. Pour en savoir plus sur ces autorisations, visitez [Accès en fonction du rôle - Update Management](automation-role-based-access-control.md#update-management). 

## <a name="solution-components"></a>Composants de la solution
Cette solution se compose des ressources suivantes qui sont ajoutées à votre compte Automation et d’agents directement connectés ou d’un groupe d’administration Operations Manager connecté.

### <a name="hybrid-worker-groups"></a>Groupes de Workers hybrides
Après avoir activé cette solution, tout ordinateur Windows directement connecté à votre espace de travail Log Analytics est automatiquement configuré comme un Runbook Worker hybride pour prendre en charge les Runbooks inclus dans cette solution. Chaque ordinateur Windows géré par la solution est répertorié dans la page des groupes de rôles de travail hybrides en tant que groupe de rôles de travail hybride du compte Automation conformément à la convention d’affectation de noms *Hostname FQDN_GUID*. Vous ne pouvez pas cibler ces groupes avec des Runbooks dans votre compte, sans quoi ils échouent. Ces groupes sont uniquement destinés à prendre en charge de la solution de gestion.

Vous pouvez toutefois ajouter les ordinateurs Windows à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.

### <a name="management-packs"></a>Packs d’administration

Si votre groupe d’administration System Center Operations Manager est connecté à un espace de travail Log Analytics, les packs d’administration suivants sont installés dans Operations Manager. Ces packs d’administration sont également installés sur des ordinateurs Windows directement connectés après l’ajout de cette solution. Aucun élément ne doit être configuré ou géré avec ces packs d’administration.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pack d’administration du déploiement des mises à jour

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager à Log Analytics](../log-analytics/log-analytics-om-agents.md).

### <a name="confirm-non-azure-machines-are-onboarded"></a>Vérifier que les ordinateurs non-Azure sont intégrés

Pour confirmer que les ordinateurs directement connectés communiquent avec Log Analytics, vous pouvez exécuter la recherche suivante dans les journaux au bout de quelques minutes :

#### <a name="linux"></a>Linux

```
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table`
```

Sur un ordinateur Windows, vous pouvez observer les informations suivantes pour vérifier la connectivité de l’agent avec Log Analytics :

1.  Ouvrez Microsoft Monitoring Agent dans le Panneau de configuration puis, dans l’onglet **Azure Log Analytics (OMS)**, l’agent affiche un message indiquant : **Microsoft Monitoring Agent est bien connecté au service Microsoft Operations Management Suite**.   
2.  Ouvrez le journal des événements Windows, accédez à **Application and Services Logs\Operations Manager**, puis recherchez l’ID d’événement 3000 et 5002 à partir du connecteur de service source. Ces événements indiquent que l’ordinateur est enregistré sur l’espace de travail Log Analytics et qu’il reçoit la configuration.  

Si l’agent ne parvient pas à communiquer avec Log Analytics et qu’il est configuré pour communiquer avec Internet par le biais d’un pare-feu ou d’un serveur proxy, vérifiez que le pare-feu ou le serveur proxy sont correctement configurés en consultant la [configuration réseau de l’agent Windows](../log-analytics/log-analytics-agent-windows.md) ou la [configuration réseau de l’agent Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Si vos systèmes Linux sont configurés pour communiquer avec un proxy ou une passerelle OMS et que vous intégrez cette solution, mettez à jour les autorisations *proxy.conf* pour accorder au groupe omiuser une autorisation d’accès en lecture sur le fichier en exécutant les commandes suivantes :  
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`  
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Les nouveaux agents Linux ajoutés affichent l’état **Mis à jour** après l’exécution d’une évaluation. Ce processus peut prendre jusqu’à 6 heures.

Pour vérifier qu’un groupe d’administration Operations Manager communique avec Log Analytics, consultez la rubrique [Valider l’intégration entre Operations Manager et OMS](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms).

## <a name="data-collection"></a>Collecte des données

### <a name="supported-agents"></a>Agents pris en charge
Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution.

| Source connectée | Prise en charge | DESCRIPTION |
| --- | --- | --- |
| Agents Windows |OUI |La solution de collecte des informations sur les mises à jour système des agents et lance l’installation des mises à jour obligatoires. |
| Agents Linux |OUI |La solution collecte des informations sur les mises à jour système des agents Linux et lance l’installation des mises à jour obligatoires sur les versions prises en charge. |
| Groupe d’administration d’Operations Manager |OUI |La solution collecte des informations sur les mises à jour système des agents dans un groupe d’administration connecté.<br>Une connexion directe entre l’agent Operations Manager et Log Analytics n’est pas obligatoire. Les données sont transférées du groupe d’administration à l’espace de travail Log Analytics. |

### <a name="collection-frequency"></a>Fréquence de collecte
Pour chaque ordinateur Windows géré, une analyse est effectuée deux fois par jour. Les API Windows sont appelées toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé et si une analyse de conformité est lancée. Pour chaque ordinateur Linux géré, une analyse est effectuée toutes les 3 heures.

L’affichage des données mises à jour des ordinateurs gérés dans le tableau de bord peut prendre de 30 minutes à 6 heures.   

## <a name="viewing-update-assessments"></a>Affichage des évaluations de mises à jour
Cliquez sur **Update Management** dans votre compte Automation pour afficher l’état de vos ordinateurs.

Cet affichage fournit des informations sur vos ordinateurs, les mises à jour manquantes, les déploiements de mises à jour et les déploiements de mises à jour planifiés.

Vous pouvez exécuter une recherche dans les journaux qui permet de retourner des informations sur l’ordinateur, la mise à jour ou le déploiement en sélectionnant l’élément dans la liste. Ainsi, la page **Recherche dans les journaux** s’ouvre avec une requête sur l’élément sélectionné.

## <a name="installing-updates"></a>Installation des mises à jour

Une fois les mises à jour évaluées pour tous les ordinateurs Linux et Windows dans votre espace de travail, vous pouvez lancer l’installation des mises à jour obligatoires en créant une opération de *déploiement de mises à jour*. Un déploiement de mises à jour est une installation planifiée de mises à jour obligatoires pour un ou plusieurs ordinateurs. Vous pouvez spécifier la date et l’heure du déploiement en plus d’un ordinateur ou d’un groupe d’ordinateurs à inclure dans un déploiement. Pour en savoir plus sur les groupes d’ordinateurs, consultez [Groupes d’ordinateurs dans Log Analytics](../log-analytics/log-analytics-computer-groups.md). Lorsque vous incluez des groupes d’ordinateurs dans votre déploiement de mises à jour, l’appartenance au groupe n’est évaluée qu’une seule fois au moment de la création de la planification. Les modifications ultérieures apportées à un groupe ne sont pas répercutées. Pour contourner ce problème, supprimez le déploiement de mises à jour planifié et recréez-le.

> [!NOTE]
> Les machines virtuelles Windows déployées à partir d’Azure Marketplace sont configurées par défaut pour recevoir des mises à jour automatiques de Windows Update Service. Ce comportement ne change pas après l’ajout de cette solution ou de machines virtuelles Windows à votre espace de travail. Si vous n’avez pas géré activement des mises à jour avec cette solution, le comportement par défaut (appliquer automatiquement des mises à jour) s’applique.

Pour éviter que les mises à jour soient appliquées en dehors d’une fenêtre de maintenance sur Ubuntu, reconfigurez le package Unattended-Upgrade pour désactiver les mises à jour automatiques. Pour plus d’informations sur cette configuration, consultez la [rubrique Mises à jour automatiques du Guide du serveur Ubuntu](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Les machines virtuelles créées à partir des images Red Hat Enterprise Linux (RHEL) à la demande disponibles dans le service Place de marché Azure sont inscrites pour accéder à l’infrastructure [RHUI (Red Hat Update Infrastructure)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) déployée dans Azure. Toute autre distribution Linux doit être mise à jour à partir du référentiel de fichiers de distributions en ligne en tenant compte de leurs méthodes prises en charge.  

## <a name="viewing-missing-updates"></a>Affichage des mises à jour manquantes

Cliquez sur **Mises à jour manquantes** pour afficher la liste des mises à jour qui manquent à vos ordinateurs. Chaque mise à jour est répertoriée et présente des informations sur le nombre d’ordinateurs qui nécessitent la mise à jour, le système d’exploitation et le lien vers plus d’informations. Chaque mise à jour peut être sélectionnée. La page **Recherche dans les journaux** s’affiche alors et donne des détails sur les mises à jour.

## <a name="viewing-update-deployments"></a>Affichage des déploiements de mises à jour

Cliquez sur **Déploiements de mises à jour** pour afficher la liste des déploiements de mises à jour existants. Cliquer sur un déploiement de mises à jour dans la liste ouvre la page **Exécution du déploiement des mises à jour** de ce déploiement de mises à jour.

![Vue d’ensemble des résultats d’un déploiement de mises à jour](./media/automation-update-management/update-deployment-run.png)

## <a name="creating-an-update-deployment"></a>Création d’un déploiement de mises à jour

Pour créer un déploiement de mises à jour, cliquez sur le bouton **Planifier le déploiement de la mise à jour** situé en haut de l’écran pour ouvrir la page **Nouveau déploiement de mises à jour**. Vous devez fournir des valeurs pour les propriétés dans le tableau suivant :

| Propriété | DESCRIPTION |
| --- | --- |
| NOM |Nom unique identifiant le déploiement de mises à jour. |
|Système d’exploitation| Linux ou Windows|
| Ordinateurs à mettre à jour |Sélectionnez une recherche enregistrée ou choisissez un ordinateur dans la liste déroulante, puis sélectionnez des ordinateurs individuels. |
|Classification des mises à jour|Sélectionnez toutes les classifications des mises à jour dont vous avez besoin.|
|Mises à jour à exclure|Entrez tous les numéros de la Base de connaissances à exclure sans indiquer le préfixe « KB ».|
|Paramètres de planification|Sélectionnez l’heure de début, puis la périodicité.|
| Fenêtre de maintenance |Nombre de minutes défini pour les mises à jour. La valeur ne peut pas être inférieure à 30 minutes ni supérieure à 6 heures. |

## <a name="search-logs"></a>Rechercher dans les journaux

En plus des détails fournis dans le portail, des recherches peuvent être effectuées dans les journaux. Avec la page **Change Tracking** ouverte, cliquez sur **Log Analytics** pour ouvrir la page **Recherche dans les journaux**.

### <a name="sample-queries"></a>Exemples de requêtes

Le tableau suivant fournit des exemples de recherches dans les journaux d’enregistrements de mise à jour collectés par cette solution :

| Requête | DESCRIPTION |
| --- | --- |
|Mettre à jour<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; project Computer, Title, KBID, Classification, PublishedDate |Ensemble des ordinateurs avec des mises à jour manquantes<br>Ajoutez une des valeurs suivantes pour limiter le système d’exploitation :<br>OSType = "Windows"<br>OSType == "Linux" |
| Mettre à jour<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; where Computer == "ContosoVM1.contoso.com"<br>&#124; project Computer, Title, KBID, Product, PublishedDate |Mises à jour manquantes pour un ordinateur spécifique (remplacer la valeur par le nom de votre ordinateur)|
| Événement<br>&#124; where EventLevelName == "error" and Computer in ((Update &#124; where (Classification == "Security Updates" or Classification == "Critical Updates")<br>&#124; where UpdateState == "Needed" and Optional == false <br>&#124; distinct Computer)) |Événements d’erreur pour les machines où des mises à jour critiques ou de sécurité obligatoires sont manquantes |
| Mettre à jour<br>&#124; where UpdateState == "Needed" and Optional == false<br>&#124; distinct Title |Mises à jour manquantes distinctes sur tous les ordinateurs | 
| UpdateRunProgress<br>&#124; where InstallationStatus == "failed" <br>&#124; summarize AggregatedValue = count() by Computer, Title, UpdateRunName |Ordinateurs avec les mises à jour ayant échoué dans une exécution de mise à jour<br>Ajoutez une des valeurs suivantes pour limiter le système d’exploitation :<br>OSType = "Windows"<br>OSType == "Linux" | 
| Mettre à jour<br>&#124; where OSType == "Linux"<br>&#124; where UpdateState != "Not needed" and (Classification == "Critical Updates" or Classification == "Security Updates")<br>&#124; summarize AggregatedValue = count() by Computer |Liste de tous les ordinateurs Linux pour lesquels une mise à jour de package corrigeant une vulnérabilité critique ou de sécurité est disponible | 
| UpdateRunProgress<br>&#124; where UpdateRunName == "DeploymentName"<br>&#124; summarize AggregatedValue = count() by Computer|Ordinateurs mis à jour dans cette mise à jour (remplacer la valeur par le nom de votre déploiement de mises à jour) | 

## <a name="integrate-with-system-center-configuration-manager"></a>Intégrer avec System Center Configuration Manager

Les clients qui ont investi dans System Center Configuration Manager pour gérer des PC, serveurs et autres appareils mobiles s’appuient aussi sur sa puissance et sa maturité pour gérer les mises à jour logicielles dans le cadre de leur cycle de gestion des mises à jour logicielles.

Pour découvrir comment intégrer la solution OMS Update Management avec System Center Configuration Manager, consultez l’article [Intégrer System Center Configuration Manager avec OMS Update Management](oms-solution-updatemgmt-sccmintegration.md).

## <a name="patching-linux-machines"></a>Mise à jour corrective des ordinateurs Linux

Les sections suivantes décrivent les problèmes potentiels liés à la mise à jour corrective Linux.

### <a name="package-exclusion"></a>Exclusion de packages

Sur certaines variantes Linux, comme Red Hat Enterprise Linux, les mises à niveau du système d’exploitation peuvent se produire par le biais de packages. Celles-ci peuvent alors entraîner des exécutions Update Management au cours desquelles le numéro de version du système d’exploitation change. Étant donné que la solution Update Management utilise les mêmes méthodes pour mettre à jour des packages qu’un administrateur sur l’ordinateur Linux local, ce comportement est volontaire.

Pour éviter de mettre à jour la version du système d’exploitation par le biais des exécutions Update Management, vous utilisez la fonctionnalité **Exclusion**.

Dans Red Hat Enterprise Linux, le nom du package à exclure est : redhat-release-server.x86_64

![Packages à exclure pour Linux](./media/automation-update-management/linuxpatches.png)

### <a name="security-patches-not-being-applied"></a>Mises à jour de sécurité non appliquées

Lorsque vous déployez des mises à jour sur un ordinateur Linux, vous pouvez sélectionner des classifications. Celles-ci permettent de filtrer les mises à jour appliquées qui répondent à des critères spécifiés. Ce filtre est appliqué localement sur l’ordinateur lorsque la mise à jour est déployée. Comme Update Management enrichit les mises à jour dans le cloud, certaines mises à jour peuvent être signalées dans Update Management comme ayant un impact sur la sécurité quand bien même l’ordinateur local n’a pas ces informations. Ainsi, si vous appliquez des mises à jour critiques à un ordinateur Linux, certaines mises à jour, non signalées comme ayant un impact sur la sécurité pour cet ordinateur, peuvent ne pas être appliquées. Toutefois, Update Management peut quand même signaler que cet ordinateur n’est pas conforme car il contient des informations supplémentaires sur la mise à jour concernée.

Le déploiement de mises à jour par classification peut ne pas fonctionner sur openSUSE Linux en raison du modèle différent de mise à jour corrective utilisé.

## <a name="troubleshooting"></a>Résolution de problèmes

Cette section fournit des informations pour vous aider à résoudre les problèmes liés à la solution de gestion de mises à jour.

Si vous rencontrez des problèmes lorsque vous essayez d’intégrer la solution ou une machine virtuelle, recherchez dans le journal d’évènements **Journaux des applications et des services\Operations Manager** les événements avec l’ID d’événement 4502 et le message d’événement contenant **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**. Le tableau suivant met en évidence des messages d’erreur spécifiques et une résolution possible pour chacun d’entre eux.  

| Message | Motif | Solution |   
|----------|----------|----------|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>System.InvalidOperationException: {"Message":"Machine is already<br>registered to a different account. "} (Impossible d’inscrire la machine pour la gestion des correctifs, l’inscription a échoué avec l’exception System.InvalidOperationException : {"Message" :"La machine est déjà inscrite sur un autre compte."} | La machine est déjà intégrée à un autre espace de travail pour Update Management | Éliminez les anciens artefacts en [supprimant le groupe de Runbooks hybrides](automation-hybrid-runbook-worker.md#remove-hybrid-worker-groups)|  
| Unable to  Register Machine for Patch Management,<br>Registration Failed with Exception<br>System.Net.Http.HttpRequestException: An error occurred while sending the request. ---><br>System.Net.WebException: The underlying connection<br>was closed: An unexpected error<br>occurred on a receive. ---> System.ComponentModel.Win32Exception:<br>The client and server cannot communicate,<br>because they do not possess a common algorithm (Impossible d’inscrire la machine pour la gestion des correctifs, l’inscription a échoué avec l’exception System.Net.Http.HttpRequestException : une erreur s’est produite lors de l’envoi de la requête. --->System.Net.WebException : la connexion sous-jacente était fermée : une erreur inattendue s’est produite à la réception. ---> System.ComponentModel.Win32Exception : le client et le serveur ne peuvent pas communiquer car ils n’ont pas d’algorithme en commun) | Proxy/passerelle/pare-feu bloquant la communication | [Passez en revue la configuration requise pour le réseau](automation-offering-get-started.md#network-planning)|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value. (Impossible d’inscrire la machine pour la gestion des correctifs, l’inscription a échoué avec l’exception Newtonsoft.Json.JsonReaderException : erreur d’analyse de valeur infinie positive.) | Proxy/passerelle/pare-feu bloquant la communication | [Passez en revue la configuration requise pour le réseau](automation-offering-get-started.md#network-planning)| 
| The certificate presented by the service <wsid>.oms.opinsights.azure.com<br>was not issued by a certificate authority<br>used for Microsoft services. Contact<br>your network administrator to see if they are running a proxy that intercepts<br>TLS/SSL communication. (Le certificat présenté par le service .oms.opinsights.azure.com n’a pas été émis par une autorité de certification utilisée par les services Microsoft. Veuillez contacter votre administrateur réseau pour déterminer si un proxy en cours d’exécution intercepte la communication TLS/SSL.) |Proxy/passerelle/pare-feu bloquant la communication | [Passez en revue la configuration requise pour le réseau](automation-offering-get-started.md#network-planning)|  
| Unable to Register Machine for Patch Management,<br>Registration Failed with Exception<br>AgentService.HybridRegistration.<br>PowerShell.Certificates.CertificateCreationException:<br>Failed to create a self-signed certificate. ---><br>System.UnauthorizedAccessException: Access is denied. (Impossible d’inscrire la machine pour la gestion des correctifs, l’inscription a échoué avec l’exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException : échec de la création d’un certificat auto-signé. --->System.UnauthorizedAccessException : accès refusé.) | Échec de génération du certificat auto-signé | Vérifiez que le compte système a<br>un accès en lecture au dossier :<br>**C:\ProgramData\Microsoft\**<br>**Crypto\RSA**|  

## <a name="next-steps"></a>étapes suivantes

Poursuivez avec le tutoriel pour apprendre à gérer les mises à jour pour vos machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Gérer les mises à jour et les correctifs pour vos machines virtuelles Windows Azure](automation-tutorial-troubleshoot-changes.md)

* Utilisez les recherches de journaux de [Log Analytics](../log-analytics/log-analytics-log-searches.md) pour afficher des données détaillées sur les mises à jour.
* [Créez des alertes](../log-analytics/log-analytics-alerts.md) lorsque des mises à jour critiques sont détectées comme manquantes sur des ordinateurs ou lorsque les mises à jour automatiques sont désactivées sur un ordinateur.
