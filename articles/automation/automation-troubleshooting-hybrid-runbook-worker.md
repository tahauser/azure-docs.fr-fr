---
title: "Dépanner les runbooks Workers hybrides d’Azure Automation | Microsoft Docs"
description: "Décrit les symptômes, les causes et les solutions liés aux problèmes de runbooks Worker hybrides les plus courants."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.openlocfilehash: 75f4ac1bc940a2b1d8e4ac6aeac8b80c642489da
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Conseils de dépannage pour les runbooks Workers hybrides

Cet article aide à dépanner les erreurs que vous pourriez rencontrer avec les runbooks Workers hybrides d’Azure Automation et suggère des solutions possibles pour les résoudre.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Une tâche de runbook se termine avec l’état suspendu.

Le runbook est interrompu peu après la tentative de l’exécuter trois fois. Il existe des conditions susceptibles d’interrompre l’exécution correcte du runbook et le message d’erreur lié n’inclut pas d’informations supplémentaires indiquant pourquoi. Cet article fournit des étapes de dépannage pour des problèmes concernant les échecs d’exécution du runbook worker hybride.

Si le problème lié à Azure n’est pas traité dans cet article, parcourez les forums Azure sur [MSDN et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez publier votre problème sur ces forums ou dans [@AzureSupport sur Twitter](https://twitter.com/AzureSupport). Vous pouvez également créer une demande de support Azure en sélectionnant **Obtenir de l’aide** sur le site du [support Azure](https://azure.microsoft.com/support/options/) .

### <a name="symptom"></a>Symptôme
L’exécution du runbook échoue et l’erreur est retournée est « L’action de la tâche ’Activate’ ne peut pas être exécutée, car le processus s’est arrêté inopinément. L’action de la tâche a été tentée 3 fois. »

Il existe plusieurs causes possibles pour cette erreur : 

1. Le worker hybride est derrière un pare-feu ou un proxy
2. L’ordinateur sur lequel le worker hybride s’exécute ne respecte pas la [configuration matérielle](automation-offering-get-started.md#hybrid-runbook-worker) minimale requise  
3. Les runbooks ne peuvent pas s’authentifier auprès des ressources locales

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Cause 1 : Le runbook worker hybride est derrière un proxy ou un pare-feu
L’ordinateur sur lequel s’exécute le runbook worker hybride est derrière un pare-feu ou un serveur proxy, et un accès réseau sortant peut ne pas être autorisé ou configuré correctement.

#### <a name="solution"></a>Solution
Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur le port 443. 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Cause 2 : L’ordinateur ne dispose pas de la configuration minimale requise
Les ordinateurs qui exécutent les workers hybrides doivent respecter la configuration matérielle minimale requise avant de pouvoir héberger cette fonctionnalité. Sinon, en fonction de l’utilisation des ressources d’autres processus d’arrière-plan et de la contention due aux runbooks lors de l’exécution, l’ordinateur sera surchargé, entraînant des retards ou des délais d’attente pour la tâche du runbook. 

#### <a name="solution"></a>Solution
Vérifiez tout d’abord que l’ordinateur désigné pour exécuter la fonctionnalité Runbook Worker hybride répond à la configuration matérielle minimale requise.  Si c’est le cas, surveillez l’utilisation du processeur et de la mémoire pour déterminer toute corrélation entre les performances des processus Runbook Worker hybride et de Windows.  S’il existe une surcharge de la mémoire ou du processeur, cela peut indiquer la nécessité de mettre à niveau ou d’ajouter des processeurs supplémentaires, ou d’augmenter la mémoire pour résoudre le goulot d’étranglement des ressources et résoudre l’erreur. Vous pouvez également sélectionner une ressource de calcul différente qui peut prendre en charge la configuration minimale requise et évoluer lorsque les demandes en matière de charge de travail indiquent qu’une augmentation est nécessaire.         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Cause 3 : Les runbooks ne peuvent pas s’authentifier auprès des ressources locales

#### <a name="solution"></a>Solution
Vérifiez dans le journal des événements **Microsoft-SMA** la présence d’un événement correspondant avec la description *Le processus Win32 s’est terminé avec le code [4294967295]*.  La cause de cette erreur est que vous n’avez pas configuré l’authentification dans vos runbooks ou que vous n’avez pas spécifié les informations d’identification Exécuter en tant que pour le groupe Worker hybride.  Veuillez consulter les [autorisations du Runbook](automation-hrw-run-runbooks.md#runbook-permissions) pour confirmer que l’authentification a été correctement configurée pour vos Runbooks.  

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir de l’aide relative à d’autres problèmes dans Automation, consultez [Dépannage des problèmes courants dans Azure Automation](automation-troubleshooting-automation-errors.md) 