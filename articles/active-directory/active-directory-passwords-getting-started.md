---
title: "Démarrage rapide de réinitialisation de mot de passe libre-service - Azure Active Directory"
description: "Déployer rapidement la réinitialisation de mot de passe en libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 34a6722fdd06cf0b198320e551daccc21956dc69
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Déploiement rapide de la réinitialisation de mot de passe en libre-service Azure AD

> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, consultez [J’ai oublié mon mot de passe Azure AD](active-directory-passwords-update-your-own-password.md).

Grâce à la réinitialisation de mot de passe en libre-service, les administrateurs informatiques disposent d’un moyen simple pour permettre aux utilisateurs de réinitialiser ou de déverrouiller leurs comptes ou leurs mots de passe. Le système inclut des rapports détaillés de suivi d’accès au système, ainsi que des notifications pour vous prévenir de toute utilisation malveillante ou de tout abus.

Ce guide part du principe que vous avez déjà une version d’essai opérationnelle ou une licence de locataire Azure Active Directory (Azure AD). Si vous avez besoin d’aide pour configurer Azure AD, consultez [Prise en main d’Azure AD](get-started-azure-ad.md).

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Activer la réinitialisation de mot de passe en libre-service (SSPR) pour votre locataire Azure AD

1. À partir de votre locataire Azure AD existant, sur le **Portail Azure** sous **Azure Active Directory** sélectionnez **Réinitialisation du mot de passe**.

2. Sur la page **Propriétés**, sous **Réinitialisation de mot de passe en libre-service activée**, choisissez l’une des options suivantes :
   * **Aucun**: personne n’est en mesure d’utiliser la fonctionnalité de réinitialisation de mot de passe en libre-service.
   * **Sélectionné** : seuls les membres d’un groupe Azure AD que vous choisissez peuvent utiliser la fonctionnalité de réinitialisation de mot de passe en libre-service. Nous vous recommandons de définir un groupe d’utilisateurs et d’utiliser ce paramètre lorsque vous déployez cette fonctionnalité pour une preuve de concept.
   * **Tout le monde** : tous les utilisateurs disposant d’un compte dans votre locataire Azure AD peuvent utiliser la fonctionnalité de réinitialisation de mot de passe en libre-service. Nous vous recommandons d’utiliser ce paramètre lorsque vous êtes prêt à déployer cette fonctionnalité pour l’ensemble de votre locataire après avoir effectué une preuve de concept.

   > [!IMPORTANT]
   > Les comptes administrateur Azure auront toujours la possibilité de réinitialiser leurs mots de passe, quel que soit le réglage de cette option. 

3. À partir de la page **Méthodes d’authentification**, choisissez les paramètres suivants :
   * **Nombre de méthodes requis pour la réinitialisation** : nous prenons en charge une méthode minimum ou deux méthodes maximum.
   * **Méthodes disponibles pour les utilisateurs** : nous avons besoin d’au moins une méthode, mais il peut être judicieux de disposer d’une méthode supplémentaire.
      * **Messagerie** : envoie un message électronique avec un code à l’adresse de messagerie d’authentification configurée pour l’utilisateur.
      * **Téléphone mobile** : offre à l’utilisateur la possibilité de recevoir un appel ou un SMS contenant un code sur son numéro de téléphone mobile configuré.
      * **Téléphone de bureau** : appelle l’utilisateur avec un code sur son numéro de téléphone configuré.
      * **Questions de sécurité** : nécessite que vous choisissiez les paramètres suivants :
         * **Nombre de questions requises pour l’inscription**: la valeur minimale pour une inscription réussie. Un utilisateur peut choisir de répondre à davantage de questions afin de créer un pool de questions à partir desquelles extraire du contenu. Ce nombre doit comprendre entre trois et cinq questions, et il doit être supérieur ou égal au nombre de questions requises pour réinitialiser le passe de l’utilisateur. L’utilisateur peut ajouter des questions personnalisées en sélectionnant le bouton **Personnalisée** lorsqu’il sélectionne ses questions de sécurité.
         * **Nombre de questions requises pour la réinitialisation** : peut indiquer de trois à cinq questions auxquelles il faut répondre correctement avant que vous n’autorisiez la réinitialisation et le déverrouillage des mots de passe utilisateur.
            
    ![Authentification][Authentication]

4. Recommandé : sous **Personnalisation**, vous pouvez modifier le lien **Contactez votre administrateur** afin qu’il redirige l’utilisateur vers une page ou une adresse de messagerie que vous avez définie. Nous vous recommandons de définir ce lien à une valeur telle qu’une adresse de messagerie ou un site Web que vos utilisateurs utilisent déjà pour des questions de support.

