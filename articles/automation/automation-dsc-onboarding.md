---
title: Gestion de machines avec Azure Automation DSC
description: Comment configurer des machines pour les gérer avec Azure Automation DSC
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: 512146c5d1aa8be06f7209418711f5a8bbd974a5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Gestion de machines avec Azure Automation DSC

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Pourquoi gérer des machines avec Azure Automation DSC ?

Tout comme le service [Desired State Configuration de PowerShell](https://technet.microsoft.com/library/dn249912.aspx), le Desired State Configuration d’Azure Automation est un service de gestion de configuration simple et puissant pour les nœuds DSC (machines physiques et virtuelles) dans n’importe quel centre de données sur le cloud ou sur site. Il permet de faire évoluer des milliers d’ordinateurs rapidement et facilement à partir d’un emplacement central et sécurisé. Vous pouvez facilement intégrer des machines, leur affecter des configurations déclaratives et afficher des rapports montrant la conformité de chaque machine à l’état souhaité que vous avez spécifié. La couche de gestion Azure Automation DSC est au DSC ce que la couche de gestion Azure Automation est aux scripts PowerShell. En d'autres termes, de la même manière que Azure Automation vous permet de gérer des scripts PowerShell, il vous aide également à gérer des configurations DSC. Pour en savoir plus sur les avantages de l’utilisation d’Azure Automation DSC, consultez [Vue d’ensemble d’Azure Automation DSC](automation-dsc-overview.md).

Azure Automation DSC peut servir à gérer une grande diversité de machines :

* Machines virtuelles Azure (classiques)
* Machines virtuelles Azure
* Machines virtuelles Amazon Web Services (AWS)
* Machines physiques/virtuelles Windows locales ou dans un cloud autre qu’Azure/AWS
* Machines physiques/virtuelles Linux sur site, dans Azure, ou dans un cloud autre qu’Azure

En outre, si vous n’êtes pas prêt à gérer la configuration de l’ordinateur dans le cloud, Azure Automation DSC peut également servir de point de terminaison dédié uniquement à la génération de rapports. Vous pouvez ainsi définir la configuration (push) de votre choix via une instance DSC en local et afficher des détails de rapports sur la conformité du nœud à l’état souhaité dans Azure Automation.

> [!NOTE]
> La gestion des machines virtuelles Azure avec DSC est fournie sans frais supplémentaires si l’extension DSC de la machine virtuelle installée est ultérieure à la version 2.70. Pour plus d’informations, reportez-vous à la [**page relative à la tarification d’Automation**](https://azure.microsoft.com/en-us/pricing/details/automation/).


Les sections suivantes décrivent la manière dont vous pouvez intégrer chaque type de machine à Azure Automation DSC.

## <a name="azure-virtual-machines-classic"></a>Machines virtuelles Azure (classiques)

Avec Azure Automation DSC, vous pouvez facilement intégrer des machines virtuelles Azure (classiques) pour une gestion de configuration via le portail Azure ou via PowerShell. En arrière-plan, et sans qu’aucun administrateur n’ait à contrôler la machine virtuelle à distance, l’extension Azure VM Desired State Configuration enregistre la machine virtuelle avec Azure Automation DSC. Étant donné que cette extension s’exécute de façon asynchrone, la section [**Résolution des problèmes liés à l’intégration de machines virtuelles Azure**](#troubleshooting-azure-virtual-machine-onboarding) suivante décrit la procédure à suivre pour contrôler sa progression ou résoudre les problèmes.

### <a name="azure-portal"></a>Portail Azure

Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Parcourir** -> **Machines virtuelles (classiques)**. Sélectionnez la machine virtuelle Windows que vous souhaitez intégrer. Dans le panneau du tableau de bord de la machine virtuelle, cliquez sur **Tous les paramètres** -> **Extensions** -> **Ajouter** -> **Azure Automation DSC** -> **Créer**. Entrez les [valeurs du gestionnaire de configuration locale de PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) requises, la clé et l’URL d’inscription de votre compte Automation, et, éventuellement, une configuration de nœud à attribuer à la machine virtuelle.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

Pour trouver l’URL d’inscription et la clé du compte Automation à intégrer à la machine, consultez la section [**Inscription sécurisée**](#secure-registration) suivante :

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Add-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

## <a name="azure-virtual-machines"></a>Machines virtuelles Azure

Azure Automation DSC vous permet d’intégrer facilement des machines virtuelles Azure pour une gestion de configuration via le portail Azure, les modèles Azure Resource Manager ou PowerShell. En arrière-plan, et sans qu’aucun administrateur n’ait à contrôler la machine virtuelle à distance, l’extension Azure VM Desired State Configuration enregistre la machine virtuelle avec Azure Automation DSC. Étant donné que cette extension s’exécute de façon asynchrone, la section [**Résolution des problèmes liés à l’intégration de machines virtuelles Azure**](#troubleshooting-azure-virtual-machine-onboarding) suivante décrit la procédure à suivre pour contrôler sa progression ou résoudre les problèmes.

### <a name="azure-portal"></a>Portail Azure

Dans le [portail Azure](https://portal.azure.com/), accédez au compte Azure Automation où vous souhaitez intégrer des machines virtuelles. Dans le tableau de bord du compte Automation, cliquez sur **Nœuds DSC** -> **+ Ajouter une machine virtuelle Azure**.

Sélectionnez une machine virtuelle Azure à intégrer.

Si l’extension d’état PowerShell souhaitée n’est pas installée sur la machine virtuelle et que l’état d’alimentation est en cours d’exécution, cliquez sur **Se connecter**.

Sous **Inscription**, entrez les [valeurs du gestionnaire de configuration locale de PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) requises pour votre cas d’utilisation et, éventuellement, une configuration de nœud à attribuer à la machine virtuelle.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Les machines virtuelles Azure peuvent être déployées et intégrées sur Azure Automation DSC via des modèles Azure Resource Manager. Pour un exemple de modèle intégrant une machine virtuelle existante à Azure Automation DSC, consultez [Configurer une machine virtuelle par le biais de l’extension DSC et d’Azure Automation DSC](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) . Pour trouver la clé d’inscription et l’URL d’inscription utilisées comme entrées dans ce modèle, consultez la section [**Inscription sécurisée**](#secure-registration) suivante.

### <a name="powershell"></a>PowerShell

Vous pouvez utiliser l’applet de commande [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) pour intégrer des machines virtuelles au portail Azure par le biais de PowerShell.

## <a name="amazon-web-services-aws-virtual-machines"></a>Machines virtuelles Amazon Web Services (AWS)

Vous pouvez facilement intégrer Amazon Web Services pour la gestion de configuration par Azure Automation DSC avec la boîte à outils AWS DSC. Pour obtenir plus d’informations sur cette boîte à outils, cliquez [ici](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/).

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>Machines physiques/virtuelles Windows locales ou dans un cloud autre qu’Azure/AWS

Les ordinateurs Windows en local et les ordinateurs Windows dans des clouds autres qu’Azure (comme Amazon Web Services) peuvent également être intégrés sur Azure Automation DSC à condition qu’ils disposent d’accès sortant à Internet. Leur intégration s’effectue très simplement, en quelques étapes :

1. Assurez-vous que la dernière version de [WMF 5](http://aka.ms/wmf5latest) est installée sur les ordinateurs que vous souhaitez intégrer à Azure Automation DSC.
2. Suivez les instructions de la section [**Génération de métaconfigurations DSC**](#generating-dsc-metaconfigurations) suivante pour générer un dossier contenant les métaconfigurations DSC nécessaires.
3. Appliquez à distance la métaconfiguration DSC PowerShell aux machines que vous voulez intégrer. **La machine à partir de laquelle cette commande est exécutée doit disposer de la dernière version de [WMF 5](http://aka.ms/wmf5latest)**:

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. Si vous ne pouvez pas appliquer les métaconfigurations DSC PowerShell à distance, copiez le dossier des métaconfigurations de l’étape 2 sur chaque machine à intégrer. Appelez ensuite **Set-DscLocalConfigurationManager** localement sur chaque machine à intégrer.
5. À l’aide du portail Azure ou des applets de commande, vérifiez que les machines à intégrer s’affichent bien en tant que nœuds DSC enregistrés dans votre compte Azure Automation.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>Machines physiques/virtuelles Linux sur site, dans Azure, ou dans un cloud autre qu’Azure

Les ordinateurs Linux en local, les ordinateurs dans Azure et les ordinateurs Linux dans des clouds autres qu’Azure peuvent également être intégrés à Azure Automation DSC à condition qu’ils disposent d’un accès sortant à Internet. Leur intégration s’effectue très simplement, en quelques étapes :

1. Assurez-vous que la dernière version du service [Desired State Configuration de PowerShel pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) est installée sur les ordinateurs que vous souhaitez intégrer à Azure Automation DSC.
2. Si les [valeurs par défaut du gestionnaire de configuration locale DSC PowerShell](https://msdn.microsoft.com/powershell/dsc/metaconfig4) correspondent à votre cas d’utilisation et que vous voulez intégrer des machines de sorte qu’elles procèdent **à la fois** à une extraction auprès d’Azure Automation DSC et qu’elles lui adressent des rapports :

   + Sur chaque ordinateur Linux que vous souhaitez intégrer sur Azure Automation DSC, utilisez Register.py pour effectuer l’intégration en utilisant les valeurs par défaut du gestionnaire de configuration locale DSC PowerShell :

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Pour trouver la clé d’inscription et l’URL d’inscription de votre compte Automation, consultez la section [**Inscription sécurisée**](#secure-registration) suivante.

     Si les valeurs par défaut du gestionnaire de configuration locale DSC PowerShell **ne** correspondent **pas** à votre cas d’utilisation ou si vous voulez intégrer des machines de sorte qu’elles adressent seulement des rapports à Azure Automation DSC, sans en extraire la configuration ou des modules PowerShell, suivez les étapes 3 à 6. Sinon, passez directement à l’étape 6.

3. Suivez les instructions de la section [**Génération de métaconfigurations DSC**](#generating-dsc-metaconfigurations) suivante pour générer un dossier contenant les métaconfigurations DSC nécessaires.
4. Appliquez à distance la métaconfiguration PowerShell DSC pour les machines que vous souhaitez intégrer :

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

La machine à partir de laquelle cette commande est exécutée doit disposer de la dernière version de [WMF 5](http://aka.ms/wmf5latest) .

1. Si vous ne pouvez pas appliquer à distance les métaconfigurations PowerShell DSC, pour chaque ordinateur Linux à intégrer, copiez la métaconfiguration correspondant à cet ordinateur à partir du dossier de l’étape 5 sur l’ordinateur Linux. Appelez ensuite `SetDscLocalConfigurationManager.py` localement sur chaque ordinateur Linux que vous souhaitez intégrer à Azure Automation DSC :

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. À l’aide du portail Azure ou des applets de commande, vérifiez que les machines à intégrer s’affichent bien en tant que nœuds DSC enregistrés dans votre compte Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Génération de métaconfigurations DSC

Pour intégrer de manière générique n’importe quelle machine à Azure Automation DSC, une [métaconfiguration DSC](https://msdn.microsoft.com/en-us/powershell/dsc/metaconfig) peut être générée qui, une fois appliquée, demande à l’agent DSC de la machine d’effectuer une extraction auprès d’Azure Automation DSC et/ou de lui adresser un rapport. Les métaconfigurations DSC pour Azure Automation DSC peuvent être générées à l’aide d’une configuration PowerShell DSC ou des applets de commande PowerShell Azure Automation.

> [!NOTE]
> Les métaconfigurations DSC contiennent les clés secrètes nécessaires à l’intégration d’une machine à un compte Automation à des fins de gestion. Veillez à protéger convenablement les métaconfigurations DSC que vous créez ou supprimez-les après utilisation.

### <a name="using-a-dsc-configuration"></a>Utilisation d’une configuration DSC

1. Ouvrez PowerShell ISE en tant qu’administrateur sur une machine de votre environnement local. Cette machine doit disposer de la dernière version de [WMF 5](http://aka.ms/wmf5latest) .
2. Copiez le script suivant localement. Ce script contient une configuration DSC PowerShell pour créer des métaconfigurations, ainsi qu’une commande pour lancer la création de métaconfigurations.

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. Renseignez la clé d’inscription et l’URL de votre compte Automation, ainsi que les noms des machines à intégrer. Tous les autres paramètres sont facultatifs. Pour trouver la clé d’inscription et l’URL d’inscription de votre compte Automation, consultez la section [**Inscription sécurisée**](#secure-registration) suivante.
4. Si vous voulez que les machines adressent les informations d’état DSC à Azure Automation DSC sans toutefois extraire la configuration ou des modules PowerShell, affectez au paramètre **ReportOnly** la valeur true.
5. Exécutez le script. Vous devez à présent avoir un dossier appelé **DscMetaConfigs** dans votre répertoire de travail contenant les métaconfigurations DSC PowerShell pour les machines à intégrer (en tant qu’administrateur) :

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Utilisation des applets de commande Azure Automation

Si les valeurs par défaut du gestionnaire de configuration locale DSC PowerShell correspondent à votre cas d’utilisation et que vous voulez intégrer des machines de sorte qu’elles procèdent à la fois à une extraction auprès d’Azure Automation DSC et qu’elles lui adressent des rapports, les applets de commande Azure Automation constituent une méthode qui simplifie la génération des métaconfigurations nécessaires :

1. Ouvrez la console PowerShell ou PowerShell ISE en tant qu’administrateur sur un ordinateur de votre environnement local.
2. Connectez-vous à Azure Resource Manager en utilisant **Add-AzureRmAccount**
3. Téléchargez les métaconfigurations DSC PowerShell pour les machines à intégrer du compte Automation vers l’emplacement où vous voulez intégrer des nœuds :

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. Vous devez à présent avoir un dossier appelé ***DscMetaConfigs***contenant les métaconfigurations DSC PowerShell pour les machines à intégrer (en tant qu’administrateur) :
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>Enregistrement sécurisé

Les machines peuvent être intégrées en toute sécurité à un compte Azure Automation via le protocole d’inscription WMF 5 DSC, qui permet à un nœud DSC de s’authentifier sur un serveur Pull ou Reporting PowerShell DSC (y compris Azure Automation DSC). Le nœud s’enregistre sur le serveur au niveau d’une **URL d’inscription** et s’authentifie à l’aide d’une **clé d’inscription**. Pendant l’enregistrement, le nœud DSC et le serveur Pull / Reporting DSC négocient un certificat unique pour ce nœud qui devra être utilisé pour l’authentification au serveur après l’enregistrement. Ce processus empêche les nœuds intégrés d’emprunter l’identité d’un autre nœud, par exemple si un nœud est compromis et agit à des fins malveillantes. Après l’enregistrement, la clé d’enregistrement n’est plus utilisée pour l’authentification et est supprimée du nœud.

Pour obtenir les informations requises pour le protocole d’inscription DSC, accédez à **Clés** sous **Paramètres du compte** dans le portail Azure. Ouvrez ce panneau en cliquant sur l’icône de clé dans le panneau **Bases** du compte Automation.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* L’URL d’enregistrement correspond à la valeur du champ URL dans le panneau Gérer les clés.
* La clé d’enregistrement correspond à la clé d’accès primaire ou à la clé d’accès secondaire dans le panneau Gérer les clés. Vous pouvez utiliser l’une de ces deux clés.

Pour renforcer la sécurité, les clés d’accès primaire et secondaire d’un compte Automation peuvent être régénérées à tout moment (à partir du panneau **Gérer les clés** ) pour éviter que des nœuds s’enregistrent ultérieurement à l’aide de clés déjà utilisées.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Résolution des problèmes liés à l’intégration de machines virtuelles Azure

Azure Automation DSC vous permet d’intégrer facilement des machines virtuelles Microsoft Azure à des fins de gestion de la configuration. En arrière-plan, l’extension Azure VM Desired State Configuration est utilisée pour enregistrer la machine virtuelle auprès d’Azure Automation DSC. Étant donné que cette extension s’exécute de façon asynchrone, il peut être très important d’en suivre la progression et de résoudre ses éventuels problèmes d’exécution.

> [!NOTE]
> Quelle que soit la méthode choisie pour intégrer une machine virtuelle Microsoft Azure sur Azure Automation DSC, l’enregistrement du nœud dans Azure Automation peut prendre jusqu’à une heure si l’extension Azure VM Desired State Configuration est utilisée. Cela est dû à l'installation de Windows Management Framework 5.0 sur la machine virtuelle par l'extension Azure VM DSC, nécessaire à l’intégration de la machine virtuelle dans Azure Automation DSC.

Pour résoudre les problèmes ou afficher l’état de l’extension Azure VM Desired State Configuration, rendez-vous dans le portail Azure, accédez à la machine virtuelle en cours d’intégration, puis cliquez sur **Extensions** sous **Paramètres**. Cliquez ensuite sur **DSC** ou **DSCForLinux** selon votre système d’exploitation. Pour plus de détails, vous pouvez cliquer sur **Afficher l’état détaillé**.

## <a name="certificate-expiration-and-reregistration"></a>Expiration du certificat et nouvel enregistrement

Après avoir inscrit un ordinateur en tant que nœud DSC dans Azure Automation DSC, il se peut que vous ayez besoin de revenir en arrière pour de multiples raisons :

* Une fois inscrit, chaque nœud négocie automatiquement un certificat unique pour l'authentification qui expire après un an. À ce stade, le protocole d’inscription PowerShell DSC ne peut pas renouveler automatiquement les certificats lorsqu’ils sont sur le point d’expirer. Vous devez donc renouveler l’inscription des nœuds après un an. Avant la réinscription, assurez-vous que chaque nœud exécute Windows Management Framework 5.0 RTM. Si le certificat d’authentification d’un nœud expire et que ce dernier n’est pas réinscrit, le nœud ne peut pas communiquer avec Azure Automation et indique « Aucune réponse ». La réinscription effectuée dans un délai de 90 jours ou moins à partir de l'heure d'expiration du certificat, ou à tout moment après le délai d'expiration du certificat, entraîne la génération et l'utilisation d'un nouveau certificat.
* Pour modifier des [valeurs du gestionnaire de configuration local PowerShell DSC](https://msdn.microsoft.com/powershell/dsc/metaconfig4) qui ont été définies lors de l’inscription initiale du nœud, telles que ConfigurationMode. Actuellement, ces valeurs de l’agent DSC peuvent être modifiées uniquement via une désinscription. La seule exception concerne la configuration du nœud assignée au nœud, qui peut être modifiée directement dans Azure Automation DSC.

L’inscription peut être renouvelée selon la procédure initiale, en utilisant l’une des méthodes d’intégration décrites dans ce document. Il est inutile d’annuler l’inscription d’un nœud dans Azure Automation DSC avant de le réinscrire.

## <a name="related-articles"></a>Articles connexes

* [Vue d’ensemble d’Azure Automation DSC](automation-dsc-overview.md)
* [Applets de commande Azure Automation DSC](/powershell/module/azurerm.automation/#automation)
* [Tarification d’Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/)
