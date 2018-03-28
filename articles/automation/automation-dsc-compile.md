---
title: Compilation de configurations dans Azure Automation DSC
description: Cet article explique comment compiler des configurations d’état souhaité (DSC) pour Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: 2df9512a70f8299ad9282894c0ae0805d05473bd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Compilation de configurations dans Azure Automation DSC

Dans Azure Automation, vous pouvez compiler des configurations d’état souhaité (DSC) de deux manières : dans le portail Azure et avec Windows PowerShell. Le tableau suivant vous aide à déterminer quand utiliser chaque méthode en fonction des caractéristiques de chacune :

### <a name="azure-portal"></a>Portail Azure

* Méthode la plus simple avec une interface utilisateur interactive
* Formulaire pour fournir les valeurs de paramètres simples
* Suivi aisé de l’état des tâches
* Accès authentifié avec ouverture de session Azure

### <a name="windows-powershell"></a>Windows PowerShell

* Appel à partir de la ligne de commande avec les applets de commande Windows PowerShell
* Possibilité d’inclusion dans une solution automatisée à plusieurs étapes
* Fourniture de valeurs de paramètres simples et complexes
* Suivi de l’état des tâches
* Client requis pour prendre en charge les applets de commande PowerShell
* Transmission ConfigurationData
* Compilation de configurations utilisant des informations d’identification

Une fois que vous avez choisi une méthode de compilation, suivez les procédures ci-dessous pour lancer la compilation.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Compilation d’une configuration DSC avec le portail Azure

