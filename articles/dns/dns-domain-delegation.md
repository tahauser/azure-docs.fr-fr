---
title: Aperçu de la délégation Azure DNS | Microsoft Docs
description: Découvrez comment modifier la délégation de domaine et les serveurs de noms Azure DNS pour fournir l’hébergement d’un domaine.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: fc79999d240baf18ccf5923908c98791c4e7e7bb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Délégation de zones DNS dans Azure DNS

Azure DNS vous permet d’héberger une zone DNS et de gérer les enregistrements DNS pour un domaine dans Azure. Pour que les requêtes DNS d’un domaine atteignent Azure DNS, le domaine doit être délégué à Azure DNS à partir du domaine parent. GardeN’oubliez pas qu’Azure DNS n’est pas le bureau d’enregistrement de domaines. Cet article explique le fonctionnement de la délégation de domaine et indique comment déléguer des domaines à Azure DNS.

## <a name="how-dns-delegation-works"></a>Fonctionnement de la délégation DNS

### <a name="domains-and-zones"></a>Zones et domaines

DNS est une hiérarchie de domaines. Celle-ci démarre à partir du domaine « racine », dont le nom est simplement « **.** ».  Puis viennent les domaines de niveau supérieur, tels que « com », « net », « org », « uk » ou « jp ».  Vous trouvez ensuite les domaines de second niveau, comme « org.uk » ou « co.jp ».  Et ainsi de suite. Les domaines de la hiérarchie DNS sont hébergés à l’aide de zones DNS distinctes. Ces zones sont globalement distribuées, et hébergées par des serveurs DNS dans le monde entier.

Une **zone DNS** est un nom unique dans le système DNS, par exemple, « contoso.com ». Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Par exemple, le domaine « contoso.com » peut contenir plusieurs enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web).

Un **bureau d’enregistrement de domaines** est une société qui fournit des noms de domaine Internet. Il vérifie si le domaine Internet que vous souhaitez utiliser est disponible. Vous pouvez ensuite l’acheter. Une fois inscrit, le nom de domaine est votre propriété légale. Si vous disposez déjà d’un domaine Internet, vous allez utiliser le bureau d’enregistrement de domaines actuel pour la délégation à Azure DNS.

Pour plus d'informations sur le propriétaire d'un nom de domaine donné ou sur l'achat d'un domaine, consultez la page [Gestion des domaines Internet dans Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).

### <a name="resolution-and-delegation"></a>Résolution et délégation

Deux types de serveur DNS sont disponibles :

* Un serveur DNS *faisant autorité* héberge les zones DNS. Il répond aux requêtes DNS pour les enregistrements de ces zones uniquement.
* Un serveur DNS *récursif* n’héberge pas de zones DNS. Il répond à toutes les requêtes DNS, en appelant des serveurs DNS faisant autorité pour rassembler les données dont il a besoin.

Azure DNS fournit un service DNS faisant autorité.  Il ne fournit pas un service DNS récursif. Les services cloud et machines virtuelles contenus dans Azure sont automatiquement configurés pour utiliser un service DNS récursif, fourni séparément, dans le cadre de l’infrastructure Azure. Pour savoir comment modifier ces paramètres DNS, voir [Résolution de noms dans Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

Les clients DNS des PC ou appareils mobiles appellent généralement un serveur DNS récursif pour effectuer les requêtes DNS dont les applications clientes ont besoin.

Lorsqu’un serveur DNS récursif reçoit une requête pour un enregistrement DNS tel que « www.contoso.com », il doit d’abord rechercher le serveur de noms qui héberge la zone pour le domaine « contoso.com ». Pour ce faire, il commence par les serveurs de noms racines, puis il recherche les serveurs de noms hébergeant la zone « com ». Il interroge ensuite les serveurs de noms « com » pour trouver les serveurs de noms hébergeant la zone « contoso.com ».  Enfin, il est en mesure de rechercher « www.contoso.com » parmi ces serveurs de noms.

On parle dans ce cas de résolution de noms DNS. À strictement parler, la résolution DNS inclut toutefois des étapes supplémentaires, telles que le suivi des enregistrements CNAME, mais ce n’est pas important pour comprendre le fonctionnement de la délégation DNS.

Comment une zone parente « pointe-t-elle » vers les serveurs de noms d’une zone enfant ? Elle utilise pour cela un type spécial d’enregistrement DNS appelé enregistrement NS (pour « serveur de noms »). Par exemple, la zone racine contient les enregistrements NS pour « com » et affiche les serveurs de noms pour la zone « com ». La zone « com », quant à elle, contient les enregistrements NS pour « contoso.com » et affiche les serveurs de noms pour la zone « contoso.com ». La configuration d’enregistrements NS pour une zone enfant dans une zone parente est appelée « délégation de domaine ».

L’image suivante montre un exemple de requête DNS. contoso.net et partners.contoso.net sont des zones Azure DNS.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. Le client demande `www.partners.contoso.net` à partir du serveur DNS local.
1. Le serveur DNS local ne dispose pas de l’enregistrement. Aussi, il effectue une demande auprès du serveur de noms racine.
1. Le serveur de noms racine ne dispose pas de l’enregistrement, mais il connaît l’adresse du serveur de noms `.net` et la fournit au serveur DNS.
1. Le serveur DNS envoie la demande au serveur de noms `.net`. Il ne possède pas l’enregistrement, mais il connaît l’adresse du serveur de noms contoso.net. Dans ce cas, il s’agit d’une zone DNS hébergée dans Azure DNS.
1. La zone `contoso.net` ne possède pas d’enregistrement, mais elle connaît le serveur de noms `partners.contoso.net` et l’envoie en guise de réponse. Dans ce cas, il s’agit d’une zone DNS hébergée dans Azure DNS.
1. Le serveur DNS demande l’adresse IP de `partners.contoso.net` à partir de la zone `partners.contoso.net`. Elle contient l’enregistrement A et peut alors envoyer comme réponse l’adresse IP.
1. Le serveur DNS fournit l’adresse IP au client
1. Le client se connecte au site web `www.partners.contoso.net`.

Chaque délégation a en fait deux copies des enregistrements NS : une dans la zone parent qui pointe vers la zone enfant et l’autre dans la zone enfant elle-même. La zone « contoso.net » contient les enregistrements NS pour « contoso.net » (en plus des enregistrements NS dans « net »). Il s’agit d’enregistrements NS faisant autorité ; ils se trouvent au sommet de la zone enfant.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déléguer votre domaine à Azure DNS](dns-delegate-domain-azure-dns.md).

