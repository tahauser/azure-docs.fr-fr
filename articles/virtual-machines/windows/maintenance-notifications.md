---
title: Gestion des notifications de maintenance pour les machines virtuelles Windows dans Azure | Microsoft Docs
description: "Affichez les notifications de maintenance pour les machines virtuelles Windows s’exécutant dans Azure et démarrez la maintenance en libre-service."
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: zivr
ms.openlocfilehash: d6d8507508ef1946c1dfa41c47ae81f51c0ad4ef
ms.sourcegitcommit: 8fc9b78a2a3625de2cecca0189d6ee6c4d598be3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/29/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Gestion de notifications de maintenance planifiées pour les machines virtuelles Windows

Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte des machines virtuelles. Les mises à jour sont des modifications telles que la mise à jour corrective de l’environnement d’hébergement ou la mise à niveau et la désactivation de matériel. Une majorité de ces mises à jour ont lieu sans affecter les machines virtuelles hébergées. Cependant, il existe des cas où les mises à jour ont un impact :

- Lorsque la maintenance ne nécessite pas de redémarrage, Azure utilise une migration sur place pour mettre en pause la machine virtuelle pendant la mise à jour de l’hôte.

- Si la maintenance nécessite un redémarrage, une notification vous dira pour quand est prévue la maintenance. Dans ces cas, vous disposez d’une période pour commencer la maintenance vous-même, au moment qui vous convient.


La maintenance planifiée nécessitant un redémarrage s’effectue par vagues. Chaque vague a une portée différente (régions).

- Une vague commence par une notification aux clients. Par défaut, la notification est envoyée aux propriétaire et copropriétaires de l’abonnement. Vous pouvez ajouter plusieurs destinataires et options de messagerie, telles que les e-mails, les SMS et les Webhooks, aux notifications à l’aide des [alertes de journal d’activité](../../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) Azure.  
- Au moment de la notification, une *fenêtre de libre-service* est disponible. Pendant cet intervalle, vous pouvez rechercher quelles machines virtuelles sont incluses dans cette vague et démarrer la maintenance de manière proactive en fonction de vos besoins de planification.
- Après la fenêtre de libre-service, une *fenêtre de maintenance planifiée* apparaît. Au cours de cette fenêtre, Azure planifie et applique la maintenance requise à votre machine virtuelle. 

L’objectif de ces deux fenêtres est de vous donner suffisamment de temps pour commencer la maintenance et redémarrer votre machine virtuelle tout en sachant à quel moment Azure démarrera automatiquement la maintenance.


Vous pouvez utiliser le portail Azure, PowerShell, les API REST et l’interface de ligne de commande pour demander les fenêtres de maintenance pour vos machines virtuelles et démarrer la maintenance en libre-service.

 > [!NOTE]
 > Si vous essayez de démarrer la maintenance et que la requête échoue, Azure marque votre machine virtuelle comme **ignorée**. Vous ne serez plus en mesure d’utiliser l’option de maintenance lancée par le client. Votre machine virtuelle devra être redémarrée par Azure pendant la phase de maintenance planifiée.


 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Devez-vous démarrer la maintenance pendant la fenêtre de libre-service ?  

Les instructions suivantes doivent vous aider à décider si vous devez utiliser cette fonctionnalité et démarrer la maintenance à votre rythme.

> [!NOTE] 
> La maintenance de libre-service n’est peut-être pas disponible pour toutes vos machines virtuelles. Pour déterminer si le redéploiement proactif est disponible pour votre machine virtuelle, recherchez **Démarrer maintenant** dans l’état de maintenance. La maintenance de libre-service n’est actuellement pas disponible pour les Services cloud (rôle de travail/web), Service Fabric et Virtual Machine Scale Sets.


