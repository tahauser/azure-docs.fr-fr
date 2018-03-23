---
title: "Mise à jour 1710 d’Azure Stack (Build 20171020.1) | Microsoft Docs"
description: "Apprenez-en davantage sur le contenu de la mise à jour 1710 pour les systèmes intégrés, les problèmes connus, et l’emplacement à partir duquel la télécharger."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 1a482f1d2f3eef8775bb7b64d4f6749f69fa5471
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="azure-stack-1710-update-build-201710201"></a>Mise à jour 1710 d’Azure Stack (Build 20171020.1)

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans cette mise à jour, les problèmes connus dans cette publication, et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour, et problèmes propres à la build (après l’installation).

> [!IMPORTANT]
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="improvements-and-fixes"></a>Améliorations et correctifs

Cette mise à jour inclut les améliorations de la qualité et les correctifs suivants pour Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Améliorations et correctifs de Windows Server 2016

- Mises à jour pour Windows Server 2016 : 10 octobre 2017, KB4041691 (version du système d’exploitation 14393.1770). Pour plus d’informations, voir [https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691).

### <a name="additional-quality-improvements-and-fixes"></a>Correctifs et améliorations de la qualité supplémentaires

- Ajout d’applets de commande PowerShell de point de terminaison privilégié pour aider à dépanner et à mettre à jour le serveur NTP (Network Time Protocol).
- Ajout de la prise en charge des modules de points de terminaison d’administration suffisante de point de terminaison privilégié et de la liste verte d’applets de commande. 
- Correction des erreurs de langue locale dans le point de terminaison privilégié.
- Ajout de la possibilité d’opérer une rotation des informations d’identification de la passerelle .
- Suppression du compte d’administrateur local CBLocalAdmin. 
- Correction du contenu du modèle d’alerte d’interrogation pour s’assurer que les alertes d’interrogation fonctionnent correctement après une mise à jour.
- Correction du fournisseur de ressources de structure pour gérer les délais d’attente durant des opérations FRU (Field Replaceable Unit). 
- Ajout de la possibilité pour les développeurs cloud d’utiliser des profils d’API Azure Resource Manager sur Azure Stack.
- Désactivation du service Windows Update sur la machine virtuelle de déploiement. 
- Suppression des actions d’activation/désactivation de nœud à partir de l’interface utilisateur.
- Corrections de divers autres problèmes de performances et de stabilité. 
 
## <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

Cette section énumère des problèmes connus que vous pouvez rencontrer lors de l’installation de la mise à jour 1710.

> [!IMPORTANT]
> Si la mise à jour échoue, quand vous essayez par la suite de la reprendre, vous devez utiliser l’applet de commande `Resume-AzureStackUpdate` à partir du point de terminaison privilégié. Ne reprenez pas la mise à jour en utilisant le portail administrateur. (Il s’agit d’un problème connu dans cette version.) Pour plus d’informations, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).

