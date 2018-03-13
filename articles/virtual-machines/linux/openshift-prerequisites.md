---
title: "Conditions préalables à l’utilisation d’OpenShift | Microsoft Docs"
description: "Conditions préalables requises au déploiement d’OpenShift dans Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 467428462260596f21ba59f49e3c48b5fc2526b6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Prérequis courants pour déployer OpenShift dans Azure

Cet article décrit les prérequis courants pour déployer OpenShift Origin ou OpenShift Container Platform dans Azure.

L’installation d’OpenShift utilise des playbooks Ansible. Ansible utilise Secure Shell (SSH) pour se connecter à tous les hôtes de cluster pour effectuer les étapes d’installation.

Lorsque vous lancez la connexion SSH aux hôtes distants, vous ne pouvez pas entrer de mot de passe. C’est pour cela qu’aucun mot de passe ne peut être associé à la clé privée sans provoquer l’échec du déploiement.

Puisque les machines virtuelles sont déployées via des modèles Azure Resource Manager, la même clé publique est utilisée pour accéder à l’ensemble des machines virtuelles. Vous devez injecter la clé privée correspondante dans la machine virtuelle qui exécute également tous les playbooks. Pour y parvenir en toute sécurité, nous utilisons un coffre de clés Azure pour transmettre la clé privée à la machine virtuelle.

Si un stockage persistant est nécessaire pour les conteneurs, alors des volumes persistants sont nécessaires. OpenShift prend en charge les disques durs virtuels (VHD) Azure pour cette fonctionnalité, mais Azure doit d’abord être configuré en tant que fournisseur cloud. 

Dans ce modèle, OpenShift :

- Crée un objet VHD dans un compte de stockage Azure.
- Monte le disque dur virtuel sur une machine virtuelle et formate le volume.
- Monte le volume sur le pod.

Pour que cette configuration fonctionne, OpenShift a besoin d’autorisations pour effectuer les tâches précédentes dans Azure. Pour cela, utilisez un principal de service. Le principal de service est un compte de sécurité dans Azure Active Directory qui dispose d’autorisations sur les ressources.

Le principal de service a besoin d’accéder aux comptes de stockage et aux machines virtuelles qui composent le cluster. Si toutes les ressources de cluster OpenShift sont déployées sur un seul groupe de ressources, des autorisations pour ce groupe de ressources peuvent être affectées au principal de service.

Ce guide décrit comment créer les artefacts associés aux prérequis.

> [!div class="checklist"]
> * Créez un coffre de clés pour gérer les clés SSH pour le cluster OpenShift.
> * Créez un principal de service pour que le fournisseur de solution cloud Azure l’utilise.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure 
Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#az_login) et suivez les instructions à l’écran ou cliquez sur **Essayer** pour utiliser Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Vous utilisez un groupe de ressources dédié pour héberger le coffre de clés. Ce groupe est différent du groupe de ressources dans lequel se déploient les ressources de cluster OpenShift. 

L’exemple suivant crée un groupe de ressources nommé *keyvaultrg* à l’emplacement *eastus* :

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Création d’un coffre de clés
Créez un coffre de clés pour stocker les clés SSH du cluster avec la commande [az keyvault create](/cli/azure/keyvault#az_keyvault_create). Le nom du coffre de clés doit être globalement unique.

L’exemple suivant crée un coffre de clés nommé *keyvault* dans le groupe de ressources *keyvaultrg* :

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Création d’une clé SSH 
Une clé SSH est nécessaire pour sécuriser l’accès au cluster OpenShift Origin. Créez une paire de clés SSH à l’aide de la commande `ssh-keygen` (sur Linux ou macOS) :
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Votre paire de clés SSH ne peut pas avoir de mot de passe.

Pour plus d’informations sur les clés SSH sur Windows, consultez [Guide pratique pour créer des clés SSH sur Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Stocker la clé privée SSH dans Azure Key Vault
Le déploiement OpenShift utilise la clé SSH que vous avez créée pour sécuriser l’accès au maître OpenShift. Pour permettre au déploiement de récupérer la clé SSH en toute sécurité, stockez la clé dans Key Vault à l’aide de la commande suivante :

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Créer un principal du service 
OpenShift communique avec Azure à l’aide d’un nom d’utilisateur et d’un mot de passe ou d’un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme OpenShift. Vous contrôlez et vous définissez les opérations que le principal du service est autorisé à effectuer dans Azure. Pour renforcer la sécurité par rapport à la simple saisie d’un nom d’utilisateur et d’un mot de passe, cet exemple crée un principal de service de base.

Créez un principal de service avec la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) et affichez les informations d’identification requises par OpenShift.

L’exemple suivant crée un service principal et lui assigne des autorisations de collaborateur à un groupe de ressources nommé myResourceGroup. Si vous utilisez Windows, exécutez ```az group show --name myResourceGroup --query id``` séparément et utilisez la sortie pour alimenter l’option --scopes.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Prenez note de la propriété appId renvoyée par la commande :
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Veillez à créer un mot de passe sécurisé. Suivez les conseils en matière de [Stratégies et restrictions de mot de passe dans Azure Active Directory](/azure/active-directory/active-directory-passwords-policy).

Pour plus d’informations sur les principaux de service, consultez [Créer un principal du service avec Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="next-steps"></a>Étapes suivantes

Cet article a abordé les thèmes suivants :
> [!div class="checklist"]
> * Créez un coffre de clés pour gérer les clés SSH pour le cluster OpenShift.
> * Créez un principal de service pour que le fournisseur de solution cloud Azure l’utilise.

Ensuite, déployez un cluster OpenShift :

- [Déployer OpenShift Origin](./openshift-origin.md)
- [Déployer OpenShift Container Platform](./openshift-container-platform.md)

