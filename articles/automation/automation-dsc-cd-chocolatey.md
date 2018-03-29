---
title: Déploiement continu d’Azure Automation DSC avec Chocolatey
description: Déploiement continu du DevOps à l’aide d’Azure Automation DSC et du gestionnaire de packages Chocolatey  Exemple avec modèle ARM JSON complet et source PowerShell.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: e92bb8b4078bc5c85a639d3b5b38c124152576f7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-dsc-and-chocolatey"></a>Exemple d'utilisation : Déploiement continu sur des ordinateurs virtuels à l’aide d’Automation DSC et Chocolatey
L’univers des opérations de développement offre de nombreux outils conçus pour aider les développeurs à franchir plus facilement différents stades dans le pipeline de l’intégration continue.  Le nouveau service Azure Automation Desired State Configuration (DSC) vient aujourd’hui enrichir la liste des options disponibles pour les équipes DevOps.  Cet article explique comment configurer le déploiement continu (CD) pour un ordinateur Windows.  Vous pouvez facilement étendre la technique afin d’inclure autant d’ordinateurs Windows que vous le souhaitez dans le rôle (par exemple, un site Web), puis dans des rôles supplémentaires.

![Déploiement continu pour machines virtuelles IaaS](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>À un niveau élevé
Il y aurait beaucoup à dire sur ce sujet, mais il est heureusement possible de décomposer toutes ces informations en deux grands processus : 

* Écrire du code et le tester, puis créer et publication des packages d’installation pour les versions majeures et mineures du système. 
* Créer et gérer des machines virtuelles qui installeront et exécuteront le code dans les packages.  

Une fois ces deux processus en place, vous pouvez mettre automatiquement à jour le package exécuté sur n’importe quelle machine virtuelle au fur et à mesure que de nouvelles versions sont créées et déployées.

## <a name="component-overview"></a>Vue d’ensemble des composants
S’ils sont communément employés dans l’univers Linux, les gestionnaires de packages tels que [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) demeurent assez méconnus dans le monde de Windows.  [Chocolatey](https://chocolatey.org/) fait partie de ces packages. Pour mieux le découvrir, je vous invite à consulter le [blog](http://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) de Scott Hanselman qui lui est dédié.  En résumé, Chocolatey vous permet, à l’aide de la ligne de commande, d’installer des packages à partir d’un référentiel de packages central dans un système Windows.  Vous pouvez créer et gérer votre propre référentiel et Chocolatey peut installer des packages à partir de tous les référentiels que vous désignez, quel qu’en soit le nombre.

Desired State Configuration (DSC) ([vue d’ensemble](https://technet.microsoft.com/library/dn249912.aspx)) est un outil PowerShell qui vous permet de déclarer la configuration que vous souhaitez affecter à une machine.  Par exemple, vous pouvez vouloir installer Chocolatey et IIS, ouvrir le port 80 et installer la version 1.0.0 de votre site Web.  Le gestionnaire de configuration locale (LCM, Local Configuration Manager) de DSC implémente alors cette configuration. Un serveur Pull DSC contient un référentiel des configurations de vos machines. Le LCM résidant sur chaque ordinateur vérifie régulièrement si sa configuration correspond à la configuration enregistrée. Il peut signaler l’état ou tenter de réaligner la configuration de la machine sur la configuration enregistrée. Vous pouvez modifier la configuration enregistrée sur le serveur Pull de manière à aligner la configuration d’une machine ou d’un ensemble de machines sur la configuration modifiée.

Azure Automation est un service géré dans Microsoft Azure qui vous permet d’automatiser différentes tâches à l’aide de runbooks, de nœuds, d’informations d’identification et de ressources comme des planifications et des variables globales. Azure Automation DSC étend cette fonctionnalité d’automatisation pour intégrer les outils PowerShell DSC.  En voici une excellente [présentation](automation-dsc-overview.md).

Une ressource DSC est un module de code qui présente des fonctionnalités spécifiques, telles que la gestion de mise en réseau, Active Directory ou SQL Server.  La ressource DSC Chocolatey sait comment accéder à un serveur NuGet (entre autres), télécharger les packages, installer les packages et ainsi de suite.  Il existe de nombreuses autres ressources DSC dans la [PowerShell Gallery](http://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title).  Vous-même devez installer ces modules dans le serveur Pull Azure Automation DSC afin qu’ils puissent être utilisés par vos configurations.

Les modèles Resource Manager offrent un moyen de générer votre infrastructure par des déclarations (réseaux, sous-réseaux, sécurité du réseau et routage, équilibreurs de charge, NIC, machines virtuelles, etc.).  Voici un [article](../azure-resource-manager/resource-manager-deployment-model.md) qui compare le modèle de déploiement Resource Manager (déclaratif) avec le modèle de déploiement Azure Service Management (ASM, ou Classic) (impératif) et présente les fournisseurs des ressources principales, le calcul, le stockage et le réseau.

Un modèle Resource Manager se distingue par sa capacité à installer une extension de machine virtuelle dans la machine virtuelle à mesure de son approvisionnement.  Une extension de machine virtuelle possède des fonctionnalités spécifiques, telles que l’exécution d’un script personnalisé, l’installation d’un logiciel antivirus ou encore l’exécution d’un script de configuration DSC.  Il existe de nombreux autres types d’extensions de machine virtuelle.

## <a name="quick-trip-around-the-diagram"></a>Tour d’horizon rapide du diagramme
Commençons par le point de départ : vous écrivez votre code, vous le générez, vous le testez, puis vous créez un package d’installation.  Chocolatey peut gérer différents types de packages d’installation, tels que MSI, MSU ou ZIP.  Et vous disposez de toute la puissance de PowerShell pour effectuer l’installation si les fonctionnalités natives de Chocolatey ne sont pas tout à fait adaptées.  Placez le package dans un endroit accessible, par exemple un référentiel de packages.  Cet exemple utilise un dossier public dans un compte de stockage d’objets blob Azure, mais vous pouvez utiliser n’importe quel emplacement.  Chocolatey fonctionne en mode natif avec des serveurs NuGet et quelques autres serveurs pour la gestion des métadonnées de packages.  [Cet article](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) en décrit les options.  Cet exemple utilise NuGet.  Un Nuspec désigne les métadonnées concernant vos packages.  Les Nuspec sont « compilés » dans NuPkg et stockés sur un serveur NuGet.  Lorsque votre configuration demande un package par son nom et fait référence à un serveur NuGet, la ressource DSC Chocolatey (qui réside désormais sur la machine virtuelle) extrait le package et l’installe à votre place.  Vous pouvez également demander une version spécifique d’un package.

Dans la partie inférieure gauche de l’image, vous verrez un modèle Azure Resource Manager (ARM).  Dans cet exemple, l’extension de machine virtuelle enregistre la machine virtuelle auprès du serveur Pull Azure Automation DSC (autrement dit, un serveur d’extraction) en tant que nœud.  La configuration est stockée dans le serveur d’extraction.  En fait, elle est stockée à deux reprises : une première fois en tant que texte brut et une deuxième fois compilée dans un fichier MOF (pour ceux qui s’y connaissent).  Dans le portail, la structure MOF est une « configuration de nœud » (et non une simple « configuration »).  Il s’agit de l’artefact qui est associé à un nœud afin que le nœud connaisse sa configuration.  Les détails ci-dessous montrent comment affecter la configuration de nœud au nœud.

Vraisemblablement, vous le faites déjà au départ, ou du moins la majeure partie.  Créer un nuspec, le compiler et le stocker dans un serveur NuGet n’a rien de compliqué.  Et vous gérez déjà des machines virtuelles.  L’étape suivante du déploiement continu suppose de configurer le serveur d’extraction (une fois), d’y enregistrer vos nœuds (une fois) et d’y créer et stocker la configuration (au début).  Au fur et à mesure que les packages sont mis à niveau et déployés dans le référentiel, vous devez actualiser la configuration et la configuration des nœuds dans le serveur d’extraction (et répéter l’opération si besoin).

Si vous ne commencez pas par un modèle ARM, il n’y a aucun problème.  Il existe des applets de commande PowerShell conçues pour vous aider à enregistrer vos machines virtuelles auprès du serveur d’extraction et de tous les autres serveurs. Pour plus d’informations, consultez cet article : [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>Étape 1 : configuration du serveur Pull et du compte Automation
Ouvrez une ligne de commande PowerShell (Add-AzureRmAccount) authentifiée : (le processus peut prendre quelques minutes en attendant que le serveur d’extraction soit configuré)

    New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
    New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT 

Vous pouvez installer votre compte Automation dans l’une des régions suivantes (également appelées « emplacements ») : États-Unis de l’Est 2, Sud du centre des États-Unis, Gouvernement des États-Unis - Virginie, Europe de l’Ouest, Sud-Est asiatique, Japon de l’Est, Centre de l’Inde et Sud-Est de l’Australie, Centre du Canada, Europe du Nord.

## <a name="step-2-vm-extension-tweaks-to-the-arm-template"></a>Étape 2 : ajustement de l’extension de machine virtuelle au modèle ARM
Voir les détails de l’enregistrement d’une machine virtuelle (à l’aide de l’extension PowerShell DSC VM) fournis dans ce [modèle de démarrage rapide d’Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver).  Cette étape consiste à enregistrer votre nouvelle machine virtuelle auprès du serveur d’extraction dans la liste des nœuds DSC.  Une partie de cette inscription consiste à spécifier la configuration du nœud à appliquer au nœud.  Comme cette configuration de nœud n’a pas encore besoin d’exister déjà sur le serveur d’extraction, cette opération peut être effectuée pour la première fois à l’étape 4.  Mais ici, à l'étape 2, vous devez avoir choisi le nom du nœud et celui de la configuration.  Dans cet exemple d'utilisation, le nœud est ’isvbox’ et la configuration est ’ISVBoxConfig’.  Le nom de la configuration de nœud (à préciser dans DeploymentTemplate.json) est donc ’ISVBoxConfig.isvbox’.  

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>Étape 3 : ajout des ressources DSC requises au serveur Pull
La PowerShell Gallery est conçue pour installer les ressources DSC dans votre compte Azure Automation.  Accédez à la ressource que vous souhaitez ajouter et cliquez sur le bouton « Deploy to Azure Automation » (Déployer vers Azure Automation).

![Exemple de la PowerShell Gallery](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Une autre technique récemment ajoutée au portail Azure vous permet d’extraire de nouveaux modules ou de mettre à jour des modules existants. Cliquez sur la ressource Compte Automation, sur la vignette Ressources et enfin sur la vignette Modules.  L’icône Parcourir la galerie vous permet de consulter la liste des modules dans la galerie, d’étudier le tout plus en détail et enfin d’importer dans votre compte Automation. Il s’agit là d’un excellent moyen pour mettre à jour vos modules de temps à autre. De plus, la fonctionnalité d’importation vérifie les dépendances avec d’autres modules pour garantir que rien n’est désynchronisé.

Il existe aussi une approche manuelle.  La structure de dossier d’un module d’intégration PowerShell pour un ordinateur Windows est un peu différente de celle à laquelle s’attend Azure Automation.  Cette différence nécessite une légère modification de votre part.  Mais il n’y a là rien de compliqué, et vous n’avez à effectuer cette opération qu’une seule fois par ressource (sauf si vous souhaitez effectuer une mise à niveau ultérieurement).  Pour plus d’informations sur la création de modules d’intégration PowerShell, consultez cet article : [Création de modules d’intégration pour Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

* Installez le module dont vous avez besoin sur votre station de travail, comme suit :
  * Installez [Windows Management Framework v5](http://aka.ms/wmf5latest) (inutile pour Windows 10)
  * `Install-Module –Name MODULE-NAME` &lt; — récupère le module à partir de la PowerShell Gallery 
* Copiez le dossier de module situé dans le répertoire `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` dans un dossier temporaire 
* Supprimez les modèles et la documentation dans le dossier principal 
* Compressez le dossier principal en attribuant au fichier ZIP exactement le même nom que celui du dossier 
* Placez le fichier ZIP dans un emplacement HTTP accessible, par exemple un stockage d’objets blob dans un compte de stockage Azure.
* Exécutez cette commande PowerShell :
  
      New-AzureRmAutomationModule `
          -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
          -Name MODULE-NAME –ContentLink "https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip"

L’exemple fourni exécute ces étapes pour cChoco et xNetworking. Consultez les [notes](#notes) relatives au traitement spécial pour cChoco.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>Étape 4 : ajout de la configuration de nœud au serveur Pull
L’importation et la compilation initiales de votre configuration dans le serveur Pull ne présentent aucune difficulté particulière.  Toutes les autres opérations d’importation/compilation de la même configuration auront exactement le même aspect.  Chaque fois que vous mettez à jour votre package et que vous devez la mettre en production, vous devez effectuer cette étape après avoir vérifié que le fichier de configuration est correct et qu’il comporte la nouvelle version de votre package.  Voici le fichier de configuration et la commande PowerShell :

ISVBoxConfig.ps1:

    Configuration ISVBoxConfig 
    { 
        Import-DscResource -ModuleName cChoco 
        Import-DscResource -ModuleName xNetworking

        Node "isvbox" {   

            cChocoInstaller installChoco 
            { 
                InstallDir = "C:\choco" 
            }

            WindowsFeature installIIS 
            { 
                Ensure="Present" 
                Name="Web-Server" 
            }

            xFirewall WebFirewallRule 
            { 
                Direction = "Inbound" 
                Name = "Web-Server-TCP-In" 
                DisplayName = "Web Server (TCP-In)" 
                Description = "IIS allow incoming web site traffic." 
                DisplayGroup = "IIS Incoming Traffic" 
                State = "Enabled" 
                Access = "Allow" 
                Protocol = "TCP" 
                LocalPort = "80" 
                Ensure = "Present" 
            }

            cChocoPackageInstaller trivialWeb 
            {            
                Name = "trivialweb" 
                Version = "1.0.0" 
                Source = “MY-NUGET-V2-SERVER-ADDRESS” 
                DependsOn = "[cChocoInstaller]installChoco", 
                "[WindowsFeature]installIIS" 
            } 
        }    
    }

New-ConfigurationScript.ps1:

    Import-AzureRmAutomationDscConfiguration ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 ` 
        -Published –Force

    $jobData = Start-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -ConfigurationName ISVBoxConfig 

    $compilationJobId = $jobData.Id

    Get-AzureRmAutomationDscCompilationJob ` 
        -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT ` 
        -Id $compilationJobId

Ces étapes génèrent une nouvelle configuration de nœud nommée « ISVBoxConfig.isvbox » placée sur le serveur d’extraction.  Le nom de la configuration de nœuds est créé en tant que « configurationName.nodeName ».

## <a name="step-5-creating-and-maintaining-package-metadata"></a>Étape 5 : création et gestion des métadonnées de packages
Pour chaque package que vous placez dans le référentiel de packages, vous avez besoin d’un nuspec descriptif.  Ce nuspec doit être compilé et stocké sur votre serveur NuGet. Cette opération est décrite [ici](http://docs.nuget.org/create/creating-and-publishing-a-package).  Vous pouvez utiliser MyGet.org comme serveur NuGet.  Bien que ce service soit payant, ils proposent gratuitement une référence SKU pour débutants.  Rendez-vous sur NuGet.org pour obtenir des instructions sur l’installation de votre propre serveur NuGet pour vos packages privés.

## <a name="step-6-tying-it-all-together"></a>Étape 6 : Exemple complet
Chaque fois qu'une version passe l'assurance qualité et est approuvée pour le déploiement, le package est créé, nuspec et nupkg sont mis à jour et déployés sur le serveur NuGet.  En outre, la configuration (étape 4 ci-dessus) doit être mise à jour pour correspondre au nouveau numéro de version.  Elle doit être envoyée au serveur d’extraction puis compilée.  À ce stade, les machines virtuelles qui dépendent de cette configuration doivent extraire la mise à jour et l'installer.  Chacune de ces mises à jour est simple et ne nécessite qu’une ou deux lignes PowerShell.  Dans le cas de Visual Studio Team Services, certaines d’entre elles sont encapsulées dans les tâches de génération et peuvent être chaînées dans une build.  Cet [article](https://www.visualstudio.com/en-us/docs/alm-devops-feature-index#continuous-delivery) fournit plus de détails.  Ce [référentiel GitHub](https://github.com/Microsoft/vso-agent-tasks) détaille les différentes tâches de génération disponibles.

## <a name="notes"></a>notes
Dans cet exemple d’utilisation, nous partons d’une machine virtuelle provenant d’une image Windows Server 2012 R2 générique de la galerie Azure.  Vous pouvez démarrer à partir de n’importe quelle image stockée, puis la modifier avec la configuration DSC.  Toutefois, la modification d’une configuration intégrée à une image est beaucoup plus difficile que la mise à jour dynamique de la configuration à l’aide de DSC.

Vous n’êtes pas obligé d’utiliser un modèle ARM ou l’extension de machine virtuelle pour utiliser cette technique avec vos machines virtuelles.  De même, il n’est pas nécessaire que vos machines virtuelles se trouvent sur Azure au niveau de la gestion des CD.  Vous devez simplement faire en sorte d’installer Chocolatey et de configurer LCM sur la machine virtuelle afin qu’elle parvienne à localiser le serveur d’extraction.  

Bien évidemment, lorsque vous mettez à jour un package sur une machine virtuelle en production, vous devez sortir cette machine virtuelle pendant l’installation de la mise à jour.  La procédure varie considérablement.  Par exemple, avec une machine virtuelle placée derrière un équilibreur de charge Azure, vous pouvez ajouter une sonde personnalisée.  Lors de la mise à jour de la machine virtuelle, faites en sorte que le point de terminaison de la sonde retourne un code 400.  La solution nécessaire pour forcer cette modification peut se trouver à l’intérieur même de votre configuration, tout comme la solution qui lui permettra de retourner un code 200 une fois la mise à jour terminée.

La source complète de cet exemple se trouve dans ce [projet Visual Studio](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) sur GitHub.

## <a name="related-articles"></a>Articles connexes
* [Vue d'ensemble d'Azure Automation DSC](automation-dsc-overview.md)
* [Applets de commande Azure Automation DSC](https://msdn.microsoft.com/library/mt244122.aspx)
* [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md)

