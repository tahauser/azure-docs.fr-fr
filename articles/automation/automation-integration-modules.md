---
title: Créer un module d’intégration Azure Automation
description: Ce didacticiel vous guide tout au long des procédures de création et de test de modules d’intégration Azure Automation. Il est complété par un exemple d’utilisation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: e1bcdb2938d1dcb2743b4c1c523ccefb322dd428
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="azure-automation-integration-modules"></a>Modules d’intégration Azure Automation
PowerShell est la technologie fondamentale derrière Azure Automation. Étant donné qu’Azure Automation est basé sur PowerShell, les modules PowerShell sont essentiels pour l’extensibilité d’Azure Automation. Dans cet article, nous vous guidons tout au long de l’utilisation détaillée des modules PowerShell dans Azure Automation, appelés « modules d’intégration », et vous présenterons les meilleures pratiques pour la création de vos propres modules PowerShell afin de garantir que ceux-ci fonctionnent en tant que modules d’intégration au sein d’Azure Automation. 

## <a name="what-is-a-powershell-module"></a>Qu’est-ce qu’un module PowerShell ?
Un module PowerShell est un groupe d’applets de commande PowerShell, telles que **Get-Date** ou **Copy-Item**, qui peut être utilisé à partir de la console PowerShell, de scripts, de flux de travail, de Runbooks et de ressources PowerShell DSC, telles que WindowsFeature ou File, pouvant être utilisées à partir de configurations PowerShell DSC. L’ensemble des fonctionnalités de PowerShell sont exposées via les applets de commande et les ressources DSC, et chaque applet de commande/ressource DSC est prise en charge par un module PowerShell ; un nombre important de ces modules est inclus dans PowerShell. Par exemple, l’applet de commande **Get-Date** fait partie du module PowerShell Microsoft.PowerShell.Utility, et l’applet de commande **Copy-Item** fait partie du module PowerShell Microsoft.PowerShell.Management. La ressource de Package DSC fait partie du module PowerShell PSDesiredStateConfiguration. Ces deux modules sont fournis avec PowerShell. Mais de nombreux modules PowerShell ne sont pas fournis avec PowerShell ; ils sont distribués avec des produits tiers ou internes, tels que System Center 2012 Configuration Manager ou par la vaste communauté PowerShell sur des sites comme PowerShell Gallery. Ces modules sont utiles car ils simplifient des tâches complexes grâce à la fonctionnalité d’encapsulation.  Vous pouvez en savoir plus sur les [modules PowerShell sur MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## <a name="what-is-an-azure-automation-integration-module"></a>Qu’est-ce qu’un module d’intégration Azure Automation ?
Un module d’intégration n’est pas différent d’un module PowerShell. Il s’agit simplement d’un module PowerShell contenant éventuellement un fichier supplémentaire : un fichier de métadonnées spécifiant un type de connexion Azure Automation à utiliser avec les applets de commande du module dans les Runbooks. Qu’ils contiennent ce fichier ou non, ces modules PowerShell peuvent être importés dans Azure Automation pour activer leurs applets de commande pour une utilisation dans les Runbooks et leurs ressources DSC disponibles au sein des configurations DSC. Dans les coulisses, Azure Automation stocke ces modules et, lors de l’exécution d’un travail de compilation de DSC ou d’une tâche de Runbook, les charges dans les bacs à sable (sandbox) Azure Automation où les Runbooks sont exécutés et les configurations DSC sont compilées. Toute ressource DSC dans les modules est automatiquement placée sur le serveur Automation DSC afin de pouvoir être extraite par les ordinateurs qui tentent d’appliquer des configurations DSC.  

Dans Azure Automation, nous vous fournissons un certain nombre de modules Azure PowerShell prêts à l’emploi, pour une automatisation immédiate de la gestion Azure. Vous pouvez également importer des modules PowerShell pour les intégrer à tout système, service ou outil que vous souhaitez. 

> [!NOTE]
> Certains modules sont fournis en tant que « modules globaux » dans le service Automation. Ces modules globaux sont disponibles lorsque vous créez un compte Automation. Nous les mettons parfois à jour ce qui les transfère automatiquement en dehors de votre compte Automation. Si vous ne souhaitez pas qu’ils soient mis à jour automatiquement, vous pouvez toujours importer le même module vous-même. Celui-ci primera sur la version du module global fournie dans le service. 

Le format dans lequel vous importez un package de module d’intégration est un fichier compressé portant le même nom que le module et une extension .zip. Il contient le module Windows PowerShell et tout fichier de prise en charge, notamment un fichier manifeste (.psd1), le cas échéant.

Si le module doit contenir un type de connexion Azure Automation, il doit également contenir un fichier portant le nom `<ModuleName>-Automation.json` qui spécifie les propriétés de type de connexion. Il s’agit d’un fichier json placé dans le dossier du module de votre fichier .zip compressé et contenant les champs d’une « connexion » ; ces derniers sont requis pour se connecter au système ou au service que représente le module. Cela crée un type de connexion dans Azure Automation. À l’aide de ce fichier, vous pouvez définir les noms de champ et les types, ainsi que si les champs doivent être chiffrés et/ou facultatifs, pour le type de connexion du module. Voici un modèle au format json :

```
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Si vous avez déjà déployé Service Management Automation et créé des packages de modules d’intégration pour vos Runbooks d’automation, vous devriez déjà connaître cette procédure. 

## <a name="authoring-best-practices"></a>Établissement de meilleures pratiques
Même si les modules d’intégration sont généralement des modules PowerShell, nous vous recommandons de prendre en compte certains éléments lors de la création d’un module PowerShell, pour une utilisation optimale de ce dernier dans Azure Automation. Certains d’entre eux sont propres à Azure Automation, et certains sont utiles pour améliorer le fonctionnement de vos modules dans le Workflow PowerShell, que vous utilisiez Azure Automation ou non. 

1. Incluez un résumé, une description et une URI d’aide pour chaque applet de commande dans le module. Dans PowerShell, vous pouvez définir certaines informations d’aide pour les applets de commande, pour que l’utilisateur puisse obtenir de l’aide quant à leur utilisation, avec l’applet de commande **Get-Help** . Par exemple, voici comment définir un synopsis et l’URI d’aide pour un module PowerShell écrit dans un fichier .psm1.<br>  
   
    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,
   
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,
   
       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )
   
    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken
   
    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
   
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred
   
    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
   <br> Cette information affichera de l’aide avec l’applet de commande **Get-Help** dans la console PowerShell, ainsi que dans Azure Automation.  Par exemple, lors de l’insertion d’activités lors de la création de runbook. En cliquant sur « Voir l’aide détaillée », l’URI d’aide s’ouvre dans un autre onglet du navigateur web que vous utilisez pour accéder à Azure Automation.<br>![Aide du module d’intégration](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. Si le module s’exécute sur un système distant,

    a. Il doit contenir un fichier de métadonnées du module d’intégration qui définit les informations nécessaires pour se connecter à ce système distant, c’est-à-dire le type de connexion.  
    b. Chaque applet de commande du module doit pouvoir accueillir un objet de connexion (une instance de ce type de connexion) en tant que paramètre.  

    Les applets de commande du module sont plus faciles à utiliser dans Azure Automation si vous autorisez le passage d’un objet, avec les champs du type de connexion en tant que paramètre, vers l’applet de commande. Ainsi, les utilisateurs ne sont pas obligés de mapper les paramètres de la ressource de connexion aux paramètres correspondants de l’applet de commande chaque fois qu’ils appellent une applet de commande. Selon l’exemple de Runbook ci-dessus, il utilise une ressource de connexion Twilio, appelée CorpTwilio, pour accéder à Twilio et renvoyer tous les numéros de téléphone du compte.  Remarquez comment il mappe les champs de la connexion aux paramètres de l’applet de commande.<br>
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
  
    Une approche plus simple consisterait à passer l’objet de connexion directement à l’applet de commande -
   
    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
   
      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
   
    Vous pouvez activer un tel comportement pour vos applets de commande en les autorisant à accepter un objet de connexion directement en tant que paramètre, au lieu d’accepter uniquement des champs de connexion en tant que paramètres. Généralement, vous voudrez disposer d’un jeu de paramètres pour chaque élément, pour qu’un utilisateur n’utilisant pas Azure Automation puisse appeler vos applets de commande sans créer une table de hachage agissant en tant qu’objet de connexion. Le jeu de paramètres **SpecifyConnectionFields** ci-dessous permet de faire passer, une par une, les propriétés de champ de connexion. **UseConnectionObject** vous permet de transmettre directement la connexion. Comme vous pouvez le voir, l’applet de commande Send-TwilioSMS dans le [module Twilio de PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) autorise le passage dans les deux cas : 
   
    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,
   
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,
   
         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection
   
       )
    }
    ```
   <br>
3. Définissez le type de sortie pour toutes les applets de commande dans le module. La définition d’un type de sortie pour une applet de commande permet à IntelliSense (au moment de la conception) de vous aider à déterminer les propriétés de sortie de l’applet de commande, à utiliser lors de la création. Cela est particulièrement utile lors de la création graphique d’un Runbook Azure Automation, où les connaissances au moment de la conception sont essentielles pour une expérience utilisateur conviviale de votre module.<br><br> ![Type de sortie de Runbook graphique](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Cela ressemble à la fonctionnalité de « frappe au kilomètre » de la sortie d’une applet de commande dans PowerShell ISE, sans avoir à exécuter celle-ci.<br><br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. Les applets de commande dans le module ne doivent pas prendre des types d’objets complexes en tant que paramètres. La différence entre PowerShell et le Workflow PowerShell réside dans le fait que ce dernier stocke des types complexes sous forme désérialisée. Les types primitifs resteront primitifs, mais les types complexes sont convertis en leurs versions désérialisées, qui sont avant tout des conteneurs de propriétés. Par exemple, si vous avez utilisé l’applet de commande **Get-Process** dans un Runbook (voire un Workflow PowerShell), elle retourne un objet de type [Deserialized.System.Diagnostic.Process], pas le type [System.Diagnostic.Process] attendu. Ce type a les mêmes propriétés que le type non désérialisé, mais aucune de ses méthodes. Et si vous essayez de transmettre cette valeur vers une applet de commande en tant que paramètre, bien que cette dernière attende une valeur [System.Diagnostic.Process] pour ce paramètre, vous obtenez l’erreur suivante : *Impossible de traiter la transformation d’argument sur le paramètre « process ». Error: "Cannot convert the "System.Diagnostics.Process (CcmExec)" value of type "Deserialized.System.Diagnostics.Process" to type "System.Diagnostics.Process" (Erreur : « impossible de convertir la valeur « System.Diagnostics.Process (CcmExec) » de type « Deserialized.System.Diagnostics.Process » en type « System.Diagnostics.Process »).*   Ceci provient de l’incompatibilité de type entre le type [System.Diagnostic.Process] attendu et le type [Deserialized.System.Diagnostic.Process] donné. Pour contourner ce problème, assurez-vous que les applets de commande de votre module ne prennent pas de types complexes en tant que paramètres. Voici la mauvaise façon de le faire.
   
    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
    <br>
    Et voici la bonne façon, en prenant un type primitif pouvant être utilisé en interne par l’applet de commande pour récupérer l’objet complexe et l’utiliser. Comme les applets de commande s’exécutent dans le cadre de PowerShell, et non de Workflow PowerShell, $process devient le type [System.Diagnostic.Process] correct au sein de l’applet de commande.  
   
    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName
   
      $process.Description
    }
    ```
   <br>
   Les ressources de connexion dans les Runbooks sont des tables de hachage ; un type complexe. Ces tables de hachage semblent cependant pouvoir être transmises sans problème dans les applets de commande pour leur paramètre -Connection, sans aucune exception de transtypage. Techniquement, certains types PowerShell peuvent être désérialisés, et peuvent donc être passés aux applets de commande pour les paramètres acceptant le type non-désérialisé. La table de hachage fait partie de ces types. Il est possible, pour les types définis par l’auteur d’un module, d’être implémentés d’une façon leur permettant de se désérialiser correctement. Certains compromis doivent cependant être pris en compte. Le type doit avoir un constructeur par défaut ainsi qu’un PSTypeConverter et toutes ses propriétés doivent être publiques. Toutefois, il est impossible de « réparer » les types prédéfinis que l’auteur du module ne possède pas, d’où la recommandation d’éviter les types complexes pour l’ensemble des paramètres. Conseil pour la création de Runbook : si, pour une raison quelconque, vos applets de commande doivent accueillir un paramètre de type complexe, ou si vous utilisez un autre module qui requiert un paramètre de type complexe, la solution de contournement dans les Runbooks PowerShell Workflow et les Workflows PowerShell dans un PowerShell local consiste à encapsuler, dans la même activité InlineScript, l’applet de commande qui génère le type complexe et l’applet de commande qui consomme le type complexe. Étant donné qu’InlineScript exécute son contenu sous forme de PowerShell au lieu de Workflow PowerShell, l’applet de commande qui génère le type complexe produit le type correct, pas le type complexe désérialisé.
5. Rendez toutes les applets de commande dans le module sans état. Le Workflow PowerShell exécute chaque applet de commande appelée dans le flux de travail dans une session distincte. Cela signifie que toute applet de commande, qui dépend de l’état de la session créée / modifiée par d’autres applets de commande dans le même module, ne fonctionnera pas dans les Runbooks PowerShell Workflow.  Voici un exemple de procédure à ne pas suivre.
   
    ```
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
   
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
   
       $output
    }
    ```
   <br>
6. Le module doit être entièrement contenu dans un package pouvant faire l’objet d’une copie Xcopy. Étant donné que les modules Azure Automation sont distribués aux bacs à sable (sandbox) lorsque les Runbooks doivent s’exécuter, ceux-ci doivent fonctionner indépendamment de l’hôte sur lequel ils s’exécutent. Cela signifie que vous devez être en mesure de compresser le package du module, le déplacer vers un autre hôte comportant une version identique ou plus récente de PowerShell, et le faire fonctionner normalement lorsqu’il est importé dans l’environnement PowerShell de cet hôte. Pour ce faire, le module ne doit pas dépendre de fichiers se trouvant à l’extérieur du dossier du module (le dossier compressé lors de l’importation dans Azure Automation), ni de seulement un jeu de paramètres unique du Registre sur un hôte, comme celui défini lors de l’installation d’un produit. Si cette meilleure pratique n’est pas respectée, le module ne sera pas utilisable dans Azure Automation.  

## <a name="next-steps"></a>Étapes suivantes

* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur la création de modules PowerShell, consultez [Writing a Windows PowerShell Module (Écriture d’un module Windows PowerShell)](https://msdn.microsoft.com/library/dd878310%28v=vs.85%29.aspx)

