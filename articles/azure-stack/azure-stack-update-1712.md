---
title: "Mise à jour 1712 d’Azure Stack | Microsoft Docs"
description: "Découvrez le contenu de la mise à jour 1712 pour les systèmes intégrés Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: b14f79ad-025f-45d8-9e1d-e53d2b420bb1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: brenduns
ms.openlocfilehash: 0456a202990d383370051d99112f829533b1b101
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="azure-stack-1712-update"></a>Mise à jour 1712 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans cette mise à jour, les problèmes connus dans cette publication, et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour, et problèmes propres à la build (après l’installation).

> [!IMPORTANT]
> Cette mise à jour est destinée uniquement aux systèmes intégrés Azure Stack. N’appliquez pas cette mise à jour au Kit de développement d’Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de mise à jour d’Azure Stack 1712 est **180106.1**. Si un client avait déployé la mise à jour **180103.2**, vous n’avez pas besoin d’appliquer la mise à jour **180106.1**.

## <a name="before-you-begin"></a>Avant de commencer

> [!IMPORTANT]
> Ne tentez pas de créer des machines virtuelles pendant l’installation de la mise à jour 1712. Voir [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#plan-for-updates) pour plus de détails.

### <a name="prerequisites"></a>Prérequis

Vous devez installer la [mise à jour 1711](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1711) d’Azure Stack avant d’appliquer cette mise à jour.

### <a name="post-update-steps"></a>Étapes après la mise à jour

Cette mise à jour nécessite également l’installation des mises à jour du microprogramme du partenaire OEM après avoir terminé l’installation de la mise à jour 1712 d’Azure Stack.

> [!NOTE]
> Consultez le site web de votre partenaire OEM pour télécharger les mises à jour.

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Cette mise à jour inclut les améliorations et les correctifs suivants pour Azure Stack.

#### <a name="new-features"></a>Nouvelles fonctionnalités

- Applet de commande Test-AzureStack pour valider la disponibilité Cloud de Azure Stack via le point de terminaison privilégié
- Capacité d’enregistrer un déploiement déconnecté de Azure Stack
- Surveillance des alertes d’expiration des certificats et des comptes utilisateur
- Applet de commande Set-BmcPassword ajouté dans le PEP pour la rotation de mot de passe du BMC
- Mises à jour de la journalisation du réseau pour prendre en charge la journalisation à la demande
- Prise en charge de l’opération de réinitialisation pour Virtual Machine Scale Sets (VMSS)
- Activation du mode plein écran sur une machine virtuelle ERCS pour la connexion de CloudAdmin
- Les locataires peuvent maintenant activer des machines virtuelles Windows automatiquement

#### <a name="fixes"></a>Correctifs

- Correctif pour afficher l’état opérationnel du nœud en maintenance lors de l’exécution d’une réparation
- Correctif pour l’horodatage de date/heure des enregistrements d’utilisation de l’adresse IP publique
- Autres correctifs divers liés aux performances, à la sécurité et à la stabilité
- Résolution des bogues des modules de point de terminaison privilégié TimeSource et Defender

#### <a name="windows-server-2016-new-features-and-fixes"></a>Correctifs et nouvelles fonctionnalités liés à Windows Server 2016

- [3 janvier 2018—KB4056890 (Version du système d’exploitation 14393.2007)](https://support.microsoft.com/help/4056890/windows-10-update-kb4056890)
    - Cette mise à jour inclut des correctifs logiciels pour le problème de sécurité de l’industrie décrit par [Avis de sécurité ADV 180002 du MSRC ](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002).

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

Cette section énumère des problèmes connus que vous pouvez rencontrer lors de l’installation de la mise à jour 1712.

1. **Symptôme :**Les opérateurs Azure Stack peuvent recevoir l’erreur suivante lors du processus de mise à jour :*"Type 'CheckHealth' of Role 'VirtualMachines' raised an exception:\n\nVirtual Machine health check for -ACS01 produced the following errors.\nThere was an error getting VM information from hosts. Exception details:\nGet-VM : The operation on computer 'Node03' failed: The WS-Management service cannot process the request. The WMI \nservice or the WMI provider returned an unknown error: HRESULT 0x8004106c."*
    1. **Cause :** Ce problème est dû à un problème dans Windows Server qui sera résolu dans les mises à jour ultérieures de Windows server.
    2. **Résolution :** Pour obtenir de l’aide, contactez le Support technique et Service clientèle (CSS) Microsoft.
<br><br>
2. **Symptôme :** Les opérateurs Azure Stack peuvent recevoir l’erreur suivante lors du processus de mise à jour :*"Enabling the seed ring VM failed on node Host-Node03 with an error: [Host-Node03] Connecting to remote server Host-Node03 failed with the following error message : The WinRM client received an HTTP server error status (500), but the remote service did not include any other information about the cause of the failure."*
    1. **Cause :** Ce problème est dû à un problème dans Windows Server qui sera résolu dans les mises à jour ultérieures de Windows server. 
    2. **Résolution :** Pour obtenir de l’aide, contactez le Support technique et Service clientèle (CSS) Microsoft.
<br><br>

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Cette section énumère des problèmes connus après l’installation de la build **180106.1**.

#### <a name="portal"></a>Portail

- Il se peut qu’il ne soit pas possible d’afficher les ressources de calcul ou de stockage sur le portail d’administration. Cela indique qu’une erreur s’est produite pendant l’installation de la mise à jour, et que celle-ci n’a pas été correctement signalée comme réussie. Si ce problème se produit, contactez le Support technique et Service clientèle Microsoft pour obtenir une assistance.
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.
- Le bouton **Déplacer** est désactivé lorsque vous affichez les propriétés d’un groupe de ressources. Il s’agit du comportement attendu. Le déplacement de groupes de ressources entre des abonnements n’est pas pris en charge actuellement.
- Pour tout workflow dans lequel vous sélectionnez un abonnement, un groupe de ressources ou un emplacement dans une liste déroulante, vous pouvez rencontrer un ou plusieurs des problèmes suivants :

   - Vous pouvez voir une ligne vide en haut de la liste. Vous devriez toujours être en mesure de sélectionner un élément comme prévu.
   - Si la liste déroulante des éléments est courte, il se peut que vous ne puissiez afficher aucun nom d’élément.
   - Si vous avez plusieurs abonnements utilisateur, la liste déroulante des groupes de ressources peut être vide.

        > [!NOTE]
        > Pour contourner les deux derniers problèmes, vous pouvez taper le nom de l’abonnement ou du groupe de ressources (si vous les connaissez), ou utiliser PowerShell à la place.

- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.
- Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack. Pour contourner ce problème, vous pouvez vérifier les autorisations à l’aide de PowerShell.

#### <a name="health-and-monitoring"></a>Intégrité et surveillance

- Si vous redémarrez une instance de rôle d’infrastructure, il se peut que vous receviez un message indiquant que le redémarrage a échoué. Pour tant, en réalité, le redémarrage a réussi.

#### <a name="marketplace"></a>Marketplace
- Certains éléments du marketplace sont supprimés dans cette version pour des raisons de compatibilité. Ils seront réactivés après une validation supplémentaire.
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.

#### <a name="compute"></a>Calcul
- Les utilisateurs ont la possibilité de créer une machine virtuelle avec stockage géoredondant. Cette configuration fait échouer la création.
- Vous pouvez configurer un groupe à haute disponibilité de machines virtuelles uniquement avec un domaine d’erreur et un domaine de mise à jour, tous deux de valeur égale à un.
- Il n’existe aucune expérience de Place de marché pour créer des groupes de machines virtuelles identiques. Vous pouvez créer un groupe identique à l’aide d’un modèle.
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

#### <a name="networking"></a>Mise en réseau
- Vous ne pouvez pas créer un équilibreur de charge avec une adresse IP publique à l’aide du portail. Pour résoudre ce problème, vous pouvez utiliser PowerShell pour créer l’équilibreur de charge.
- Lorsque vous créez un équilibreur de charge réseau, vous devez créer une règle de traduction d’adresses réseau (NAT). À défaut, une erreur s’affiche lorsque vous tentez d’ajouter une règle NAT après avoir créé l’équilibreur de charge.
- Vous ne peut pas dissocier une adresse IP publique d’une machine virtuelle (VM) une fois que la VM a été créée et associée à cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été affectée reste associée à la machine virtuelle d’origine. Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle. Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour la création de nouvelles machines virtuelles.
- Les opérateurs Azure Stack peuvent être dans l’impossibilité de déployer, supprimer ou modifier des réseaux virtuels ou des groupes de sécurité réseau. Ce problème se produit principalement lors des tentatives de mise à jour ultérieures du même package. Il est dû à un problème d’empaquetage avec une mise à jour, que nous étudions actuellement.
- L’équilibrage de charge interne gère mal les adresses MAC des machines virtuelles principales, ce qui arrête les instances Linux.

#### <a name="sqlmysql"></a>SQL/MySQL
- Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL.
- La création d’éléments directement sur des serveurs d’hébergement SQL et MySQL qui n’est pas effectuée par le fournisseur de ressources n’est pas prise en charge, et peut aboutir à un état non compatible.

    > [!NOTE]
    > Vos utilisateurs existants de fournisseur de ressources SQL ou MySQL ne doivent pas être impactés lors de la mise à jour de vos systèmes intégrés Azure Stack vers la version 1712. Vous pouvez continuer d’utiliser vos builds actuels de fournisseur de ressources SQL ou MySQL jusqu'à ce qu’une mise à jour prochaine de Azure Stack.

#### <a name="app-service"></a>App Service
- Un utilisateur doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.

#### <a name="identity"></a>Identité

Dans les environnements déployés des services de fédération Azure Active Directory (AD FS), le compte **azurestack\azurestackadmin** n’est plus le propriétaire de l’abonnement Fournisseur par défaut. Au lieu de vous connecter au **portail d’administration / point de terminaison adminmanagement** avec le compte **azurestack\azurestackadmin**, vous pouvez utiliser le compte **azurestack\cloudadmin**, afin de pouvoir gérer et utiliser l’abonnement Fournisseur par défaut.

> [!IMPORTANT]
> Même si le compte **azurestack\cloudadmin** est propriétaire de l’abonnement Fournisseur par défaut dans les environnements AD FS déployés, il ne dispose pas des autorisations nécessaires pour établir une connexion RDP avec l’hôte. Continuez à utiliser le compte **azurestack\azurestackadmin** ou le compte d’administrateur local pour vous connecter, accéder à l’hôte et le gérer en fonction des besoins.

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger le package de mise à jour Azure Stack 1712 à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Plus d’informations

Microsoft fournit un moyen de surveiller et de reprendre les mises à jour à l’aide du point de terminaison privilégié (PEP) installé avec la mise à jour 1712.

- Consultez la documentation [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Voir aussi

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