La maintenance de libre-service n’est pas recommandée pour les déploiements qui utilisent des **groupes à haute disponibilité**, car il s’agit de configurations hautement disponibles, dans lesquelles seulement un domaine de mise à jour est affecté à un moment donné. 
    - Laissez Azure déclencher la maintenance, mais n’oubliez pas que l’ordre des domaines de mise à jour affectés n’est pas forcément séquentiel, et qu’il y a une pause de 30 minutes entre les domaines de mise à jour.
    - Si une perte temporaire d’une partie de votre capacité (1/nombre de domaines de mise à jour) pose problème, elle peut facilement être compensée par l’allocation d’instances additionnelles au cours de la période de maintenance. **Ne pas** utiliser la maintenance libre-service dans les scénarios suivants : 
    - Si vous arrêtez vos machines virtuelles fréquemment, soit manuellement, à l’aide de DevTest Labs, en utilisant l’arrêt automatique ou en suivant une planification, cela peut rétablit l’état de maintenance et par conséquent entraîner un temps d’arrêt supplémentaire.
    - Sur les machines virtuelles à durée de vie limitée dont vous savez qu’elles seront supprimées avant la fin de la vague de maintenance. 
    - Pour les charges de travail avec un état volumineux stockées dans le disque local (éphémère) qui doivent être maintenues lors de la mise à jour. 
    - Dans les cas où vous redimensionnez souvent votre machine virtuelle, comme cela peut rétablir l’état de maintenance. 
    - Si vous avez adopté des événements planifiés qui permettent un basculement proactif ou l’arrêt approprié de votre charge de travail, 15 minutes avant le début de l’arrêt de la maintenance

**Utilisez** la maintenance de libre-service, si vous envisagez d’exécuter votre machine virtuelle sans interruption pendant la phase de maintenance planifiée et qu’aucune des contre-indications mentionnées ci-dessus n’est applicable. 

Il est conseillé d’utiliser la maintenance de libre-service dans les cas suivants :
    - Vous devez communiquer une fenêtre de maintenance exacte à votre administration ou à votre client final. 
    - Vous devez réaliser la maintenance pour une date donnée. 
    - Vous devez contrôler la séquence de maintenance, par exemple, les applications multiniveaux pour garantir la récupération sans échec.
    - Vous avez besoin de plus de 30 minutes de temps de récupération de machine virtuelle entre deux domaines de mise à jour (UD). Pour contrôler le délai entre les domaines de mise à jour, vous devez déclencher la maintenance sur vos machines virtuelles, un domaine de mise à jour (UD) à la fois.

 

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Vérifier l’état de maintenance à l’aide de PowerShell

Vous pouvez également utiliser Azure Powershell pour voir quand les machines virtuelles sont planifiées pour la maintenance. Les informations de maintenance planifiée sont disponibles à partir de l’applet de commande [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) lorsque vous utilisez le paramètre `-status`.
 
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. S’il n’existe aucune maintenance planifiée qui affecte la machine virtuelle, l’applet de commande ne retourne pas d’informations de maintenance. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

Les propriétés suivantes sont retournées sous MaintenanceRedeployStatus : 
| Valeur | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indique si vous pouvez démarrer la maintenance sur la machine virtuelle maintenant ||
| PreMaintenanceWindowStartTime         | Début de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| PreMaintenanceWindowEndTime           | Fin de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowStartTime            | Début de la maintenance planifiée pendant laquelle Azure lance la maintenance sur votre machine virtuelle ||
| MaintenanceWindowEndTime              | Fin de la fenêtre de maintenance planifiée pendant laquelle Azure lance la maintenance sur votre machine virtuelle ||
| LastOperationResultCode               | Résultat de la dernière tentative de lancement de la maintenance sur la machine virtuelle ||



Vous pouvez également obtenir l’état de maintenance pour toutes les machines virtuelles dans un groupe de ressources à l’aide de [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) sans spécifier de machine virtuelle.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

La fonction PowerShell suivante accepte votre ID d’abonnement et imprime une liste de machines virtuelles qui sont planifiées pour la maintenance.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Démarrer la maintenance sur votre machine virtuelle à l’aide de PowerShell

À l’aide des informations de la fonction de la section précédente, la commande suivante démarre la maintenance sur une machine virtuelle si **IsCustomerInitiatedMaintenanceAllowed** est défini sur true.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Déploiements Classic

