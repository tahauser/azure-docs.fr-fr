---
title: "Modèle Azure Resource Manager avec extension VS Code | Microsoft Docs"
description: "Utilisez l’extension des outils Azure Resource Manager pour travailler sur des modèles Resource Manager."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/06/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: 0dd48146624052fbd00093066d79067b75e33c92
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="use-visual-studio-code-extension-to-create-azure-resource-manager-template"></a>Utiliser l’extension de Code Visual Studio pour créer un modèle Azure Resource Manager
Cet article vous indique les avantages liés à l’installation et à l’utilisation de l’extension des outils Azure Resource Manager dans Visual Studio Code. Vous pouvez créer des modèles Resource Manager dans VS Code sans l’extension, mais cette dernière fournit des options de saisie semi-automatique qui simplifient le développement de modèles. Des fonctions de modèle, des paramètres et des variables sont disponibles dans le modèle.

Pour effectuer les opérations décrites dans cet article, vous avez besoin de [Visual Studio Code](https://code.visualstudio.com/).

Pour comprendre les concepts associés au déploiement et à la gestion de vos solutions Azure, voir [Présentation d’Azure Resource Manager](resource-group-overview.md).

## <a name="create-the-template"></a>Créer le modèle

Cet article s’appuie sur le modèle que vous avez créé dans la section [Créer et déployer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md). Si vous avez déjà ce modèle, vous pouvez ignorer cette section.

1. Si vous avez besoin de créer ce modèle, démarrez VS Code. Sélectionnez **Fichier** > **Nouveau fichier**. 

   ![Nouveau fichier](./media/resource-manager-vscode-extension/new-file.png)

2. Copiez et collez la syntaxe JSON suivante dans votre fichier :

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "storageSKU": {
         "type": "string",
         "allowedValues": [
           "Standard_LRS",
           "Standard_ZRS",
           "Standard_GRS",
           "Standard_RAGRS",
           "Premium_LRS"
         ],
         "defaultValue": "Standard_LRS",
         "metadata": {
           "description": "The type of replication to use for the storage account."
         }
       },   
       "storageNamePrefix": {
         "type": "string",
         "maxLength": 11,
         "defaultValue": "storage",
         "metadata": {
           "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
         }
       }
     },
     "variables": {
       "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
     },
     "resources": [
       {
         "name": "[variables('storageName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "[parameters('storageSKU')]"
         },
         "kind": "Storage",
         "location": "[resourceGroup().location]",
         "tags": {},
         "properties": {
           "encryption":{
             "services":{
               "blob":{
                 "enabled":true
               }
             },
             "keySource":"Microsoft.Storage"
           }
         }
       }
     ],
     "outputs": {  }
   }
   ```

3. Enregistrez ce fichier sous **azuredeploy.json** dans un dossier local.

   ![Enregistrer un modèle](./media/resource-manager-vscode-extension/save-template.png)

## <a name="install-the-extension"></a>Installer l’extension

1. Dans VS Code, sélectionnez **Extensions**.

   ![Sélectionner des extensions](./media/resource-manager-vscode-extension/select-extensions.png)

2. Recherchez les **Outils Azure Resource Manager** et sélectionnez **Installer**.

   ![Installer l’extension](./media/resource-manager-vscode-extension/install-extension.png)

3. Pour terminer l’installation de l’extension, sélectionnez **Recharger**.

## <a name="edit-the-template"></a>Modifier le modèle

1. Ouvrez votre fichier azuredeploy.json.

2. L’extension récupère toutes les [fonctions de modèle](resource-group-template-functions.md) disponibles. Elle lit également les paramètres et les variables que vous avez définis dans le modèle. Pour afficher cette fonctionnalité, ajoutez deux valeurs à la section de sortie. Dans le modèle, remplacez la section des sorties par ce qui suit :

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": ""
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

3. Placez le curseur à l’intérieur des guillemets pour obtenir une valeur dans **groupLocation**. Tapez le crochet ouvrant (`[`). Notez que l’extension propose immédiatement des fonctions de modèle disponibles.

   ![Afficher les fonctions disponibles](./media/resource-manager-vscode-extension/available-functions.png)

4. Commencez à taper **resourceGroup**. Lorsque la fonction `resourceGroup()` s’affiche, appuyez sur la touche de tabulation ou Entrée.

   ![Sélectionner les fonctions resourceGroup](./media/resource-manager-vscode-extension/show-resourcegroup.png)

5. L’extension renseigne la syntaxe de la fonction. La fonction [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) n’accepte pas de paramètres. Ajoutez un point après la parenthèse de droite. L’extension fournit les propriétés qui sont disponibles pour l’objet renvoyé par la fonction `resourceGroup()`. Sélectionnez `location`.

   ![Sélectionner les propriétés](./media/resource-manager-vscode-extension/resourcegroup-properties.png)

6. Après **l’emplacement**, ajoutez le crochet fermant.

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

7. Placez le curseur à l’intérieur des guillemets pour **storageUri**. Tapez de nouveau le crochet ouvrant. Commencez à taper **référence**. Lorsque cette fonction est sélectionnée, appuyez sur la touche de tabulation ou Entrée.

   ![Sélectionner une référence](./media/resource-manager-vscode-extension/add-reference.png)

8. Le [référence](resource-group-template-functions-resource.md#reference) accepte l’ID de ressource ou le nom de la ressource en tant que paramètre. Le nom du compte de stockage est déjà indiqué dans une variable. Tapez **var**, puis sélectionnez Ctrl + espace. L’extension suggère la fonction variables.

   ![Sélectionner la variable](./media/resource-manager-vscode-extension/add-variable.png)

   Appuyez sur la touche de tabulation ou Entrée.

9. La fonction [variables](resource-group-template-functions-deployment.md#variables) requiert le nom de la variable. À l’intérieur des parenthèses, ajoutez un guillemet simple. L’extension fournit les noms de variables que vous avez définis dans le modèle.

   ![Afficher des variables](./media/resource-manager-vscode-extension/show-variables.png) 

10. Sélectionnez la variable **storageName**. Ajoutez le crochet fermant. L’exemple suivant illustre la section de sortie :

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
       }
   }
   ```

Le modèle final est :

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": { 
    "groupLocation": {
      "type": "string",
      "value": "[resourceGroup().location]"
    },
    "storageUri": {
      "type": "string",
      "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
    }
  }
}
```

## <a name="deploy-template"></a>Déployer un modèle

Vous êtes prêt à déployer ce modèle. Vous utilisez soit PowerShell, soit Azure CLI pour créer un groupe de ressources. Vous déployez ensuite un compte de stockage dans ce groupe de ressources.

* Pour PowerShell, utilisez les commandes suivantes à partir du dossier qui contient le modèle :

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Pour une installation locale d’Azure CLI, utilisez les commandes suivantes à partir du dossier qui contient le modèle :

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Lorsque le déploiement est achevé, les valeurs de sortie sont renvoyées.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources que vous avez déployées en supprimant le groupe de ressources.

Pour PowerShell, utilisez :

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Pour l’interface de ligne de commande Azure, consultez :

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la structure du modèle, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour en savoir plus sur les propriétés d’un compte de stockage, consultez la [référence sur le modèle des comptes de stockage](/azure/templates/microsoft.storage/storageaccounts).
* Pour afficher des modèles complets pour de nombreux types de solutions, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/).
