---
title: "Inscrire Azure Stack | Microsoft Docs"
description: "Inscrire Azure Stack auprès de votre abonnement Azure."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Inscrire Azure Stack auprès de votre abonnement Azure

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez inscrire [Azure Stack](azure-stack-poc.md) auprès d’Azure pour télécharger des éléments de la Place de Marché à partir d’Azure et configurer la génération de rapports de données commerciales envoyés à Microsoft. 

> [!NOTE]
>L’inscription est recommandée, car elle vous permet de tester des fonctionnalités Azure Stack importantes, telles que la syndication de la Place de Marché et les rapports d’utilisation. Après avoir inscrit Azure Stack, l’utilisation est signalée à Azure Commerce. Vous pouvez la consulter sous l’abonnement utilisé pour l’inscription. Toute utilisation que les utilisateurs du kit de développement Azure Stack signalent ne leur sera pas facturée.
>


## <a name="get-azure-subscription"></a>Prendre un abonnement Azure

Avant d’inscrire Azure Stack auprès d’Azure, vous devez disposer des éléments suivants :

- L’ID d’abonnement d’un abonnement Azure. Pour obtenir l’ID, connectez-vous à Azure, cliquez sur **Plus de services** > **Abonnements**, cliquez sur l’abonnement que vous voulez utiliser. Sous **Éléments principaux** vous trouverez alors l’**ID d’abonnement**. Les abonnements au cloud du gouvernement de Chine, d’Allemagne et des États-Unis ne sont pas pris en charge pour le moment.
- Le nom d’utilisateur et le mot de passe d’un compte qui est un propriétaire de l’abonnement (les comptes MSA/2FA sont pris en charge).
- L’annuaire Azure Active Directory de l’abonnement Azure. Pour trouver cet annuaire dans Azure, pointez sur votre avatar situé en haut à droite dans le portail Azure. 

Si vous n’avez pas d’abonnement Azure répondant à ces exigences, vous pouvez [créer un compte Azure gratuit ici](https://azure.microsoft.com/en-us/free/?b=17.06). L’inscription d’Azure Stack n’entraîne aucun frais sur votre abonnement Azure.



## <a name="register-azure-stack-resource-provider-in-azure"></a>Inscrire un fournisseur de ressources Azure Stack dans Azure
> [!NOTE] 
> Cette étape doit être effectuée une seule fois dans un environnement Azure Stack.
>

1. Démarrez une session PowerShell en tant qu’administrateur.
2. Connectez-vous au compte Azure propriétaire de l’abonnement Azure (vous pouvez utiliser l’applet de commande Login-AzureRmAccount pour vous connecter puis, lors de la connexion, veillez à définir le paramètre -EnvironmentName sur « AzureCloud »).
3. Inscrivez le fournisseur de ressources Azure « Microsoft.AzureStack ».

**Exemple :** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure

> [!NOTE]
> Toutes ces étapes doivent être exécutées à partir d’une machine ayant accès au point de terminaison privilégié. Pour le Kit de développement Azure Stack, il s’agira de l’ordinateur hôte. Si vous utilisez un système intégré, contactez votre opérateur Azure Stack.
>

1. Ouvrez une console PowerShell en tant qu’administrateur et [installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).  

2. Ajoutez le compte Azure que vous utiliserez pour inscrire Azure Stack. Pour cela, exécutez l’applet de commande `Add-AzureRmAccount` sans aucun paramètre. Vous êtes invité à entrer vos informations d’identification de compte Azure et vous devrez peut-être utiliser l’authentification à 2 facteurs en fonction de la configuration de votre compte.  

3. Si vous avez plusieurs abonnements, exécutez la commande suivante pour sélectionner celui que vous souhaitez utiliser :  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Supprimez toutes les versions existantes des modules PowerShell qui correspondent à l’inscription et [téléchargez la dernière version de celui-ci depuis GitHub](azure-stack-powershell-download.md).  

5. Dans le répertoire « AzureStack-Tools-master » créé à l’étape précédente, accédez au dossier « Registration » et importez le module « .\RegisterWithAzure.psm1 » :  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. Dans la même session PowerShell, exécutez le script suivant. À l’invite d’informations d’identification, spécifiez `azurestack\cloudadmin` comme utilisateur, le mot de passe est identique à celui utilisé pour l’administrateur local pendant le déploiement.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | Paramètre | Description |
   | -------- | ------------- |
   | CloudAdminCredential | Les informations d’identification de domaine de cloud qui sont utilisées pour [accéder au point de terminaison privilégié](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Le nom d’utilisateur est au format « \<Azure Stack domain\>\cloudadmin ». Pour le kit de développement, le nom d’utilisateur est défini sur « azurestack\cloudadmin ». Si vous utilisez un système intégré, contactez votre opérateur Azure Stack pour obtenir cette valeur.|
   | AzureSubscriptionId | L’abonnement Azure que vous utilisez pour inscrire Azure Stack.|
   | AzureDirectoryTenantName | Nom de l’annuaire de locataire Azure associé à votre abonnement Azure. La ressource de l’enregistrement sera créée dans ce locataire de l’annuaire. |
   | PrivilegedEndpoint | Une console PowerShell distante préconfigurée qui vous fournit des fonctionnalités telles que la collecte de journaux et d’autres tâches de post-déploiement. Pour le kit de développement, le point de terminaison privilégié est hébergé sur la machine virtuelle « AzS-ERCS01 ». Si vous utilisez un système intégré, contactez votre opérateur Azure Stack pour obtenir cette valeur. Pour en savoir plus, reportez-vous à la rubrique relative à l’[utilisation du point de terminaison privilégié](azure-stack-privileged-endpoint.md).|
   | BillingModel | Le modèle de facturation utilisé par votre abonnement. Les valeurs autorisées pour ce paramètre sont « Capacity », « PayAsYouUse » et « Development ». Pour le kit de développement, cette valeur est définie sur « Development ». Si vous utilisez un système intégré, contactez votre opérateur Azure Stack pour obtenir cette valeur. |

7. Une fois le script terminé, un message « Activation d’Azure Stack (cette étape peut prendre jusqu’à 10 minutes) » s’affiche. 

## <a name="verify-the-registration"></a>Vérifier l’inscription

1. Connectez-vous au portail d’administration (https://adminportal.local.azurestack.external).
2. Cliquez sur **Plus de services** > **Gestion de la Place de Marché** > **Ajouter à partir d’Azure**.
3. Si une liste d’éléments disponibles dans Azure (tels que WordPress) s’affiche, l’activation a réussi.

> [!NOTE]
> Une fois l’inscription terminée, l’avertissement relatif à la non-inscription n’apparaît plus.

## <a name="next-steps"></a>Étapes suivantes

[Se connecter à Azure Stack](azure-stack-connect-azure-stack.md)

