---
title: "Intégrer Azure DevTest Labs à votre pipeline de livraison et d’intégration continue VSTS | Microsoft Docs"
description: "Découvrez comment intégrer Azure DevTest Labs à votre pipeline de livraison et d’intégration continue VSTS."
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: v-craic
ms.openlocfilehash: 6c6bd4fbd89ec87cbbdbfb9ed42f86a484acf7ad
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Intégrer Azure DevTest Labs à votre pipeline de livraison et d’intégration continue VSTS
Vous pouvez utiliser l’extension *Azure DevTest Labs Tasks* installée dans Visual Studio Team Services (VSTS) pour intégrer facilement votre pipeline de génération et mise en production CI/CD à Azure DevTest Labs. L’extension installe trois tâches : 
* Créer une machine virtuelle
* Créer une image personnalisée à partir d’une machine virtuelle
* Supprimer une machine virtuelle 

Le processus facilite, par exemple, le déploiement rapide d’une image de référence pour une tâche de test spécifique, puis sa suppression une fois le test terminé.

Cet article explique comment créer et déployer une machine virtuelle, créer une image personnalisée, puis supprimer la machine virtuelle, le tout en tant que pipeline complet. Normalement, vous exécutez chaque tâche individuellement dans votre pipeline de génération, test et déploiement personnalisé.

## <a name="before-you-begin"></a>Avant de commencer
Pour pouvoir intégrer votre pipeline CI/CD à Azure DevTest Labs, vous devez installer l’extension à partir de Visual Studio Marketplace.
1. Accédez à [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Sélectionnez **Installer**.
1. Terminez l’Assistant.

## <a name="create-a-resource-manager-template"></a>Créer un modèle Resource Manager
Cette section décrit comment créer le modèle Azure Resource Manager que vous utilisez pour créer une machine virtuelle Azure à la demande.

1. Pour créer un modèle Resource Manager dans votre abonnement, suivez la procédure indiquée dans [Utiliser un modèle Resource Manager](devtest-lab-use-resource-manager-template.md).
1. Avant de générer le modèle Resource Manager, ajoutez [l’artefact WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) dans le cadre de la création de la machine virtuelle.

   L’accès à WinRM est requis pour utiliser des tâches de déploiement telles que *Copie de fichiers Azure* et *PowerShell sur des ordinateurs cibles*.

   > [!NOTE]
   > Quand vous utilisez WinRM avec une adresse IP partagée, vous devez ajouter une règle NAT pour mapper un port externe au port WinRM. Cette étape n’est pas requise si vous créez la machine virtuelle avec une adresse IP publique.
   >
   >

1. Enregistrez le modèle dans un fichier sur votre ordinateur. Nommez le fichier **CreateVMTemplate.json**.
1. Archivez le modèle dans votre système de contrôle de code source.
1. Ouvrez un éditeur de texte et collez-y le script suivant.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Archivez le script dans votre système de contrôle de code source. Nommez le, par exemple, **GetLabVMParams.ps1**.

   Quand vous exécutez ce script sur l’agent dans le cadre de la définition de mise en production et que vous utilisez des tâches telles que *Copie de fichiers Azure* ou *PowerShell sur des ordinateurs cibles*, le script collecte les valeurs dont vous avez besoin pour déployer votre application sur la machine virtuelle. Normalement, vous utilisez ces tâches pour déployer des applications sur une machine virtuelle Azure. Les tâches requièrent des valeurs telles que le nom du groupe de ressources de la machine virtuelle, une adresse IP et un nom de domaine complet (FQDN).

## <a name="create-a-release-definition-in-release-management"></a>Créer une définition de mise en production Release Management
Pour créer la définition de mise en production, effectuez les étapes suivantes :

1. Sous l’onglet **Mises en production** du hub **Build et mise en production**, sélectionnez le bouton portant un signe plus (+).
2. Dans la fenêtre **Créer une définition de mise en production**, sélectionnez le modèle **Vide**, puis sélectionnez **Suivant**.
3. Sélectionnez **Choisir plus tard**, puis sélectionnez **Créer** pour créer une définition de mise en production avec un environnement par défaut et aucun artefact lié.
4. Pour ouvrir le menu contextuel, dans la nouvelle définition de mise en production, sélectionnez les points de suspension (...) en regard du nom de l’environnement, puis sélectionnez **Configurer les variables**. 
5. Dans la fenêtre **Configurer l’environnement**, pour les variables que vous utilisez dans les tâches de définition de mise en production, entrez les valeurs suivantes :

   a. Pour **vmName**, entrez le nom que vous avez assigné à la machine virtuelle quand vous avez créé le modèle Resource Manager dans le portail Azure.

   b. Pour **userName**, entrez le nom d’utilisateur que vous avez assigné à la machine virtuelle quand vous avez créé le modèle Resource Manager dans le portail Azure.

   c. Pour **password**, entrez le mot de passe que vous avez assigné à la machine virtuelle quand vous avez créé le modèle Resource Manager dans le portail Azure. Utilisez l’icône en forme de cadenas pour masquer et sécuriser le mot de passe.

### <a name="create-a-vm"></a>Créer une machine virtuelle

L’étape suivante du déploiement consiste à créer la machine virtuelle à utiliser en tant qu’image de référence pour les déploiements suivants. Vous créez la machine virtuelle au sein de votre instance Azure DevTest Lab à l’aide de la tâche spécialement conçue à cet effet. 

1. Dans la définition de mise en production, sélectionnez **Ajouter des tâches**.
2. Sous l’onglet **Déployer**, ajoutez une tâche *Azure DevTest Labs - Créer une machine virtuelle*. Configurez la tâche comme indiqué ci-dessous :

   > [!NOTE]
   > Pour créer la machine virtuelle à utiliser pour les déploiements suivants, consultez [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pour **Abonnement RM Azure**, sélectionnez une connexion dans la liste **Connexions au service Azure disponibles**, ou créez une connexion d’autorisations plus limitée à votre abonnement Azure. Pour plus d’informations, consultez [Point de terminaison de service Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Pour **Nom du lab**, sélectionnez le nom de l’instance que vous avez créée.

   c. Pour **Nom du modèle**, entrez le chemin complet et le nom du fichier de modèle que vous avez enregistré dans votre dépôt de code source. Vous pouvez utiliser les propriétés intégrées de Release Management pour simplifier le chemin, par exemple :

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Pour **Paramètres du modèle**, entrez les paramètres des variables qui sont définies dans le modèle. Utilisez les noms des variables que vous avez définies dans l’environnement, par exemple :

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Pour **Variables de sortie - ID de la machine virtuelle du lab**, indiquez l’ID de la machine virtuelle créée ; vous en aurez besoin au cours des étapes suivantes. Vous définissez le nom par défaut de la variable d’environnement qui est automatiquement renseignée avec cet ID dans la section **Variables de sortie**. Vous pouvez modifier la variable, si nécessaire, mais n’oubliez pas d’utiliser le nom correct dans les tâches suivantes. L’ID de la machine virtuelle du lab est écrit sous la forme suivante :

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Exécutez le script que vous avez créé pour collecter les détails de la machine virtuelle DevTest Labs. 
4. Dans la définition de mise en production, sélectionnez **Ajouter des tâches** puis, sous l’onglet **Déployer**, ajoutez une tâche *Azure PowerShell*. Configurez la tâche comme indiqué ci-dessous :

   > [!NOTE]
   > Pour collecter les détails de la machine virtuelle DevTest Labs, consultez [Deploy: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) (Déployer : Azure PowerShell) et exécutez le script.

   a. Pour **Type de connexion Azure**, sélectionnez **Azure Resource Manager**.

   b. Pour **Abonnement RM Azure**, sélectionnez une connexion dans la liste sous **Connexions au service Azure disponibles**, ou créez une connexion d’autorisations plus limitée à votre abonnement Azure. Pour plus d’informations, consultez [Point de terminaison de service Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. Pour **Type de script**, sélectionnez **Fichier de script**.
 
   d. Pour **Chemin du script**, entrez le chemin complet et le nom du script que vous avez enregistré dans votre dépôt de code source. Vous pouvez utiliser les propriétés intégrées de Release Management pour simplifier le chemin, par exemple :
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Pour **Arguments de script**, entrez le nom de la variable d’environnement qui a été automatiquement renseignée avec l’ID de la machine virtuelle du lab par la tâche précédente, par exemple : 
      ```
      -labVmId '$(labVMId)'
      ```
    Le script collecte les valeurs requises et les stocke dans des variables d’environnement au sein de la définition de mise en production afin que vous puissiez facilement y faire référence dans les étapes suivantes.

5. Déployez votre application sur la nouvelle machine virtuelle DevTest Labs. Les tâches que vous utilisez normalement pour déployer l’application sont *Copie de fichiers Azure* et *PowerShell sur des ordinateurs cibles*.
   Les informations sur la machine virtuelle dont vous avez besoin pour les paramètres de ces tâches sont stockées dans trois variables de configuration nommées **labVmRgName**, **labVMIpAddress** et **labVMFqdn** au sein de la définition de mise en production. Si vous souhaitez uniquement tester la création d’une machine virtuelle DevTest Labs et d’une image personnalisée, sans y déployer d’application, vous pouvez ignorer cette étape.

### <a name="create-an-image"></a>Créer une image

L’étape suivante consiste à créer, dans votre instance Azure DevTest Labs, une image de la machine virtuelle qui vient d’être déployée. Vous pouvez ensuite utiliser l’image pour créer des copies de la machine virtuelle à la demande chaque fois que vous souhaitez exécuter une tâche de développement ou exécuter des tests. 

1. Dans la définition de mise en production, sélectionnez **Ajouter des tâches**.
2. Sous l’onglet **Déployer**, ajoutez une tâche **Azure DevTest Labs - Créer une image personnalisée**. Configurez-le comme suit :

   > [!NOTE]
   > Pour créer l’image, consultez [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pour **Abonnement RM Azure**, dans la liste **Connexions au service Azure disponibles**, sélectionnez une connexion, ou créez une connexion d’autorisations plus limitée à votre abonnement Azure. Pour plus d’informations, consultez [Point de terminaison de service Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Pour **Nom du lab**, sélectionnez le nom de l’instance que vous avez créée.

   c. Pour **Nom de l’image personnalisée**, entrez un nom pour l’image personnalisée.

   d. (Facultatif) Pour **Description**, entrez une description afin de faciliter la sélection de l’image appropriée ultérieurement.

   e. Pour **Machine virtuelle du lab source - ID de la machine virtuelle du lab source**, si vous avez changé le nom par défaut de la variable d’environnement qui a été automatiquement renseignée avec l’ID de la machine virtuelle du lab par une tâche antérieure, modifiez-le ici. La valeur par défaut est **$(labVMId)**.

   f. Pour **Variables de sortie - ID de l’image personnalisée**, indiquez l’ID de l’image qui vient d’être créée ; cet ID est nécessaire pour gérer ou supprimer l’image. Le nom par défaut de la variable d’environnement qui est automatiquement renseignée avec cet ID est défini dans la section **Variables de sortie**. Vous pouvez modifier la variable si nécessaire.

### <a name="delete-the-vm"></a>Supprimer la machine virtuelle

La dernière étape consiste à supprimer la machine virtuelle que vous avez déployée dans votre instance Azure DevTest Labs. Normalement, vous supprimez la machine virtuelle après avoir exécuté les tâches de développement ou exécuté les tests dont vous avez besoin sur la machine virtuelle déployée. 

1. Dans la définition de mise en production, sélectionnez **Ajouter des tâches** puis, sous l’onglet **Déployer**, ajoutez une tâche *Azure DevTest Labs - Supprimer la machine virtuelle*. Configurez-le comme suit :

      > [!NOTE]
      > Pour supprimer la machine virtuelle, consultez [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pour **Abonnement RM Azure**, sélectionnez une connexion dans la liste **Connexions au service Azure disponibles**, ou créez une connexion d’autorisations plus limitée à votre abonnement Azure. Pour plus d’informations, consultez [Point de terminaison de service Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. Pour **ID de la machine virtuelle du lab**, si vous avez changé le nom par défaut de la variable d’environnement qui a été automatiquement renseignée avec l’ID de la machine virtuelle du lab par une tâche antérieure, modifiez-le ici. La valeur par défaut est **$(labVMId)**.

2. Entrez un nom pour la définition de mise en production, puis enregistrez-le.
3. Créez une mise en production, sélectionnez la build la plus récente et déployez-la sur l’environnement unique dans la définition.

À chaque étape, actualisez l’affichage de votre instance DevTest Labs dans le portail Azure pour réafficher la machine virtuelle et l’image en cours de création, ainsi que la machine virtuelle qui est supprimée.

Vous pouvez maintenant utiliser l’image personnalisée pour créer des machines virtuelles quand elles s’avèrent nécessaires.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [Créer des environnements à plusieurs machines virtuelles avec les modèles Resource Manager](devtest-lab-create-environment-from-arm.md).
* Découvrez les autres modèles Resource Manager à démarrage rapide pour l’automatisation de DevTest Labs à partir du [dépôt DevTest Labs GitHub public](https://github.com/Azure/azure-quickstart-templates).
* Si nécessaire, consultez la page [VSTS Troubleshooting](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) (Résolution des problèmes liés à VSTS).
 
