---
title: "Se connecter à Azure Stack avec l’interface CLI | Microsoft Docs"
description: "Découvrez comment utiliser l’interface de ligne de commande (CLI) multiplateforme pour gérer et déployer des ressources sur Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: mabrigg
ms.openlocfilehash: ad258cf6c73b2c1ea5f208ebef9906a0e3d35ea4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Installer et configurer l’interface CLI pour une utilisation avec Azure Stack

Dans cet article, nous vous guidons tout au long du processus d’utilisation de l’interface de ligne de commande (CLI) Azure pour gérer les ressources du Kit de développement Azure Stack à partir des plateformes clientes Linux et Mac. 

## <a name="install-cli"></a>Installer l’interface CLI

Ensuite, connectez-vous à votre station de travail de développement et installez l’interface CLI. Azure Stack nécessite la version 2.0 d’Azure CLI. Vous pouvez l’installer en utilisant la procédure décrite dans l’article [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Pour vérifier que l’installation a réussi, ouvrez un terminal ou une fenêtre d’invite de commandes et exécutez la commande suivante :

```azurecli
az --version
```

Vous devriez voir la version d’Azure CLI et d’autres bibliothèques dépendantes installées sur votre ordinateur.

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Approuver le certificat racine d’autorité de certification Azure Stack

Récupérez le certificat racine d’autorité de certification Azure Stack auprès de l’opérateur Azure Stack et approuvez-le. Pour approuver le certificat racine d’autorité de certification Azure Stack, ajoutez-le au certificat Python existant. Si vous exécutez l’interface CLI sur une machine Linux créée dans l’environnement Azure Stack, exécutez la commande bash suivante :

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Si vous exécutez l’interface CLI sur une machine qui se trouve en dehors de l’environnement Azure Stack, vous devez tout d’abord configurer la [connectivité VPN à Azure Stack](azure-stack-connect-azure-stack.md). Copiez à présent le certificat PEM que vous avez exporté auparavant sur votre station de travail de développement et exécutez les commandes suivantes, en fonction du système d’exploitation de votre station de travail de développement.

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry    = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="get-the-virtual-machine-aliases-endpoint"></a>Obtenir le point de terminaison des alias de machines virtuelles

Avant de pouvoir créer des machines virtuelles à l’aide de CLI, les utilisateurs doivent contacter l’opérateur Azure Stack et obtenir l’URI du point de terminaison des alias de machines virtuelles. Par exemple, Azure utilise l’URI suivant : https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json. L’administrateur de cloud doit configurer un point de terminaison similaire pour Azure Stack avec les images disponibles dans la Place de marché Azure Stack. Les utilisateurs doivent passer l’URI du point de terminaison au paramètre `endpoint-vm-image-alias-doc` à la commande `az cloud register` comme indiqué dans la section suivante. 
   

## <a name="connect-to-azure-stack"></a>Se connecter à Azure Stack

Suivez les étapes ci-dessous pour vous connecter à Azure Stack :

1. Inscrivez votre environnement Azure Stack en exécutant la commande `az cloud register`.
   
   a. Pour inscrire l’environnement *administrateur de cloud*, utilisez :

      ```azurecli
      az cloud register \ 
        -n AzureStackAdmin \ 
        --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

   b. Pour inscrire l’environnement *utilisateur*, utilisez :

      ```azurecli
      az cloud register \ 
        -n AzureStackUser \ 
        --endpoint-resource-manager "https://management.local.azurestack.external" \ 
        --suffix-storage-endpoint "local.azurestack.external" \ 
        --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
        --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
      ```

2. Définissez l’environnement actif avec les commandes suivantes.

   a. Pour l’environnement *administrateur de cloud*, utilisez :

      ```azurecli
      az cloud set \
        -n AzureStackAdmin
      ```

   b. Pour l’environnement *utilisateur*, utilisez :

      ```azurecli
      az cloud set \
        -n AzureStackUser
      ```

3. Mettez à jour la configuration de votre environnement pour utiliser le profil de version des API propre à Azure Stack. Pour mettre à jour la configuration, exécutez la commande suivante :

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. Connectez-vous à votre environnement Azure Stack avec la commande `az login`. Vous pouvez vous connecter à l’environnement Azure Stack en tant qu’utilisateur ou que [principal de service](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects). 

   * Connexion en tant *qu’utilisateur* : vous pouvez spécifier directement le nom d’utilisateur et le mot de passe dans la commande `az login` ou vous authentifier avec un navigateur. Vous devez choisir cette dernière solution si l’authentification multifacteur est activée sur votre compte.

      ```azurecli
      az login \
        -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
      ```

      > [!NOTE]
      > Si l’authentification multifacteur est activée sur votre compte d’utilisateur, vous pouvez utiliser la commande `az login command` sans fournir le paramètre `-u`. Elle vous donne une URL et un code qui vous permettent de vous authentifier.
   
   * Connexion en tant que *principal de service* : avant de vous connecter, [créez un principal de service avec le portail Azure](azure-stack-create-service-principals.md) ou l’interface CLI, et attribuez-lui un rôle. Ensuite, connectez-vous avec la commande suivante :

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

## <a name="test-the-connectivity"></a>Tester la connectivité

Maintenant que tout est configuré, nous allons utiliser l’interface CLI pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```azurecli
az group create \
  -n MyResourceGroup -l local
```

Si la création du groupe de ressources réussit, la commande précédente affiche les propriétés suivantes pour la ressource créée :

![Sortie de la création du groupe de ressources](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Problèmes connus
Il existe certains problèmes connus dont vous devez prendre connaissance quand vous utilisez CLI dans Azure Stack :

* Le mode interactif de l’interface CLI, c’est-à-dire la commande `az interactive`, n’est pas encore pris en charge dans Azure Stack.
* Pour obtenir la liste des images de machines virtuelles disponibles dans Azure Stack, utilisez la commande `az vm images list --all` au lieu de la commande `az vm image list`. L’option `--all` permet de faire en sorte que la réponse retourne uniquement les images disponibles dans l’environnement Azure Stack. 
* Les alias d’images de machines virtuelles disponibles dans Azure ne s’appliquent pas forcément à Azure Stack. Si vous utilisez des images de machines virtuelles, vous devez utiliser la totalité du paramètre URN (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) plutôt que l’alias de l’image. Cet URN doit par ailleurs correspondre aux spécifications des images dérivées de la commande `az vm images list`.
* Par défaut, CLI 2.0 utilise « Standard_DS1_v2 » comme taille d’image de machine virtuelle par défaut. Toutefois, cette taille n’est pas encore disponible dans Azure Stack : vous devez donc spécifier le paramètre `--size` explicitement lorsque vous créez une machine virtuelle. Vous pouvez obtenir la liste des tailles de machines virtuelles disponibles dans Azure Stack avec la commande `az vm list-sizes --location <locationName>`.


## <a name="next-steps"></a>Étapes suivantes

[Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)

[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)
