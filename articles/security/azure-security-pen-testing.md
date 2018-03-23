---
title: Test de pénétration | Microsoft Docs
description: Cet article fournit une vue d’ensemble du processus de test de pénétration (pentest) et explique comment effectuer ce test sur vos applications exécutées dans l’infrastructure Azure.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: 5f2a72d133218e72d1db86b787c998cc50269d3a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="pen-testing"></a>Test de pénétration
L’un des avantages liés à l’utilisation d’Azure pour tester et déployer des applications est que vous pouvez créer rapidement des environnements.  Vous n’avez pas à vous soucier des aspects liés à la demande, à l’acquisition, à la mise en rack et à l’empilage de votre propre matériel en local.

Tout cela est très bien, certes, mais il vous reste toujours à effectuer les contrôles de sécurité standard. Vous devez notamment soumettre les applications que vous déployez dans Azure à un test de pénétration.

Vous savez sans doute déjà que Microsoft effectue le [test de pénétration de l’environnement Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ceci permet de dynamiser les améliorations d’Azure. 

Nous n’effectuons pas les tests de pénétration à votre place, mais nous savons parfaitement qu’il est important pour vous d’exécuter ces tests sur vos propres applications. Et nous ne pouvons que vous en remercier car, lorsque vous améliorez la sécurité de vos applications, vous renforcez la sécurité de l’ensemble de l’écosystème Azure.

Que faire, alors ?

Depuis le 15 juin 2017, une approbation préalable n’est plus exigée par Microsoft pour réaliser des tests d’intrusion sur les ressources Azure. Les clients qui souhaitent officiellement attester de leurs engagements relatifs aux tests d’intrusion à venir auprès de Microsoft Azure sont invités à remplir le [formulaire Azure Service Penetration Testing Notification](https://portal.msrc.microsoft.com/en-us/engage/pentest) (Notification de tests d’intrusion sur les services Azure). Ce processus concerne uniquement Microsoft Azure et ne s’applique à aucun autre service cloud de Microsoft. 

>[!IMPORTANT] 
>Même s’il n’est plus nécessaire de prévenir Microsoft d’une activité de test d’intrusion, les clients doivent toujours respecter les [Règles d’engagement pour les tests d’intrusion unifiés du cloud Microsoft](https://technet.microsoft.com/en-us/mt784683). 

Vous avez la possibilité d’effectuer plusieurs tests :

* Tests sur vos points de terminaison visant à détecter les [10 principales vulnérabilités de l’OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzzing](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de vos points de terminaison
* [Balayage des ports](https://en.wikipedia.org/wiki/Port_scanner) de vos points de terminaison

En revanche, vous ne pouvez effectuer aucune forme [d’attaque par déni de service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), c’est-à-dire que nous ne pouvez ni initier une attaque par déni de service proprement dite, ni effectuer les tests associés permettant de déterminer, démontrer ou simuler n’importe quel type d’attaque de déni de service.

## <a name="next-steps"></a>Étapes suivantes

- Prêt à vous lancer dans les tests de pénétration de vos applications hébergées dans Microsoft Azure ? Si tel est le cas, rendez-vous sur la page [Penetration Test Overview](https://technet.microsoft.com/library/mt784683.aspx) (Présentation du test de pénétration) et cliquez sur le bouton Create a Testing Request (Créer une demande de test) au bas de la page. 