Si vous avez encore des machines virtuelles qui ont été déployées à l’aide du modèle de déploiement Classic, vous pouvez utiliser PowerShell pour interroger ces machines virtuelles et démarrer la maintenance.

Pour obtenir l’état de la maintenance d’une machine virtuelle, tapez :

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Pour démarrer la maintenance sur votre machine virtuelle classique, tapez :

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>Forum Aux Questions


**Q : Pourquoi devez-vous redémarrer mes machines virtuelles maintenant ?**

**R :** Alors que la majorité des mises à jour et des mises à niveau vers la plateforme Azure n’affectent pas la disponibilité des machines virtuelles, il existe des cas où nous ne pouvons pas éviter le redémarrage des machines virtuelles hébergées dans Azure. Nous avons accumulé plusieurs modifications qui nous obligent à redémarrer nos serveurs, ce qui entraînera le redémarrage des machines virtuelles.

**Q : Si je suis vos recommandations concernant la haute disponibilité en utilisant un groupe à haute disponibilité, suis-je sécurisé ?**

**R :** Les machines virtuelles déployées dans un groupe à haute disponibilité ou dans des groupes de machines virtuelles identiques incluent la notion de domaines de mise à jour (UD). Lors de la réalisation de la maintenance, Azure respecte la contrainte de domaine de mise à jour et ne redémarre pas les machines virtuelles à partir de différents domaines de mise à jour (dans le même groupe à haute disponibilité).  Azure attend également au moins 30 minutes avant de passer au groupe de machines virtuelles suivant. 

Pour plus d’informations sur la haute disponibilité, consultez [Régions et disponibilité des machines virtuelles dans Azure](regions-and-availability.MD).

**Q : Comment être averti d’une maintenance planifiée ?**

**R :** Une vague d’opérations de maintenance planifiées commence par une planification sur une ou plusieurs régions Azure. Peu après, une notification par e-mail est envoyée aux propriétaires d’abonnement (un e-mail par abonnement). Il est possible de configurer des canaux et des destinataires supplémentaires pour cette notification à l’aide de la fonctionnalité Alertes de journal d’activité. Dans le cas où vous déployez une machine virtuelle dans une région où la planification de la maintenance est déjà effectuée, vous ne recevez pas la notification. Au lieu de cela, vous devez vérifier l’état de maintenance de la machine virtuelle.

**Q : Je ne vois aucune indication de maintenance planifiée dans le portail, dans PowerShell ou dans l’interface de ligne de commande (CLI). Quel est le problème ?**

**R :** Les informations relatives à la maintenance planifiée ne sont disponibles pendant une vague d’opérations de maintenance planifiées que pour les machines virtuelles qu’elle affectera. En d’autres termes, si vous ne voyez pas de données, il est possible que la vague d’opérations de maintenance soit déjà terminée (ou qu’elle n’ait pas démarré), ou bien que votre machine virtuelle soit déjà hébergée sur un serveur mis à jour.

**Q : Est-il possible de savoir exactement quand ma machine virtuelle sera affectée ?**

**R :** Lors de la définition de la planification, nous définissons une fenêtre de temps de plusieurs jours. Toutefois, le séquencement exact des serveurs (et des machines virtuelles) au sein de cette fenêtre est inconnu. Les clients qui souhaitent connaître l’heure exacte pour leurs machines virtuelles peuvent utiliser des [événements planifiés](scheduled-events.md), faire une requête à partir de la machine virtuelle et recevoir une notification 15 minutes avant le redémarrage d’une machine virtuelle.

**Q : Combien de temps vous faudra-t-il pour redémarrer ma machine virtuelle ?**

**R :** En fonction de la taille de votre machine virtuelle, le redémarrage peut prendre plusieurs minutes pendant la fenêtre de maintenance libre-service. Tout redémarrage lancé par Azure pendant la fenêtre de maintenance planifiée dure habituellement 25 minutes environ. Notez que dans le cas où vous utilisez des services cloud (rôle de travail/web), Virtual Machine Scale Sets ou des groupes à haute disponibilité, vous disposez de 30 minutes entre chaque groupe de machines virtuelles (UD) pendant la fenêtre de maintenance planifiée. 

