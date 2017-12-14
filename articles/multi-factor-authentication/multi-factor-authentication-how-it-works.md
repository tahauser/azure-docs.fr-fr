---
title: "Azure Multi-Factor Authentication : fonctionnement"
description: "Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Il fournit une sécurité supplémentaire en exigeant un deuxième formulaire d'authentification et fournit une authentification renforcée via un éventail d'options de vérification simples."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 0791ec4c3bf4a69e2a93b8f3dd1dcf9b649f4cff
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication : fonctionnement
La sécurité de la vérification en deux étapes repose sur son approche en couche. Compromettre plusieurs facteurs d'authentification présente un défi de taille pour les attaquants. Même si un attaquant réussit à connaître le mot de passe de l'utilisateur, ce dernier est inutile sans posséder l'appareil de confiance. 

![Vérification](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple.  Il fournit une sécurité supplémentaire en exigeant un deuxième formulaire d'authentification et fournit une authentification renforcée via un éventail d'options de vérification simples.


## <a name="methods-available-for-two-step-verification"></a>Méthodes disponibles pour la vérification en deux étapes
Lorsqu'un utilisateur se connecte, une vérification supplémentaire lui est envoyée.  Voici la liste des méthodes qui peuvent être utilisées pour cette seconde vérification.

| Méthode de vérification | Description |
| --- | --- |
| appel téléphonique |Un appel est passé au numéro de téléphone enregistré d’un utilisateur. L’utilisateur entre un code PIN si nécessaire, puis appuie sur la touche #. |
| SMS |Un SMS est envoyé sur le téléphone mobile de l’utilisateur avec un code à six chiffres. L’utilisateur entre ce code sur la page de connexion. |
| Notification sur l’application mobile |Une demande de vérification est envoyée au smartphone d’un utilisateur. L’utilisateur entre un code PIN si nécessaire, puis sélectionne **Vérifier** sur l’application mobile. |
| Code de vérification de l’application mobile |L’application mobile, qui est exécutée sur le smartphone d’un utilisateur, affiche un code de vérification qui change toutes les 30 secondes. L’utilisateur trouve le code le plus récent et le saisit sur la page de connexion. |
| Jetons OATH tiers | Le serveur Azure Multi-Factor Authentication peut être configuré pour accepter les méthodes de vérification tierces. |

Azure Multi-Factor Authentication fournit des méthodes de vérification sélectionnables pour cloud et pour serveur. Vous pouvez choisir les méthodes mises à la disposition de vos utilisateurs : appel téléphonique, SMS, notification sur l’application ou codes d’application. Pour plus d’informations, consultez [Méthodes de vérification sélectionnables](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les différentes [versions et méthodes de consommation pour Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

- Choisissez de déployer Azure MFA [dans le cloud ou en local](multi-factor-authentication-get-started.md)

- Obtenez des réponses aux [questions fréquemment posées](multi-factor-authentication-faq.md).