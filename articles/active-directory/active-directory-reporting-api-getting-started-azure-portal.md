---
title: "Prise en main de l’API de création de rapports Azure AD | Microsoft Docs"
description: "Prise en main de l'API de création de rapports Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9c858b8f2d5a4a348bc0b4443ddbe0000a5b62f4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Prise en main de l’API de création de rapports Azure Active Directory

Azure Active Directory vous fournit plusieurs rapports. Les données de ces rapports peuvent être très utiles pour vos applications, telles que les systèmes SIEM, l’audit et les outils d’analyse décisionnelle. Les API de création de rapports Azure AD fournissent un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

Cet article vous fournit les informations dont vous avez besoin pour vous familiariser avec l’API de création de rapports Azure AD.
Dans la section suivante, vous trouverez plus d’informations sur l’utilisation des API d’audit et de connexion. 

Pour consulter les questions les plus fréquentes, lisez notre [FAQ](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-faq). En cas de problème, [Envoyez un ticket de support](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="learning-map"></a>Carte d’apprentissage
1. **Préparer** : avant de pouvoir tester les exemples d’API, vous devez respecter la [configuration requise pour accéder à l’API de création de rapports Azure AD](active-directory-reporting-api-prerequisites-azure-portal.md).
2. **Explorer** : faites-vous une première impression des API de création de rapports :
   
   * [Utilisation des exemples pour l’API d’audit](active-directory-reporting-api-audit-samples.md) 
   * [Utilisation des exemples pour l’API de création de rapports sur l’activité de connexion](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Personnaliser** : créez votre propre solution : 
   
   * [Utilisation de la référence d’API d’audit](active-directory-reporting-api-audit-reference.md) 
   * [Utilisation de la référence d’API de création de rapports sur l’activité de connexion](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Étapes suivantes
Si vous voulez consulter l’ensemble des points de terminaison de l’API Azure AD Graph disponibles, utilisez ce lien : [https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta](https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta).

