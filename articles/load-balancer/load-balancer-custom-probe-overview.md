---
title: Utiliser les sondes personnalisées de l’équilibreur de charge pour surveiller l’état d’intégrité | Microsoft Docs
description: Découvrez comment utiliser les sondes personnalisées pour l’équilibreur de charge Azure afin de surveiller les instances situées derrière un équilibreur de charge
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="understand-load-balancer-probes"></a>Comprendre les sondes de l’équilibrage de charge

Azure Load Balancer utilise des sondes d’intégrité pour déterminer quelle instance de pool principale devrait recevoir de nouveaux flux. En cas d’échec d’une sonde d’intégrité, Load Balancer cesse d’envoyer de nouveaux flux à l’instance non intègre respective, et les flux existants sur cette instance ne sont pas affectés.  Lorsque toutes les instances de pool principales ont été explorées, tous les flux existants expirent dans toutes les instances du pool principal.

Les rôles de service cloud (rôles de travail et rôles Web) utilisent un agent invité pour la surveillance par sonde. Des sondes d’intégrité personnalisées TCP ou HTTP doivent être configurées quand vous utilisez des machines virtuelles derrière Load Balancer.

## <a name="understand-probe-count-and-timeout"></a>Présentation du nombre et du délai d’expiration des sondes

Le comportement des sondes dépend des éléments suivants :

* Le nombre de sondes ayant réussi qui permet à une instance d’être étiquetée comme étant en cours d’exécution.
* Le nombre de sondes ayant échoué qui permet à une instance d’être étiquetée comme n’étant pas en cours d’exécution.

La valeur du délai d’expiration et de la fréquence définie dans SuccessFailCount détermine si une instance est confirmée comme étant en cours d’exécution ou pas. Dans le portail Azure, le délai d’expiration est défini sur deux fois la valeur de la fréquence.

La configuration de sonde de toutes les instances à charge équilibrée pour un point de terminaison (jeu d’équilibrage de la charge) doit être identique. Vous ne pouvez pas avoir de configuration de sonde différente pour chaque instance de rôle ou machine virtuelle du même service hébergé pour une combinaison de points de terminaison donnée. Par exemple, chaque instance doit avoir des délais d’expiration et des ports locaux identiques.

> [!IMPORTANT]
> Une sonde d’équilibreur de charge utilise une adresse IP 168.63.129.16. Cette adresse IP publique facilite la communication vers les ressources de plateforme internes pour le scénario « Apportez votre propre réseau virtuel IP Azure ». L’adresse IP publique virtuelle 168.63.129.16 est utilisée dans toutes les régions et ne change pas. Nous vous conseillons d’autoriser cette adresse IP dans toutes les stratégies de pare-feu local. Elle ne doit pas être considérée comme un risque de sécurité, car seule la plateforme Azure interne peut envoyer un message à partir de cette adresse. Si vous n’autorisez pas cette adresse IP dans vos stratégies de pare-feu, un comportement inattendu se produit dans différentes situations. Il inclut la configuration de la même plage d’adresses IP (168.63.129.16) et la duplication des adresses IP.

## <a name="learn-about-the-types-of-probes"></a>En savoir plus sur les types de sondes

### <a name="guest-agent-probe"></a>Sonde d’agent invité

Une sonde d’agent invité est disponible pour Azure Cloud Services uniquement. L’équilibreur de charge utilise l’agent invité dans la machine virtuelle. Ensuite, il écoute et répond HTTP 200 OK uniquement lorsque l’instance est prête (les autres états sont de type occupé, recyclage ou arrêt).