| Symptôme  | Cause :  | Résolution : |
|---------|---------|---------|
|Lorsque vous effectuez une mise à jour, une erreur similaire à la suivante<br>peut se produire lors de l’étape « Storage Hosts Restart Storage Node »<br> du plan d’action de mise à jour.<br><br>**{"name":"Restart Storage Hosts","description":"Restart<br> Storage Hosts.","errorMessage":"Type 'Restart' of Role<br> 'BareMetal' raised an exception:\n\n The computer<br> HostName-05 is skipped. Fail to retrieve its LastBootUpTime<br> via the WMI service with the following error message:<br> The RPC server is unavailable.<br> (Exception from HRESULT: 0x800706BA).\nat Restart-Host** | Ce problème est dû à la présence d’un pilote potentiellement défectueux dans certaines configurations. | 1. Connectez-vous à l’interface web du contrôleur de gestion de la carte de base (BMC), puis redémarrez l’ordinateur hôte identifié dans le message d’erreur.<br><br>2. Reprenez la mise à jour en utilisant le point de terminaison privilégié. |
| Lorsque vous effectuez une mise à jour, le processus de mise à jour semble stagner<br> et cesser de progresser après l’étape « Step: Running step 2.4 - Install<br> update » du plan d’action de mise à jour.<br><br>Cette étape est ensuite suivie par une série de processus de copie de fichiers .nupkg<br> vers des partages de fichiers de l’infrastructure interne. Par exemple : <br><br>**Copying 1 files from content\PerfCollector\VirtualMachines to <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Ou bien, le message suivant s’affiche :<br><br>**WarningMessage:Task: Invocation of interface ’LiveUpdate’<br> of role ’Cloud\Fabric\VirtualMachines’ failed:<br> Type ’LiveUpdate’ of Role ’VirtualMachines’ raised an<br> exception: There is not enough space on the disk.**  | Ce problème est dû au fait que des fichiers journaux saturent les disques d’une machine virtuelle d’infrastructure, et à un dysfonctionnement du serveur de fichiers avec montée en puissance parallèle (SOFS) de Windows Server qui sera corrigé dans une prochaine mise à jour. | Pour obtenir de l’aide, contactez le Support technique et Service clientèle (CSS) Microsoft. | 
| Lorsque vous effectuez une mise à jour, une erreur similaire à la suivante<br> peut se produire lors de l’étape « Step: Running step 2.13.2 - Update<br> *VM_Name* » du plan d’action de mise à jour. (Le nom de la machine virtuelle<br> peut varier.)<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: Invocation of interface 'LiveUpdate' of<br> role 'Cloud\Fabric\WAS'failed: Type 'LiveUpdate' of Role<br> 'WAS' raised an exception: ERROR during storage<br> initialization: An error occurred while trying to make an API<br> call to Microsoft Storage service: {"Message": "A timeout<br> occurred while communicating with Service Fabric.<br> Exception Type: TimeoutException.<br> Exception message: Operation timed out."}**  | Ce problème est dû à un délai d’attente d’E/S dans Windows Server, qui sera résolu dans une mise à jour ultérieure. | Pour obtenir une assistance, contactez le Support technique et Service clientèle Microsoft.
| Lorsque vous effectuez une mise à jour, une erreur similaire à la suivante<br> peut se produire au cours de l’étape « Step 21 Restart SQL server VMs. »<br><br>**Type 'LiveUpdateRestart' of Role 'VirtualMachines' raised an<br> exception: VerboseMessage:[VirtualMachines:LiveUpdateRestart]<br> Querying for VM MachineName-Sql01. - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart]<br> VM is marked as HighlyAvailable. - 10/13/2017 5:11:50 PM<br> VerboseMessage:[VirtualMachines:LiveUpdateRestart] at<br>MS.Internal.ServerClusters.ExceptionHelp.Build at<br>MS.Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(Boolean force) at Microsoft.FailoverClusters.PowerShell.<br>StopClusterResourceCommand.BeginTimedOperation() at <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() at  Microsoft.FailoverClusters.PowerShell.<br>FCCmdlet.ProcessRecord() - 10/13/2017 5:11:50 PM Warning<br>Message: Task: Invocation of interface 'LiveUpdateRestart' of<br> role 'Cloud\Fabric\VirtualMachines' failed:** | Ce problème peut se produire si la machine virtuelle n’a pas pu redémarrer. | Pour obtenir une assistance, contactez le Support technique et Service clientèle Microsoft.
| Lorsque vous effectuez une mise à jour, une erreur similaire à la suivante peut se produire :<br><br>**2017-10-22T01:37:37.5369944Z Type 'Shutdown' of Role 'SQL'<br> raised an exception: An error occurred pausing node<br> 's45r1004-Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 at<br> Shutdown,C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>Applications \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br>Deployment\Classes\SQL\SQL.psm1: line 50 at <ScriptBlock&#62;,<br> <No file>: line 18 at <ScriptBlock&#62;, <No file&#62;: line 16** | Ce problème peut se produire si la machine virtuelle ne peut pas être mise en état suspendu afin de drainer les rôles. | Pour obtenir une assistance, contactez le Support technique et Service clientèle Microsoft.
| Lorsque vous effectuez une mise à jour, l’une des erreurs suivantes peut se produire :<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation<br> for ADFS/Graph role failed with error: Error checking ADFS<br> probe endpoint *endpoint_URI*: Exception calling<br> "GetResponse" with "0" argument(s): "The remote server<br> returned an error: (503) Server Unavailable." at Invoke-<br>ADFSGraphValidation**<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation<br> for ADFS/Graph role failed with error: Error fetching<br> ADFS properties: Could not connect to <br>net.tcp://localhost:1500/policy. The connection attempt lasted<br> for a time span of 00:00:02.0498923. TCP error code<br> 10061: No connection could be made because the target<br> machine actively refused it 127.0.0.1:1500.<br> at Invoke-ADFSGraphValidation** | Le plan d’action de mise à jour ne peut pas valider l’intégrité des services de fédération Active Directory (AD FS). | Pour obtenir une assistance, contactez le Support technique et Service clientèle Microsoft.

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Cette section énumère des problèmes connus après l’installation de la build 20171020.1.

### <a name="portal"></a>Portail

