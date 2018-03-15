---
title: "Inscription Azure pour systèmes intégrés Azure Stack | Microsoft Docs"
description: "Décrit le processus d’inscription Azure pour les déploiements à plusieurs nœuds d’Azure Stack connectés à Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d5b77bb43c48bd286708ca96699b20be0f761baa
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure
Vous pouvez inscrire Azure Stack auprès d’Azure pour télécharger des éléments de la Place de Marché à partir d’Azure et configurer la génération de rapports de données commerciales envoyés à Microsoft. Après avoir inscrit Azure Stack, l’utilisation est signalée à Azure Commerce. Vous pouvez la consulter sous l’abonnement utilisé pour l’inscription.

> [!IMPORTANT]
> L’inscription est obligatoire si vous choisissez le modèle de facturation de paiement à l’utilisation. Dans le cas contraire, vous enfreindrez les conditions des licences du déploiement Azure Stack car l’utilisation ne sera pas rapportée.

## <a name="before-you-register-azure-stack-with-azure"></a>Avant d’inscrire Azure Stack auprès d’Azure
Avant d’inscrire Azure Stack auprès d’Azure, vous devez disposer des éléments suivants :

- L’ID d’abonnement d’un abonnement Azure. Pour obtenir l’ID, connectez-vous à Azure, cliquez sur **Plus de services** > **Abonnements**, cliquez sur l’abonnement que vous voulez utiliser. Sous **Éléments principaux** vous trouverez alors l’ID d’abonnement. 

  > [!NOTE]
  > Les abonnements au cloud du gouvernement de Chine, d’Allemagne et des États-Unis ne sont pas pris en charge pour le moment. 

- Le nom d’utilisateur et le mot de passe d’un compte qui est un propriétaire de l’abonnement (les comptes MSA/2FA sont pris en charge)
- *Non obligatoire depuis la version de mise à jour Azure Stack 1712 (180106.1)* : Azure AD pour l’abonnement Azure. Pour trouver cet annuaire dans Azure, pointez sur votre avatar situé en haut à droite dans le portail Azure. 
- Le fournisseur de ressources Azure Stack inscrit (pour plus d’informations, consultez la section Inscrire le fournisseur de ressources Azure Stack ci-dessous)

