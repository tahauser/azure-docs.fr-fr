---
title: "Prise en charge du pare-feu Azure Cosmos DB et contrôle d’accès IP | Microsoft Docs"
description: "Découvrez comment utiliser les stratégies de contrôle d’accès IP pour la prise en charge du pare-feu dans les comptes de base de données Azure Cosmos DB."
keywords: "contrôle d’accès IP, prise en charge du pare-feu"
services: cosmos-db
author: shahankur11
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: c1b9ede0-ed93-411a-ac9a-62c113a8e887
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: ankshah
ms.openlocfilehash: 1ceaa834ff68d5dca4abce561f9185e89af582af
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="azure-cosmos-db-firewall-support"></a>Prise en charge du pare-feu Azure Cosmos DB
Pour sécuriser les données stockées dans un compte de base de données Azure Cosmos DB, Azure Cosmos DB assure la prise en charge d’un [modèle d’autorisation](https://msdn.microsoft.com/library/azure/dn783368.aspx) basé sur un secret, qui utilise un code d’authentification de message basé sur le hachage (HMAC) à forte intégrité. Outre le modèle d’autorisations basé sur un secret, Azure Cosmos DB prend désormais en charge les contrôles d’accès basés sur une stratégie IP pour la prise en charge du pare-feu entrant. Ce modèle est semblable aux règles de pare-feu d’un système de base de données classique et renforce la sécurité du compte de base de données Azure Cosmos DB. Avec ce modèle, vous pouvez désormais configurer un compte de base de données Azure Cosmos DB pour qu’il soit accessible uniquement à partir d’un ensemble d’ordinateurs et/ou de services cloud approuvés. L’accès aux ressources Azure Cosmos DB à partir de ces ensembles d’ordinateurs et de services approuvés nécessite toujours que l’appelant présente un jeton d’autorisation valide.

## <a name="ip-access-control-overview"></a>Vue d’ensemble du contrôle d’accès IP
Par défaut, un compte de base de données Azure Cosmos DB est accessible depuis l’Internet public tant que la demande est accompagnée d’un jeton d’autorisation valide. Pour configurer le contrôle d’accès basé sur la stratégie IP, l’utilisateur doit fournir le jeu d’adresses IP ou des plages d’adresses IP au format CIDR pour être ajouté à la liste d’adresses IP clients autorisées pour un compte de base de données donné. Une fois que cette configuration est appliquée, toutes les demandes provenant d’ordinateurs ne figurant pas sur cette liste autorisée seront bloquées par le serveur.  Le flux de traitement des connexions du contrôle d’accès basé sur IP est décrit dans le diagramme suivant :

![Diagramme illustrant le processus de connexion du contrôle d’accès basé sur IP](./media/firewall-support/firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Connexions à partir de services cloud
Dans Azure, les services cloud sont une méthode courante d’hébergement de la logique de service de couche intermédiaire à l’aide d’Azure Cosmos DB. Pour autoriser l’accès à un compte de base de données Azure Cosmos DB à partir d’un service cloud, l’adresse IP publique de ce dernier doit être ajoutée à la liste d’adresses IP autorisées de votre compte de base de données Azure Cosmos DB en [configurant la stratégie de contrôle d’accès IP](#configure-ip-policy).  Cela garantit que toutes les instances de rôle des services cloud ont accès à votre compte de base de données Azure Cosmos DB. Vous pouvez récupérer des adresses IP pour vos services cloud dans le portail Azure, comme illustré dans la capture d’écran suivante :

![Capture d’écran illustrant l’adresse IP publique pour un service cloud affichée dans le portail Azure](./media/firewall-support/public-ip-addresses.png)

Lorsque vous faites évoluer votre service cloud en ajoutant des instances de rôle supplémentaires, ces nouvelles instances auront automatiquement accès au compte de base de données Azure Cosmos DB, car ils font partie du même service cloud.

## <a name="connections-from-virtual-machines"></a>Connexions à partir de machines virtuelles
Des [machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) ou des [jeux de mise à l’échelle de machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) peuvent également être utilisés pour héberger les services de couche intermédiaire à l’aide d’Azure Cosmos DB.  Pour configurer le compte de base de données Azure Cosmos DB afin qu’il autorise l’accès à partir de machines virtuelles, les adresses IP publiques de la machine virtuelle ou du groupe de machines virtuelles identiques doivent figurer parmi les adresses IP autorisées de votre compte de base de données Azure Cosmos DB en [configurant la stratégie de contrôle d’accès IP](#configure-ip-policy). Vous pouvez récupérer des adresses IP pour des machines virtuelles dans le portail Azure, comme illustré dans la capture d’écran suivante.

![Capture d’écran illustrant une adresse IP publique pour une machine virtuelle affichée dans le portail Azure](./media/firewall-support/public-ip-addresses-dns.png)

Lorsque vous ajoutez des instances de machine virtuelle supplémentaires au groupe, elles disposent automatiquement d’un accès à votre compte de base de données Azure Cosmos DB.

## <a name="connections-from-the-internet"></a>Connexions à partir d’Internet
Lorsque vous accédez à un compte de base de données Azure Cosmos DB à partir d’un ordinateur sur Internet, l’adresse IP ou la plage d’adresses IP de l’ordinateur doit être ajoutée à la liste d’adresses IP autorisées pour le compte de base de données Azure Cosmos DB. 

## <a name="connections-from-azure-paas-service"></a>Connexions à partir du service Azure PaaS 
Dans Azure, les services PaaS tels qu’Azure Stream Analytics et Azure Functions sont utilisés conjointement avec Azure Cosmos DB. Pour autoriser l’accès à un compte de base de données Azure Cosmos DB à partir de ces types de services dont l’adresse IP n’est pas facilement utilisable, l’adresse IP 0.0.0.0 doit être ajoutée à la liste autorisée des adresses IP associées à votre compte de base de données Azure Cosmos DB en [configurant la stratégie de contrôle d’accès IP](#configure-ip-policy).  L’accès des services Azure PaaS à un compte Azure Cosmos DB qui comporte cette règle est ainsi garanti. 

 ## <a id="configure-ip-policy"></a> Configuration de la stratégie de contrôle d’accès IP
La stratégie de contrôle d’accès IP peut être définie dans le portail Azure ou par programmation avec [Azure CLI](cli-samples.md), [Azure Powershell](powershell-samples.md)ou [l’API REST](/rest/api/documentdb/) en mettant à jour la propriété `ipRangeFilter`. Les plages/adresses IP doivent être séparées par des virgules et ne doivent pas contenir d’espaces. Exemple : « 13.91.6.132,13.91.6.1/24 ». Lors de la mise à jour de votre compte de base de données avec ces méthodes, veillez à renseigner toutes les propriétés afin d’éviter le rétablissement des paramètres par défaut.

> [!NOTE]
> En activant une stratégie de contrôle d’accès IP pour votre compte de base de données Azure Cosmos DB, tous les accès à votre compte de base de données Azure Cosmos DB à partir d’ordinateurs ne figurant pas sur la liste de plages d’adresses IP autorisées sont bloqués. En vertu de ce modèle, la navigation dans le plan de données à partir du portail sera également bloquée pour assurer l’intégrité du contrôle d’accès.

Pour simplifier le développement, le portail Azure vous aide à identifier et à ajouter l’adresse IP de votre ordinateur client à la liste autorisée, afin que les applications qui exécutent votre machine puissent accéder au compte Azure Cosmos DB. L’adresse IP du client est ici détectée comme étant vue par le portail. Il peut s’agir de l’adresse IP client de votre ordinateur, mais il peut également s’agir de l’adresse IP de votre passerelle réseau. N’oubliez pas de la supprimer avant de passer à la production.

Pour définir la stratégie de contrôle d’accès IP dans le portail Azure, accédez au panneau du compte Azure Cosmos DB, cliquez sur **Pare-feu** dans le menu de navigation, puis cliquez sur **ACTIVÉ**. 

![Capture d’écran montrant comment ouvrir le panneau Pare-feu dans le portail Azure](./media/firewall-support/azure-portal-firewall.png)

Dans le volet Nouveau, indiquez si le portail Azure peut accéder au compte et ajoutez d’autres adresses et plages si nécessaire, puis cliquez sur **Enregistrer**.  

> [!NOTE]
> Quand vous activez une stratégie de contrôle d’accès IP, vous devez ajouter l’adresse IP du portail Azure pour conserver l’accès. Les adresses IP du portail sont :
> |Région|Adresse IP|
> |------|----------|
> |Toutes les régions, à l’exception de celles spécifiées ci-dessous|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
> |Allemagne|51.4.229.218|
> |Chine|139.217.8.252|
> |Gouvernement des États-Unis|52.244.48.71|
>

![Capture d’écran montrant comment configurer les paramètres du pare-feu dans le portail Azure](./media/firewall-support/azure-portal-firewall-configure.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>Dépannage de la stratégie de contrôle d’accès IP
### <a name="portal-operations"></a>Opérations du portail
En activant une stratégie de contrôle d’accès IP pour votre compte de base de données Azure Cosmos DB, tous les accès à votre compte de base de données Azure Cosmos DB à partir d’ordinateurs ne figurant pas sur la liste de plages d’adresses IP autorisées sont bloqués. Par conséquent, si vous souhaitez activer les opérations de plan de données du portail telles que la navigation dans les collections et l’interrogation des documents, vous devez autoriser explicitement l’accès au portail Azure à l’aide du panneau **Pare-feu** dans le portail. 

![Capture d’écran montrant comment activer l’accès au portail Azure](./media/firewall-support/azure-portal-access-firewall.png)

### <a name="sdk--rest-api"></a>Kit SDK et API REST
Pour des raisons de sécurité, l’accès via le kit SDK ou l’API REST à partir d’ordinateurs ne figurant pas dans la liste autorisée renvoie une réponse générique 404 Introuvable, ainsi que des détails supplémentaires. Consultez la liste des adresses IP autorisées qui est configurée pour votre compte de base de données Azure Cosmos DB, afin de vérifier que la configuration de la stratégie appropriée est appliquée à votre compte de base de données Azure Cosmos DB.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les conseils sur les performances relatives au réseau, consultez [Conseils relatifs aux performances](performance-tips.md).