1. Dans votre compte Automation, cliquez sur **Configurations DSC**.
2. Cliquez sur une configuration pour ouvrir son panneau.
3. Cliquez sur **Compiler**.
4. Si la configuration n’a aucun paramètre, vous devez confirmer si vous souhaitez la compiler. Si la configuration a des paramètres, le panneau **Compiler la configuration** s’ouvre et vous permet de fournir la valeur des paramètres. Pour plus d’informations sur les paramètres, consultez la section [**Paramètres de base**](#basic-parameters) suivante.
5. Le panneau **Tâche de compilation** est ouvert de sorte que vous pouvez suivre l’état de la tâche de compilation, ainsi que les configurations de nœud (documents de configuration MOF) qui ont dû être placés sur le serveur collecteur Azure Automation DSC.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Compilation d’une configuration DSC avec Windows PowerShell

Vous pouvez utiliser [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) pour démarrer la compilation avec Windows PowerShell. L’exemple de code suivant commence la compilation d’une configuration DSC appelée **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` renvoie un objet de tâche de compilation que vous pouvez utiliser pour suivre son état. Vous pouvez ensuite utiliser cet objet de tâche de compilation avec [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) pour déterminer l’état de la tâche de compilation, et [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) pour afficher ses flux (sortie). L’exemple de code suivant démarre la compilation de la configuration **SampleConfig** , attend qu’elle soit terminée, puis affiche ses flux.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Paramètres de base
La déclaration de paramètres dans les configurations DSC, y compris les types de paramètres et les propriétés, fonctionne de la même manière que pour les runbooks Azure Automation. Consultez [Démarrage d’un runbook dans Azure Automation](automation-starting-a-runbook.md) pour en savoir plus sur les paramètres de runbooks.

L’exemple suivant utilise deux paramètres appelés **FeatureName** et **IsPresent** pour déterminer les valeurs des propriétés dans la configuration du nœud **ParametersExample.sample**, générée pendant la compilation.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

Vous pouvez compiler les configurations DSC qui utilisent des paramètres de base dans le portail Azure Automation DSC ou avec Azure PowerShell :

### <a name="portal"></a>Portail

Dans le portail, vous pouvez entrer des valeurs de paramètre après avoir cliqué sur **Compiler**.

![texte de remplacement](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell requiert des paramètres dans une [table de hachage](http://technet.microsoft.com/library/hh847780.aspx) où la clé correspond au nom de paramètre et la valeur est égale à la valeur du paramètre.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Pour plus d’informations sur la transmission d’informations d’identification PowerShell en tant que paramètres, voir la section <a href="#credential-assets">**Ressources d’informations d’identification**</a> ci-dessous.

## <a name="composite-resources"></a>Ressources composites

Les **ressources composites** vous permettent d’utiliser des configurations DSC en tant que ressources imbriquées à l’intérieur d’une configuration. Cela vous permet d’appliquer plusieurs configurations à une seule ressource. Consultez [Ressources composites : Utilisation d’une configuration DSC en tant que ressource](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) pour en savoir plus sur les **Ressources composites**

> [!NOTE]
> Afin que la compilation réalisée par les **Ressources composites** se déroule normalement, vous devez en premier lieu vous assurer que toutes les ressources DSC sur lesquelles le composite s’appuie soient d’abord installées dans le dépôt Modules de compte Azure Automation, sinon l’importation est incorrecte.

Pour ajouter une **Ressource composite** DSC, vous devez ajouter le module de ressources à une archive (*.zip). Accédez au dépôt Modules sur votre compte Azure Automation. Cliquez ensuite sur le bouton « Ajouter un module ».

![Ajout d’un module](./media/automation-dsc-compile/add_module.png)

Accédez au répertoire où se trouve votre archivage. Sélectionnez le fichier d’archive et cliquez sur OK.

![Sélection d’un module](./media/automation-dsc-compile/select_dscresource.png)

Vous revenez au répertoire des modules, où vous pouvez surveiller l’état de votre **Ressource composite** pendant sa décompression et son inscription auprès d’Azure Automation.

![Importation d’une ressource composite](./media/automation-dsc-compile/register_composite_resource.png)

Une fois le module enregistré, vous pouvez cliquer dessus pour vérifier la disponibilité des **Ressources composites** qui peuvent désormais être utilisées dans une configuration.

![Validation de la ressource composite](./media/automation-dsc-compile/validate_composite_resource.png)

Vous pouvez ainsi appeler la **Ressource composite** dans votre configuration de la manière suivante :

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** vous permet de séparer la configuration structurelle de toute configuration spécifique de l’environnement lors de l’utilisation de la configuration de l’état souhaité PowerShell. Consultez [Distinguer « objet » et « emplacement » dans la configuration de l’état souhaité PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) pour en savoir plus sur **ConfigurationData**.

> [!NOTE]
> Vous pouvez utiliser **ConfigurationData** lors de la compilation dans Azure Automation DSC à l’aide d’Azure PowerShell, mais pas dans le portail Azure.

L’exemple de configuration DSC suivant utilise **ConfigurationData** via les mots-clés **$ConfigurationData** et **$AllNodes**. Vous avez également besoin du module [**xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/) pour cet exemple :

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Vous pouvez compiler la configuration DSC précédente avec PowerShell. La commande PowerShell suivante ajoute deux configurations de nœud au serveur collecteur Azure Automation DSC : **ConfigurationDataSample.MyVM1** et **ConfigurationDataSample.MyVM3** :

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Éléments multimédias

Les références de ressources sont les mêmes dans les configurations Azure Automation DSC et les runbooks. Consultez les liens suivants pour plus d’informations :

* [Certificats](automation-certificates.md)
* [Connexions](automation-connections.md)
* [Informations d'identification](automation-credentials.md)
* [Variables](automation-variables.md)

### <a name="credential-assets"></a>Ressources d’informations d’identification

Bien que les configurations DSC dans Azure Automation puissent se rapporter à des ressources d’informations d’identification utilisant **Get-AutomationPSCredential**, les ressources d’information d’identification peuvent également transmises via des paramètres, si vous le souhaitez. Si une configuration accepte un paramètre de type **PSCredential** , vous devez transmettre le nom de chaîne d’une ressource d’informations d’identification Azure Automation comme valeur de ce paramètre, plutôt qu’un objet PSCredential. En arrière-plan, la ressource d’informations d’identification Azure Automation portant le même nom est récupérée et transmise à la configuration.

Conserver les informations d’identification en sûreté dans les configurations de nœud (documents de configuration MOF) nécessite le chiffrement des informations d’identification dans le fichier MOF de configuration de nœud. Azure Automation va encore plus loin et chiffre la totalité du fichier MOF. Cependant, actuellement, vous devez indiquer à la configuration de l’état souhaité PowerShell que vous êtes d’accord pour que les informations d’identification soient extraites en texte brut lors de la génération du fichier MOF de configuration de nœud, parce que la configuration de l’état souhaité PowerShell ne sait pas qu’Azure Automation va chiffrer l’intégralité du fichier MOF après sa génération via une tâche de compilation.

Vous pouvez indiquer à la configuration DSC PowerShell que vous êtes d’accord pour que les informations d’identification soient extraites en texte brut dans les fichiers MOF de configuration de nœud générés en utilisant [**ConfigurationData**](#configurationdata). Vous devez transmettre `PSDscAllowPlainTextPassword = $true` par le biais de **ConfigurationData** pour chaque nom de bloc de nœuds qui apparaît dans la configuration DSC et utilise les informations d’identification.

L’exemple suivant montre une configuration de l’état souhaité qui utilise une ressource d’informations d’identification Automation.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Vous pouvez compiler la configuration DSC précédente avec PowerShell. La commande PowerShell suivante ajoute deux configurations de nœud au serveur collecteur Azure Automation DSC : **CredentialSample.MyVM1** et **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

> [!NOTE]
> Lorsque la compilation est terminée, l’erreur suivante peut s’afficher : **Le module « Microsoft.PowerShell.Management » n’a pas été importé, car le composant logiciel enfichable « Microsoft.PowerShell.Management » avait déjà été importé.** Vous pouvez ignorer cet avertissement sans problème.

## <a name="importing-node-configurations"></a>Importation de configurations de nœuds

Vous pouvez également importer les configurations de nœud (MOF) que vous avez compilées hors d’Azure. Cette configuration de nœuds offre l’avantage de pouvoir être signée.
Une configuration de nœuds signée est vérifiée localement sur un nœud géré par l’agent DSC, garantissant ainsi que la configuration appliquée au nœud provient d’une source autorisée.

> [!NOTE]
> Vous pouvez importer des configurations signées dans votre compte Azure Automation, mais Azure Automation ne prend pas en charge la compilation de configurations signées.

> [!NOTE]
> Un fichier de configuration de nœuds ne doit pas être supérieur à 1 Mo pour pouvoir être importé dans Azure Automation.

Pour savoir comment signer des configurations de nœuds, consultez le site https://msdn.microsoft.com/en-us/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importation d’une configuration de nœuds dans le portail Azure

1. Dans votre compte Automation, cliquez sur **Configurations de nœuds DSC** sous **Gestion de la configuration**.

    ![Configurations de nœuds DSC](./media/automation-dsc-compile/node-config.png)
2. Dans le panneau **Configurations de nœuds DSC**, cliquez sur **Ajouter une configuration de nœuds**.
3. Dans le panneau **Importer**, cliquez sur l’icône de dossier en regard de la zone de texte **Fichier de configuration de nœuds** pour rechercher un fichier de configuration de nœuds (MOF) sur votre ordinateur local.

    ![Rechercher un fichier local](./media/automation-dsc-compile/import-browse.png)
4. Entrez un nom dans la zone de texte **Nom de la configuration**. Ce nom doit correspondre au nom de la configuration à partir de laquelle la configuration de nœuds a été compilée.
5. Cliquez sur **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importation d’une configuration de nœuds avec PowerShell

Vous pouvez utiliser l’applet de commande [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) pour importer une configuration de nœuds dans votre compte Automation.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



