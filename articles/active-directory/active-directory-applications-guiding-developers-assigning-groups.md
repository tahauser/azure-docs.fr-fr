---
title: "Attribuer des groupes à des applications Azure AD | Microsoft Docs"
description: "Implémentation de l’attribution de groupe pour les applications Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: mtillman
editor: 
ms.assetid: 29b5ba89-a1c7-4f1f-a294-248a40106617
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
robots: noindex
ms.openlocfilehash: 471099c58a7fc1dfd520e693873a76f64c0fbf0a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="assign-azure-active-directory-groups-to-an-application"></a>Attribuer des groupes Azure Active Directory à une application
Avant d'affecter des utilisateurs et des groupes à une application, vous devez demander l'affectation de l'utilisateur. Pour savoir comment demander l’affectation d’utilisateurs, consultez l’article [Demande de l’affectation de l’utilisateur](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

Cet article suppose que vous avez déjà créé des groupes dans le répertoire actif que vous utilisez pour cette application.

## <a name="assigning-groups-to-an-application"></a>Affectation de groupes à une application
1. Connectez-vous au portail Azure avec un compte administrateur.
2. Dans le menu principal, cliquez sur **Tous les éléments** .
3. Choisissez le répertoire que vous utilisez pour l’application.
4. Cliquez sur l’onglet **APPLICATIONS** .
5. Sélectionnez l'application dans la liste des applications associées à ce répertoire.
6. Cliquez sur l’onglet **UTILISATEURS ET GROUPES** .
7. Filtrez la liste des groupes dans le répertoire actif à l’aide de la liste déroulante **Groupes** .
8. Sélectionnez le groupe.
9. Cliquez sur **AFFECTER**.
10. Cliquez sur **Oui** lorsque vous y êtes invité.

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
