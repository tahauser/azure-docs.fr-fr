---
title: "Mise à niveau d’un coffre de sauvegarde vers un coffre Recovery Services | Microsoft Docs"
description: "Instructions et informations de support technique pour mettre à niveau votre coffre de sauvegarde Azure vers un coffre Recovery Services."
services: backup
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
ms.assetid: 228fef19-2f6b-4067-acc3-fb6e501afb88
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/4/2018
ms.author: sogup;markgal;arunak
ms.openlocfilehash: 8396a7276fde10eb95a22ed07fa61625acfdd77f
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services

Cet article explique comment mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services. Le processus de mise à niveau n’a d’incidence sur aucun travail de sauvegarde en cours, et n’entraîne aucune perte de données de sauvegarde. Principales raisons de mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services :
- Toutes les fonctionnalités d’un coffre de sauvegarde sont conservées dans un coffre Recovery Services.
- Les coffres Recovery Services offrent davantage de fonctionnalités que les coffres de sauvegarde, dont une meilleure sécurité, une surveillance intégrée, des restaurations plus rapides et des restaurations au niveau élément.
- Gérez les éléments de sauvegarde à partir d’un portail amélioré et simplifié.
- Les nouvelles fonctionnalités s’appliquent uniquement aux coffres Recovery Services.

## <a name="impact-to-operations-during-upgrade"></a>Incidence de la mise à niveau sur les opérations

La mise à niveau d’un coffre de sauvegarde vers un coffre Recovery Services n’a aucune incidence sur les opérations de votre plan de données. Tous les travaux de sauvegarde continuent à se dérouler normalement, et tous les travaux de restauration actifs se poursuivent sans interruption. Lors de la mise à niveau, les opérations de gestion sont brièvement interrompues et vous ne pouvez pas protéger de nouveaux éléments ou créer des travaux de sauvegarde ad hoc. Les travaux de restauration de machines virtuelles IaaS ne s’exécutent pendant la mise à niveau. La mise à niveau du coffre prend moins d’une heure. Quand elle est terminée, un coffre Recovery Services remplace le coffre de sauvegarde.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>Modifications apportées à l’automatisation et à l’outil après mise à niveau

Lors de la préparation de votre infrastructure pour la mise à niveau du coffre, vous devez mettre à jour l’automation ou les outils existants pour vous assurer qu’ils continuent à fonctionner après la mise à niveau.
Consultez les références d’applets de commande PowerShell pour le [modèle de déploiement Resource Manager](backup-client-automation.md).


## <a name="before-you-upgrade"></a>Avant de mettre à niveau

Avant de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services, vérifiez les points suivants.

- **Version minimale de l’agent** : pour mettre à niveau votre coffre, vérifiez que la version de l’agent Microsoft Azure Recovery Services (MARS) est au minimum la version 2.0.9083.0. Si la version de l’agent MARS est antérieure à la version 2.0.9083.0, mettez à jour l’agent avant de commencer le processus de mise à niveau.
- **Modèle de facturation basé sur l’instance** : les coffres Recovery Services prennent uniquement en charge le modèle de facturation basée sur l’instance. Si vous avez un coffre de sauvegarde qui utilise l’ancien modèle de facturation basée sur le stockage, convertissez le modèle de facturation durant la mise à niveau.
- **Aucune opération de configuration de la sauvegarde en cours** : durant la mise à niveau, l’accès au plan de gestion est limité. Effectuez toutes les actions du plan de gestion, puis démarrez la mise à niveau.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>Utilisation de scripts PowerShell pour mettre à niveau vos coffres

Vous pouvez utiliser des scripts PowerShell pour mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Vérifiez que vous disposez des composants PowerShell requis pour déclencher la mise à niveau de coffre. Les scripts PowerShell pour les coffres de sauvegarde ne fonctionnent pas pour les coffres Recovery Services. Préparez votre environnement à la mise à niveau des coffres :