Si vous n’avez pas d’abonnement Azure répondant à ces exigences, vous pouvez [créer un compte Azure gratuit ici](https://azure.microsoft.com/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.

### <a name="bkmk_powershell"></a>Installer PowerShell pour Azure Stack
Vous devez utiliser la dernière version de PowerShell pour Azure Stack pour inscrire le système auprès d’Azure.

S’il ne l’est pas déjà, [installez PowerShell pour Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Télécharger les outils Azure Stack
Le référentiel GitHub d’outils Azure Stack contient des modules PowerShell qui prennent en charge les fonctionnalités Azure Stack, notamment des fonctionnalités d’inscription. Pendant le processus d’inscription, vous devez importer et utiliser le module PowerShell RegisterWithAzure.psm1, qui se trouve dans le référentiel d’outils Azure Stack, pour inscrire votre instance d’Azure Stack auprès d’Azure. 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>Inscrire Azure Stack dans des environnements connectés
Les environnements connectés peuvent accéder à Internet et à Azure. Pour ces environnements, vous devez inscrire le fournisseur de ressources Azure Stack auprès d’Azure, puis configurer votre modèle de facturation.

### <a name="register-the-azure-stack-resource-provider"></a>Inscrire le fournisseur de ressources Azure Stack
Pour inscrire le fournisseur de ressources Azure Stack auprès d’Azure, démarrez PowerShell ISE en tant qu’administrateur et utilisez les commandes PowerShell suivantes. Ces commandes :
- Vous invitent à ouvrir une session en tant que propriétaire de l’abonnement Azure à utiliser et à définir le paramètre `EnvironmentName` sur **AzureCloud**.
- Inscrivez le fournisseur de ressources Azure **Microsoft.AzureStack**.

PowerShell pour :

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Inscrire Azure Stack auprès d’Azure à l’aide du modèle de facturation de paiement à l’utilisation
Utilisez ces étapes pour inscrire Azure Stack auprès d’Azure à l’aide du modèle de facturation de paiement à l’utilisation.

Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** dans le répertoire **AzureStack-Tools-master** créé lorsque vous avez [téléchargé les outils Azure Stack](#bkmk_tools). Importez le module **RegisterWithAzure.psm1** à l’aide de PowerShell : 

PowerShell pour :

```powershell
Import-Module .\RegisterWithAzure.psm1
```
Ensuite, dans la même session PowerShell, exécutez l’applet de commande **Set-AzsRegistration**. Lorsque les informations d’identification vous sont demandées, spécifiez le propriétaire de l’abonnement Azure.  

PowerShell pour :

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Paramètre|Description|
|-----|-----|
|CloudAdminCredential|Objet PowerShell contenant les informations d’identification (nom d’utilisateur et mot de passe) du propriétaire de l’abonnement Azure.|
|PrivilegedEndpoint|Une console PowerShell distante préconfigurée qui vous fournit des fonctionnalités telles que la collecte de journaux et d’autres tâches de post-déploiement. Pour en savoir plus, reportez-vous à l’article relatif à l’[utilisation du point de terminaison privilégié](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint).|
|BillingModel|Le modèle de facturation utilisé par votre abonnement. Les valeurs autorisées pour ce paramètre sont : Capacity, PayAsYouUse et Development.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Inscrire Azure Stack auprès d’Azure à l’aide du modèle de facturation de capacité
Suivez les mêmes instructions que celles utilisées pour l’inscription à l’aide du modèle de facturation de paiement à l’utilisation, mais ajoutez le numéro de contrat sous lequel la capacité a été achetée et définissez le paramètre `BillingModel` sur **Capacité**. Tous les autres paramètres restent inchangés.

PowerShell pour :
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Inscrire Azure Stack dans des environnements déconnectés 
*Les informations contenues dans cette section s’appliquent à compter de la version de mise à jour 1712 d’Azure Stack (180106.1) et ne sont pas prises en charge par les versions antérieures.*

Si vous inscrivez Azure Stack dans un environnement déconnecté (sans connectivité Internet), vous devez obtenir un jeton d’inscription à partir de l’environnement d’Azure Stack, puis l’utiliser sur un ordinateur qui peut se connecter à Azure et sur lequel [PowerShell pour Azure Stack est installé](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtenir un jeton d’inscription à partir de l’environnement d’Azure Stack
  1. Pour obtenir le jeton d’inscription, exécutez les commandes PowerShell suivantes :  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Le jeton d’inscription est enregistré dans le fichier spécifié pour *$env:SystemDrive\RegistrationToken.txt*.

  2. Enregistrez ce jeton d’inscription pour l’utiliser sur la machine connectée à Azure.


### <a name="connect-to-azure-and-register"></a>Se connecter à Azure et s’inscrire
Démarrez PowerShell ISE en tant qu’administrateur et accédez au dossier **Registration** dans le répertoire **AzureStack-Tools-master** créé lorsque vous avez [téléchargé les outils Azure Stack](#bkmk_tools). Importez le module **RegisterWithAzure.psm1** : 

PowerShell pour :
```powershell
Import-Module .\RegisterWithAzure.psm1
```
Ensuite, dans la même session PowerShell, spécifiez le jeton d’inscription pour s’inscrire auprès d’Azure :

```Powershell  
$registrationToken = "<Your Registration Token>"
Register-AzsEnvironment -RegistrationToken $registrationToken  
```
Si vous le souhaitez, vous pouvez utiliser l’applet de commande Get-Content pour pointer vers un fichier contenant votre jeton d’inscription :

 ```Powershell  
 $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
 Register-AzsEnvironment -RegistrationToken $registrationToken  
 ```
> [!NOTE]  
> Enregistrez le nom de ressource d’inscription ou le jeton d’inscription pour une référence ultérieure.

## <a name="verify-azure-stack-registration"></a>Vérifier l’inscription Azure Stack
Utilisez ces étapes pour vérifier qu’Azure Stack a bien été inscrit auprès d’Azure.
1. Connectez-vous au [portail d’administration](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal) Azure Stack : https&#58;//adminportal.*&lt;region>.&lt;fqdn>*.
2. Cliquez sur **Plus de services** > **Gestion de la Place de Marché** > **Ajouter à partir d’Azure**.

Si une liste d’éléments disponibles dans Azure (tels que WordPress) s’affiche, l’activation a réussi.

> [!NOTE]
> Une fois l’inscription terminée, l’avertissement relatif à la non-inscription n’apparaît plus.

## <a name="renew-or-change-registration"></a>Renouveler ou modifier l’inscription
Vous devez mettre à jour ou renouveler votre inscription dans les cas suivants :
- Après le renouvellement de votre abonnement annuel basé sur la capacité.
- Lorsque vous changez de modèle de facturation.
- Lorsque vous mettez à l’échelle des modifications (ajout/suppression de nœuds) pour la facturation basée sur la capacité.

### <a name="change-the-subscription-you-use"></a>Modifier l’abonnement que vous utilisez
Si vous souhaitez modifier l’abonnement que vous utilisez, vous devez d’abord exécuter l’applet de commande **Remove-AzsRegistration**, vous assurer d’être connecté au contexte Azure PowerShell correct, puis exécuter **Set-AzsRegistration** avec les paramètres modifiés :

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Modifier le modèle de facturation ou les fonctionnalités de syndication
Si vous souhaitez modifier le modèle de facturation ou les fonctionnalités de syndication pour votre installation, vous pouvez appeler la fonction d’inscription pour définir les nouvelles valeurs. Vous n’avez pas besoin de supprimer l’inscription actuelle d’abord : 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>Supprimer une ressource inscrite
Si vous souhaitez supprimer une inscription, vous devez utiliser l’applet de commande **UnRegister-AzsEnvironment** et transmettre le nom de ressource d’inscription ou le jeton d’inscription que vous avez utilisé pour **Register-AzsEnvironment**.

Pour supprimer une inscription à l’aide d’un nom de ressource :

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
Pour supprimer une inscription à l’aide d’un jeton d’inscription :

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

## <a name="next-steps"></a>Étapes suivantes

[Intégration à une solution de monitoring externe](azure-stack-integrate-monitor.md)