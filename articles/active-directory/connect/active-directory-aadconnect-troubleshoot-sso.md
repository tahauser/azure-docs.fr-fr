---
title: "Azure Active Directory Connect : Résoudre les problèmes d’authentification unique transparente | Microsoft Docs"
description: "Cette rubrique explique comment résoudre les problèmes relatifs à l’authentification unique transparente Azure Active Directory"
services: active-directory
keywords: "Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: d5f47bd780de692a5e641fc49ea0c433809068bc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Résoudre les problèmes d’authentification unique transparente Azure Active Directory

Cet article fournit des informations sur les problèmes courants liés à l’authentification unique transparente (Seamless SSO) Azure Active Directory (Azure AD).

## <a name="known-problems"></a>Problèmes connus

- Dans certains cas, l’activation de l’authentification unique transparente peut prendre jusqu’à 30 minutes.
- La prise en charge du navigateur Edge n’est pas disponible.
- Le démarrage des clients Office, en particulier dans les scénarios d’ordinateurs partagés, entraîne l’affichage d’invites de connexion supplémentaires. Les utilisateurs doivent entrer leur nom d'utilisateur fréquemment, mais pas leur mot de passe.
- Si l’authentification unique transparente réussit, l’utilisateur n’a pas la possibilité de choisir l’option **Maintenir la connexion**. En raison de ce comportement, les scénarios de mappage SharePoint et OneDrive ne fonctionnent pas.
- L’authentification unique transparente ne fonctionne pas en mode Navigation privée sur Firefox.
- L’authentification unique transparente ne fonctionne pas sur Internet Explorer quand le mode protégé amélioré est activé.
- L’authentification unique transparente ne fonctionne pas sur les navigateurs mobiles iOS et Android.
- Si vous synchronisez 30 forêts Active Directory ou plus, vous ne pouvez pas activer l’authentification unique transparente via Azure AD Connect. En guise de solution de contournement, vous pouvez [activer manuellement](#manual-reset-of-azure-ad-seamless-sso) la fonctionnalité pour votre locataire.
- L’ajout des URL du service Azure AD (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) à la zone Sites de confiance plutôt qu’à la zone Intranet local, *empêche les utilisateurs de se connecter*.

## <a name="check-the-status-of-the-feature"></a>Vérification de l’état de la fonctionnalité

Assurez-vous que la fonctionnalité Authentification unique transparente est toujours **activée** sur votre client. Vous pouvez vérifier l’état en accédant au volet **Azure AD Connect** dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/).

![Centre d’administration Azure Active Directory : volet Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Raisons des échecs de connexion dans le Centre d’administration Azure Active Directory (licence Premium requise)

Si votre locataire dispose d’une licence Azure AD Premium, vous pouvez également consulter le [rapport d’activité de connexion](../active-directory-reporting-activity-sign-ins.md) dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/).

![Centre d’administration Azure Active Directory : rapport de connexions](./media/active-directory-aadconnect-sso/sso9.png)

Accédez à **Azure Active Directory** > **Connexions** dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/), puis sélectionnez une activité de connexion. Recherchez le champ **Code d’erreur de connexion**. Notez la valeur de ce champ et cherchez dans le tableau suivant la raison de l’échec et la solution à appliquer :

|Code d’erreur de connexion|Raison de l’échec de connexion|Résolution :
| --- | --- | ---
| 81001 | Le ticket Kerberos de l’utilisateur est trop volumineux. | Réduisez les appartenances à des groupes de l’utilisateur, puis réessayez.
| 81002 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81003 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81004 | Échec de la tentative d’authentification Kerberos. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81008 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81009 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81010 | L’authentification unique transparente a échoué, car le ticket Kerberos de l’utilisateur est arrivé à expiration ou n’est pas valide. | L’utilisateur doit se connecter à partir d’un appareil joint à un domaine au sein de votre réseau d’entreprise.
| 81011 | Impossible de trouver l’objet utilisateur à partir des informations contenues dans le ticket Kerberos de l’utilisateur. | Utilisez Azure AD Connect pour synchroniser les informations de l’utilisateur dans Azure AD.
| 81012 | L’utilisateur qui tente de se connecter à Azure AD est différent de l’utilisateur connecté à l’appareil. | L'utilisateur doit se connecter à partir d’un autre appareil.
| 81013 | Impossible de trouver l’objet utilisateur à partir des informations contenues dans le ticket Kerberos de l’utilisateur. |Utilisez Azure AD Connect pour synchroniser les informations de l’utilisateur dans Azure AD. 

