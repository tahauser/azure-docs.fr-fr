---
title: Fichier Include
description: Fichier Include
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: b345283f87c446ff3b583b0c5dd8a4be222303ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Les limites suivantes s’appliquent à la sauvegarde Azure.

| Identificateur de la limite | Limite par défaut |
| --- | --- |
| Nombre de serveurs/machines pouvant être enregistrés pour chaque coffre |50 pour Windows Server/Client/SCDPM  <br/> 200 pour les machines virtuelles IaaS |
| Taille d’une source de données pour les données stockées dans le stockage de l’archivage Azure |54 400 Go max<sup>1</sup> |
| Nombre de coffres de sauvegarde pouvant être créés dans chaque abonnement Azure |25 coffres Recovery Services par région |
| Nombre de sauvegardes pouvant être planifiées par jour |3 par jour pour Windows Server/Client  <br/> 2 par jour pour SCDPM <br/> Une fois par jour pour les machines virtuelles IaaS |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde |16 |
| Taille du disque de données attaché à une machine virtuelle Azure pour la sauvegarde| 4095 Go <sup>2</sup>|

* <sup>1</sup>La limite de 54 400 Go ne s’applique pas à la sauvegarde de la machine virtuelle IaaS.
 

