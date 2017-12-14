---
title: "Créer un équilibrage de charge accessible sur Internet pour les services cloud Azure | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge accessible sur Internet dans un modèle de déploiement classique pour les services cloud"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-service-management
ms.assetid: 0bb16f96-56a6-429f-88f5-0de2d0136756
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: b389d9a01db394b79d07ff9c3d6d1cd94e811472
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Création d'un équilibreur de charge accessible sur Internet pour les services cloud

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Services cloud Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Avant d’utiliser des ressources Azure, il est important de comprendre qu’Azure dispose actuellement de deux modèles de déploiement : Azure Resource Manager et classique. Veillez à bien comprendre les [modèles et outils de déploiement](../azure-classic-rm.md) avant d’utiliser une ressource Azure. Pour consulter la documentation relative aux différents outils, cliquez sur les onglets situés en haut de cet article. Cet article traite du modèle de déploiement classique. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l’aide d’Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

Les services cloud sont automatiquement configurés avec un équilibrage de charge et peuvent être personnalisés via le modèle de service

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Création d’un équilibreur de charge à l'aide du fichier de définition de service

Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure pour .NET 2.5 pour mettre à jour votre service cloud. Les paramètres de point de terminaison des services cloud sont définis dans le fichier de [définition de service](https://msdn.microsoft.com/library/azure/gg557553.aspx) .csdef.

L’exemple suivant montre la configuration d’un fichier servicedefinition.csdef pour un déploiement de cloud :

En vérifiant l’extrait de code du fichier .csdef généré par un déploiement de cloud, vous pouvez voir le point de terminaison externe configuré pour utiliser les ports HTTP sur les ports 10000, 10001 et 10002.

```xml
<ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
<Endpoints>
    <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
    <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
    <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

    <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

    <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
        <AllocatePublicPortFrom>
            <FixedPortRange min=“10110” max=“10120“  />
        </AllocatePublicPortFrom>
    </InstanceInputEndpoint>
    <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
</Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Vérifier l'état d'intégrité de l'équilibrage de charge pour les services cloud

Voici un exemple de sonde d’intégrité :

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
</LoadBalancerProbes>
```

L’équilibreur de charge combine les informations du point de terminaison et de la sonde pour créer une URL sous la forme de `http://{DIP of VM}:80/Probe.aspx` pour interroger l’intégrité du service.

Le service détecte les sondes périodiques à partir de la même adresse IP. Il s'agit de la requête de la sonde d'intégrité en provenance de l'hôte du nœud sur lequel la machine virtuelle s'exécute. Le service doit répondre avec un code d’état HTTP 200 pour que l’équilibrage de charge suppose que le service est sain. Tout autre code d'état HTTP (par exemple 503) prend directement la machine virtuelle hors service.

La définition de la sonde en contrôle également la fréquence. Dans le cas ci-dessus, l’équilibrage de charge sonde le point de terminaison toutes les 5 secondes. Si aucune réponse positive n'est reçue pendant 10 secondes (deux intervalles de sondage), la sonde est considérée comme défaillante et la machine virtuelle est mise hors service. De même, si le service est hors service et qu’une réponse positive est reçue, le service est immédiatement remis en service. Si le service fluctue entre sain et défectueux, l'équilibrage de charge peut décider de retarder la remise en service du service jusqu'à ce qu'il soit sain pendant un certain nombre d'analyses.

Vérifiez le schéma de définition de service de la [sonde d’intégrité](https://msdn.microsoft.com/library/azure/jj151530.aspx) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

