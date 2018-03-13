---
title: "Mise à jour 1711 d’Azure Stack | Microsoft Docs"
description: "Découvrez le contenu de la mise à jour 1711 pour les systèmes intégrés Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 2b66fe05-3655-4f1a-9b30-81bd64ba0013
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: brenduns
ms.openlocfilehash: 3b3f6d66d8d5a095ff839195ccf718a9fa085527
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-1711-update"></a>Mise à jour 1711 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans cette mise à jour, les problèmes connus dans cette publication, et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour, et problèmes propres à la build (après l’installation).

> [!IMPORTANT]
> Cette mise à jour est destinée uniquement aux systèmes intégrés Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de mise à jour d’Azure Stack 1711 est **171201.3**.

## <a name="before-you-begin"></a>Avant de commencer

### <a name="prerequisites"></a>Prérequis

Vous devez installer la [mise à jour 1710](https://docs.microsoft.com/azure/azure-stack/azure-stack-update-1710) d’Azure Stack avant d’appliquer cette mise à jour.

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Cette mise à jour inclut les améliorations et les correctifs suivants pour Azure Stack.

#### <a name="new-features"></a>Nouvelles fonctionnalités

- Prise en charge de la syndication des modèles de solution
- Mises à jour dans la journalisation Azure Stack Graph et la gestion des erreurs
- Possibilité d’activer et de désactiver les hôtes
- Les utilisateurs peuvent maintenant activer automatiquement des machines virtuelles Windows
- Ajout de l’applet de commande PowerShell de point de terminaison privilégié pour récupérer des clés de récupération BitLocker à des fins de rétention
- Prise en charge de la mise à jour des images hors connexion lors de la mise à jour de l’infrastructure
- Activation de la sauvegarde de l’infrastructure avec le service Backup activé

#### <a name="fixes"></a>Correctifs

- Correction d’une condition de concurrence dans le système DNS pendant FRU (Field Replaceable Unit) et mise à jour de la journalisation de cluster
- Correctif lié à la capacité de redémarrage de disable-host dans FRU
- Autres correctifs divers liés aux performances, à la sécurité et à la stabilité

#### <a name="windows-server-2016-new-features-and-fixes"></a>Correctifs et nouvelles fonctionnalités liés à Windows Server 2016

- [14 novembre 2017 : KB4048953 (version du système d’exploitation 14393.1884)](https://support.microsoft.com/help/4048953)

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

Cette section énumère des problèmes connus que vous pouvez rencontrer lors de l’installation de la mise à jour 1711.


1. **Symptôme :** Les opérateurs Azure Stack peuvent recevoir l’erreur suivante lors du processus de mise à jour : *"name:Install Update.", "description": "Install Update on Hosts and Infra VMs.", "errorMessage": "Type 'LiveUpdate' of Role 'VirtualMachines' raised an exception:\n\nThere is not enough space on the disk.\n\nat <ScriptBlock>, <No file>: line22", "status": "Error", "startTimeUtc": "2017-11-10T16:46:59.123Z", "endTimeUtc": "2017-11-10T19:20:29.669Z", "steps": [ ]"*
    2. **Cause :** Ce problème est dû à un manque d’espace disque libre sur une ou plusieurs machines virtuelles qui font partie de l’infrastructure Azure Stack.
    3. **Résolution :** Pour obtenir de l’aide, contactez le Support technique et Service clientèle (CSS) Microsoft.
<br><br>
2. **Symptôme :** Les opérateurs Azure Stack peuvent recevoir l’erreur suivante lors du processus de mise à jour :*Exception calling "ExtractToFile" with "3" argument(s):"The process cannot access the file '<\\<machineName>-ERCS01\C$\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Diagnostics\Microsoft.AzureStack.Common.Tools.Diagnostics.AzureStackDiagnostics.dll>'*
    1. **Cause :** Ce problème se produit lors de la reprise à partir du portail d’une mise à jour qui a été précédemment reprise à l’aide d’un point de terminaison privilégié (PEP).
    2. **Résolution :** Pour obtenir de l’aide, contactez le Support technique et Service clientèle (CSS) Microsoft.
<br><br>
3. **Symptôme :**Les opérateurs Azure Stack peuvent recevoir l’erreur suivante lors du processus de mise à jour :*"Type 'CheckHealth' of Role 'VirtualMachines' raised an exception:\n\nVirtual Machine health check for <machineName>-ACS01 produced the following errors.\nThere was an error getting VM information from hosts. Exception details:\nGet-VM : The operation on computer 'Node03' failed: The WS-Management service cannot process the request. The WMI \nservice or the WMI provider returned an unknown error: HRESULT 0x8004106c".*
    1. **Cause :** Ce problème est dû à un problème dans Windows Server qui sera résolu dans les mises à jour ultérieures de Windows server.
    2. **Résolution :** Pour obtenir de l’aide, contactez le Support technique et Service clientèle (CSS) Microsoft.
<br><br>
4. **Symptôme :**Les opérateurs Azure Stack peuvent recevoir l’erreur suivante lors du processus de mise à jour :*"Type 'DefenderUpdate' of Role 'URP' raised an exception: Failed getting version from \\SU1FileServer\SU1_Public\DefenderUpdates\x64\{file name}.exe after 60 attempts at Copy-AzSDefenderFiles, C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.Defender\Microsoft.AzureStack.Defender.psm1: line 262"*
    1. **Cause :** Ce problème est dû à l’échec du téléchargement en arrière-plan des mises à jour de définitions Windows Defender.
    2. **Résolution :** Essayez de reprendre la mise à jour jusqu’à huit heures après la première tentative de mise à jour.

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Cette section énumère des problèmes connus après l’installation de la build **20171201.3**.

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
- Lorsque vous tentez d’ajouter des éléments à la Place de marché d’Azure Stack à l’aide de l’option **Ajouter à partir d’Azure**, certains éléments disponibles en téléchargement peuvent ne pas être visibles.
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
- L’équilibrage de charge interne gère incorrectement les adresses MAC des machines virtuelles principales, ce qui bloque les instances Linux.

#### <a name="sqlmysql"></a>SQL/MySQL
- Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL.
- La création d’éléments directement sur des serveurs d’hébergement SQL et MySQL qui n’est pas effectuée par le fournisseur de ressources n’est pas prise en charge, et peut aboutir à un état non compatible.

#### <a name="app-service"></a>App Service
- Un utilisateur doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.

#### <a name="identity"></a>Identité

Dans les environnements déployés des services de fédération Azure Active Directory (AD FS), le compte **azurestack\azurestackadmin** n’est plus le propriétaire de l’abonnement Fournisseur par défaut. Au lieu de vous connecter au **portail d’administration / point de terminaison adminmanagement** avec le compte **azurestack\azurestackadmin**, vous pouvez utiliser le compte **azurestack\cloudadmin**, afin de pouvoir gérer et utiliser l’abonnement Fournisseur par défaut.

> [!IMPORTANT]
> Même si le compte **azurestack\cloudadmin** est propriétaire de l’abonnement Fournisseur par défaut dans les environnements AD FS déployés, il ne dispose pas des autorisations nécessaires pour établir une connexion RDP avec l’hôte. Continuez à utiliser le compte **azurestack\azurestackadmin** ou le compte d’administrateur local pour vous connecter, accéder à l’hôte et le gérer en fonction des besoins.

#### <a name="infrastructure-backup-sevice"></a>Service Infrastructure Backup
<!-- 1974890-->

- **Les sauvegardes antérieures à 1711 ne sont pas prises en charge pour la récupération cloud.**  
  Les sauvegardes antérieures à 1711 ne sont pas compatibles avec la récupération cloud. Vous devez d’abord mettre à jour vers 1711 et activer les sauvegardes. Si vous avez activé déjà les sauvegardes, effectuez une sauvegarde après la mise à jour vers 1711. Les sauvegardes antérieures à 1711 doivent être supprimées.

- **L’activation de la sauvegarde d’infrastructure sur ASDK est destinée seulement à des tests.**  
  Les sauvegardes d’infrastructure peuvent être utilisées pour restaurer des solutions à plusieurs nœuds. Vous pouvez activer la sauvegarde d’infrastructure sur ASDK, mais il n’existe aucun moyen de tester la récupération.

Pour plus d’informations, consultez [Sauvegarde et récupération de données pour Azure Stack avec le service Infrastructure Backup](azure-stack-backup-infrastructure-backup.md).

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger le package de mise à jour Azure Stack 1711 à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).

## <a name="more-information"></a>Plus d’informations

Microsoft fournit un moyen de surveiller et de reprendre les mises à jour à l’aide du point de terminaison privilégié (PEP) installé avec la mise à jour 1711.

- Consultez la documentation [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update). 

## <a name="see-also"></a>Voir aussi

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
