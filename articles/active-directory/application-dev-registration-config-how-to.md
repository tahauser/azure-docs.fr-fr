---
title: "Guide pratique pour sélectionner les autorisations pour une API donnée | Microsoft Docs"
description: "Guide pratique pour trouver les points de terminaison d’authentification pour une application personnalisée que vous développez ou que vous inscrivez auprès d’Azure AD."
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 6c8b8bb81e62747b7ab5eaca94d2820d2e0661d2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Guide pratique pour sélectionner les autorisations pour une API donnée

Vous pouvez trouver les points de terminaison d’authentification pour votre application dans le [portail Azure](https://portal.azure.com).

-   Accédez au [portail Azure](https://portal.azure.com).

-   Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.

-   Cliquez sur **Inscriptions des applications** puis choisissez **Points de terminaison**.

-   Ceci ouvre la page **Points de terminaison** qui répertorie tous les points de terminaison d’authentification pour votre locataire.

-   Utilisez le point de terminaison spécifique au protocole d’authentification que vous utilisez, en combinaison avec l’ID d’application pour créer la demande d’authentification spécifique à votre application.

## <a name="next-steps"></a>Étapes suivantes
[Guide du développeur Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
