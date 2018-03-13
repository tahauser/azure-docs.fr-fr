---
title: Principal de service de cluster Azure Kubernetes
description: "Créer et gérer un principal de service Azure Active Directory pour un cluster Kubernetes dans Azure Container Service"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 02588ca770ae519615360ce3e8935231aa74f8cf
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>Configurer un principal de service Azure Active Directory pour un cluster Kubernetes dans Container Service

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Dans Azure Container Service, un cluster Kubernetes nécessite un [principal de service Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md) pour interagir avec des API Azure. Le principal de service est nécessaire pour gérer dynamiquement des ressources telles que des [itinéraires définis par l’utilisateur](../../virtual-network/virtual-networks-udr-overview.md) et [Azure Load Balancer de couche 4](../../load-balancer/load-balancer-overview.md).


Cet article indique les différentes options permettant de configurer un principal de service pour votre cluster Kubernetes. Par exemple, si vous avez installé et configuré [Azure CLI 2.0](/cli/azure/install-az-cli2), vous pouvez exécuter la commande [`az acs create`](/cli/azure/acs#az_acs_create) pour créer le cluster Kubernetes et le principal du service en même temps.


## <a name="requirements-for-the-service-principal"></a>Configuration requise pour le principal du service

Vous pouvez utiliser un principal de service Azure AD existant qui répond aux exigences ci-dessous ou en créer un.

* **Portée** : groupe de ressources

* **Rôle** : collaborateur

* **Clé secrète client** : doit être un mot de passe. Actuellement, vous ne pouvez pas utiliser de principal du service configuré pour l’authentification par certificat.

> [!IMPORTANT]
> Pour créer un principal de service, vous devez disposer des autorisations suffisantes pour inscrire une application auprès de votre client Azure AD et l’affecter à un rôle dans votre abonnement. Si voir si vous disposez des autorisations requises, [procédez à une vérification dans le portail](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Option 1 : créer un principal de service dans Azure Active Directory

Si vous souhaitez créer un principal de service Azure AD avant de déployer votre cluster Kubernetes, Azure propose plusieurs méthodes.

L’exemple de commande suivant vous montre comment procéder avec [l’Azure CLI 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). Vous pouvez également créer un principal de service à l’aide [d’Azure PowerShell](../../azure-resource-manager/resource-group-authenticate-service-principal.md), du [portail](../../azure-resource-manager/resource-group-create-service-principal-portal.md) ou d’autres méthodes.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>"
```

Le résultat est semblable à ce qui suit (illustré ici de manière rédigée) :

![Créer un principal du service](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

**L’ID client** (`appId`) et la **clé secrète client** (`password`) que vous utilisez en tant que paramètres du principal du service pour le déploiement du cluster sont mis en surbrillance.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Spécifier un principal de service lors de la création du cluster Kubernetes

Fournissez **l’ID client** (également appelé `appId`, pour l’ID de l’application) et la **clé secrète client** (`password`) d’un principal du service existant en tant que paramètres lors de la création du cluster Kubernetes. Veillez à que le principal de service réponde aux exigences indiquées au début cet article.

Vous pouvez spécifier ces paramètres lors du déploiement du cluster Kubernetes à l’aide [d’Azure CLI 2.0](container-service-kubernetes-walkthrough.md), du [portail Azure](../dcos-swarm/container-service-deployment.md) ou d’autres méthodes.

>[!TIP]
>Lorsque vous spécifiez **l’ID client**, veillez à utiliser l’`appId`, et non l’`ObjectId`, du principal du service.
>

L’exemple suivant illustre une manière de transmettre les paramètres avec Azure CLI 2.0. Cet exemple utilise le [modèle de démarrage rapide Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Téléchargez](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) le fichier de paramètres de modèle `azuredeploy.parameters.json` à partir de GitHub.

2. Pour spécifier le principal du service, entrez les valeurs pour `servicePrincipalClientId` et `servicePrincipalClientSecret` dans le fichier (vous devez également fournir vos propres valeurs pour `dnsNamePrefix` et `sshRSAPublicKey`. La clé en question est la clé publique SSH pour accéder au cluster). Enregistrez le fichier .

    ![Transmettez les paramètres du principal du service](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Exécutez la commande suivante à l’aide de `--parameters` pour définir le chemin d’accès au fichier azuredeploy.parameters.json. Cette commande déploie le cluster dans un groupe de ressources que vous créez appelé `myResourceGroup` dans la région États-Unis de l’Ouest.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Option 2 : générer un principal de service lors de la création du cluster avec `az acs create`

Si vous exécutez la commande [`az acs create`](/cli/azure/acs#az_acs_create) pour créer le cluster Kubernetes, vous pouvez générer un principal de service automatiquement.

Comme avec d’autres options de création de clusters Kubernetes, vous pouvez spécifier des paramètres pour un principal du service existant lorsque vous exécutez `az acs create`. Toutefois, si vous omettez ces paramètres, Azure CLI en crée un automatiquement à utiliser avec Container Service. Cette procédure est réalisée en toute transparence au cours du déploiement.

La commande suivante crée un cluster Kubernetes et génère des clés SSH et des informations d’identification du principal du service :

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Si votre compte ne dispose pas des autorisations Azure AD ni des autorisations d’abonnement pour créer un principal de service, la commande génère une erreur semblable à `Insufficient privileges to complete the operation.`
>

## <a name="additional-considerations"></a>Considérations supplémentaires

* Si vous ne disposez pas des autorisations pour créer un principal de service dans votre abonnement, vous devrez peut-être demander à votre administrateur Azure AD ou à l’administrateur de votre abonnement de vous attribuer les autorisations nécessaires ou de créer un principal de service à utiliser avec Azure Container Service.

* Le principal de service pour Kubernetes fait partie de la configuration du cluster. Toutefois, n’utilisez pas l’identité pour déployer le cluster.

* Chaque principal de service est associé à une application Azure AD. Le principal de service pour un cluster Kubernetes peut être associé à tout nom d’application Azure AD valide (par exemple : `https://www.contoso.org/example`). L’URL de l’application ne doit pas être un point de terminaison réel.

* Lorsque vous spécifiez **l’ID Client** du principal de service, vous pouvez utiliser la valeur de `appId` (comme indiqué dans cet article) ou le principal de service correspondant `name` (par exemple, `https://www.contoso.org/example`).

* Sur les machines virtuelles principales et d’agent du cluster Kubernetes, les informations d’identification du principal de service sont stockées dans le fichier `/etc/kubernetes/azure.json`.

* Si vous utilisez la commande `az acs create` pour générer automatiquement le principal de service, les informations d’identification du principal de service sont écrites dans le fichier `~/.azure/acsServicePrincipal.json` sur la machine utilisée pour exécuter la commande.

* Si vous utilisez la commande `az acs create` pour générer automatiquement le principal de service, ce dernier peut également s’authentifier auprès d’un registre [Azure Container Registry](../../container-registry/container-registry-intro.md) créé dans le même abonnement.

* Les informations d’identification du principal de service peuvent expirer, vos nœuds de cluster passent alors à l’état **NotReady**. Consultez la section [Expiration des informations d’identification](#credential-expiration) pour plus d’informations d’atténuation.

## <a name="credential-expiration"></a>Expiration des informations d’identification

Sauf si vous spécifiez une fenêtre de validité personnalisée avec le paramètre `--years` lorsque vous créez un principal de service, ses informations d’identification sont valides pendant 1 an à compter de la création. Lorsque les informations d’identification expirent, vos nœuds de cluster peuvent passer à l’état **NotReady**.

Pour vérifier la date d’expiration d’un principal de service, exécutez la commande [az ad app show](/cli/azure/ad/app#az_ad_app_show) avec le paramètre `--debug`, puis recherchez la valeur `endDate` de `passwordCredentials` au bas de la sortie :

```azurecli
az ad app show --id <appId> --debug
```

Sortie (tronquée ici) :

```json
...
"passwordCredentials":[{"customKeyIdentifier":null,"endDate":"2018-11-20T23:29:49.316176Z"
...
```

Si les informations d’identification de votre principal de service ont expiré, utilisez la commande [az ad sp reset-credentials](/cli/azure/ad/sp#az_ad_sp_reset_credentials) pour mettre à jour les informations d’identification :

```azurecli
az ad sp reset-credentials --name <appId>
```

Output:

```json
{
  "appId": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "name": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "password": "404203c3-0000-0000-0000-d1d2956f3606",
  "tenant": "72f988bf-0000-0000-0000b-2d7cd011db47"
}
```

Ensuite, mettez `/etc/kubernetes/azure.json` à jour avec les nouvelles informations d’identification sur tous les nœuds de cluster et redémarrez les nœuds.

## <a name="next-steps"></a>Étapes suivantes

* [Prise en main de Kubernetes](container-service-kubernetes-walkthrough.md) dans votre cluster de service de conteneur.

* Pour résoudre les problèmes du principal de service pour Kubernetes, consultez la [documentation du moteur ACS](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).