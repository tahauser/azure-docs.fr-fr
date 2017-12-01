---
title: "Test de pénétration | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble du processus de test de pénétration (pentest) et explique comment effectuer ce test sur vos applications exécutées dans l’infrastructure Azure."
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
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 3ad22e78693c54c62f9230f7f52460e01e5e0022
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="pen-testing"></a>Test de pénétration
L’un des avantages liés à l’utilisation d’Azure pour tester et déployer des applications est que vous pouvez créer rapidement des environnements.  Vous n’avez pas à vous soucier des aspects liés à la demande, à l’acquisition, à la mise en rack et à l’empilage de votre propre matériel en local.

Tout cela est très bien, certes, mais il vous reste toujours à effectuer les contrôles de sécurité standard. Vous devez notamment soumettre les applications que vous déployez dans Azure à un test de pénétration.

Vous savez sans doute déjà que Microsoft effectue le [test de pénétration de l’environnement Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ceci permet de dynamiser les améliorations d’Azure. 

Nous n’effectuons pas les tests de pénétration à votre place, mais nous savons parfaitement qu’il est important pour vous d’exécuter ces tests sur vos propres applications. Et nous ne pouvons que vous en remercier car, lorsque vous améliorez la sécurité de vos applications, vous renforcez la sécurité de l’ensemble de l’écosystème Azure.

Or, lorsque vous soumettez vos applications à un test de pénétration, nous pouvons le percevoir comme une attaque. Nous [surveillons en permanence](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) les schémas d’attaque et initions un processus de réponse aux incidents si les circonstances nous y obligent. Mais lancer ce processus dans le cadre de vos propres tests de pénétration ne présente aucun intérêt, pour vous comme pour nous.

Que faire, alors ?

Quand vous êtes prêt à effectuer des tests de pénétration sur vos applications hébergées sur Azure, vous pouvez [nous en informer](https://portal.msrc.microsoft.com/en-us/engage/pentest). Une fois informé, Microsoft ne prendra aucune mesure à votre encontre (par exemple, en bloquant l’adresse IP à partir de laquelle vous effectuez vos tests). Vos tests doivent être conformes aux conditions de test de pénétration Azure décrites dans [Microsoft Cloud Unified Penetration Testing Rules of Engagement](https://technet.microsoft.com/en-us/mt784683) (Règles d’engagement pour les tests de pénétration unifiés du cloud Microsoft).

Vous avez la possibilité d’effectuer plusieurs tests :

* Tests sur vos points de terminaison visant à détecter les [10 principales vulnérabilités de l’OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzzing](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de vos points de terminaison
* [Balayage des ports](https://en.wikipedia.org/wiki/Port_scanner) de vos points de terminaison

En revanche, vous ne pouvez effectuer aucune forme [d’attaque par déni de service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), c’est-à-dire que nous ne pouvez ni initier une attaque par déni de service proprement dite, ni effectuer les tests associés permettant de déterminer, démontrer ou simuler n’importe quel type d’attaque de déni de service.

## <a name="next-steps"></a>Étapes suivantes

- Prêt à vous lancer dans les tests de pénétration de vos applications hébergées dans Microsoft Azure ? Si tel est le cas, rendez-vous sur la page [Penetration Test Overview](https://technet.microsoft.com/library/mt784683.aspx) (Présentation du test de pénétration) et cliquez sur le bouton Create a Testing Request (Créer une demande de test) au bas de la page. 