---
title: "Mettre les groupes de machines virtuelles identiques à disposition dans Azure Stack | Microsoft Docs"
description: "Découvrez comment un opérateur cloud peut ajouter des machines virtuelles identiques à la Marketplace Azure Stack"
services: azure-stack
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: anajod
keywords: 
ms.openlocfilehash: 6e4c1145b8ff4665024b4532cf9cc9ac6a0a24a1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Mettre les groupes de machines virtuelles identiques à disposition dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les groupes de machines virtuelles identiques constituent une ressource de calcul Azure Stack. Vous pouvez les utiliser pour déployer et gérer un groupe de machines virtuelles identiques. Dans la mesure où toutes les machines virtuelles sont configurées de la même façon, les groupes identiques ne requièrent pas d’approvisionnement préalable des machines virtuelles. Il est plus simple de créer des services à grande échelle qui ciblent le Big Compute, le Big Data et les charges de travail en conteneurs.

Cette rubrique vous guide tout au long du processus de mise à disposition des groupes identiques dans la Marketplace Azure Stack. Lorsque vous aurez suivi cette procédure, vos utilisateurs pourront ajouter des groupes de machines virtuelles identiques à leurs abonnements.

Les groupes de machines virtuelles identiques sur Azure Stack suivent le même principe que sur Azure. Pour plus d’informations, consultez les vidéos suivantes :
* [Mark Russinovich parle des groupes identiques Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Jeux de mise à l’échelle de machine virtuelle, avec Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Sur Azure Stack, les groupes de machines virtuelles identiques ne sont pas compatibles avec la mise à l’échelle automatique. Vous pouvez ajouter d’autres instances à un groupe identique avec le portail Azure Stack, les modèles Resource Manager ou PowerShell.

## <a name="prerequisites"></a>configuration requise
* **PowerShell et outils**

   Installer et configurez PowerShell pour Azure Stack et les outils Azure Stack. Consultez la page [Devenir opérationnel avec PowerShell dans Azure Stack](azure-stack-powershell-configure-quickstart.md).

   Après avoir installé les outils Azure Stack, veillez à importer le module PowerShell suivant (chemin d’accès relatif au dossier .\ComputeAdmin du dossier AzureStack-Tools-master) :

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Image du système d’exploitation**

   Si vous n’avez pas ajouté d’image de système d’exploitation à votre Marketplace Azure Stack, consultez la page [Ajouter l’image de machine virtuelle de Windows Server 2016 à la marketplace Azure Stack](azure-stack-add-default-image.md).

   Pour la prise en charge de Linux, téléchargez Ubuntu Server 16.04 et ajoutez-le à l’aide de ```Add-AzsVMImage``` avec les paramètres suivants : ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>Ajouter le groupe de machines virtuelles identiques

Modifiez le script PowerShell suivant en fonction de votre environnement, puis exécutez-le pour ajouter un groupe de machines virtuelles identiques à votre Marketplace Azure Stack. 

``$User`` est le compte servant à se connecter au portail d’administration. Par exemple : serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>Supprimer un groupe de machines virtuelles identiques

Pour supprimer un élément de la galerie du groupe de machines virtuelles identiques, exécutez la commande PowerShell suivante :

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> L’élément de la galerie n’est pas forcément supprimé immédiatement de la marketplace. Vous devrez peut-être pour cela actualiser le portail plusieurs fois.


## <a name="next-steps"></a>Étapes suivantes
[Forum aux questions sur Azure Stack](azure-stack-faq.md)

