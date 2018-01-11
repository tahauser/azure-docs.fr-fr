---
title: "Microsoft Azure Multi-Factor Authentication - État utilisateur"
description: "En savoir plus sur l’état utilisateur dans Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: b7fb5135ac1ae776851d79f936eb860b4b121c71
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Comment exiger la vérification en deux étapes pour un utilisateur ou un groupe

Il existe deux approches pour exiger une vérification en deux étapes. La première option consiste à activer l’authentification Azure Multi-Factor Authentication (MFA) pour chaque utilisateur. En activant chaque utilisateur individuellement, celui-ci effectue toujours la vérification en deux étapes (à quelques exceptions près, comme lorsqu’ils se connectent à partir d’adresses IP approuvées ou si la fonctionnalité de mémorisation des appareils est activée). La seconde option consiste à définir une stratégie d’accès conditionnel qui requiert une vérification en deux étapes sous certaines conditions.

>[!TIP] 
>Choisissez une de ces deux méthodes pour exiger la vérification en deux étapes, mais pas les deux. L’activation d’un utilisateur pour l’authentification multifacteur Azure remplace toutes les stratégies d’accès conditionnel.

## <a name="which-option-is-right-for-you"></a>Choisir l’option qui vous convient

**L’activation de l’authentification multifacteur Azure en modifiant les états des utilisateurs** est l’approche traditionnelle pour exiger une vérification en deux étapes. Elle fonctionne à la fois pour l’authentification Azure MFA dans le cloud et le serveur Azure MFA. Tous les utilisateurs que vous activez bénéficient de la même expérience, qui consiste à lancer la vérification en deux étapes chaque fois qu’ils se connectent. L’activation d’un utilisateur remplace toutes les stratégies d’accès conditionnel appliquées à cet utilisateur. 

