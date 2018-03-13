---
title: "Résolution des problèmes liés aux erreurs du pack de contenu des journaux d’activité Azure Active Directory | Microsoft Docs"
description: "Fournit la liste des messages d’erreur du pack de contenu d’activité Azure Active Directory et les étapes à suivre pour les corriger."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b22f387a0338246c7586f0f0735b4612a796691a
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Résolution des problèmes liés aux erreurs du pack de contenu des journaux d’activité Azure Active Directory 


Pendant que vous utilisez le pack de contenu Power BI pour Azure Active Directory Preview, vous pouvez rencontrer les erreurs suivantes : 

- [Échec de l’actualisation](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [Échec de la mise à jour des informations d’identification de la source de données](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [L’importation de données prend trop de temps](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Cette rubrique traite des causes possibles de ces erreurs et explique comment les corriger.
 
## <a name="refresh-failed"></a>Échec de l’actualisation 
 
**Circonstances de l’apparition de l’erreur** : message électronique en provenance de Power BI ou état d’échec dans l’historique d’actualisation. 


| Cause : | Procédure de résolution |
| ---   | ---        |
| Les erreurs d’échec d’actualisation peuvent être générées quand les informations d’identification des utilisateurs se connectant au pack de contenu ont été réinitialisées, mais pas mises à jour dans les paramètres de connexion du pack de contenu. | Dans Power BI, recherchez le jeu de données correspondant au tableau de bord Journaux d’activité Azure Active Directory (journaux d’activité Azure Active Directory), choisissez Planifier l’actualisation, puis entrez vos informations d’identification Azure AD. |
| Une actualisation peut échouer à cause de problèmes de données dans le pack de contenu sous-jacent. | Soumettez un ticket de support. Pour plus d’informations, consultez [Comment obtenir une assistance pour Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Échec de la mise à jour des informations d’identification de la source de données 
 
**Circonstances de l’apparition de cette erreur** : dans Power BI, pendant que vous vous connectez au pack de contenu des journaux d’activité Azure Active Directory (version préliminaire). 

| Cause : | Procédure de résolution |
| ---   | ---        |
| L’utilisateur qui se connecte n’est ni administrateur général, ni lecteur sécurité, ni administrateur de sécurité. | Utilisez un compte ayant le rôle d’administrateur général, de lecteur sécurité ou d’administrateur de la sécurité pour accéder aux packs de contenu. |
| Votre locataire n’est pas de type Premium ou ne compte aucun utilisateur avec un fichier de licence Premium. | Soumettez un ticket de support. Pour plus d’informations, consultez [Comment obtenir une assistance pour Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>L’importation de données prend trop de temps 
 
**Circonstances de l’apparition de cette erreur** : dans Power BI, une fois que vous avez connecté votre pack de contenu, le processus d’importation de données démarre pour préparer votre tableau de bord pour le journal d’activité Azure Active Directory. Vous obtenez le message suivant : « *L’importation de données...* »  

| Cause : | Procédure de résolution |
| ---   | ---        |
| Selon la taille de votre locataire, cette étape peut prendre de quelques minutes à 30 minutes. | Soyez patient. Si votre tableau de bord ne s’affiche pas à la place du message au bout d’une heure, soumettez un ticket de support. Pour plus d’informations, consultez [Comment obtenir une assistance pour Azure Active Directory](active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Étapes suivantes

Pour installer le pack de contenu Power BI pour Azure Active Directory Preview, cliquez [ici](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/).


