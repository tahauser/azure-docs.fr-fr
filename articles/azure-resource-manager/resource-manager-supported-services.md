---
title: Types de ressources et fournisseurs de ressources Azure | Microsoft Docs
description: "Décrit les fournisseurs de ressources qui prennent en charge Resource Manager, ainsi que les schémas et versions d’API disponibles et les régions pouvant héberger les ressources."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 6b1915288feff8f1d9fc780fef40a247d1c35839
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="resource-providers-and-types"></a>Fournisseurs et types de ressources

Lorsque vous déployez des ressources, vous devez fréquemment récupérer des informations sur les fournisseurs et les types de ressources. Dans cet article, vous apprenez à :

* Afficher tous les fournisseurs de ressources dans Azure
* Vérifier l’état de l’inscription d’un fournisseur de ressources
* Inscrire un fournisseur de ressources
* Afficher les types de ressources pour un fournisseur de ressources
* Afficher les emplacements valides pour un type de ressource
* Afficher les versions d’API valides pour un type de ressource

Vous pouvez effectuer ces étapes via le portail, PowerShell ou l’interface de ligne de commande Azure.

## <a name="powershell"></a>PowerShell

Pour afficher tous les fournisseurs de ressources dans Azure et l’état de l’inscription de votre abonnement, utilisez :

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Qui retourne des résultats semblables à :

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

L’inscription d’un fournisseur de ressources configure votre abonnement pour travailler avec le fournisseur de ressources. L’étendue pour l’inscription est toujours l’abonnement. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement. Toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer `/register/action`l’opération pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Qui retourne des résultats semblables à :

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Vous ne pouvez pas annuler l’inscription d’un fournisseur de ressources lorsque vous avez encore des types de ressources de ce fournisseur de ressources dans votre abonnement.

Pour afficher des informations pour un fournisseur de ressources particulier, utilisez :

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

Qui retourne des résultats semblables à :

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Pour afficher les types de ressources pour un fournisseur de ressources, utilisez :

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Résultat :

```powershell
batchAccounts
operations
locations
locations/quotas
```

La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée. 

Pour obtenir les versions d’API disponibles pour un type de ressource, utilisez :

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Résultat :

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. 

Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez :

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Résultat :

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI
Pour afficher tous les fournisseurs de ressources dans Azure et l’état de l’inscription de votre abonnement, utilisez :

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Qui retourne des résultats semblables à :

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

L’inscription d’un fournisseur de ressources configure votre abonnement pour travailler avec le fournisseur de ressources. L’étendue pour l’inscription est toujours l’abonnement. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement. Toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer `/register/action`l’opération pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire.

```azurecli
az provider register --namespace Microsoft.Batch
```

Qui retourne un message indiquant que l’inscription est en cours.

Vous ne pouvez pas annuler l’inscription d’un fournisseur de ressources lorsque vous avez encore des types de ressources de ce fournisseur de ressources dans votre abonnement.

Pour afficher des informations pour un fournisseur de ressources particulier, utilisez :

```azurecli
az provider show --namespace Microsoft.Batch
```

Qui retourne des résultats semblables à :

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Pour afficher les types de ressources pour un fournisseur de ressources, utilisez :

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Résultat :

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée. 

Pour obtenir les versions d’API disponibles pour un type de ressource, utilisez :

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Résultat :

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. 

Pour obtenir les emplacements pris en charge pour un type de ressource, utilisez :

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Résultat :

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="portal"></a>Portail

Pour afficher tous les fournisseurs de ressources dans Azure et l’état de l’inscription de votre abonnement, sélectionnez **Abonnements**.

![sélectionner les abonnements](./media/resource-manager-supported-services/select-subscriptions.png)

Choisissez l’abonnement à afficher.

![spécifier l’abonnement](./media/resource-manager-supported-services/subscription.png)

Sélectionnez **Fournisseurs de ressources** et affichez la liste des fournisseurs de ressources disponibles.

![afficher les fournisseurs de ressources](./media/resource-manager-supported-services/show-resource-providers.png)

L’inscription d’un fournisseur de ressources configure votre abonnement pour travailler avec le fournisseur de ressources. L’étendue pour l’inscription est toujours l’abonnement. Par défaut, de nombreux fournisseurs de ressources sont enregistrés automatiquement. Toutefois, vous devrez peut-être inscrire manuellement certains fournisseurs de ressources. Pour inscrire un fournisseur de ressources, vous devez être autorisé à effectuer `/register/action`l’opération pour le fournisseur de ressources. Cette opération est incluse dans les rôles de contributeur et de propriétaire. Pour inscrire un fournisseur de ressources, sélectionnez **Inscrire**.

![inscrire un fournisseur de ressources](./media/resource-manager-supported-services/register-provider.png)

Vous ne pouvez pas annuler l’inscription d’un fournisseur de ressources lorsque vous avez encore des types de ressources de ce fournisseur de ressources dans votre abonnement.

Pour visualiser des informations concernant un fournisseur de ressources spécifique, sélectionnez **Tous les services**.

![sélectionner Tous les services](./media/resource-manager-supported-services/more-services.png)

Recherchez **Explorateur de ressources** et sélectionnez-le dans les options disponibles.

![sélectionner l’Explorateur de ressources](./media/resource-manager-supported-services/select-resource-explorer.png)

Sélectionnez **Fournisseurs**.

![sélectionner les fournisseurs](./media/resource-manager-supported-services/select-providers.png)

Sélectionnez le fournisseur de ressources et le type de ressource que vous souhaitez afficher.

![sélectionner le type de ressource](./media/resource-manager-supported-services/select-resource-type.png)

Resource Manager est pris en charge dans toutes les régions, mais il est possible que certaines ressources que vous déployez ne soient pas prises en charge dans toutes les régions. Par ailleurs, il peut y avoir des limitations sur votre abonnement qui vous empêchent d’utiliser certaines régions prenant en charge la ressource. L’Explorateur de ressources affiche les emplacements valides pour le type de ressource.

![afficher les emplacements](./media/resource-manager-supported-services/show-locations.png)

La version de l'API correspond à une version des opérations de l'API REST publiées par le fournisseur de ressources. Lorsqu'un fournisseur de ressources active de nouvelles fonctionnalités, une nouvelle version de l'API REST sera publiée. L’Explorateur de ressources affiche les versions d’API valides pour le type de ressource.

![afficher les versions d’API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour en savoir plus sur le déploiement de ressources, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).
* Pour afficher les opérations pour un fournisseur de ressources, consultez [API REST Azure](/rest/api/).

