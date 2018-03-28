---
title: Inscrire le kit ASDK auprès d’Azure | Microsoft Docs
description: Explique comment inscrire Azure Stack auprès d’Azure pour activer la syndication de la Place de marché et les rapports d’utilisation.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 9e2dbc71f6424b87945e346a42c86d4cde7f740e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure
Vous pouvez inscrire le Kit de développement Azure Stack auprès d’Azure pour télécharger des éléments de la Place de marché à partir d’Azure et renvoyer des rapports de données commerciales à Microsoft. L’inscription est recommandée, car elle vous permet de tester des fonctionnalités Azure Stack importantes, telles que la syndication de la Place de marché et les rapports d’utilisation. Après avoir inscrit Azure Stack, l’utilisation est signalée à Azure Commerce. Vous pouvez la consulter sous l’abonnement utilisé pour l’inscription. Toutefois, l’utilisation du kit de développement Azure Stack n’est pas facturée à ses utilisateurs.


## <a name="register-azure-stack-with-azure"></a>Inscrire Azure Stack auprès d’Azure 
Pour inscrire le kit ASDK auprès d’Azure, procédez aux étapes suivantes.

> [!NOTE]
> Toutes ces étapes doivent être exécutées à partir d’un ordinateur qui a accès au point de terminaison privilégié. Pour le kit ASDK, il s’agit de l’ordinateur hôte du kit de développement. 

Avant de suivre ces étapes pour inscrire le kit ASDK auprès d’Azure, veillez à installer Azure Stack PowerShell et télécharger les outils Azure Stack, comme décrit dans l’article relatif à la [configuration post-déploiement](asdk-post-deploy.md). 

1. Ouvrez une console PowerShell en tant qu’administrateur.  

2. Pour inscrire le kit ASDK auprès d’Azure, exécutez les commandes PowerShell suivantes (vous devez vous connecter à la fois à votre abonnement Azure et à l’installation locale du kit ASDK) :

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
    Set-AzsRegistration `
        -CloudAdminCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)