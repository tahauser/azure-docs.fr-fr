---
title: Utiliser Draft avec AKS et Azure Container Registry
description: Utiliser Draft avec AKS et Azure Container Registry
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: df5614d8a708b49ee1368c4d7983f45d29920fd8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Utiliser Draft avec Azure Container Service (AKS)

Draft est un outil open source qui facilite l'empaquetage et l’exécuter du code dans un cluster Kubernetes. Draft s'applique au cycle d’itération de développement, pendant le développement du code, mais avant le contrôle de version. Avec Draft, vous pouvez rapidement redéployer une application sur Kubernetes à mesure que le code est modifié. Pour plus d’informations sur Draft, consultez la [documentation Draft sur GitHub](https://github.com/Azure/draft/tree/master/docs).

Ce document détaille l'utilisation de Draft avec un cluster Kubernetes sur AKS.

## <a name="prerequisites"></a>Composants requis

Les étapes détaillées dans ce document supposent que vous ayez créé un cluster ACS et que vous ayez établi une connexion kubectl avec le cluster. Si vous avez besoin de ces éléments, reportez-vous au [démarrage rapide d’ACS](./kubernetes-walkthrough.md).

Vous avez également besoin d'un registre Docker privé dans Azure Container Registry (ACR). Pour obtenir des instructions sur le déploiement d’une instance ACR, consultez [Démarrage rapide d'Azure Container Registry](../container-registry/container-registry-get-started-azure-cli.md).

## <a name="install-helm"></a>Installer Helm

L’interface CLI Helm est un client qui s’exécute sur votre système de développement et vous permet de démarrer, d’arrêter et de gérer des applications avec des graphiques Helm.

Pour installer l’interface CLI Helm sur un Mac, utilisez `brew`. Pour connaître les autres options d’installation, consultez options [Installing Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) (Installation de Helm).

```console
brew install kubernetes-helm
```

Output:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Installer Draft

L’interface CLI Draft est un client qui s’exécute sur votre système de développement et vous permet de rapidement déployer un code dans un cluster Kubernetes.

Pour installer l’interface CLI Draft sur un Mac, utilisez `brew`. Pour connaître les autres options d’installation, consultez le [guide d'installation de Draft](https://github.com/Azure/draft/blob/master/docs/install.md).

```console
brew install draft
```

Output:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Configurer Draft

Lors de la configuration de Draft, un registre de conteneurs doit être spécifié. Dans cet exemple, nous utilisons Azure Container Registry.

Exécutez la commande suivante pour obtenir le nom et le nom du serveur de connexion de votre instance ACR. Mettez à jour la commande avec le nom du groupe de ressources contenant votre instance ACR.

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

Le mot de passe de l'instance ACR est également nécessaire.

Exécutez la commande suivante pour retourner le mot de passe ACR. Mettez à jour la commande avec le nom de l'instance ACR.

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

Initialisez Draft avec la commande `draft init`.

```console
draft init
```

Pendant ce processus, vous êtes invité à saisir les informations d’identification du registre de conteneurs. Lorsque vous utilisez un registre Azure Container Registry, l’URL du registre est le nom du serveur de connexion ACR, le nom d’utilisateur est le nom de l'instance ACR, et le mot de passe est le mot de passe ACR.

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

Une fois le processus terminé, Draft est configuré dans le cluster Kubernetes et prêt à être utilisé.

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>Exécuter une application

Le référentiel Draft contient plusieurs exemples d’applications qui peuvent être utilisés lors d'une démonstration de Draft. Créez une copie clonée du référentiel.

```console
git clone https://github.com/Azure/draft
```

Accédez au répertoire d’exemples Java.

```console
cd draft/examples/java/
```

Utilisez la commande `draft create` pour démarrer le processus. Cette commande crée les artefacts utilisés pour exécuter l’application dans un cluster Kubernetes. Ces éléments incluent un fichier Dockerfile, un graphique Helm et un fichier `draft.toml` (le fichier de configuration Draft).

```console
draft create
```

Output:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Pour exécuter l’application sur un cluster Kubernetes, utilisez la commande `draft up`. Cette commande charge le code de l'application et les fichiers de configuration sur le cluster Kubernetes. Elle exécute ensuite le fichier Dockerfile pour créer une image de conteneur, envoie l’image vers le registre de conteneurs, puis exécute le graphique Helm pour démarrer l’application.

```console
draft up
```

Output:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>Test de l'application

Pour tester l’application, utilisez la commande `draft connect`. Cette commande génère un proxy pour établir une connexion au pod Kubernetes et permettre une connexion locale sécurisée. Lorsque ce processus est terminé, l’application est accessible via l’URL fournie.

Dans certains cas, le téléchargement de l’image de conteneur et le démarrage de l’application peuvent prendre plusieurs minutes. Si vous recevez une erreur lors de l’accès à l’application, relancez la connexion.

```console
draft connect
```

Output:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Lorsque vous avez fini de tester l'application, utilisez la commande `Control+C` pour interrompre la connexion proxy.

## <a name="expose-application"></a>Exposer l’application

Lorsque vous testez une application dans Kubernetes, vous pouvez rendre l’application disponible sur Internet. Pour cela, vous utilisez un service Kubernetes de type [LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer) ou un [contrôleur d’entrée](https://kubernetes.io/docs/concepts/services-networking/ingress/). Ce document détaille l'utilisation d’un service Kubernetes.


Tout d’abord, le pack Draft doit être mis à jour pour indiquer qu’un service de type `LoadBalancer` doit être créé. Pour cela, mettez à jour le type de service dans le fichier `values.yaml`.

```console
vi chart/java/values.yaml
```

Recherchez la propriété `service.type` et remplacez la valeur `ClusterIP` par `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Exécutez `draft up` pour relancer l'application.

```console
draft up
```

Le renvoi d'une adresse IP publique par le service peut prendre plusieurs minutes. Pour suivre la progression de l'opération, utilisez la commande `kubectl get service` avec un espion.

```console
kubectl get service -w
```

Au début, l’*adresse IP externe* pour le service apparaît comme `pending`.

```
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Une fois que l’adresse IP externe est passée du statut `pending` à `IP address`, utilisez `Control+C` pour arrêter le processus de surveillance kubectl.

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Pour afficher l’application, accédez à l’adresse IP externe.

```console
curl 52.175.224.118
```

Output:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Itération sur l’application

Maintenant que Draft a été configuré et que l’application est en cours d’exécution dans Kubernetes, vous pouvez passer à l’itération du code. Chaque fois que vous souhaitez tester le code mis à jour, exécutez la commande `draft up` pour mettre à jour de l’application en cours d’exécution.

Pour cet exemple, mettez à jour l’application Java Hello World.

```console
vi src/main/java/helloworld/Hello.java
```

Mettez à jour le texte Hello World.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

Exécutez la commande `draft up` pour redéployer l’application.

```console
draft up
```

Sortie

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

Enfin, affichez l’application pour voir les mises à jour.

```console
curl 52.175.224.118
```

Output:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l'utilisation de Draft, consultez la documentation Draft sur GitHub.

> [!div class="nextstepaction"]
> [Documentation Draft](https://github.com/Azure/draft/tree/master/docs)