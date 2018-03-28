---
title: Résolution des erreurs courantes lors du déploiement Azure | Microsoft Docs
description: Décrit comment résoudre les erreurs courantes lors du déploiement de ressources sur Azure à l’aide d’Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: erreur de déploiement, déploiement Azure, déployer dans azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: f251fe11c43dc4b3f29c70f937f5bfcb6af6c44e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
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
| DeploymentFailed | L’erreur DeploymentFailed est une erreur générale qui ne fournit pas les détails dont vous avez besoin pour résoudre l’erreur. Pour en savoir plus, recherchez un code d’erreur dans les détails de l’erreur. | [Rechercher un code d’erreur](#find-error-code) |
| DeploymentQuotaExceeded | Si vous atteignez la limite des 800 déploiements par groupe de ressources, supprimez les déploiements inutiles dans l’historique. Vous pouvez supprimer des entrées de l’historique avec la commande [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) dans Azure CLI ou la commande [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) dans PowerShell. La suppression d’une entrée à partir de l’historique des déploiements n’affecte pas les ressources de déploiement. | |
| DnsRecordInUse | Le nom de l’enregistrement DNS doit être unique. Indiquez un autre nom ou modifiez l’enregistrement existant. | |
| ImageNotFound | Vérifiez les paramètres d’image de machine virtuelle. |  |
| InUseSubnetCannotBeDeleted | Vous pouvez rencontrer cette erreur quand vous tentez de mettre à jour une ressource, mais que la requête est traitée en supprimant et en créant la ressource. Veillez à spécifier toutes les valeurs non modifiées. | [Mettre à jour une ressource](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Procurez-vous le jeton d’accès pour le client approprié. Vous pouvez uniquement obtenir le jeton auprès du client auquel appartient votre compte. | |
| InvalidContentLink | Vous avez probablement tenté d’établir une liaison avec un modèle imbriqué qui n’est pas disponible. Vérifiez l’URI que vous avez indiqué pour le modèle imbriqué. Si le modèle existe dans un compte de stockage, assurez-vous que l’URI est accessible. Vous devrez peut-être valider un jeton SAS. | [Modèles liés](resource-group-linked-templates.md) |
| InvalidParameter | L’une des valeurs que vous avez fournies pour une ressource ne correspond pas à la valeur attendue. Cette erreur peut être due à de nombreuses conditions différentes. Par exemple, il se peut qu’un mot de passe soit insuffisant ou un nom d’objet blob incorrect. Vérifiez le message d’erreur pour déterminer la valeur à corriger. | |
| InvalidRequestContent | Vos valeurs de déploiement contiennent des valeurs inattendues ou n’incluent pas les valeurs requises. Vérifiez les valeurs pour votre type de ressource. | [Référence de modèle](/azure/templates/) |
| InvalidRequestFormat | Activez l’enregistrement du débogage durant l’exécution du déploiement et vérifiez le contenu de la requête. | [Activer l’enregistrement du débogage](#enable-debug-logging) |
| InvalidResourceNamespace | Vérifiez l’espace de noms de ressources que vous avez spécifié dans la propriété **type**. | [Référence de modèle](/azure/templates/) |
| InvalidResourceReference | La ressource n’existe pas encore ou n’est pas correctement référencée. Vérifiez si vous devez ajouter une dépendance. Vérifiez que votre utilisation de la fonction **reference** inclut les paramètres requis pour votre scénario. | [Résoudre les erreurs de dépendance](resource-manager-not-found-errors.md) |
| InvalidResourceType | Vérifiez le type de ressource que vous avez spécifié dans la propriété **type**. | [Référence de modèle](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Inscrivez votre abonnement auprès du fournisseur de ressources. | [Résoudre les erreurs d’inscription](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Vérifiez que la syntaxe de votre modèle ne contient pas d’erreurs. | [Résoudre les erreurs de modèle non valide](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Supprimez les dépendances inutiles. | [Résoudre les dépendances circulaires](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Vérifiez si votre compte appartient au même client que le groupe de ressources que vous déployez. | |
| LinkedInvalidPropertyId | L’ID de ressource pour une ressource particulière n’est pas correctement résolu. Assurez-vous de bien fournir toutes les valeurs requises pour l’ID de ressource, notamment l’ID d’abonnement, le nom du groupe de ressources, le type de ressource, le nom de la ressource parente (si nécessaire) et le nom de la ressource. | |
| LocationRequired | Indiquez un emplacement pour votre ressource. | [Définir un emplacement](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | Assurez-vous que la ressource imbriquée a un nombre correct de segments dans le nom et le type. | [Résoudre les segments de la ressource](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
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
| TemplateResourceCircularDependency | Supprimez les dépendances inutiles. | [Résoudre les dépendances circulaires](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Réduisez le nombre de groupes de ressources pour un déploiement unique. | [Déploiement de groupes inter-ressources](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Recherche un code d'erreur

Il existe deux types d’erreurs que vous pouvez rencontrer :

* des erreurs de validation
* des erreurs de déploiement

Les erreurs de validation sont liées à des scénarios pouvant être identifiés avant le déploiement. Elles incluent notamment les erreurs de syntaxe dans votre modèle ou le déploiement de ressources entraînant un dépassement des quotas d’abonnement. Les erreurs de déploiement sont liées aux événements se produisant lors du déploiement. Elles incluent la tentative d’accès à une ressource qui est déployée en parallèle.

Les deux types d’erreurs retournent un code d’erreur qui vous permet de résoudre les problèmes liés au déploiement. Les deux types d’erreurs apparaissent dans le [journal d’activité](resource-group-audit.md). Toutefois, les erreurs de validation n’apparaissent pas dans l’historique de votre déploiement, car le déploiement n’a jamais démarré.

### <a name="validation-errors"></a>Erreurs de validation

Lors d’un déploiement via le portail, une erreur de validation s’affiche après l’envoi de vos valeurs.

![afficher l’erreur de validation dans le portail](./media/resource-manager-common-deployment-errors/validation-error.png)

Sélectionnez le message pour obtenir plus d’informations. Dans l’image suivante, vous pouvez voir une erreur **InvalidTemplateDeployment** et un message qui indique qu’une stratégie a bloqué le déploiement.

![afficher les détails de validation](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Erreurs de déploiement

Lorsque l’opération passe la validation, mais qu’elle échoue pendant le déploiement, l’erreur s’affiche dans les notifications. Sélectionnez la notification.

![erreur de notification](./media/resource-manager-common-deployment-errors/notification.png)

Vous voyez plus d’informations sur le déploiement. Sélectionnez l’option pour rechercher plus d’informations sur l’erreur.

![échec du déploiement](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Vous voyez le message et les codes d’erreur. Notez qu’il y a deux codes d’erreur. Le premier code d’erreur (**DeploymentFailed**) est un code d’erreur général qui ne fournit pas les détails dont vous avez besoin pour résoudre l’erreur. Le deuxième code d’erreur (**StorageAccountNotFound**) fournit les détails dont vous avez besoin. 

![détails de l’erreur](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Activer l’enregistrement du débogage

Vous avez parfois besoin de plus d’informations sur la demande et la réponse pour connaître la cause du problème. Avec PowerShell ou l’interface de ligne de commande Azure, vous pouvez demander la journalisation d’informations supplémentaires lors d’un déploiement.

- PowerShell

   Dans PowerShell, définissez le paramètre **DeploymentDebugLogLevel** pour All, ResponseContent ou RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Examinez le contenu de la requête avec l’applet de commande suivant :

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Ou examinez la réponse avec :

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Ces informations peuvent vous aider à déterminer si une valeur dans le modèle n’est pas définie correctement.

- Azure CLI

   Examinez les opérations de déploiement avec la commande suivante :

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Modèle imbriqué

   Pour enregistrer les informations de débogage pour un modèle imbriqué, utilisez l’élément **debugSetting**.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```

## <a name="create-a-troubleshooting-template"></a>Création d’un modèle de résolution des problèmes

Dans certains cas, le moyen le plus simple pour résoudre les problèmes de votre modèle consiste à tester des parties de celui-ci. Vous pouvez créer un modèle simplifié qui vous permet de vous concentrer sur la partie que vous pensez erronée. Par exemple, supposons que vous recevez une erreur lorsque vous référencez une ressource. Au lieu de traiter un modèle complet, créez un modèle retournant la partie qui peut être à l’origine de votre problème. Il peut vous aider à déterminer si vous transmettez les paramètres appropriés à l’aide de fonctions de modèle et si vous obtenez la ressource que vous attendez.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Si vous rencontrez des erreurs de déploiement que vous pensez liées à une mauvaise définition des dépendances, vous pouvez tester votre modèle en le divisant en modèles plus simples. Commencez par créer un modèle déployant une seule ressource (comme un serveur SQL Server). Lorsque vous êtes sûr que cette ressource est correctement définie, ajoutez une ressource qui en dépend (par exemple une base de données SQL). Une fois ces deux ressources correctement définies, ajoutez les autres ressources dépendantes (telles que les stratégies d’audit). Supprimez le groupe de ressources entre chaque déploiement de test afin de tester correctement les dépendances.


## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).
* Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](resource-manager-deployment-operations.md).
