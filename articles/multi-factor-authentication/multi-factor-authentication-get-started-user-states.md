---
title: "Microsoft Azure Multi-Factor Authentication - État utilisateur"
description: "Découvrez les états utilisateurs dans Azure Multi-Factor Authentication."
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
ms.openlocfilehash: ad8d531d633eb65fe90404fdab0499b8e5332db6
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Comment exiger la vérification en deux étapes pour un utilisateur ou un groupe

Deux approches sont possibles pour exiger une vérification en deux étapes. La première option consiste à activer Azure Multi-Factor Authentication (MFA) pour chaque utilisateur. S’il est activé individuellement, l’utilisateur effectue la vérification en deux étapes chaque fois qu’il se connecte (à quelques exceptions près, notamment lorsqu’il se connecte à partir d’adresses IP approuvées ou que la fonctionnalité de _mémorisation des appareils_ est activée). La seconde option consiste à définir une stratégie d’accès conditionnel qui requiert une vérification en deux étapes sous certaines conditions.

>[!TIP] 
>Choisissez une de ces deux méthodes pour exiger la vérification en deux étapes, mais pas les deux. L’activation d’Azure Multi-Factor Authentication pour un utilisateur remplace toutes les stratégies d’accès conditionnel.

## <a name="which-option-is-right-for-you"></a>Quelle est l’option qui vous convient ?

L’approche classique pour exiger une vérification en deux étapes consiste à **activer Azure Multi-Factor Authentication en modifiant les états utilisateurs**. Elle fonctionne à la fois pour l’authentification Azure MFA dans le cloud et le serveur Azure MFA. Tous les utilisateurs activés effectuent la vérification en deux étapes chaque fois qu’ils se connectent. L’activation d’un utilisateur remplace toutes les stratégies d’accès conditionnel appliquées à cet utilisateur. 