5. Facultatif : la page **Inscription** permet aux administrateurs :
   * D’obliger les utilisateurs à s’inscrire durant la connexion.
   * De définir le nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification.

6. Facultatif : la page **Notifications** permet aux administrateurs :
   * De notifier les utilisateurs lors des réinitialisations de mot de passe.
   * De notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe.

À ce stade, vous avez configuré la réinitialisation de mot de passe en libre-service pour votre locataire Azure AD. Vos utilisateurs peuvent désormais utiliser les instructions figurant dans les articles [S’inscrire à la réinitialisation de mot de passe en libre-service](active-directory-passwords-reset-register.md) et [Réinitialiser ou modifier votre mot de passe](active-directory-passwords-update-your-own-password.md) pour mettre à jour leur mot de passe sans intervention de l’administrateur. Vous pouvez vous arrêter ici si vous êtes un client cloud uniquement. Ou vous pouvez passer à la section suivante pour configurer la synchronisation des mots de passe sur un domaine Active Directory local.

> [!TIP]
> Testez la réinitialisation de mot de passe en libre-service avec un utilisateur plutôt qu’un administrateur, car Microsoft applique des spécifications d’authentification forte pour les comptes d’administrateur Azure. Pour plus d’informations sur la stratégie de mot de passe administrateur, consultez notre article [Stratégie de mot de passe](active-directory-passwords-policy.md#administrator-password-policy-differences).

## <a name="configure-synchronization-to-an-existing-identity-source"></a>Configurer la synchronisation avec une source d’identité existante

Pour activer la synchronisation d’identité locale avec Azure AD, vous devez installer et configurer [Azure AD Connect](./connect/active-directory-aadconnect.md) sur un serveur de votre organisation. Cette application gère la synchronisation des utilisateurs et des groupes à partir de votre source d’identité existante avec votre locataire Azure AD. Pour plus d'informations, consultez les pages suivantes :

* [Mise à niveau à partir de DirSync ou d’Azure AD Sync vers Azure AD Connect](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [Prise en main d’Azure AD Connect à l’aide de paramètres express](./connect/active-directory-aadconnect-get-started-express.md)
* [Configurez l’écriture différée du mot de passe](active-directory-passwords-writeback.md#configure-password-writeback) pour écrire des mots de passe en différé à partir d’Azure AD dans votre répertoire local

### <a name="on-premises-policy-change"></a>Modification de la stratégie locale

Si vous synchronisez des utilisateurs à partir d’un domaine Active Directory local et que vous souhaitez autoriser les utilisateurs à réinitialiser leurs mots de passe immédiatement, apportez la modification suivante à votre stratégie de mot de passe locale :

1. Accédez à **Configuration de l’ordinateur** > **Stratégies** > **Paramètres Windows** > **Paramètres de sécurité** > **Stratégies de compte** > **Stratégie de mot de passe**.

2. Définissez la **durée de validité minimale du mot de passe** sur **0 jour**.

Ce paramètre de sécurité détermine la période de temps, en jours, pendant laquelle un utilisateur doit utiliser un mot de passe avant de pouvoir le changer. Si vous définissez le paramètre d’utilisation minimale sur **0 jour**, les utilisateurs peuvent utiliser la réinitialisation de mot de passe en libre-service si leurs mots de passe sont modifiés par leurs équipes de support technique.

![Stratégie][Policy]

## <a name="disable-self-service-password-reset"></a>Désactiver la réinitialisation du mot de passe libre-service

Il est facile de désactiver la réinitialisation du mot de passe libre-service. Ouvrez votre locataire Azure AD et accédez à **Réinitialisation de mot de passe** > **Propriétés**, puis sélectionnez **Aucun** sous **Réinitialisation de mot de passe en libre-service activée**.

### <a name="learn-more"></a>En savoir plus
Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD :

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-best-practices.md)
* [Réinitialiser ou modifier votre mot de passe](active-directory-passwords-update-your-own-password.md)
* [S’inscrire pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md)
* [Vous avez une question relative à la licence ?](active-directory-passwords-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](active-directory-passwords-data.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](active-directory-passwords-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)

## <a name="next-steps"></a>Étapes suivantes

Avec ce guide de démarrage rapide, vous avez appris à configurer la réinitialisation de mot de passe en libre-service pour vos utilisateurs. Pour effectuer ces étapes, accédez au portail Azure :

> [!div class="nextstepaction"]
> [Activer la réinitialisation du mot de passe libre-service](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Méthodes d’authentification disponibles dans Azure Active Directory et quantité requise"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "Stratégie de groupe de mot de passe locale définie sur 0 jour"

