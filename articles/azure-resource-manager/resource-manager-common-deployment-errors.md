---
title: "Résolution des erreurs courantes lors du déploiement Azure | Microsoft Docs"
description: "Décrit comment résoudre les erreurs courantes lors du déploiement de ressources sur Azure à l’aide d’Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "erreur de déploiement, déploiement Azure, déployer dans azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: tomfitz
ms.openlocfilehash: ca7e3cb541948e6cc0b8d077616f3611e3ab2477
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager

Cette article décrit certaines erreurs courantes liées au déploiement Azure que vous pouvez rencontrer et fournit des informations pour les résoudre. Si vous ne trouvez pas le code d’erreur correspondant à l’erreur de votre déploiement, consultez [Rechercher un code d’erreur](#find-error-code).

## <a name="error-codes"></a>Codes d’erreur

| Code d'erreur | Atténuation | Plus d’informations |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Suivez les restrictions concernant l’attribution de noms pour les comptes de stockage. | [Résoudre les erreurs de nom du compte de stockage](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Vérifiez les propriétés disponibles du compte de stockage. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Le cluster ou la région n’a pas de ressources disponibles, ou ne prend pas en charge la taille de machine virtuelle demandée. Renouvelez la demande plus tard ou demandez une taille de machine virtuelle différente. | [Problèmes de provisionnement et d’allocation pour Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md) et [Problèmes de provisionnement et d’allocation pour Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| AnotherOperationInProgress | Attendez que l’opération simultanée soit terminée. | |
| AuthorizationFailed | Votre compte ou principal du service ne dispose pas de droits d’accès suffisants pour terminer le déploiement. Vérifiez le rôle auquel votre compte appartient et son accès dans le cadre du déploiement. | [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md) |
| BadRequest | Vous avez envoyé des valeurs de déploiement qui ne correspondent pas aux valeurs attendues par Resource Manager. Vérifiez le message d’état interne pour résoudre plus facilement le problème. | [Référence de modèle](/azure/templates/) et [Emplacements pris en charge](resource-manager-templates-resources.md#location) |
| Conflit | Vous demandez une opération qui n’est pas autorisée dans l’état actuel de la ressource. Par exemple, un redimensionnement de disque est autorisé uniquement durant la création ou la libération d’une machine virtuelle. | |
| DeploymentActive | Attendez le déploiement simultané sur ce groupe de ressources soit terminé. | |
| DnsRecordInUse | Le nom de l’enregistrement DNS doit être unique. Indiquez un autre nom ou modifiez l’enregistrement existant. | |
| ImageNotFound | Vérifiez les paramètres d’image de machine virtuelle. |  |
| InUseSubnetCannotBeDeleted | Vous pouvez rencontrer cette erreur quand vous tentez de mettre à jour une ressource, mais que la requête est traitée en supprimant et en créant la ressource. Veillez à spécifier toutes les valeurs non modifiées. | [Mettre à jour une ressource](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Procurez-vous le jeton d’accès pour le client approprié. Vous pouvez uniquement obtenir le jeton auprès du client auquel appartient votre compte. | |
| InvalidContentLink | Vous avez probablement tenté d’établir une liaison avec un modèle imbriqué qui n’est pas disponible. Vérifiez l’URI que vous avez indiqué pour le modèle imbriqué. Si le modèle existe dans un compte de stockage, assurez-vous que l’URI est accessible. Vous devrez peut-être valider un jeton SAS. | [Modèles liés](resource-group-linked-templates.md) |
| InvalidParameter | L’une des valeurs que vous avez fournies pour une ressource ne correspond pas à la valeur attendue. Cette erreur peut être due à de nombreuses conditions différentes. Par exemple, il se peut qu’un mot de passe soit insuffisant ou un nom d’objet blob incorrect. Vérifiez le message d’erreur pour déterminer la valeur à corriger. | |
| InvalidRequestContent | Vos valeurs de déploiement contiennent des valeurs inattendues ou n’incluent pas les valeurs requises. Vérifiez les valeurs pour votre type de ressource. | [Référence de modèle](/azure/templates/) |
| InvalidRequestFormat | Activez l’enregistrement du débogage durant l’exécution du déploiement et vérifiez le contenu de la requête. | [Activer l’enregistrement du débogage](resource-manager-troubleshoot-tips.md#enable-debug-logging) |
| InvalidResourceNamespace | Vérifiez l’espace de noms de ressources que vous avez spécifié dans la propriété **type**. | [Référence de modèle](/azure/templates/) |
| InvalidResourceReference | La ressource n’existe pas encore ou n’est pas correctement référencée. Vérifiez si vous devez ajouter une dépendance. Vérifiez que votre utilisation de la fonction **reference** inclut les paramètres requis pour votre scénario. | [Résoudre les erreurs de dépendance](resource-manager-not-found-errors.md) |
| InvalidResourceType | Vérifiez le type de ressource que vous avez spécifié dans la propriété **type**. | [Référence de modèle](/azure/templates/) |
| InvalidTemplate | Vérifiez que la syntaxe de votre modèle ne contient pas d’erreurs. | [Résoudre les erreurs de modèle non valide](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | Vérifiez si votre compte appartient au même client que le groupe de ressources que vous déployez. | |
| LinkedInvalidPropertyId | L’ID de ressource pour une ressource particulière n’est pas correctement résolu. Assurez-vous de bien fournir toutes les valeurs requises pour l’ID de ressource, notamment l’ID d’abonnement, le nom du groupe de ressources, le type de ressource, le nom de la ressource parente (si nécessaire) et le nom de la ressource. | |
| LocationRequired | Indiquez un emplacement pour votre ressource. | [Définir un emplacement](resource-manager-templates-resources.md#location) |
| MissingRegistrationForLocation | Vérifiez l’état d’inscription du fournisseur de ressources ainsi que les emplacements pris en charge. | [Résoudre les erreurs d’inscription](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Inscrivez votre abonnement auprès du fournisseur de ressources. | [Résoudre les erreurs d’inscription](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Vérifier l’état d’inscription du fournisseur de ressources. | [Résoudre les erreurs d’inscription](resource-manager-register-provider-errors.md) |
| NotFound | Vous essayez peut-être de déployer une ressource dépendante en parallèle avec une ressource parente. Vérifiez si vous avez besoin d’ajouter une dépendance. | [Résoudre les erreurs de dépendance](resource-manager-not-found-errors.md) |
| OperationNotAllowed | Le déploiement tente une opération qui dépasse le quota autorisé pour l’abonnement, le groupe de ressources ou la région. Si possible, modifiez votre déploiement pour respecter les quotas. Dans le cas contraire, vous pouvez demander une modification de vos quotas. | [Résoudre les erreurs de quota](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Assurez-vous qu’il existe une ressource parente avant de créer des ressources enfants. | [Résoudre les erreurs de ressource parente](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | L’adresse IP spécifiée inclut une plage d’adresses requise par Azure. Modifiez l’adresse IP pour éviter d’utiliser la plage réservée. | [Adresses IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | L’adresse IP spécifiée se trouve en dehors de la plage de sous-réseau. Modifiez l’adresse IP pour qu’elle se trouve dans la plage de sous-réseau. | [Adresses IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Certaines propriétés ne peuvent pas être modifiées sur une ressource déployée. Durant la mise à jour d’une ressource, limitez vos modifications aux propriétés autorisées. | [Mettre à jour une ressource](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | Votre abonnement inclut une stratégie de ressource qui empêche une action que vous tentez d’exécuter au cours du déploiement. Recherchez la stratégie qui bloque l’action. Si possible, modifiez votre déploiement pour respecter les limitations de la stratégie. | [Résoudre les erreurs de stratégie](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Spécifiez un nom de ressource qui n’inclut pas de nom réservé. | [Noms de ressource réservés](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Attendez que la suppression soit terminée. | |
| ResourceGroupNotFound | Vérifiez le nom du groupe de ressources cible pour le déploiement. Il doit déjà exister dans votre abonnement. Vérifiez le contexte de votre abonnement. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | Votre déploiement fait référence à une ressource qui ne peut pas être résolue. Vérifiez que votre utilisation de la fonction **reference** inclut les paramètres requis pour votre scénario. | [Résoudre les erreurs de référence](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | Le déploiement tente de créer des ressources qui dépassent le quota autorisé pour l’abonnement, le groupe de ressources ou la région. Si possible, modifiez votre infrastructure pour respecter les quotas. Dans le cas contraire, vous pouvez demander une modification de vos quotas. | [Résoudre les erreurs de quota](resource-manager-quota-errors.md) |
| SkuNotAvailable | Sélectionnez la référence SKU (par exemple, la taille de la machine virtuelle) disponible pour l’emplacement que vous avez sélectionné. | [Résoudre les erreurs de référence SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Attribuez un nom unique au compte de stockage. | [Résoudre les erreurs de nom du compte de stockage](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Attribuez un nom unique au compte de stockage. | [Résoudre les erreurs de nom du compte de stockage](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Vérifiez l’abonnement, le groupe de ressources et le nom du compte de stockage que vous tentez d’utiliser. | |
| SubnetsNotInSameVnet | Une machine virtuelle ne peut avoir qu’un seul réseau virtuel. Si vous déployez plusieurs cartes réseau, assurez-vous qu’elles appartiennent au même réseau virtuel. | [Cartes réseau multiples](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>Recherche un code d'erreur

Si vous rencontrez une erreur au cours du déploiement, Resource Manager retourne un code d’erreur. Vous pouvez afficher le message d’erreur via le portail, PowerShell ou Azure CLI. Il se peut que le message d’erreur externe soit trop général pour aider à résoudre le problème. Recherchez le message interne qui contient des informations détaillées sur l’erreur. Pour plus d’informations, consultez [Déterminer le code d’erreur](resource-manager-troubleshoot-tips.md#determine-error-code).

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).
* Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](resource-manager-deployment-operations.md).
