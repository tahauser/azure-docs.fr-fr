---
title: "Sécurité des mots de passe à niveaux dans Azure AD | Microsoft Docs"
description: "Explique comment Azure AD impose des mots de passe forts et protège les mots de passe des utilisateurs contre les cybercriminels,"
services: active-directory
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.openlocfilehash: 19c8d9084c9ce03d347e58b3359b65cdbcd80651
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Une approche à plusieurs niveaux de la sécurité des mots de passe dans Azure AD

Cet article décrit les meilleures pratiques que vous pouvez suivre en tant qu’utilisateur ou administrateur pour protéger vos comptes Azure Active Directory (Azure AD) ou votre compte Microsoft.

 > [!NOTE]
 > **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
 >
 > Les administrateurs Azure AD peuvent réinitialiser les mots de passe utilisateur à l’aide des instructions de l’article [Réinitialiser le mot de passe d’un utilisateur dans Azure Active Directory](active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Conditions requises pour les mots de passe

Azure AD intègre les approches courantes suivantes à la sécurisation des mots de passe :

* Exigences en termes de longueur du mot de passe
* Exigences en termes de « complexité » du mot de passe
* Expiration du mot de passe périodique et régulière

Pour plus d’informations sur la réinitialisation de mot de passe dans Azure Active Directory, consultez la rubrique [Réinitialisation de mot de passe en libre service pour Azure Ad pour les professionnels en informatique](active-directory-passwords-update-your-own-password.md).

## <a name="azure-ad-password-protections"></a>Protections de mot de passe Azure AD

Azure AD et le système de comptes Microsoft utilisent des approches validées par le secteur pour assurer la protection sécurisée des mots de passe administrateur et utilisateur, dont :

* Mots de passe interdits dynamiquement
* Verrouillage de mot de passe intelligent

Pour plus d’informations sur la gestion des mots de passe en fonction de la recherche en cours, consultez le livre blanc [Password Guidance](https://aka.ms/passwordguidance) (Conseils en matière de mots de passe).

### <a name="dynamically-banned-passwords"></a>Mots de passe interdits dynamiquement

Azure AD et les comptes Microsoft assurent la protection des mots de passe en interdisant dynamiquement tous les mots de passe utilisés couramment. L’équipe Azure AD Identity Protection analyse régulièrement les listes de mots de passe interdits, empêchant ainsi les utilisateurs de choisir des mots de passe utilisés couramment. Ce service est disponible pour les clients Azure AD et du service de comptes Microsoft.

Lorsque vous créez des mots de passe, il est important que les administrateurs encouragent les utilisateurs à choisir des phrases de mot de passe qui incluent une combinaison unique de lettres, de chiffres et de caractères ou de mots. Cette approche contribue à rendre les mots de passe utilisateur quasiment impossibles à compromettre, mais plus faciles à mémoriser pour les utilisateurs.

#### <a name="password-breaches"></a>Violations de mot de passe

Microsoft cherche toujours à garder une longueur d’avance sur les cybercriminels.

L’équipe Azure AD Identity Protection analyse en permanence les mots de passe couramment utilisés. Les cybercriminels utilisent également des stratégies similaires pour créer leurs attaques ; ils peuvent, par exemple, créer une [rainbow table](https://en.wikipedia.org/wiki/Rainbow_table) pour craquer les codes de hachage de mot de passe.

Microsoft analyse sans cesse les [violations de données](https://www.privacyrights.org/data-breaches) pour maintenir à jour une liste des mots de passe interdits dynamiquement, qui garantit que les mots de passe vulnérables sont interdits avant de devenir une véritable menace pour les clients Azure AD. Pour plus d’informations sur nos travaux en matière de sécurité actuels, consultez le [Rapport de renseignement sur la sécurité (SIR) de Microsoft](https://www.microsoft.com/security/sir/default.aspx).

### <a name="smart-password-lockout"></a>Verrouillage de mot de passe intelligent

Quand Azure AD détecte qu’un potentiel cybercriminel tente de pirater un mot de passe utilisateur, nous verrouillons le compte utilisateur avec le verrouillage de mot de passe intelligent. Azure AD est conçu pour déterminer les risques associés à des sessions de connexion spécifiques. Ensuite, à l’aide des données de sécurité les plus récentes, nous appliquons une sémantique de verrouillage pour arrêter les cybermenaces.

Si l’accès d’un utilisateur à son compte Azure AD est verrouillé, l’écran sera semblable à ce qui suit :

  ![Accès verrouillé au compte Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

Pour les autres comptes Microsoft, l’utilisateur verra ce qui suit :

  ![Accès verrouillé au compte Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Pour plus d’informations sur la réinitialisation de mot de passe dans Azure Active Directory, consultez la rubrique [Réinitialisation de mot de passe en libre service pour Azure Ad pour les professionnels en informatique](active-directory-passwords-update-your-own-password.md).

  >[!NOTE]
  >Si vous êtes administrateur Azure AD, vous souhaiterez peut-être utiliser [Windows Hello](https://www.microsoft.com/windows/windows-hello) pour éviter que vos utilisateurs créent des mots de passe classiques.
  >

## <a name="next-steps"></a>Étapes suivantes

* [Comment mettre à jour votre mot de passe](active-directory-passwords-update-your-own-password.md)
* [Principes de base de la gestion des identités Azure](fundamentals-identity.md)
* [Rapport sur l’activité de réinitialisation de mot de passe](active-directory-passwords-reporting.md)
