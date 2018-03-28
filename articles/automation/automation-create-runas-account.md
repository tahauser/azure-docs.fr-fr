---
title: Créer des comptes d’identification Azure Automation
description: Cet article indique comment mettre à jour votre compte Automation et créer des comptes d’identification avec PowerShell, ou à partir du portail.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: e7a339ae443bc61e1d2364afd8400fc436593b9f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>Mettre à jour l’authentification de votre compte Automation avec des comptes d’identification 
Vous pouvez mettre à jour votre compte Automation existant à partir du portail Azure ou utiliser PowerShell si :

* Vous créez un compte Automation sans compte d’identification.
* Vous possédez déjà un compte Automation pour gérer les ressources Resource Manager et vous voulez le mettre à jour pour inclure le compte d’identification pour l’authentification du Runbook.
* Vous possédez déjà un compte Automation pour gérer les ressources classiques et souhaitez le mettre à jour pour utiliser le compte d’identification Classic au lieu de créer un compte et d’y migrer vos Runbooks et ressources.   

Le processus crée les éléments ci-après dans votre compte Automation.

**Pour les comptes d’identification :**

* Crée une application Azure AD avec un certificat auto-signé, crée un compte de principal de service pour cette application dans Azure AD et affecte le rôle Collaborateur pour le compte dans votre abonnement actuel. Vous pouvez remplacer ce paramètre par un rôle de propriétaire ou par tout autre rôle. Pour plus d’informations, voir [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md).
* Crée une ressource de certificat Automation nommée *AzureRunAsCertificate* dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par l’application Azure AD.
* Crée une ressource de connexion Automation nommée *AzureRunAsConnection* dans le compte Automation spécifié. La ressource de connexion conserve les ID applicationId, tenantId et subscriptionId, et l’empreinte de certificat.

**Pour les comptes d’identification Classic :**

* Crée une ressource de certificat Automation nommée *AzureClassicRunAsCertificate*dans le compte Automation spécifié. La ressource de certificat conserve la clé privée du certificat utilisée par le certificat de gestion.
* Crée une ressource de connexion Automation nommée *AzureClassicRunAsConnection* dans le compte Automation spécifié. La ressource de connexion conserve le nom de l’abonnement, l’ID subscriptionId et le nom de ressource de certificat.

## <a name="prerequisites"></a>Prérequis