**Q : quelle est l’expérience dans le cas des Services cloud (rôle de travail/web), de Service Fabric, et de Virtual Machine Scale Sets ?**

**R :** Alors que ces plateformes sont affectées par une maintenance planifiée, les clients qui utilisent ces plateformes sont considérés comme sécurisés étant donné que seules les machines virtuelles d’un domaine de mise à niveau (UD) seront affectées à un moment donné. La maintenance de libre-service n’est actuellement pas disponible pour les Services cloud (rôle de travail/web), Service Fabric et Virtual Machine Scale Sets.

**Q : J’ai reçu un e-mail concernant la désaffectation de matériel. Est-ce la même chose qu’une maintenance planifiée ?**

**R :** Alors que la désaffectation de matériel est un événement de maintenance planifié, nous n’avons pas encore intégré ce cas d’utilisation au nouvel environnement.  

**Q : Je ne vois aucune information de maintenance sur mes machines virtuelles. Quelle est la cause du problème ?**

**R :** Plusieurs raisons peuvent expliquer pourquoi vous ne voyez aucune information de maintenance sur vos machines virtuelles :
1.  Vous utilisez un abonnement marqué comme interne à Microsoft.
2.  Vos machines virtuelles ne sont pas planifiées pour la maintenance. Il est possible que la vague d’opérations de maintenance soit terminée, annulée ou modifiée, de sorte que celle-ci n’affecte plus vos machines virtuelles.
3.  La colonne **Maintenance** n’a pas été ajoutée à l’affichage de liste de votre machine virtuelle. Alors que nous avons ajouté cette colonne à la vue par défaut, les clients qui ont effectué une configuration pour afficher des colonnes non définies par défaut doivent ajouter manuellement la colonne **Maintenance** à la vue liste de leurs machines virtuelles.

**Q : La maintenance de ma machine virtuelle est planifiée pour la deuxième fois. Pourquoi ?**

**R :** Il existe plusieurs cas d’utilisation où vous verrez votre machine virtuelle planifiée pour la maintenance après avoir déjà effectué votre maintenance-redéploiement :
1.  Nous avons annulé la vague d’opérations de maintenance et l’avons redémarrée avec une charge utile différente. Il est possible que nous ayons détecté une charge utile ayant généré une erreur et nous voulons simplement déployer une charge utile supplémentaire.
2.  Votre machine virtuelle a été *réparée par service* sur un autre nœud en raison d’une panne matérielle.
3.  Vous avez choisi d’arrêter (de libérer) et de redémarrer la machine virtuelle.
4.  Vous avez activé **Arrêt automatique** pour la machine virtuelle.


**Q : la maintenance de mon groupe à haute disponibilité prend beaucoup de temps, et je vois maintenant l’état« ignoré » sur certaines de mes instances de groupes à haute disponibilité. Pourquoi ?** 

**R :** si vous avez cliqué pour mettre à jour plusieurs instances dans un groupe à haute disponibilité de manière successive et rapide, Azure mettra ces demandes en file d’attente et commencera à mettre à jour les machines virtuelles uniquement, dans un domaine de mise à jour (UD) à la fois. Toutefois, dans la mesure où il peut y avoir une pause entre les domaines de mise à jour, la mise à jour peut sembler prendre plus de temps. Si la file d’attente de mise à jour prend plus de 60 minutes, certaines instances afficheront l’état **ignoré**, même si elles ont correctement été mises à jour. Pour éviter cet état incorrect, mettez à jour vos groupes à haute disponibilité en cliquant uniquement sur l’instance au sein d’un groupe, et attendez que la mise à jour sur cette machine virtuelle soit terminée avant de cliquer sur la machine virtuelle suivante dans un autre domaine de mise à jour.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment vous pouvez vous inscrire aux événements de maintenance à partir de la machine virtuelle à l’aide de [Événements planifiés](scheduled-events.md).
