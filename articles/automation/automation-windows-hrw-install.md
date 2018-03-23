---
title: Runbook Worker hybride Windows Azure Automation
description: Cet article fournit des informations sur l’installation d’un Runbook Worker hybride Azure Automation qui vous permet d’exécuter des Runbooks sur les machines Windows de votre centre de données local ou de votre environnement cloud.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c03b124e21603056219a6ff6458469b4ecd60969
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Déploiement d’un Runbook Worker hybride Windows

Dans Azure Automation, les Runbooks ne peuvent pas accéder aux ressources d’autres clouds ou dans votre environnement local car ils s'exécutent dans le cloud Azure.  La fonctionnalité de Runbook Worker hybride d’Azure Automation vous permet d’exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés.  

Cette fonctionnalité est illustrée dans l’image suivante :<br>

![Vue d'ensemble des Runbooks Workers hybrides](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Pour obtenir un aperçu technique des considérations de rôle et de déploiement d’un Runbook Worker hybride, consultez [Présentation de l’architecture Automation](automation-offering-get-started.md#automation-architecture-overview).

## <a name="hybrid-runbook-worker-groups"></a>Groupes de Runbooks Workers hybrides

Chaque Runbook Worker hybride est membre d'un groupe de Runbooks Workers hybrides que vous spécifiez lorsque vous installez l'agent.  Un groupe peut inclure un seul agent, mais vous pouvez installer plusieurs agents dans un groupe pour une haute disponibilité.

Lorsque vous démarrez un Runbook sur un Runbook Worker hybride, vous spécifiez le groupe sur lequel il s’exécute.  Les membres du groupe déterminent quel Worker traite la demande.  Vous ne pouvez pas spécifier un Worker particulier.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installation de la fonctionnalité Windows Runbook Worker hybride 

Il existe deux méthodes pour installer et configurer un Runbook Worker hybride Windows.  La méthode recommandée utilise un Runbook Automation afin d’automatiser totalement le processus requis pour configurer un ordinateur Windows.  La deuxième méthode consiste à suivre la procédure pas à pas pour installer et configurer le rôle manuellement.  

> [!NOTE]
> Pour gérer la configuration de vos serveurs prenant en charge le rôle Runbook Worker hybride avec configuration de l’état souhaité (DSC), vous devez les ajouter en tant que nœuds DSC.  Pour plus d’informations sur leur intégration pour les gérer avec DSC, consultez la page [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).        
Si vous activez la [solution de gestion des mises à jour](../operations-management-suite/oms-solution-update-management.md), tout ordinateur Windows connecté à votre espace de travail OMS est automatiquement configuré comme un Runbook Worker hybride pour prendre en charge les Runbooks inclus dans cette solution.  Toutefois, il n’est enregistré auprès d’aucun des groupes de Workers hybrides déjà définis dans votre compte Automation.  L’ordinateur peut être ajouté à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides.  Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.  

Consultez les informations suivantes concernant les [exigences matérielles et logicielles requise](automation-offering-get-started.md#hybrid-runbook-worker) et les [informations pour préparer votre réseau](automation-offering-get-started.md#network-planning) avant de commencer le déploiement d’un Runbook Worker hybride.  Une fois que vous avez déployé un runbook worker avec succès, consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.  
 
### <a name="automated-deployment"></a>Déploiement automatisé

Effectuez les étapes suivantes pour automatiser l’installation et la configuration du rôle Worker hybride Windows.  

1. Téléchargez le script *New-OnPremiseHybridWorker.ps1* à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/) directement depuis l’ordinateur qui exécute le rôle Runbook Worker hybride ou depuis un autre ordinateur dans votre environnement, et copiez-le dans le Worker.  

    Le script *New-OnPremiseHybridWorker.ps1* requiert les paramètres suivants lors de l’exécution :

  * *AutomationAccountName* (obligatoire) : nom de votre compte Automation.  
  * *ResourceGroupName* (obligatoire) - le nom du groupe de ressources associé à votre compte Automation.  
  * *HybridGroupName* (obligatoire) - le nom d’un groupe de Runbooks Workers hybrides que vous spécifiez comme cible pour les Runbooks prenant en charge ce scénario. 
  *  *SubscriptionID* (obligatoire) - l’ID de l’abonnement Azure dans lequel se trouve votre compte Automation.
  *  *WorkspaceName* (facultatif) : le nom d’espace de travail OMS.  Si vous n’avez pas d’espace de travail OMS, le script en crée un et le configure.  

     > [!NOTE]
     > Actuellement, les seules régions Automation prises en charge pour l’intégration avec OMS sont : **Sud-Est de l’Australie**, **Est des États-Unis 2**, **Sud-Est asiatique** et **Europe de l’Ouest**.  Si votre compte Automation ne se trouve pas dans une de ces régions, le script crée un espace de travail OMS, mais vous avertit qu’il ne peut pas lier les deux.
     >
2. Sur votre ordinateur, démarrez **Windows PowerShell** à partir de l’écran **Démarrer** en mode Administrateur.  
3. Dans l’interface de ligne de commande PowerShell, accédez au dossier qui contient le script que vous avez téléchargé, puis exécutez-le en modifiant les valeurs des paramètres *-AutomationAccountName*, *-ResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* et *-WorkspaceName*.

     > [!NOTE] 
     > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure.  Vous **devez** vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. Vous êtes invité à accepter d’installer **NuGet** et à vous authentifier avec vos informations d’identification Azure.<br><br> ![Exécution du script New-OnPremiseHybridWorker](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. Une fois le script terminé, la page Groupes de Workers hybrides affiche le nouveau groupe et le nombre de membres, ou, si le groupe existe déjà, incrémente le nombre de membres.  Vous pouvez sélectionner le groupe dans la liste de la page **Groupes de Workers hybrides** et sélectionner la vignette **Workers hybrides**.  La page **Workers hybrides** affiche chaque membre du groupe listé.  

### <a name="manual-deployment"></a>Déploiement manuel 

Effectuez les deux premières étapes une fois pour votre environnement Automation, puis répétez les étapes restantes pour chaque ordinateur Worker.

#### <a name="1-create-operations-management-suite-workspace"></a>1. Création d’un espace de travail Operations Management Suite

Si vous ne disposez pas déjà d’un espace de travail Operations Management Suite, créez-en un en suivant les instructions mentionnées à la page [Gérer votre espace de travail](../log-analytics/log-analytics-manage-access.md). Vous pouvez utiliser un espace de travail existant si vous en avez déjà un.

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2. Ajout de la solution Automation à l’espace de travail Operations Management Suite

Les solutions ajoutent des fonctionnalités à Operations Management Suite.  La solution Automation ajoute des fonctionnalités à Azure Automation, notamment la prise en charge des Runbooks Workers hybrides.  Lorsque vous ajoutez la solution à votre espace de travail, les composants Worker sont automatiquement transférés à l’ordinateur agent que vous installerez à l’étape suivante.

Suivez les instructions de la page [Pour ajouter une solution à l’aide de la galerie de solutions](../log-analytics/log-analytics-add-solutions.md) pour ajouter la solution **Automation** à votre espace de travail Operations Management Suite.

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installation de Microsoft Monitoring Agent

Microsoft Monitoring Agent connecte les ordinateurs à Operations Management Suite.  Lorsque vous installez l’agent sur votre ordinateur local et que vous le connectez à votre espace de travail, il télécharge automatiquement les composants requis pour le Runbook Worker hybride.

Suivez les instructions de la page [Connecter des ordinateurs Windows à Log Analytics](../log-analytics/log-analytics-windows-agent.md) pour installer l’agent sur l’ordinateur local.  Vous pouvez répéter ce processus pour plusieurs ordinateurs afin d’ajouter plusieurs Workers à votre environnement.

Lorsque l’agent parvient à se connecter à Operations Management Suite, il est répertorié dans l’onglet **Sources connectées** du volet **Paramètres** d’Operations Management Suite.  Vous pouvez vérifier que l’agent a correctement téléchargé la solution Automation lorsqu’un dossier appelé **AzureAutomationFiles** figure dans C:\Program Files\Microsoft Monitoring Agent\Agent.  Pour valider la version du Runbook Worker hybride, vous pouvez accéder à C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ et consulter le sous-dossier \\*version*.   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installer l'environnement de Runbook et se connecter à Azure Automation

Lorsque vous ajoutez un agent à Operations Management Suite, la solution Automation lance le module PowerShell **HybridRegistration**, qui contient l’applet de commande **Add-HybridRunbookWorker**.  Vous utilisez cette applet de commande pour installer l'environnement de Runbook sur la machine et l'inscrire auprès d'Azure Automation.

Ouvrez une session PowerShell en mode administrateur et exécutez les commandes suivantes pour importer le module.

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

Exécutez ensuite l'applet de commande **Add-HybridRunbookWorker** en utilisant la syntaxe suivante :

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

Vous pouvez obtenir les informations requises pour cette applet de commande dans la page **Gérer les clés** du portail Azure.  Ouvrez cette page en sélectionnant l’option **Clés** sur la page **Paramètres** de votre compte Automation.

![Vue d'ensemble des Runbooks Workers hybrides](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** est le nom du groupe de Runbooks Workers hybrides. Si ce groupe existe déjà dans le compte Automation, la machine actuelle est ajoutée à ce dernier.  S'il n'existe pas encore, il est alors ajouté.
* **EndPoint** est le champ **URL** dans la page **Gérer les clés**.
* **Token** est la **clé d’accès primaire** dans la page **Gérer les clés**.  

Utilisez le commutateur **-Verbose** avec **Add-HybridRunbookWorker** pour recevoir des informations détaillées concernant l’installation.

#### <a name="5-install-powershell-modules"></a>5. Installer des modules PowerShell

Les Runbooks peuvent utiliser toutes les activités et applets de commande définies dans les modules installés dans votre environnement Azure Automation.  Toutefois, comme ces modules ne sont pas automatiquement déployés sur les ordinateurs locaux, vous devez les installer manuellement.  L’exception est le module Azure, qui est installé par défaut et qui permet d’accéder aux applets de commande de l’ensemble des services et activités Azure pour Azure Automation.

Étant donné que l’objectif principal de la fonctionnalité Runbook Worker hybride est de gérer les ressources locales, vous devez probablement installer les modules qui prennent en charge ces ressources.  Vous pouvez vous reporter à la section [Installation de modules](http://msdn.microsoft.com/library/dd878350.aspx) pour obtenir des informations sur l'installation de modules Windows PowerShell.  Les modules installés doivent se trouver dans un emplacement référencé par la variable d’environnement PSModulePath afin d’être importés automatiquement par le Worker hybride.  Pour plus d’informations, consultez la page [Modifier le chemin d’Installation de PSModulePath](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx). 

## <a name="troubleshooting"></a>Résolution de problèmes 

Le Runbook Worker hybride dépend de l’agent Microsoft Monitoring Agent pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du Worker échoue, voici les causes possibles de l’erreur :  

1. Le worker hybride est derrière un pare-feu ou un proxy.  
    Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur le port 443.  

2. L’ordinateur sur lequel le worker hybride s’exécute ne respecte pas les [exigences](automation-offering-get-started.md#hybrid-runbook-worker).  
    Les ordinateurs qui exécutent les workers hybrides doivent respecter la configuration matérielle minimale requise avant de pouvoir héberger cette fonctionnalité. Sinon, en fonction de l’utilisation des ressources d’autres processus d’arrière-plan et de la contention due aux runbooks lors de l’exécution, l’ordinateur sera surchargé, entraînant des retards ou des délais d’attente pour la tâche du runbook.
   Vérifiez que l’ordinateur désigné pour exécuter la fonctionnalité Runbook Worker hybride répond à la configuration matérielle minimale requise.  Si c’est le cas, surveillez l’utilisation du processeur et de la mémoire pour déterminer toute corrélation entre les performances des processus Runbook Worker hybride et de Windows.  S’il existe une surcharge de la mémoire ou du processeur, cela peut indiquer la nécessité de mettre à niveau ou d’ajouter des processeurs supplémentaires, ou d’augmenter la mémoire pour résoudre le goulot d’étranglement des ressources et résoudre l’erreur. Vous pouvez également sélectionner une ressource de calcul différente qui peut prendre en charge la configuration minimale requise et évoluer lorsque les demandes en matière de charge de travail indiquent qu’une augmentation est nécessaire.
    
3. Le service Microsoft Monitoring Agent n’est pas en cours d’exécution.  
    Si le service Windows de Microsoft Monitoring Agent n’est pas en cours d’exécution, le Runbook Worker hybride ne peut pas communiquer avec Azure Automation.  Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante dans PowerShell : `get-service healthservice`.  Si le service est arrêté, entrez la commande suivante dans PowerShell pour démarrer le service : `start-service healthservice`.  

4. Dans le journal des événements **Application et journaux des services\gestionnaire des opérations**, vous voyez l’événement 4502 et l’EventMessage contenant **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** avec la description suivante : *le certificat présenté par le service \<wsid\>.oms.opinsights.azure.com n’a pas été émis par une autorité de certification utilisée par les services Microsoft. Veuillez contacter votre administrateur réseau pour déterminer si un proxy en cours d’exécution intercepte la communication TLS/SSL. L’article KB3126513 contient des informations supplémentaires pour la résolution des problèmes de connectivité.*
    Une cause possible est que votre proxy ou votre pare-feu réseau bloque les communications vers Microsoft Azure.  Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur les ports 443.

Les journaux sont stockés localement sur chaque Worker hybride à l’emplacement C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Vous pouvez vérifier s’il existe des événements d’avertissement ou d’erreur écrits dans les journaux des évènements **Application et journal des services\Microsoft SMA\gestionnaire des opérations** et **Application et journal des services\gestionnaire des opérations** qui indiqueraient une connectivité ou tout autre problème affectant l’intégration du rôle à Azure Automation ou un problème lors de l’exécution d’opérations normales.  

## <a name="next-steps"></a>Étapes suivantes

* Consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.
* Pour obtenir des instructions sur la suppression des Runbooks Worker hybrides, consultez [Remove Azure Automation Hybrid Runbook Workers](automation-remove-hrw.md) (Supprimer des Runbooks Worker hybrides Azure Automation).