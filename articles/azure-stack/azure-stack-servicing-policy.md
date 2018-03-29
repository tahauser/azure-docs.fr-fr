---
title: Stratégie de maintenance Azure Stack | Microsoft Docs
description: Découvrez la stratégie de maintenance Azure Stack et comment conserver un système intégré dans un état de prise en charge.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: f495ca12e7cdb1bf61f09bd2d4a8a21654745d8a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-stack-servicing-policy"></a>Stratégie de maintenance Azure Stack
Cet article décrit la stratégie de maintenance pour les systèmes intégrés Azure Stack et la marche à suivre pour conserver votre système dans un état de prise en charge. 

## <a name="update-package-types"></a>Types de packages de mise à jour

Il existe deux types de packages de mise à jour pour les systèmes intégrés : les mises à jour logicielles Microsoft et les mises à jour propres à votre fabricant de matériel OEM, telles que les microprogrammes et les pilotes. Ces mises à jour sont remises sous forme de packages de mise à jour Azure Stack distincts et sont gérées indépendamment.

- **Mises à jour logicielles Microsoft**. Microsoft est responsable du cycle de vie de maintenance de bout en bout pour les packages de mise à jour logicielle Microsoft. Ces packages peuvent inclure les dernières mises à jour de sécurité Windows Server, des mises à jour ne concernant pas la sécurité, et des mises à jour de fonctionnalités Azure Stack. Vous pouvez télécharger ces packages de mise à jour directement à partir de Microsoft.
- **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel partenaires d’Azure Stack sont responsables du cycle de vie de maintenance de bout en bout (y compris les instructions) pour les mises à jour logicielles des microprogrammes et des pilotes liées au matériel. De plus, ils sont propriétaires et gèrent les instructions pour tous les logiciels et le matériel sur l’hôte du cycle de vie de matériel. Le fabricant de matériel OEM héberge ces packages de mise à jour sur son propre site de téléchargement.

## <a name="update-package-release-cadence"></a>Cadence de publication des packages de mise à jour

Microsoft prévoie de publier des packages de mise à jour logicielle chaque mois. Toutefois, il est possible qu’il y ait plusieurs mises à jour durant un mois donné, ou qu’il n’y en ait aucune. Les fabricants de matériel OEM publient leurs mises à jour en fonction des besoins.

Un package de mise à jour Microsoft a la convention de nommage suivante pour vous aider à identifier facilement la date de publication :

*Version_principale_produit.Version_mineure_produit.AAMMJJ.Numéro_Build*

Par exemple, une mise à jour logicielle Microsoft publiée le 15 juin 2017 a la version « 1.0.170615.1 ».

## <a name="keep-your-system-under-support"></a>Conserver votre système dans un état de prise en charge

Pour bénéficier d’une prise en charge de votre système, vous devez mettre à jour votre Azure Stack dans un intervalle de temps spécifique. Notre stratégie de report des mises à jour logicielles Microsoft est de trois mois. Si votre système est obsolète depuis plus de trois mois, vous êtes considéré comme non conforme. Pour bénéficier de la prise en charge, vous devez mettre à jour le système avec au moins la version minimale prise en charge. 

Les packages de mise à jour logicielle Microsoft sont non cumulatifs et nécessitent le package de mise à jour précédent comme prérequis. Si vous décidez de reporter une ou plusieurs mises à jour, pensez-y si vous souhaitez obtenir la dernière version.

Le tableau suivant présente des exemples de versions de package de mise à jour, leur prérequis et la version minimale prise en charge dont votre système doit disposer pour bénéficier de la prise en charge. Ce tableau est basé sur la version initiale des systèmes intégrés Azure Stack (build 1708), avec la première publication de package de mise à jour (1709) en septembre 2017. 

| Package de mise à jour le plus récent (*exemple*) | Configuration requise | Version minimale prise en charge |
| -- | -- | -- |
| 1710 | 1709 | N/A |
| 1711 | 1710 | 1709 |
| 1712 | 1711 | 1710 |
| 1802 | 1801 | 1712 |
| 1803 | 1802 | 1801 |
| 1804 | 1803 | 1802 |
| 1805 | 1804 | 1803 |
| | | 

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les mises à jour dans Azure Stack](azure-stack-updates.md)


