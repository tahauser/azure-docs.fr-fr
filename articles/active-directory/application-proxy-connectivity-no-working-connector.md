---
title: "Aucun groupe de connecteurs en fonctionnement n’est disponible pour une application de proxy d’application | Microsoft Docs"
description: "Résolvez les problèmes que vous pouvez rencontrer lorsqu’il n’y a aucun connecteur en fonctionnement dans un groupe de connecteurs pour votre application avec le proxy d’application Azure AD"
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
ms.openlocfilehash: 7aad43be4dc411215a4b54c21a16c4cbb949c7ec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Aucun groupe de connecteurs en fonctionnement n’est disponible pour une application de proxy d’application

Cet article vous permet de résoudre les problèmes courants rencontrés lorsque aucun connecteur n’est détecté pour une application de proxy d’application intégrée dans Azure Active Directory.

## <a name="overview-of-steps"></a>Vue d’ensemble des étapes
Si le groupe de connecteurs ne contient aucun connecteur en fonctionnement pour votre application, il existe plusieurs moyens de résoudre le problème :

-   Si le groupe ne contient aucun connecteur, vous pouvez :

    -   Télécharger un nouveau connecteur sur le serveur local approprié et l’affecter à ce groupe

    -   Déplacer un connecteur actif dans le groupe

-   Si le groupe ne contient aucun connecteur actif, vous pouvez :

    -   Identifier la raison de l’inactivité de votre connecteur et résoudre le problème

    -   Déplacer un connecteur actif dans le groupe

Pour savoir quel est le problème, ouvrez le menu Proxy d’application » dans votre application et consultez le message d’avertissement du groupe de connecteurs. Il indique que le groupe a besoin d’au moins un connecteur (vous n’en avez aucun dans le groupe) ou qu’il ne dispose d’aucun connecteur actif (bien que vous ayez probablement des connecteurs inactifs).

   ![Sélection du groupe de connecteurs dans le portail Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Pour plus d’informations sur chacune de ces options, consultez la section appropriée ci-dessous. Chacune d’entre elles suppose que vous démarrez de la page de gestion du connecteur. Si vous consultez le message d’erreur ci-dessus, vous pouvez accéder à cette page en cliquant sur le message d’avertissement. Ou vous pouvez accéder à **Azure Active Directory**, puis cliquer sur **Applications d’entreprise** et sur **Toutes les applications.**

   ![Gestion du groupe de connecteurs dans le portail Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Télécharger un nouveau connecteur

Pour télécharger un nouveau connecteur, utilisez le bouton Télécharger le connecteur en haut de la page.

Le connecteur doit être installé sur un ordinateur avec une visibilité directe sur l’application back-end. Il est généralement placé sur le même serveur que l’application. Une fois le téléchargement terminé, le connecteur doit apparaître dans ce menu. Cliquez sur le connecteur, puis utilisez la liste déroulante « Groupe de connecteurs » pour vérifier qu’il appartient au bon groupe. Enregistrez la modification.

   ![Télécharger le connecteur à partir du portail Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Déplacer un connecteur

Si vous avez un connecteur actif qui doit appartenir au groupe et dispose d’une visibilité directe sur l’application back-end cible, vous pouvez déplacer le connecteur dans le groupe affecté. Pour cela, cliquez sur le connecteur. Dans le champ Groupe de connecteurs, utilisez la liste déroulante pour sélectionner le groupe approprié, puis cliquez sur Enregistrer.

## <a name="resolve-an-inactive-connector"></a>Résoudre le problème d’un connecteur inactif

Si les seuls connecteurs du groupe sont inactifs, ils se trouvent probablement sur un ordinateur dont les ports requis ne sont pas tous débloqués.

Consultez le document relatif au dépannage des ports pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes
[Présentation des connecteurs de proxy d’application Azure AD](application-proxy-understand-connectors.md)


