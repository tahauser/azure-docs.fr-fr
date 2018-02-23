---
title: "Configurer des zones de recherche inversée pour une vérification de bannière SMTP dans Azure | Microsoft Docs"
description: "Décrit comment configurer des zones de recherche inversée pour une vérification de bannière SMTP dans Azure"
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: 
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2018
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configurer des zones de recherche inversée pour une vérification de bannière SMTP

Cet article décrit comment utiliser une zone de recherche inversée dans Azure DNS et créer un enregistrement DNS inversé (PTR) pour une vérification de bannière SMTP. 

## <a name="symptom"></a>Symptôme

Si vous hébergez un serveur SMTP dans Microsoft Azure, vous pouvez recevoir le message d’erreur suivant quand vous envoyez ou recevez un message à partir de serveurs de messagerie distants :

**554: No PTR Record** (554 : Aucun enregistrement PTR) 

## <a name="solution"></a>Solution

Pour une adresse IP virtuelle dans Azure, les enregistrements inversés sont créés dans des zones de domaine de Microsoft, pas dans des zones de domaine personnalisées.

Pour configurer les enregistrements PTR dans des zones de domaine de Microsoft, utilisez la propriété -ReverseFqdn sur la ressource PublicIpAddress. Pour plus d’informations, consultez [Configurer des DNS inversés dans les services hébergés par Azure](../dns/dns-reverse-dns-for-azure-services.md). 

Lorsque vous configurez les enregistrements PTR, assurez-vous que l’adresse IP et le nom de domaine complet inversé sont détenus par l’abonnement. Si vous essayez de définir un nom de domaine complet inversé qui n’appartient pas à l’abonnement, le message d’erreur suivant s’affiche :

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn matches fqdn of any public ip resource under the subscription; 
    2) ReverseFqdn resolves to the fqdn (through CName records chain) of any public ip resource under the subcription; 
    3) It resolves to the ip address (through CName and A records chain) of a static public ip resource under the subscription. 

Si vous modifiez manuellement votre bannière SMTP pour qu’elle corresponde à notre nom de domaine complet inversé par défaut, le serveur de messagerie distant peut toujours échouer, car il peut s’attendre à ce que l’hôte de la bannière SMPT corresponde à l’enregistrement MX du domaine.