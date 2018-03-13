---
title: "Vue d’ensemble d’Azure DNS | Microsoft Docs"
description: "Vue d’ensemble des services d’hébergement DNS sur Microsoft Azure. Héberger votre domaine sur Microsoft Azure"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: f255fd9621ff90bfbb3ad193faa64495acf7ecd7
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2017
---
# <a name="azure-dns-overview"></a>Vue d’ensemble d’Azure DNS

Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP. Azure DNS est un service d'hébergement pour les domaines DNS et qui offre une résolution de noms à l'aide de l'infrastructure Microsoft Azure. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure. Désormais, Azure DNS prend également en charge les domaines DNS privés. Pour plus d’informations, consultez [Utilisation d’Azure DNS pour les domaines privés](private-dns-overview.md).

![Vue d’ensemble de DNS](./media/dns-overview/scenario.png)

## <a name="features"></a>Caractéristiques

* **Fiabilité et performances** : les domaines DNS dans Azure DNS sont hébergés sur un réseau global de serveurs de noms DNS. Azure DNS utilise la mise en réseau Anycast, pour que chaque requête DNS obtienne une réponse du serveur DNS disponible le plus proche. Cette technique offre des performances élevées et une haute disponibilité pour votre domaine.

* **Intégration transparente** : le service Azure DNS peut être utilisé pour gérer les enregistrements DNS pour vos services Azure et pour fournir également un DNS pour vos ressources externes. Azure DNS est intégré au portail Azure et utilise les mêmes informations d’identification, de facturation et de contrat d’assistance que vos autres services Azure.

* **Sécurité** : le service Azure DNS est basé sur Azure Resource Manager. Ainsi, il tire parti de fonctionnalités de Resource Manager telles que le contrôle d’accès en fonction du rôle, les journaux d’audit et le verrouillage de ressources. Vos domaines et enregistrements peuvent être gérés via le portail Azure, des applets de commande Azure PowerShell et l’interface CLI Azure multiplateforme. Les applications nécessitant une gestion automatique de DNS peuvent s’intégrer au service par le biais de l’API REST et des Kits de développement logiciel (SDK).

Azure DNS ne prend actuellement pas en charge l'achat de noms de domaine. Si vous voulez acheter des domaines, vous devez utiliser un bureau d’enregistrement de noms de domaine tiers. Le bureau d’enregistrement facture généralement des frais annuels peu élevés. Les domaines peuvent être hébergés dans Azure DNS pour la gestion des enregistrements DNS. Pour plus d’informations, consultez [Déléguer un domaine à Azure DNS](dns-domain-delegation.md) .

## <a name="pricing"></a>Tarifs

La facturation DNS est basée sur le nombre de zones DNS hébergées dans Azure et le nombre de requêtes DNS. Pour en savoir plus sur la tarification, consultez [Tarification d’Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="faq"></a>Forum Aux Questions

Pour les questions fréquemment posées sur Azure DNS, consultez le [FAQ sur Azure DNS](dns-faq.md).

## <a name="next-steps"></a>Étapes suivantes

Obteniez plus d’informations sur les zones et enregistrements DNS en consultant : [Vue d’ensemble des enregistrements et zones DNS](dns-zones-records.md).

Découvrez comment [créer une zone DNS](./dns-getstarted-create-dnszone-portal.md) dans Azure DNS.

Découvrez certaines des autres [fonctionnalités de réseau](../networking/networking-overview.md) clés d’Azure.