Si vous choisissez [d’utiliser PowerShell pour créer les comptes d’identification](#create-run-as-account-using-powershell), ce processus requiert :

* Windows 10 et sur Windows Server 2016 avec les modules Azure Resource Manager 3.4.1 et versions ultérieures. Le script PowerShell ne prend pas en charge les versions antérieures de Windows.
* Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations sur la version PowerShell 1.0, voir [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un compte Automation, référencé en tant que valeur des paramètres *–AutomationAccountName* et *-ApplicationDisplayName*.

Pour obtenir les valeurs des paramètres *SubscriptionID*, *ResourceGroup* et *AutomationAccountName*, qui sont des paramètres obligatoires pour le script, procédez comme suit :

1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Automation**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Comptes Automation**.
2. Dans la page de compte Automation, sélectionnez votre compte Automation, puis sous **Paramètres de compte**, sélectionnez **Propriétés**.  
3. Notez les valeurs dans la page **Propriétés**.<br><br> ![Panneau Propriétés du compte Automation](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>Autorisations requises pour mettre à jour votre compte Automation
Pour mettre à jour un compte Automation conformément à cette rubrique, vous devez disposer des privilèges spécifiques suivants et des autorisations requises.   
 
* Votre compte d’utilisateur AD doit être ajouté à un rôle disposant d’autorisations équivalentes à celles du rôle Contributeur pour les ressources Microsoft.Automation, comme indiqué dans l’article [Contrôle d’accès en fonction du rôle dans Azure Automation](automation-role-based-access-control.md#contributor).  
* Les utilisateurs non administrateurs dans votre locataire Azure AD peuvent [inscrire des applications AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions) si l’option **Les utilisateurs peuvent inscrire des applications** du locataire Azure AD au sein de la page **Paramètres utilisateur** est définie sur **Oui**. Si le paramètre Inscriptions d’applications est défini sur **Non**, l’utilisateur qui effectue cette action doit être un administrateur général dans Azure AD.

Si vous n’êtes pas membre de l’instance Active Directory de l’abonnement avant d’être ajouté au rôle Administrateur général/Coadministrateur de l’abonnement, vous êtes ajouté à Active Directory en tant qu’invité. Dans ce cas, vous recevez le message d’avertissement « Vous n’avez pas les autorisations pour créer… » dans le panneau **Ajouter un compte Automation**. Les utilisateurs ayant préalablement reçu le rôle administrateur général/coadministrateur peuvent être supprimés de l’instance Active Directory de l’abonnement, puis rajoutés pour devenir des utilisateurs complets dans Active Directory. Pour vérifier si tel est le cas, dans le volet **Azure Active Directory** du portail Azure, sélectionnez **Utilisateurs et groupes** et **Tous les utilisateurs**, choisissez l’utilisateur concerné, puis sélectionnez **Profil**. La valeur de l’attribut **Type d’utilisateur** sous le profil de l’utilisateur ne doit pas être **Invité**.

## <a name="create-run-as-account-from-the-portal"></a>Créer un compte d’identification à partir du portail
Dans cette section, exécutez la procédure ci-après pour mettre à jour votre compte Azure Automation dans le Portail Azure. Vous créez individuellement les comptes d’identification et les comptes d’identification Classic. Si vous n’avez pas besoin de gérer des ressources classiques, vous pouvez simplement créer le compte d’identification Azure.  

1. Connectez-vous au portail Azure avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
2. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Automation**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Comptes Automation**.
3. Sur la page **Comptes Automation**, sélectionnez votre compte Automation depuis la liste des comptes Automation.
4. Dans le volet à gauche, sélectionnez **Comptes d’identification** sous la section **Paramètres de compte**.  
5. Selon le compte dont vous avez besoin, sélectionnez **Compte d’identification Azure** ou **Compte d’identification Azure Classic**. Une fois que vous avez sélectionné une option, le volet **Ajouter un compte d’identification Azure** ou **Ajouter un compte d’identification Azure Classic** s’affiche. Après avoir consulté les informations correspondantes, cliquez sur **Créer** pour procéder à la création du compte d’identification.  
6. Pour suivre la progression de la création du compte d’identification, accédez à l’onglet **Notifications** du menu. Une bannière est également affichée indiquant que le compte est en cours de création. L’exécution de ce processus peut prendre plusieurs minutes.  

## <a name="create-run-as-account-using-powershell-script"></a>Créer un compte d’identification à l’aide d’un script PowerShell
Ce script PowerShell prend en charge les configurations suivantes :

* Créez un compte d’identification à l’aide d’un certificat auto-signé.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé.
* Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat émis par votre AC d’entreprise.
* Créez un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government.

>[!NOTE]
> Si vous sélectionnez l’option permettant de créer un compte d’identification Classic, après l’exécution du script, vous devez charger le certificat public (extension de nom de fichier .cer) dans le magasin de gestion de l’abonnement dans lequel le compte Automation a été créé.
> 

1. Enregistrez le script suivant sur votre ordinateur. Dans cet exemple, enregistrez-le sous le nom de fichier *New-RunAsAccount.ps1*.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,
        
        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,
        
        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,
        
        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,
        
        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,
        
        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,
        
        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",
        
        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )
        
    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256
        
        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }
        
    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid
        
        $startDate = Get-Date
        $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
        
        #Create an Azure AD application, AD App Credential, AD ServicePrincipal
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
        
        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }
        
    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }
        
    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }
        
    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources
        
    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }
        
    Login-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        
    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"
        
    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
        
    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName
        
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true
        
    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}
        
    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues
        
    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"
        
        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }
        
        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false
        
        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}
        
        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues
        
        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

