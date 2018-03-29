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
ms.openlocfilehash: 517ebbf739c64c0364dc21386fee86ebc740e997
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Lorsque vous générez un certificat client, il est automatiquement installé sur l’ordinateur que vous avez utilisé pour le générer. Si vous souhaitez installer le certificat client sur un autre ordinateur client, il vous faut exporter le certificat client généré.

1. Pour exporter un certificat client, ouvrez **Gérer les certificats utilisateur**. Les certificats clients que vous avez générés se trouvent par défaut dans « Certificates - Current User\Personal\Certificates ». Cliquez avec le bouton droit sur le certificat client à exporter, cliquez sur **Toutes les tâches**, puis cliquez sur **Exporter** pour ouvrir **l’Assistant Exportation du certificat**.

  ![Exportation](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. Dans l’Assistant Exportation de certificat, cliquez sur **Suivant** pour continuer.

  ![Suivant](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.

  ![Exporter la clé privée](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Dans la page **Format de fichier d’exportation**, laissez les valeurs par défaut sélectionnées. Assurez-vous que l’option **Inclure tous les certificats dans le chemin d’accès de certification si possible** est sélectionnée. Ce paramètre exporte également les informations de certificat racine qui sont nécessaires à la réussite de l’authentification du client. Sans cela, l’authentification du client échoue car le client n’a pas le certificat racine approuvé. Cliquez ensuite sur **Suivant**.

  ![Format de fichier d’exportation](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Dans la page **Sécurité** , vous devez protéger la clé privée. Si vous choisissez d’utiliser un mot de passe, veillez à enregistrer ou à mémoriser celui que vous définissez pour ce certificat. Cliquez ensuite sur **Suivant**.

  ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Dans **Fichier à exporter**, cliquez sur **Parcourir** pour accéder à l’emplacement vers lequel vous souhaitez exporter le certificat. Pour la zone **Nom de fichier**, nommez le fichier de certificat. Cliquez ensuite sur **Suivant**.

  ![Fichier à exporter](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Cliquez sur **Terminer** pour exporter le certificat.

  ![Terminer](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)