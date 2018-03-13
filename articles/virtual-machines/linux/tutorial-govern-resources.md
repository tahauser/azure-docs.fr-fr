---
title: "Gouvernance de machines virtuelles Azure à l’aide d’Azure CLI | Microsoft Docs"
description: "Didacticiel : apprenez à gérer des machines virtuelles Azure en appliquant le contrôle d’accès en fonction du rôle (RBAC), des stratégies, des verrous et des balises avec Azure CLI"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 86ac3ec390c7aa9bc24a90ef2ee582f97f8b5407
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="virtual-machine-governance-with-azure-cli"></a>Gouvernance de machines virtuelles à l’aide d’Azure CLI

[!include[Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pour installer et utiliser l’interface CLI localement, consultez [Install Azure CLI 2.0](/cli/azure/install-azure-cli) (Installer Azure CLI 2.0).

## <a name="understand-scope"></a>Comprendre l’étendue

[!include[Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Dans ce didacticiel, vous allez appliquer tous les paramètres de gestion à un groupe de ressources afin de pouvoir facilement supprimer ces paramètres quand vous avez terminé.

Créons ce groupe de ressources.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Pour le moment, le groupe de ressources est vide.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Vous devez vous assurer que les utilisateurs de votre organisation disposent du niveau d’accès approprié à ces ressources. Il n’est pas question de leur accorder un accès illimité, mais de faire en sorte qu’ils puissent accomplir leur travail. Le [contrôle d’accès en fonction du rôle](../../active-directory/role-based-access-control-what-is.md) vous permet de définir les utilisateurs autorisés à effectuer des actions spécifiques dans une étendue.

Pour créer et supprimer des attributions de rôles, les utilisateurs doivent disposer d’un accès `Microsoft.Authorization/roleAssignments/*`. Cet accès est accordé par le biais du rôle Propriétaire ou Administrateur de l’accès utilisateur.

Pour gérer les solutions de machine virtuelle, il existe trois rôles de ressource qui fournissent un accès souvent nécessaire :

* [Collaborateur de machine virtuelle](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Collaborateur de réseau](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Collaborateur de compte de stockage](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Au lieu d’affecter des rôles à des utilisateurs, il est souvent plus facile de [créer un groupe Azure Active Directory](../../active-directory/active-directory-groups-create-azure-portal.md) et d’y regrouper les utilisateurs qui ont besoin d’effectuer des actions similaires. Ensuite, vous affectez ce groupe au rôle approprié. Pour simplifier, vous allez créer un groupe Azure Active Directory vide. Vous pouvez toujours affecter ce groupe à un rôle pour une étendue. 

L’exemple suivant permet de créer un groupe Azure Active Directory nommé *VMDemoContributors* avec le pseudonyme de messagerie *vmDemoGroup*. Le pseudonyme de messagerie sert d’alias pour le groupe.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

Le groupe ne se propage pas immédiatement dans Azure Active Directory après le retour de l’invite de commandes. Après avoir attendu 20 à 30 secondes, utilisez la commande [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour assigner le nouveau groupe Azure Active Directory au rôle Contributeur de machine virtuelle pour le groupe de ressources.  Si vous exécutez la commande suivante avant la propagation, vous recevrez le message d’erreur **Le principal <guid> n’existe pas dans le répertoire**. Essayez d’exécuter à nouveau la commande.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

En règle générale, vous répétez ce processus pour *Contributeur de réseaux* et *Contributeur de comptes de stockage*, pour être sûr que les utilisateurs sont affectés à la gestion des ressources déployées. Dans cet article, vous pouvez ignorer ces étapes.

## <a name="azure-policies"></a>Stratégies Azure

[!include[Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Appliquer les stratégies

Votre abonnement comprend déjà plusieurs définitions de stratégie. Pour afficher les définitions de stratégie disponibles, utilisez la commande [az policy definition list](/cli/azure/policy/definition#az_policy_definition_list) :

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Vous voyez les définitions de stratégie existantes. Le type de stratégie est **BuiltIn** ou **Custom**. Recherchez les définitions qui décrivent la condition que vous souhaitez affecter. Dans cet article, vous affectez des stratégies qui :

* Limitent les emplacements pour toutes les ressources
* Limitent les références SKU pour les machines virtuelles
* Auditent les machines virtuelles qui n’utilisent pas de disques managés

L’exemple suivant vous permet de récupérer trois définitions de stratégie basées sur le nom d’affichage. La commande [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) sert à assigner ces définitions au groupe de ressources. Pour certaines stratégies, vous devez fournir des valeurs de paramètre pour définir les valeurs autorisées.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

L’exemple précédent suppose que vous connaissiez déjà les paramètres d’une stratégie. Si vous avez besoin d’afficher les paramètres, utilisez :

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>Déployer la machine virtuelle

Vous avez affecté des rôles et des stratégies, vous êtes donc prêt à déployer votre solution. La taille par défaut est Standard_DS1_v2, qui correspond à l’une des références SKU autorisées. La commande crée les clés SSH si elles n’existent pas déjà dans un emplacement par défaut.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Une fois votre déploiement terminé, vous pouvez appliquer davantage de paramètres de gestion à la solution.

## <a name="lock-resources"></a>Verrouiller des ressources

Les [verrous de ressources](../../azure-resource-manager/resource-group-lock-resources.md) empêchent les utilisateurs de votre organisation de supprimer ou de modifier accidentellement des ressources critiques. Contrairement au contrôle d’accès basé sur les rôles, les verrous de ressources permettent d’appliquer une restriction à tous les utilisateurs et rôles. Vous pouvez définir le niveau de verrouillage sur *CanNotDelete* ou *ReadOnly*.

Pour créer ou supprimer des verrous de gestion, vous devez avoir accès aux actions `Microsoft.Authorization/locks/*`. Parmi les rôles prédéfinis, seuls les rôles **Propriétaire** et **Administrateur de l'accès utilisateur** peuvent effectuer ces actions.

Pour verrouiller la machine virtuelle et le groupe de sécurité réseau, utilisez la commande [az lock create](/cli/azure/lock#az_lock_create) :

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

Pour tester les verrous, exécutez la commande suivante :

```azurecli-interactive 
az group delete --name myResourceGroup
```

Un message d’erreur s’affiche, indiquant que l’opération de suppression ne peut pas être effectuée à cause d’un verrou. Pour pouvoir supprimer le groupe de ressources, vous devez impérativement en supprimer les verrous. Cette étape est expliquée dans [Nettoyer les ressources](#clean-up-resources).

## <a name="tag-resources"></a>Baliser des ressources

Vous allez appliquer des [balises](../../azure-resource-manager/resource-group-using-tags.md) à vos ressources Azure pour les organiser de façon logique par catégories. Chaque balise se compose d’un nom et d’une valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

[!include[Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Pour appliquer des balises à une machine virtuelle, utilisez la commande [az resource tag](/cli/azure/resource#az_resource_tag). Les éventuelles balises existantes de la ressource ne seront pas conservées.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Rechercher des ressources à l’aide de leurs balises

Pour rechercher des ressources avec le nom et la valeur d’une balise, utilisez la commande [az resource list](/cli/azure/resource#az_resource_list) :

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

Vous pouvez utiliser les valeurs retournées pour des tâches de gestion telles que l’arrêt de toutes les machines virtuelles avec une valeur de balise.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Afficher les coûts selon les valeurs de balise

[!include[Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous ne pouvez pas supprimer le groupe de sécurité réseau verrouillé tant que vous n’avez pas supprimé le verrou. Pour supprimer le verrou, récupérez les ID des verrous et fournissez-les à la commande [az lock delete](/cli/azure/lock#az_lock_delete) :

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées. Quittez la session SSH sur votre machine virtuelle, puis supprimez les ressources suivantes :

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous montré comment créer une image de machine virtuelle. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Assigner des utilisateurs à un rôle
> * Appliquer des stratégies qui imposent des standards
> * Protéger les ressources critiques avec des verrous
> * Ajouter des balises aux ressources pour la facturation et la gestion

Passez au didacticiel suivant pour en savoir plus sur les machines virtuelles hautement disponibles.

> [!div class="nextstepaction"]
> [Surveiller les machines virtuelles](tutorial-monitoring.md)