Pour plus d’informations, consultez les sections relatives à la [configuration du fichier de définition de service (csdef) pour les sondes d’intégrité](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou à la [création d’un équilibreur de charge public pour les services cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Dans quelles conditions une sonde d’agent invité marque-t-elle une instance comme étant défectueuse ?

Si l’agent invité ne répond pas avec HTTP 200 OK, l’équilibreur de charge marque l’instance comme ne répondant pas. Il arrête ensuite d’envoyer du trafic vers celle-ci. L’équilibreur de charge continue d’effectuer un test ping sur l’instance. Si l’agent invité répond avec HTTP 200, l’équilibreur de charge renvoie du trafic vers cette instance.

Quand vous utilisez un rôle web, le code du site web s’exécute généralement dans w3wp.exe, qui n’est pas surveillé par l’agent de structure Azure ou l’agent invité. Les échecs dans w3wp.exe (par exemple, les réponses HTTP 500) ne sont pas signalés à l’agent invité. Par conséquent, l’équilibreur de charge n’accepte qu’une instance hors rotation.

### <a name="http-custom-probe"></a>Sonde HTTP personnalisée

La sonde personnalisée HTTP remplace la sonde d’agent invité par défaut. Vous pouvez créer votre propre logique personnalisée pour déterminer l'état de l'instance de rôle. L’équilibreur de charge sonde régulièrement votre point de terminaison (toutes les 15 secondes, par défaut). L’instance est considérée comme faisant partie de la rotation de l’équilibreur de charge si elle répond avec HTTP 200 dans le délai imparti. Le délai d’attente est de 31 secondes par défaut.

Une sonde personnalisée HTTP peut être utile pour implémenter votre propre logique afin de supprimer des instances de la rotation de l’équilibreur de charge. Par exemple, vous pouvez décider de supprimer une instance si elle utilise plus de 90 % du processeur et retourne un état autre que 200. Si vous avez des rôles web qui utilisent w3wp.exe, vous obtenez aussi la surveillance automatique de votre site Web. Les défaillances de votre code de site web renvoient un état autre que 200 pour la sonde de l’équilibreur de charge.

> [!NOTE]
> La sonde HTTP personnalisée prend en charge les chemins relatifs et le protocole HTTP uniquement. HTTPS n’est pas pris en charge.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Dans quelles conditions une sonde HTTP personnalisée marque-t-elle une instance comme étant défectueuse ?

* L’application HTTP retourne un code de réponse HTTP autre que 200 (par exemple, 403, 404 ou 500). Cette reconnaissance positive vous avertit que l’instance d’application doit être mise immédiatement hors service.
* Le serveur HTTP ne répond pas du tout après le délai d’attente. Selon la valeur définie pour le délai d’attente, il se peut que plusieurs demandes d’analyse ne reçoivent pas de réponse avant que celle-ci ne soit marquée comme n’étant pas en cours d’exécution (autrement dit, avant que les sondes SuccessFailCount soient envoyées).
* Le serveur ferme la connexion via une réinitialisation TCP.

### <a name="tcp-custom-probe"></a>Sonde TCP personnalisée

Les sondes TCP personnalisées établissent une connexion en effectuant une connexion en trois temps au port défini.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Dans quelles conditions une sonde TCP personnalisée marque-t-elle une instance comme étant défectueuse ?

* Le serveur TCP ne répond pas du tout après le délai d’attente. La sonde est marquée comme n’étant pas en cours d’exécution en fonction du nombre de demandes d’analyse ayant échoué, qui ont été configurées pour rester sans réponse avant que la sonde soit marquée comme n’étant pas en cours d’exécution.
* La sonde reçoit une réinitialisation TCP de l’instance de rôle.

Pour plus d’informations sur la configuration d’une sonde d’intégrité HTTP ou d’une sonde TCP, consultez [Création d’un équilibrage de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Rajouter des instances saines à la rotation de l’équilibreur de charge

Les sondes TCP et HTTP sont considérées comme saines et marquent l’instance de rôle comme saine dans les cas suivants :

* L’équilibreur de charge reçoit une sonde positive au premier démarrage de la machine virtuelle.
* Le nombre SuccessFailCount (décrit précédemment) définit la valeur des sondes ayant réussi nécessaires pour marquer l’instance de rôle comme étant saine. Si une instance de rôle a été supprimée, le nombre de sondes ayant réussi successives doit être égal ou supérieur à la valeur de SuccessFailCount pour marquer l’instance de rôle comme étant en cours d’exécution.

> [!NOTE]
> Si l’intégrité d’une instance de rôle fluctue, l’équilibreur de charge attend plus longtemps avant de remettre l’instance de rôle dans un état d’intégrité normal. Ce délai d’attente supplémentaire protège l’utilisateur et l’infrastructure. Il s’agit d’une stratégie intentionnelle.

## <a name="use-log-analytics-for-a-load-balancer"></a>Utiliser l’analytique des journaux pour un équilibreur de charge

Vous pouvez utiliser [l’analytique des journaux](load-balancer-monitor-log.md) pour l’équilibreur de charge pour vérifier le nombre et l’état d’intégrité des sondes. La journalisation peut être utilisée avec Power BI ou Operational Insights pour fournir des statistiques sur l’état d’intégrité de l’équilibreur de charge.
