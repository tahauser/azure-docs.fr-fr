---
title: "Azure AD Connect : Authentification unique transparente - Démarrage rapide | Microsoft Docs"
description: "Cet article explique comment bien démarrer avec l’authentification unique transparente d’Azure Active Directory"
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
ms.date: 02/23/2017
ms.author: billmath
ms.openlocfilehash: 58ca992f9fcf9a03d917f0dc250a292c4d5f49e5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Authentification unique transparente Azure Active Directory - Démarrage rapide

## <a name="deploy-seamless-single-sign-on"></a>Déploiement de l'authentification unique transparente

L’authentification unique transparente (Seamless SSO) Azure Active Directory (Azure AD) connecte automatiquement les utilisateurs lorsque leurs ordinateurs d’entreprise sont connectés au réseau de l’entreprise. Seamless SSO offre à vos utilisateurs un accès facilité à vos applications dans le cloud sans nécessiter de composants sur site supplémentaires.

Pour déployer l’authentification unique transparente, procédez comme suit.

## <a name="step-1-check-the-prerequisites"></a>Étape 1 : Vérifier les prérequis

Vérifiez que les prérequis suivants sont remplis :

* **Configurez votre serveur Azure AD Connect** : si vous utilisez [l’authentification directe](active-directory-aadconnect-pass-through-authentication.md) comme méthode de connexion utilisateur, aucune vérification de prérequis n’est nécessaire. Si vous utilisez la [synchronisation de hachage de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) comme méthode de connexion, et s’il existe un pare-feu entre Azure AD Connect et Azure AD, vérifiez les points suivants :
   - Vous utilisez Azure AD Connect 1.1.644.0 ou version ultérieure. 
   - Si votre pare-feu ou votre proxy permettent la mise en liste verte DNS, mettez dans la liste verte les connexions aux URL **\*.msappproxy.net** via le port 443. Dans le cas contraire, autorisez l’accès aux [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/details.aspx?id=41653), qui sont mises à jour chaque semaine. Cette condition préalable est applicable uniquement lorsque vous activez la fonctionnalité. Elle n'est pas requise pour les connexions d'utilisateur réelles.

    >[!NOTE]
    >Les versions 1.1.557.0, 1.1.558.0, 1.1.561.0 et 1.1.614.0 d’Azure AD Connect comportent un problème lié à la synchronisation de hachage de mot de passe. Si vous _ne prévoyez pas_ d’utiliser la synchronisation de hachage de mot de passe en même temps que l’authentification directe, lisez les [Notes de publication Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) pour en savoir plus.

* **Définir les informations d'identification de l'administrateur de domaine** : Vous devez disposer des informations d'identification de l'administrateur de domaine pour chaque forêt Active Directory, dans les scénarios suivants :
    * Synchronisation avec Azure AD via Azure AD Connect.
    * La forêt contient des utilisateurs pour lesquels vous souhaitez activer Seamless SSO.

## <a name="step-2-enable-the-feature"></a>Étape 2 : Activer la fonctionnalité

Activez Seamless SSO via [Azure AD Connect](active-directory-aadconnect.md).

Si vous procédez à une nouvelle installation d’Azure AD Connect, choisissez le [chemin d’installation personnalisé](active-directory-aadconnect-get-started-custom.md). Sur la page **Connexion utilisateur**, cochez la case **Activer l’authentification unique**.

![Azure AD Connect : connexion utilisateur](./media/active-directory-aadconnect-sso/sso8.png)

Si vous disposez déjà d’une installation Azure AD Connect, sélectionnez la page **Modifier la connexion utilisateur** dans Azure AD Connect, puis cliquez sur **Suivant**.

![Azure AD Connect : Modifier la connexion utilisateur](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Suivez les instructions de l’Assistant jusqu’à ce que vous accédiez à la page **Activer l’authentification unique**. Fournissez les informations d'identification de l'administrateur de domaine pour chaque forêt Active Directory, dans les scénarios suivants :
    * Synchronisation avec Azure AD via Azure AD Connect.
    * La forêt contient des utilisateurs pour lesquels vous souhaitez activer Seamless SSO.

À la fin de l’Assistant, l’authentification unique transparente est activée sur votre locataire.

>[!NOTE]
> Les informations d’identification de l'administrateur de domaine ne sont pas stockées dans Azure AD Connect ni dans Azure AD. Elles sont uniquement utilisées pour activer la fonctionnalité.

Suivez ces instructions pour vérifier que vous avez activé l’authentification unique transparente correctement :

1. Connectez-vous au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) à l’aide des informations d’identification d’administrateur général de votre locataire.
2. Sélectionnez **Active Directory** dans le volet de gauche.
3. Sélectionnez ensuite **Azure AD Connect**.
4. Vérifiez que la fonctionnalité **Authentification unique transparente** est **activée**.

