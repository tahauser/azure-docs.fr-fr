---
title: Migration d’Orchestrator vers Azure Automation
description: Décrit comment migrer des Runbooks et des packs d'intégration de System Center Orchestrator vers Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: b50f9973ef7ea5f884930d4f1ee67f886cc36ad0
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migration d'Orchestrator vers Azure Automation (bêta)
Dans [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) , les Runbooks sont basés sur les activités de packs d'intégration spécifiquement écrits pour Orchestrator, tandis que dans Azure Automation, ils sont basés sur Windows PowerShell.  [Runbooks graphiques](automation-runbook-types.md#graphical-runbooks) dans Azure Automation ont une apparence semblable aux Runbooks Orchestrator, avec leurs activités représentant les applets de commande PowerShell, les Runbooks enfants et les ressources.

Le [Kit de migration de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclut des outils destinés à vous aider à convertir des Runbooks Orchestrator en Runbooks Azure Automation.  En plus de convertir les Runbooks proprement dits, vous devez convertir les packs d'intégration avec les activités qu'ils utilisent en modules d'intégration à l'aide d'applets de commande Windows PowerShell.  

Voici le processus de base pour la conversion de Runbooks Orchestrator en Runbooks Azure Automation.  Chacune de ces étapes est décrite en détail dans les sections ci-dessous.

1. Téléchargez le [Kit de migration de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) qui contient les outils et modules décrits dans cet article.
2. Importez le [module d'activités Standard](#standard-activities-module) dans Azure Automation.  Cela inclut les versions converties des activités Orchestrator standard qui peuvent être utilisées par les Runbooks convertis.
3. Importez les [modules d'intégration de System Center Orchestrator](#system-center-orchestrator-integration-modules) dans Azure Automation pour les packs d'intégration utilisés par vos Runbooks ayant accès à System Center.
4. Convertissez les packs d'intégration personnalisés et tiers à l'aide du [convertisseur de packs d'intégration](#integration-pack-converter) , puis installez-les dans Azure Automation.
5. Convertissez les Runbooks Orchestrator à l'aide du [convertisseur de Runbooks](#runbook-converter) (bientôt disponible), puis installez-les dans Azure Automation.
6. Créez manuellement des ressources Orchestrator requises dans Azure Automation, étant donné que le convertisseur de Runbooks ne convertit pas ces ressources.
7. Configurez un [Runbook Worker hybride](#hybrid-runbook-worker) dans votre centre de données local pour exécuter les Runbooks convertis qui accéderont aux ressources locales.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) stocke et exécute des Runbooks dans votre centre de données local comme Orchestrator, et utilise les mêmes modules d'intégration qu'Azure Automation. Lorsqu'il est disponible, le [convertisseur de Runbooks](#runbook-converter) convertit les Runbooks Orchestrator en Runbooks graphiques, lesquels ne sont néanmoins pas pris en charge dans SMA.  Vous pouvez toujours installer le [module d’activités standard](#standard-activities-module) et les [modules d’intégration de System Center Orchestrator](#system-center-orchestrator-integration-modules) dans SMA, mais vous devez [réécrire vos Runbooks](http://technet.microsoft.com/library/dn469262.aspx) manuellement.

## <a name="hybrid-runbook-worker"></a>Runbook Worker hybride
Dans Orchestrator, les Runbooks sont stockés sur un serveur de base de données et s'exécutent sur des serveurs de Runbooks, les deux se trouvant dans votre centre de données local.  Dans Azure Automation, les Runbooks sont stockés dans le cloud Azure et peuvent s'exécuter dans votre centre de données locales à l'aide d'un [Runbook Worker hybride](automation-hybrid-runbook-worker.md).  C'est ainsi que vous exécuterez généralement les Runbooks convertis à partir d'Orchestrator, car ils sont conçus pour s'exécuter sur des serveurs locaux.

## <a name="integration-pack-converter"></a>convertisseur de packs d'intégration
Le convertisseur de packs d'intégration convertit les packs d'intégration créés à l'aide d' [Orchestrator Integration Toolkit (OIT)](http://technet.microsoft.com/library/hh855853.aspx) en modules d'intégration basés sur Windows PowerShell et qui peuvent être importés dans Azure Automation ou dans Service Management Automation.  

Lorsque vous exécutez le convertisseur de packs d'intégration, un Assistant vous permettant de sélectionner un fichier de pack d'intégration (.oip) s'affiche.  Cet Assistant répertorie ensuite les activités incluses dans ce pack d'intégration et vous permet de sélectionner celles qui seront migrées.  Une fois l'Assistant terminé, il crée un module qui inclut une applet de commande correspondant pour chacune des activités du pack d'intégration d'origine.

### <a name="parameters"></a>Paramètres
Toutes les propriétés d'une activité du pack d'intégration sont converties en paramètres de l'applet de commande correspondante dans le module d'intégration.  Les applets de commande Windows PowerShell possèdent un ensemble de [paramètres communs](http://technet.microsoft.com/library/hh847884.aspx) qui peuvent être utilisés avec toutes les applets de commande.  Par exemple, le paramètre -Verbose entraîne l'affichage, par une applet de commande, des informations détaillées relatives à son fonctionnement.  Aucune applet de commande ne peut avoir un paramètre portant le même nom qu'un paramètre commun.  Si une activité a une propriété portant le même nom qu'un paramètre commun, l'Assistant vous invite à fournir un autre nom pour le paramètre.

### <a name="monitor-activities"></a>Activités d'analyse
Dans Orchestrator, les Runbooks d’analyse commencent par une [activité d’analyse](http://technet.microsoft.com/library/hh403827.aspx) et s’exécutent en permanence, attendant d’être appelés par un événement particulier.  Azure Automation ne prend pas en charge les Runbooks d'analyse. Les activités d'analyse se trouvant dans le pack d'intégration ne seront donc pas converties.  Au lieu de cela, une applet de commande réservée est créée dans le module d'intégration pour l'activité d'analyse.  Cette applet de commande n'a aucune fonctionnalité, mais elle permet l'installation de tout Runbook converti qui l'utilise.  Ce Runbook ne pourra pas s'exécuter dans Azure Automation, mais il peut être installé afin que vous puissiez le modifier.

### <a name="integration-packs-that-cannot-be-converted"></a>Packs d'intégration ne pouvant pas être convertis
Les packs d'intégration qui n'ont pas été créés avec OIT ne peuvent pas être convertis avec le convertisseur de packs d'intégration. Il existe également des packs d'intégration fournis par Microsoft qui ne peuvent actuellement pas être convertis avec cet outil.  Les versions converties de ces packs d'intégration sont [fournies pour le téléchargement](#system-center-orchestrator-integration-modules) afin de pouvoir être installées dans Azure Automation ou Service Management Automation.

## <a name="standard-activities-module"></a>module d'activités Standard
Orchestrator inclut un ensemble d' [activités standard](http://technet.microsoft.com/library/hh403832.aspx) qui ne sont pas incluses dans un pack d'intégration, mais qui sont utilisées par de nombreux Runbooks.  Le module d’activités standard est un module d’intégration qui inclut une applet de commande équivalente pour chacune de ces activités.  Vous devez installer le module d'intégration dans Azure Automation avant d'importer des Runbooks convertis qui utilisent une activité standard.

En plus de prendre en charge les Runbooks convertis, les applets de commande du module d'activités standard peuvent être utilisées par une personne ayant une bonne connaissance d'Orchestrator pour créer de nouveaux Runbooks dans Azure Automation.  Alors que les fonctionnalités de toutes les activités standard peuvent être effectuées à l'aide d'applets de commande, il est possible qu'elles fonctionnent différemment.  Les applets de commande du module d'activités standard converties fonctionnent de la même manière que leurs activités correspondantes et utilisent les mêmes paramètres.  Cela peut aider l'auteur des Runbooks Orchestrator existants dans sa transition vers les Runbooks Azure Automation.

## <a name="system-center-orchestrator-integration-modules"></a>modules d'intégration de System Center Orchestrator
Microsoft fournit des [packs d'intégration](http://technet.microsoft.com/library/hh295851.aspx) destinés à la création des Runbooks afin d'automatiser les composants de System Center et d'autres produits.  Certains de ces packs d'intégration sont actuellement basés sur OIT, mais ne peuvent actuellement pas être convertis en modules d'intégration en raison de problèmes connus.  [modules d'intégration de System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) incluent des versions converties de ces packs d'intégration qui peuvent être importées dans Azure Automation et dans Service Management Automation.  

Avec la version RTM de cet outil, les versions mises à jour des packs d'intégration basés sur OIT pouvant être convertis par le convertisseur de packs d'intégration seront publiées.  Des conseils seront également donnés pour vous aider à convertir des Runbooks à l'aide d'activités des packs d'intégration non basés sur OIT.

## <a name="runbook-converter"></a>convertisseur de Runbooks
Le convertisseur de Runbooks convertit les Runbooks Orchestrator en [Runbooks graphiques](automation-runbook-types.md#graphical-runbooks) qui peuvent être importés dans Azure Automation.  

Le convertisseur de Runbooks est implémenté sous forme de module PowerShell avec une applet de commande appelé **ConvertFrom-SCORunbook** qui effectue la conversion.  Lorsque vous installez l'outil, il crée un raccourci vers une session PowerShell qui charge l'applet de commande.   

Voici le processus de base pour la conversion d'un Runbook Orchestrator et son importation dans Azure Automation.  Les sections suivantes fournissent plus de détails sur l'utilisation de l'outil et l'utilisation de Runbooks convertis.

1. Exporter un ou plusieurs Runbooks depuis Orchestrator.
2. Obtenir des modules d'intégration pour toutes les activités dans le Runbook.
3. Convertir les Runbooks Orchestrator dans le fichier exporté.
4. Passer en revue les informations des journaux pour valider la conversion et déterminer les tâches manuelles requises.
5. Importer les Runbooks convertis dans Azure Automation.
6. Créer tous les éléments requis dans Azure Automation.
7. Modifier le Runbook dans Azure Automation pour modifier toutes les activités requises.

### <a name="using-runbook-converter"></a>Utilisation du convertisseur de Runbooks
La syntaxe de **ConvertFrom-SCORunbook** se présente comme suit :

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>

* RunbookPath - chemin d'accès au fichier d'exportation contenant les Runbooks à convertir.
* Module - liste délimitée par des virgules de modules d'intégration contenant des activités dans les Runbooks.
* OutputFolder - chemin d'accès au dossier pour créer des Runbooks graphiques convertis.

L’exemple de commande suivant convertit les Runbooks dans le fichier d’exportation **MyRunbooks.ois_export**.  Ces Runbooks utilisent les packs d'intégration Active Directory et Data Protection Manager.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"


### <a name="log-files"></a>Fichiers journaux
Le convertisseur de Runbooks créera les fichiers journaux suivants au même emplacement que le Runbook converti.  Si les fichiers existent déjà, ils sont remplacés par les informations de la dernière conversion.

| Fichier | Sommaire |
|:--- |:--- |
| Convertisseur de Runbooks - Progress.log |Étapes détaillées de la conversion incluant des informations sur chaque activité convertie avec succès et un avertissement pour chaque activité non convertie. |
| Convertisseur de Runbooks - Summary.log |Résumé de la dernière conversion, y compris tous les avertissements et le suivi des tâches que vous devez effectuer, par exemple la création d'une variable requise pour le Runbook converti. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportation de Runbooks Orchestrator
Le convertisseur de Runbooks fonctionne avec un fichier exporté Orchestrator qui contient un ou plusieurs Runbooks.  Il créera un Runbook Azure Automation correspondant pour chaque Runbook Orchestrator dans le fichier d'exportation.  

Pour exporter un Runbook à partir d'Orchestrator, cliquez sur le nom du Runbook dans Runbook Designer et sélectionnez **Exporter**.  Pour exporter tous les Runbooks, cliquez avec le bouton droit sur le nom du dossier, puis sélectionnez **Exporter**.

### <a name="runbook-activities"></a>Activités de Runbook
Le convertisseur de Runbooks convertit chaque activité du Runbook Orchestrator en activité correspondante dans Azure Automation.  Pour les activités qui ne peuvent pas être converties, une activité d'espace réservé avec un texte d'avertissement est créée dans le Runbook.  Après avoir importé le Runbook converti dans Azure Automation, vous devez remplacer toutes ces activités par des activités valides qui effectuent la fonctionnalité requise.

Toutes les activités d'Orchestrator dans le [Module d'activités standard](#standard-activities-module) seront converties.  Certaines activités standard d'Orchestrator ne sont pas dans ce module et ne sont pas converties.  Par exemple, **Envoyer un événement de plate-forme** n'a pas d'équivalent dans Azure Automation, dans la mesure où cet événement est spécifique à Orchestrator.

[Activités d'analyse](https://technet.microsoft.com/library/hh403827.aspx) ne sont pas converties, dans la mesure où elles n'ont pas d'équivalent dans Azure Automation.  Les activités de type Analyse dans les [packs d'intégration convertis](#integration-pack-converter) qui seront convertis en activité d'espace réservé constituent une exception.

Toute activité d'un [pack d'intégration converti](#integration-pack-converter) sera convertie si vous fournissez le chemin d'accès au module d'intégration avec le paramètre **Modules** .  Pour les Packs d'intégration de System Center, vous pouvez utiliser les [Modules d'intégration de System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Ressources Orchestrator
Le convertisseur de Runbooks convertit uniquement les Runbooks, pas les autres ressources Orchestrator telles que les compteurs, les variables ou les connexions.  Les compteurs ne sont pas pris en charge dans Azure Automation.  Les variables et les connexions sont pris en charge, mais vous devez les créer manuellement.  Les fichiers journaux vous informeront si le Runbook a besoin de ces ressources et spécifiera les ressources correspondantes que vous devrez créer dans Azure Automation pour que le Runbook converti fonctionne correctement.

Par exemple, un Runbook peut utiliser une variable pour remplir une valeur particulière dans une activité.  Le Runbook converti convertira l'activité et spécifiera dans Azure Automation une ressource de variable portant le même nom que la variable Orchestrator.  Ceci sera consigné dans le fichier **Convertisseur de Runbooks - Summary.log** créé après la conversion.  Vous devrez créer manuellement cette ressource de variable dans Azure Automation avant d'utiliser le Runbook.

### <a name="input-parameters"></a>Paramètres d'entrée
Les Runbooks Orchestrator acceptent des paramètres d'entrée avec l'activité **Initialiser des données** .  Si le Runbook en cours de conversion inclut cette activité, un [Paramètre d'entrée](automation-graphical-authoring-intro.md#runbook-input-and-output) dans le Runbook d'Azure Automation est créé pour chaque paramètre dans l'activité.  Une activité de [Contrôle de script de flux de travail](automation-graphical-authoring-intro.md#activities) est créée dans le Runbook converti qui extrait et renvoie chaque paramètre.  Toutes les activités dans le Runbook qui utilisent un paramètre d'entrée font référence à la sortie de cette activité.

Cette stratégie est utilisée parce qu'elle reflète le mieux la fonctionnalité dans le Runbook Orchestrator.  Les activités dans de nouveaux Runbooks graphiques doivent faire directement référence aux paramètres d'entrée utilisant une source de données d'entrée Runbook.

### <a name="invoke-runbook-activity"></a>Appeler l'activité Runbook
Les Runbooks d'Orchestrator font démarrer d'autres Runbooks avec l'activité **Appeler Runbook** . Si le Runbook en cours de conversion inclut cette activité et que l'option **Attendre la fin de l'exécution** est définie, une activité de Runbook est créée pour lui dans le Runbook converti.  Si l’option **Attendre la fin de l’exécution** n’est pas définie, une activité de script de flux de travail utilisant **Start-AzureAutomationRunbook** pour démarrer le Runbook est créée.  Après avoir importé le Runbook converti dans Azure Automation, vous devez modifier cette activité avec les informations spécifiées dans l'activité.

## <a name="related-articles"></a>Articles connexes
* [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Runbook Worker hybride](automation-hybrid-runbook-worker.md)
* [Activités standard d'Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
* [Télécharger le kit de migration System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
