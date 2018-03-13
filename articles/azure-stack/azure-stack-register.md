---
title: "Inscrire Azure Stack | Microsoft Docs"
description: "Inscrire Azure Stack auprès de votre abonnement Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2018
ms.author: jeffgilb
ms.openlocfilehash: b58b3fc538d2237c12a860d268d550c4223155ba
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Inscrire Azure Stack auprès de votre abonnement Azure

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez inscrire [Azure Stack](azure-stack-poc.md) auprès d’Azure pour télécharger des éléments de la Place de Marché à partir d’Azure et configurer la génération de rapports de données commerciales envoyés à Microsoft.

> [!NOTE]
>L’inscription est recommandée, car elle vous permet de tester des fonctionnalités Azure Stack importantes, telles que la syndication de la Place de Marché et les rapports d’utilisation. Après avoir inscrit Azure Stack, l’utilisation est signalée à Azure Commerce. Vous pouvez la consulter sous l’abonnement utilisé pour l’inscription. Toute utilisation que les utilisateurs du kit de développement Azure Stack signalent ne leur sera pas facturée.


## <a name="get-azure-subscription"></a>Prendre un abonnement Azure

Avant d’inscrire Azure Stack auprès d’Azure, vous devez disposer des éléments suivants :

- L’ID d’abonnement d’un abonnement Azure. Pour obtenir l’ID, connectez-vous à Azure, cliquez sur **Plus de services** > **Abonnements**, cliquez sur l’abonnement que vous voulez utiliser. Sous **Éléments principaux** vous trouverez alors l’**ID d’abonnement**. Les abonnements au cloud du gouvernement de Chine, d’Allemagne et des États-Unis ne sont pas pris en charge pour le moment.
- Le nom d’utilisateur et le mot de passe d’un compte qui est un propriétaire de l’abonnement (les comptes MSA/2FA sont pris en charge).
- *Non obligatoire depuis la version de mise à jour Azure Stack 1712 (180106.1) :* Azure Active Directory pour l’abonnement Azure. Pour trouver cet annuaire dans Azure, pointez sur votre avatar situé en haut à droite dans le portail Azure.

