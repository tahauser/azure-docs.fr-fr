---
title: "Personnalisation de la réinitialisation de mot de passe libre-service - Azure Active Directory"
description: "Options de personnalisation de la réinitialisation du mot de passe libre-service Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 526286c7f6b62d165af43487ca63fe9055623d0c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Personnaliser les fonctionnalités d’Azure AD pour la réinitialisation du mot passe libre-service

Les professionnels de l’informatique qui souhaitent déployer la réinitialisation du mot de passe libre-service dans Azure Active directory (Azure AD) peuvent personnaliser l’expérience en fonction des besoins de leurs utilisateurs.

## <a name="customize-the-contact-your-administrator-link"></a>Personnaliser le lien « Contactez votre administrateur »

Même si la réinitialisation n’est pas activée, les utilisateurs disposent toujours d’un lien « Contactez votre administrateur » dans le portail de réinitialisation du mot de passe. Si l’utilisateur sélectionne ce lien, l’une des actions ci-dessous se produit :
   * Un e-mail est envoyé à vos administrateurs pour demander une assistance lors de la modification du mot de passe de l’utilisateur. 
   * Les utilisateurs sont dirigés vers une URL que vous spécifiez pour l’assistance. 

Nous vous recommandons de définir comme contact une adresse e-mail ou un site web que vos utilisateurs utilisent déjà pour des questions de support.

![Contact][Contact]

L’e-mail de contact est envoyé aux destinataires suivants dans cet ordre :

1. Si le rôle **Administrateur de mot de passe** est affecté, les administrateurs détenteurs de ce rôle sont avertis.
2. Si aucun administrateur de mot de passe n’est affecté, les administrateurs disposant du rôle **Administrateur d’utilisateurs** sont avertis.
3. Si aucun des rôles précédents n’a été affecté, les **Administrateurs globaux** sont avertis.

Dans tous les cas, un maximum de 100 destinataires sont informés.

Pour en savoir plus sur les différents rôles d’administrateur et sur la façon de les affecter, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Désactiver les e-mails « Contactez votre administrateur »

Si votre organisation ne souhaite pas avertir les administrateurs au sujet des demandes de réinitialisation de mot de passe, vous pouvez activer la configuration suivante :

* Activez la réinitialisation du mot de passe en libre-service pour tous les utilisateurs finaux. Cette option se trouve sous **Réinitialisation de mot de passe** > **Propriétés**.
  
  Si vous ne voulez pas que les utilisateurs réinitialisent leurs propres mots de passe, vous pouvez étendre l’accès à un groupe vide. *Nous ne recommandons pas cette option.*
* Personnalisez le lien du support technique pour fournir une URL web ou un lien mailto: que les utilisateurs peuvent utiliser pour obtenir une assistance. Cette option se trouve sous **Réinitialisation de mot de passe** > **Personnalisation** > **Adresse e-mail ou URL du support technique**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Personnaliser la page de connexion AD FS pour la réinitialisation du mot de passe libre-service

Les administrateurs des services de fédération Active Directory (AD FS) peuvent ajouter un lien à leur page de connexion à l’aide des instructions figurant dans l’article [Ajouter la description de la page de connexion](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Pour ajouter un lien vers la page de connexion AD FS, exécutez la commande suivante sur votre serveur AD FS. Les utilisateurs peuvent utiliser cette page pour accéder au flux de travail de réinitialisation du mot de passe libre-service.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Personnaliser l’apparence du panneau d’accès et de la page de connexion

Vous pouvez personnaliser la page de connexion. Vous pouvez ajouter un logo qui apparaît à côté de l’image qui correspond à la marque de votre société.

Les graphiques que vous choisissez s’affichent dans les circonstances suivantes :

* Quand un utilisateur entre son nom d’utilisateur
* Si l’utilisateur accède à l’URL personnalisée :
    * En passant le paramètre *whr* à la page de réinitialisation du mot de passe, par exemple « https://login.microsoftonline.com/?whr=contoso.com »
    * En passant le paramètre *username* à la page de réinitialisation du mot de passe, par exemple « https://login.microsoftonline.com/?username= admin@contoso.com »

Retrouvez plus d’informations sur la configuration de la marque de société dans l’article [Ajouter une marque de société à votre page de connexion dans Azure AD](customize-branding.md).

### <a name="directory-name"></a>Nom de l’annuaire

Vous pouvez changer l’attribut de nom de l’annuaire sous **Azure Active Directory** > **Propriétés**. Vous pouvez afficher un nom d’organisation convivial visible dans le portail et dans les communications automatisées. Cette option est essentiellement visible dans les e-mails automatiques dans les formes qui suivent :

* Le nom convivial dans l’e-mail, par exemple « Microsoft pour le compte de la démonstration CONTOSO »
* La ligne d’objet dans l’e-mail, par exemple « Code de vérification d’e-mail pour le compte de démonstration CONTOSO »

## <a name="next-steps"></a>Étapes suivantes

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

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "Si nécessaire, contactez votre administrateur pour réinitialiser votre exemple d’e-mail de mot le passe"
