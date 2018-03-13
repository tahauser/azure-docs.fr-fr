---
title: Authentification Azure Container Registry avec des principaux de service
description: "Découvrez comment fournir un accès aux images de votre registre de conteneurs privé à l’aide d’un principal de service Azure Active Directory."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 97036ecabceb12b87b76c6ecb7e521157cbef827
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Authentification Azure Container Registry avec des principaux de service

Vous pouvez utiliser un principal de service Azure Active Directory (Azure AD) pour fournir l’accès `docker push` et `pull` à votre registre de conteneurs. En utilisant un principal de service, vous pouvez fournir l’accès aux applications et services « sans affichage ».

## <a name="what-is-a-service-principal"></a>Qu’est-ce qu’un principal de service ?

Les *principaux de service* Azure AD donnent accès aux ressources Azure dans votre abonnement. Vous pouvez considérer un principal de service comme l’identité d’un utilisateur pour un service, le « service » étant n’importe quelle application, n’importe quel service ou n’importe quelle plateforme qui a besoin d’accéder aux ressources. Vous pouvez configurer un principal de service avec des droits d’accès limités aux ressources que vous spécifiez. Ensuite, vous pouvez configurer votre application ou votre service afin qu’il/elle utilise les informations d’identification du principal de service pour accéder à ces ressources.

Dans le contexte d’Azure Container Registry, vous pouvez créer un principal de service Azure AD avec des autorisations d’extraction, d’envoi et d’extraction, ou de propriétaire pour votre registre Docker privé dans Azure.

## <a name="why-use-a-service-principal"></a>Pourquoi utiliser un principal de service ?

En utilisant un principal de service Azure AD, vous pouvez fournir un accès délimité à votre registre de conteneurs privé. Vous pouvez créer différents principaux de service pour chacune de vos applications ou chacun de vos services, en personnalisant les droits d’accès à votre registre. De plus, étant donné que vous pouvez éviter le partage des informations d’identification entre les services et applications, vous pouvez faire tourner les informations d’identification ou révoquer l’accès uniquement pour le principal de service (et donc l’application) de votre choix.

Par exemple, votre application web peut utiliser un principal de service qui lui fournit l’accès `pull` uniquement, alors que votre système de génération peut utiliser un principal de service qui lui fournit à la fois l’accès `push` et l’accès `pull`. Si le développement de votre application est confié à quelqu’un d’autre, vous pouvez modifier les informations d’identification du principal de service sans affecter le système de génération.

## <a name="when-to-use-a-service-principal"></a>Quand utiliser un principal de service

Vous devez utiliser un principal de service pour fournir l’accès au registre dans les **scénarios sans affichage**. Autrement dit, pour toute application, service ou script qui doit envoyer ou extraire des images conteneur de manière automatisée ou sans assistance.

Pour un accès individuel à un registre, par exemple lorsque vous extrayez manuellement une image de conteneur vers votre station de travail de développement, vous devez utiliser votre propre [identité Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) pour accéder au registre (par exemple, avec [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous disposez d’un principal de service auquel vous avez accordé l’accès à votre registre de conteneurs, vous pouvez utiliser ses informations d’identification dans vos applications et services pour interagir avec le registre.

Bien que la configuration des applications individuelles pour qu’elles utilisent les informations d’identification du principal de service ne soit pas abordée dans cet article, vous trouverez des instructions pour certains services et plateformes ici :

* [S’authentifier avec Azure Container Registry à partir d’Azure Container Service (AKS)](container-registry-auth-aks.md)
* [S’authentifier avec Azure Container Registry à partir d’Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login