![Portail Azure : volet Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Étape 3 : Déployer la fonctionnalité

Pour déployer la fonctionnalité sur les systèmes de vos utilisateurs, ajoutez l’URL Azure AD ci-après aux paramètres de zone intranet des utilisateurs en utilisant la stratégie de groupe dans Active Directory :

- https://autologon.microsoftazuread-sso.com


En outre, vous devez activer un paramètre de stratégie Zone intranet appelé **Autoriser les mises à jour de la barre d’état via le script** via la stratégie de groupe. 

>[!NOTE]
> Les instructions suivantes ne valent que pour les navigateurs Internet Explorer et Google Chrome sur Windows (si ce dernier partage l’ensemble des URL de sites de confiance avec Internet Explorer). Lisez la section suivante pour savoir comment configurer Mozilla Firefox et Google Chrome sur Mac.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Pourquoi devez-vous modifier les paramètres de la zone Intranet des utilisateurs ?

Par défaut, le navigateur calcule automatiquement la zone appropriée, Internet ou Intranet, à partir d’une URL spécifique. Par exemple, « http://contoso/ » est mappée à la zone Intranet, tandis que « http://intranet.contoso.com/ » est mappée à la zone Internet (car l’URL contient un point). Les navigateurs n’enverront pas de tickets Kerberos à un point de terminaison cloud, comme l’URL Azure AD, sauf si vous ajoutez explicitement l’URL à la zone intranet du navigateur.

### <a name="detailed-steps"></a>Procédure détaillée

1. Ouvrez l’outil Éditeur de gestion des stratégies de groupe.
2. Modifiez la stratégie de groupe qui est appliquée à certains ou à l’ensemble de vos utilisateurs. Cette exemple utilise la **stratégie de domaine par défaut**.
3. Accédez à **Configuration utilisateur** > **Modèles d'administration** > **Composants Windows** > **Internet Explorer** > **Panneau de configuration Internet** > **Page Sécurité**. Puis sélectionnez **Liste des attributions de sites aux zones**.
    ![Authentification unique](./media/active-directory-aadconnect-sso/sso6.png)
4. Activez la stratégie, puis entrez les valeurs suivantes dans la boîte de dialogue :
   - **Nom de la valeur** : URL Azure AD vers laquelle les tickets Kerberos sont transférés.
   - **Valeur** (données) : **1** indique la zone Intranet.

    Le résultat ressemble à :

    Valeur : https://autologon.microsoftazuread-sso.com
  
    Data 1

   >[!NOTE]
   > Si vous souhaitez interdire à certains utilisateurs l’utilisation de l’authentification unique transparente (par exemple, si ces utilisateurs se connectent sur des bornes partagées), définissez les valeurs précédentes sur **4**. Cette opération ajoute l’URL Azure AD à la zone Sites sensibles et rend l’authentification unique transparente inutilisable en permanence.
   >

5. Sélectionnez **OK**, puis de nouveau **OK**.

    ![Authentification unique](./media/active-directory-aadconnect-sso/sso7.png)

6. Accédez à **Configuration utilisateur** > **Modèles d’administration** > **Composants Windows** > **Internet Explorer** > **Panneau de configuration Internet** > **Page Sécurité** > **Zone intranet**. Puis sélectionnez **Autoriser les mises à jour de la barre d’état via le script**.

    ![Authentification unique](./media/active-directory-aadconnect-sso/sso11.png)

7. Activez le paramètre de stratégie, puis sélectionnez **OK**.

    ![Authentification unique](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Considérations sur le navigateur

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (toutes les plateformes)

Mozilla Firefox n'utilise pas automatiquement l’authentification Kerberos. Chaque utilisateur doit ajouter manuellement l’URL Azure AD à ses paramètres Firefox comme suit :
1. Exécutez Firefox et entrez `about:config` dans la barre d’adresses. Ignorez les notifications que vous voyez.
2. Recherchez la préférence **network.negotiate-auth.trusted-URI**. Cette préférence répertorie les sites de confiance de Firefox pour l’authentification Kerberos.
3. Cliquez avec le bouton droit et sélectionnez **Modifier**.
4. Saisissez https://autologon.microsoftazuread-sso.com dans le champ.
5. Sélectionnez **OK** puis rouvrez le navigateur.

#### <a name="safari-mac-os"></a>Safari (Mac OS)

Assurez-vous que l’ordinateur utilisant Mac OS est connecté à Azure AD. Pour obtenir des instructions sur la façon de rejoindre Azure AD, voir [Meilleures pratiques pour intégrer OS X dans Active Directory](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (toutes les plateformes)

Si vous avez remplacé les paramètres de stratégie [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) ou [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) dans votre environnement, veillez à y ajouter également l’URL d’Azure AD (https://autologon.microsoftazuread-sso.com).

#### <a name="google-chrome-mac-os-only"></a>Google Chrome (Mac OS uniquement)

En ce qui concerne Google Chrome sur Mac OS et sur les autres plateformes non-Windows, consultez la [liste de stratégies du site Chromium Projects](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) afin d’obtenir plus d’informations sur la procédure d’ajout à la liste verte de l’URL Azure AD pour l’authentification intégrée.

L’utilisation des extensions de stratégie de groupe Active Directory tierces afin de déployer l’URL Azure AD pour les utilisateurs de Firefox et de Google Chrome sur Mac n’est pas couverte par cet article.

#### <a name="known-browser-limitations"></a>Limitations connues du navigateur

L’authentification unique transparente ne fonctionne pas en mode de navigation privée sur Firefox et Edge. Par ailleurs, il ne fonctionne pas sur Internet Explorer si le navigateur en cours d’utilisation est en mode Protection améliorée.

## <a name="step-4-test-the-feature"></a>Étape 4 : Tester la fonctionnalité

Pour tester la fonctionnalité d’un utilisateur spécifique, assurez-vous que toutes les conditions suivantes sont en place :
  - L’utilisateur se connecte à un appareil d’entreprise.
  - L'appareil est joint à votre domaine Active Directory.
  - L’appareil dispose d’une connexion directe à votre contrôleur de domaine, soit sur le réseau câblé ou sans fil de l’entreprise, soit par le biais d’une connexion d’accès à distance, comme une connexion VPN.
  - Vous avez [déployé la fonctionnalité](##step-3-roll-out-the-feature) pour cet utilisateur via la stratégie de groupe.

Pour tester le scénario dans lequel l’utilisateur entre uniquement le nom d’utilisateur, mais pas le mot de passe :
   - Connectez-vous à https://myapps.microsoft.com/ dans une nouvelle session de navigation privée.

Pour tester le scénario dans lequel l’utilisateur n’a pas à entrer le nom d’utilisateur ou le mot de passe, effectuez l'une des étapes suivantes : 
   - Connectez-vous à https://myapps.microsoft.com/contoso.onmicrosoft.com dans une nouvelle session de navigation privée. Remplacez *contoso* par le nom de votre locataire.
   - Connectez-vous à https://myapps.microsoft.com/contoso.com dans une nouvelle session de navigation privée. Remplacez *contoso.com* par un domaine vérifié (pas un domaine fédéré) dans votre locataire.

## <a name="step-5-roll-over-keys"></a>Étape 5 : substituer les clés

À l’étape 2, Azure AD Connect crée des comptes d’ordinateurs (représentant Azure AD) dans toutes les forêts Azure Directory sur lesquelles vous avez activé l’authentification unique transparente. Pour en savoir plus, consultez [Authentification unique transparente Azure Active Directory : immersion technique](active-directory-aadconnect-sso-how-it-works.md). Pour améliorer la sécurité, nous vous recommandons de substituer régulièrement les clés de déchiffrement Kerberos de ces comptes d’ordinateur. Pour obtenir des instructions sur la substitution des clés, voir [Authentification unique transparente Azure Active Directory : questions fréquentes](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Vous n’avez pas besoin d’effectuer cette étape _immédiatement_ après avoir activé la fonctionnalité. Substituez les clés de déchiffrement Kerberos au moins une fois tous les 30 jours.

## <a name="next-steps"></a>étapes suivantes

- [Présentation technique approfondie](active-directory-aadconnect-sso-how-it-works.md) : découvrez comment fonctionne la fonctionnalité Authentification unique transparente.
- [Forum aux questions](active-directory-aadconnect-sso-faq.md) : obtenez des réponses aux questions fréquentes sur l’authentification unique transparente.
- [Résoudre les problèmes](active-directory-aadconnect-troubleshoot-sso.md) : découvrez comment résoudre les problèmes courants liés à la fonctionnalité Authentification unique transparente.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : utilisez le Forum Azure Active Directory pour consigner de nouvelles demandes de fonctionnalités.
