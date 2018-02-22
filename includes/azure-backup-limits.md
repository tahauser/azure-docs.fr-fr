---
title: Fichier Include
description: Fichier Include
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk
ms.custom: include file
ms.openlocfilehash: 7ca5b34961b4d0e3d4fcecb8175e3e0901d7049d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
Les limites suivantes s’appliquent à la sauvegarde Azure.

| Identificateur de la limite | Limite par défaut |
| --- | --- |
| Nombre de serveurs/machines pouvant être enregistrés pour chaque coffre |50 pour Windows Server/Client/SCDPM  <br/> 200 pour les machines virtuelles IaaS |
| Taille d’une source de données pour les données stockées dans le stockage de l’archivage Azure |54 400 Go max<sup>1</sup> |
| Nombre de coffres de sauvegarde pouvant être créés dans chaque abonnement Azure |25 coffres Recovery Services par région |
| Nombre de sauvegardes pouvant être planifiées par jour |3 par jour pour Windows Server/Client  <br/> 2 par jour pour SCDPM <br/> Une fois par jour pour les machines virtuelles IaaS |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde |16 |
| Taille du disque de données attaché à une machine virtuelle Azure pour la sauvegarde| 1024 Go <sup>2</sup>|

* <sup>1</sup>La limite de 54 400 Go ne s’applique pas à la sauvegarde de la machine virtuelle IaaS.
* <sup>2</sup> Nous disposons d’une [préversion privée](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0) qui prend en charge les disques jusqu’à 4 To. 

