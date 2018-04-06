---
title: Mise à jour 1802 d’Azure Stack | Microsoft Docs
description: Découvrez le contenu de la mise à jour 1802 pour les systèmes intégrés Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 71862463a62f11a4f2cea7dfcc60961331ded377
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-1802-update"></a>Mise à jour 1802 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans cette mise à jour 1802, les problèmes connus dans cette publication, et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]        
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build    
Le numéro de build de mise à jour d’Azure Stack 1802 est **20180302.1**.  


## <a name="before-you-begin"></a>Avant de commencer    
> [!IMPORTANT]    
> Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](/azure-stack-updates#plan-for-updates) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).


### <a name="prerequisites"></a>Prérequis

- Installez la [mise à jour 1712](azure-stack-update-1712.md) d’Azure Stack avant d’appliquer la mise à jour 1802 d’Azure Stack.    

- Installez le **correctif logiciel AzS - 1.0.180312.1 - Build 20180222.2** avant d’appliquer la mise à jour 1802 d’Azure Stack. Ce correctif logiciel met à jour Windows Defender et est disponible lorsque vous téléchargez des mises à jour pour Azure Stack.

  Pour installer le correctif logiciel, suivez les procédures normales pour [l’installation des mises à jour d’Azure Stack](azure-stack-apply-updates.md). Le nom de la mise à jour apparaît en tant que **Correctif logiciel AzS - 1.0.180312.1** et inclut les fichiers suivants : 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Après avoir chargé ces fichiers dans un compte de stockage et un conteneur, exécutez la programmation d’installation à partir de la vignette Mise à jour du portail d’administration. 
  
  Contrairement aux mises à jour d’Azure Stack, l’installation de cette mise à jour ne modifie pas la version d’Azure Stack.  Pour confirmer l’installation de cette mise à jour, affichez la liste des **mises à jour installées**.
 


### <a name="post-update-steps"></a>Étapes après la mise à jour
*Il n’existe aucune étape après la mise à jour pour la mise à jour 1802.*


### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Cette mise à jour inclut les améliorations et les correctifs suivants pour Azure Stack.

- **La prise en charge est ajoutée pour les versions d’API de service de stockage Azure suivantes** :
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Pour plus d’informations, consultez [Stockage Azure Stack : Différences et considérations](/azure/azure-stack/user/azure-stack-acs-differences).

- **Prise en charge des [objets blob de blocs](azure-stack-acs-differences.md) plus grands**:
    - La taille maximum de bloc autorisée passe de 4 Mo à 100 Mo.
    - La taille maximum d’objet blob passe de 195 Go à 4,75 To.  

- **Infrastructure backup** s’affiche maintenant dans la vignette Fournisseurs de ressources, et des alertes de sauvegarde sont activées. Pour plus d’informations sur le service Infrastructure Backup, consultez [Sauvegarde et récupération de données pour Azure Stack avec le service Infrastructure Backup](azure-stack-backup-infrastructure-backup.md).

- **Mettez à jour vers la cmdlet *Test-AzureStack***  pour améliorer les diagnostics de stockage. Pour plus d’informations sur cette cmdlet, consultez [Exécuter un test de validation pour Azure Stack](azure-stack-diagnostic-test.md).

- **Améliorations du contrôle d’accès en fonction du rôle** : vous pouvez maintenant utiliser le contrôle d’accès en fonction du rôle pour déléguer des autorisations à des groupes d’utilisateurs universels lorsqu’Azure Stack est déployé avec AD FS. Pour en savoir plus sur le contrôle d’accès en fonction du rôle, consultez [Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md).

- **La prise en charge est ajoutée pour plusieurs domaines d’erreur**.  Pour plus d’informations, consultez [High availability for Azure Stack](azure-stack-key-features.md#high-availability-for-azure-stack) (Haute disponibilité pour Azure Stack).

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.

<!--
#### New features
-->


<!--
#### Fixes
-->


### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour    
*Il n’y a aucun problème connu pour l’installation de la mise à jour 1802.*


### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)
Les éléments suivants sont des problèmes connus après l’installation pour la build **20180302.1**

#### <a name="portal"></a>Portail
- La possibilité [d’ouvrir une nouvelle demande de support dans la liste déroulante](azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail administrateur n’est pas disponible. À la place, utilisez le lien suivant :     
    - Pour les systèmes Azure Stack intégrés, utilisez https://aka.ms/newsupportrequest.

- <!-- 2050709 --> In the admin portal, it is not possible to edit storage metrics for Blob service, Table service, or Queue service. When you go to Storage, and then select the blob, table, or queue service tile, a new blade opens that displays a metrics chart for that service. If you then select Edit from the top of the metrics chart tile, the Edit Chart blade opens but does not display options to edit metrics.

- Il se peut qu’il ne soit pas possible d’afficher les ressources de calcul ou de stockage sur le portail d’administration. Ce problème est dû au fait qu’une erreur s’est produite pendant l’installation de la mise à jour, et que celle-ci a été, à tort, signalée comme réussie. Si ce problème se produit, contactez les services de support technique Microsoft pour obtenir de l’aide.

- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.

- Quand vous affichez les propriétés d’une ressource ou d’un groupe de ressources, le bouton **Déplacer** est désactivé. Il s’agit du comportement attendu. Le déplacement de ressources ou de groupes de ressources entre des groupes de ressources ou des abonnements n’est pas pris en charge actuellement.

- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

- Dans le tableau de bord du portail d’administration, la vignette Mise à jour ne parvient pas à afficher les informations sur les mises à jour. Pour résoudre ce problème, cliquez sur la vignette pour l’actualiser.

-   Dans le portail d’administration, vous pouvez voir une alerte critique pour le composant Microsoft.Update.Admin. Le nom de l’alerte, la description et la correction s’affichent tous comme suit :  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERREUR - Absence du modèle de FaultType ResourceProviderTimeout.)

    Cette alerte peut être ignorée en toute sécurité. 

- <!-- 2253274 --> In the admin and user portals, the Settings blade for vNet Subnets fails to load. As a workaround, use PowerShell and the [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0) cmdlet to view and  manage this information.

- Dans le portail d’administration et le portail utilisateur, le panneau Vue d’ensemble ne parvient pas à charger lorsque vous sélectionnez le panneau Vue d’ensemble des comptes de stockage qui ont été créés avec une ancienne version de l’API (exemple : 2015-06-15). Cela inclut les comptes de stockage de système comme **updateadminaccount** utilisé pendant la mise à jour et le correctif. 

  Pour résoudre ce problème, utilisez PowerShell pour exécuter le script **ResourceSynchronization.ps1** pour restaurer l’accès aux détails du compte de stockage. [Le script est disponible à partir de GitHub]( https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts) et doit s’exécuter avec des informations d’identification d’administrateur de service sur le point de terminaison privilégié. 


#### <a name="health-and-monitoring"></a>Intégrité et surveillance
Il n’y a aucun problème connu après la mise à jour vers 1802.

#### <a name="marketplace"></a>Marketplace
- Les utilisateurs ont la possibilité de parcourir entièrement le marketplace, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.

#### <a name="compute"></a>Calcul
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

- Azure Stack prend en charge l’utilisation de disques durs virtuels de type fixe uniquement. Certaines images proposées par le biais de la Place de Marché sur Azure Stack utilisent des disques durs virtuels dynamiques, mais elles ont été supprimées. Le redimensionnement d’une machine virtuelle à laquelle un disque dynamique est attaché laisse la machine virtuelle dans un état d’échec.

  Pour résoudre ce problème, supprimez la machine virtuelle sans supprimer le disque de la machine virtuelle, un objet blob VHD dans un compte de stockage. Convertissez ensuite le disque dur virtuel de disque dynamique en disque fixe, puis recréez la machine virtuelle.

- Lorsque vous créez un groupe à haute disponibilité dans le portail en accédant à **Nouveau** > **Compute** > **Groupe à haute disponibilité**, vous pouvez uniquement créer un groupe à haute disponibilité avec un domaine d’erreur et un domaine de mise à jour de 1. Pour contourner ce problème, lors de la création d’une nouvelle machine virtuelle, créez le groupe à haute disponibilité à l’aide de PowerShell, CLI, ou depuis le portail.

- Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, le portail affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de la série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

-  Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

- <!-- 1662991 --> Linux VM diagnostics is not supported in Azure Stack. When you deploy a Linux VM with VM diagnostics enabled, the deployment fails. The deployment also fails if you enable the Linux VM basic metrics through diagnostic settings.  




#### <a name="networking"></a>Mise en réseau
- Une fois une machine virtuelle créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.

- L’équilibrage de charge interne gère incorrectement les adresses MAC des machines virtuelles principales, ce qui le bloque lorsqu’il utilise des instances Linux sur le réseau principal.  L’équilibrage de charge interne fonctionne correctement avec des instances Windows sur le réseau principal.

-   La fonctionnalité de transfert IP est visible dans le portail ; toutefois son activation n’a aucun effet. Cette fonctionnalité n’est pas encore prise en charge.

- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

- Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.

-   <!-- 2096388 --> You cannot use the admin portal to update rules for a network security group. 

    Solution de contournement pour App Service : si vous avez besoin d’établir la connexion d’un bureau à distance à des instances de contrôleur, vous modifiez les règles de sécurité dans les groupes de sécurité réseau avec PowerShell.  Voici des exemples montrant comment *autoriser* la configuration, puis la restaurer sur *refuser* :  
    
    - *Autoriser :*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn’t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Refuser :*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn’t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL et MySQL
 - Avant de continuer, consultez la remarque importante dans [Avant de commencer](#before-you-begin) vers le début de ces notes de publication.
- Il faut parfois attendre une heure pour que les utilisateurs puissent créer des bases de données dans un nouveau déploiement SQL ou MySQL.

- Seul le fournisseur de ressources est pris en charge pour créer des éléments sur des serveurs qui hébergent SQL ou MySQL. Les éléments créés sur un serveur hôte qui ne sont pas créés par le fournisseur de ressources peuvent entraîner un état qui ne correspond pas.  


> [!NOTE]  
> Après la mise à jour vers Azure Stack 1802, vous pouvez continuer à utiliser les fournisseurs de ressources SQL et MySQL que vous avez déployés précédemment.  Nous vous recommandons de mettre à jour SQL et MySQL lorsqu’une nouvelle version est disponible. Comme Azure Stack, appliquez de façon séquentielle les mises à jour aux fournisseurs de ressources SQL et MySQL.  Par exemple, si vous utilisez la version 1710, commencez par appliquer la version 1711, puis la 1712 et ensuite mettez à jour vers la 1802.      
>   
> L’installation de la mise à jour 1802 n’affecte pas l’utilisation actuelle des fournisseurs de ressources SQL ou MySQL par vos utilisateurs.
> Quelle que soit la version des fournisseurs de ressources que vous utilisez, vos données utilisateur dans leurs bases de données restent intactes et accessibles.    


#### <a name="app-service"></a>App Service
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.

<!--
#### Identity
-->

#### <a name="downloading-azure-stack-tools-from-github"></a>Téléchargement des outils Azure Stack à partir de GitHub
- Quand vous utilisez l’applet de commande PowerShell *invoke-webrequest* pour télécharger les outils Azure Stack à partir de Github, une erreur s’affiche :     
    -  *invoke-webrequest : La demande a été abandonnée : Impossible de créer un canal sécurisé SSL/TLS.*     

  Cette erreur se produit en raison d’une désapprobation récente de la prise en charge GitHub des normes de chiffrement Tlsv1 et Tlsv1.1 (valeur par défaut pour PowerShell). Pour plus d’informations, consultez [Avis de suppression des normes de chiffrement faible](https://githubengineering.com/crypto-removal-notice/).

  Pour résoudre ce problème, ajoutez `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` au début du script pour forcer la console PowerShell à utiliser TLSv1.2 lors du téléchargement à partir de dépôts GitHub.


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger le package de mise à jour Azure Stack 1802 à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Plus d’informations
Microsoft fournit un moyen de surveiller et de reprendre les mises à jour à l’aide du point de terminaison privilégié (PEP) installé avec la mise à jour 1710.

- Consultez la documentation [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Voir aussi

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
