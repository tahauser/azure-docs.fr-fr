---
title: "Azure AD Connect Health - Alerte indiquant que les données du Service de contrôle d’intégrité ne sont pas mises à jour | Microsoft Docs"
description: "Ce document décrit la cause de l’alerte indiquant que les données du Service de contrôle d’intégrité ne sont pas mises à jour et explique comment y remédier."
services: active-directory
documentationcenter: 
author: zhiweiw
manager: maheshu
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 45e88320a64770239c8f322212e12ca7826cd0da
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Les données du Service de contrôle d’intégrité ne sont pas mises à jour

## <a name="overview"></a>Vue d'ensemble
<li>Le service Azure AD Connect Health génère une alerte d’actualisation des données lorsqu’il ne reçoit pas tous les points de données de la part du serveur pendant deux heures. Le titre de l’alerte est **Les données du Service de contrôle d’intégrité ne sont pas mises à jour**. </li>
<li>L’alerte d’état **Avertissement** se déclenche si Connect Health ne reçoit pas d’éléments de données partiels de la part du serveur pendant deux heures. L’alerte d’état Avertissement ne déclenche pas de notifications par e-mail à l’administrateur des clients. </li>
<li>L’alerte d’état **Erreur** se déclenche si Connect Health ne reçoit pas du tout d’éléments de données de la part du serveur pendant deux heures. L’alerte d’état Erreur déclenche des notifications par e-mail à l’administrateur des clients. </li>

>[!IMPORTANT] 
> Cette alerte suit la [stratégie de conservation des données](active-directory-aadconnect-health-gdpr.md#data-retention-policy) de Connect Health.

## <a name="troubleshooting-steps"></a>Étapes de dépannage 
* N’oubliez pas de prendre connaissance et de respecter la [section Configuration requise](active-directory-aadconnect-health-agent-install.md#requirements).
* Utilisez l’[outil de test de connectivité](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) pour découvrir les problèmes de connectivité.


## <a name="next-steps"></a>Étapes suivantes
* [Forum Aux Questions (FAQ) Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