**L’activation de l’authentification multifacteur Azure avec une stratégie d’accès conditionnel** est une approche plus souple pour exiger une vérification en deux étapes. Cependant, elle fonctionne uniquement pour l’authentification multifacteur Azure dans le cloud, et l’accès conditionnel est une [fonctionnalité payante d’Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Vous pouvez créer des stratégies d’accès conditionnel qui s’appliquent à des groupes et à des utilisateurs individuels. Vous pouvez appliquer davantage de restrictions aux groupes à haut risque par rapport aux groupes à faible risque, ou exiger la vérification en deux étapes uniquement pour les applications cloud à haut risque tout en ignorant celles à faible risque. 

Ces deux options invitent les utilisateurs à s’inscrire à l’authentification multifacteur Azure la première fois qu’ils se connectent après l’activation de cette fonctionnalité. Elles fonctionnent également avec les paramètres configurables [Azure multi-Factor Authentication](multi-factor-authentication-whats-next.md)

## <a name="enable-azure-mfa-by-changing-user-status"></a>Activer Azure MFA en modifiant l’état de l’utilisateur

Les comptes d'utilisateur dans Azure Multi-Factor Authentication peuvent présenter les trois états suivants :

| État | Description | Applications affectées (autres que des navigateurs) | Applications du navigateur affectées | Authentification moderne affectée |
|:---:|:---:|:---:|:--:|:--:|
| Désactivé |État par défaut d’un nouvel utilisateur non inscrit dans l’authentification multifacteur Azure. |Non |Non |Non |
| Activé |L’utilisateur a été inscrit dans l’authentification multifacteur Azure, mais n’a pas été enregistré. Il sera invité à s’inscrire la prochaine fois qu’il se connectera. |Non.  Ils continuent de fonctionner jusqu’à ce que le processus d’inscription soit terminé. | Oui. Une fois que la session expire, l’inscription pour utiliser l’authentification multifacteur est nécessaire.| Oui. Une fois que le jeton d’accès expire, l’inscription pour utiliser l’authentification multifacteur est nécessaire. |
| Appliquée |L’utilisateur a été inscrit et a terminé le processus d’inscription pour utiliser l’authentification multifacteur Azure. |Oui.  Les applications requièrent des mots de passe d'application. |Oui. L’authentification multifacteur est requise à la connexion. | Oui. L’authentification multifacteur est requise à la connexion. |

L’état d’un utilisateur indique si un administrateur l’a inscrit dans l’authentification multifacteur Azure et s’il a terminé le processus d’inscription.

Tous les utilisateurs commencent avec l’état *désactivé*. Lorsque vous inscrivez des utilisateurs dans l’authentification multifacteur Azure, leur état devient *activé*. Lorsque les utilisateurs activés se connectent et terminent le processus d’inscription, leur état passe à *appliquée*.  

### <a name="view-the-status-for-a-user"></a>Afficher l’état d’un utilisateur

Pour accéder à la page où vous pouvez afficher et gérer les états des utilisateurs, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**.
   ![Sélectionnez Multi-Factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Une nouvelle page, qui affiche les états de l’utilisateur, s’ouvre.
   ![État utilisateur pour l’authentification multifacteur - capture d’écran](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Modifier l’état d’un utilisateur

1. Utilisez les étapes précédentes pour accéder à la page des utilisateurs de l’authentification multifacteur.
2. Recherchez l’utilisateur que vous souhaitez activer pour l’authentification multifacteur Azure. Vous devrez peut-être modifier l'affichage en haut de la page. 
   ![Rechercher un utilisateur - capture d’écran](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Cochez la case en regard du nom de l’utilisateur.
4. À droite, sous les étapes rapides, cliquez sur **Activer** ou **Désactiver**.
   ![Activer l’utilisateur sélectionné - capture d’écran](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >Les utilisateurs *activés* basculent automatiquement vers l’état *Appliquée* quand ils s’inscrivent à Azure MFA. Vous ne devez pas définir manuellement l’état de l’utilisateur sur Appliquée. 

5. Confirmez votre sélection dans la fenêtre contextuelle qui s’ouvre. 

Après avoir activé des utilisateurs, vous devez les en avertir par e-mail. Informez-les qu’ils seront invités à s’inscrire la prochaine fois qu’ils se connectent. En outre, si votre organisation utilise des applications sans navigateur qui ne prennent pas en charge l’authentification moderne, vos utilisateurs devront créer des mots de passe d’application. Vous pouvez également inclure un lien vers notre [guide de l’utilisateur final sur l’authentification multifacteur Azure](./end-user/multi-factor-authentication-end-user.md) pour les aider à commencer.

### <a name="use-powershell"></a>Utiliser PowerShell
Pour modifier l’état d’état utilisateur avec [Azure AD PowerShell](/powershell/azure/overview), modifiez `$st.State`. Il existe trois états possibles :

* Activé
* Appliquée
* Désactivé  

Ne basculez pas les utilisateurs directement vers l’état *Appliquée*. Les applications sans navigateur cesseront de fonctionner, car l’utilisateur n’a pas effectué l’enregistrement MFA et obtenu un [mot de passe d’application](multi-factor-authentication-whats-next.md#app-passwords). 

PowerShell est une bonne option si vous devez activer de nombreux utilisateurs à la fois. Créez un script PowerShell qui effectue une itération sur une liste d’utilisateurs et active ces utilisateurs :

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Voici un exemple :

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Activer Azure MFA avec une stratégie d’accès conditionnel

L’accès conditionnel est une fonctionnalité payante d’Azure Active Directory qui offre de nombreuses options de configuration. Ces étapes expliquent comment créer une stratégie. Pour plus d’informations, consultez [Accès conditionnel dans Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Accès conditionnel**.
3. Sélectionnez **Nouvelle stratégie**.
4. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**. Utilisez les onglets **Include** et **Exclure** pour choisir les utilisateurs et groupes qui seront gérés par la stratégie.
5. Sous **Affectations**, sélectionnez **Applications cloud**. Choisissez d’inclure **Toutes les applications cloud**.
6. Sous **Contrôles d’accès**, sélectionnez **Accorder**. Choisissez **Exiger une authentification multifacteur**.
7. Définissez l’option **Activer la stratégie** sur **On** (activée) puis sélectionnez **Enregistrer**.

Les autres options de la stratégie d’accès conditionnel vous permettent de spécifier exactement le moment où la vérification en deux étapes doit être requise. Par exemple, vous pouvez créer la stratégie suivante : lorsque des prestataires essaient d’accéder à notre app d’approvisionnement à partir de réseaux non approuvés et à l’aide d’appareils non joints à un domaine, exiger la vérification en deux étapes. 

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des conseils sur les [meilleures pratiques en matière d’accès conditionnel](../active-directory/active-directory-conditional-access-best-practices.md)

- Gérer les paramètres de l’authentification multifacteur pour [vos utilisateurs et leurs appareils](multi-factor-authentication-manage-users-and-devices.md)