2. Sur votre ordinateur, démarrez **Windows PowerShell** dans l’écran **Démarrer** avec des droits de l’utilisateur élevés.
3. À partir de l’interface de ligne de commande avec élévation de privilèges, accédez au dossier contenant le script que vous avez créé à l’étape 1.  
4. Exécutez le script en utilisant les valeurs de paramètre pour la configuration dont vous avez besoin.

    **Créer un compte d’identification à l’aide d’un certificat auto-signé**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat d’entreprise**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Créer un compte d’identification standard et un compte d’identification Classic à l’aide d’un certificat auto-signé dans le cloud Azure Government**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Connectez-vous avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.
    >
    >

Une fois le script exécuté, notez les points suivants :
* Si vous avez créé un compte d’identification Classic avec un certificat public auto-signé (fichier .cer), le script le crée et l’enregistre dans le dossier de fichiers temporaires sur votre ordinateur, sous le profil d’utilisateur *%USERPROFILE%\AppData\Local\Temp* utilisé pour exécuter la session PowerShell.
* Si vous avez créé un compte d’identification Classic avec un certificat public d’entreprise (fichier .cer), utilisez ce certificat. Suivez les instructions pour [charger un certificat d’API de gestion vers le portail Azure](../azure-api-management-certs.md), puis validez la configuration des informations d’identification avec les ressources de déploiement classique à l’aide de [l’exemple de code pour l’authentification avec les ressources de déploiement Azure Classic](automation-verify-runas-authentication.md#classic-run-as-authentication). 
* Si vous n’avez *pas* créé de compte d’identification Classic, authentifiez-vous avec des ressources Resource Manager et validez la configuration des informations d’identification à l’aide [l’exemple de code pour l’authentification avec les ressources de gestion des services](automation-verify-runas-authentication.md#automation-run-as-authentication).

## <a name="limiting-run-as-account-permissions"></a>Limiter les autorisations de compte d’identification

Pour contrôler le ciblage de l’automatisation sur des ressources dans Azure Automation, des droits de contributeur sont accordés au compte d’identification dans l’abonnement. Si vous devez limiter ce que peut faire le principal de service RunAs, vous pouvez supprimer le compte du rôle de contributeur à l’abonnement et l’ajouter comme contributeur aux groupes de ressources que vous voulez spécifier.

Dans le portail Azure, sélectionnez **Abonnements**, puis choisissez l’abonnement de votre compte Automation. Sélectionnez **Contrôle d’accès (IAM)** et recherchez le principal de service pour votre compte Automation (il ressemble à \<AutomationAccountName\>_identificateur unique). Sélectionnez le compte, puis cliquez sur **Supprimer** pour le supprimer de l’abonnement.

![Contributeurs d’abonnement](media/automation-create-runas-account/automation-account-remove-subscription.png)

Pour ajouter le principal de service à un groupe de ressources, sélectionnez le groupe de ressources dans le portail Azure, puis sélectionnez **Contrôle d’accès (IAM)**. Sélectionnez **Ajouter**. Cette opération ouvre la page **Ajouter des autorisations**. Pour **Rôle**, sélectionnez **Contributeur**. Dans la zone de texte **Sélectionner**, tapez le nom du principal de service pour votre compte d’identification, puis sélectionnez-le dans la liste. Cliquez sur **Enregistrer** pour enregistrer les modifications. Effectuez cette opération pour les groupes de ressources auxquels vous voulez accorder l’accès à votre principal de service d’identification Azure Automation.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les principaux de service, consultez [Objets principal du service et application](../active-directory/active-directory-application-objects.md).
* Pour plus d’informations sur les certificats et les services Azure, consultez [Vue d’ensemble des certificats pour Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