## <a name="troubleshooting-checklist"></a>Liste de contrôle pour la résolution des problèmes

Utilisez la liste de contrôle suivante pour résoudre les problèmes d’authentification unique transparente :

- Vérifiez que la fonctionnalité d’authentification unique transparente est activée dans Azure AD Connect. Si vous ne pouvez pas activer la fonctionnalité (par exemple, en raison d’un port bloqué), vérifiez que toutes les [conditions préalables](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites) sont bien respectées.
- Vérifiez que ces deux URL Azure AD (https://autologon.microsoftazuread-sso.com et https://aadg.windows.net.nsatc.net) figurent bien dans les paramètres Zone intranet de l’utilisateur.
- Vérifiez que l’appareil d’entreprise est joint au domaine Active Directory.
- Assurez-vous que l'utilisateur est connecté à l'appareil via un compte de domaine Active Directory.
- Vérifiez que le compte de l’utilisateur provient d’une forêt Active Directory dans laquelle l’authentification unique (SSO) transparente a été configurée.
- Vérifiez que l’appareil est connecté au réseau d’entreprise.
- Vérifiez que l’heure de l’appareil est synchronisée à la fois avec celle d’Active Directory et celle du contrôleur de domaine, et qu'elles ne comptent pas plus de cinq minutes d’écart.
- Affichez la liste des tickets Kerberos existants sur l’appareil à l’aide de la commande `klist` dans une invite de commandes. Vérifiez que les tickets émis pour le compte d’ordinateur `AZUREADSSOACCT` y figurent. En règle générale, la durée de validité des tickets Kerberos des utilisateurs est de 12 heures. L'instance Active Directory est peut-être paramétrée différemment.
- Videz les tickets Kerberos existants de l’appareil à l’aide de la commande `klist purge`, puis réessayez.
- Pour déterminer si des problèmes liés à JavaScript existent, passez en revue les journaux de console du navigateur (sous **Outils de développement**).
- Examinez les [journaux des contrôleurs de domaine](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Journaux des contrôleurs de domaine

Si vous activez l’audit des réussites sur votre contrôleur de domaine, chaque fois qu’un utilisateur se connecte à l’aide de l’authentification unique transparente, une entrée de sécurité est enregistrée dans le journal des événements. Vous trouverez ces événements de sécurité au moyen de la requête suivante. (Recherchez l'événement **4769** associé au compte d'ordinateur **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Réinitialisation manuelle de la fonctionnalité

Si vous n’avez pas réussi à résoudre le problème, vous pouvez réinitialiser manuellement la fonctionnalité pour votre locataire. Procédez comme suit sur le serveur sur site où vous exécutez Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Étape 1 : Importer le module PowerShell Authentification unique (SSO) transparente

1. Téléchargez et installez l’[Assistant de connexion Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Téléchargez et installez le [Module Azure Active Directory 64 bits pour Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Étape 2 : Obtenir la liste des forêts Azure Directory dans lesquelles l’authentification unique (SSO) transparente a été activée

1. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Lorsque vous y êtes invité, fournissez les informations d’identification de l’administrateur général de votre locataire.
2. Appelez `Get-AzureADSSOStatus`. Cette commande vous fournit la liste des forêts Azure Directory (examinez la liste « Domaines ») dans lesquelles cette fonctionnalité a été activée.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Étape 3 : Désactiver l'authentification unique (Seamless SSO) pour chaque forêt Azure Directory dans laquelle vous avez configuré la fonctionnalité

1. Appelez `$creds = Get-Credential`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt Azure Directory souhaitée.
2. Appelez `Disable-AzureADSSOForest -OnPremCredentials $creds`. Cette commande supprime le compte d’ordinateur `AZUREADSSOACCT` du contrôleur de domaine sur site pour cette forêt Azure Directory spécifique.
3. Répétez les étapes précédentes pour chaque forêt Azure Directory dans laquelle vous avez configuré la fonctionnalité.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Étape 4 : Activer l’authentification unique (SSO) transparente pour chaque forêt Azure Directory

1. Appelez `Enable-AzureADSSOForest`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt Azure Directory souhaitée.
2. Répétez l'étape précédente pour chaque forêt Azure Directory dans laquelle vous souhaitez configurer la fonctionnalité.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Étape 5. Activer la fonctionnalité pour votre locataire

Pour activer la fonctionnalité sur votre locataire, appelez `Enable-AzureADSSO`, puis tapez **true** à l'invite `Enable:`.
