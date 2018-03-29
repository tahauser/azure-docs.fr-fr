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
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Si vous avez des problèmes pour vous connecter, effectuez les vérifications suivantes :

- Si vous avez exporté un certificat client, vérifiez que vous l’avez exporté au format .pfx à l’aide de la valeur par défaut « Inclure tous les certificats dans le chemin d’accès de certification, si possible ». Lorsque vous exportez à l’aide de cette valeur, les informations de certificat racine sont également exportées. Lorsque le certificat est installé sur l’ordinateur client, le certificat racine présent dans le fichier .pfx est également installé sur l’ordinateur client. Les informations de certificat racine doivent être installées sur l’ordinateur client. Pour vérifier, accédez à **Gérer les certificats utilisateur**, puis à **Autorités de certification racines de confiance\Certificats**. Vérifiez que le certificat racine est répertorié. Le certificat racine doit être présent pour que l’authentification puisse fonctionner.

- Si vous utilisez un certificat qui a été émis à l’aide d’une autorité de certification d’entreprise et que vous rencontrez des problèmes pour l’authentification, vérifiez l’ordre de l’authentification sur le certificat client. Vous pouvez vérifier l’ordre de la liste d’authentification en double-cliquant sur le certificat client et en accédant à **Détails > Utilisation avancée de la clé**. Vérifiez que la liste affiche « Authentification client » comme premier élément. Si ce n’est pas le cas, vous devez émettre un certificat client basé sur le modèle Utilisateur disposant de l’authentification client comme premier élément dans la liste.

- Pour plus d’informations sur la résolution des problèmes liés à P2S, consultez [Résoudre les problèmes de connexions liés à P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).