---
title: "Installer Trend Micro Deep Security sur une machine virtuelle | Microsoft Docs"
description: "Cet article décrit comment installer et configurer la sécurité Trend Micro sur une machine virtuelle créée avec le modèle de déploiement Classic dans Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: iainfou
ms.openlocfilehash: 41b7ecf0d0c71b5c225454cc77ce87d5736c2165
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Windows
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Cet article décrit comment installer et configurer Trend Micro Deep Security comme service sur une machine virtuelle nouvelle ou existante exécutant Windows Server. Deep Security inclut une protection anti-programmes malveillants, un pare-feu, un système de prévention contre les intrusions et une surveillance de l’intégrité.

Le client est installé sous forme d’extension de sécurité via l’agent de machine virtuelle. Sur une nouvelle machine virtuelle, vous installez Deep Security Agent, car le portail Azure crée automatiquement l’agent de machine virtuelle.

Il peut arriver qu’une machine virtuelle existante créée à l’aide du portail Azure, d’Azure CLI ou de PowerShell n’ait pas d’agent de machine virtuelle. Sur une machine virtuelle existante dépourvue de l’agent de machine virtuelle, vous devez commencer par télécharger et installer celui-ci. Cet article aborde ces deux situations.

Si vous disposez d’un abonnement Trend Micro pour une solution locale, vous pouvez l’utiliser pour protéger vos machines virtuelles Azure. Si vous n’êtes pas encore client, vous pouvez vous inscrire pour une version d’évaluation. Pour plus d’informations sur cette solution, consultez le billet de blog [Extension de l’agent de machine virtuelle Microsoft Azure pour Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installation de l'agent Deep Security sur une nouvelle machine virtuelle

Le [portail Azure](http://portal.azure.com) vous permet d’installer l’extension de sécurité Trend Micro lorsque vous utilisez une image de la **Place de marché** pour créer la machine virtuelle. La création d’une machine virtuelle unique à l’aide du portail est une méthode simple pour ajouter une protection Trend Micro.

L’utilisation d’une entrée de la **Place de marché** a pour effet d’ouvrir un Assistant qui vous aide à configurer la machine virtuelle. Vous utilisez le panneau **Paramètres**, le troisième volet de l’Assistant, pour installer l’extension de sécurité Trend Micro.  Pour obtenir des instructions générales, consultez [Créer une machine virtuelle exécutant Windows dans le portail Azure](tutorial.md).

Lorsque vous accédez au panneau **Paramètres** de l’Assistant, procédez comme suit :

1. Cliquez sur **Extensions**, puis sur **Ajouter une extension** dans le volet suivant.

   ![Commencer à ajouter l’extension][1]

2. Sélectionnez **Deep Security Agent** dans le volet **Nouvelle ressource**. Dans le volet Deep Security Agent, cliquez sur **Créer**.

   ![Identifier le Deep Security Agent][2]

3. Entrez l’**identificateur du locataire** et le **mot de passe d’activation du locataire** pour l’extension. Vous pouvez également entrer un **identificateur de stratégie de sécurité**. Ensuite, cliquez sur **OK** pour ajouter le client.

   ![Fournir les détails de l’extension][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installation de l’agent Deep Security sur une machine virtuelle existante
Pour installer l’agent sur une machine virtuelle existante, vous devez disposer des éléments suivants :

* Le module Azure PowerShell, version 0.8.2 ou ultérieure, installé sur votre poste local. Vous pouvez vérifier la version d’Azure PowerShell installée à l’aide de la commande **Get-Module azure | format-table version** . Pour des instructions et un lien vers la dernière version, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Connectez-vous à votre abonnement Azure à l’aide d’ `Add-AzureAccount`.
* L'agent de machine virtuelle installé sur la machine virtuelle cible.

Tout d'abord, vérifiez que l’agent de machine virtuelle est déjà installé. Renseignez le nom du service cloud et le nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commandes de niveau administrateur Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < et >.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si vous ignorez le nom du service cloud et de la machine virtuelle, exécutez **Get-AzureVM** afin d’afficher ces informations pour toutes les machines virtuelles de l’abonnement actuel.

Si la commande **write-host** renvoie **True**, cela signifie que l’agent de machine virtuelle est installé. Si elle retourne **False**, consultez les instructions et un lien vers le téléchargement dans le billet de blog Azure [Agent de machine virtuelle et extensions - 2e partie](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Si l'agent de machine virtuelle est installé, exécutez ces commandes.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes
Quelques minutes sont nécessaires avant que l'exécution de l'agent ne démarre, après l'installation. Vous devez ensuite activer Deep Security sur la machine virtuelle de façon à ce qu'elle puisse être gérée par Deep Security Manager. Pour obtenir des instructions supplémentaires, consultez les articles suivants :

* L’article de Trend sur cette solution, [Sécurité cloud instantanée pour Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
* Un [exemple de script Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) pour configurer la machine virtuelle.
* [instructions](http://go.microsoft.com/fwlink/?LinkId=404099) de l’exemple.

## <a name="additional-resources"></a>Ressources supplémentaires
[Connexion à une machine virtuelle exécutant Windows Server]

[Fonctionnalités et extensions de machine virtuelle Azure]

<!-- Image references -->
[1]: ./media/install-trend/new_vm_Blade3.png
[2]: ./media/install-trend/find_SecurityAgent.png
[3]: ./media/install-trend/SecurityAgentDetails.png

<!-- Link references -->
[Connexion à une machine virtuelle exécutant Windows Server]:connect-logon.md
[Fonctionnalités et extensions de machine virtuelle Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
