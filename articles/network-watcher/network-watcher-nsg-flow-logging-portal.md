---
title: "Gérer les journaux de flux des groupes de sécurité réseau avec Azure Network Watcher | Microsoft Docs"
description: "Cette page explique comment gérer les journaux des flux de groupe de sécurité réseau dans Azure Network Watcher"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 633543aba99f5c09b14a9e4b11adf59ca04d0fe5
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Gérer les journaux de flux des groupes de sécurité réseau sur le Portail Azure

> [!div class="op_single_selector"]
> - [Portail Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Les journaux de flux des groupes de sécurité réseau correspondent à une fonctionnalité de Network Watcher qui permet de visualiser des informations sur le trafic IP d’entrée et de sortie par le biais d’un groupe de sécurité réseau. Ces journaux de flux, écrits au format JSON, fournissent des informations importantes, notamment : 

- les flux entrants et sortants, règle par règle ;
- la carte réseau à laquelle s’applique le flux ;
- cinq informations sur le flux (adresse IP source ou de destination, port source ou de destination, protocole) ;
- des informations indiquant si le trafic a été autorisé ou refusé.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite sur la page [Créer une instance de Network Watcher](network-watcher-create.md). Il part également du principe que vous disposez d’un groupe de ressources et d’une machine virtuelle valide.

## <a name="register-insights-provider"></a>Inscription du fournisseur Insights

Pour que la journalisation du flux fonctionne correctement, le fournisseur **Microsoft.Insights** doit être inscrit. Pour inscrire le fournisseur, suivez les étapes ci-dessous : 

1. Accédez à **Abonnements**, puis sélectionnez l’abonnement pour lequel vous souhaitez activer les journaux de flux. 
2. Sur le panneau **Abonnement**, sélectionnez **Fournisseurs de ressources**. 
3. Étudiez la liste des fournisseurs et vérifiez que le fournisseur **microsoft.insights** est inscrit. Sinon, sélectionnez **Inscrire**.

![Afficher les fournisseurs][providers]

## <a name="enable-flow-logs"></a>Activer les journaux des flux

Ces étapes vous guident tout au long du processus d’activation des journaux de flux sur un groupe de sécurité réseau.

### <a name="step-1"></a>Étape 1

Accédez à une instance de Network Watcher, puis sélectionnez **Journaux de flux des NSG**.

![Vue d’ensemble des journaux de flux][1]

### <a name="step-2"></a>Étape 2

Sélectionnez un groupe de sécurité réseau dans la liste.

![Vue d’ensemble des journaux de flux][2]

### <a name="step-3"></a>Étape 3 : 

Dans le panneau **Paramètres des journaux de flux**, réglez l’état sur **Activé**, puis configurez un compte de stockage.  Quand vous avez terminé, sélectionnez **OK**. Ensuite, sélectionnez **Enregistrer**.

![Vue d’ensemble des journaux de flux][3]

## <a name="download-flow-logs"></a>Télécharger des journaux de flux

Les journaux des flux sont enregistrés dans un compte de stockage. Téléchargez vos journaux de flux pour les afficher.

### <a name="step-1"></a>Étape 1

Pour télécharger des journaux de flux, sélectionnez **Vous pouvez télécharger les journaux de flux à partir de comptes de stockage configurés**. Cette étape vous amène sur une vue du compte de stockage qui vous permet de choisir les journaux à télécharger.

![Paramètres des journaux de flux][4]

### <a name="step-2"></a>Étape 2

Accédez au compte de stockage approprié. Ensuite, sélectionnez **Conteneurs** > **insights-journal-networksecuritygroupflowevent**.

![Paramètres des journaux de flux][5]

### <a name="step-3"></a>Étape 3 :

Accédez à l’emplacement du journal de flux, sélectionnez-le, puis sélectionnez **Télécharger**.

![Paramètres des journaux de flux][6]

Pour plus d’informations sur la structure du journal, consultez la page [Vue d’ensemble des journaux de flux des groupes de sécurité réseau](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [visualiser vos journaux de flux des NSG avec Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
