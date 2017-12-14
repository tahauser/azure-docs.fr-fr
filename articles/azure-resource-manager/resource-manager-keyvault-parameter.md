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
ms.date: 11/30/2017
ms.author: tomfitz
ms.openlocfilehash: 7e02bd9c6130ef8b120282fafa9f0ee517890d0d
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
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

Le modèle qui reçoit un secret de coffre de clés est similaire à n’importe quel modèle. Ceci s’explique par le fait que **vous référencez le coffre de clés dans le fichier de paramètres, et non dans le modèle.** L’illustration suivante montre comment le fichier de paramètres fait référence à la question secrète et passe cette valeur au modèle.

![ID statique](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Par exemple, le [modèle suivant](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) déploie une base de données SQL qui comprend un mot de passe administrateur. Le paramètre du mot de passe est défini sur une chaîne sécurisée. Toutefois, le modèle ne spécifie pas d’où vient cette valeur.

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

À présent, créez un fichier de paramètres pour le modèle précédent. Dans le fichier de paramètres, spécifiez un paramètre qui correspond au nom du paramètre dans le modèle. Pour la valeur du paramètre, référencez le secret du coffre de clés. Vous référencez la clé secrète en passant l'identificateur de ressource du coffre de clés et le nom de la clé secrète. Dans le [fichier de paramètres suivant](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json), le secret du coffre de clés doit déjà exister, et vous définissez une valeur statique pour son ID de ressource. Copiez ce fichier localement et définissez l’ID d’abonnement, le nom du coffre et le nom du serveur SQL.

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

Maintenant, déployez le modèle et transmettez le fichier de paramètres. Vous pouvez vous servir de l’exemple de modèle de GitHub, à condition d’utiliser un fichier de paramètres régionaux dont les valeurs sont définies pour votre environnement.

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Pour PowerShell, utilisez la commande suivante :

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Référencement d’un secret avec un ID dynamique

La section précédente expliquait comment transmettre un ID de ressource statique pour la clé secrète du coffre de clés. Toutefois, dans certains scénarios, vous devez référencer une clé secrète de coffre de clés qui varie selon le déploiement actuel. Dans ce cas, vous ne pouvez pas coder en dur l’ID de ressource dans le fichier de paramètres. Malheureusement, vous ne pouvez pas générer dynamiquement l’ID de ressource dans le fichier de paramètres, car les expressions de modèle ne sont pas autorisées dans ce dernier.

Pour générer dynamiquement l’ID de ressource pour un secret de coffre de clés, vous devez déplacer la ressource nécessitant le secret dans un modèle lié. Dans votre modèle parent, ajoutez le modèle lié et passez un paramètre qui contient l’ID de ressource généré dynamiquement. L’illustration suivante montre comment un paramètre dans le modèle lié fait référence au secret.

![ID dynamique](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Votre modèle lié doit être disponible par le biais d’un URI externe. Normalement, vous ajoutez votre modèle à un compte de stockage et y accéder via l’URI comme `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

Le [modèle suivant](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json) crée de façon dynamique l’ID de coffre de clés et le passe en tant que paramètre. Il établit un lien vers un [exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) dans GitHub.

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
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
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

Déployez le modèle précédent et fournissez des valeurs pour les paramètres. Vous pouvez vous servir de l’exemple de modèle de GitHub, mais vous devez fournir des valeurs de paramètres pour votre environnement.

Pour l’interface de ligne de commande Azure, consultez :

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

Pour PowerShell, utilisez la commande suivante :

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).
* Pour obtenir des exemples complets de référencement de clés secrètes, consultez [Exemples de coffres de clés](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
