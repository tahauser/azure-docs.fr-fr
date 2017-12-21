---
title: "Créer une application gérée par Azure avec Azure CLI | Microsoft Docs"
description: "Montre comment créer une application managée Azure destinée aux membres de votre organisation."
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 35059603096279f7d58da1c1b40dd2ab3f1b5c38
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Créer et déployer une application managée avec l’interface de ligne de commande Azure

Cet article fournit une introduction sur l’utilisation des applications managées. Vous ajoutez une définition d’application managée à un catalogue interne pour les utilisateurs dans votre organisation. Vous déployez ensuite cette application managée à votre abonnement. Pour simplifier l’introduction, nous avons déjà créé les fichiers de votre application managée. Un fichier définit l’infrastructure de votre solution. Un deuxième fichier définit l’interface utilisateur pour le déploiement de l’application managée par le biais du portail. Ces fichiers sont disponibles dans GitHub. Vous apprenez à générer ces fichiers dans le didacticiel [Créer l’application de catalogue de service](publish-service-catalog-app.md).

Lorsque vous avez terminé, vous disposez de trois groupes de ressources contenant les différentes parties de l’application managée.

| Groupe de ressources | Contient | Description |
| -------------- | -------- | ----------- |
| appDefinitionGroup | La définition d’application gérée. | Le serveur de publication crée ce groupe de ressources et la définition de l’application managée. Toute personne ayant accès à la définition d’application managée peut la déployer. |
| applicationGroup | L’instance de l’application managée. | Le consommateur crée ce groupe de ressources et l’instance de l’application managée. Le consommateur peut mettre à jour l’application managée par cette instance. |
| infrastructureGroup | Les ressources de l’application managée. | Ce groupe de ressources est automatiquement créé lors de la création de l’application managée. Le serveur de publication a accès à ce groupe de ressources pour gérer l’application. Le consommateur a un accès limité au groupe de ressources. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Créer un groupe de ressources pour définition

La définition de votre application managée existe dans un groupe de ressources. Un groupe de ressources est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

Pour créer un groupe de ressources, utilisez la commande suivante :

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Créer la définition d’application gérée

Lors de la définition de l’application managée, vous sélectionnez un utilisateur, un groupe ou une application qui gère les ressources au nom du consommateur. Cette identité dispose d’autorisations sur le groupe de ressources managé en fonction du rôle attribué. En général, vous créez un groupe Azure Active Directory pour gérer les ressources. Toutefois, pour cet article, utilisez votre propre identité.

Pour obtenir l’ID d’objet de votre identité, fournissez votre nom d’utilisateur principal dans la commande suivante :

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Ensuite, vous avez besoin de l’ID de définition de rôle du rôle RBAC intégré auquel vous souhaitez accorder l’accès à l’utilisateur. La commande suivante montre comment obtenir l’ID de définition de rôle pour le rôle Propriétaire :

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

À présent, créez la ressource de définition de l’application managée. L’application managée contient uniquement un compte de stockage.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

Une fois la commande terminée, vous avez une définition de l’application managée dans votre groupe de ressources. 

Certains des paramètres utilisés dans cet exemple sont les suivants :

* **resource-group** : nom du groupe de ressources dans lequel la définition de l’application managée est créée.
* **lock-level**: type de verrou placé sur le groupe de ressources gérées. Il empêche le client d’effectuer des opérations indésirables sur ce groupe de ressources. Actuellement, ReadOnly est le seul niveau de verrou pris en charge. Lorsque ReadOnly est spécifié, le client peut lire uniquement les ressources présentes dans le groupe de ressources gérées.
* **authorizations** : décrit l’ID principal et l’ID de définition de rôle utilisés pour accorder des autorisations au groupe de ressources gérées. Il est spécifié sous la forme `<principalId>:<roleDefinitionId>`. Plusieurs valeurs peuvent également être spécifiées pour cette propriété. Si plusieurs valeurs sont nécessaires, elles doivent être spécifiées sous la forme `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Les valeurs sont séparées par un espace.
* **package-file-uri**: l’emplacement d’un package .zip contenant les fichiers requis. Au minimum, le package contient les fichiers **mainTemplate.json** et **createUiDefinition.json**. **mainTemplate.json** définit les ressources Azure approvisionnées pour l’application managée. Le modèle n’est en rien différent d’un modèle Resource Manager normal. **createUiDefinition.json** : génère l’interface utilisateur pour les clients qui créent l’application managée par le biais du portail.

## <a name="create-resource-group-for-managed-application"></a>Créer un groupe de ressources pour l’application managée

Maintenant, vous êtes prêt à déployer l’application managée. 

Pour contenir l’application managée déployée, vous avez besoin d’un groupe de ressources. Utilisez **westcentralus** pour l’emplacement.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Déployer l’application managée

Vous déployez l’application avec les commandes suivantes :

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Certains des paramètres utilisés dans cet exemple sont les suivants :

* **managedapp-definition-id**: l’ID de la définition que vous avez créée précédemment dans cet article.
* **managed-rg-id**: l’ID du groupe de ressources pour les ressources associées à l’application managée. La commande crée ce groupe de ressources. Il **ne doit pas exister avant d’exécuter la commande**. Ce groupe de ressources est géré par l’éditeur. 
* **resource-group** : groupe de ressources dans lequel la ressource d’application managée est créée.
* **parameters** : paramètres qui sont nécessaires aux ressources définies dans l’application managée.

À l’issue du déploiement, vous pouvez constater que l’application managée est créée dans applicationGroup. La ressource storageAccount est créée dans infrastructureGroup.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées](overview.md).
* Consultez les [exemples d’application gérée](https://github.com/Azure/azure-managedapp-samples/tree/master/samples) pour voir des exemples de fichiers.
* Pour en savoir plus sur la création d’un fichier de définition de l’interface utilisateur pour une application gérée, consultez [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
