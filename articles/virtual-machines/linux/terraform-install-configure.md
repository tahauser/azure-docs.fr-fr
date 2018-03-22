---
title: "Installer et configurer Terraform pour approvisionner les machines virtuelles et d’autres infrastructures dans Azure | Microsoft Docs"
description: "Découvrez comment installer et configurer Terraform pour créer des ressources Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: dada9c70eef2adb2704e276a5401509581e37538
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installer et configurer Terraform pour approvisionner les machines virtuelles et d’autres infrastructures dans Azure
 
Terraform fournit un moyen simple de définir, de prévisualiser et de déployer l’infrastructure cloud à l’aide d’un [langage de création de modèles simple](https://www.terraform.io/docs/configuration/syntax.html). Cet article décrit les étapes nécessaires pour utiliser Terraform en vue d’approvisionner des ressources dans Azure. 

> [!TIP]
Pour en savoir plus sur l’utilisation de Terraform avec Azure, visitez [Terraform Hub](/azure/terraform). Terraform est installé par défaut dans [Cloud Shell](/azure/terraform/terraform-cloud-shell). Si vous utilisez Cloud Shell, vous pouvez ignorer les sections de ce document sur l’installation et la configuration.

## <a name="install-terraform"></a>Installer Terraform

Pour installer Terraform, [téléchargez](https://www.terraform.io/downloads.html) le package correspondant à votre système d’exploitation dans un répertoire d’installation distinct. Le téléchargement contient un seul fichier exécutable, pour lequel vous devez également définir un chemin d’accès global. Pour obtenir des instructions sur la définition du chemin d’accès sous Linux et Mac, consultez [cette page web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Pour obtenir des instructions sur la définition du chemin d’accès sous Windows, consultez [cette page web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Vérifiez la configuration de votre chemin d’accès à l’aide de la commande `terraform`. Une liste des options Terraform disponibles devrait s’afficher en sortie :

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurer l’accès Terraform à Azure

Configurez [un principal de service Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) pour permettre à Terraform d’approvisionner des ressources dans Azure. Le principal de service autorise vos scripts Terraform utilisant des informations d’identification à approvisionner des ressources dans votre abonnement Azure.

Il existe plusieurs façons de créer une application Azure AD et un principal de service Azure AD. À l’heure actuelle, le moyen le plus simple et le plus rapide consiste à utiliser Azure CLI 2.0, que [vous pouvez télécharger et installer sur Windows, Linux ou un Mac](/cli/azure/install-azure-cli).

Connectez-vous afin de gérer votre abonnement Azure en émettant la commande suivante :

   `az login`

Si vous avez plusieurs abonnements Azure, leurs détails sont retournés par la commande `az login`. Définissez la variable d’environnement `SUBSCRIPTION_ID` pour qu’elle contienne la valeur du champ `id` retourné à partir de l’abonnement que vous souhaitez utiliser. 

Sélectionnez l’abonnement à utiliser pour cette session.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Interrogez le compte pour obtenir les valeurs ID d’abonnement et ID locataire.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Créez ensuite des informations d’identification distinctes pour Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Les valeurs appId, password, sp_name et tenant sont renvoyées. Prenez note des valeurs appId et password.

Pour confirmer vos informations d’identification, ouvrez un nouvel interpréteur de commandes et exécutez la commande suivante, en utilisant les valeurs renvoyées pour sp_name, password et tenant :

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Configurer des variables d’environnement Terraform

Configurez Terraform de sorte à utiliser l’ID de locataire, l’ID d’abonnement, l’ID client et la clé secrète client du principal de service lors de la création de ressources Azure. Vous pouvez également définir l’environnement si vous travaillez sur un cloud Azure autre qu’Azure public. Définissez les variables d’environnement suivantes, qui sont utilisées automatiquement par les [modules Azure Terraform](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

Vous pouvez utiliser cet exemple de script shell pour définir ces variables :

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Exécuter un exemple de script

Créez un fichier `test.tf` dans un répertoire vide et collez-y le script suivant. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Enregistrez le fichier, puis exécutez `terraform init`. Cette commande télécharge les modules Azure nécessaires pour créer un groupe de ressources Azure. La sortie suivante s’affiche :

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Affichez un aperçu du script avec `terraform plan`, puis créez le groupe de ressources `testResouceGroup` avec `terraform apply` :

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Étapes suivantes

Vous avez installé Terraform et configuré les informations d’identification Azure pour pouvoir démarrer le déploiement d’infrastructure dans votre abonnement Azure. Vous avez ensuite testé votre installation en créant un groupe de ressources Azure vide.

> [!div class="nextstepaction"]
> [Créer une machine virtuelle Azure avec Terraform](terraform-create-complete-vm.md)

