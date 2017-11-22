---
title: "Vérification en deux étapes dans Azure MFA | Microsoft Docs"
description: "En quoi consiste Azure Multi-Factor Authentication (MFA), pourquoi l’utiliser, informations sur le client MFA, différentes méthodes et versions disponibles. "
keywords: "introduction à l'authentification multifacteur, vue d'ensemble de l'authentification multifacteur, définition de l'authentification multifacteur"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: richagi
ms.assetid: c40d7a34-1274-4496-96b0-784850c06e9b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2017
ms.author: joflore
ms.openlocfilehash: a928344dfc41687c92e7845bc304a05122ab8a92
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-multi-factor-authentication"></a>Présentation d'Azure Multi-Factor Authentication
La vérification en deux étapes est une méthode d’authentification qui nécessite plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. Cette authentification fonctionne en nécessitant au minimum deux des méthodes de vérification suivantes :

* Un élément que vous connaissez (généralement un mot de passe)
* Un élément que vous possédez (un appareil de confiance qui n'est pas facilement dupliqué, comme un téléphone)
* Un élément vous concernant particulièrement (biométrie)

<center>![Nom d’utilisateur et mot de passe](./media/multi-factor-authentication/pword.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Certificats](./media/multi-factor-authentication/phone.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Smartphone](./media/multi-factor-authentication/hware.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Carte à puce](./media/multi-factor-authentication/smart.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Carte à puce virtuelle](./media/multi-factor-authentication/vsmart.png)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Nom d’utilisateur et mot de passe](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) est la solution de vérification en deux étapes de Microsoft. Azure MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Il fournit une authentification forte au moyen de diverses méthodes de vérification, notamment les appels téléphoniques, l’envoi de SMS ou la vérification sur l’application mobile.

## <a name="why-use-azure-multi-factor-authentication"></a>Pourquoi utiliser Azure Multi-Factor Authentication ?
Aujourd’hui, plus que jamais, les personnes sont de plus en plus connectées. Grâce aux smartphones, tablettes, ordinateurs portables et PC, les utilisateurs disposent de plusieurs options pour accéder à leurs comptes et aux applications où qu’ils se trouvent et pour rester en permanence connectés.

Azure Multi-Factor Authentication est une solution facile à utiliser, évolutive et fiable qui fournit une deuxième méthode d’authentification pour protéger vos utilisateurs.

| ![Facile à utiliser](./media/multi-factor-authentication/simple.png) | ![Évolutif](./media/multi-factor-authentication/scalable.png) | ![Toujours protégé](./media/multi-factor-authentication/protected.png) | ![Fiable](./media/multi-factor-authentication/reliable.png) |
|:---:|:---:|:---:|:---:|
| **Facile à utiliser** |**Évolutif** |**Toujours protégé** |**Fiable** |

* **Facile à utiliser** : Azure Multi-Factor Authentication est simple à configurer et utiliser. La protection supplémentaire fournie par Azure Multi-Factor Authentication permet aux utilisateurs de gérer leurs propres appareils. De plus, dans de nombreux cas elle peut être configurée de manière très simple.
* **Évolutif** : Azure Multi-Factor Authentication utilise la puissance du cloud et s’intègre à votre site Active Directory local et à vos applications personnalisées. Cette protection est même étendue à vos scénarios à mission critique volumineux.
* **Toujours protégé** : Azure Multi-Factor Authentication fournit une authentification forte qui utilise les normes les plus strictes du secteur.
* **Fiable** : Microsoft garantit une disponibilité à 99,9 % d’Azure Multi-Factor Authentication. Le service est considéré comme non disponible quand il ne parvient pas à recevoir ou traiter des demandes de vérification pour la vérification en deux étapes.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [fonctionnement d’Azure Multi-Factor Authentication](multi-factor-authentication-how-it-works.md)

- En savoir plus sur les différentes [versions et méthodes de consommation pour Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)
