---
title: "Démarrage rapide - Créer un registre Docker privé dans Azure avec le portail Azure"
description: "Apprenez rapidement à créer un registre de conteneurs Docker privé avec le portail Azure."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: db112f7f8f486093509a86f9781c30133925c25f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Créer un registre de conteneur à l’aide du portail Azure

Un registre Azure Container Registry est un registre Docker privé dans Azure, dans lequel vous pouvez stocker et gérer vos images conteneurs Docker privées. Dans ce démarrage rapide, vous allez créer un registre de conteneurs avec le portail Azure, envoyer une image de conteneur dans le registre et enfin déployer le conteneur dans Azure Container Instances (ACI) à partir de votre registre.

Pour suivre cette procédure de démarrage rapide, Docker doit être installé en local. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure depuis l’adresse https://portal.azure.com.

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Sélectionnez **Créer une ressource** > **Conteneurs** > **Azure Container Registry**.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-01]

Entrez les valeurs **Nom du registre** et **Groupe de ressources**. Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Créez un groupe de ressources nommé `myResourceGroup`, et pour **Référence SKU**, sélectionnez « De base ». Sélectionnez **Créer** pour déployer l’instance ACR.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-03]

Dans le cadre de ce guide de démarrage rapide, nous allons créer un registre *De base*. Azure Container Registry est disponible dans plusieurs références SKU, qui sont brièvement décrites dans le tableau suivant. Pour plus d’informations sur chaque référence, consultez [Références SKU de registres de conteneurs][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Lorsque le message **Déploiement réussi** s’affiche, sélectionnez le registre de conteneurs dans le portail, puis sélectionnez **Clés d’accès**.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-05]

Sous **Utilisateur administrateur**, sélectionnez **Activer**. Notez les valeurs suivantes :

* Serveur de connexion
* Nom d’utilisateur
* password

Vous utilisez ces valeurs dans les étapes suivantes lorsque vous travaillez avec votre registre dans l’interface de ligne de commande Docker.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-06]

## <a name="log-in-to-acr"></a>Se connecter à l’ACR

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. Pour ce faire, utilisez la commande [docker login][docker-login]. Remplacez le *nom d’utilisateur*, le *mot de passe* et le *serveur de connexion* par les valeurs notées à l’étape précédente.

```bash
docker login --username <username> --password <password> <login server>
```

Une fois l’opération terminée, la commande renvoie `Login Succeeded`. Vous pouvez également voir un avertissement de sécurité vous recommandant l’utilisation du paramètre `--password-stdin`. Même si son utilisation n’est pas le sujet de cet article, nous vous recommandons de suivre cette meilleure pratique. Pour plus d’informations, consultez la référence de la commande [docker login][docker-login].

## <a name="push-image-to-acr"></a>Envoyer une image dans l’ACR

Pour envoyer une image dans votre registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si nécessaire, exécutez la commande suivante pour extraire une image existante du hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

Avant d’envoyer l’image dans votre registre, vous devez l’étiqueter avec le nom du serveur de connexion à l’ACR. Marquez l’image en utilisant la commande [docker tag][docker-tag]. Remplacez la valeur du *serveur de connexion* par le nom du serveur de connexion enregistré précédemment.

```bash
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Enfin, utilisez la commande [docker push][docker-push] pour pousser l’image vers l’instance ACR. Remplacez la valeur du *serveur de connexion* par le nom du serveur de connexion de votre instance ACR.

```bash
docker push <login server>/aci-helloworld:v1
```

La sortie d’une commande `docker push` réussie ressemble à :

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Répertorier les images conteneur

Pour répertorier les images de votre instance ACR, accédez à votre registre dans le portail, sélectionnez **Référentiels**, puis sélectionnez le référentiel que vous avez créé avec `docker push`.

Dans cet exemple, nous sélectionnons le référentiel **aci-helloworld** et nous pouvons voir l’image étiquetée `v1` sous **BALISES**.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-09]

## <a name="deploy-image-to-aci"></a>Déployer l’image dans ACI

Pour déployer dans une instance à partir de votre registre, nous devons accéder au référentiel (aci-helloworld), puis cliquez sur l’ellipse à côté de v1.

![Lancement d’Azure Container Instance depuis le portail][qs-portal-10]

Un menu contextuel s’affiche. Sélectionnez **Exécuter l’instance** :

![Lancement du menu contextuel ACI][qs-portal-11]

Renseignez le **Nom du conteneur**, veillez à avoir sélectionné le bon abonnement, sélectionnez le **Groupe de ressources** existant « myResourceGroup » puis cliquez sur **OK** pour lancer Azure Container Instance.

![Lancement des options de déploiement ACI][qs-portal-12]

Lorsque le déploiement commence, une vignette apparaît sur le tableau de bord de votre portail, indiquant la progression du déploiement. Une fois le déploiement terminé, la vignette est mise à jour pour afficher votre nouveau groupe de conteneurs **mycontainer**.

![Statut de déploiement ACI][qs-portal-13]

Sélectionnez le groupe de conteneurs mycontainer pour afficher ses propriétés. Prenez note de l’**Adresse IP** du groupe de conteneurs, ainsi que du **STATUT** de votre conteneur.

![Détails du conteneur ACI][qs-portal-14]

## <a name="view-the-application"></a>Afficher l’application

Lorsque le conteneur passe à l’état **En cours d’exécution**, utilisez le navigateur de votre choix pour accéder à l’adresse IP que vous avez notée à l’étape précédente pour afficher l’application.

![Application Hello world dans le navigateur][qs-portal-15]

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer vos ressources, accédez au groupe de ressources **myResourceGroup** dans le portail. Une fois le groupe de ressources chargé, cliquez sur **Supprimer le groupe de ressources** pour supprimer le groupe de ressources, Azure Container Registry et toutes les instances Azure Container Instances.

![Création d’un registre de conteneur dans le portail Azure][qs-portal-08]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un registre de conteneur Azure Container Registry avec Azure CLI, et lancé une instance via des instances Azure Container Instances. Poursuivez avec le didacticiel sur Azure Container Instances pour approfondir vos connaissances sur ACI.

> [!div class="nextstepaction"]
> [Didacticiels Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
