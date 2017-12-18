---
title: "Déployer une machine virtuelle avec un certificat stocké de façon sécurisée sur Azure Stack | Microsoft Docs"
description: "Découvrez comment déployer une machine virtuelle et y placer un certificat avec un coffre de clés dans Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: mabrigg
ms.openlocfilehash: e319f5c6d27d3a223764b0a5593480f02864ddbe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>Créer une machine virtuelle et inclure un certificat récupéré à partir d’un coffre de clés

Cet article aide à créer une machine virtuelle dans Azure Stack et à y placer des certificats. 

## <a name="prerequisites"></a>Composants requis

* Les utilisateurs doivent s’abonner à une offre qui inclut le service Key Vault. 
* [Installer PowerShell pour Azure Stack.](azure-stack-powershell-install.md)  
* [Configurez l’environnement PowerShell de l’utilisateur Azure Stack](azure-stack-powershell-configure-user.md).

Un coffre de clés dans Azure Stack est utilisé pour stocker les certificats. Les certificats sont utiles dans de nombreux scénarios différents. Prenons l’exemple d’un scénario selon lequel une machine virtuelle dans Azure Stack exécute une application qui a besoin d’un certificat. Ce certificat peut être utilisé pour le chiffrement, l’authentification auprès d’Active Directory ou le protocole SSL sur un site web. Conserver le certificat dans un coffre de clés permet d’assurer sa sécurité.

Dans cet article, nous vous guidons à travers les étapes requises pour placer un certificat sur une machine virtuelle Windows dans Azure Stack. Vous pouvez les suivre à partir du kit de développement Azure Stack ou d’un client externe Windows si vous êtes connecté via un VPN.

Les étapes suivantes décrivent le processus permettant de placer un certificat sur la machine virtuelle :

1. Créer un secret Key Vault.
2. Mettre à jour le fichier azuredeploy.parameters.json.
3. Déployer le modèle

## <a name="create-a-key-vault-secret"></a>Créer un secret Key Vault

Le script suivant crée un certificat au format .pfx et un coffre de clés, puis stocke le certificat dans le coffre de clés en tant que secret. Vous devez utiliser le paramètre `-EnabledForDeployment` pour créer le coffre de clés. Il garantit que le coffre de clés peut être référencé à partir de modèles Azure Resource Manager.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

À l’exécution du script précédent, la sortie inclut l’URI du secret. Notez cet URI. Vous devez le référencer dans le [modèle Placer le certificat dans Windows Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Téléchargez le dossier [Modèle vm-push-certificate-windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) sur votre ordinateur de développement. Ce dossier contient les fichiers `azuredeploy.json` et `azuredeploy.parameters.json` dont vous avez besoin aux étapes suivantes.

Modifiez le fichier `azuredeploy.parameters.json` en fonction des valeurs de votre environnement. Les paramètres les plus intéressants sont le nom du coffre, le groupe de ressources du coffre et l’URI du secret (généré par le script précédent). Le fichier suivant est un exemple de fichier de paramètres :

## <a name="update-the-azuredeployparametersjson-file"></a>Mettre à jour le fichier azuredeploy.parameters.json

Mettez à jour le fichier azuredeploy.parameters.json avec les valeurs vaultName, URI du secret, VmName et autres en fonction de votre environnement. Le fichier JSON suivant est un exemple de fichier de paramètres du modèle : 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Déployer le modèle

Déployez maintenant le modèle avec le script PowerShell suivant :

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Une fois le modèle déployé, la sortie suivante est générée :

![Sortie du déploiement](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Lorsque cette machine virtuelle est déployée, Azure Stack place le certificat sur la machine virtuelle. Sous Windows, le certificat est ajouté à l’emplacement de certificat LocalMachine, avec le magasin de certificats fourni par l’utilisateur. Sous Linux, le certificat est placé dans le répertoire /var/lib/waagent, avec le nom de fichier &lt;UppercaseThumbprint&gt;.crt pour le fichier de certificat X509 et &lt;UppercaseThumbprint&gt;.prv pour la clé privée.

## <a name="retire-certificates"></a>Mettre hors service des certificats

Dans la section précédente, nous vous avons montré comment placer un nouveau certificat sur une machine virtuelle. Votre ancien certificat est toujours sur la machine virtuelle ; il ne peut pas être supprimé. Toutefois, vous pouvez désactiver l’ancienne version du secret avec la cmdlet `Set-AzureKeyVaultSecretAttribute`. L’exemple suivant montre comment utiliser cette cmdlet. Veillez à remplacer les valeurs du nom du coffre, du nom du secret et de la version en fonction de votre environnement :

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une machine virtuelle avec un mot de passe Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Autoriser une application à accéder à Key Vault](azure-stack-kv-sample-app.md)


