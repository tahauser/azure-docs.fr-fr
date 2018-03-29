---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 591da67e6411d0e859076f0a3c3c38afc1ebe1f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Vous pouvez vérifier que votre connexion a réussi à l’aide de l’applet de commande « Get-AzureVNetConnection ».

1. Utilisez l’exemple d’applet de commande suivant, en configurant les valeurs sur les vôtres. Le nom du réseau virtuel doit être placé entre guillemets s’il contient des espaces.

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. Une fois l’applet de commande exécutée, affichez les valeurs. Dans l’exemple ci-dessous, la zone État de la connectivité indique « Connecté » et vous pouvez voir les octets d’entrée et de sortie.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal