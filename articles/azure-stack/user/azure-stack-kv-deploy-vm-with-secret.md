---
title: "Déployer une machine virtuelle avec un mot de passe stocké de façon sécurisée sur Azure Stack | Microsoft Docs"
description: "Découvrez comment déployer une machine virtuelle en utilisant un mot de passe stocké dans Azure Stack Key Vault"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: mabrigg
ms.openlocfilehash: 8d9a2cebd7a28ca13cf88518a7c83b217af4c0e1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-virtual-machine-by-retrieving-the-password-stored-in-a-key-vault"></a>Créer une machine virtuelle en récupérant le mot de passe stocké dans un coffre de clés

Quand vous devez passer une valeur sécurisée lors d’un déploiement, comme un mot de passe, vous pouvez stocker cette valeur en tant que secret dans un coffre de clés Azure Stack et la référencer dans les modèles Azure Resource Manager. Ainsi, vous ne devez pas entrer manuellement le secret chaque fois que vous déployez les ressources ; vous pouvez aussi spécifier quels utilisateurs ou principaux du service peuvent accéder au secret. 

Dans cet article, nous vous guidons à travers les étapes nécessaires pour déployer une machine virtuelle Windows dans Azure Stack en récupérant le mot de passe qui est stocké dans un coffre de clés. Ainsi, le mot de passe n’est jamais placé en texte brut dans le fichier des paramètres du modèle. Vous pouvez utiliser les étapes à partir du Kit de développement Azure Stack, ou à partir d’un client externe si vous êtes connecté via un VPN.

## <a name="prerequisites"></a>Composants requis
 
* Les utilisateurs doivent s’abonner à une offre qui inclut le service Key Vault.  
* [Installer PowerShell pour Azure Stack.](azure-stack-powershell-install.md)  
* [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](azure-stack-powershell-configure-user.md)

Les étapes suivantes décrivent le processus nécessaire pour créer une machine virtuelle en récupérant le mot de passe stocké dans un coffre de clés :

1. Créez un secret de coffre de clés.
2. Mettre à jour le fichier azuredeploy.parameters.json.
3. Déployez le modèle.

## <a name="create-a-key-vault-secret"></a>Créer un secret Key Vault

Le script suivant crée un coffre de clés et stocke un mot de passe dans le coffre de clés en tant que secret. Utilisez le paramètre `-EnabledForDeployment` quand vous créez le coffre de clés. Ce paramètre garantit que le coffre de clés peut être référencé à partir des modèles Azure Resource Manager.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

À l’exécution du script précédent, la sortie inclut l’URI du secret. Notez cet URI. Vous devez le référencer dans le modèle utilisé dans [Déployer une machine virtuelle Windows avec un mot de passe dans un coffre de clés](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Téléchargez le dossier [101-vm-secure-password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) sur votre ordinateur de développement. Ce dossier contient les fichiers `azuredeploy.json` et `azuredeploy.parameters.json` dont vous avez besoin dans les étapes suivantes.

Modifiez le fichier `azuredeploy.parameters.json` en fonction des valeurs de votre environnement. Les paramètres les plus intéressants sont le nom du coffre, le groupe de ressources du coffre et l’URI du secret (généré par le script précédent). Le fichier suivant est un exemple de fichier de paramètres :

## <a name="update-the-azuredeployparametersjson-file"></a>Mettre à jour le fichier azuredeploy.parameters.json

Mettez à jour le fichier azuredeploy.parameters.json avec les valeurs de la machine virtuelle KeyVaultURI, secretName, adminUsername correspondant à votre environnement. Le fichier JSON suivant est un exemple de fichier de paramètres du modèle : 

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Déploiement de modèle

Déployez maintenant le modèle avec le script PowerShell suivant :

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
Une fois le modèle déployé, la sortie suivante est générée :

![Sortie du déploiement](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)


## <a name="next-steps"></a>Étapes suivantes
[Déployer un exemple d’application avec Key Vault](azure-stack-kv-sample-app.md)

[Déployer une machine virtuelle avec un certificat Key Vault](azure-stack-kv-push-secret-into-vm.md)

