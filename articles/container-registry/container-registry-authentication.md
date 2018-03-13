---
title: Authentification avec un registre de conteneurs Azure
description: "Options d’authentification pour un registre de conteneurs Azure, y compris la connexion de registre et la connexion directe des principaux du service Azure Active Directory."
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70758f938718aef160670bc023aff5fc0c9fb92a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>S’authentifier avec un registre de conteneurs Docker

Plusieurs méthodes permettent de s’authentifier avec un registre de conteneurs Azure, chacune d’elles étant applicable à un ou plusieurs scénarios d’usage du registre.

Vous pouvez vous connecter directement à un registre via une [connexion individuelle](#individual-login-with-azure-ad), et vos applications et orchestrateurs de conteneurs peuvent effectuer une authentification sans assistance, ou « sans périphérique de contrôle », à l’aide d’un [principal du service](#service-principal) Azure Active Directory (Azure AD).

Azure Container Registry ne prend pas en charge les opérations Docker non authentifiées ni les accès anonymes. Pour des images publiques, vous pouvez utiliser [Docker Hub](https://docs.docker.com/docker-hub/).

## <a name="individual-login-with-azure-ad"></a>Connexion individuelle avec Azure AD

Si vous utilisez directement votre registre, par exemple si vous extrayez des images et en envoyez depuis et vers votre station de travail de développement, authentifiez-vous à l’aide de la commande [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) dans [Azure CLI](/cli/azure/install-azure-cli) :

```azurecli
az acr login --name <acrName>
```

Si vous vous connectez avec `az acr login`, l’interface CLI utilise le jeton créé lorsque vous avez exécuté `az login` pour authentifier en toute transparence votre session avec votre registre. Une fois que vous êtes connecté de cette façon, vos informations d’identification sont mises en cache, et les commandes `docker` suivantes ne nécessitent pas de nom d’utilisateur ni de mot de passe. Si votre jeton arrive à expiration, vous pouvez l’actualiser en utilisant de nouveau la commande `az acr login` pour la réauthentification. L’utilisation de `az acr login` avec des identités Azure fournit un [accès en fonction du rôle](../active-directory/role-based-access-control-configure.md).

## <a name="service-principal"></a>Principal du service

Vous pouvez affecter un [principal du service](../active-directory/develop/active-directory-application-objects.md) à votre registre, et votre application ou service peut l’utiliser pour une authentification sans périphérique de contrôle. Les principaux du service autorisent les [accès en fonction du rôle](../active-directory/role-based-access-control-configure.md) à un registre, et vous pouvez affecter plusieurs principaux du service à un registre. Plusieurs principaux du service vous permettent de définir différents accès pour plusieurs applications.

Rôles disponibles :

  * **Lecteur** : extraction
  * **Contributeur** : extraction et envoi
  * **Propriétaire** : extraction, envoi et attribution de rôles à d’autres utilisateurs

Les principaux du service activent une connectivité sans périphérique de contrôle à un registre dans les scénarios d’envoi et d’extraction suivants :

  * *Lecteur* : déploiements de conteneurs à partir d’un registre vers des systèmes d’orchestration, y compris DC/OS, Docker Swarm et Kubernetes. Vous pouvez également procéder à des extractions depuis les registres de conteneurs vers des services Azure connexes tels que [AKS](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), et autres.

  * *Contributeur* : solutions d’intégration et de déploiement en continu, comme Visual Studio Team Services (VSTS) ou Jenkins, qui créent des images de conteneur et les envoient vers un registre.

> [!TIP]
> Vous pouvez régénérer le mot de passe d’un principal du service en exécutant la commande [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_reset_credentials).
>

Vous pouvez également vous connecter directement avec un principal du service. Fournissez le mot de passe et l’ID d’application du principal du service pour la commande `docker login` :

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Une fois que vous êtes connecté, Docker met les informations d’identification en cache. Vous n’avez donc pas besoin de vous souvenir de l’ID d’application.

Selon la version de Docker que vous avez installée, vous pouvez voir un avertissement de sécurité recommandant l’utilisation du paramètre `--password-stdin`. Même si son utilisation n’est pas le sujet de cet article, nous vous recommandons de suivre cette meilleure pratique. Pour en savoir plus, consultez les informations de référence sur la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/).

Pour plus d’informations sur l’utilisation d’un principal de service pour l’authentification sans périphérique de contrôle à ACR, consultez [Authentification d’Azure Container Registry avec des principaux de service](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Compte d’administrateur

Chaque registre de conteneurs inclut un compte d’utilisateur administrateur, qui est désactivé par défaut. Vous pouvez activer le compte d’utilisateur administrateur et gérer ses informations d’identification dans le [portail Azure](container-registry-get-started-portal.md#create-a-container-registry) ou à l’aide d’Azure CLI.

> [!IMPORTANT]
> Le compte d’administrateur est conçu pour permettre à un seul utilisateur d’accéder au registre, principalement à des fins de test. Nous vous recommandons de partager les informations d’identification du compte d’administrateur avec plusieurs utilisateurs. Tous les utilisateurs qui s’authentifient avec le compte d’administrateur apparaissent sous la forme d’un seul utilisateur avec un accès par envoi et par extraction au registre. La modification ou la désactivation de ce compte désactive l’accès au registre pour tous les utilisateurs qui utilisent ces informations d’identification. Une identité individuelle est recommandée pour les utilisateurs et principaux du service pour les scénarios sans périphérique de contrôle.
>

Le compte d’administrateur reçoit deux mots de passe qui peuvent être régénérés. Deux mots de passe vous permettent de maintenir la connexion au registre en utilisant un mot de passe tandis que vous régénérez l’autre. Si le compte d’administrateur est activé, vous pouvez transmettre le nom d’utilisateur et l’un ou l’autre des mots de passe à la commande `docker login` pour une authentification de base auprès du registre. Par exemple : 

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

Là encore, Docker recommande d’utiliser le paramètre `--password-stdin` au lieu de le fournir sur la ligne de commande pour renforcer la sécurité. Vous pouvez également spécifier uniquement votre nom d’utilisateur, sans `-p`, puis entrer votre mot de passe à l’invite.

Pour activer l’utilisateur administrateur pour un registre existant, vous pouvez utiliser le paramètre `--admin-enabled` de la commande [az acr update](/cli/azure/acr?view=azure-cli-latest#az_acr_update) dans Azure CLI :

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Pour activer l’utilisateur administrateur dans le portail Azure, accédez au registre, sélectionnez **Clés d’accès** sous **PARAMÈTRES**, puis **Activer** sous **Utilisateur administrateur**.

![Activer l’IU de l’utilisateur administrateur dans le portail Azure][auth-portal-01]

## <a name="next-steps"></a>Étapes suivantes

* [Push your first image using the Azure CLI (Transmettre votre première image à l’aide d’Azure CLI)](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