Si vous n’avez pas d’abonnement Azure répondant à ces exigences, vous pouvez [créer un compte Azure gratuit ici](https://azure.microsoft.com/en-us/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.

## <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure  
> [!NOTE]
> Toutes ces étapes doivent être exécutées à partir d’une machine ayant accès au point de terminaison privilégié. Pour le Kit de développement Azure Stack, il s’agira de l’ordinateur hôte. Si vous utilisez un système intégré, contactez votre opérateur Azure Stack.
>

1. Ouvrez une console PowerShell en tant qu’administrateur et [installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).  

2. Ajoutez le compte Azure que vous utilisez pour inscrire Azure Stack. Pour ajouter le compte, exécutez l’applet de commande `Add-AzureRmAccount` avec le paramètre EnvironmentName défini sur **AzureCloud**. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à 2 facteurs en fonction de la configuration de votre compte.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous souhaitez utiliser :  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Exécutez la commande suivante pour inscrire le fournisseur de ressources Azure Stack dans votre abonnement Azure :

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Supprimez toutes les versions existantes des modules PowerShell qui correspondent à l’inscription et [téléchargez la dernière version de celui-ci depuis GitHub](azure-stack-powershell-download.md).  

6. Dans le répertoire « AzureStack-Tools-master » créé à l’étape précédente, accédez au dossier « Registration » et importez le module « .\RegisterWithAzure.psm1 » :  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. Dans la même session PowerShell, exécutez le script suivant: à l’invite d’informations d’identification, spécifiez `azurestack\cloudadmin` comme utilisateur, le mot de passe est identique à celui utilisé pour l’administrateur local pendant le déploiement.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | Paramètre | Description |  
   |--------|-------------|
   | CloudAdminCredential | Les informations d’identification de domaine de cloud qui sont utilisées pour [accéder au point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Le nom d’utilisateur est au format **\<Azure Stack domain\>\cloudadmin**. Pour le kit de développement, le nom d’utilisateur est défini sur **azurestack\cloudadmin**. Si vous utilisez un système intégré, contactez votre opérateur Azure Stack pour obtenir cette valeur.|  
   | PrivilegedEndpoint | Une console PowerShell distante préconfigurée qui vous fournit des fonctionnalités telles que la collecte de journaux et d’autres tâches de post-déploiement. Pour le kit de développement, le point de terminaison privilégié est hébergé sur la machine virtuelle « AzS-ERCS01 ». Si vous utilisez un système intégré, contactez votre opérateur Azure Stack pour obtenir cette valeur. Pour en savoir plus, reportez-vous à la rubrique relative à l’article [Utilisation du point de terminaison privilégié](azure-stack-privileged-endpoint.md).|  
   | BillingModel | Le modèle de facturation utilisé par votre abonnement. Les valeurs autorisées pour ce paramètre sont **Capacity**, **PayAsYouUse** et **Development**. Pour le kit de développement, cette valeur est définie sur **Development**. Si vous utilisez un système intégré, contactez votre opérateur Azure Stack pour obtenir cette valeur. |

8. Une fois le script terminé, un message « Activation d’Azure Stack (cette étape peut prendre jusqu’à 10 minutes) » s’affiche.




## <a name="verify-the-registration"></a>Vérifier l’inscription  

1. Connectez-vous au portail d’administration (https://adminportal.local.azurestack.external).

2. Cliquez sur **Plus de services** > **Gestion de la Place de Marché** > **Ajouter à partir d’Azure**.

3. Si une liste d’éléments disponibles dans Azure (tels que WordPress) s’affiche, l’activation a réussi.

> [!NOTE]
> Une fois l’inscription terminée, l’avertissement relatif à la non-inscription n’apparaît plus.


## <a name="modify-the-registration"></a>Modifier l’inscription

### <a name="change-the-subscription-you-use"></a>Modifier l’abonnement que vous utilisez
Si vous souhaitez modifier l’abonnement que vous utilisez, vous devez d’abord exécuter Remove-AzsRegistration, assurez-vous d’être connecté au contexte correct de Azure PowerShell, puis exécutez Set-AzsRegistration avec les paramètres modifiés.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>Modifier le modèle de facturation ou les fonctionnalités de syndication
Si vous souhaitez modifier le modèle de facturation ou les fonctionnalités de syndication pour votre installation, vous pouvez appeler la fonction d’inscription pour définir les nouvelles valeurs. Vous n’avez pas besoin de supprimer l’inscription actuelle d’abord.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>Inscription déconnectée
*Les informations contenues dans cette section s’appliquent à compter de la version de mise à jour de Azure Stack 1712 (180106.1) et ne sont pas prises en charge par les versions antérieures.*

Si vous enregistrez Azure Stack dans un environnement déconnecté, vous devez obtenir un jeton d’inscription à partir de l’environnement de Azure Stack, puis l’utiliser sur une machine qui peut se connecter à Azure pour l’inscription.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtenir un jeton d’inscription à partir de l’environnement de Azure Stack
  1. Pour obtenir le jeton d’inscription, exécutez la commande suivante :  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Le jeton d’inscription est enregistré dans le fichier spécifié pour *$env:SystemDrive\RegistrationToken.txt*.

  2. Enregistrez ce jeton d’inscription pour l’utiliser sur une machine connectée par Azure.


### <a name="connect-to-azure-and-register"></a>Se connecter à Azure et s’inscrire
Exécutez les étapes de cette procédure sur une machine pouvant se connecter à Azure.

  1. [Télécharger les derniers modules PowerShell correspondant à l’inscription à partir de GitHub](azure-stack-powershell-download.md).  

  2. Dans le répertoire « AzureStack-Tools-master » créé à l’étape précédente, accédez au dossier « Registration » et importez le module « .\RegisterWithAzure.psm1 » :  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. Copiez [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) dans un dossier, comme *C:\Temp*.

  4. Démarrez PowerShell ISE en tant qu’administrateur et importez le module RegisterWithAzure.  

  5. Vérifiez que vous êtes connecté dans le contexte approprié de Azure PowerShell que vous souhaitez utiliser pour inscrire votre environnement de Azure Stack :  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Spécifiez le jeton d’inscription pour s’inscrire auprès de Azure :

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    Si vous le souhaitez, vous pouvez utiliser l’applet de commande Get-Content pour pointer vers un fichier contenant votre jeton d’inscription :

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> Enregistrez le nom de ressource d’inscription ou le jeton d’inscription pour une référence ultérieure.

### <a name="remove-a-registered-resource"></a>Supprimer une ressource inscrite
Si vous souhaitez supprimer la ressource d’inscription, vous devez utiliser UnRegister-AzsEnvironment et passez le nom de ressource d’inscription ou le jeton d’inscription que vous avez utilisé pour Register-AzsEnvironment.
- **Nom de la ressource d'inscription :**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **Jeton d’inscription :**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>étapes suivantes
[Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)