- Il se peut qu’il ne soit pas possible d’afficher les ressources de calcul ou de stockage sur le portail d’administration. Cela indique qu’une erreur s’est produite pendant l’installation de la mise à jour, et que celle-ci n’a pas été correctement signalée comme réussie. Si ce problème se produit, contactez le Support technique et Service clientèle Microsoft pour obtenir une assistance.
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.
- Le bouton **Déplacer** est désactivé lorsque vous affichez les propriétés d’un groupe de ressources. Il s’agit du comportement attendu. Le déplacement de groupes de ressources entre des abonnements n’est pas pris en charge actuellement.
- Pour tout workflow dans lequel vous sélectionnez un abonnement, un groupe de ressources ou un emplacement dans une liste déroulante, vous pouvez rencontrer un ou plusieurs des problèmes suivants :

   - Vous pouvez voir une ligne vide en haut de la liste. Vous devriez toujours être en mesure de sélectionner un élément comme prévu.
   - Si la liste déroulante des éléments est courte, il se peut que vous ne puissiez afficher aucun nom d’élément.
   - Si vous avez plusieurs abonnements utilisateur, la liste déroulante des groupes de ressources peut être vide. 

   Pour contourner les deux derniers problèmes, vous pouvez taper le nom de l’abonnement ou du groupe de ressources (si vous les connaissez), ou utiliser PowerShell à la place.
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.
- Vous n’avez pas la possibilité d’afficher les autorisations de votre abonnement sur les portails Azure Stack. Pour contourner ce problème, vous pouvez vérifier les autorisations à l’aide de PowerShell.
  
### <a name="backup"></a>Sauvegarde

- N’activez pas la **sauvegarde de l’infrastructure** sur le panneau dédié à celle-ci.

### <a name="health-and-monitoring"></a>Intégrité et surveillance

- Si vous redémarrez une instance de rôle d’infrastructure, il se peut que vous receviez un message indiquant que le redémarrage a échoué. Pour tant, en réalité, le redémarrage a réussi.

### <a name="marketplace"></a>Marketplace
- Lorsque vous tentez d’ajouter des éléments à la Place de marché d’Azure Stack à l’aide de l’option **Ajouter à partir d’Azure**, certains éléments disponibles en téléchargement peuvent ne pas être visibles.
- Les utilisateurs ont la possibilité de parcourir entièrement la Place de marché, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.

### <a name="compute"></a>Calcul
- Les utilisateurs ont la possibilité de créer une machine virtuelle avec stockage géoredondant. Cette configuration fait échouer la création.
- Vous pouvez configurer un groupe à haute disponibilité de machines virtuelles uniquement avec un domaine d’erreur et un domaine de mise à jour, tous deux de valeur égale à un.
- Il n’existe aucune expérience de Place de marché pour créer des groupes de machines virtuelles identiques. Vous pouvez créer un groupe identique à l’aide d’un modèle.
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.
 
### <a name="networking"></a>Mise en réseau
- Vous ne pouvez pas créer un équilibreur de charge avec une adresse IP publique à l’aide du portail. Pour résoudre ce problème, vous pouvez utiliser PowerShell pour créer l’équilibreur de charge.
- Lorsque vous créez un équilibreur de charge réseau, vous devez créer une règle de traduction d’adresses réseau (NAT). À défaut, une erreur s’affiche lorsque vous tentez d’ajouter une règle NAT après avoir créé l’équilibreur de charge.
- Vous ne peut pas dissocier une adresse IP publique d’une machine virtuelle (VM) une fois que la VM a été créée et associée à cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été affectée reste associée à la machine virtuelle d’origine. Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle. Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour la création de nouvelles machines virtuelles.
 
### <a name="sqlmysql"></a>SQL/MySQL
- Il faut parfois attendre une heure pour qu’ils puissent créer des bases de données avec une nouvelle référence SQL ou MySQL. 
- La création d’éléments directement sur des serveurs d’hébergement SQL et MySQL qui n’est pas effectuée par le fournisseur de ressources n’est pas prise en charge, et peut aboutir à un état non compatible.
 
### <a name="app-service"></a>App Service
- Un utilisateur doit inscrire le fournisseur de ressources de stockage avant de créer sa première fonction Azure dans l’abonnement.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Procédures de FRU (Field Replaceable Unit)

- Lors de l’exécution de la mise à jour, les images hors connexion ne sont pas corrigées. Si vous avez besoin de remplacer un nœud d’unité d’échelle, vérifiez avec votre fournisseur de matériel OEM que le nœud remplacé dispose du dernier niveau de correctif.

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1710 [ici](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>étapes suivantes

- Pour une vue d’ensemble de la gestion des mises à jour dans Azure Stack, voir [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer les mises à jour, voir [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
