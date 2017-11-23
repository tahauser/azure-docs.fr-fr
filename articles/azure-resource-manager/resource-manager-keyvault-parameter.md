---
title: "Clé secrète de coffre de clés avec un modèle Azure Resource Manager | Microsoft Docs"
description: "Montre comment passer une clé secrète à partir d’un coffre de clés en tant que paramètre lors du déploiement."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: tomfitz
ms.openlocfilehash: e789a234979be877d990665902fd6219ae7ec40b
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Utiliser Azure Key Vault pour transmettre une valeur de paramètre sécurisée pendant le déploiement

Lorsque vous avez besoin de passer une valeur sécurisée (par exemple, un mot de passe) comme paramètre au cours du déploiement, vous pouvez récupérer la valeur à partir d’un coffre [Azure Key Vault](../key-vault/key-vault-whatis.md). Vous récupérez la valeur en référençant le coffre de clés et la clé secrète dans votre fichier de paramètres. La valeur n’est jamais exposée, car vous référencez uniquement son ID de coffre de clés. Il est inutile d’entrer manuellement la valeur de la clé secrète chaque fois que vous déployez les ressources. Le coffre de clés peut exister dans un autre abonnement que le groupe de ressources sur lequel vous effectuez le déploiement. Lorsque vous référencez le coffre de clés, incluez l’ID d’abonnement.

Lorsque vous créez le coffre de clés, définissez la propriété *enabledForTemplateDeployment* sur *true*. En définissant cette valeur sur true, vous autorisez l’accès depuis des modèles Resource Manager pendant le déploiement.

## <a name="deploy-a-key-vault-and-secret"></a>Déploiement d'un coffre de clés et d’une clé secrète

Pour créer un coffre de clés et une clé secrète, utilisez l’interface de ligne de commande Azure CLI ou PowerShell. Remarque : le coffre de clés est activé pour le déploiement du modèle. 

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Pour PowerShell, utilisez la commande suivante :

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>Autoriser l’accès à la clé secrète

Que vous utilisiez un coffre de clés nouveau ou existant, vérifiez que l’utilisateur qui déploie le modèle peut accéder à la clé secrète. L’utilisateur déployant un modèle qui fait référence à une clé secrète doit avoir l’autorisation `Microsoft.KeyVault/vaults/deploy/action` pour le coffre de clés. Les rôles [propriétaire](../active-directory/role-based-access-built-in-roles.md#owner) et [contributeur](../active-directory/role-based-access-built-in-roles.md#contributor) accordent cet accès.

## <a name="reference-a-secret-with-static-id"></a>Référencement d’un secret avec un ID statique

Le modèle qui reçoit un secret de coffre de clés est similaire à n’importe quel modèle. Ceci s’explique par le fait que **vous référencez le coffre de clés dans le fichier de paramètres, et non dans le modèle.** Par exemple, le modèle suivant déploie une base de données SQL qui inclut un mot de passe administrateur. Le paramètre du mot de passe est défini sur une chaîne sécurisée. Toutefois, le modèle ne spécifie pas d’où vient cette valeur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

À présent, créez un fichier de paramètres pour le modèle précédent. Dans le fichier de paramètres, spécifiez un paramètre qui correspond au nom du paramètre dans le modèle. Pour la valeur du paramètre, référencez le secret du coffre de clés. Vous référencez la clé secrète en passant l'identificateur de ressource du coffre de clés et le nom de la clé secrète. Dans l’exemple suivant, la clé secrète du coffre de clés doit déjà exister, et vous définissez une valeur statique pour son ID de ressource.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Maintenant, déployez le modèle et transmettez le fichier de paramètres. Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group create --name datagroup --location "Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-file sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Pour PowerShell, utilisez la commande suivante :

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateFile sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Référencement d’un secret avec un ID dynamique

La section précédente expliquait comment transmettre un ID de ressource statique pour la clé secrète du coffre de clés. Toutefois, dans certains scénarios, vous devez référencer une clé secrète de coffre de clés qui varie selon le déploiement actuel. Dans ce cas, vous ne pouvez pas coder en dur l’ID de ressource dans le fichier de paramètres. Malheureusement, vous ne pouvez pas générer dynamiquement l’ID de ressource dans le fichier de paramètres, car les expressions de modèle ne sont pas autorisées dans ce dernier.

Pour générer dynamiquement l’ID de ressource pour une clé secrète de coffre de clés, vous devez déplacer la ressource qui a besoin de la clé secrète dans un modèle imbriqué. Dans votre modèle principal, vous ajoutez le modèle imbriqué et passez un paramètre qui contient l’ID de ressource généré dynamiquement. Votre modèle imbriqué doit être disponible par le biais d’un URI externe. Le reste de cet article présuppose que vous avez ajouté le modèle précédent à un compte de stockage, et qu’il est accessible via l’URI - `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

Déployez le modèle précédent et fournissez des valeurs pour les paramètres.

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).
* Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
