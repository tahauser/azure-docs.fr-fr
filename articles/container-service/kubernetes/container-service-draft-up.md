---
title: Utiliser Draft avec Azure Container Service et Azure Container Registry
description: "Créez un cluster Kubernetes ACS et un service Azure Container Registry pour générer votre première application dans Azure avec Draft."
services: container-service
author: squillace
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: de1e8ec1b712aeb5572c7972b22412f2ae90b7b9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Utiliser Draft avec Azure Container Service et Azure Container Registry pour générer et déployer une application dans Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Draft](https://aka.ms/draft) est un nouvel outil open source qui facilite le développement d’applications basées sur des conteneurs et leur déploiement dans des clusters Kubernetes sans nécessiter une connaissance approfondie de Docker et Kubernetes, ni même l’installation de ces outils. L’utilisation d’outils tels que Draft vous permet, à vous et votre équipe, de vous concentrer sur la génération de l’application avec Kubernetes sans avoir à accorder autant d’attention à l’infrastructure.

Vous pouvez utiliser Draft avec n’importe quel registre d’images Docker et n’importe quel cluster Kubernetes, y compris au niveau local. Ce didacticiel montre comment utiliser ACS avec Kubernetes et ACR pour créer un pipeline de développement en direct mais sécurisé dans Kubernetes à l’aide de Draft, et comment utiliser le DNS Azure pour exposer ce pipeline de développement pour que d’autres utilisateurs le voient sur un domaine.


## <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry
Vous pouvez facilement [créer un service Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md), mais les étapes requises sont les suivantes :

1. Créez un groupe de ressources Azure pour gérer votre registre ACR et le cluster Kubernetes dans ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Créez un registre d’images ACR à l’aide de la commande [az acr create](/cli/azure/acr#az_acr_create), puis vérifiez que l’option `--admin-enabled` est définie sur `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Créer un service Azure Container Service avec Kubernetes

Vous voici prêt à utiliser [az acs create](/cli/azure/acs#az_acs_create) pour créer un cluster ACS à l’aide de Kubernetes en tant que valeur `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Étant donné que Kubernetes n’est pas le type d’orchestrator par défaut, veillez à utiliser le commutateur `--orchestrator-type kubernetes`.

Lorsque l’opération a réussi, la sortie ressemble à ce qui suit.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

À présent que vous disposez d’un cluster, vous pouvez importer les informations d’identification à l’aide de la commande [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Vous possédez maintenant un fichier de configuration local pour votre cluster, ce fichier étant requis pour l’exécution des opérations Helm et Draft.

## <a name="install-and-configure-draft"></a>Installer et configurer Draft


1. Téléchargez Draft pour votre environnement à partir de l’URL https://github.com/Azure/draft/releases, puis installez-le dans votre PATH afin de pouvoir utiliser la commande.
2. Téléchargez Helm pour votre environnement à partir de l’URL https://github.com/kubernetes/helm/releases, puis [installez-le dans votre PATH afin de pouvoir utiliser la commande](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Configurez Draft pour l’utilisation de votre registre et la création de sous-domaines pour chaque graphique Helm qu’il génère. Pour configurer Draft, vous devez disposer des éléments suivants :
  - Nom de votre service Azure Container Registry (dans cet exemple, `draftacsdemo`)
  - Votre clé de Registre, ou mot de passe, à partir de `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`

  Appelez `draft init`. Le processus de configuration vous invite à entrer les valeurs ci-dessus. Notez que le format d’URL pour l’URL du Registre est le nom du Registre (dans cet exemple, `draftacsdemo`) plus `.azurecr.io`. Votre nom d’utilisateur est le nom du Registre. La première fois que vous l’exécutez, le processus se présente comme suit.
 ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
```

Vous voici prêt à déployer une application.


## <a name="build-and-deploy-an-application"></a>Générer et déployer une application

Le référentiel Draft comporte [six exemples d’applications simples](https://github.com/Azure/draft/tree/master/examples). Clonez le référentiel, puis utilisez l’[exemple Java](https://github.com/Azure/draft/tree/master/examples/java). Accédez au répertoire examples/java, puis tapez `draft create` pour générer l’application. La sortie doit ressembler à l’exemple qui suit.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

La sortie inclut un fichier Dockerfile et un graphique Helm. Pour générer et déployer l’application, il vous suffit de taper `draft up`. La sortie est volumineuse, mais doit ressembler à l’exemple suivant.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Afficher votre application en toute sécurité

Le conteneur est en cours d’exécution dans ACS. Pour l’afficher, utilisez la commande `draft connect` qui crée une connexion sécurisée à l’adresse IP du cluster avec un port spécifique pour votre application afin que vous puissiez l’afficher localement. Si l’opération réussit, recherchez l’URL de connexion à votre application sur la première ligne après l’indicateur **SUCCESS**.

> [!NOTE]
> Si vous recevez un message indiquant qu’aucun POD n’était prêt, attendez un moment, puis réessayer. Vous pouvez également observer les pods qui deviennent prêts avec `kubectl get pods -w`, puis recommencer l’opération quand ils le sont.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Dans l’exemple précédent, vous pourriez taper `curl -s http://localhost:46143` pour recevoir la réponse, `Hello World, I'm Java!`. Lorsque vous appuyez sur CTRL+C ou CMD+C (selon votre système d’exploitation), le tunnel sécurisé est détruit et vous pouvez poursuivre l’itération.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Partage de votre application en configurant un domaine de déploiement avec le DNS Azure

Vous avez déjà appliqué la boucle d’itération de développement que Draft crée dans les étapes précédentes. Cependant, vous pouvez partager votre application sur internet en effectuant les opérations suivantes :
1. Installation d’une entrée dans votre cluster ACS (pour fournir une adresse IP publique à laquelle afficher l’application)
2. Délégation de votre domaine personnalisé au DNS Azure et mappage de votre domaine à l’adresse IP qu’ACS assigne à votre contrôleur d’entrée

### <a name="use-helm-to-install-the-ingress-controller"></a>Utilisez helm pour installer le contrôleur d’entrée.
Utilisez **Helm** pour rechercher et installer `stable/traefik`, un contrôleur d’entrée, pour autoriser les demandes entrantes de vos builds.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
À présent, définissez un espion sur le contrôleur `ingress` pour capturer la valeur IP externe lors de son déploiement. Cette adresse IP correspondra à celle [mappée sur votre domaine de déploiement](#wire-up-deployment-domain) à la section suivante.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

Dans ce cas précis, l’adresse IP externe du domaine de déploiement est `13.64.108.240`. Maintenant, vous pouvez mapper votre domaine sur cette adresse IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapper l’adresse IP d’entrée à un sous-domaine personnalisé

Draft crée une version pour chaque graphique Helm qu’il génère, autrement dit pour chaque application sur laquelle vous travaillez. Chacune d’elles obtient un nom généré qui est utilisé par **Draft** en tant que _sous-domaine_ au-dessus du _domaine de déploiement_ racine que vous contrôlez. (Dans cet exemple, nous utilisons `squillace.io` comme domaine de déploiement.) Pour autoriser ce comportement de sous-domaine, vous devez créer un enregistrement A pour `'*.draft'` dans vos entrées DNS pour votre domaine de déploiement, afin que chaque sous-domaine généré soit routé vers le contrôleur d’entrée du cluster Kubernetes. 

Votre fournisseur de domaine dispose de sa propre méthode pour affecter des serveurs DNS ; pour [déléguer vos serveurs de noms de domaine à Azure DNS](../../dns/dns-delegate-domain-azure-dns.md), procédez comme suit :

1. Créez un groupe de ressources pour votre zone.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Créez une zone DNS pour votre domaine.
Utilisez la commande [az network dns zone create](/cli/azure/network/dns/zone#az_network_dns_zone_create) pour obtenir les serveurs de noms afin de déléguer le contrôle DNS à Azure DNS pour votre domaine.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Ajoutez les serveurs DNS qui vous sont affectés au fournisseur de domaine de votre domaine de déploiement, ce qui vous permet d’utiliser Azure DNS pour repointer votre domaine à votre convenance. La façon de procéder varie selon le fournisseur du domaine. Pour plus d’informations utiles, voir [Délégation de domaine à DNS Azure](../../dns/dns-delegate-domain-azure-dns.md). 
4. Une fois votre domaine délégué à DNS Azure, créez une entrée de jeu d’enregistrements A pour le mappage de votre domaine de déploiement à l’adresse IP `ingress` définie à l’étape 2 de la section précédente.
  ```azurecli
  az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
  ```
Le résultat ressemble à ceci :
  ```json
  {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
  }
  ```
5. Réinstallez **draft**.

   1. Supprimez **draft** du cluster en tapant `helm delete --purge draft`. 
   2. Réinstallez **draft** à l’aide de la même commande `draft-init`, mais avec l’option `--ingress-enabled`:
    ```bash
    draft init --ingress-enabled
    ```
   Répondez aux invites comme vous l’avez fait la première fois. Toutefois, vous avez une question de plus à laquelle répondre en utilisant le chemin d’accès complet du domaine que vous avez configuré avec le DNS Azure.

6. Entrez votre domaine de niveau supérieur pour l’entrée (par exemple, draft.example.com) : draft.squillace.io
7. Cette fois, lorsque vous appelez `draft up`, vous pouvez voir votre application (ou lui appliquer l’action `curl`) à l’URL sous la forme `<appname>.draft.<domain>.<top-level-domain>`. Dans le cas de cet exemple, `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un cluster Kubernetes ACS, vous pouvez utiliser [Azure Container Registry](../../container-registry/container-registry-intro.md) pour découvrir comment créer d’autres déploiements distincts de ce scénario. Par exemple, vous pouvez créer un jeu d’enregistrements DNS de domaine draft._basedomain.toplevel_ qui contrôle les éléments d’un sous-domaine plus profond pour des déploiements ACS spécifiques.