**L’activation d’Azure Multi-Factor Authentication avec une stratégie d’accès conditionnel** est une approche plus souple pour exiger une vérification en deux étapes. Cependant, elle ne fonctionne que pour Azure MFA dans le cloud, et _l’accès conditionnel_ est une [fonctionnalité payante d’Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Vous pouvez créer des stratégies d’accès conditionnel qui s’appliquent à des groupes et à des utilisateurs individuels. Vous pouvez appliquer davantage de restrictions aux groupes à haut risque par rapport aux groupes à faible risque, ou exiger la vérification en deux étapes uniquement pour les applications cloud à haut risque tout en ignorant celles à faible risque. 

Les deux options invitent les utilisateurs à s’inscrire à Azure Multi-Factor Authentication la première fois qu’ils se connectent après l’activation de ces exigences. Elles fonctionnent également avec les [paramètres Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md) configurables.

## <a name="enable-azure-mfa-by-changing-user-status"></a>Activer Azure MFA en modifiant l’état de l’utilisateur

Les comptes d'utilisateur dans Azure Multi-Factor Authentication peuvent présenter les trois états suivants :

| Statut | Description | Applications affectées (autres que des navigateurs) | Applications du navigateur affectées | Authentification moderne affectée |
|:---:|:---:|:---:|:--:|:--:|
| Désactivé |État par défaut d’un nouvel utilisateur non inscrit à Azure MFA. |Non  |Non  |Non  |
| activé |L’utilisateur a été inscrit dans l’authentification multifacteur Azure, mais n’a pas été enregistré. Il sera invité à s’inscrire la prochaine fois qu’il se connectera. |Non.  Ils continuent de fonctionner jusqu’à ce que le processus d’inscription soit terminé. | Oui. Après expiration de la session, l’inscription à Azure MFA est nécessaire.| Oui. Après expiration du jeton d’accès, l’inscription à Azure MFA est nécessaire. |
| Appliquée |L’utilisateur a été inscrit et a terminé le processus d’inscription pour utiliser l’authentification multifacteur Azure. |Oui.  Les applications requièrent des mots de passe d'application. |Oui. Azure MFA est requis lors de la connexion. | Oui. Azure MFA est requis lors de la connexion. |

L’état d’un utilisateur indique si un administrateur l’a inscrit dans l’authentification multifacteur Azure et s’il a terminé le processus d’inscription.

Tous les utilisateurs commencent avec l’état *Désactivé*. Dès lors qu’ils sont inscrits à Azure MFA, leur état devient *Activé*. Lorsque les utilisateurs activés se connectent et suivent le processus d’inscription, leur état passe à *Appliqué*.  

### <a name="view-the-status-for-a-user"></a>Afficher l’état d’un utilisateur

Pour accéder à la page où vous pouvez afficher et gérer les états des utilisateurs, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les utilisateurs**.
3. Sélectionnez **Multi-Factor Authentication**.
   ![Sélectionnez Multi-Factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Une nouvelle page, qui affiche les états utilisateurs, s’ouvre.
   ![État utilisateur pour l’authentification multifacteur - capture d’écran](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Modifier l’état d’un utilisateur

1. Suivez les étapes précédentes pour accéder à la page **utilisateurs** d’Azure Multi-Factor Authentication.
2. Recherchez l’utilisateur pour lequel vous souhaitez activer Azure MFA. Vous devrez peut-être modifier l’affichage en haut de la page. 
   ![Rechercher un utilisateur - capture d’écran](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Cochez la case en regard du nom de l’utilisateur.
4. À droite, sous **étapes rapides**, cliquez sur **Activer** ou **Désactiver**.
   ![Activer l’utilisateur sélectionné - capture d’écran](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >Les utilisateurs *activés* basculent automatiquement vers l’état *Appliqué* quand ils s’inscrivent à Azure MFA. Ne définissez pas manuellement l’état utilisateur *Appliqué*. 

5. Confirmez votre sélection dans la fenêtre contextuelle qui s’ouvre. 

Dès que vous avez activé les utilisateurs, informez-les-en par e-mail. Informez-les qu’ils seront invités à s’inscrire la prochaine fois qu’ils se connectent. Par ailleurs, si votre organisation utilise des applications sans navigateur qui ne prennent pas en charge l’authentification moderne, vos utilisateurs devront créer des mots de passe d’application. Vous pouvez également inclure un lien vers le [guide de l’utilisateur final d’Azure MFA](./end-user/multi-factor-authentication-end-user.md) pour les aider à commencer.

### <a name="use-powershell"></a>Utiliser PowerShell
Pour modifier l’état utilisateur avec [Azure AD PowerShell](/powershell/azure/overview), modifiez `$st.State`. Il existe trois états possibles :

* activé
* Appliquée
* Désactivé  

Ne basculez pas les utilisateurs directement vers l’état *Appliquée*. Sinon, les applications sans navigateur cesseront de fonctionner, car l’utilisateur n’a pas effectué l’enregistrement Azure MFA et obtenu un [mot de passe d’application](multi-factor-authentication-whats-next.md#app-passwords). 

PowerShell est une bonne option si vous devez activer de nombreux utilisateurs à la fois. Créez un script PowerShell qui effectue une itération sur une liste d’utilisateurs et active ces utilisateurs :

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Voici un exemple de script :

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

_L’accès conditionnel_ est une fonctionnalité payante d’Azure Active Directory, qui offre de nombreuses options de configuration. Ces étapes expliquent comment créer une stratégie. Pour plus d’informations, consultez [Accès conditionnel dans Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Accès conditionnel**.
3. Sélectionnez **Nouvelle stratégie**.
4. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**. Utilisez les onglets **Inclure** et **Exclure** pour choisir les utilisateurs et les groupes gérés par la stratégie.
5. Sous **Affectations**, sélectionnez **Applications cloud**. Choisissez d’inclure **Toutes les applications cloud**.
6. Sous **Contrôles d’accès**, sélectionnez **Accorder**. Choisissez **Exiger une authentification multifacteur**.
7. Définissez l’option **Activer la stratégie** sur **Activée**, puis sélectionnez **Enregistrer**.

Les autres options de la stratégie d’accès conditionnel permettent de spécifier précisément à quel moment la vérification en deux étapes est requise. Par exemple, vous pouvez créer la stratégie suivante : lorsque des fournisseurs tentent d’accéder à notre application d’approvisionnement sur des réseaux non approuvés et des appareils non joints à un domaine, exiger la vérification en deux étapes. 

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des conseils : [Meilleures pratiques en matière d’accès conditionnel](../active-directory/active-directory-conditional-access-best-practices.md).

- Gérez les paramètres d’Azure Multi-Factor Authentication pour [vos utilisateurs et leurs appareils](multi-factor-authentication-manage-users-and-devices.md).
