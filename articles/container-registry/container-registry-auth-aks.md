---
title: "S’authentifier avec Azure Container Registry à partir d’Azure Container Service"
description: "Découvrez comment fournir un accès aux images de votre registre de conteneurs privé à partir d’Azure Container Service à l’aide d’un principal de service Azure Active Directory."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.openlocfilehash: a115df87feea0c9f7987e0c65f6f880325d88ca2
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-service"></a>S’authentifier avec Azure Container Registry à partir d’Azure Container Service

Lorsque vous utilisez Azure Container Registry (ACR) avec Azure Container Service (AKS), vous avez besoin d’un mécanisme d’authentification. Ce document décrit en détail les configurations recommandées pour l’authentification entre ces deux services Azure.

## <a name="grant-aks-access-to-acr"></a>Accorder à AKS un accès à ACR

Lors de la création d’un cluster AKS, un principal de service est également créé pour gérer le bon fonctionnement du cluster avec les ressources Azure. Ce principal du service peut également servir à l’authentification sur un registre ACR. Pour ce faire, vous devez créer une attribution de rôle pour accorder au principal du service un accès en lecture à la ressource ACR. 

Vous pouvez utiliser l’exemple suivant pour effectuer cette opération.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Accès à l’aide d’une clé secrète Kubernetes

Dans certains cas, le principal du service utilisé par AKS ne peut pas être étendu dans le registre ACR. Dans ce cas, vous pouvez créer un principal de service unique et l’étendre uniquement au registre ACR.

Le script suivant peut être utilisé pour créer le principal du service. 

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id. 
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource. 
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Les informations d’identification du principal de service peuvent désormais être stockées dans un [secret d’extraction d’images][image-pull-secret] Kubernetes et référencées lors de l’exécution des conteneurs dans un cluster AKS. 

La commande suivante permet de créer le secret Kubernetes. Remplacez le nom du serveur par le nom du serveur de connexion ACR, le nom d’utilisateur par l’identifiant du principal de service et le mot de passe par le mot de passe du principal de service.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Le secret Kubernetes peut être utilisé dans un déploiement de pod à l’aide du paramètre `ImagePullSecrets`. 

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
