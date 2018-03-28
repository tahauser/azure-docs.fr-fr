---
title: Comment résoudre les problèmes courants lors de la création du disque dur virtuel | Microsoft Docs
description: Réponses aux questions courantes de dépannage et aux problèmes rencontrés lors de la création du disque dur virtuel.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: mbaldwin
ms.openlocfilehash: 65361ad5bd7c3311c428b64b8476ec8f2ea2d17b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Comment résoudre les problèmes courants rencontrés lors de la création du disque dur virtuel
Cet article aide les éditeurs et/ou coadministrateurs Azure Marketplace qui peuvent rencontrer un problème ou avoir des questions courantes lors de la publication ou de la gestion de leurs solutions de machine virtuelle.

1. Comment modifier le nom de l’hôte ?
   
    Une fois que la machine virtuelle est créée, il est impossible pour les utilisateurs de mettre à jour le nom de l’hôte.
2. Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion ?
   
   * [Référence pour les machines virtuelles Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Référence pour les machines virtuelles Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Comment générer de nouveaux certificats ssh ?
   
   Veuillez vous reporter au lien : [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Comment configurer un certificat VPN ouvert ?
   
   Veuillez vous reporter au lien : [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Quelle est la stratégie de prise en charge pour l’exécution de logiciels serveur Microsoft dans un environnement de machine virtuelle Microsoft Azure (infrastructure en tant que service) ?
   
   Veuillez vous reporter au lien : [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Les machines virtuelles ont-elles un identificateur unique ?
   
   Azure encode l’ID unique de machine virtuelle Azure dans chaque machine virtuelle. Pour plus d’informations, consultez le blog et la documentation.
7. Dans une machine virtuelle, comment puis-je gérer l’extension de script personnalisé lors de la tâche de démarrage ?
   
   Veuillez vous reporter au lien : [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Comment créer une machine virtuelle à partir du portail Azure à l’aide du disque dur virtuel qui est téléchargé vers le stockage Premium ?
   
   Nous ne prenons pas encore en charge cette fonctionnalité.
9. Les applications 32 bits sont-elles prises en charge dans Azure Marketplace ?
   
   Reportez-vous au lien suivant pour plus d’informations sur la stratégie de prise en charge : [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Chaque fois que j’essaie de créer une image à partir de mes disques durs virtuels, j’obtiens l’erreur « .VHD is already registered with image repository as the resource » (Le disque dur virtuel est déjà enregistré avec le référentiel d’image en tant que ressource) dans PowerShell. Je n’ai pas créé d’image au préalable et je n’ai trouvé aucune image portant ce nom dans Azure. Comment résoudre ce problème ?
    
    Cela se produit généralement si l’utilisateur a provisionné une machine virtuelle à partir de ce disque dur virtuel et si le disque dur virtuel est verrouillé. Vérifiez qu’aucune machine virtuelle n’est allouée à partir de ce disque dur virtuel. Si l’erreur persiste, ouvrez un ticket de support à l’aide de ce lien ou du portail de publication (toutes les informations sont fournies dans la réponse à la question 11).