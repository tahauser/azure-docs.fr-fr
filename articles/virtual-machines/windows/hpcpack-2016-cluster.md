---
title: Cluster HPC Pack 2016 dans Azure | Microsoft Docs
description: Découvrez comment déployer un cluster HPC Pack 2016 dans Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 03/09/2018
ms.author: danlep
ms.openlocfilehash: c26dd85d896445e19efb9906d953fd535fc1fb5c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Déployer un cluster HPC Pack 2016 dans Azure

Suivez les étapes de cet article pour déployer un cluster [Microsoft HPC Pack 2016 Update 1](https://technet.microsoft.com/library/cc514029) sur des machines virtuelles Azure. HPC Pack est la solution HPC gratuite de Microsoft basée sur les technologies Microsoft Azure et Windows Server. Elle prend en charge un vaste éventail de charges de travail HPC.

Utilisez un des [modèles Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) pour déployer le cluster HPC Pack 2016. Selon la topologie de cluster choisie, le nombre et les types de nœuds principaux de cluster et de nœuds de calcul peuvent varier.

## <a name="prerequisites"></a>Prérequis


### <a name="pfx-certificate"></a>Certificat PFX

Un cluster Microsoft HPC Pack 2016 nécessite un certificat d’échange d’informations personnelles (PFX) pour sécuriser la communication entre les nœuds HPC. Le certificat doit répondre aux exigences suivantes :

* Il doit avoir une clé privée capable d’échanger des clés.
* L’utilisation de clés comprend la signature numérique et le chiffrage de clés.
* L’utilisation améliorée de la clé inclut l’authentification cliente et serveur.

Si vous ne disposez pas d’un certificat conforme à ces exigences, vous pouvez demander le certificat auprès d’une autorité de certification. Sinon, utilisez les commandes suivantes pour générer le certificat auto-signé en fonction du système d’exploitation sur lequel vous exécutez la commande. Ensuite, exportez le certificat dans un fichier PFX protégé par mot de passe avec une clé privée.

* **Pour Windows 10 ou Windows Server 2016**, exécutez l’applet de commande **New-SelfSignedCertificate** PowerShell intégrée comme suit :

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **Pour les systèmes d’exploitation antérieurs à Windows 10 ou Windows Server 2016**, téléchargez le [générateur de certificat auto-signé](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) à partir du centre de scripts Microsoft. Extrayez son contenu et exécutez les commandes suivantes à l’invite de PowerShell :

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

Une fois que vous avez créé le certificat dans le magasin de l’utilisateur actuel, utilisez le composant logiciel enfichable Certificats pour exporter le certificat dans un fichier PFX protégé par mot de passe avec la clé privée. Vous pouvez aussi exporter le certificat à l’aide de l’applet de commande PowerShell [Export-Pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps).

### <a name="upload-certificate-to-an-azure-key-vault"></a>Chargement d’un certificat vers Azure Key Vault

Avant de déployer le cluster HPC, chargez le certificat PFX dans [Azure Key Vault](../../key-vault/index.md) en tant que secret, puis enregistrez les informations suivantes à utiliser durant le déploiement : **nom du coffre**, **groupe de ressources du coffre**, **URL du certificat** et **empreinte numérique du certificat**.

L’exemple de script PowerShell ci-dessous charge le certificat, crée le coffre de clés et génère les informations requises. Pour en savoir plus sur le téléchargement d’un certificat dans Azure Key Vault, voir [Prise en main d’Azure Key Vault](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Topologies prises en charge

Choisissez un des [modèles Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) pour déployer le cluster HPC Pack 2016. Voici les architectures générales de trois exemples de topologies de cluster. Les topologies à haute disponibilité incluent plusieurs nœuds principaux de cluster.

1. Cluster haute disponibilité avec un domaine Active Directory

    ![Cluster haute disponibilité dans un domaine AD](./media/hpcpack-2016-cluster/haad.png)


2. Cluster haute disponibilité sans domaine Active Directory

    ![Cluster haute disponibilité sans domaine AD](./media/hpcpack-2016-cluster/hanoad.png)

3. Cluster avec un seul nœud principal

   ![Cluster avec un seul nœud principal](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Déployer un cluster

Pour créer le cluster, choisissez un modèle et cliquez sur **Déployer sur Azure**. Dans le [portail Azure](https://portal.azure.com), spécifiez les paramètres du modèle comme décrit dans les étapes suivantes. Chaque modèle crée toutes les ressources Azure requises pour l’infrastructure de cluster HPC. Les ressources incluent un réseau virtuel Azure, une adresse IP publique, un équilibreur de charge (uniquement pour un cluster haute disponibilité), des interfaces réseau, des groupes à haute disponibilité, des comptes de stockage et des machines virtuelles.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Étape 1 : Sélectionner l’abonnement, l’emplacement et le groupe de ressources

L’**abonnement** et l’**emplacement** doivent être les mêmes que ceux que vous avez spécifiés lorsque vous avez téléchargé votre certificat PFX (voir Configuration requise). Nous vous recommandons de créer un **groupe de ressources** différent pour le déploiement.

### <a name="step-2-specify-the-parameter-settings"></a>Étape 2 : Spécifier les paramètres

Saisissez ou modifiez les valeurs des paramètres du modèle. Cliquez sur l’icône en regard de chaque paramètre pour obtenir de l’aide. Voir aussi les conseils sur les [tailles de machines virtuelles disponibles](sizes.md).

Spécifiez les valeurs que vous avez enregistrées dans les conditions préalables pour les paramètres suivants : **nom du coffre**, **groupe de ressources du coffre**, **URL du certificat** et **empreinte numérique du certificat**.

### <a name="step-3-review-terms-and-create"></a>Étape 3. Passer en revue les termes et conditions, et commencer
Passez en revue les termes et conditions associés au modèle. Si vous les acceptez, cliquez sur **Acheter** pour commencer le déploiement.

Le déploiement peut prendre 30 minutes ou plus de temps selon la topologie de cluster.

## <a name="connect-to-the-cluster"></a>Connexion au cluster
1. Une fois que le cluster HPC Pack est déployé, accédez au [portail Azure](https://portal.azure.com). Cliquez sur **Groupes de ressources**, puis recherchez le groupe de ressources dans lequel le cluster a été déployé. Vous pouvez rechercher les machines virtuelles avec nœud principal.

    ![Nœuds principaux de cluster dans le portail](./media/hpcpack-2016-cluster/clusterhns.png)

2. Cliquez sur un nœud principal (dans un cluster haute disponibilité, cliquez sur l’un de ceux disponibles). Dans **Vue d’ensemble**, vous pouvez trouver l’adresse IP publique ou le nom DNS complet du cluster.

    ![Paramètres de connexion au cluster](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Cliquez sur **Se connecter** pour vous connecter aux nœuds principaux à l’aide du Bureau à distance avec votre nom d’administrateur. Si le cluster que vous avez déployé est dans un domaine Active Directory, le nom d’utilisateur est au format \<privateDomainName>\\\<adminUsername> (par exemple, hpc.local\hpcadmin).

## <a name="next-steps"></a>Étapes suivantes
* Envoyez des travaux au cluster. Voir [Envoyer des travaux à un cluster HPC Pack dans Azure](hpcpack-cluster-submit-jobs.md) et [Gérer un cluster HPC Pack 2016 dans Azure avec Azure Active Directory](hpcpack-cluster-active-directory.md).

