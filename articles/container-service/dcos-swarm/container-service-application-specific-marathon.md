---
title: "Service Marathon lié à une application ou à un utilisateur donnés"
description: "Créer un service Marathon lié à une application ou à un utilisateur donnés"
services: container-service
author: rgardler
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 61cc379bf32f3e0db11d8fd9dae36d9ecb6b1a6c
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="create-an-application-or-user-specific-marathon-service"></a>Créer un service Marathon lié à une application ou à un utilisateur donnés

Azure Container Service fournit un ensemble de serveurs maîtres sur lesquels nous préconfigurons Apache Mesos et Marathon. Ceux-ci peuvent être utilisés pour organiser vos applications sur le cluster, mais il est préférable de ne pas utiliser les serveurs maîtres à cet effet. Par exemple, l’ajustement de la configuration de Marathon nécessite la connexion aux serveurs maîtres et la manipulation au sein de ces derniers ; cela favorise les serveurs maîtres uniques qui diffèrent légèrement de la norme et doivent être traités et gérés indépendamment. En outre, la configuration requise par une équipe peut ne pas constituer la configuration optimale pour une autre équipe.

Dans cet article, nous expliquerons comment ajouter un service Marathon lié à une application ou à un utilisateur donnés.

Étant donné que ce service appartiendra à un utilisateur ou à une équipe unique, ces derniers peuvent le configurer comme ils l’entendent. En outre, Azure Container Service garantit l’exécution continue du service. En cas d’échec de ce dernier, Azure Container Service le redémarre à votre place. La plupart du temps, vous ne remarquerez même pas la survenue d’un temps d’arrêt.

## <a name="prerequisites"></a>Composants requis
[Déployez une instance d’Azure Container Service](container-service-deployment.md) avec un orchestrator de type DC/OS, et [vérifiez que votre client peut se connecter à votre cluster](../container-service-connect.md). Effectuez également les étapes suivantes.

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Créer un service Marathon lié à une application ou à un utilisateur donnés
Commencez par créer un fichier de configuration JSON qui définit le nom du service d’application que vous souhaitez créer. Ici, nous utilisons `marathon-alice` comme nom d’infrastructure. Enregistrez le fichier au format `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Ensuite, utilisez l’interface de ligne de commande DC/OS pour installer l’instance Marathon avec les options qui sont définies dans votre fichier de configuration :

```bash
dcos package install --options=marathon-alice.json marathon
```

Vous devriez à présent voir votre service `marathon-alice` s’exécuter dans l’onglet Services de l’interface utilisateur de votre DC/OS. Si vous souhaitez accéder directement à l’interface utilisateur, son chemin sera `http://<hostname>/service/marathon-alice/` .

## <a name="set-the-dcos-cli-to-access-the-service"></a>Configurer l’interface de ligne de commande DC/OS pour accéder au service
Vous pouvez éventuellement configurer votre interface de ligne de commande DC/OS afin d’accéder à ce nouveau service, en définissant la propriété `marathon.url` pour que celle-ci pointe sur l’instance `marathon-alice`, comme suit :

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Vous pouvez vérifier quelle instance de Marathon votre interface de ligne de commande utilise avec la commande `dcos config show` . Vous pouvez revenir à l’utilisation de votre service Marathon maître avec la commande `dcos config unset marathon.url`.

