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
ms.openlocfilehash: 8a49653b4083cbfd17656d701225dcb14f91f637
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Chaque ordinateur client qui se connecte à un réseau virtuel avec une connexion de point à site doit avoir un certificat client installé. Le certificat client est généré à partir du certificat racine et installé sur chaque ordinateur client. Si aucun certificat client valide n’est installé et que le client essaie de se connecter au réseau virtuel, l’authentification échoue.

Vous pouvez soit générer un certificat unique pour chaque client, soir utiliser le même certificat pour plusieurs clients. Le fait de générer des certificats clients uniques vous offre la possibilité de révoquer un seul certificat. Dans le cas contraire, si plusieurs clients utilisent le même certificat client et que vous devez révoquer ce dernier, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent ce certificat pour s’authentifier.

Vous pouvez générer des certificats clients à l’aide des méthodes suivantes :

- **Certificat d’entreprise :**

  - Si vous utilisez une solution de certificat d’entreprise, générez un certificat client avec le format de valeur de nom commun « name@yourdomain.com », plutôt que le format « nom_domaine\nom_utilisateur ».
  - Assurez-vous que le certificat client repose sur le modèle de certificat « Utilisateur » qui indique « Authentification client » comme premier élément dans la liste d’usages, plutôt que la mention « Ouverture de session par carte à puce » ou autre. Vous pouvez vérifier le certificat en double-cliquant sur le certificat client et en affichant **Détails > Utilisation avancée de la clé**.

- **Certificat racine auto-signé :** il est important que vous suiviez les procédures décrites dans l’un des articles de certificat P2S ci-dessous. Dans le cas contraire, les certificats clients que vous créez ne seront pas compatibles avec les connexions P2S, et les clients recevront une erreur lorsqu’ils essaieront de se connecter. Les procédures décrites dans les articles ci-après génèrent un certificat client compatible : 

  * [Instructions pour PowerShell sur Windows 10](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert) : ces instructions requièrent Windows 10 et PowerShell pour générer des certificats. Les certificats qui sont générés peuvent être installés sur n’importe quel client P2S pris en charge.
  * [Instructions pour MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) : si vous n’avez pas accès à un ordinateur Windows 10, utilisez MakeCert pour générer des certificats. MakeCert est déconseillé, mais vous pouvez toujours l’utiliser pour générer des certificats. Les certificats qui sont générés peuvent être installés sur n’importe quel client P2S pris en charge.

  Lorsque vous générez un certificat client à partir d’un certificat racine auto-signé à l’aide des instructions précédentes, ce certificat est automatiquement installé sur l’ordinateur que vous avez utilisé pour le générer. Si vous souhaitez installer un certificat client sur un autre ordinateur client, vous devez l’exporter en tant que .pfx, avec l’intégralité de la chaîne du certificat. Cette opération crée un fichier .pfx contenant les informations de certificat racine qui sont requises pour l’authentification correcte du client. Pour plus d’informations sur la procédure d’exportation d’un certificat, consultez la section [Certificats - Exporter un certificat client](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).