1. Installez ou mettez à niveau [Windows Management Framework (WMF) vers la version 5](https://www.microsoft.com/download/details.aspx?id=50395) ou ultérieure.
2. [Installez Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi).
3. Téléchargez le [script PowerShell](https://aka.ms/vaultupgradescript2) pour mettre à niveau vos coffres.

### <a name="run-the-powershell-script"></a>Exécution du script PowerShell

Utilisez le script suivant pour mettre à niveau vos coffres. L’exemple de script suivant comprend des explications des paramètres.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID** : numéro d’ID d’abonnement du coffre en cours de mise à niveau.<br/>
**VaultName** : nom du coffre de sauvegarde en cours de mise à niveau.<br/>
**Location** : emplacement du coffre en cours de mise à niveau.<br/>
**ResourceType** : utilisez BackupVault.<br/>
**TargetResourceGroupName** : étant donné que vous mettez à niveau le coffre vers un déploiement basé sur Resource Manager, spécifiez un groupe de ressources. Vous pouvez utiliser un groupe de ressources existant ou en créez un en fournissant un nouveau nom. Si vous orthographiez mal le nom d’un groupe de ressources, vous pouvez en créer un autre. Pour plus d’informations sur les groupes de ressources, voir la [Présentation des groupes de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups).

>[!NOTE]
> Les noms de groupes de ressources doivent respecter certaines contraintes. Veillez à suivre les recommandations. Le non-respect de celles-ci pourrait entraîner l’échec des mises à niveau de coffre.
>
>Les clients **Azure du gouvernement américain** doivent définir l’environnement sur « AzureUSGovernment » lors de l’exécution du script.
>Les clients **Azure China** doivent définir l’environnement sur « AzureChinaCloud » durant l’exécution du script.

L’extrait de code suivant illustre ce à quoi votre commande PowerShell devrait ressembler :

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

Vous pouvez également exécuter le script sans paramètre. Vous êtes alors invité à fournir des entrées pour tous les paramètres obligatoires.

Le script PowerShell vous invite à entrer vos informations d’identification. Entrez vos informations d’identification à deux reprises : une première fois pour le compte Service Manager et une deuxième fois pour le compte Resource Manager.

### <a name="pre-requisites-checking"></a>Vérification des conditions préalables
Une fois que vous avez entré vos informations d’identification Azure, Azure vérifie que votre environnement remplit les conditions préalables suivantes :

- **Version minimale de l’agent** : la mise à niveau des coffres de sauvegarde vers des coffres Recovery Services exige que la version de l’agent MARS soit au moins la version 2.0.9083.0. Si vous avez des éléments inscrits auprès d’un coffre de sauvegarde avec un agent dont la version est antérieure à la version 2.0.9083.0, la vérification des prérequis échoue. Si la vérification des conditions préalables échoue, mettez à jour l’agent et réessayez de mettre à niveau le coffre. Vous pouvez télécharger la dernière version de l’agent à partir de l’URL [http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).
- **Tâches de configuration en cours** : si quelqu’un est en train de configurer un travail pour un coffre de sauvegarde défini pour être mis à niveau, ou d’inscrire un élément, la vérification des conditions préalables échoue. Achevez la configuration ou l’inscription de l’élément, puis démarrez le processus de mise à niveau du coffre.
- **Modèle de facturation basée sur le stockage** : les coffres Recovery Services prennent en charge le modèle de facturation basée sur l’instance. Si vous exécutez la mise à niveau du coffre sur un coffre de sauvegarde utilisant le modèle de facturation basée sur le stockage, vous être invité à mettre à niveau votre modèle de facturation en même temps que le coffre. Autrement, vous pouvez commencer par mettre à jour votre modèle de facturation, avant d’exécuter la mise à niveau du coffre.
- Identifiez un groupe de ressources pour le coffre Recovery Services. Pour tirer parti des fonctionnalités de déploiement de Resource Manager, vous devez placer un coffre Recovery Services dans un groupe de ressources. Si vous ignorez le groupe de ressources à utiliser, entrez un nom. Le processus de mise à niveau crée alors le groupe de ressources pour vous. Le processus de mise à niveau associe également le coffre au nouveau groupe de ressources.

Une fois que le processus de mise à niveau a fini de vérifier les conditions préalables, le processus vous invite à démarrer la mise à niveau du coffre. Après que vous avez confirmé, l’exécution du processus de mise à niveau prend généralement de 15 à 20 minutes, selon la taille de votre coffre. Si vous avez un coffre de grande taille, la mise à niveau peut prendre jusqu’à 90 minutes.

## <a name="managing-your-recovery-services-vaults"></a>Accéder à vos coffres Recovery Services

Les écrans suivants montrent un nouveau coffre Recovery Services, mis à niveau à partir du coffre de sauvegarde, dans le portail Azure. Le premier écran présente le tableau de bord du coffre qui affiche les entités clés pour le coffre.

![exemple de coffre Recovery Services mis à niveau à partir d’un coffre de sauvegarde](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

Le deuxième écran présente les liens d’aide disponibles pour vous aider à commencer à utiliser le coffre Recovery Services.

![liens d’aide dans le panneau Démarrage rapide](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Étapes post-mise à niveau
Les coffres Recovery Services prennent en charge la spécification d’informations de fuseau horaire dans la stratégie de sauvegarde. Une fois le coffre mis à niveau, accédez à Stratégies de sauvegarde dans le menu de paramètres du coffre et mettez à jour les informations de fuseau horaire pour chacune des stratégies configurées dans le coffre. Cet écran montre déjà l’heure de planification de sauvegarde spécifiée en fonction du fuseau horaire local utilisé quand vous avez créé la stratégie. 

## <a name="enhanced-security"></a>Sécurité améliorée

Quand un coffre de sauvegarde est mis à niveau vers un coffre Recovery Services, les paramètres de sécurité de ce coffre sont activés automatiquement. Quand les paramètres de sécurité sont actifs, certaines opérations telles que la suppression des sauvegardes ou la modification d’une phrase secrète nécessitent un code PIN [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Pour plus d’informations sur la sécurité améliorée, consultez l’article [Fonctionnalités de sécurité pour la protection des sauvegardes hybrides](backup-azure-security-feature.md). 

Quand la sécurité améliorée est activée, les données sont conservées jusqu’à 14 jours après que les informations relatives au point de récupération ont été supprimées du coffre. Les clients sont facturés pour le stockage de ces données de sécurité. La rétention des données de sécurité s’applique aux points de récupération définis pour l’agent de sauvegarde Azure, le serveur de sauvegarde Azure et System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Collecter des données sur votre coffre

Une fois que vous avez effectué la mise à niveau vers un coffre Recovery Services, configurez des rapports pour Sauvegarde Azure (pour les machines virtuelles IaaS et Microsoft Azure Recovery (MARS)), et utilisez Power BI pour accéder aux rapports. Pour plus d’informations sur la collecte de données, consultez l’article [Configurer les rapports de la Sauvegarde Azure](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Le plan de mise à niveau affecte-t-il mes sauvegardes en cours ?**</br>
Non. Vos sauvegardes en cours se poursuivent sans interruption pendant et après la mise à niveau.

**Si je n’envisage de mise à niveau prochainement, qu’advient-il de mes coffres ?**</br>
Toutes les nouvelles fonctionnalités s’appliquant uniquement aux coffres Recovery Services, nous vous conseillons vivement de mettre à niveau vos coffres. À compter du 1er septembre 2017, Microsoft commencera la mise à niveau automatique des coffres de sauvegarde vers des coffres Recovery Services. Après le 30 novembre 2017, vous ne pouvez plus créer de coffres de sauvegarde à l’aide de PowerShell. Votre coffre peut être mis à niveau automatiquement à tout moment dans l’intervalle. Microsoft vous recommande de mettre à niveau votre coffre dès que possible.

**Quelles sont les implications de cette migration pour mes outils existants ?**</br>
Mettez à jour vos outils vers le modèle de déploiement Resource Manager. Les coffres Recovery Services ont été créés pour une utilisation dans le modèle de déploiement Resource Manager. Il est important de planifier le modèle de déploiement Resource Manager et de tenir compte de la différence dans vos coffres. 

**Lors de la mise à niveau, existe-t-il un temps d’arrêt ?**</br>
Cette durée dépend du nombre de ressources faisant l’objet de la mise à niveau. Pour les déploiements de taille modeste (quelques dizaines d’instances protégées), la mise à niveau entière doit prendre moins de 20 minutes. Pour les déploiements plus importants, il ne devrait pas excéder une heure.

**Puis-je restaurer après la mise à niveau ?**</br>
Non. La restauration n’est pas prise en charge une fois la mise à niveau des ressources opérée avec succès.

**Puis-je vérifier si mon abonnement ou mes ressources peuvent faire l’objet d’une mise à niveau ?**</br>
Oui. La première étape d’une mise à niveau consiste à valider le fait que les ressources peuvent être mises à niveau. En cas d’échec de la validation des conditions préalables, vous recevez des messages concernant toutes les raisons pour lesquelles la mise à niveau ne peut pas être effectuée.

**Puis-je mettre à niveau mon coffre de sauvegarde basé sur un fournisseur de services de chiffrement ?**</br>
Non. Actuellement, vous ne pouvez pas mettre à niveau des coffres de sauvegarde basés sur un fournisseur de services de chiffrement. Nous ajouterons la prise en charge de la mise à niveau des coffres de sauvegarde basés sur un fournisseur de services de chiffrement dans les versions ultérieures.

**Puis-je afficher mon coffre Azure Classic après mise à niveau ?**</br>
Non. Vous ne pouvez pas afficher ou gérer votre coffre classique après mise à niveau. Vous pouvez utiliser le nouveau portail Azure uniquement pour toutes les actions de gestion sur le coffre.

**Ma mise à niveau a échoué, mais l’ordinateur où se trouvait l’agent nécessitant une mise à jour n’existe plus. Que faire dans ce cas ?**</br>
Si vous devez stocker les sauvegardes de cet ordinateur pour une rétention à long terme, vous ne pourrez pas mettre à niveau le coffre. Dans les versions ultérieures, nous ajouterons la prise en charge de la mise à niveau d’un coffre de ce type.
Si vous n’avez plus besoin de stocker les sauvegardes de cet ordinateur, désinscrivez-le du coffre et réessayez la mise à niveau.

**Pourquoi ne puis-je pas voir les informations relatives aux travaux de mes ressources après la mise à niveau ?**</br>
La surveillance des sauvegardes (agent MARS et IaaS) est une nouvelle fonctionnalité que vous obtenez quand vous mettez à niveau votre coffre de sauvegarde vers un coffre Recovery Services. La synchronisation des informations de surveillance avec le service prend jusqu’à 12 heures.

**Comment signaler un problème ?**</br>
Si une partie de la mise à niveau du coffre échoue, notez l’OperationId mentionné dans l’erreur. Le Support technique de Microsoft travaillera de manière proactive pour résoudre le problème. Vous pouvez contacter le Support ou nous envoyer un e-mail à l’adresse rsvaultupgrade@service.microsoft.com avec votre ID d’abonnement, le nom du coffre et l’OperationId. Nous tenterons de résoudre le problème le plus rapidement possible. Ne retentez l’opération que sauf si vous y êtes explicitement invité par Microsoft.


## <a name="next-steps"></a>Étapes suivantes
Consultez l’article suivant pour :</br>
[Sauvegarder une machine virtuelle IaaS](backup-azure-arm-vms-prepare.md)</br>
[Sauvegarder un serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md)</br>
[Sauvegarder un serveur Windows Server](backup-configure-vault.md)
