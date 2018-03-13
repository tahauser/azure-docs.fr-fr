---
title: "Déployer OpenShift Container Platform dans Azure | Microsoft Docs"
description: "Déployez OpenShift Container Platform dans Azure."
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
ms.openlocfilehash: f1ba6a3d3b9e576d513b55beac4e9365102433e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-openshift-container-platform-in-azure"></a>Déployer OpenShift Container Platform dans Azure

Vous pouvez utiliser plusieurs méthodes pour déployer OpenShift Container Platform dans Azure :

- Vous pouvez déployer manuellement les composants d’infrastructure Azure nécessaires, puis suivre la [documentation](https://docs.openshift.com/container-platform/3.6/welcome/index.html) d’OpenShift Container Platform.
- Vous pouvez également utiliser un [modèle Resource Manager](https://github.com/Microsoft/openshift-container-platform/) existant qui simplifie le déploiement du cluster OpenShift Container Platform.
- Une autre option consiste à utiliser l’[offre de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Pour toutes les options, vous devez avoir un abonnement Red Hat. Pendant le déploiement, l’instance Red Hat Enterprise Linux est inscrite dans l’abonnement Red Hat et associée à l’ID du pool contenant les droits pour OpenShift Container Platform.
Assurez-vous d’avoir un nom d’utilisateur, un mot de passe et un ID de pool RHSM (Red Hat Subscription Manager) valides. Vous pouvez vérifier ces informations en vous connectant à https://access.redhat.com.

## <a name="deploy-by-using-the-openshift-container-platform-resource-manager-template"></a>Déployer à l’aide du modèle Resource Manager d’OpenShift Container Platform

Pour déployer à l’aide du modèle Resource Manager, vous utilisez un fichier de paramètres pour fournir les paramètres d’entrée. Pour personnaliser l’un des éléments de déploiement qui ne sont pas couverts par des paramètres d’entrée, bifurquez le dépôt GitHub et modifiez les éléments appropriés.

Certaines options de personnalisation courantes sont notamment :

- CIDR de réseau virtuel (variable dans azuredeploy.json)
- Taille de machine virtuelle bastion (variable dans azuredeploy.json)
- Conventions de nommage (variables dans azuredeploy.json)
- Caractéristiques de cluster OpenShift, modifiées dans le fichier hosts (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Configurer le fichier de paramètres

Utilisez la valeur de `appId` du principal de service créé précédemment pour le paramètre `aadClientId`. 

L’exemple suivant crée un fichier de paramètres nommé azuredeploy.parameters.json avec toutes les entrées obligatoires.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "true"
        },
        "enableCockpit": {
            "value": "false"
        },
        "rhsmUsernamePasswordOrActivationKey": {
            "value": "usernamepassword"
        },
        "rhsmUsernameOrOrgId": {
            "value": "{RHSM Username}"
        },
        "rhsmPasswordOrActivationKey": {
            "value": "{RHSM Password}"
        },
        "rhsmPoolId": {
            "value": "{Pool ID}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

Remplacez les éléments entre crochets par vos informations spécifiques.

### <a name="deploy-by-using-azure-cli"></a>Déployer à l’aide d’Azure CLI

> [!NOTE] 
> La commande suivante requiert Azure CLI 2.0.8 ou version ultérieure. Pour vérifier la version d’Azure CLI, exécutez la commande `az --version`. Pour mettre à jour l’interface, consultez [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latesti).

L’exemple suivant déploie le cluster OpenShift et toutes les ressources associées dans un groupe de ressources nommé myResourceGroup, avec le nom de déploiement myOpenShiftCluster. Le modèle est référencé directement à partir du dépôt GitHub, et un fichier de paramètres locaux nommé azuredeploy.parameters.json est utilisé.

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

La durée du déploiement varie en fonction du nombre total de nœuds déployés, avec un minimum de 30 minutes. L’URL de la console OpenShift et le nom DNS du maître OpenShift est affiché sur le terminal à l’issue du déploiement.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="deploy-by-using-the-openshift-container-platform-azure-marketplace-offer"></a>Déployer à l’aide de l’offre de la Place de marché Azure pour OpenShift Container Platform

La façon la plus simple de déployer OpenShift Container Platform dans Azure consiste à utiliser l’[offre de la Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Cette option est la plus simple, mais les possibilités de personnalisation sont limitées. Cette offre inclut trois options de configuration en lien avec la taille du cluster :

- **Petite** : déploie un cluster sans haute disponibilité avec un nœud master, un nœud infrastructure, deux nœuds application et un seul nœud bastion. Tous les nœuds ont une taille de machine virtuelle DS2v2 standard. Ce cluster nécessite un total de 10 cœurs et convient parfaitement pour tester à petite échelle.
- **Moyenne** : déploie un cluster à haute disponibilité avec trois nœuds master, deux nœuds infrastructure, quatre nœuds application et un seul nœud bastion. Tous les nœuds, sauf le nœud bastion, ont une taille de machine virtuelle DS3v2 standard. Le nœud bastion est une machine virtuelle DS2v2 standard. Ce cluster requiert 38 cœurs.
- **Grande** : déploie un cluster à haute disponibilité avec trois nœuds master, deux nœuds infrastructure, six nœuds application et un seul nœud bastion. Les nœuds master et infrastructure ont une taille de machine virtuelle DS3v2 standard. Les nœuds application ont une taille de machine virtuelle DS4v2 standard et le nœud bastion est une machine virtuelle DS2v2 standard. Ce cluster requiert 70 cœurs.

La configuration du fournisseur de solution cloud Azure est facultative pour les tailles de cluster moyenne et grande. La petite taille de cluster d’offre pas d’option de configuration du fournisseur de solution cloud Azure.

## <a name="connect-to-the-openshift-cluster"></a>Se connecter au cluster OpenShift

Une fois le déploiement terminé, connectez-vous à la console OpenShift dans un navigateur à l’aide de la valeur `OpenShift Console Uri`. Vous pouvez aussi vous connecter à l’OpenShift master à l’aide de la commande suivante :

```bash
$ ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le cluster OpenShift et toutes les ressources associées quand vous n’en avez plus besoin.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- [Tâches de post-déploiement](./openshift-post-deployment.md)
- [Résoudre les problèmes de déploiement d’OpenShift dans Azure](./openshift-troubleshooting.md)
- [Prise en main d’OpenShift Container Platform](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
