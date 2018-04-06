---
title: 'Azure AD Connect : résoudre les problèmes de synchronisation d’objets | Microsoft Docs'
description: Cet article explique comment résoudre les problèmes de synchronisation d’objets à l’aide de la tâche de résolution des problèmes.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: billmath
ms.openlocfilehash: 54ae18b9a802fe078d307f4d36400adf806b233f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Résoudre les problèmes de synchronisation d’objets avec la synchronisation Azure AD Connect
Ce document explique comment résoudre les problèmes de synchronisation d’objets à l’aide de la tâche de résolution des problèmes.

## <a name="troubleshooting-task"></a>Tâche de résolution des problèmes
Dans le cas d’un déploiement d’Azure Active Directory (Azure AD) Connect version 1.1.749.0 ou ultérieure, utilisez la tâche de résolution des problèmes disponible dans l’Assistant pour résoudre les problèmes de synchronisation d’objets. Pour les versions antérieures, corrigez les problèmes manuellement en suivant la procédure décrite [ici](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Exécuter la tâche de résolution des problèmes de l’Assistant
Pour exécuter la tâche de résolution des problèmes de l’Assistant, procédez comme suit :

1.  Ouvrez une nouvelle session Windows PowerShell sur votre serveur Azure AD Connect avec l’option Exécuter en tant qu’administrateur.
2.  Exécutez `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.
3.  Lancez l’Assistant Azure AD Connect.
4.  Accédez à la page Tâches supplémentaires, sélectionnez Résoudre les problèmes, puis cliquez sur Suivant.
5.  Dans la page de résolution des problèmes, cliquez sur Lancer pour ouvrir le menu de dépannage de PowerShell.
6.  Dans le menu principal, sélectionnez Troubleshoot Object Synchronization (Résoudre les problèmes de synchronisation d’objets).

### <a name="troubleshooting-input-parameters"></a>Paramètres d’entrée de la tâche de résolution des problèmes
La tâche de résolution des problèmes requiert les paramètres d’entrée suivants :
1.  **Nom unique de l’objet** : nom unique de l’objet auquel doit s’appliquer la résolution des problèmes.
2.  **Nom du Connecteur AD** : nom de la forêt AD où réside l’objet ci-dessus.
3.  Informations d’identification d’administrateur général de locataire Azure AD ![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Comprendre les résultats de la tâche de résolution des problèmes
La tâche de résolution des problèmes effectue les vérifications suivantes :

1.  détection d’une incompatibilité de nom d’utilisateur principal (UPN) si l’objet est synchronisé avec Azure Active Directory ;
2.  vérification si l’objet a été exclu en raison d’un filtrage de domaine ;
3.  vérification si l’objet a été exclu en raison d’un filtrage d’unité d’organisation.

Le reste de cette section décrit les résultats spécifiques qui sont renvoyés par la tâche. Dans chaque cas, la tâche fournit une analyse suivie des actions recommandées pour résoudre le problème.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Détection d’une incompatibilité de nom d’utilisateur principal (UPN) si l’objet est synchronisé avec Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Suffixe UPN NON vérifié auprès du locataire Azure AD
Lorsque le suffixe d’UPN/ID de connexion alternatif n’est pas vérifié auprès du locataire Azure AD, Azure Active Directory remplace les suffixes UPN par le nom de domaine par défaut « onmicrosoft.com ».

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Remplacement du suffixe UPN d’un domaine fédéré par celui d’un autre domaine fédéré
Azure Active Directory n’autorise pas la synchronisation d’un remplacement de suffixe d’UPN/ID de connexion alternatif d’un domaine fédéré par celui d’un autre domaine fédéré. Ceci s’applique aux domaines qui sont vérifiés auprès du locataire Azure AD et qui présentent le type d’authentification Fédéré.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Fonctionnalité DirSync « SynchronizeUpnForManagedUsers » du locataire Azure AD désactivée
Lorsque la fonctionnalité DirSync « SynchronizeUpnForManagedUsers » du locataire Azure AD est désactivée, Azure Active Directory n’autorise pas les mises à jour de synchronisation d’UPN/ID de connexion alternatif pour les comptes d’utilisateur sous licence avec authentification gérée.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Objet exclu en raison d’un filtrage de domaine
### <a name="domain-is-not-configured-to-sync"></a>Domaine non configuré pour la synchronisation
L’objet n’entre pas dans le champ d’application de la synchronisation, car le domaine n’est pas configuré. Dans l’exemple ci-après, la synchronisation ne s’applique pas à l’objet, car le domaine auquel appartient ce dernier a été exclu de la synchronisation.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Domaine configuré pour la synchronisation, mais dépourvu de profils d’exécution/étapes d’exécution
L’objet n’entre pas dans le champ d’application de la synchronisation, car le domaine ne présente pas de profils d’exécution/étapes d’exécution. Dans l’exemple ci-après, la synchronisation ne s’applique pas à l’objet, car le domaine auquel appartient ce dernier est dépourvu d’étapes d’exécution pour le profil d’exécution d’importation intégrale.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch6.png)

### <a name="object-is-filtered-due-to-ou-filtering"></a>Objet exclu en raison d’un filtrage d’unité d’organisation
L’objet n’entre pas dans le champ d’application de la synchronisation à cause de la configuration du filtrage d’unité d’organisation. Dans l’exemple ci-après, l’objet appartient à OU=NoSync,DC=bvtadwbackdc,DC=com.  Cette unité d’organisation n’entre pas dans le champ d’application de la synchronisation.
![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch7.png)

## <a name="html-report"></a>Rapport HTML
Outre l’analyse de l’objet, la tâche de résolution des problèmes génère un rapport HTML incluant toutes les connaissances concernant l’objet. Ce rapport HTML peut être partagé avec l’équipe du support technique à des fins de résolution des problèmes approfondie s’il y a lieu.

![](media\active-directory-aadconnect-troubleshoot-objectsynch\objsynch8.png)

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
